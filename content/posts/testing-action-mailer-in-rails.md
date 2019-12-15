---
title: Testing ActionMailer in Rails
date: 2019-12-15
published: false
tags: []
series: false
cover_image: ./images/action_mailer.jpeg
canonical_url: false
description: "Testing your Mailers can be sometimes complicated. Do you remember sending and action email and heading to your inbox to see the email design and content? Right there are simple ways to test your Mailers."
---

Testing your Mailers can be sometimes complicated. Do you remember sending and action email and heading to your inbox to see the email design and content? Right there are simple ways to test your Mailers.

Rails provides us with useful test helpers to ActionMailer which helps to easily iterate our design, test if our code queus the right email and our email contains the right content.

### Getting Started
After creating a rails app we can generate a new Mailer in our app.
```
rails generate mailer NotificationMailer
```
We the above commmand we generate a mailer named `NotificationMailer` , this creates:-

1. A class `NotificationMailer` in `app/mailers`   directory.
2.  A class `NotificationMailerTest` in `test/mailers/notification_mailer_test.rb`
3. A preview class `NotificationMailerPreview` in `test/mailer/preview/notificaton_mailer_preview.rb`
4. A view directory `notification_mailer` in `app/views`

### Mailer Logic
We are ready to create our first email. To create an email that is send when users sign up in your application head to `NotificationMailer` in `app/mailer`

```ruby
class NotificationMailer < ApplicationMailer
  default from: 'info@gathuku.tech'

  def welcome(user)
    @user = user
    mail(to: @user.email, subject: 'Welcome to my App')
  end
end
```
We have defined a welcome method that send welcome email. The method accepts user object as the argument.

> Note we have defined the default from at the top of our class.

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
Remember the preview file that was created? Mailer preview enables us to preview our email design. This is helpful since we dont have to send the actual email to see oru email design.
All we need is to define a method that implements our mailer.  `notification_mailer_preview.rb`

```ruby
class NotificationMailerPreview < ActionMailer::Preview
  def welcome
    user = User.first
    NotificationMailer.welcome(user)
  end
end
```
After this ensure you server is running and navigate to route `http://localhost:3000/rails/mailers`. All your mailers and sepecific email methods will be listed. Click `welcome` to view the welcome email design.

### Testing
