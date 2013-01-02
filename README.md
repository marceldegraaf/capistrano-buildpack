# Capistrano::Buildpack

Deploy [12-factor](http://www.12factor.net/) applications using Capistrano.

## Installation

Add this line to your application's Gemfile:

    gem 'capistrano-buildpack'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install capistrano-buildpack

## Usage

Here's a basic Capfile that uses `Capistrano::Buildpack`:

    require 'rubygems'

    set :application, "bugsplatdotinfo"
    set :repository, "https://github.com/peterkeen/bugsplat.rb"
    set :scm, :git
    set :additional_domains, ['bugsplat.info']
    
    role :web, "examplevps.bugsplat.info"
    set :buildpack_url, "https://github.com/peterkeen/bugsplat-buildpack-ruby-simple"
    
    set :user, "peter"
    set :base_port, 6700
    set :concurrency, "web=1"

    read_env 'prod'
   
    load 'deploy'
    require 'capistrano-buildpack'
    
This will load a file named `.env.prod` which should consist of environment variables like this:

    SOME_VAR_NAME=some_value
    SOME_OTHER_VAR=something_else
    
This example just unconditionally loads 'prod' but you can have as many different sets as you want,
loading them as appropriate in tasks.
    
To run a deploy:

    $ cap deploy:setup # create the required directory structure and gem install foreman-export-nginx
    $ cap deploy       # actually run a deploy
    
Deploy will do the following things:

* Create the required directory structure at `/apps/<application>`
* Clone/update the buildpack
* Clone/update the code repository
* Apply the buildpack to the code repository
* Create `upstart`-style init files in `/etc/init` and start up the app
* Create an nginx config file at `/etc/nginx/conf.d/<application>.conf and restart nginx

The nginx config will list at least one domain for the app: `<application>.<hostname>`, which in this case is `bugsplatdotinfo.examplevps.bugsplat.info`. Anything
you add to the `:additional_domains` setting gets tacked on at the end.

## Very Important Notes

`Capistrano::Buildpack` will *not* run `bin/release` from the buildpack, so any environment variables that that attempts to set need to be set using `read_env`.
In addition, at the moment the exported nginx config does not have compression turned on.

Also, note that right now this does not support HTTPS. I'm working on it.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
