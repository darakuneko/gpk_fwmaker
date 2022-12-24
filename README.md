# GPK FWMaker
Make QMK Firmware with API     
May correspond to Vial....    

Before use
-------
Install Docker Desktop    
link: https://www.docker.com

Usage
-------
#### Download

https://github.com/darakuneko/gpk_fwmaker/archive/refs/heads/main.zip

### Image Build
``` 
cd gpk_fwmaker
docker-compose build
```

###  Startup
``` 
cd gpk_fwmaker
docker-compose up -d
```

### Copy Keyboard File
After initialized, "GPKFW" directory will be created.

e.g.   
windows: C:\Users\xxxx\GPKFW   
mac: /Users/xxxx/GPKFW   
ubuntu: /home/xxxx/GPKFW    

Copy the keyboard files to "GPKFW" directory.   
Firmware is also created here.   
![dir](https://user-images.githubusercontent.com/5214078/207864849-c199464f-9b03-4028-b148-0688e9e5603e.jpg)

### Build Firmware
GPK FWMaker's API is used to build a firmware.    
   
e.g.   
``` 
windows
curl -X POST -H "Content-Type: application/json" -d '{\"kb\": \"reviung/reviung41\", \"km\": \"default", \"tag\": \"0.19.3\"}' 127.0.0.1:3123/build/qmk  | jq -r '.stderr, .stdout'

other
curl -X POST -H "Content-Type: application/json" -d '{"kb": "reviung/reviung41", "km": "default", "tag": "0.19.3"}' 127.0.0.1:3123/build/qmk | jq -r '.stderr, .stdout'
```

Recommend: It is easier to see when formatted using the jq command.   

### API
|          |        url         |  method |                   response                    |  parameter  |
|:--------:|:------------------:| :---: |:---------------------------------------------:| :---: |
|  Build   |     /build/qmk     |  post  | json<br>{ stderr: string,<br>stdout: string } | kb(required): string<br>km(required): string<br>tag(required): string |
| Generate | /generate/qmk/file |  post  | json<br>{ stderr: string,<br>stdout: string } | kb(required): string<br>mcu(required): string<br>layout(required): string<br>user(required): string |
| TagList  |     /tags/qmk      |  get  |                     array                     | |
|  Update  |    /update/qmk     |  get  |                    string                     | |

Port: 3123

#### Build
Build firmware.
The built firmware is created in the GPKFW directory.   
kb is the keyboard name.  
km is the keymap.  
tag can be taken from the TagList api or check the qmk's tag page(https://github.com/qmk/qmk_firmware/tags). 

e.g.    
For command  
make reviung/reviung41:default  
   
Parameter  
kb: reviung/reviung41  
km: default 

#### Generate
Keyboard files.
The files is created in the GPKFW directory.   
kb is the keyboard name.  
mcu is the development_board/processor name of this link.   
https://github.com/qmk/qmk_firmware/blob/master/data/schemas/keyboard.jsonschema    
layout is the directory name of this link.    
https://github.com/qmk/qmk_firmware/tree/master/layouts/default    
user is the username.

#### TagList
Tag list of cloned repositories.    
    
#### Update
Clone again to the latest state. 
     
------- 
☕️☕️☕️ Donation ☕️☕️☕️   
[Amazon](https://www.amazon.co.jp/hz/wishlist/ls/66VQJTRHISQT) or [Ko-fi](https://ko-fi.com/darakuneko)  

