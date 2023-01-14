# GPK FWMaker
Make QMK/Vial Firmware with API     

Before use
-------
Install Docker Desktop    
link: https://www.docker.com

Usage
-------
### Download

https://github.com/darakuneko/gpk_fwmaker/archive/refs/heads/main.zip

Image Build
-------
``` 
cd gpk_fwmaker
docker-compose build
```

Startup
-------

``` 
cd gpk_fwmaker
docker-compose up -d
```

Copy Keyboard File
-------
After initialized, "GPKFW" directory will be created.

e.g.   
windows: C:\Users\xxxx\GPKFW   
mac: /Users/xxxx/GPKFW   
ubuntu: /home/xxxx/GPKFW    

Copy the keyboard files to "GPKFW" directory.   
Firmware is also created here.   
<img src="https://user-images.githubusercontent.com/5214078/207864849-c199464f-9b03-4028-b148-0688e9e5603e.jpg" width="50%" />

Build Firmware
-------
GPK FWMaker's API is used to build a firmware.    
   
e.g.   
``` 
windows
curl -X POST -H "Content-Type: application/json" -d '{\"kb\": \"reviung/reviung41\", \"km\": \"default", \"tag\": \"0.19.3\"}' 127.0.0.1:3123/build/qmk

other
curl -X POST -H "Content-Type: application/json" -d '{"kb": "reviung/reviung41", "km": "default", "tag": "0.19.3"}' 127.0.0.1:3123/build/qmk
```

API
-------

|                  |           url           | method |    response    |                                              parameter                                              |
|:----------------:|:-----------------------:|:------:|:--------------:|:---------------------------------------------------------------------------------------------------:|
|      Build       |       /build/qmk        |  post  | string(stream) |                kb(required): string<br>km(required): string<br>tag(required): string                |
|                  |       /build/vial       |  post  | string(stream) |              kb(required): string<br>km(required): string<br>commit(optional): string               |
|   GenerateFile   |   /generate/qmk/file    |  post  |     string     | kb(required): string<br>mcu(required): string<br>layout(required): string<br>user(required): string |
|  GenerateUniqID  |    /generate/vial/id    |  get   |     string     |                                                                                                     |
|     TagList      |        /tags/qmk        |  get   |     array      |                                                                                                     |
|     Convert      |    /convert/via/json    |  post  | string(stream) |                        info(required): json file<br>kle(required): json file                        |
| UpdateRepository | /update/repository/qmk  |  get   | string(stream) |                                                                                                     |
|                  | /update/repository/vial |  get   | string(stream) |                                                                                                     |

Port: 3123

### Build
Build firmware.
The built firmware is created in the GPKFW directory.   
kb is the keyboard name.  
km is the keymap.  

- QMK   
  tag can be taken from the TagList api or check the qmk's tag page   
  https://github.com/qmk/qmk_firmware/tags
- Vial  
  If commit is specified, it will build on any commit of the vial branch.    

e.g.    
For command  
make reviung/reviung41:default  
   
Parameter  
kb: reviung/reviung41  
km: default 

__For more information about the build__  
QMK  
https://docs.qmk.fm   
Vial   
https://get.vial.today/docs/porting-to-via.html

### GenerateFile
Keyboard files.    
The files is created in the GPKFW directory.   
kb is the keyboard name.  
mcu is the development_board/processor name of this link.   
https://github.com/qmk/qmk_firmware/blob/master/data/schemas/keyboard.jsonschema    
layout is the directory name of this link.    
https://github.com/qmk/qmk_firmware/tree/master/layouts/default    
user is the username.

### GenerateUniqID
Unique ID for use with Vial   

### TagList
Tag list of cloned repositories.    


### Convert 
-------
QMK info.json and KLE json are used to create via.json.

info.json - required fields
```
{
  "keyboard_name": "Your Keyboard's Name",
  "usb": {
    "vid": "0xFEED",
    "pid": "0x0001",
  },
  "matrix_size": {
    "cols": 1,
    "rows": 1
  }
}
 ```
 
KLE json edited like VIA  
https://www.caniusevia.com/docs/layouts 

### UpdateRepository
Clone again to the latest state.   

If you get an error like this, please update the repository.    
``` 
error: branch 'x.x.x' not found.
``` 
------- 
☕️☕️☕️ Donation ☕️☕️☕️   
[Amazon](https://www.amazon.co.jp/hz/wishlist/ls/66VQJTRHISQT) or [Ko-fi](https://ko-fi.com/darakuneko)  

