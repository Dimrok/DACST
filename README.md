# DAVST, a Docker Application Visual Scripting Tool

Insert screenshot here.

## Abstract

At the current stage of Docker, "compose files" are used to descriped a stack (multiple containers working together to provide an application). Compose file are YAML-based files, presenting the different services used by the application.

However, based on my experience (as a beginner with compose file), I feel like there are rooms for improvement, regarding multiple aspects:

- Services (images) interface is not explicit.
- Relationship between services are not always explicit.
- Compose files tend to grow big and get confusing.
- There are no application libraries.

I'll try to address through this project. I'll use the following images to build my demo app.

- `mongo:3.7.3-jessie`

## Frictions points, detailed

Are detailed below the multiple frictions points listed.

### Services (images) interface is not explicit.

When you use a service to describe an interface, you need to consult the image configuration, in particular the `.Config` field. I'll use MongoDB as an example:

`docker inspect mongo:3.7.3-jessie | jq ".[0].Config"`

```
{
  ...
  "ExposedPorts": {
    "27017/tcp": {}
  },
  ...
  "Volumes": {
    "/data/configdb": {},
    "/data/db": {}
  },
  ...
  "Labels": null
}
```

One of the goal of this project is to offer an explicit and visual representation of this configuration, e.g.

```
╔═══════════════════════╗
║ image: mongo          ║>
╠═══════════════════════╣
║ version: 3.7.3-jessie ║
║ name: db-*            ║
║-----------------------║
║ ports:                ║
║                 27017 ║>
║-----------------------║
║ volumes:              ║
║        /data/configdb ║>
║              /data/db ║>
║-----------------------║
║ health checks:        ║
║          /_ping:27017 ║>
╚═══════════════════════╝
```

_Reference:_
- [Docker build](https://docs.docker.com/engine/reference/builder)
- [mongo image](https://hub.docker.com/_/mongo/)

### Relationship between services are not always explicit

The compose-file format tried to address the dependency between services, resulting on different approaches between the different versions (1, 2 and 3 at present time).

> depends_on does not wait for db and redis to be “ready” before starting web - only until they have been started. If you need to wait for a service to be ready, see Controlling startup order for more on this problem and strategies for solving it.
> Version 3 no longer supports the condition form of depends_on.
> The depends_on option is ignored when deploying a stack in swarm mode with a version 3 Compose file.

This project will try to provide built-in `blocks` to represent dependencies between services, e.g.

```
 ╔═════════════════════╗
>║ Dependency: healthy ║>
 ╚═════════════════════╝
```

_Reference:_

- [Compose file documentation](https://docs.docker.com/compose/compose-file)
- [depends_on](https://docs.docker.com/compose/compose-file/#depends_on)
- [Control startup order in Compose](https://docs.docker.com/compose/startup-order)


### Compose files tend to grow big and get confusing

When you have many services, a textual representation, like the current YAML-based configuration file can grow big, especially if you have one compose file dedicated to development and one dedicated to production.

The visual scripting can provide a simple, understandable by everyone with strong and recognizable visual documents way to represent an application.


```
╔══════════╗                                 ╔═══════════╗
║ Variable ║                                 ║ Something ║
╠══════════╣     ╔════════════════╗          ╠═══════════╣
║    debug ║>--->║ Conditinal: If ║      --->║    ...    ║
╚══════════╝     ╠════════════════╣     -    ╚═══════════╝
                 ║ type: boolean  ║    -
                 ║----------------║   -
                 ║            true║>--
                 ║           false║>--
                 ╚════════════════╝   -      ╔═══════════╗
                                       -     ║ Something ║
                                        -    ╠═══════════╣
                                         --->║    ...    ║
                                             ╚═══════════╝
```

_Reference:_

### There are no application libraries

It's one of the biggest weakness of Docker, at present time, is the lack of ability to share application definitions. A visual scripting tool won't solve this problem but can make extending / forking an existing application easier.

_Reference:_

## Technology

Even if I'm not confortable with it, I chose to use JavaScript, for many reasons:

- The `baguette` project (where this project could fit) is developed in JavaScript.
- JavaScript is good at displaying and interracting.
- The existing libraries like [JointJS](https://jointjs.com) or [Draw2D](http://www.draw2d.org/draw2d) (see [JS drawing libraries diagrams](https://modeling-languages.com/javascript-drawing-libraries-diagrams)).
