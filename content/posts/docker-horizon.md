---
categories:
    - openstack
date: 2020-08-27T17:06:35Z
description: A simple horizon docker demo. FYI.
keywords: OpenStack, horizon, dashboard
lastmod: 2026-08-14T00:00:00Z
tags:
    - docker
    - dashboard
title: Horizon Image Based on Mitaka
---



# Horizon

> Note: OpenStack Mitaka is a 2016-era release and long since EOL — running it today is only sensible for nostalgia/demos. The `purplemystic/mitaka_horizon` image's availability on Docker Hub is unverified. For a current deployment, use the official containerized tooling (Kolla / Kolla-Ansible) or the Horizon images shipped by OpenStack releases. The `service ...` (SysV) start commands inside the container predate systemd-era images; the docker run/exec syntax shown still works.

**All accounts' password is `root`.**

```bash
# create container and expose some ports
docker container run -d --privileged --name ho \
    -p 80:80 -p 5000:5000 -p 35357:35357 \
    --add-host info:127.0.0.1 --add-host controller:127.0.0.1 \
    purplemystic/mitaka_horizon init

# restart rabbitmq and apache2
# mysql and apache2 use domain: controller
# rabbitmq use domain: info(For more information: https://blog.yoooo.fun/rabbitmq-lost-user-info.html)
docker container exec -it ho bash -c "service mysql restart; service rabbitmq-server restart; service memcached restart; service apache2 restart"

docker container exec -it ho bash
# login to ho, and source openrc
source ~/admin-openrc

# access by browser
# configure your /etc/hosts
echo "127.0.0.1 info controller" >> /etc/hosts
# Finally, you can login by browser
http://127.0.0.1/horizon

```
