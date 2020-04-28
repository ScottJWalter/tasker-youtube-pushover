![Tasker](./.images/tasker.png) ![](./.images/plus.png) ![Pushover](./.images/pushover.png) ![](./.images/equals.png) ![Youtube](./.images/youtube.png)

# Tasker + Pushover = Youtube Controller

[Tasker][1] is a fantastic Android application that gives you extensive control over your device, including interaction with popular notification platforms like [Pushover][2].  This enables sending messages via Pushover to the device which, in turn, direct Tasker to perform specific functions.

This is a Tasker project than enables Youtube control on your android device via Pushover notifications.  It's a very simple proof of concept, demonstrating:

* How to target a specific device
* A simple but adaptable message structure

Think of Pushover as a device control stream similar to [MQTT][3], and you can start to see the potential power.

## Prerequisites

The following apps need to be set up on your device prior to installing this project:

1. [Tasker][10], including the following Tasker plugins:
   2. [AutoInput][15]
3. [Pushover][11]
4. [Youtube][12]

**NOTE:**

* The process of installing Pushover involves creating an account on [Pushover.net][2], and registring your device.  It also installs the Tasker Pushover Plugin, which is used by this project.

* During the registration process you can give your device a unique name.  **Remember this name, as you'll need it to send notifications (explained below).**

## Installing

As this is a [**Tasker Project**][20] file, with the profiles and tasks bundled up into a single XML file, installation is straightforward (assuming the prerequisites above have been satisfied), and can be performed in 2 ways:

### Method 1:  Via TaskerNet (easiest)

1. Click [this link][100] from your android device's browser.  It will open the TaskerNet page for this project.
2. Follow the instructions on the page to complete the import.

### Method 2:  Via Github (this repo)

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

**Control commands** are passed in the message body of the notification:

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


## Project Details

### Profiles

There are 8 profiles, one for each message type:

* `Youtube Pause`
* `Youtube Play`
* `Youtube Next`
* `Youtube Prev`
* `Youtube Stop`
* `Youtube URL`
* `Youtube Volume Up`
* `Youtube Volume Down`

### Tasks

There are 9 task scripts, broken down into 6 main scripts for individual commands:

* `youtube-pause`
* `youtube-play`
* `youtube-next`
* `youtube-prev`
* `youtube-stop`
* `youtube-url`

One script for handling volume up _and_ down:

* `youtube-volume`

And two "sub-scripts" called by the others:

* `youtube-init` &mdash; Initializes some variables, including the Project's version number.
* `youtube-show` &mdash; Turns on the device's display *(**NOTE:**  This is the touchiest part of the project.  I'm still chasing down erratic behavior when trying to turn the display back on.)*.

### Scenes

One scene is used (as part of the device wake-up process):

* `youtube-wake-screen`

Both the background and text are set to transparent.

### Variables

| name | description |
| ---- | ----------- |
| `%tVol` | Temporary variable used by `youtube-volume` to set media volume. |
| `%VERSION` | Project version number. |

## Notes & Troubleshooting

* This is probably common knowledge, but it wasn't to me until it happened.  In mucking around with the notification settings on my device, I learned that if you "silence" the Pushover app (i.e. turn off vibration, sound, etc. so the notifications get logged to the app but not displayed), this system will _not_ work.  It needs the pop-up part of the notification system to trigger Tasker.  The Pushover/Tasker integration makes sure the pop up notifications don't appear.
* While I tried to use [AutoInput][15]'s ability to activate the display, it only reliably turned it off for me.  After much googling, I've incorporated a hack that also throws a transparent scene Popup up first to "wake" the device before trying to turn the screen on.  The `youtube-show` task list handles creating a Popup based on the `youtube-wake-screen` scene.

## Background

I have an old Nexus 6 phone in the lab, and wanted to put it to use, even trivially.  It occured to me that it would make a great small TV screen (since I tend to listen more than watch videos when I'm working).  And, initially, it was quick to get up and get running, and I was back to work while [The Red Green Show][50] played in the background (don't judge).  All was right with the world.

But, after time, it got tiresome having to reach across the desk to tap the screen.  There had to be a better way.  Figuring "It's Android. It can run Tasker.  Tasker can control Youtube.  There's _got_ to be something already existing to handle this!", I consulted the Google ... and couldn't find much.  Seems most people are interested in turning their old phones into devices that controlled TVs or cast video _to_ them.  I couldn't find anything about doing it the other way around (playing video _on_ the phone while controlling it _from_ my workstation).

Time for some creative thinking:

1. Get Tasker to control Youtube (play, pause, next, prev, stop, etc.)
2. Get Tasker to accept input from somewhere else.

Enter [Pushover][2], which comes with a Tasker plugin for just such a connection.

So, the general pattern is:

1. **Create a profile in Tasker based on a Pushover Event notificication.**  Remember to specify a unique `title` and `body` (each profile can respond to a different `title`/`body` combination).
1. **Create your Tasker tasks (or task scripts) for the profile.**  You get access to the parts of a Pushover notification through the [Tasker variables the Pushover plugin exposes][30].
1. **Save your work.**  It's easy to overlook the checkmark that appears in the top bar when there are changes to your profiles or tasks.  If you don't save, any changes don't get loaded and executed.
1. **Push a notification.**  Use the [Pushover.net][2] website (or something like [Markus Perl's pushover-cli][40]) to send a notification to your device with the correct `title` and `body` values.

*Voil&agrave;*!


[1]: https://tasker.joaoapps.com/
[2]: https://pushover.net/
[3]: http://mqtt.org/

[10]: https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm
[11]: https://play.google.com/store/apps/details?id=net.superblock.pushover
[12]: https://play.google.com/store/apps/details?id=com.google.android.youtube

[15]: https://play.google.com/store/apps/details?id=com.joaomgcd.autoinput

[20]: https://tasker.joaoapps.com/userguide/en/activity_main.html#projects
[21]: https://raw.githubusercontent.com/ScottJWalter/tasker-youtube/master/Youtube.prj.xml
[22]: https://tasker.joaoapps.com/userguide/en/beginner.html

[30]: https://support.pushover.net/i20-using-pushover-message-variables-in-tasker

[40]: https://github.com/markus-perl/pushover-cli

[50]: https://www.youtube.com/watch?v=FUXPuYZ4DEQ&list=PLB90DDA45E8C2EF45

[100]: https://taskernet.com/shares/?user=AS35m8lCND4aKgub4jqkiizlsYO7piFwEGbhUrPJjAPSZaWK%2FXh4FrTyT9hN40yI7hG%2F&id=Project%3AYoutube