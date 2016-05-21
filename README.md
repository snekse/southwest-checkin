# Southwest Checkin 2.0

[![Build Status](https://travis-ci.org/aortbals/southwest-checkin.svg?branch=master)](https://travis-ci.org/aortbals/southwest-checkin) [![Coverage Status](https://coveralls.io/repos/aortbals/southwest-checkin/badge.svg?branch=master&service=github)](https://coveralls.io/github/aortbals/southwest-checkin?branch=master)

Automatically checks in passengers for their Southwest Flight.

Version 2.0 of this project is a complete rewrite of the service. The brittle HTML parsing and form submissions are a thing of the past. A much better approach is being taken to automate checkins. And, importantly, the new version has a robust test suite. It is even written in a new language (Ruby) and framework (Rails).

If you are interested in the old version, see the [1.0 branch](https://github.com/aortbals/southwest-checkin/tree/1.0).

## Features

- Accounts
    - an easy and convient way to manage your reservations
    - view or remove your reservations at any time
    - increased security
- Email Notifications
    - Notified when a reservation is added
    - Notified on successful checkin
- Checks in all passengers for a given confirmation number
- Secured via HTTPS
- Modern UI
- Modern background processing and job scheduling
- Full test suite


## Local Installation

1. While not strictly required, it is recommended to install [`rbenv`](https://github.com/sstephenson/rbenv) and [`ruby-build`](https://github.com/sstephenson/ruby-build) to manage ruby versions in development. Ruby 2.2 or greater is required.

2. Required dependencies

    - Ruby 2.2 or greater
    - Postgres
    - Redis

3. After installing the aforementioned dependencies, install the ruby dependencies:

    ```shell
    bundle install
    ```

4. Create and seed the database:

    ```shell
    rake db:create db:migrate db:seed
    ```

5. Adding some basic test data for development:

    ```shell
    rake dev:prime
    ```

6. Copy `.env.example` to `.env`. The defaults should work in development.

    ```shell
    cp .env.example .env
    ```
7. Run the tests:

    ```shell
    rspec
    ```

8. Run the development server:

    ```
    rails s
    ```

9. Run sidekiq to process jobs:

    ```
    bundle exec sidekiq
    ```


## Heroku Installation

1. While not strictly required, it is recommended to install [`rbenv`](https://github.com/sstephenson/rbenv) and [`ruby-build`](https://github.com/sstephenson/ruby-build) to manage ruby versions in development. Ruby 2.2 or greater is required.

    - Note: Replace anything inside of <> with your projects information.
    - Note: These instructions do not include steps to add email support.
    - Note: This tutorial assumes you're using a Mac with OSX, if you're running something else you may need to make a few adjustments for your OS but should work mostly the same.
    - Note: I recommend using Homebrew to install dependencies.

2. Required dependencies

    - Ruby 2.2 or greater
    - Postgres
    - Redis
    - [Heroku Toolbelt](https://toolbelt.heroku.com/)

3. Setup heroku and download latest version of the code:

    - Create an account on Heroku
    - From the web interface create a new app
    - From the web interface add the following resources to your project:
        - Redis To Go
        - Heroku Postgres
    - Create a folder and `cd` into it using Terminal

    ```shell
    cd Your/Project/Folder
    git clone https://github.com/aortbals/southwest-checkin.git
    heroku git:remote -a <YOUR_HEROKU_APP_NAME>
    ```

4. Get sidekiq to load the redis add-on's URL

    ```shell
    heroku config:set REDIS_PROVIDER=REDISTOGO_URL
    ```


5. Add ruby version to Gemfile:
    
    - Open the `Gemfile` located in the project folder and add the following lines near the top, right below `source 'https://rubygems.org'"`
    
    ```
    ruby '2.2.0'
    gem 'rails_12factor', group: :production
    ```

6. Remove line from `config/environments/production.rb`:

    ```
    config.logger = ActiveSupport::TaggedLogging.new(Syslogger.new("southwest-checkin", Syslog::LOG_PID, Syslog::LOG_LOCAL7))
    ```

7. Configure Heroku:

    ```shell
    heroku config:set SITE_NAME='Southwest Checkin' SITE_URL=<HEROKU_URL> ASSET_HOST=<HEROKU_URL> MAILER_DEFAULT_FROM_EMAIL=<YOUR_EMAIL> MAILER_DEFAULT_REPLY_TO=<YOUR_EMAIL> DEPLOY_BRANCH=master DEPLOY_USER=deploy DEPLOY_PORT=22 MAILER_ADDRESS= MAILER_DOMAIN= MAILER_USERNAME= MAILER_PASSWORD= MAILER_DEFAULT_HOST= DEPLOY_DOMAIN= DEPLOY_TO= DEPLOY_REPOSITORY= AIRBRAKE_API_KEY= AIRBRAKE_HOST= AIRBRAKE_DEPLOY_NOTIFICATION=false
    ```

8. After installing the aforementioned dependencies (see step 2), install the ruby dependencies:

    ```shell
    gem install bundler
    bundle install
    ```

9. Now commit and push your app to heroku:
    
    ```shell
    git add .
    git commit -am ""
    git push heroku master
    ```

10. Create and seed the database:

    ```shell
    heroku run rake db:create db:migrate db:seed
    ```

11. Now browse to your Heroku's app url and start adding reservations.


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Write rspec tests
5. Push to the branch (`git push origin my-new-feature`)
6. Create new Pull Request
