--- 
layout: post 
title: Using Python to scrape HTML tables with merged cells
---

The python library `pandas` makes it easy to scrape HTML tables from the web. Its `read_html()` method is very straightfoward and, for most tables, does a great job. It reads a page's tables directly into memory as a list of dataframes. 

For example, say we wanted to scrape the Flow of Funds table found on [this page](https://www.federalreserve.gov/apps/fof/DisplayTable.aspx?t=f.105). We could do the following: 

```python
import pandas as pd 
fof_url = "https://www.federalreserve.gov/apps/fof/DisplayTable.aspx?t=f.105"
fof = pd.read_html(fof_url)[0]
fof.to_csv("fof.csv", header = False, index = False)
```

...which scrapes the table then saves it as a CSV which ends up looking like this:

![alt text](https://raw.githubusercontent.com/johnricco/johnricco.github.io/master/assets/fof_csv.png)

Perfect! But not all tables are this simple. What if the table has cells that are merged, as is common in multi-level column titles? Take the first table on [this page](https://www.ssa.gov/policy/docs/statcomps/supplement/2015/5h.html) for example. It has a hierarchical column title strucuture, with complicated relationships between headers, subheaders, and even sub-subheaders.

![alt text](https://raw.githubusercontent.com/johnricco/johnricco.github.io/master/assets/ssa.png)

Pandas does not handle this situation well. Specifically, it has no way of parsing the `rowspan` and `colspan` HTML attributes that determine the dimensions of the cells. If we try to use `read_html` on this type of table, the result will be an unusable mess -- often skipping the column headers entirely. 

### A custom method to handle this 

Fortunately it's not too difficult to approach this from scratch. The following class and method mimics the functionality of `read_html`, with the added ability to accurately parse merged cells: 

```python
import os
import requests
import urllib
import math
import copy
import pandas as pd	
import numpy as np
from bs4 import BeautifulSoup 

class html_tables(object):
    
    def __init__(self, url):
        
        self.url      = url
        self.r        = requests.get(self.url)
        self.url_soup = BeautifulSoup(self.r.text)
        
    def read(self):
        
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
            
			# Create list to store rowspan values 
            skip_index = [0 for i in range(0, n_cols)]
			
            # Start by iterating over each row in this table...
			row_counter = 0
            for row in self.tables_html[n].find_all("tr"):
                
                # Skip row if it's blank
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
                        
                        # Insert data into cell
                        df.iat[row_counter, col_counter] = cell_data

                        # Record column skipping index
                        if row_dim[row_dim_counter] > 1:
                            this_skip_index[col_counter] = row_dim[row_dim_counter]
                
                # Adjust row counter 
                row_counter += 1
                
                # Adjust column skipping index
                skip_index = [i - 1 if i > 0 else i for i in this_skip_index]

            # Append dataframe to list of tables
            self.tables.append(df)
        
        return(self.tables)
```

The gist what's going on here: 
* First we parse the HTML using `BeautifulSoup`, finding all tables, then all rows within each table, then each cell within each row. 
* Then when we're looping over each of these cells, we check to see if there are any `colspan` and `rowspan` attributes associated with the cell -- this tells us the dimensions of the cell. In spreadsheet terms, you might think of a `rowspan` value of 2 corresponding to a cell being merged with the cell below it.
..* The `colspan` information is easy to use. If we register a `colspan` value greater than 1, we skip that number of columns before we start filling in the next cell. 
..* The `rowspan` information is a bit trickier. If we register a `rowspan` value greater than 1, we store it in a list ("`skip_index`") where each element corresponds to a column in our table. When the loop is filling in data for each cell, it first checks to see if there's a nonzero value in this column's `skip_index`; if that's true, it skips this column. With every row iteration, we increment the nonzero `skip_index` elemnets by -1 until it's back to zero. 

Let's say we wanted to try this with that SSA table from above. We would do the following:

```python
ssa_url = "https://www.ssa.gov/policy/docs/statcomps/supplement/2015/5h.html"
ssa = html_tables(ssa_url)
first_table = ssa.read()[0]
first_table.to_csv("ssa.csv", header = False, index = False)
```

The resulting CSV now has the correct positions for each header cell:

![ssa_csv](https://raw.githubusercontent.com/johnricco/johnricco.github.io/master/assets/ssa_csv.png)

Not the prettiest table, but it contains the correct positional information, and could easily be beautified in Excel by actually merging the cells. 