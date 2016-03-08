--- 
layout: post 
title: A simple solution for creating panel data from scratch in Stata 
---

Recently I've been trying to put my entire workflow in Stata. This means
doing even the most elementary things -- like setting up the skeleton of
a dataset -- in Stata, and not in Excel, however tempting it may be. It
can be frustrating but the long-run benefits are worth it, I think.

One thing that is somewhat unintuitive in Stata is setting up the basics
of a panel dataset. That is, starting with an empty dataset and then
producing something that looks like this:

[pic]

It's a task that comes naturally in object-oriented language. It's not difficult to imagine how you might combine and repeat vectors to do this, for example in R:

```R
c <- c("Canada", "France", "Germany", "UK", "Italy", "Japan", "USA")
y <- 2010:2014

country <- unlist(lapply(c, function(x) rep(x, length(y))))
year <- rep(y, length(c))
df <- data.frame(country, year)

```

