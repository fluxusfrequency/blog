# Better Service Oriented Architecture Project Development With Scripts, Foreman, and NGINX

## MOAR!

Everyone knows moar is better. Moar kittens, moar money, moar apps. Why settle for one Ruby project, when you can have three? We'll take one Rails app for authorization and one to serve an API. Hey, let's throw in a Sinatra proxy server serving up an AngularJS app to while we're at it! Now we're cookin'!

There are many ways organizations stand to gain by splitting their application into multiple projects running in symphony. If we're being good programmers and following the Single Responsibility Principle (SRP), it makes sense to embrace it at all levels of organization, from our methods and classes up through our project structure. To organize this on the macro level, we can use a Service Oriented Architecture (SOA) approach. In this article, we'll explore some patterns that make it easier to develop SOA apps with a minimum of headaches.

## Service Oriented Architecture

In the mid-2000s, some programmers began to organize their applications in a new way. Led by enterprise behemoths like Microsoft and IBM, the programming community saw a rise in the use of Web Services: applications that provide data or functionality to others. When you stick a few of these services together, and coordinate them in some kind of client-facing interface, you've built an application with a Service Oriented Architecture (SOA). The benefit of this approach remain relevant in modern web development:

- Data storage is encapsulated
- You can reuse services between multiple applications (e.g. authentication)
- You can monitor messages sent between services for business intelligence
- Services are modular, and can be composed into new applications without repetition of common functionality

## A Sample Project

To illustrate some processes that make it easier to develop an SOA project, we'll imagine that we're building a project called `Panda`, that is composed of three services:

PandaAPI: A RESTful API that serves data about Giant Pandas
PandaAuth: Login page and user authentication service
PandaClient: An AngularJS app sitting atop a Sinatra proxy server

## Setting Up GitHub

To deal with an SOA project like this, it's helpful to make sure you have everything well-structured on GitHub, so that all developers working on it can get up to speed quickly, and stay in sync with each other. I recommend creating an [organization](https://github.com/organizations/new), and setting up all of the service project repositories under that organiztion. For Panda, I would start with a structure that looks like this:

```
panda-org (organization)
|- panda-auth (repo)
|- panda-api (repo)
|- panda-client (repo)
|- processes (repo)
```

The first three repos will hold the actual service projects, and the `processes` repo will hold scripts and processes that are shared between them.

## Git Your S#*& Together

It can be pretty annoying to have your projects all out of sync. To make it easier to keep things up to date, here's a bash script you can use to pull all of your projects down and update them in one go. Inside of the processes folder, touch a file called `git_update.sh`.

```bash
#git_update.sh

#!/bin/bash

BRANCH=$1
: ${BRANCH:="master"}

cd ../panda-auth   && git checkout $BRANCH && git pull origin $BRANCH
cd ../panda-api    && git checkout $BRANCH && git pull origin $BRANCH
cd ../panda-client && git checkout $BRANCH && git pull origin $BRANCH
```

When executing this script, you can specify the branch by running `sh ./git_update <feature-name>`.

We can do something similar for bundling and running migrations. This file is called `bundle_and_migrate.sh`:

```bash
#!/bin/bash

# Load RVM
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*
export PATH="/usr/local/bin:/usr/local/sbin:~/bin:$PATH"
[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # Load RVM function

cd ../panda-auth   && bundle && bundle exec rake db:migrate
cd ../panda-api    && bundle && bundle exec rake db:migrate
cd ../panda-client && bundle && bundle exec rake db:migrate
```

Now the projects are all updated and ready to go, and we want to begin developing some features. We could write another script to go start `rails server` in each of our directories, but there is a better way.

## Call In The Foreman

The [foreman gem](https://github.com/ddollar/foreman) is my favorite way to manage multiple applications: it runs them all in a single terminal session. It's pretty simple to get set up, and saves you having to run a lot of shell sessions (and a lot of headaches).

First, we'll need to `gem install foreman`, to make sure we have the global executable available. Then, we'll set up a `Procfile` to tell it which processes we want it to run. This is a lot like the `Procfile` you've probably used if you've ever deployed anything to [Heroku](https://www.heroku.com/). We'll create ours in the `processes` directory, since that's where we're keeping things that pertain to all of the projects in our SOA app.

```
#Procfile

auth:     sh -c 'cd ../panda-auth   && bundle exec rails s -p 3000'
api:      sh -c 'cd ../panda-api    && bundle exec rails s -p 3001'
client:   sh -c 'cd ../panda-client && bundle exec rails s -p 3002'
```

This will work great as long as all of your apps are running on the same gemset. If not, you will need to check out the [subcontractor gem](https://github.com/pitluga/subcontractor).

From the `processes` folder, run `foreman start`. Sweet. Now everything is all set up. Just open up your browser and navigate to `http://localhost:3000`. Oh, and pop open two more tabs for `http://localhost:3001` and `http://localhost:3002` in them.

Man, wouldn't it be nice if we could just run everything under a single host name?

## NGINX, NGINX #9

To get around the problem of having three different localhosts, we can use [NGINX](http://nginx.com/). If you're not familiar with NGINX, it's an Apache alternative that acts as "a web server, a reverse proxy server and an application load balancer" (from the official site). We can use it to serve up all three of our apps from the same host, and make things a whole lot easier on ourselves.

To install NGINX, I recommend using [Homebrew](http://brew.sh/). If you have Homebrew, installation is as simple as `brew install nginx`. If you don't, you can try one of these [alternatives](http://wiki.nginx.org/Install).

Once NGINX is installed, we'll want to locate our `nginx.conf`. If you installed using Homebrew, it will be located at `/usr/local/etc/nginx/nginx.conf`. Otherwise, you'll want to use [ack](http://quickleft.com/blog/looking-for-a-needle-in-a-haystack-or-using-ack-to-improve-your-development-workflow), `mdfind`, or another search tool to locate it. Once you've located it, open it in your text editor and locate the `server` section. Find the block that starts with `location /` (line 43 for me) and replace it with the following:

```
#nginx.conf

http {
  ...

  server {
    listen       8080;
    ...

    # Client
    location / {
      proxy_pass        http://127.0.0.1:3002;
      proxy_set_header  X-Real-IP  $remote_addr;
    }

    # Auth
    location /auth {
      proxy_pass        http://127.0.0.1:3000;
      proxy_set_header  X-Real-IP  $remote_addr;
    }

    # API
    location /api {
      proxy_pass        http://127.0.0.1:3001;
      proxy_set_header  X-Real-IP  $remote_addr;
    }

  ...
  }
  ...
}
```

Now start NGINX with the `nginx` command. With these `proxy_pass` settings in place, we should be able to visit see all of our apps from `http://localhost:8080`:

- `/` takes us to the client app
- `/auth` takes us to the auth app
- `api` takes us to the API app

## Dealing With Redirects

One last tricky part of developing SOA apps is figuring out how to deal with url redirects between our apps. Let's say that you want the client app to redirect users to the auth app if they haven't logged in yet. You would probably want to do something like this in the client app:

```ruby
#app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  before_action :check_user

  private

  def check_user
    if !session[:user_id]
      redirect_to '/auth'
    end
  end
end
```

Sounds good, and it should work locally. But it could be a problem if some of your apps are served from subdomains in production. Fortunately, there's an easy way to get around this. Create a `config/service_urls.yml` file in each project. Inside of it, we'll define the url for each app:

```ruby
#config/service_urls.yml

defaults: &defaults
  service-urls:
    panda-api: 'localhost:8080/api'
    panda-auth: 'localhost:8080/auth'
    panda-client: 'localhost:8080'

development:
  <<: *defaults

test:
  <<: *defaults

production:
  service-urls:
    panda-api: 'TBD'
    panda-auth: 'TBD'
    panda-client: 'TBD'
```

We'll also need to register this configuration file in `config/application.rb`:

```
#config/application.rb

module PandaClient
  class Application < Rails::Application
    ...
    # In Rails 4.2, you can use:
    Rails.configuration.urls = config_for(service_urls)[Rails.env]

    # For older Rails versions, use:
    Rails.configuration.urls = YAML.load(File.read(Rails.root.join('config', 'service_urls.yml')))[Rails.env]
  end
end
```

With this configuration in place, we can now update the url redirect to look like the following, and it will work in all environments:

```ruby
#app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  ...
  def check_user
    if !session[:user_id]
      redirect_to auth_url
    end
  end

  def auth_url
    @auth_url ||= Rails.application.config.urls['service-urls']['panda-auth']
  end
end
```

## All Your App Are Belong To Us

I hope that you have a better idea of what it takes to develop an application using Service Oriented Architecture. Juggling several services can be pretty confusing, but if you start with the right set up, it makes things a lot easier. All your apps will be under control if you manage them from a central place, and using the strategies discussed in this article should help make the process less painful. Good luck!

Until next time,

Happy Hacking!
