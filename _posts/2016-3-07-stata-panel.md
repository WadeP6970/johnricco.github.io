--- 
layout: post 
title: A simple solution for creating panel data from scratch in Stata 
---

Recently I've been trying to put my entire workflow into Stata. This means
doing even the most elementary things -- like setting up the skeleton of
a dataset -- in Stata, and not in Excel, however tempting it may be for simple tasks. It
can be frustrating but the long-run benefits are worth it, I think.

One thing that is somewhat unintuitive in Stata is setting up a classic *i,t* panel dataset. That is, starting with an empty dataset and then
producing something that looks like this:

<a href='http://postimage.org/' target='_blank'><img src='http://s18.postimg.org/4406cpytl/panel.png' border='0' alt="panel" /></a>

It's a task that comes naturally in object-oriented languages. It's not difficult to imagine how we might combine and repeat vectors to do this, for example in R:

```R
c <- c("Canada", "France", "Germany", "UK", "Italy", "Japan", "USA")
y <- 2010:2014

country <- unlist(lapply(c, function(x) rep(x, length(y))))
year <- rep(y, length(c))
df <- data.frame(country, year)
```
It's less intuitive in Stata, however, because there's no obvious parallel for these types of data structures. 

Fortunately there's a simple (and elegant!) solution. Together, `expand` and `bysort` can produce what we're looking for. It goes like this:

```
import excel using "$home/countries.xlsx", firstrow clear
expand 5
bysort country: gen year = 2010 + [_n-1]
```
The first line reads in a list of the unique values of your *i* variable, in this case countries. You could just as easily enter them into the data editor. The second line *expands* your data -- that is, it replaces each observation with *n* copies, where *n* is the time dimension of your data. In my example, that's 5 years for 2010-14. And the third line simply fills in your data with the time dimension, *by* each value of *i* using Stata's _n observation identifier. 