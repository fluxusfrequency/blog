# Getting Started With Active Job

With the [announcement of Rails 4.2](http://edgeguides.rubyonrails.org/4_2_release_notes.html) came some exciting news: Rails now has built in support for executing jobs in the background with [Active Job](https://github.com/rails/rails/tree/master/activejob). The ability to schedule newsletters, follow-up emails, and database housekeeping tasks is vital to almost any production application. In the past, developers had to hand-roll this functionality using gems like [Resque Scheduler](https://github.com/resque/resque-scheduler) or [Sidekiq Scheduler](https://github.com/Moove-it/sidekiq-scheduler). With the release of 4.2, setting up jobs for workers to execute at a later time is built in to Rails, making developers' lives easier. In this article, we'll take a look at how to set up Active Job to send a follow up email to a new user in a sample Rails application.

## Updating Rails

You'll need Rails 4.2.0beta1 or greater if you want to use Active Job as part of Rails (you can also use require it as a gem in older versions of Rails). This tutorial is based on `4.2.0beta2`. You can upgrade your Rails version by specifying `gem 'rails', '~> 4.2.0beta2` in your `Gemfile` (as of this writing, `4.2.0beta2` is not yet released, so you'll want to set the git remote to `gem 'rails', github: 'rails/rails'`). Don't forget to run `bundle update`.

## Setting Up Resque

In order to send emails outside of our main application process, we'll need to make use of a queueing system. There are many choices of technology for setting up background workers available, and Active Job abstracts the differences between them. As of this writing, it can interface with nine queueing systems. Today we'll use Resque, as it is a widely-used and simple choice.

To use Resque, you'll need to make sure you have [Redis](http://redis.io/) installed. If you don't, I recommend using [Homebrew](http://brew.sh/). Otherwise, you can follow the [instructions for download](http://redis.io/download) from the official site. Once it's set up, make sure `redis-server` is running.

Once you've got Redis up and running, you'll need to install and configure the Resque gem. We'll also need `resque-scheduler` to use ActiveJob. Add them the Gemfile with `gem 'resque'` and `gem 'resque-scheduler'` and run `bundle install`. You'll also need to create a Resque configuration file:

```ruby
#config/initializers/resque.rb

Resque.redis = Redis.new(:url => 'http://localhost:6379')
Resque.after_fork = Proc.new { ActiveRecord::Base.establish_connection }
```

We'll also require the Resque and Resque Scheduler rake task, so we can start our workers with rake:

```ruby
#lib/tasks/resque.rake

require 'resque/tasks'
require 'resque/scheduler/tasks'

namespace :resque do
  task :setup do
    require 'resque'
    require 'resque-scheduler'
  end
end
```

We can now try to start a worker by running `QUEUE=* rake environment resque:work`. If everything's working right, we should be able to see it in the Resque console by running `resque-web` and visiting `http://0.0.0.0:5678/overview`. If you see "0 of 1 Workers Working", all's well. We'll also need to boot up the scheduler process in a separate process, with `rake environment resque:scheduler`.

## Creating the Mailer

Now we need to give our worker an email to send. Let's imagine that we want to send a weekly newsletter to keep our users engaged, and drive traffic to our site. We'll begin by setting up a mailer. Our mailer will take a hash of params that includes an `email` key.

```ruby
#app/mailers/user_mailer.rb

class UserMailer < ActionMailer::Base
  default from: 'noreturn@example.com'

  def follow_up_email(email)
    mail(
      to: email,
      subject: 'We hope you are enjoying our app'
    )
  end
end
```

We'll also need to write a template for the mailer to use. To send a valid multi-part email, we would also need create an html version. I'll leave that part to your imagination.

```ruby
#app/views/user_mailer/follow_up_email.text

Hey, we saw that you recently signed up for our app.
We hope you're enjoying it!
```

## Creating the Job

Now that we have a working mailer, we're ready to set up Active Job. We'll begin by configuring it to use Resque.

```ruby
#config/initializers/active_job.rb

ActiveJob::Base.queue_adapter = :resque
```

With Active Job in place, we can now create the job that the background worker will use to send the email. The conventions for a job include giving it a queue, and defining a `perform` method.

```ruby
#app/jobs/follow_up_email_job.rb

class FollowUpEmailJob < ActiveJob::Base
  queue_as :email

  def perform(email)
    UserMailer.follow_up_email(email).deliver_now
  end
end
```

Now when a user signs up, we can set the job to send the email later. Note that the syntax is a little bit different than what is described in [the documentation](http://edgeguides.rubyonrails.org/active_job_basics.html).

```ruby
#app/controllers/users_controller.rb

class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.create(user_params)
    FollowUpEmailJob.new(@user.email).enqueue(wait: 10.seconds)
    # redirect somewhere
  end
end
```

To make this work, we'll need some routes and a view template:

```ruby
#config/routes.rb

Rails.application.routes.draw do
  resources :users, only: [:new, :create]
end
```


```erb
#app/views/users/new.html.erb

<%= form_for @user do |f| %>
  <%= f.email_field :email %>
  <%= f.submit %>
<% end %>
```

## Setting Up Mailcatcher

We can test that this is working properly using the `mailcatcher` gem. You'll want to do a global install with `gem install mailcatcher`. Once we run `mailcatcher`, we'll be able to intercept emails and view them at `http://127.0.0.1:1080`. We'll also need to configure Action Mailer to send the emails to `localhost:1025` via smtp.

```ruby
#config/environments/development.rb

Rails.application.configure do
  ...
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = { :address => "localhost", :port => 1025 }
end
```

## Trying It Out

Now everything is set. We'll sign up a new user and watch the job get enqueued in the queue, then catch the mail in Mailcatcher. At this point, we have four processes running in the terminal:

- `mailcatcher`
- `QUEUE=* rake environment resque:work`
- `rake environment resque:scheduler`
- `rails server`

It's time. Open up `http:/http://0.0.0.0:5678` in your browser and watch the Resque dashboard. In another tab, visit `http://127.0.0.1:1080` to view sent mails in Mailcatcher.

Now visit `localhost:3000/users/new` and sign up a new user. Ten seconds later, a new job should appear in the `emails` queue of the Resque dashboard. Then we should see the email come through in the Mailcatcher tab just afterward.

## Using Acive Job with Action Mailer

The example here shows how to schedule any job with ActiveJob. But since ActiveJob comes backed into ActionMailer, we could have also scheduled the job directly with the `UserMailer` in the controller.

```ruby
#app/controllers/users_controller.rb

class UsersController < ApplicationController
  ...
  def create
    ...
    UserMailer.follow_up_email(email).deliver_later!(wait: 10.seconds)
  end
end
```

## Conclusion

Active Job makes scheduling jobs easier. It's also a great way to set up your job infrastructure without knowing too much about what queueing system you're using. If you needed to switch to [Sidekiq](https://github.com/mperham/sidekiq) or [Delayed Job](https://github.com/collectiveidea/delayed_job) in the future, it would be a painless switch: just set the ActiveJob adapter to the appropriate technology.

As Active Job matures, hopefully we'll see the ability to schedule recurring tasks, like sending monthly newsletters.

