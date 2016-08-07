---
layout: post
title: Setting C.H.I.P. up
date: '2016-08-07T15:18:00.001+02:00'
---

Few weeks ago I finally received my [C.H.I.P.][chip]
and [PocketC.H.I.P.][pockchip]

C.H.I.P. is the famous $9 computer crowdfunded in
[May 2015 via Kickstarter][kickchip] and PowerC.H.I.P. combines a 4.3-inch
screen and a button-style QWERTY keypad with it.

Here they are in all their beauty:

| ![chip]({{ site.url }}/images/chip.jpg) | ![pocketchip]({{ site.url}}/images/pocketchip.jpg) |


## Setting C.H.I.P. up

I am interested to play with both toys remotely, so I did follow
[Setting up CHIP as a headless server with minimal tools][headlesschip] in order
to install and configure ssh.

Here is what I did for C.H.I.P.:

1. connect via USB to my laptop (a Mac) via `screen` and logon usr: `chip`, pwd: `chip` as
   per default settings (to be changed later ;-).
   
   ```shell
   $ screen /dev/tty.usbmodem1411
   ```

1. follow the [WiFi Connection][wifi] instructions in the docs, i.e. execute the
   command
   
   ```shell
   $ sudo nmcli device wifi connect 'homeSSID' password 'mypass' ifname wlan0
   ```

1. update packages and install `ssh`

   ```shell
   $ sudo apt-get update
   $ sudo apt-get install ssh
   ```

1. optional, upgrade packages and cleanup:

    ```shell
    $ sudo apt-get upgrade
    $ sudo apt-get autoremove
    ```

1. fix locale (I use American setup, so in the GUI I did select the item `en_US.UTF-8 UTF-8`):

    ```shell
    $ sudo locale-gen en_US en_US.UTF-8
    $ sudo dpkg-reconfigure locales
    ```

I also renamed the hosts `biochip` (the bare one) and `chippie` (the Pocket
one). Although I found various posts on how to do it I tried w/ `nmtui` and
selected the *Set system hostname* item then rebooted: all worked smoothly.


## ToDo's

* disable `root` account (`ssh` and non) login.
* change `chip` account password
* change `root` account password


[chip]: <https://getchip.com/pages/chip> "C.H.I.P. the $9 computer"
[pockchip]: <https://getchip.com/pages/pocketchip> "PocketC.H.I.P. the super handy fun computer"
[kickchip]: <https://www.kickstarter.com/projects/1598272670/chip-the-worlds-first-9-computer> "C.H.I.P. Kickstarter"
[headlesschip]: <https://bbs.nextthing.co/t/setting-up-chip-as-a-headless-server-with-minimal-tools/1505> "Setting up CHIP as a headless server with minimal tools"
[wifi]: <http://docs.getchip.com/chip.html#wifi-connection> "C.H.I.P. WiFi Connection"
