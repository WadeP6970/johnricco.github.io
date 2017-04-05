--- 
layout: post 
title: Using Python to scrape HTML tables with merged cells
---

The python library `pandas` makes it easy to scrape HTML tables from the web. Its `read_html()` method is very straightfoward and, for most tables, does a great job. It reads a page's tables directly into memory as a list of pandas dataframes. 

For example, say we wanted to scrape the Flow of Funds table found on [this page](https://www.federalreserve.gov/apps/fof/DisplayTable.aspx?t=f.105). We could do the following: 

```python
import pandas as pd 
fof_url = "https://www.federalreserve.gov/apps/fof/DisplayTable.aspx?t=f.105"
fof = pd.read_html(fof_url)[0]
fof.to_csv("fof.csv", header = False, index = False)
```

...which scrapes the table then saves it as a CSV which ends up looking like this:

(fof_csv.png)

However, not all tables are this simple. What if the table has cells that are merged, as is common in multi-level column titles? Take the first table on [this page](https://www.ssa.gov/policy/docs/statcomps/supplement/2015/5h.html) for example. It has a hierarchical column title strucuture, with headers and subheaders.

(ssa.png)

Pandas does not handle this situation well. Specifically, it has no way of parsing the `rowspan` and `colspan` HTML attributes that determine the dimensions of the cells. If we try to use `read_html`, the result will be an unusable mess. 

### A custom method to handle this 

Fortunately it's not too difficult to approach this from scratch. The following class and method mimics the functionality of `read_html`, but can accurately parse merged cells: 

```python
class html_tables(object):
    
    def __init__(self, url):
        
        self.url      = url
        self.r        = requests.get(self.url)
        self.url_soup = BeautifulSoup(self.r.text)
        
    def read(self, remove_footnotes = True):
        
        self.remove_footnotes = remove_footnotes
        self.tables      = []
        self.tables_html = self.url_soup.find_all("table")
        
        # Parse each table
        for n in range(0, len(self.tables_html)):
            
            n_cols = 0
            n_rows = 0
            
            for row in self.tables_html[n].find_all("tr"):
                col_tags = row.find_all(["td", "th"])
                if len(col_tags) > 0:
                    n_rows += 1
                    if len(col_tags) > n_cols:
                        n_cols = len(col_tags)
            
            # Create dataframe
            df = pd.DataFrame(index = range(0, n_rows), columns = range(0, n_cols))
            if self.indent_dict is not None: 
                indents = []
            
            # Start by iterating over each row in this table...
            row_counter = 0
            skip_index = [0 for i in range(0, n_cols)]
            
            for row in self.tables_html[n].find_all("tr"):
                
                # Skip row if its blank
                if len(row.find_all(["td", "th"])) == 0:
                    next
                
                else:
                    
                    # Get all cells containing data in this row
                    columns = row.find_all(["td", "th"])
                    col_dim = []
                    row_dim = []
                    col_dim_counter = -1
                    row_dim_counter = -1
                    col_counter = -1
                    indent_recorded = False
                    this_skip_index = copy.deepcopy(skip_index)
                    
                    for col in columns:
                        
                        # Determine cell dimensions
                        colspan = col.get("colspan")
                        if colspan is None:
                            col_dim.append(1)
                        else:
                            col_dim.append(int(colspan))
                        col_dim_counter += 1
                            
                        rowspan = col.get("rowspan")
                        if rowspan is None:
                            row_dim.append(1)
                        else:
                            row_dim.append(int(rowspan))
                        row_dim_counter += 1
                            
                        # Adjust column counter
                        if col_counter == -1:
                            col_counter = 0  
                        else:
                            col_counter = col_counter + col_dim[col_dim_counter - 1]
                            
                        while skip_index[col_counter] > 0:
                            col_counter += 1

                        # Get cell contents  
                        cell_data = col.get_text()
                        
                        # Parse out footnotes
                        if self.remove_footnotes:
                            footnote = col.find(["sup", "sub"])
                            if footnote is not None:
                                cell_data = cell_data[0: -len(footnote.get_text())]
                                
                        # Insert data into cell
                        df.iat[row_counter, col_counter] = cell_data

                        # Record column skipping index
                        if row_dim[row_dim_counter] > 1:
                            this_skip_index[col_counter] = row_dim[row_dim_counter]
                
                # Adjust row counter 
                row_counter += 1
                
                # Adjust column skipping index
                skip_index = [i - 1 if i > 0 else i for i in this_skip_index]
            
            # Add indents to dataframe
            if self.indent_dict is not None: 
                df = df.assign(indent = indents)
            
            # Append dataframe to list of tables
            self.tables.append(df)
        
        return(self.tables)
```

The gist is that it uses `BeautifulSoup` to parse the HTML, loops over each to record any `colspan` and `rowspan attributes`, and then recursively looks those up when filling in data for following cells.

Let's say we wanted to try this  


