--- 
layout: post 
title: Interactive CDFs of rent per bedroom 
---

A few months ago, I used census survey data to create an [interactive
graph](http://greatergreaterwashington.org/post/28904/not-all-the-
housing-in-a-region-costs-the-same-despite-what-headlines-imply/) of
rent distributions in American urban areas. Though useful, the data have
three major issues:

* Because household-level data isn't available, I used ZIP code to
construct the CDFs. This aggregation hides variation we might be
interested in.

* The gross rent figures aren't broken down by bedroom, so to the extent
that there are meaningful geographic differences in housing
characteristics (e.g. NYC probably has a higher share of studios than
Pittsburgh), the CDFs aren't quite comparable across cities.

* The ZIP-level data are cut off at $2000. This is too low a cutoff for
expensive cities.

Fortunately, Shawn Bucholtz of HUD took notice of my post and was kind
enough to send me internal data that addresses all three of my concerns
-- and so I re-created the graph with the new data.

Embedded below is a graph with a subsample of some of America's largest
urban areas. The full interactive version where you can choose from 40
urban areas is [here](https://johnricco.shinyapps.io/cdfs/).

<iframe
src="https://cdn.rawgit.com/johnricco/f11250fdb29725b4191f/raw/
c521156f95f9d375645a2883eba354f88c5accfd/cdf.html" width="600"
height="400" frameBorder="0"></iframe> If you have any feedback or
questions, feel free to reach out on
[Twitter](https://twitter.com/riccoja).