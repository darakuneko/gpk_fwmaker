# GPK FWMaker
Make QMK/Vial Firmware with API     

GUI version
-------
GPK FWBuilder    
https://github.com/darakuneko/gpk_fwbuilder    


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
docker compose build
```

Startup
-------

``` 
cd gpk_fwmaker
docker compose up -d
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
|      Build       |       /build/qmk        |  post  | string(stream) |                kb(required): string<br>km(required): string<br>tag(required): string<br>useRepo(optional):boolean             |
|                  |       /build/vial       |  post  | string(stream) |              kb(required): string<br>km(required): string<br>commit(optional): string<br>useRepo(optional):boolean               |
|                  |       /build/custom     |  post  | string(stream) |              fw(required): string<br>kb(required): string<br>km(required): string<br>commit(optional): string<br>useRepo(optional):boolean               |
|   Repository Keyboards List   |   /list/keyboards      |  post  |     arrary     | fw(required): string |
|   Copy Repository Keyboard File   |   /copy/keyboard     |  post  |     string(stream)     | fw(required): string |
|   GenerateFile   |   /generate/qmk/file    |  post  |     string     | kb(required): string<br>mcu(required): string<br>layout(required): string<br>user(required): string |
|  GenerateUniqID  |    /generate/vial/id    |  get   |     string     |                                                                                                     |
|     TagList      |        /tags/qmk        |  get   |     array      |                                                                                                     |
|     Convert<br>via.json      |    /convert/via/json    |  post  | string(stream) |                        info(required): json file<br>kle(required): json file     
|     Convert<br>KLE json to QMK/Vial       |    /convert/kle/qmk    |  post  | string |                        kle(required): json file<br>params.kb(required): string <br>params.mcu(required): string<br>params.user(required): string<br>params.vid(required): string<br>params.pid(required): string<br>params.option(required): int <br>params.rows(Required if option is 0 or 1): int<br>params.cols(Required if option is 0 or 1): params.int<br> |
| UpdateRepository | /update/repository/qmk  |  get   | string(stream) |                                                                                                     |                                                                                                  |
| UpdateRepository | /update/repository/qmk  |  get   | string(stream) |                                                                                                     |
|                  | /update/repository/custom |  post   | string(stream) | id(required): string<br>url(required): string |
| DeleteRepositoryCustom | /delete/repository/custom |  post   | string(stream) | id(required): string |

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


### Convert (via.json)
-------
QMK info.json and KLE json are used to create via.json.

e.g.    
```
const data = new FormData()
const infoBuffer = fs.readFileSync(infoFilePath)
data.append('info', infoBuffer, {
        filename: infoFileName,
        contentType: 'application/json',
        knownLength: infoBuffer.length
})
const kleBuffer = fs.readFileSync(kleFilePath)
data.append('kle', kleBuffer, {
        filename: kleFileName,
        contentType: 'application/json',
        knownLength: kleBuffer.length
})
axios.post(url("/convert/via/json"), data, {headers: {"Content-Type": "multipart/form-data"}})
```

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

### Convert (KLE json to QMK/Vial)
-------
It is based on Firmware 'scripts'.   
zykrah's Nice project👍      
https://github.com/zykrah/firmware-scripts       

e.g.    
```
const data = new FormData()
const buffer = fs.readFileSync(filepath)
data.append('kle', buffer, {
        filename: filename,
        contentType: 'application/json',
        knownLength: buffer.length
})
data.append('params', JSON.stringify(params))
axios.post(url("/convert/kle/qmk"), data, {headers: {"Content-Type": "multipart/form-data"}})
```

#### Params
kle is KLE json.    
kb is the keyboard name.      
mcu is the development_board/processor name of this link.      
https://github.com/qmk/qmk_firmware/blob/master/data/schemas/keyboard.jsonschema      
user is the username.      
vid is Vendor ID.      
pid is Product ID.      
option:

|          value        |                      |
|:----------------:|:-----------------------:| 
|       0           |           build QMK Firmware           | 
|       1           |           build Vail Firmware        | 
|       2           |           make only via.json | 

rows is matrix pins.      
cols is matrix pins.      
e.g.      
GP0,GP1,GP2,GP3     

#### KLE Guidelines
![switch](https://user-images.githubusercontent.com/5214078/212447224-56b04aa8-387c-4bf9-a8d3-bf383770c18c.png)

- 0: "label" in the info.json and layer 0 
- 1: layer 1
- 2: (VIAL only) If there is a 'u' here, the key is included as a key for the unlock combo （Same as Firmware 'scripts）
- 3: Multilayout index （Same as Firmware 'scripts）
- 4: (VIAL only) If there is an 'e' here, the key is an encoder （Same as Firmware 'scripts）
- 5: Multilayout value （Same as Firmware 'scripts）
- 6: Secondary Multilayout name （Same as Firmware 'scripts）
- 7: Primary Multilayout name/label （Same as Firmware 'scripts）
- 8: layer 2
- 9: Row
- 11: Col

0 can use label.　　　　   
If the label of keycodes matches the 0 value of KLE Json, it is converted to "aliases" or "key" at layer 0. 
In info.json, it is used as is.    
https://keyboards.qmk.fm/v1/constants/keycodes_0.0.3.json     
In 0, 1, and 8, a blank space is KC_NO.    

Please refer to these KLE and make it.    
https://t.ly/bNH0    
https://t.ly/Y3BEW      
https://t.ly/xiJG8     

Main labels   
```
A
B
C
D
E
F
G
H
I
J
K
L
M
N
O
P
Q
R
S
T
U
V
W
X
Y
Z
1
2
3
4
5
6
7
8
9
0
Enter
Esc
Backspace
Tab
Spacebar
-
=
]
[
\\
#
;
'
`
,
.
/
Caps Lock
F1
F2
F3
F4
F5
F6
F7
F8
F9
F10
F11
F12
Print Screen
Scroll Lock
Pause
Insert
Home
Page Up
Delete
End
Page Down
Right
Left
Down
Up
Num Lock
Menu
Mute
Volume Up
Volume Down
Caps Lock
Num Lock
Left Control
Left Shift
Left Alt
Left GUI
Right Control
Right Shift
Right Alt
Right GUI
```

### UpdateRepository
Clone again to the latest state.   

If you get an error like this, please update the repository.    
``` 
error: branch 'x.x.x' not found.
``` 
------- 
☕️☕️☕️ Donation ☕️☕️☕️   
[Amazon](https://www.amazon.co.jp/hz/wishlist/ls/66VQJTRHISQT) or [Ko-fi](https://ko-fi.com/darakuneko)  

