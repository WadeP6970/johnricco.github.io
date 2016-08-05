--- 
layout: post 
title: How I used R to program an election predicion market Twitter bot
---

During this uniquely insane election cycle, my Twitter feed has been a mess of outrage and ill-advised hot takes. To counter that, I created a Twitter bot that routinely tweets out prediction market probabilities for the top candidates. These markets are good at quickly and accurately aggregating the latest information into observable odds, without the biases of punditry. 

To do so, I used the R package twitteR. Here's how I did it. (Shout out to Simon Munzert's bot-building guide, which was a helpful reference.)

*1) The data*

Eli Dourado's website primary.guide aggregates pricing data from Befair into a simple table that answers the question: how likely is candidate X to win the US general election? Fortunately, his site includes a continously-updated JSON file candidate-probability pairs that can be accessed at any time. This is where my R script grabs its data from every time it runs.

*2) The Twitter account and API access*

I created the actual account, @primaryguidebot, through Twitter's normal sign-up process. From there, I registered a Twitter app at [link]. This where you'll find the credentials needed to programmatically interface with Twitter. More on that below.

*3) The R script*

First, we need to load a few packages. `jsonlite` helps us parse the JSON data; `twitteR` gives us an interface to the Twitter API; and `dplyr` makes the small amount of data munging here simpler and easier to read (I'm a sucker for chaining code with %>%):

```R
library(jsonlite)
library(twitteR)
library(dplyr)
```
Next, we grant access to our Twitter account by using the `setup_twitter_oauth` function from `twitteR`. You need four pieces of information:

```R
api_key <- "ABC"
api_secret <- "XYZ"
access_token <- "123"
access_token_secret <- "789"
setup_twitter_oauth(api_key, api_secret, access_token, access_token_secret)
print(2)
```
The `print(2)` line tells R not to cache your OAuth access credentials between sessions, in response to a prompt from the `setup_twitter_oauth`. 

After that, it's time to read the data. `fromJSON` takes the JSON file and returns a named list, which we can easily work into a data.frame, a data structure that most R users are probably more comfortable with:

```R
json <- fromJSON("https://primary.guide/json/general")
d <- data.frame(candidate = names(json), 
                prob = unlist(json), 
                stringsAsFactors = F)
```

(The probabilities are returned as strings; that's why I bother to specify stringsAsFactors = F.)

Then we clean up the data. That involves converting the probabilities to numbers, sorting them, and generating a concatened column of formatted candidate-probability pairs for our tweet:

```R
d <- d %>%
  mutate(p = as.numeric(p)) %>%
  arrange(-p) %>%
  mutate(text = paste0("-", candidate, ": ", prob, "%")
```

Our `d$text` vector now contains information that looks like, for example, "-Donald Trump: 22.5%".

Now all that's left is to generate the actual content of the tweet, subsetting `d$text` for the top four candidates and separating each chunk of text with a newline. Then we tweet it with...`tweet`. It's that simple:

```R
this_tweet <- paste0("Current probabilities:",
                     d$text[1], 
                     d$text[2],
                     d$text[3],
                     d$text[4],
                     "primary.guide",
                     sep = "\n")
tweet(this_tweet)
```

If we run the entirety of this code, the account posts a tweet that looks like this:

*4) The automation*

In order to turn this code into an actual Twitter bot, we need to make it tweet at regular intervals on its own. This can be accomplished with Windows Task Scheduler (or chron / launchd job with Mac). This stackoverflow post demonstrates how to do this, step-by-step.

And that's it! It's a relatively straightfoward process. Hopefully this guide can help someone else create their own bot.