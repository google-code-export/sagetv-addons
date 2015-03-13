# Overview #

Starting with v2.3.0, SRE now uses the raw ESPN Bottom Line feed for all of its monitors.  There are some major benefits to this source switch:

  * The "Bottom Line" data feed is a raw data source, which means it's easily parsed and the format of this data feed should not change too often, but when it does, the required fix in SRE should literally take only a minute or two to implement; this is the dream data source I've been looking for!
  * No more having to scrape HTML pages for game data!

However, with benefits comes sacrafice:

  * The Bottom Line feed only feeds the current day's scores; no more looking into the future to verify upcoming events and overrides.
  * The Bottom Line feed uses many different shorthands and abbreviations for team names, especially NCAA schools; this will cause a greater need for overrides; SRE will continue to make common substitutions, but due to the varying formats used by the Bottom Line, I can't make auto subs for them all, it's just too much to try and keep track of.  See below for how to find the appropriate shorthand when needed.

# Finding Override Values #

So when SRE needs an override to monitor a game you need to determine the team's name as used by the ESPN Bottom Line feed.  To do this, visit this URL:

http://sre-maps.appspot.com

This is a web application hosted on the Google App Engine that will allow you to view a pretty printed version of the various data feeds used by SRE.  Simply click on the **View Data Feed** link on the left hand side and choose the sport for which you need information about.  You will get a list of all the events for the current day that SRE will be able to monitor.  Find the game you're interested in monitoring and note the way the teams are specified.  Take that information and create an override in SRE.  **NOTE:** For NCAA events, disregard team rankings as displayed on the feed.  For example, if you need to monitor the NCAA men's hoops game between Syracuse and Seton Hall and SRE tells you an override is needed so you go to the above web site and load the NCAA Men's Basketball feed and you find the game listed as:

(5) Syracuse at S Hall (9:00 PM ET)

Then you would create an override in SRE using `Syracuse at S Hall` since you always drop the ranking specifiers and, of course, you don't include the start time when creating an override.

## Maps ##

When you create an override in SRE (v2.4.x.y) and that override is verified as being valid then SRE will ask you if you want to add the override to the online global map.  Doing so means that you and all SRE users will immediately start to the use the override automatically.  Once an override is added to the global map then you will not need to create the same override for a team in the future - SRE will automatically pull in the override from the web.

Overrides are needed mostly for NCAA events and should rarely be needed for the pro sports.  With enough help from the community of SRE users, eventually we should have a complete map for all NCAA schools such that the need to create overrides for NCAA events becomes unnecessary.