---
layout: single
title:  "Create and remove network on Docker "
header:
  image: "/assets/images/containers.jpg"
  image_description: "containers"
date:   2022-05-20 11:08:45 +0300
category: Python
classes: wide
tags:  
- docker
---
Now i will show you how to create and remove network on Docker, lets follow the instructions :)

Let’s start!
------------
If you don’t have that installed yet, install [Docker](https://docs.docker.com/engine/install/ubuntu/)

See the list
------------
{% highlight bash %}
$ docker network ls

NETWORK ID     NAME                        DRIVER    SCOPE
cd7ebce86a7a   bridge                      bridge    local
7eb4ec22a7d5   flasknginx_default          bridge    local
c60fcafb09d1   host                        host      local
{% endhighlight %}

* *docker network ls:* Lists all the networks the Engine daemon knows about. This includes the networks that span across multiple hosts in a cluster.


Creating new interface
-----------------------------------
We want to create a Docker new network with name 'demo' by type this command.


{% highlight bash %}
$ docker network create --subnet=172.16.0.0/24 --gateway=172.16.0.1 demo
db96fec85d09eece7c24335e0606da2c4ea44ed5e4eedf9215151d6b43f18008
{% endhighlight %}

* *docker network create:* Creates a new network. The DRIVER accepts bridge or overlay which are the built-in network drivers, you can specify subnet and gateway too

Then you can see the details or inspect
-----------------------------------
{% highlight bash %}
$ docker network inspect demo

[
    {
        "Name": "demo",
        "Id": "db96fec85d09eece7c24335e0606da2c4ea44ed5e4eedf9215151d6b43f18008",
        "Created": "2022-05-20T13:21:03.011718959+09:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.16.0.0/24",
                    "Gateway": "172.16.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]


{% endhighlight %}

* *docker network inspect :* Returns information about one or more networks. By default, this command renders all results in a JSON object.

More information 
-----------------------------------

More information about Docker network can be found in [docker-docs](https://docs.docker.com/engine/reference/commandline/network/)
