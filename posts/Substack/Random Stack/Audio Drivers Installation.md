---
title: Audio Drivers Installion
slug: audio-drivers-installation
date: 2017-11-17 11:06 UTC+05:30
status: published
tags: [system-maintainance, installation]
category: posts/substack/stack
---

> Repairing (installation) Audio Drivers of Debian PC

<a title="Futase_tdkr, Public domain, via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:Realtek_ALC882_HDAudio_codec.jpg"><img width="512" alt="Realtek ALC882 HDAudio codec" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Realtek_ALC882_HDAudio_codec.jpg/512px-Realtek_ALC882_HDAudio_codec.jpg"></a>

It has been that there is a requirement of 4.11 kernel version for Realtek-ALC1220 to support. So if you too built a your new PC like me then it might be helpful to you for temporary replacement. Uninstall your alsa-base and reinstall latest version it will work fine.

```sh
sudo apt-get remove alsa-base
```

Now go to http://www.stchman.com/tools/alsa/alsa_setup.sh and download the script and then do

```sh
sudo ./alsa_setup.sh
```

This will reinstall the lastest driver in your system and reboots it.
 Precautions if you are having Hybrid Graphics(Intel + Nvidia) then it is suggested to run on Intel HD Graphics and then install. Because this driver is supported for Intel HDA versions if you try to install when you are running in NVIDIA this might cause several failures.


---
## Subscribe!
If you find the content here helpful/interesting & want to read more, then _**subscribe**_ to [Random Stack](https://randomstack8.substack.com/) to **never miss an update.**
<div class="row">
	<iframe src="https://randomstack8.substack.com/embed" max-width="480" height="120" frameborder="0" scrolling="no" class="centred"></iframe>
	<br>
</div>