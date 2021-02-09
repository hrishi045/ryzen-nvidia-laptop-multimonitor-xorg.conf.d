# ryzen-nvidia-laptop-multimonitor-xorg.conf.d

## What does this do?

It lets you use external displays with certain kinds of Ryzen+Nvidia laptops that have poor linux support. Ideally, you shouldn't buy these if you're planning on running Linux reliably but if you did, this configuration may help you alleviate some of the problems.

Note that this doesn't enable "NVIDIA Optimus" on your machine. If you have no choice but to use these configuration files, your particular setup will not support it anyway. There's just no way to reliably use it. GPU switching only reliably works when you're just using the internal monitor.

You have three different options when you've got a laptop like this:
- Use the integrated GPU and offload to NVIDIA when you need to (kinda like how it works in Windows)
  - This *may* work when you're only using the internal monitor and will probably not work with the external monitor as it needs an NVIDIA driver to work
- Use the integrated GPU and disable NVIDIA GPU completely
  - You will not be able to detect/use the external monitor at all
  - Internal monitor works fine
  - Good battery life
  - You can't make use of the dedicated NVIDIA GPU
- Use the NVIDIA GPU only
  - This should let you use both the Internal and external monitor(s).
  - Poor battery life
  - You can run applications on the dedicated GPU (and only on the dedicated GPU)

The configuration on this repo enables the use of NVIDIA only and trades battery life for external monitor support.

## Who is this for? (IMPORTANT)

- [x] People with a relatively new Ryzen CPU + Nvidia GPU laptop like my ASUS FX505DT with `AMD Ryzen 5 3550H with Radeon Vega Mobile Gfx (8) @ 2.100GHz (and AMD Radeon RX Vega 8)`,  and `NVIDIA GeForce GTX 1650 Mobile / Max-Q`
  - Something with the Integrated GPU connected to the Internal display
  - And the external NVIDIA GPU connected to the external display
- [x] People who don't care about battery life (you'd be lucky to get 
- [x] People who primarily use their external monitor or a dual monitor (your laptop's internal screen + external monitor) setup (both should work)
- [x] People who don't need Wayland (Xorg only. Proprietary NVIDIA drivers don't support Wayland and with this configuration, it's pretty much impossible)
- [x] People who have proprietary NVIDIA drivers installed (nouveau doesn't work)

I tested this on Ubuntu 20.04.2 and Arch Linux but theoretically it should work on all Linux distributions on the 5.8+ kernel and NVIDIA 460+ driver. 

## Prerequisites

- Install proprietary NVIDIA drivers (Nouveau just won't work)
- Remove any **conflicting** xorg.conf.d entries. Don't remove everything in there as you may probably need the other files. Anything setting up drivers can go.  

## Installation

1. Copy [`10-optimus.conf`](./10-optimus.conf) to your `xorg.conf.d` directory:
  - `/etc/X11/xorg.conf.d` on Arch Linux
  - `/usr/share/X11/xorg.conf.d` on Ubuntu

2. Change the BusID in [`10-optimus.conf`](./10-optimus.conf) to your device's BusID.
  - Do `lspci | grep VGA`
  - Highlighted is your BusID: 
  
  **`01:00.0`**`VGA compatible controller: NVIDIA Corporation TU117M [GeForce GTX 1650 Mobile / Max-Q] (rev a1)`
  - Set this in your `10-optimus.conf`:
  
  `BusID "PCI:`**`1:0:0`**`"`

3. If you have issues with the DPI, copy [`20-monitor.conf`](./20-monitor.conf) to the `xorg.conf.d` directory and change the `HDMI-0` to whatever `xrandr --listmonitors` shows you. 

4. Reboot

