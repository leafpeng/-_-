>Change background image
```
%LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\RoamingState
```
>Under the "icon" line, add the following lines:
```
"backgroundImage" : "ms-appdata:///roaming/yourimage.jpg",
"backgroundImageOpacity" : 0.75,
"backgroundImageStrechMode" : "fill",
```
>If you’re using a GIF file, you may want to change the “fill” to “uniformToFill” instead. 