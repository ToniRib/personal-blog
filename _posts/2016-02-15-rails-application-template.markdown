---
layout: post
title:  "Creating a Rails Template"
date:   2016-02-15 16:00:00 -0700
categories: jekyll update
---

Every time I start a new Rails app I seem to forget at least one gem or configuration setting. I'm sure you've been there before too. Whether it's setting the database up to use postgresql, remembering to add (and configure) database cleaner, or remembering to add capybara for easier testing, I always seem to forget something.

I was pairing with a friend on Saturday who was showing me how to properly set up javascript testing using RSpec and was thinking to myself "man, there's so much setup here I'm DEFINITELY going to forget something next time I do this!"

And then today I stumbled upon [Rails Application Templates](http://guides.rubyonrails.org/rails_application_templates.html) and [this blog on the .railsrc file](https://www.natashatherobot.com/how-to-configure-your-rails-defaults/) and immediately spent 2 hours setting up files of my own.

## .railsrc

So what are these magical files? The `.railsrc` is similar to your `.bash_profile` except it is executed every time you run the `rails new` command. It's basically a file of the default settings you want Rails to use whenever creating a new Rails project. So if you're like me and you forget to add `-d postgresql` even though you __always__ want a postgres database, fear not! Simply add this line to your `.railsrc` file and you'll never forget again.

Here's how my final `.railsrc` file turned out:

```
--skip-bundle
--skip-test-unit
--database=postgresql
--skip-turbolinks
--template=~/template.rb
```

Now let's go through each one and explain why I used it:

```
--skip-bundle
```

This tells the rails generator to skip bundling my gems, which I want to do because I'm going to completely replace the gemfile in my template.

```
--skip-test-unit
```

Necessary since I am going to immediately set up my testing using [RSpec](https://github.com/rspec/rspec-rails). Might as well skip the regular Test::Unit if I know I am going to not use it and will remove the entire test directory anyway.

```
--database=postgresql
```

So I never forget to use postgresql instead of sqlite3! This helps especially if you are going to push your app to Heroku for production.

```
--skip-turbolinks
```

Turbolinks have caused me pain in the past. They seem to interfere with javascript/jQuery sometimes so I ended out having to remove them entirely from my last project. I've gotten the same feedback from other Turing students so I'm just going to opt out.

```
--template=~/template.rb
```

This tells Rails where to find my Rails Application Template file that I want to use for all Rails projects going forward. In this file is where most of the magic happens, so let's take a look at it!

## Rails Application Template
