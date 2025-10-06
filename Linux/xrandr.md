# Custom resolution
https://unix.stackexchange.com/questions/227876/how-to-set-custom-resolution-using-xrandr-when-the-resolution-is-not-available-i
- Author modified script: https://gist.github.com/chirag64/7853413
- OG script: https://gist.github.com/debloper/2793261

my simple script
```sh
#!/bin/sh  
  
full_2100x1400='"2096x1400_60.00"  247.61  2096 2248 2472 2848  1400 1401 1404 1449  -HSync +Vsync'  
mode_2100x1400=${full_2100x1400%% *}  

# execute: gtf <hRes> <vRes> <refreshHz>
# keep everything after "Modeline" exactly as is
xrandr --newmode "${full_2100x1400}"  

# execute: xrandr 
# to get the get the display output name  (not screen)
xrandr --addmode Virtual-1 "${mode_2100x1400}"  
xrandr --output Virtual-1 --mode "${mode_2100x1400}"  

echo "Setting to: ${mode_2100x1400}"
```

https://writeloop.dev/posts/changing-screen-resolution-manually-through-xrandr-and-on-kvm-virt-manager/
