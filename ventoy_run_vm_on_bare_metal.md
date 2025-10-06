Run VM on bare metal using Ventoy

https://www.reddit.com/r/linux/comments/189hduh/comment/kbt0mdx/
- PDF

https://www.ventoy.net/en/plugin_vhdboot.html

```
The basic idea is that you create a VM machine in VM software (I use VirtualBox) and you install OS (both Linux and Windows are supported) in the VM into a virtual hard disk file (.vhd). Once you have the OS installed and running in the VM, you copy the .vhd file over to Ventoy (as if it was .iso) and you boot it the same way you would an .iso. This boots the .vhd on bare metal, just like it would the .iso, but the .vhd is writable, which means that you can use the OS as normal. This includes installing any program or driver you want (including 3D graphics drivers), updating the OS and basically doing anything you can with the normally installed OS. The OS is running bare metal, so there aren't any issues with anticheat or proctoring stuff because there is no VM to detect. If your Ventoy device is USB3, yes you can game on this system, it most you will get marginally longer load time, but I, personally, have never noticed this. The only difference is that all the OS files are read and written to and from the .vhd file, so when you shut down the OS and unplug the Ventoy device, there's no trace that anything was ever ran on your PC.

Here's the official documentation: [https://www.ventoy.net/en/plugin_vhdboot.html](https://www.ventoy.net/en/plugin_vhdboot.html)

However, it is a bit cryptic, so I wrote this post a while back about how to get this kind of setup going: [https://www.reddit.com/r/linux_gaming/comments/wxed28/its_been_while_since_eac_and_battleeye_added/ilupj7z/?context=3](https://www.reddit.com/r/linux_gaming/comments/wxed28/its_been_while_since_eac_and_battleeye_added/ilupj7z/?context=3)

Feel free to ask questions, I'll be happy to answer (as long as I've seen evidence that you've done your research/reading first, I won't answer basic questions such as "What is a VM?").
```