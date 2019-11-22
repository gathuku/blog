---
title: Testing External API with VCR
date: 2019-10-23
published: true
tags: []
series: false
cover_image: ./images/jam.jpeg
canonical_url: false
description: "Running external APIs tests can be time consuming, VCR is a ruby gem that allows you to record test suite's HTTP interactions and replay them during future test runs for fast, deterministic, accurate tests"
---

Testing external APIs can be a time consuming API, VCR is a ruby gem that allows you to record test suite's HTTP interactions and replay them during future test runs for fast, deterministic, accurate tests.

### How VCR works?
When we make the first API call the request goes through full request and respoonse cycle. When the responce is returned. VCR records the API request and response, which it saves as a `cassette`. In other words, VCR stubs it for future use. When the test is run again, no API call is made. Instead, VCR stubs the response. The test will run much faster as a result.

### Installation
VCR works together with [webmock](https://github.com/bblimke/webmock) a library for stubbing and setting expectation of HTTP requests. Add latest version of VCR and Webmock in your `Gemfile`. Run `bundle install` to update.

```ruby
gem 'vcr', '~> 5.0'
gem 'webmock', '~> 3.7', '>= 3.7.6'
```
> Its good to add them in test group gems
> Add them as development dependencies in you are developing a gem.

### Configuration
VCR implements a [configure](https://relishapp.com/vcr/vcr/v/1-6-0/docs/configuration/) block for different configuration. Add the below block in your `test_helper.rb`

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
- `allow_http_connections_when_no_cassette` - allows actual full request and response cycle, when no cassette found.
- `cassette_library_dir` - directy where cassettes are stored.
- `hook_into`- specifies a stubbing library
- `ignore_request` - default is `false` when `true` makes full request without cassettes even if they exists.
- `ignore-localhost` -  prevent VCR
from having any affect on localhost requests
- `default_cassette_options` - takes a hash
that provides defaults for each cassette you use

### Testing API calls
We are now ready to start testing APIs calls. For demo purpose lets use [JSONplaceholder](https://jsonplaceholder.typicode.com/) a fake API for developers.

```ruby
class VCRTest < Test::Unit::TestCase
  def test_fetch_post
    VCR.use_cassette("get_post") do
      response = Net::HTTP.get_response(URI('https://jsonplaceholder.typicode.com/posts/1'))
      assert_equal(200,response.status)
    end
  end
end
```
Run the test once and VRC will record in `cassette_library_dir` you defined in configure block, for our case `cassettes/get_post.yml`. When you run test again no real connection is made making the process very fast. You may realise the speed when you have very many API endpoints to test.
