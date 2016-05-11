---
layout: post
title:  "Testing Mailers in Rails"
date:   2016-05-11 19:30:00 -0700
categories: jekyll update
---

I recently got an assignment at work to move the location of some of the mailings we send out so we could also create Event logs to go with each piece of mail. In doing this, I ended out creating a whole new EventMailer class that inherits from ActionMailer::Base. This is called from one of my model classes in the format of `event.send_mail` where `send_mail` determines what type of mail to send and calls the appropriate Mailer action for it.

However, that left me with a question of how to test all of this. I wanted to go beyond just making sure that some piece of mail was 'delivered' in my tests. I figured there must be a way to check at least the subject and maybe a few other parameters.

Turns out this is pretty easy with RSpec. First, in order to make sure your tests don't ACTUALLY send emails, you need to add a line to your `test.rb` file:

```
config.action_mailer.delivery_method = :test
```

This ensures that new messages will simply populate the `ActionMailer::Base.deliveries` array instead of flooding your users with junk test mail. Then, you can simply write a test like this:

```
it "sends mail to the correct user" do
  event = Event.new(name: "New Mail")

  expect { event.send_mail }
    .to change { ActionMailer::Base.deliveries.count }.by(1)

  mail = ActionMailer::Base.deliveries.last
  expect(mail.subject).to eq event.name
  expect(mail.from).to include "example@example.com"
end
```

You can use the block expect syntax from RSpec to show that the number of 'deliveries' has increased by one. Then, because the actual 'mail' is stored in the `ActionMailer::Base.deliveries` array, we can pick out the first one and check it's subject and who sent it.

However, this wasn't enough for me. I currently have two main methods in my Mailer class and I wanted to make sure the correct type of email was being sent. This one took a little more searching, but it is possible to test. In the example below, `event.send_mail` uses the name of the event to determine which of the two EventMailer methods to call. In this case, I am expecting it to call `EventMailer.first_mailer_method(name).deliver_now`.

```
it "sends mail through the appropriate method" do
   event = Event.new(name: "First Mailer Method")

   message_delivery = instance_double(ActionMailer::MessageDelivery)
   expect(EventMailer).to receive(:first_mailer_method).with(event.name).and_return(message_delivery)
   expect(message_delivery).to receive(:deliver_now)

   event.send_mail
 end
```

This creates an [instance double](https://relishapp.com/rspec/rspec-mocks/v/3-2/docs/verifying-doubles/using-an-instance-double) of the [ActionMailer::MessageDelivery](http://edgeapi.rubyonrails.org/classes/ActionMailer/MessageDelivery.html) class which is used by `ActionMailer::Base` when creating a new mailer. We can then say that we expect our `EventMailer` to receive a call to the `first_mailer_method` with the appropriate arguments and that it turns an instance of our `message_delivery` instance double. Since `deliver_now` is also chained on the end, we have to add the additional statement that `message_delivery` should receive `deliver_now`.

It's always been a little strange to me that I have to make the actual call after the expectations, but in RSpec I'm basically setting it up to say "hey, you're about to receive something like this, and here's what I'm expecting to happen once you do." I wasn't convinced at first that this was passing for the correct reasons, so I tried subbing out the `first_mailer_method` with my `second_mailer_method` just to ensure that it would fail since that method is not expected to be called in this instance. Happily, it failed!

I'm sure there are even more ways to test Mailers, but this was a good start for me.
