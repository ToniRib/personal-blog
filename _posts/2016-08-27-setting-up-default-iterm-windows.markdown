---
layout: post
title:  "Setting Up Default iTerm Windows"
date:   2016-08-27 17:15:00 -0700
categories: jekyll update
---

When I first started at Traveler's Haven, I used to spend 5 minutes every morning getting my window configuration set up. I needed one pane for navigating our API files and running the console, one for navigating our web files and running that console, one to run redis, one to run sidekiq, one to run memcached, one to run our API server, and one to run our web server. Each morning I would open iTerm, carefully open up 7 tabs the way I wanted them, and then spend time typing the correct commands into each one to get everything up and running that I wanted.

It was a major pain in the ass.

I quickly started Googling to figure out if there was a way around this. Turns out, you can set up window configurations that you can launch with a specific key sequence, which will not only bring up the panes you want in the correct arrangement, but also launch commands automatically in each one of them! Setting this up took me about 15 minutes, but now when I open iTerm every morning it auto-launches my configuration and everything is ready in about 3 seconds.

Here's how I did it.

(Note: Thanks to [Andrew Ray's Blog](http://blog.andrewray.me/how-to-create-custom-iterm2-window-arrangments/) and [Chris Schmitz's Blog](http://chris-schmitz.com/develop-faster-with-iterm-profiles-and-window-arrangements/) for getting me started!)

1) Set up a profile for each pane. To do this, go to `iTerm2 > Preferences > Profiles` and click on the `+` sign on the bottom left side of the window. This will generate a new profile which you can give an appropriate name and any commands you want to be sent at the time of launch. These can be entered into the `Send text at start` section with a `;` between multiple commands.

2) Ensure that all other iTerm windows that you don't want to be part of this arrangement are closed.

3) Using `cmd - D` and `cmd - shift - D` open as many panes as you want to be a part of this arrangement. Move them around and size them until you have what you want.

4) One pane at a time, right click and select `Edit Session...` and then select which profile you want to be assigned to that pane. Click `Use Selected Profile` and you should see the window update with the Session Title. Close the preferences window.

5) Click `cmd - shift - s` to save the current window arrangement. Enter a name for your arrangement and click `OK`.

6) Go to `iTerm2 > Preferences > Keys` and click the `+` icon under the Key Mappings table to add a new mapping. Using your keyboard, mimic what your new shortcut will be and whatever you type will appear in the `Keyboard Shortcut` box. Under `Action`, scroll all the way to the bottom and select `Select Menu Item...`. In the new dropdown, look for the `Window` section and under `Restore Window Arrangement` select the name of your new window arrangement. Click `OK`.

7) Close iTerm and reopen it.

8) Hit your selected key shortcut and watch the magic happen!
