# Red Hat Image Mode Bootc ISO Workflow

**Goal**: The examples in documentation are always so limited so the goal here is to show an end to end build of a Bootc installation ISO.

## What is Red Hat Image Mode?

Image mode is a new approach to operating system (OS) deployment that lets users build, deploy, and manage Red Hat Enterprise Linux as a bootc container image. It reduces complexity across the enterprise by letting development, operations, and solution providers use the same container-native tools and techniques to manage everything from applications to the underlying OS. 

## Workflow

The following is the step by step process I took to create a Bootc image mode ISO.  The following steps were carried out on a RHEL9.4 host as root though sudo and a regular user could also be used.

The first requirement was getting the container tools installed and so I used the following to get those installed.

~~~bash
# dnf install container-tools
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
  (...)
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

Once the tooling is in place we need to login to `register.redhat.io` with a Red Hat account.

~~~bash
# podman login registry.redhat.io
Username: myusername
Password: 
Login Succeeded!
~~~

Next we need to pull two images for this workflow locally:

 * The latest Red Hat Bootc Image Builder image
 * The latest Red Hat Bootc image

First let's pull the image builder image.

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

Then we can pull the RHEL Bootc image.  Note if there is a need to build a custom Bootc image that can also be done as well and is documented [here](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/using_image_mode_for_rhel_to_build_deploy_and_manage_operating_systems/building-and-testing-the-rhel-bootable-container-images_using-image-mode-for-rhel-to-build-deploy-and-manage-operating-systems#building-and-testing-the-rhel-bootable-container-images_using-image-mode-for-rhel-to-build-deploy-and-manage-operating-systems)

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

Once we have pulled our images let's just review what we have.

~~~bash
# podman images
REPOSITORY                                    TAG         IMAGE ID      CREATED     SIZE
registry.redhat.io/rhel9/rhel-bootc           latest      482f4c67cc15  4 days ago  1.47 GB
registry.redhat.io/rhel9/bootc-image-builder  latest      7e467a06cbc4  4 days ago  521 MB
~~~

Next I created a directory structure under `root` home directory.

~~~bash
# mkdir ~/bootc
# cd bootc
# mkdir output
~~~

I also created a config.toml file which allows us to customize the image.  In this example I embedding my user/password, public ssh-key and the groups I should belong to.

~~~bash
# cat <<EOL > config.toml 
[[blueprint.customizations.user]]
name = "myuser"
password = "password"
key = "ssh-rsa publick-key"
groups = ["wheel"]
EOL
~~~

Before we begin building let's review where we are and what is in the directory structure.

~~~bash
# pwd
/root/bootc
# ls
config.toml  output
~~~

If everything looks good we can proceed to run the build process which consists of using `podman` to run the `bootc-image-builder` while passing in some directories and referencing the starting image we will use to build our ISO.  The entire build process happens within a container and the ISO generated is dumped to the `output` directory we have mapped into the container.   The process will take a bit to run and the log output is very long.  I have provided the complete log run [here](https://github.com/schmaustech/bootc-iso/blob/main/bootc-iso-build-log) with the condensed version below.

~~~bash
# podman run --rm -it --privileged --security-opt label=type:unconfined_t -v /var/lib/containers/storage:/var/lib/containers/storage -v /root/bootc/output:/output -v /root/bootc/config.toml:/config.toml registry.redhat.io/rhel9/bootc-image-builder --type iso --config /config.toml --local registry.redhat.io/rhel9/rhel-bootc:latest
Generating manifest manifest-iso.json
DONE
Building manifest-iso.json
starting -Pipeline source org.osbuild.containers-storage: 51102953cf5005007cf8b3bd76c39a0ae558aa34f311915042cdc0bc4c1fb246
Build
  root: <host>
Pipeline source org.osbuild.curl: 07337b98b3c859adfb37b011d83cf0511884147bf999e7869ffbf9074b529a4f
Build
  root: <host>
(...)
Writing to 'stdio:/run/osbuild/tree/install.iso' completed successfully.

⏱  Duration: 3s
org.osbuild.implantisomd5: bf37713d1bdb752cb80271b8243583e86665b003c69abff0e1730de24398fac1 {
  "filename": "install.iso"
}
['implantisomd5', '/run/osbuild/tree/install.iso']
Inserting md5sum into iso image...
md5 = 11cf490ec817e7904ba35961d5323195
Inserting fragment md5sums into iso image...
fragmd5 = 4f7338c869faf1a2881d46dc4ac1eebf46148d4bf2fa1d59149d74f43195
frags = 20
Setting supported flag to 0

⏱  Duration: 3s
manifest - finished successfully
build:    	9133fb8610ab053dae7e281e6a6655dbb912c4530d32e4da75c06b8713a87c80
anaconda-tree:	fcd61d1236a42900977530f12f45fe452f2f0c8bf3c80a7ba60cb45ffe4bf36d
rootfs-image:	0a517e05ab42f947beec8dae4d2da338ca9cc7fe17b1daba013e24b1c60aeadf
efiboot-tree:	61a20c820b40436ce7bd6d1a74c6b97a05f7c8800b678083942e814cf9f7cc0e
bootiso-tree:	53d4713e1d036ca2d06ae3b0b64c8b9a4efb63cbc6fed4f13afe1e984b7af04d
bootiso:  	bf37713d1bdb752cb80271b8243583e86665b003c69abff0e1730de24398fac1
Build complete!
Results saved in
.
~~~

Once the build process completes we will find an `install.iso` in the `output/bootiso/` directory.

~~~bash
# cd ~/bootc/output/bootiso/
# ls -l
total 2211840
-rw-r--r--. 1 root root 2264924160 Jul 26 10:38 install.iso
~~~

The installation iso that was created can then be used to install RHEL on another host automatically.
