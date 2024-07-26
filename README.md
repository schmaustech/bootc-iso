# Red Hat Image Mode Bootc ISO Workflow

**Goal**: The examples in documentation are always so limited so the goal here is to show an end to end build of a Bootc installation ISO.

## What is Red Hat Image Mode?

Image mode is a new approach to operating system (OS) deployment that lets users build, deploy, and manage Red Hat Enterprise Linux as a bootc container image. It reduces complexity across the enterprise by letting development, operations, and solution providers use the same container-native tools and techniques to manage everything from applications to the underlying OS. 

## Workflow

~~~bash
# yum install container-tools
Updating Subscription Management repositories.
Last metadata expiration check: 2:20:17 ago on Fri 26 Jul 2024 07:17:27 AM CDT.
Dependencies resolved.
==============================================================================================================================================================================================================================================
 Package                                                Architecture                                  Version                                                   Repository                                                               Size
==============================================================================================================================================================================================================================================
Installing:
 container-tools                                        noarch                                        1-14.el9                                                  rhel-9-for-x86_64-appstream-rpms                                        8.3 k
Upgrading:
 aardvark-dns                                           x86_64                                        2:1.10.0-3.el9_4                                          rhel-9-for-x86_64-appstream-rpms                                        969 k
 buildah                                                x86_64                                        2:1.33.7-3.el9_4                                          rhel-9-for-x86_64-appstream-rpms                                        9.4 M
 netavark                                               x86_64                                        2:1.10.3-1.el9                                            rhel-9-for-x86_64-appstream-rpms                                        4.0 M
 podman                                                 x86_64                                        4:4.9.4-6.el9_4                                           rhel-9-for-x86_64-appstream-rpms                                         16 M
Installing dependencies:
 cockpit-podman                                         noarch                                        84.1-1.el9                                                rhel-9-for-x86_64-appstream-rpms                                        683 k
 podman-docker                                          noarch                                        4:4.9.4-6.el9_4                                           rhel-9-for-x86_64-appstream-rpms                                        106 k
 podman-remote                                          x86_64                                        4:4.9.4-6.el9_4                                           rhel-9-for-x86_64-appstream-rpms                                         10 M
 python3-podman                                         noarch                                        3:4.9.0-1.el9                                             rhel-9-for-x86_64-appstream-rpms                                        178 k
 python3-pyxdg                                          noarch                                        0.27-3.el9                                                rhel-9-for-x86_64-appstream-rpms                                        108 k
 python3-tomli                                          noarch                                        2.0.1-5.el9                                               rhel-9-for-x86_64-appstream-rpms                                         37 k
 skopeo                                                 x86_64                                        2:1.14.3-0.1.el9                                          rhel-9-for-x86_64-appstream-rpms                                        8.5 M
 toolbox                                                x86_64                                        0.0.99.5-2.el9                                            rhel-9-for-x86_64-appstream-rpms                                        2.5 M
 udica                                                  noarch                                        0.2.8-1.el9                                               rhel-9-for-x86_64-appstream-rpms                                         54 k

Transaction Summary
==============================================================================================================================================================================================================================================
Install  10 Packages
Upgrade   4 Packages

Total download size: 52 M
Is this ok [y/N]: y
Downloading Packages:
(1/14): container-tools-1-14.el9.noarch.rpm                                                                                                                                                                    29 kB/s | 8.3 kB     00:00    
(2/14): python3-tomli-2.0.1-5.el9.noarch.rpm                                                                                                                                                                  126 kB/s |  37 kB     00:00    
(3/14): python3-pyxdg-0.27-3.el9.noarch.rpm                                                                                                                                                                   347 kB/s | 108 kB     00:00    
(4/14): cockpit-podman-84.1-1.el9.noarch.rpm                                                                                                                                                                  3.0 MB/s | 683 kB     00:00    
(5/14): udica-0.2.8-1.el9.noarch.rpm                                                                                                                                                                          616 kB/s |  54 kB     00:00    
(6/14): python3-podman-4.9.0-1.el9.noarch.rpm                                                                                                                                                                 1.3 MB/s | 178 kB     00:00    
(7/14): podman-docker-4.9.4-6.el9_4.noarch.rpm                                                                                                                                                                1.2 MB/s | 106 kB     00:00    
(8/14): toolbox-0.0.99.5-2.el9.x86_64.rpm                                                                                                                                                                     4.6 MB/s | 2.5 MB     00:00    
(9/14): netavark-1.10.3-1.el9.x86_64.rpm                                                                                                                                                                      4.4 MB/s | 4.0 MB     00:00    
(10/14): aardvark-dns-1.10.0-3.el9_4.x86_64.rpm                                                                                                                                                               3.3 MB/s | 969 kB     00:00    
(11/14): skopeo-1.14.3-0.1.el9.x86_64.rpm                                                                                                                                                                     3.9 MB/s | 8.5 MB     00:02    
(12/14): podman-remote-4.9.4-6.el9_4.x86_64.rpm                                                                                                                                                               3.2 MB/s |  10 MB     00:03    
(13/14): buildah-1.33.7-3.el9_4.x86_64.rpm                                                                                                                                                                    3.5 MB/s | 9.4 MB     00:02    
(14/14): podman-4.9.4-6.el9_4.x86_64.rpm                                                                                                                                                                      5.3 MB/s |  16 MB     00:02    
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                                                                                         9.6 MB/s |  52 MB     00:05     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                                                                                                                      1/1 
  Upgrading        : aardvark-dns-2:1.10.0-3.el9_4.x86_64                                                                                                                                                                                1/18 
  Upgrading        : netavark-2:1.10.3-1.el9.x86_64                                                                                                                                                                                      2/18 
  Upgrading        : podman-4:4.9.4-6.el9_4.x86_64                                                                                                                                                                                       3/18 
  Installing       : skopeo-2:1.14.3-0.1.el9.x86_64                                                                                                                                                                                      4/18 
  Installing       : toolbox-0.0.99.5-2.el9.x86_64                                                                                                                                                                                       5/18 
  Installing       : cockpit-podman-84.1-1.el9.noarch                                                                                                                                                                                    6/18 
  Installing       : podman-docker-4:4.9.4-6.el9_4.noarch                                                                                                                                                                                7/18 
  Upgrading        : buildah-2:1.33.7-3.el9_4.x86_64                                                                                                                                                                                     8/18 
  Installing       : podman-remote-4:4.9.4-6.el9_4.x86_64                                                                                                                                                                                9/18 
  Installing       : udica-0.2.8-1.el9.noarch                                                                                                                                                                                           10/18 
  Installing       : python3-tomli-2.0.1-5.el9.noarch                                                                                                                                                                                   11/18 
  Installing       : python3-pyxdg-0.27-3.el9.noarch                                                                                                                                                                                    12/18 
  Installing       : python3-podman-3:4.9.0-1.el9.noarch                                                                                                                                                                                13/18 
  Installing       : container-tools-1-14.el9.noarch                                                                                                                                                                                    14/18 
  Running scriptlet: podman-2:4.6.1-8.el9_3.x86_64                                                                                                                                                                                      15/18 
  Cleanup          : podman-2:4.6.1-8.el9_3.x86_64                                                                                                                                                                                      15/18 
  Cleanup          : buildah-1:1.31.4-1.el9_3.x86_64                                                                                                                                                                                    16/18 
  Cleanup          : netavark-2:1.7.0-2.el9_3.x86_64                                                                                                                                                                                    17/18 
  Cleanup          : aardvark-dns-2:1.7.0-1.el9.x86_64                                                                                                                                                                                  18/18 
  Running scriptlet: aardvark-dns-2:1.7.0-1.el9.x86_64                                                                                                                                                                                  18/18 
  Verifying        : python3-pyxdg-0.27-3.el9.noarch                                                                                                                                                                                     1/18 
  Verifying        : python3-tomli-2.0.1-5.el9.noarch                                                                                                                                                                                    2/18 
  Verifying        : container-tools-1-14.el9.noarch                                                                                                                                                                                     3/18 
  Verifying        : cockpit-podman-84.1-1.el9.noarch                                                                                                                                                                                    4/18 
  Verifying        : skopeo-2:1.14.3-0.1.el9.x86_64                                                                                                                                                                                      5/18 
  Verifying        : toolbox-0.0.99.5-2.el9.x86_64                                                                                                                                                                                       6/18 
  Verifying        : udica-0.2.8-1.el9.noarch                                                                                                                                                                                            7/18 
  Verifying        : python3-podman-3:4.9.0-1.el9.noarch                                                                                                                                                                                 8/18 
  Verifying        : podman-docker-4:4.9.4-6.el9_4.noarch                                                                                                                                                                                9/18 
  Verifying        : podman-remote-4:4.9.4-6.el9_4.x86_64                                                                                                                                                                               10/18 
  Verifying        : netavark-2:1.10.3-1.el9.x86_64                                                                                                                                                                                     11/18 
  Verifying        : netavark-2:1.7.0-2.el9_3.x86_64                                                                                                                                                                                    12/18 
  Verifying        : aardvark-dns-2:1.10.0-3.el9_4.x86_64                                                                                                                                                                               13/18 
  Verifying        : aardvark-dns-2:1.7.0-1.el9.x86_64                                                                                                                                                                                  14/18 
  Verifying        : buildah-2:1.33.7-3.el9_4.x86_64                                                                                                                                                                                    15/18 
  Verifying        : buildah-1:1.31.4-1.el9_3.x86_64                                                                                                                                                                                    16/18 
  Verifying        : podman-4:4.9.4-6.el9_4.x86_64                                                                                                                                                                                      17/18 
  Verifying        : podman-2:4.6.1-8.el9_3.x86_64                                                                                                                                                                                      18/18 
Installed products updated.

Upgraded:
  aardvark-dns-2:1.10.0-3.el9_4.x86_64                           buildah-2:1.33.7-3.el9_4.x86_64                           netavark-2:1.10.3-1.el9.x86_64                           podman-4:4.9.4-6.el9_4.x86_64                          
Installed:
  cockpit-podman-84.1-1.el9.noarch      container-tools-1-14.el9.noarch      podman-docker-4:4.9.4-6.el9_4.noarch      podman-remote-4:4.9.4-6.el9_4.x86_64      python3-podman-3:4.9.0-1.el9.noarch      python3-pyxdg-0.27-3.el9.noarch     
  python3-tomli-2.0.1-5.el9.noarch      skopeo-2:1.14.3-0.1.el9.x86_64       toolbox-0.0.99.5-2.el9.x86_64             udica-0.2.8-1.el9.noarch                 

Complete!
~~~

~~~bash
# podman login registry.redhat.io
Username: myusername
Password: 
Login Succeeded!
~~~

~~~bash
# sudo podman pull registry.redhat.io/rhel9/bootc-image-builder
Trying to pull registry.redhat.io/rhel9/bootc-image-builder:latest...
Getting image source signatures
Checking if image destination supports signatures
Copying blob edab65b863ae done   | 
Copying blob ce39a10ee5db done   | 
Copying blob 6e743249fd30 done   | 
Copying config 7e467a06cb done   | 
Writing manifest to image destination
Storing signatures
7e467a06cbc49d0e601ab5acad54afcf16c7fd3187296c74f2780ec3e758977a
~~~

~~~bash
# podman pull registry.redhat.io/rhel9/rhel-bootc:latest
Trying to pull registry.redhat.io/rhel9/rhel-bootc:latest...
Getting image source signatures
Checking if image destination supports signatures
Copying blob b696b6658912 done   | 
Copying blob b696b6658912 done   | 
Copying blob e0b929cd893f done   | 
(...)
Copying blob 2942d3f50802 done   | 
Copying blob 8ea0992b56d4 done   | 
Copying blob ad312c5c40cc done   | 
Copying blob bd9ddc54bea9 done   | 
Copying config 482f4c67cc done   | 
Writing manifest to image destination
Storing signatures
482f4c67cc158fa4b8db27c09832d3133bc45b1d989aa6d166ca2ef45f6c7178
~~~
