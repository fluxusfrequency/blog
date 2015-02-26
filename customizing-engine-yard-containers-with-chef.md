# Customizing Engine Yard Application Environments With Chef

I recently started using Engine Yard for a side project of mine.
I realized that there are probably a lot of people who have never tried
it before, so I thought I would document the steps I followed along the
way to get up and running.

Just in case you don't know what Engine Yard is all about,

One of the cool things about Engine Yard is it's easier to get a handle on the Unix environment your
app is running on than it is with some other hosting solutions. One of
the best ways to get things set up on your instance is with Chef. Chef
is ...

## Setting Up An Engine Yard Environment

First set up an account: sign up for a free trial.

Next create an application, then create an environment for it.

Make up an environment name ("my_app")
Add EY SSH key to your git host (GitHub)
Select server stack (Passenger)
Select Ruby version, database, what to do on server failure
Add dev machine SSH keys to EY
Configure DB backup schedule, snapshot schedule
Choose how much beef you want (single instance, staging, production, custom)
Set migration task, email notifications
Wait for it to be provisioned
Add your ssh key
  - If you don't have one, `ssh-keygen -t rsa`
  - Add it to your SSH agent with `ssh-add ~/.ssh/id_rsa`
  - `pbcopy < ~/.ssh/id_rsa.pub`
  - EY Cloud -> Tools -> SSH Keys
  - Name the key something and paste it into the text area
  - Hit the big "apply button" on the page for your app environment
  - Wait for it to update
When it is, you can deploy from the app with the deploy button, or with the EY Gem
I had to use the gem the first time.

## Install the EY Gem

- `gem install engineyard`
- `ey init` will generate a `config/ey.yml` file. Commit and push it.
- `ey deploy`

Other things you can do with EY Gem:

- `ey recipes upload`/`download` sync Chef recipes between your dev
  machine and remote server (see Chef section below).
- `ey recipes apply` trigger a Chef run
- `ey timeout-deploy` marks the current deploy as failed and begins
  a new deploy
- `ey status` shows the status of your most recent deploy
- `ey environments` shows the environments for this app (pass `--all` to
  see all environments for all apps).
- `ey servers` shows all the servers for an environment (if you have
  multiple)
- `ey logs` shows the logs
- `ey rebuild` rerun the configuration bootstrap. Used for security
  patches, for example
- `ey rollback` revert to a previous deployment
- `ey web disable`/`enable` toggles a maintenance page
- `ey restart` restarts the servers
- `ey ssh` lets you SSH in
- `ey launch` visits your app in a browser window
- `ey whoami` see which EY account you're using
- `ey login`/`logout`

## Chef Recipes

EY uses Chef under the hood to make your deploys quick and easy. They
also let you customize and add recipes to make getting your server set
up as straightforward as possible.

Your Chef recipes will run whenever you create a new instance, add an
instance to a cluster, run `ey recipes apply`, or trigger a Chef run with from the Cloud
Dashboard with the "Upgrade" or "Apply Buttons".

Since EY uses Chef under the hood, it will do two runs: first, the
default run that builds your instance, then a second run that executes
your custom recipes.

### Getting Set Up

- Fork this repo: `https://github.com/engineyard/ey-cloud-recipes`
- Clone it down in a different dir than your app

### Default Recipes

It comes with most of the things you would ever need - Sidekiq, Redis,
Solr, Elasticsearch, Crun, PostgreSQL Extensions

- look in `cookbooks`, find one you want
- to turn it on, uncomment `include_recipe` for the desired recipe in `cookbooks/main/recipes/default.rb`
- Do whatever the comments say to do in addition to that
- save the file, commit and push to your forked repo
- apply them to your EY account for your app (environment_name is what you called your app on EY):

```
ey recipes upload -e environment_name
ey recipes apply -e environment_name
```

### Custom Recipes

In case there isn't a recipe that meets your needs in the defaults, you
can write your own. Here's how to create one for HTTP Basic Auth.

- open the forked `ey-cloud-recipes` repo
- run `rake new_cookbook COOKBOOK=httpauth` (this will generate a bunch
  of files under `cookbooks/httpauth/` for you)
- edit `cookbooks/httpauth/recipes/default.rb`:

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

- create a file at "cookbooks/httpauth/templates/default/nginx/htpasswd.users.erb"
```
<% @sysadmins.each do |sa| -%>
  <%= sa["id"] %>:<%= sa["htpasswd"] %>
<% end -%>
```

- include the recipe in `cookbooks/main/recipes/default.rb` with `include_recipe "httpauth"`
- make sure your syntax is okay with `rake test`
- commit and push your recipe to the forked repo
- as above, upload them:

```
ey recipes upload -e environment_name
ey recipes apply -e environment_name
```

Your recipes get saved on your server in the `/etc/chef-custom/` directory.

## SSHing In

If Chef ain't cuttin' the muster, you can SSH in. There are three ways
to do this:

1) with `ssh username@123.123.123.123`. The IP address can be found
2) Click on the SSH link in your application dashboard on EY.
3) Run `ey ssh` from your app root on your dev machine

When you get there, it will output some interesting information about
your app's server environment:

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

Pretty cool. It's nice to have access to get the kind of control you
would have on a typical bare-bones hosting service, but also have the
option to use ready-made packages to configure your server environment
with a minimum of fuss.


## Conclusion

More info: https://support.cloud.engineyard.com/home
