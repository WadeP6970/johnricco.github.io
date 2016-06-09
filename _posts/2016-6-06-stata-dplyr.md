--- 
layout: post 
title: Stata to R translation, dplyr style 
---

I work in a field where most people do data munging with Stata. A lot of my colleagues want to learn R but are turned off by the moderately steep learning curve -- base R can be kinda terrifying when the extent of your programming experience is writing do-files. And while R is immensely more flexible, Stata is *very* well-suited for most basic tasks when you're dealing with a single dataset. You `keep` this, `drop` that, `gen` this, etc.

For this type of person -- the marginally interested, somewhat offput Stata user -- I recommend learning dplyr. Its syntax is intuitive and concise enough that longtime Stata users don't think *why do I have to write all this code just to drop a variable??* So below, I've put together a list of examples that translate everyday Stata commands into dplyr. Of course, I recommend reading one or more of the many of dplyr tutorials out there for a complete treatment of all the things this package can do. But I hope this helps answer: how do I do [Stata thing] in R?

I'll be using the `flights` data from the nycflights13 package, and assigning it to a dataframe called "d".

**Filtering observations**

Stata:

```
keep if origin == "LGA"            #1
drop if month == 12                #2
keep if day > 5 & day < 10         #3
keep in 1/200                      #4
gen last = substr(tailnum, 6, 6)   #5
  keep if last == "A"  
```
R:

```R
d %>% filter(origin == "LGA")                #1
d %>% filter(month != 12)                    #2
d %>% filter(day > 5, day < 10)              #3
d %>% slice(1:200)                           #4
d %>% filter(substr(tailnum, 6, 6) == "A")   #5
```

**Subsetting columns**

Stata:

```
keep month day origin    #1
drop tailnum flight      #2
keep year-arr_delay      #3
keep arr_*               #4
```

R:

```R
d %>% select(month, day, origin)    #1
d %>% select(-tailnum, -flight)     #2
d %>% select(year:arr_delay)        #3
d %>% select(starts_with("arr_"))   #4
```

**Generating new variables**

Stata:

```
gen total_delay = dep_delay + arr_delay   #1
gen first_letter = substr(origin, 1, 1)   #2
gen flight_path = origin + "_" + dest     #3
```

R:

```R
d %>% mutate(total_delay = dep_delay + arr_delay)            #1
d %>% mutate(first_letter = substr(origin, 1, 1))            #2
d %>% mutate(flight_path = paste(origin, dest, sep = "_"))   #3
```

**Renaming variables** 

Stata:

```
rename arr_delay arrival_delay   #1
rename arr_* arrival_*           #2
```

R:

```R
d %>% rename(arrival_delay = arr_delay)   #1
```

(Note: there's no good way to accomplish #2 using dplyr. A regex function applied over a vector of column names will do the trick, though.)

**Appending datasets**

(Let's assume we have a second dataset, `d1`, that contains flight information for 2014 and contains the same variables.)

Stata:

```
append using d1   #1
```

R:

```R
d %>% bind_rows(d1)   #1
```

 **Merging datasets**
 
 Stata:
 
 ```
 
 ```
 
 R:
 
 ```
 
 ```

** Collapsing data **

Stata:

```
collapse (mean) mean_delay = arr_delay, by(carrier)                  #1
collapse (min) min_d = distance (max) max_d = distance, by(origin)   #2

```

R:

```
d %>%                                     #1
  group_by(carrier) %>%
  summarise(mean_delay mean(arr_delay))   
d %>%                                     #2
  group_by(origin) %>%
  summarise_each(min_d = min(distance),
  		        (max_d = max(distance)
  		        

```



