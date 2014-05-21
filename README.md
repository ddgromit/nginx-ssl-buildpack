Heroku Buildpack for Nginx + SSL
============================

This buildpack gives your app access to the `nginx` command compiled with SSL support.


Features
------

* Downloads and compiles Nginx 1.5.7 with PCRE
* SSL support via `--with-http_ssl_module`
* Adds `nginx` to your app's PATH
* Additional files (mime.types, example configs, etc) are available at `$HOME/nginx` if you need them.
* Your app must generate `nginx.conf` and run `nginx` itself (explained below) 

Usage
------

You can check out a complete "hello world" example app at [nginx-ssl-buildpack-example](https://github.com/ddgromit/nginx-ssl-buildpack-example).

### Creating a new app with this buildpack

```ruby
# Create your app if you haven't already
heroku create

# Set the buildpack URL
heroku config:set BUILDPACK_URL=https://github.com/ddgromit/nginx-ssl-buildpack
```

After adding the buildpack, `nginx` will be available on your path.  It is the responsibility of your app and it's `Procfile` to call nginx with a `nginx.conf` that you've generated. 

 **You must generate nginx.conf yourself** because what port you're using depends on the `PORT` environment variable, which is different on every app.


### Procfile example

Here we use ERB to generate a nginx.conf at dyno start time.

```
web: erb nginx.conf.erb > nginx.conf && nginx -c $HOME/nginx.conf
```

### nginx.conf.erb example

The nginx.conf.erb can access environment variables like the port it needs to use.

```Nginx
worker_processes <%= ENV['NGINX_WORKERS'] || 4 %>;
daemon off;

events {
  worker_connections 768;
}

http {
  server {
    listen <%= ENV["PORT"] %>;
    server_name _;

    location / {
      return 200;
    }
  }
}
```



Customization
-----

There's a few reasons you might fork this buildpack.

- add custom modules during compilation
- keep your own version of this repo for security and permanence
- make improvements

I'd encourage you to update the README to explain how your buildpack is special, and consider creating a PR back to this repository.


There are a few ways to use your own version of this buildpack in your app.

```
# Create a new Heroku app that uses your buildpack
heroku create --buildpack <your-github-url>

# Configure an existing Heroku app to use your buildpack
heroku config:set BUILDPACK_URL=<your-github-url>

# You can also use a git branch!
heroku config:set BUILDPACK_URL=<your-github-url>#your-branch
```


Credits
-----

Parts of this codebase were taken from [ryandotsmith's nginx-buildpack](https://github.com/ryandotsmith/nginx-buildpack)
