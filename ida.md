
# ida free on fresh ubuntu 22
xub 22
```
cd ~/idafree8.4
ldd *.so* | grep "not found"
sudo apt install libxcb-xinerama0 libxcb-xkb-dev
sudo apt install libxcb-icccm4
sudo apt install libxcb-image0
sudo apt install libxcb-keysyms1
sudo apt install libxcb-render-util0
sudo apt install libxkbcommon-x11-0
```