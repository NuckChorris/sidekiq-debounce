# Sidekiq::Debounce

[![Travis CI](http://img.shields.io/travis/hummingbird-me/sidekiq-debounce/master.svg)](https://travis-ci.org/hummingbird-me/sidekiq-debounce)
[![CodeClimate](http://img.shields.io/codeclimate/github/hummingbird-me/sidekiq-debounce.svg)](https://codeclimate.com/github/NuckChorris/sidekiq-debounce)
[![Coverage](http://img.shields.io/codeclimate/coverage/github/hummingbird-me/sidekiq-debounce.svg)](https://codeclimate.com/github/NuckChorris/sidekiq-debounce)
[![RubyGems](http://img.shields.io/gem/v/sidekiq-debounce.svg)](https://rubygems.org/gems/sidekiq-debounce)

Sidekiq::Debounce is a client-side Sidekiq middleware which provides a way to
easily rate-limit creation of Sidekiq jobs.

When you create a job via `#perform_in` on a Worker with debounce enabled,
Sidekiq::Debounce will prevent other jobs with the same arguments from being
created until the time has passed. Every time you create another job with those
same arguments prior to the job being run, the timer is reset and the entire
period must pass again before the job is executed.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'sidekiq-debounce'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install sidekiq-debounce

## Usage

Add `Sidekiq::Debounce` to your client middleware chain, and then add
`sidekiq_options debounce: true` to the worker you wish to debounce.

Use `#perform_in` instead of `#perform_async` to set the timeframe.

```rb
# rails example
# config/initializers/sidekiq.rb
require "sidekiq/debounce"
Sidekiq.configure_client do |config|
  config.client_middleware do |chain|
    chain.add Sidekiq::Debounce
  end
end
```

```rb
class YourWorker
  include Sidekiq::Worker
  sidekiq_options debounce: true # add this option

  def perform(id)
    ...
    ...
  end
end

# YourWorker.perform_in 1.day, 42
# YourWorker.perform_in 2.days, 42
# will create only one schedule with 42 as a parameter, and its schedule datetime will be 2 days later (not the initial 1 day)
```

## Contributing

1. Fork it ( https://github.com/hummingbird-me/sidekiq-debounce/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
