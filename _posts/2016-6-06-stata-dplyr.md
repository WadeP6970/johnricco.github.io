--- 
layout: post 
title: Stata to R translation, dplyr style 
---

I work in a field where most people do data munging with Stata. A lot of my colleagues want to learn R but are turned off by the moderately steep learning curve -- base R can be kinda terrifying when the extent of your programming experience is writing do-files. And while R is immensely more flexible, Stata is *very* well-suited for most basic tasks when you're dealing with a single dataset. You -keep- this, -drop- that, -gen- this, etc.

For this type of person -- the marginally interested, somewhat offput Stata user -- I recommend learning dplyr. Its syntax is intuitive and concise enough that longtime Stata users don't think *why do I have to write all this code just to drop a variable??* So below, I've put together a list of examples that translate everyday Stata commands into dplyr. Of course, I recommend reading one or more of the many of dplyr tutorials out there for a complete treatment of all the things this package can do. But I hope this helps answer: how do I do [Stata thing] in R?

I'll be using the flights data from the nycflights13 package. It looks like this:



First, some stuff on filtering observations. This is pretty straightforward. Stata captures this with "keep/drop if..." logic; with dplyr the verb is "filter".

Stata:
```
keep if origin == "LGA"            //1
drop if month == 12                //2
keep if day > 5 & day < 10         //3
keep in 1/200                      //4
gen last = substr(tailnum, 6, 6)   //5
  keep if last == "A"  
```

R:
```R
d %>% filter(origin == "LGA")                //1
d %>% filter(month != 12)                    //2
d %>% filter(day > 5, day < 10)              //3
d %>% slice(1:200)                           //4
d %>% filter(substr(tailnum, 6, 6) == "A")   //5
```


Next, let's look at subsetting on columns. Again, this is keep/drop logic in Stata, and the dplyr verb is "select", much like how you *SELECT* columns in a SQL query.

