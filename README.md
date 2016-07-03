# Apex
Apex is a TCP reverse proxy server with load balancing capabilities.

Apex is mainly build to act as a load balancer and to route traffic to the desired backend server with the capability to select between multiple balancing algorithms. Through the simplicity of the config file you can set it up really quick and it just works.

A cool feature is that you can add or remove backend server through a very simple RESTful API and that these servers are automatically removed or added live to the load balancer
and are directly accessible and usable.

# Features

- built on top of [netty](https://github.com/netty/netty)
- high performance
- load balancing
- multiple strategies (round robin, random, least connections, fastest)
- dynamic server adding/removing (simple RESTful API)
- health check (ping probe, interval configurable in ms)
- offline/online server management (removing and adding back to the LB)
- configurable threads (recommended value is cpu cores * 2)
- logging (debug logging configurable)
- simple but powerful

# Apex config

Very simple but neat config format based on my project [Cope](https://jackwhite20.github.io/Cope/).

Available balance strategies are:

| Strategy  | Description |
| --------- | ----------- |
| ROUND_ROBIN | Typical round robin algorithm |
| RANDOM | Random based selection |
| LEAST_CON | The one with the least amount of connections |
| FASTEST | The one with the fastest connection time |

```ini
# The first timeout value is read timeout
# and the second one is write timeout
# Both are in seconds
general:
    debug true
    server 0.0.0.0 80
    backlog 100
    threads 4
    timeout 30 30
    balance ROUND_ROBIN
    probe 5000

# How the RESTful API should be accessible
# It is recommended to not bind this to 0.0.0.0
# or to a specific external interface
rest:
    server localhost 6000

# Here are all your backend servers
backend:
    api-01 172.16.0.10 8080
    api-02 172.16.0.11 8080
    api-03 172.16.0.12 8080
    api-04 172.16.0.13 8080
```

# Apex RESTful API

The API consists of two simple GET paths with path variables.

| Path | Example | Description |
| --------- | ----------- | ----------- |
| /apex/add/{name}/{ip}/{port} | /apex/add/web-01/172.16.0.50/80 | Adds the given backend server to the load balancer |
| /apex/remove/{name} | /apex/remove/web-01 | Removes the given backend server from the load balancer |

_Responses:_

| Path | Success | Error |
| --------- | ----------- | ----------- |
| /apex/add/{name}/{ip}/{port} | ```{"status":"OK","message":"Successfully added server"}``` | ```{"status":"SERVER_ALREADY_ADDED","message":"Server was already added"}``` |
| /apex/remove/{name} | ```{"status":"OK","message":"Successfully removed server"}``` | ```{"status":"SERVER_NOT_FOUND","message":"Server not found"}``` |

### License

Licensed under the GNU General Public License, Version 3.0.