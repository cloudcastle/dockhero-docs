[DockHero](http://addons.heroku.com/dockhero) hosts your docker stack in the cloud.

Run any image from Docker Hub in our infrastructure in a minute, and attach it to your Heroku app like any other add-on.
Each docker image runs on a separate Amazon Web Services instance with a dedicated IP.
Your docker image logs will appear among your Heroku app’s logs.

> callout
> The add-on is currently in private testing and available to the alpha testers only.
> Please email us at [dockhero@castle.co](mailto:dockhero@castle.co) to get access.

## Provisioning the add-on

DockHero can be attached to a Heroku application via the CLI:

> callout
> A list of all plans available can be found [here](http://addons.heroku.com/dockhero).

```term
$ heroku addons:create dockhero
-----> Adding dockhero to sharp-mountain-4005... done, v18 (free)
```

Once DockHero has been added a `DOCKHERO_HOST` setting will be available in the app configuration and will contain the hostname of the newly provisioned container instance. This can be confirmed using the `heroku config:get` command.

```term
$ heroku config:get DOCKHERO_HOST
12345678-dockhero.node.tutum.io
```


## Preparing a stackfile
We'll be building a pluggable resource which returns MOTD via HTTP.
Here's a [Docker image](https://hub.docker.com/r/dockhero/motd-http/) which does that.

> callout
> You should already have Docker set up locally.
> Please follow the [installation instructions for your platform](https://docs.docker.com/installation/)

Create `docker-compose.yml` file with the following content:

```yml
web:
  image: dockhero/motd-http
  ports:
    - "80:8000"
```

Then run the stack locally

```term
$ docker-compose up
Starting motdhttp_web_1...
Attaching to motdhttp_web_1
```

Now you can test if the stack is up (assuming Dockes is installed via `docker-machine`):

```term
$ curl http://$(docker-machine ip default)/
From listening comes wisdom and from speaking repentance.
```

## Deploying your first stack via Dashboard

The dashboard can be accessed via the CLI:

```term
$ heroku addons:open dockhero
Opening dockhero for sharp-mountain-4005
```

or by visiting the [Heroku Dashboard](https://dashboard.heroku.com/apps) and selecting the application in question. Select DockHero from the Add-ons menu.

To deploy your example Docker stack, copy-paste the content of `docker-compose.yml` created above into the text area and click *Redeploy* button.
Your stack will be up in a minute

> callout
> You can find more example stacks [here](https://github.com/cloudcastle/dockhero/tree/master/examples)

## Using with Ruby


In a Ruby applications, you can fetch the host name of your Docker service from DOCKHERO environment variable.
With the stack above spinned up, you could read the MOTD message using the following Ruby code:

```ruby
#!/usr/bin/env ruby
response = open("http://#{DOCKHERO_HOST}/") { |f| f.read }
puts "DOCKHERO says:\n  #{response}"
```

## Monitoring & Logging

DockHero activity can be identified within the Heroku log-stream by `dockhero` prefix:

```term
$ heroku logs -t | grep 'dockhero'
```

## Troubleshooting

In Alpha version, there's a known issue: the stack doesn't get up sometimes.
The workaround is to remove add-on and add it again:

```term
$ heroku addons:destroy dockhero
$ heroku addons:create dockhero
```


## Migrating between plans

Currently only the Test plan is supported

## Removing the add-on

DockHero can be removed via the CLI.

> warning
> This will destroy all associated data and cannot be undone!

```term
$ heroku addons:destroy dockhero
-----> Removing dockhero from sharp-mountain-4005... done, v20 (free)
```


## Support

All DockHero support and runtime issues should be submitted via one of the [Heroku Support channels](support-channels). For other questions or suggestions, please email us at [dockhero@castle.co](mailto: dockhero@castle.co).

If you prefer GitHub way, please feel free to file an issue [here](https://github.com/cloudcastle/dockhero/issues)

You can improve the current docs or post your own stackfile examples by forking [our repo](https://github.com/cloudcastle/dockhero/) and sending a pull request.