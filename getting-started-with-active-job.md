# Getting Started With Active Job

With the [announcement of Rails 4.2](http://edgeguides.rubyonrails.org/4_2_release_notes.html) came some exciting news: Rails now has built in support for executing jobs in the background with [Active Job](https://github.com/rails/rails/tree/master/activejob). The ability to schedule newsletters, follow-up emails, and database housekeeping tasks is vital to almost any production application. In the past, developers had to hand-roll this functionality using gems like [Resque Scheduler](https://github.com/resque/resque-scheduler) or [Sidekiq Scheduler](https://github.com/Moove-it/sidekiq-scheduler). With the release of 4.2, setting up jobs for workers to execute at a later time is built in to Rails, making developers' lives easier. In this article, we'll take a look at how to set up Active Job to send a follow up email to a new user in a sample Rails application.

## Updating Rails

You'll need Rails 4.2.0 or greater if you want to use Active Job as part of Rails (you can also use require it as a gem in older versions of Rails). You can upgrade your Rails version by specifying `gem 'rails', '4.2.0beta1'` in your `Gemfile`, then running `bundle update`. You'll also want to run `rake rails:update`, being careful not to overwrite any of your application-specific configuration settings.

## Setting Up Resque

In order to send emails outside of our main application process, we'll need to make use of a queueing system. There are many choices of technology for setting up background workers available, and Active Job abstracts the differences between them. As of this writing, it can interface with nine queueing systems. Today we'll use Resque, as it is a widely-used and simple choice.

To use Resque, you'll need to make sure you have [Redis](http://redis.io/) installed. If you don't, I recommend using [Homebrew](http://brew.sh/). Otherwise, you can follow the [instructions for download](http://redis.io/download) from the official site. Once it's set up, make sure `redis-server` is running.

Once you've got Redis up and running, you'll need to install and configure the Resque gem. We'll also need `resque-scheduler` to use ActiveJob. Add them the Gemfile with `gem 'resque'` and `gem 'resque-scheduler'` and run `bundle install`. You'll also need to create a Resque configuration file:

```
#config/initializers/resque.rb

Resque.redis = Redis.new(:url => 'http://localhost:6379')
Resque.after_fork = Proc.new { ActiveRecord::Base.establish_connection }

```

We'll also require the Resque rake task, so we can start our workers with rake:

```
#lib/tasks/resque.rake
require "resque/tasks"
```

We can now try to start a worker by running `QUEUE=* rake environment resque:work`. If everything's working right, we should be able to see it in the Resque console by running `resque-web` and visiting `http://0.0.0.0:5678/overview`. If you see "0 of 1 Workers Working", all's well.

## Creating the Mailer

Now we need to give our worker an email to send. Let's imagine that we want to send a weekly newsletter to keep our users engaged, and drive traffic to our site. We'll begin by setting up a mailer. First, we'll write the test. For now, we'll set up our mailer's interface to take a hash of options, one of which will be email.

```
#test/mailers/user_mailer_test.rb

require 'test_helper'

class UserMailerTest < ActionMailer::TestCase
  tests UserMailer

  test 'sends a weekly email' do
    email = UserMailer.follow_up_email(email: 'user@example.com').deliver

    assert_equal 1, ActionMailer::Base.deliveries.length

    assert_includes email.from, 'noreturn@example.com'
    assert_includes email.to, 'user@example.com'
    assert_equal 'We hope you are enjoying our app', email.subject
  end
end
```

This test will fail because we haven't created the mailer yet. Let's do that now.

```
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

If we run the test again, it blows up because there's no template for the email. Let's write that.

```
#app/views/user_mailer/follow_up_email.text

Hey, we saw that you recently signed up for our app.
We hope you're enjoying it!

```

Now the test passes. To send a valid multi-part email, we would also need create an html version. I'll leave that part to your imagination.

## Creating the Job

Now that we have a working mailer, we're ready to set up Active Job. We'll begin by configuring it to use Resque.

```
#config/initializers/active_job.rb

ActiveJob::Base.queue_adapter = :resque
```

With Active Job in place, we can now create the job that the background worker will use to send the email. The conventions for a job include giving it a queue, and defining a `perform` method.

```
#app/jobs/follow_up_email_job.rb

class FollowUpEmailJob < ActiveJob::Base
  queue_as :email

  def perform(email)
    UserMailer.follow_up_email(email)
  end
end
```

Now when a user signs up, we can set the job to send the email later. Note that the syntax is a little bit different than what is described in [the documentation](http://edgeguides.rubyonrails.org/active_job_basics.html).

```
#app/controllers/users_controller.rb

class UsersController < ApplicationController
  def new
    @user = User.new
  end
  
  def create
    @user = User.create(user_params)
    FollowUpEmailJob.new(@user.email).enqueue(wait: 1.minute)
  end
end
```

## Trying it Out

We can test that this is working properly using the `mailcatcher` gem. You'll want to do a global install with `gem install mailcatcher`. We'll also take a look at the Resque queue to make sure we see things happening there. At this point, you should have three processes running:

```
mailcatcher
QUEUE=* rake environment resque:work
rails server
```

Assuming you've already got the standard routes and templates in place to create a new user, we can visit `localhost:3000/users/new` and sign up a new user.

Now to try it out. We'll open up `http:/http://0.0.0.0:5678` in your browser and watch our worker. In another tab, we'll open `http://127.0.0.1:1080/` to view Mailcatcher. When we create the user, the Resque server should show a `follow_up_email` job appear in the `emails` queue. Then, in three minutes, we should see the email come through.


## Statistics

[Activejob Stats gem](https://github.com/seuros/activejob-stats)

## Conclusion
