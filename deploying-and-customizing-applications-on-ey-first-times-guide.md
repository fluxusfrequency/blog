# Deploying and Customizing Applications on Engine Yard: A First-Timer's Guide

I've tried a lot of different Platform as a Service (PaaS) providers for hosting my applications. Some of them make it super-easy to get everything running on the server, but the magic gets in the way when you need to customize things. Others give you full control, but it can be time-consuming to get all of your dependencies properly set up. It would be nice to have some of the boilerplate taken care of for me, while still retaining full control of my server environment.

I recently deployed a production application to Engine Yard (EY), and was pleasantly surprised to find that this is exactly the kind of service they're offering. It was a breeze to get Redis, Cron, and other tools I needed up and running, but I also had root access to my server and could SSH in as I would with a bare server. My favorite feature was the ability to push custom Chef recipes to my server, making it super-easy to tweak the server as I needed without having to spend a lot of time downloading Ubuntu packages and managing user permissions.

There was a little bit of a learning curve to getting set up the first time, so I went ahead and documented the process to help other first-timers get up and running with a minimum of fuss.

## Setting Up An Engine Yard Environment

I started out with a production application in a repository that was ready to go. It just needed a server to run on.

I went to Engine Yard and signed up for [a free trial](https://www.engineyard.com/trial) account.

Once I was done filling out my contact and billing information, I created my first "application" resource on Engine Yard Cloud. Here are the steps I followed to get it running from there.

First, I created a "production" environment for my application, and configured it. I was given four choices of server beefiness - single instance, staging, production, or custom. I went with a production box, and added Phusion Passenger and PostgreSQL to the stack. Since I was deploying a Rails app, I also added Ruby 2.2.0 and set up my migration command. I was happy to see that EY would backup by my database and take a server snapshot on a recurring schedule. I opted in for that service.

While the server was being provisioned, there were a few access-related tasks I had to take care of as well. First, I added the SSH keys from my development machine to my production environment. Here are the steps I followed:
- Ran `ssh-keygen -t rsa`, since I didn't have a key on my development machine yet.
- Added it to my SSH agent with `ssh-add ~/.ssh/id_rsa`.
- Copied the key to my clipboard with `pbcopy < ~/.ssh/id_rsa.pub`.
- Visited my EY Cloud dashboard, then clicked on Tools > SSH Keys.
- Named the key "development_machine" and pasted it into the text area.
- Hit the big "apply button" on my app's "production" environment page.

I also had to add an SSH key EY provided to my GitHub account. This allowed EY to grab my code and push it to the server directly.

A few minutes later, the server and my credentials were all set up, and I was ready to deploy. I saw a big "deploy" button, so I hit that. There was a problem with my deploy, so I decided to dig into it from the command line.

## Using the `engineyard` Gem

### Configuring and Deploying

I did a little research to figure out why my deploy wasn't working, and figured out that I needed to add a `config/ey.yml` file to my Rails project. For that, it would be easiest use the [engineyard gem](https://github.com/engineyard/engineyard).

To install the gem globally, I ran `gem install engineyard`. Then I initialized an EY configuration file using `ey init`. I checked out the `config/ey.yml` file it generated. Everything looked good, so I committed and pushed it up to GitHub.

This time, I deployed using `ey deploy`, and it worked like a charm.

### Logging In and Out
- `ey login`
- `ey logout`
- `ey whoami`

### Custom Deploys

- `ey status` shows the status of your most recent deploy
- `ey timeout-deploy` marks the current deploy as failed and begins a new deploy
- `ey rollback` revert to a previous deployment

### Environments
- `ey environments` shows the environments for this app (pass `--all` to see all environments for all apps)
- `ey servers` shows all the servers for an environment (if you have multiple)
- `ey rebuild` reruns the configuration bootstrap process, useful for security patches and upgrades
- `ey restart` restarts the servers

### Debugging
- `ey logs` shows the logs
- `ey web disable`/`enable` toggles a maintenance page
- `ey ssh` lets you SSH in
- `ey launch` launches app in a browser window

### Customizing The Server Environment
- `ey recipes upload` adds Chef recipes from your dev machine and the remote server
- `ey recipes download` syncs Chef recipes from the remote server to your dev machine
- `ey recipes apply` trigger a Chef run

These last few commands come in really handy when you want to customize your server setup. Let's take a deeper look at how to upload custom Chef recipes to an application environment.

## Chef Recipes

EY uses Chef under the hood to make your deploys quick and easy. There's a default set of recipes that get run every time you deploy.

After the default recipes run, EY runs any custom recipes that you've added to your environment. Since there are [so many Chef recipes available](https://github.com/opscode-cookbooks), getting dependencies set up is straightforward.

Your Chef recipes will run whenever you create a new instance, add an instance to a cluster, run `ey recipes apply`, or trigger a Chef run with from the Cloud Dashboard with the "Upgrade" or "Apply" buttons.

### Getting Set Up

To add recipes to your application, you'll need to fork the [Engine Yard Cloud Recipes repo](https://github.com/engineyard/ey-cloud-recipes). Then, clone your fork down to your development machine, in a different directory than your application.

### Default Recipes

The EY Cloud Recipes repo comes with comes with cookbooks for most of the things you would ever need - Sidekiq, Redis, Solr, Elasticsearch, Cron, PostgreSQL Extensions, and much more. Here's what I did to add Redis to my project.

1) Opened `/cookbooks` and found the subdirectory I wanted (`/redis`)
2) Uncommented `include_recipe redis` in `cookbooks/main/recipes/default.rb`
3) Saved the file, committed it, and push to my forked repo
4) Uploaded the recipes to my app with `ey recipes upload -e production`
5) Applied the recipes to my app with `ey recipes apply -e production`

I took a look at my EY Cloud dashboard, and a few short moments later, Redis was running on my server.

### Custom Recipes

I wanted to add HTTP Basic Auth to my server, but it wasn't one of the recipes in the repo, so I wrote my own recipe for it. Here's how I did it.

1) Opened my `ey-cloud-recipes` fork repo.
2) Ran `rake new_cookbook COOKBOOK=httpauth`. This generated a bunch of files under `cookbooks/httpauth/`.
3) Edited `cookbooks/httpauth/recipes/default.rb` like this:
  ```
  sysadmins = search(:users, 'groups:sysadmin')

  template "/etc/nginx/htpasswd.users" do
    source "nginx/htpasswd.users.erb"
    owner node['staging']['nginx']['user']
    group node['staging']['nginx']['user']
    mode "0640"
    variables(
      :sysadmins => sysadmins
    )
    notifies :restart, "service[nginx]", :delayed
  end
  ```
4) Created a file at `cookbooks/httpauth/templates/default/nginx/htpasswd.users.erb` with this code in it:
  ```
  <% @sysadmins.each do |sa| -%>
    <%= sa["id"] %>:<%= sa["htpasswd"] %>
  <% end -%>
  ```

5) Added the recipe to my main cookbook (`cookbooks/main/recipes/default.rb`) by adding this line: `include_recipe "httpauth"`
6) Checked my syntax with `rake test`
7) Committed and pushed to my fork.
8) Uploaded the recipes to my app with `ey recipes upload -e production`
9) Applied the recipes to my app with `ey recipes apply -e production`

The recipe was successfully added to my server in the `/etc/chef-custom/` directory. I know because I SSH'd in and took a look around. How did I do that? I'm glad you asked.

## SSHing In

If you ever need to confirm that your Chef recipes are configuring the server the way you expected, or need to access your server directly with root access for any other reason, you can SSH in. There are three ways to do this:

1) Run `ssh username@123.123.123.123`, the old-fashioned way. You can find your server's IP address in the EY Cloud dashboard.
2) Click on the SSH link in your application dashboard on EY instead.
3) Run `ey ssh` from the application directory on your dev machine.

When you login to your server, some helpful information about your app's server environment is displayed:

```
Applications:
myapplication:
cd /data/myapplication/current # go to the application root folder
tail -f /data/myapplication/current/log/production.log # production logs
cat /data/nginx/servers/myapplication.conf # current nginx conf


SQL database:
cd /db # your attached DB volume

PostgreSQL:
tail -f /db/postgresql/$1.$2/data/pg_log/* # logs
pg_top -dmyapplication


Inspect node data passed to chef cookbooks:
sudo gem install jazor
sudo jazor /etc/chef/dna.json
sudo jazor /etc/chef/dna.json 'applications.map {|app, data| [app, data.keys]}'
```

Pretty cool. It's nice to have access to get the kind of control you would have on a typical bare-bones hosting service, without being responsible for setting up every dependency by hand.

## Conclusion

If you're anything like me, you drag your feet about trying new things when it comes to ops. Perhaps you've felt the pain of trying to take a server from vanilla Ubuntu to a custom build with Cron, Redis, Elasticsearch and a bunch of other packages, carefully balancing everything so that it doesn't fall apart. Many of us have also experienced getting blocked when using a full-service PaaS that isn't working the way we expect, and not having the control to change things. Experiences like this make it hard for me to get excited about setting up servers, so I typically avoid it when I can.

That said, I'm glad I invested a few minutes in checking out how things work on Engine Yard. Their balance between automation and control seems like a good balance to have. Getting up and running took a little bit of learning, but they had super-helpful [docs](https://support.cloud.engineyard.com/home), and their customer service department was right there on chat or email every time I had a question.

If you haven't given Engine Yard a try, I'd recommend it. It's nice to know that this level of customization is available in the PaaS world.

P. S. What kind of custom Chef recipes are you using on your servers? I know that business requirements can lead to some pretty gnarly setups. Tell me about it in a comment below!


