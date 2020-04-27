![Tasker](./.images/tasker.png) ![](./.images/plus.png) ![Pushover](./.images/pushover.png) ![](./.images/equals.png) ![Youtube](./.images/youtube.png)

# Tasker + Pushover = Youtube Controller

[Tasker][1] is a fantastic Android application that gives you extensive control over your device, including interaction with popular notification platforms like [Pushover][2].  This enables sending messages via Pushover to the device which, in turn, direct Tasker to perform specific functions.

This is a Tasker project than enables Youtube control on your android device via Pushover notifications.  It's a very simple proof of concept, demonstrating:

* How to target a specific device
* A simple but adaptable message structure

Think of Pushover as a device control stream similar to [MQTT][3], and you can start to see the potential power.

## Prerequisites

The following apps need to be set up on your device prior to installing this project:

1. [Tasker][10]
2. [Pushover][11]
3. [Youtube][12]

**NOTE:**

* The process of installing Pushover involves creating an account on [Pushover.net][2], and registring your device.  It also installs the Tasker Pushover Plugin, which is used by this project.

* During the registration process you can give your device a unique name.  **Remember this name, as you'll need it to send notifications (explained below).**

## Installing

As this is a [**Tasker Project**][20] file, with the profiles and tasks bundled up into a single XML file, installation is straightforward (assuming the prerequisites above have been satisfied):

1. [Download the XML file][21] to your device.
2. Launch Tasker
3. Make sure you're not in [**Beginner Mode**][22]
4. Long-press on the `Home` icon (the default project) at the bottom of the screen.
5. Select **Import...**
6. Find the XML file on your device and select it.
7. **DONE**

## Getting Started

This tasker project reacts to Pushover notifications that match a specific pattern:

```sh
device = <device_name> # from pushover.net, optional
title = 'youtube'
body = <control_command>
url = <youtube video URL> # optional
```

where:

* `device` &mdash; If you have multiple Youtube apps active on your Pushover account, you can specify which device to control with this parameter.  Omitting it turns the notification into a broadcast to all devices.
* `title` &mdash; Currently hardcoded as `youtube`.  The title acts as the "topic" filter (i.e. using different titles gives you different topics to control even more devices).
* `body` &mdash; While the `title` identifies the topic/channel, the `body` is the "command".  It differentiates which task script gets fired.
* `url` &mdash; This only applies to the `url` command, and is the URL of the Youtube video to start playing.

## Control Commands


| command | description |
| ------- | ----------- |
| `next` | Wakes up the screen and starts playing the next video in the current playlist. |
| `pause` | Pauses the currently playing video in the Youtube app and blanks the screen. |
| `play` | Wakes up the screen and begins playing the current video in the Youtube app. |
| `prev` | Wakes up the screen ahd starts playing the previous video in the current playlist. |
| `stop` | Stops the currently playing video in the Youtube app and blanks the screen. |
| `url` | Wakes up the screen and starts playing the video (specified by the `url` parameter) in the Youtube app. |
| `volume.down` | Decreases the volume by 1. |
| `volume.up` | Increases the volume by 1. |


## Background

I have an old Nexus 6 phone in the lab, and wanted to put it to use, even trivially.  It occured to me that it would make a great small TV screen (since I tend to listen more than watch videos when I'm working).  And, initially, it was quick to get up and running.

But, after time, it got tiresome having to reach across the desk to tap the screen.  There had to be a better way.  Figuring "It's Android. It can run Tasker.  Tasker can control Youtube.  There's _got_ to be something already existing to handle this!"

Well ... not that I could find.  Seems most people are interested in turning their old phones into remote controls _for_ TVs (and such) or home-brew Chromecast TV sticks.  I couldn't find anything about doing it the other way around (playing video _on_ the phone while controlling it _from_ my workstation).

Time for some creative thinking:

1. Get Tasker to control Youtube (play, pause, next, prev, stop, etc.)
2. Get Tasker to accept input from somewhere else.

Enter [Pushover][2], which comes with a Tasker plugin for just such a connection.

So, the general pattern becomes:

1. In Tasker create a profile off a Pushover Event notificication.
2. Specify a unique `title` and `body` (each profile can respond to a different `title`/`body` combination).
3. Create your tasks (or task scripts) for the profile, you get access to the parts of a Pushover notification through the [Tasker variables the Pushover plugin exposes][30].
4. Save your work (it's easy to overlook the checkmark that appears in the top bar when there are changes to your profiles or tasks.  If you don't save, any changes don't get loaded and executed).
5. Use the [Pushover.net][2] website (or something like [Markus Perl's pushover-cli][40]) to send a notification to your device with the correct `title` and `body` values.

And *Voil&agrave;*!


[1]: https://tasker.joaoapps.com/
[2]: https://pushover.net/
[3]: http://mqtt.org/

[10]: https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm
[11]: https://play.google.com/store/apps/details?id=net.superblock.pushover
[12]: https://play.google.com/store/apps/details?id=com.google.android.youtube

[20]: https://tasker.joaoapps.com/userguide/en/activity_main.html#projects
[21]: https://raw.githubusercontent.com/ScottJWalter/tasker-youtube/master/Youtube.prj.xml
[22]: https://tasker.joaoapps.com/userguide/en/beginner.html

[30]: https://support.pushover.net/i20-using-pushover-message-variables-in-tasker

[40]: https://github.com/markus-perl/pushover-cli
