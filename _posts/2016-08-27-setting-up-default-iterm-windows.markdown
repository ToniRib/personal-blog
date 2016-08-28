---
layout: post
title:  "Setting Up Default iTerm Windows"
date:   2016-08-27 17:15:00 -0700
categories: jekyll update
---

When I first started at Traveler's Haven, I used to spend 5 minutes every morning getting my window configuration set up. I needed one pane for navigating our API files and running the console, one for navigating our web files and running that console, one to run redis, one to run sidekiq, one to run memcached, one to run our API server, and one to run our web server. Each morning I would open iTerm, carefully open up 7 tabs the way I wanted them, and then spend time typing the correct commands into each one to get everything up and running that I wanted.

It was a major pain in the ass.

I quickly started Googling to figure out if there was a way around this. Turns out, you can set up window configurations that you can launch with a specific key sequence, which will not only bring up the tabs you want in the correct arrangement, but also launch commands automatically in each one of them! Setting this up took me about 15 minutes, but now when I open iTerm every morning it auto-launches my configuration and everything is ready in about 3 seconds.

Here's how I did it. The example I'm going to use is for setting up a 3 profile configuration that I'm using to work on a Rails site for my husband's glass paintings.

(Note: Thanks to [Andrew Ray's Blog](http://blog.andrewray.me/how-to-create-custom-iterm2-window-arrangments/) and [Chris Schmitz's Blog](http://chris-schmitz.com/develop-faster-with-iterm-profiles-and-window-arrangements/) for getting me started!)

1) Set up a profile for each pane. To do this, go to `iTerm2 > Preferences > Profiles` and click on the `+` sign on the bottom left side of the window. This will generate a new profile which you can give an appropriate name and any commands you want to be sent at the time of launch.

![iterm_profiles]({{ site.url }}/images/iterm_profiles.png)
