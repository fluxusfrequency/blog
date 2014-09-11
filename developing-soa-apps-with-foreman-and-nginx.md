# Developing Service-Oriented Architecture Projects Locally Using Scripts, Foreman and Nginx

## MOAR!

<img src="moar.jpg"/>

Everyone knows moar is better. Moar kittens, moar money, moar apps. Why settle for one Rails project, when you can have three? Let's throw in a gem and an AngularJS app while we're at it! Now we're cookin'!

Seriously, though, there are many ways that companies stand to gain from split their application into multiple projects that run alongside each other. If we're being good programmers and following the Single Responsibility Principle (SRP), it makes sense to embrace it at all levels of organization, from our methods and classes up through our project structure. 

## Service Oriented Architecture

In the mid-2000s, there began to be a rise in the use of Web Services: applications that provide data or functionality to others. When you stick a few of these services together, and coordinate them in some kind of client-facing interface, you've built an application with a Service Oriented Architecture (SOA). Here are some of the benefits of an approach like this:

- Data storage is encapsulated
- You can reuse services between multiple applications (e.g. authentication)
- You can monitor messages sent between services for business intelligence
- Services are modular, and can be composed into new applications without repetition of common functionality

## A Sample Project

Today we'll take a look at a sample application built from the ground up using a Service Oriented Architecture.

Auth / Landing
Front end proxy app
API

Diagram

## Setting Up GitHub

Organization
Processes Repo
Other Repos

## Hacking at Home

In production, each of these projects might live on their own server, and communicate via HTTP or HTTPS on the web. Hopefully, there would be an operations team dedicated to making sure all of these servers stayed up and running, and communicating properly. 

Developing all of these projects on your local dev machine is no cakewalk, either, though. Having to run three different servers in separate terminal sessions, remember which port goes to which, and making sure that you're redirecting to the right place when you want to jump from to another...it can be a lot of mental overhead.

Here's one way to do it, that I've found much easier to deal with, after some initial setup.

First, create a project structure that looks something like this:

```
code
|- panda-auth
|- panda-api
|- panda-client
|- processes
```

### Git Your S#*& Together

<img src="ship.jpg" />

It can be pretty annoying to have your projects all out of sync. Here's a bash script you can use to pull all of your projects down and update them in one go. Create it as `git_update.sh` inside of the processes folder.

You can specify the branch by running `sh ./git_update <feature-name>`.

```
#git_update.sh

#!/bin/bash

BRANCH=$1
: ${BRANCH:="master"}

cd ../panda-auth   && git checkout $BRANCH && git pull origin $BRANCH
cd ../panda-api    && git checkout $BRANCH && git pull origin $BRANCH
cd ../panda-client && git checkout $BRANCH && git pull origin $BRANCH

```

We can do something similar for bundling and running migrations. This file is called `bundle_and_migrate.sh`:

```
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

### Call In The Foreman

<img src="foreman.gif" />

The [foreman gem](https://github.com/ddollar/foreman) is my favorite way to manage multiple applications: it runs them all in a single terminal session. It's pretty simple to get set up, and saves you having to run a lot of shell sessions (and a lot of headaches). 

First, we'll need to `gem install foreman`, to make sure we have the global executable available. Then, we'll set up a `Procfile` to tell it which processes we want it to run. This is a lot like the `Procfile` you may have used if you've ever deployed anything to [Heroku](https://www.heroku.com/). We'll create ours in the `processes` directory, since that's where we're keeping things that pertain to all of the projects in our SOA app.

```
#Procfile

auth:     sh -c 'cd ../panda-auth   && bundle exec rails s -p 3000'
api:      sh -c 'cd ../panda-api    && bundle exec rails s -p 3001'
client:   sh -c 'cd ../panda-client && bundle exec rails s -p 3002'

```

This will work great as long as all of your apps are running on the same gemset. If not, you will need to check out the [subcontractor gem](https://github.com/pitluga/subcontractor).

Sweet. Now everything should be easy right? Just open up your browser and navigate to `http://localhost:3000`. Oh, and maybe pop open two more tabs with ports `3001` and `3002` in them. Man, wouldn't it be nice if we could just do everything under a single host name?

## Setting Up Nginx

To get around the problem of having three different localhosts, we can use [NGINX](http://nginx.com/). If you're not familiar with NGINX, it's an Apache alternative that acts as "a web server, a reverse proxy server and an application load balancer". Using it, we can serve up all three of our apps from the same host, and make things a whole lot easier on ourselves during the development process.

To install it on your dev machine, I recommend using [Homebrew](http://brew.sh/). If you have it, getting NGINX on your machine is as simple as `brew install nginx`. If you don't have Homebrew, you can try one of these [alternatives](http://wiki.nginx.org/Install).

Once nginx is installed, we'll want to located `nginx.conf`. If you installed using homebrew, your copy will be located at `/usr/local/etc/nginx/nginx.conf`. Otherwise, you'll want to use [ack](), `mdfind`, or another search tool to locate it. Once you've located it, open it up in your text editor and make these changes:

```
#nginx.conf




```



get around the three localhosts problem
installing
nginx.conf
proxy pass

## Dealing With Redirects

app_config.yml
`redirect_to Rails.application.config.app['service-urls']['myapp-frontend']`

## Profit

