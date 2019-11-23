---
title: Testing External APIs with VCR
date: 2019-11-23
published: true
tags: []
series: false
cover_image: ./images/vcr.jpg
canonical_url: false
description: "Running external APIs tests can be time-consuming, VCR is a ruby gem that allows you to record test suite's HTTP interactions and replay them during future test runs for fast, deterministic, accurate tests"
---

Testing external APIs can be a time-consuming task, [VCR](https://github.com/vcr/vcr) is a ruby gem that allows you to record test suite's HTTP interactions and replay them during future test runs for fast, deterministic, accurate tests.

### How VCR works?
When we make the first API call the request goes through full request and response cycle. VCR records the API request and response, which it saves as a `cassette`. In other words, VCR stubs it for future use. When the test is run again, no API call is made. Instead, VCR stubs the response. The test will run much faster as a result.

### Installation
VCR works together with [webmock](https://github.com/bblimke/webmock) a library for stubbing and setting expectations of HTTP requests. Add the latest version of VCR and Webmock in your `Gemfile`. Run `bundle install` to update dependencies.

```ruby
gem 'vcr', '~> 5.0'
gem 'webmock', '~> 3.7', '>= 3.7.6'
```
> Its good to add them in test group gems

> Add them as development dependencies if you are developing a gem.

### Configuration
VCR implements a [configure](https://relishapp.com/vcr/vcr/v/1-6-0/docs/configuration/) block for different configuration. Add the below block in your `test_helper.rb` file.

```ruby
VCR.configure do |config|
  config.allow_http_connections_when_no_cassette = false
  config.cassette_library_dir = File.expand_path('cassettes', __dir__)
  config.hook_into :webmock
  config.ignore_request { ENV['DISABLE_VCR'] }
  config.ignore_localhost = true
  config.default_cassette_options = {
    record: :new_episodes
  }
end
```
- __allow_http_connections_when_no_cassette__ - allows actual full request and response cycle, when no cassette found.
- __cassette_library_dir__ - directory where cassettes are stored.
- __hook_into__ - specifies a request stubbing library
- __ignore_request__ - default is `false` when `true` VCR makes full request without using cassettes even if they exists.
- __ignore-localhost__ -  prevent VCR from having any affect on localhost requests
- __default_cassette_options__ - takes a hash that provides defaults for each cassette you use

### Testing API calls
We are now ready to start testing APIs calls. For demo, purpose lets use [JSONplaceholder](https://jsonplaceholder.typicode.com/) a fake API for developers.

```ruby
class VCRTest < Minitest::Test
  def test_fetch_post
    VCR.use_cassette("get_post") do
      response = Net::HTTP.get_response(URI('https://jsonplaceholder.typicode.com/posts/1'))
      assert_equal(200,response.status)
    end
  end
end
```
Run the test once and VRC will record in `cassette_library_dir` you defined in configure block, for our case `cassettes/get_post.yml`. When you run the test again no real connection is made making the process very fast. You may realize the speed when you have very many API endpoints to test.

### Tips
>If you are having issues with your cassettes or you need new data you can deletes the cassettes and a new version will be created.

> If you have sensitive information in your responses like API keys. You can filter the keys in your configuration block.
```ruby
VCR.configure do |config|
 config.filter_sensitive_data('<API_KEY>',ENV('API_KEY'))
end
```

If you like the blog, share with friends

##### Thanks for reading :tada: :blush:
