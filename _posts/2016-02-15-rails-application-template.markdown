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

Before going into details about my template file, here's a link to it: [Toni's Rails Template](https://gist.github.com/ToniRib/c9ae717689d84febc8a3)

I modeled my Rails template after some recent projects, but obviously you can customize yours to your liking. One of the great things you can do in the template is add all of the gems that you are going to want so you don't forget any. I found that if I didn't delete the Gemfile and then re-create it, Rails would group my gems together all funky. Here's a look at how I'm creating the Gemfile:

```
# template.rb

# Remove the gemfile so we can start with a clean slate otherwise Rails groups
# the gems in a very strange way
remove_file "Gemfile"
add_file "Gemfile"

prepend_to_file "Gemfile" do
  "source \"https://rubygems.org\""
end

# Add all the regular gems
gem "rails", "4.2.5.1"
gem "bootstrap-sass", "~> 3.3.6"
gem "sass-rails", "~> 5.0"
gem "uglifier", ">= 1.3.0"
gem "jquery-rails"
gem "jbuilder", "~> 2.0"
gem "sdoc", "~> 0.4.0", group: :doc
gem "bcrypt", "~> 3.1.7"
gem "figaro"
gem "pg"

gem_group :development, :test do
  gem "byebug"
  gem "rspec-rails", "~> 3.0"
  gem "capybara"
  gem "database_cleaner"
  gem "selenium-webdriver"
  gem "factory_girl_rails", "~> 4.0"
  gem "shoulda-matchers", "~> 3.1"
end

gem_group :development do
  gem "web-console", "~> 2.0"
  gem "spring"
  gem "quiet_assets"
end
```

You might wonder where all the commands like `gem_group` and `prepend_to_file` came from. Turns out these are [Thor Actions](http://www.rubydoc.info/github/wycats/thor/Thor/Actions) that you can use to really easily do almost anything to your files when setting them up. This documentation is extremely useful if you're going to do anything more complicated in your template file than just adding gems. If found some of the most useful commands were `prepend_to_file`, `append_to_file`, and `insert_into_file` because it allowed me to not only create new files and folders but also to modify existing files to create the exact configuration I wanted.

After getting my Gemfile set up I bundled and ran some of the RSpec setup:

```
# Bundle and set up RSpec
run "bundle install"
run "rails generate rspec:install"

# Set up the spec folders for RSpec
run "mkdir spec/models"
run "mkdir spec/controllers"
run "mkdir spec/features"
run "touch spec/factories.rb"
```

I also wanted to set up [FactoryGirl](https://github.com/thoughtbot/factory_girl_rails) with a `factories.rb` file and ensure the FactoryGirl configuration made it into the `rails_helper.rb` file:

```
# Inject into the factory girl files
append_to_file "spec/factories.rb" do
  "FactoryGirl.define do\nend"
end

insert_into_file "spec/rails_helper.rb", after: "RSpec.configure do |config|\n" do
  "  config.include FactoryGirl::Syntax::Methods\n"
end
```

Similarly, I needed to set up the configuration for [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner) in the `rails_helper.rb` file so I won't have any funky test errors due to my database not being cleaned out after each test:

```
# Set up Database Cleaner
insert_into_file "spec/rails_helper.rb", after: "RSpec.configure do |config|\n" do
  "  config.before(:suite) do\n    DatabaseCleaner.clean_with(:truncation)\n  end\n\n
  config.before(:each) do\n    DatabaseCleaner.strategy = :transaction\n  end\n\n
  config.before(:each, :js => true) do\n    DatabaseCleaner.strategy = :truncation\n  end\n\n
  config.before(:each) do\n    DatabaseCleaner.start\n  end\n\n
  config.after(:each) do\n    DatabaseCleaner.clean\n  end\n\n"
end

gsub_file "spec/rails_helper.rb",
          "config.use_transactional_fixtures = true",
          "config.use_transactional_fixtures = false"
```

Some of the spacing looks a little odd but it's so everything is indented properly in the file when it is created. Additionally, [this blog](http://devblog.avdi.org/2012/08/31/configuring-database_cleaner-with-rails-rspec-capybara-and-selenium/) was very helpful for understanding this configuration.

Next I got [ShouldaMatchers](https://github.com/thoughtbot/shoulda-matchers) set up per thoughtbot's suggested configuration for use with RSpec:

```
# Set up Shoulda Matchers
append_to_file "spec/rails_helper.rb" do
  "\nShoulda::Matchers.configure do |config|\n  config.integrate do |with|\n    with.test_framework :rspec\n    with.library :rails\n  end\nend"
end
```

I also want to use [bootstrap-sass](https://github.com/twbs/bootstrap-sass) in my projects which required a little more configuration. First I had to rename `application.css` to `application.scss`. I always like to have a `custom.scss` file in my project so I created that also. I then imported the bootstrap gems into the `application.scss` file and removed (per the gem's instructions) the `*= require_tree .` and `*= require_self` lines since it is recommend to simply use the Sass `@import` function to add in all necessary files. Finally, I added the bootstrap sprockets into the `application.js` file to get access to bootstrap's javascript features.

```
# Set up for scss and bootstrap
run "mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss"
run "touch app/assets/stylesheets/custom.scss"

append_to_file "app/assets/stylesheets/application.scss" do
  "\n\n@import \"bootstrap-sprockets\";\n@import \"bootstrap\";\n@import \"custom\";"
end

gsub_file "app/assets/stylesheets/application.scss",
          "*= require_tree .",
          ""

gsub_file "app/assets/stylesheets/application.scss",
          "*= require_self",
          ""

append_to_file "app/assets/javascripts/application.js" do
  "//= require bootstrap-sprockets"
end
```

One thing that really bugs me about new Rails apps is that they don't include the line for ensuring your website is responsive (and properly resized) on mobile devices. Since I ALWAYS forget this one, it definitely had to go into my template.

```
insert_into_file "app/views/layouts/application.html.erb", before: "</head>" do
  "  <meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">"
end
```

Finally, I renamed the README file, initialized a new git repository and made the first commit:

```
# et up as a git repo and make the first commit
after_bundle do
  git :init
  git add: '.'
  git commit: "-a -m 'Initial commit'"
end
```

And that's it!

## Conclusion

I wanted to make sure this actually worked, so I set up a [test repo](https://github.com/ToniRib/example_with_template) and created some basic tests to ensure all of my gems and configurations were working as expected. Everything seems to be working great, and I can guarantee you that even though I had to spend some time setting all of this up, it's going to be totally worth it.
