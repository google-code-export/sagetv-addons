# Quick and Dirty Setup (for now) #

This page will be filled in shortly, but for now here's how to get started with the beta:

  * Download a beta from the project snapshots download area (see link on main page)
  * Make sure the SageTV Jetty plugin is installed and running correctly
  * Unzip SageRSS beta into `C:\Program Files\SageTV\SageTV`; **maintain directory structure when unzipping**
  * Load SageRSS at `http://your_jetty_server/sagerss/`
  * Create a search, click Search, if you like the results copy the RSS link into your RSS reader
  * Enjoy

**NOTES:**  All inputs use regular expressions to match.  You can supply multiple regular expressions for any field by separating them with semicolons.  A field must match **every** regex to be considered a match.  "Or"s should be done in a single regex.

## Examples ##

Search for all programs with a category of Comedy and Special (i.e. we're looking for comedy specials).  Use this regex in the Category box:

`Comedy;Special`

On the other hand, if you want to find all specials _or_ comedies then use this search in the Category box:

`Comedy|Special`

The first is sending two separate regexs to the search while the second is sending a single regex to the search.