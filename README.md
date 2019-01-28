# Rails app using MySQL with docker-compose

Based on the docker-compose documentation here: https://docs.docker.com/compose/rails/ using MySQL instead of Postsgres.

These files form the basis for setting up the environment to run a Rails app in a docker container connected to a MySQL db in a docker container.

To get started, clone this repo and copy it's files to a new folder and `git init` a new repo if you wish to keep it separate from the starting point in this repo, or simply run the commands with this same folder as your working directory.

With the starting files in place, run `docker-compose run web rails new . --force --no-deps --database=mysql`

If you are running docker on linux (recommended for use with Linux or Mac - I've had issues with this setup on Windows) the files created by the above command are owned by root. Fix this by running `sudo chown -R $USER:$USER .`

Now that the Rails skeleton app has been generated and the Gemfile is updated with rails dependencies, you need to build the image once again. Run `docker-compose build`. You'll also need to rebuild the image if you make changes to the Dockerfile or the Gemfile at any point.

Once you have the `web` image built, you need to connect to the database container. Replace the contents of the rails db config file (`config/database.yml`) with the following:

```
default: &default
  adapter: mysql2
  encoding: utf
  host: db
  username: root
  password: dockerrails
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
  <<: *default
  database: myapp_development


test:
  <<: *default
  database: myapp_test
```

If you're going to use this setup for production, you can add your production database as well, but if so then you probably know more than me and won't be using this repo or ReadMe. I don't recommend using this for production as I've only used it for learning and basic development.

You can now start the app by running: `docker-compose up`

You should see some output from the database.

The final step is to create the database. Run `docker-compose run web rake db:create` in another terminal and wait for the output that says something along the lines of:

```
Starting rails_db_1 ... done
Created database 'myapp_development'
Created database 'myapp_test'
```

That's it! You should be able to see the Rails welcome page on port 3000 of the machine you're running docker on. If it is a VirtualBox or VMWare VM, you can get the IP using `ip a s`. If you don't get an IP from that command, you may need to change your networking settings to make the VM accessible from your host. If you're running Docker for Mac or Docker for Windows, you can go to `localhost:3000` and see the Rails welcome page.
