---
title: Testing ActionMailer in Rails
date: 2019-12-15
published: true
tags: []
series: false
cover_image: ./images/action_mailer.jpg
canonical_url: false
description: "Testing your Mailers can be sometimes complicated. Do you remember sending an email and heading to your inbox to see the email design and content? Right here are simple ways to test your Mailers."
---

Rails provide us with useful test helpers to ActionMailer which helps to easily iterate our design, test if our code queues the right email and our email contains the right content.

### Getting Started
After creating a rails app we can generate a new Mailer in our app.
```
rails generate mailer NotificationMailer
```
With the above command we generate a mailer named `NotificationMailer`, this creates:-

1. A class `NotificationMailer` in `app/mailers`   directory.
2.  A class `NotificationMailerTest` in `test/mailers/notification_mailer_test.rb`
3. A preview class `NotificationMailerPreview` in `test/mailer/preview/notificaton_mailer_preview.rb`
4. A view directory `notification_mailer` in `app/views`

### Mailer Logic
We are ready to create our first email. To create an email that is sent when users sign up in your application head to `NotificationMailer` in `app/mailer`

```ruby
class NotificationMailer < ApplicationMailer
  default from: 'info@gathuku.tech'

  def welcome(user)
    @user = user
    mail(to: @user.email, subject: 'Welcome to my App')
  end
end
```
We have defined a welcome method that sends a welcome email. The method accepts the user object as the argument.

> Note we have defined the `default from` at the top of our class.

### Mailer view
After writing our logic remember `notification_mailer` directory that was created in our `app/view`, head into the directory and create a file named `welcome.erb.html`. The file will render HTML design for our email.

> Note the naming convention. The name must match the name of our email method.

```html
<!-- <!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">

  </head>
  <body>
    <h1>Thanks for signing,<%= @user.email %> </h1>
    <p>Welcome to the app</p>
  </body>
</html> -->
```

### Mailer Preview
Remember the preview file that was created? Mailer preview enables us to preview our email design. This is helpful since we don't have to send the actual email to see our email design.
All we need is to define a method that implements our mailer.  `notification_mailer_preview.rb`

```ruby
class NotificationMailerPreview < ActionMailer::Preview
  def welcome
    user = User.first
    NotificationMailer.welcome(user)
  end
end
```
After this ensure your server is running and navigate to route `http://localhost:3000/rails/mailers`. All your mailers and specific email methods will be listed. Click `welcome` to view the welcome email design.

### Testing

Your mailers class just like any other part of Rails application should be tested to ensure they are working properly.
Why should you test your mailers? below are some of the reasons.

- To ensure that emails are being processed (created and sent).
- To ensure that the email content is correct (subject, sender, body, etc).
- To ensure the right emails are being sent at the right times.

There are two aspects of testing mailers.

 - Units tests
 - Functional tests

#### Units tests

 In unit tests, we run the mailer in isolation with tightly controlled inputs and compare the output to a known value (a fixture.)
 In our `NotificationMailer` we have `welcome` action which is used to send a welcome email when users sign up. Below is the unit test.
 ```ruby
 # frozen_string_literal: true

require 'test_helper'

class NotificationMailerTest < ActionMailer::TestCase

  def setup
    @user = users(:one)
  end

  test 'welcome' do
    email = NotificationMailer.welcome(@user)
    assert_emails 1 do
      email.deliver_later
    end

    assert_equal email.to, [@user.email]
    assert_equal email.from, ['biodkod.co.ke']
    assert_equal email.subject, 'Welcome to my App'
    assert_match 'Thanks for signing', email.body.encoded
  end
end
 ```
 In the test above, we create the email and store the returned object in the email variable. We then ensure that it was sent (the first assert), then, in the second batch of assertions, we ensure that the email does indeed contain what we expect.

### Conclusion
 To understand more about mailers. The line `ActionMailer::Base.delivery_method = :test` in `config/environments/test.rb` sets the delivery method to test mode so that email will not be delivered (useful to avoid spamming your users while testing) but instead, it will be appended to an array (`ActionMailer::Base.deliveries`)

 > You can clear the deliveries array with `ActionMailer::Base.deliveries.clear`
