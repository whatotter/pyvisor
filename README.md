# PyVisor
a lightweight kvm solution (0.3mb! ~~(54.2mb with mediamtx binaries)~~) that is meant to be used on the go, without requiring a pi or a kvm switch

works on:
- laptops
- linux hosts
- windows hosts
- pis
- literally anything that has windows or linux and two usb 2.0 ports

all you need is just a host, two raspberry pi picos (or any circuitpython MCU with usb_cdc and usb_hid support, and UART), and a cheapo capture card

## DISCLAIMER
while it can be used for kvm-over-ip, it's not **MEANT** to be used for it. this is by no means secure at all, and is all meant to be running on localhost ONLY  
you do NOT want to run this in a critical network unless its for only temporary maintenance

also the code is a shitshow but it works so enjoy

## setup
### bake your pies first

for the sender pi, you can either use a pico or a usb-to-ttl converter @ 57600bps  

here's the pi-ttl-converter tutorial anyways - ignore this if your gonna use a usb-to-ttl converter  
grab one pico and explicitly name it as "sender" (or vnc, or something you can distinguish it by)  
flash circuitpython 8+ on it, and then put `boot.py` and `main.py` from `./circuitpython/sender`

once thats done, grab your other pi and explicitly name that one as "reciever" (or hid)  
flash circuitpython 8+ on it AGAIN, and then put `/adafruit_hid` and `main.py` from `./circuitpython/receiver`

connect these pins:

| sender |    | reciever |  
|--------| -- |----------|
| GND    | -> | GND      |  
| GP8    | -> | GP9      |
| GP9    | -> | GP8      |

and your done :) now you can plug in the vnc/sender pi to the host


### setup the host
1. install required packages
    - ```pip install opencv-python flask flask-socketio vidgear```

2. setup your startup commands in `config.json`, **AND YOUR SERIAL PORT FOR YOUR PI/TTL CONVERTER**

3. and then `sudo python3 main.py` (or just `python3 main.py` for windows users)  

**IF YOU WANT TO USE WEBRTC WHIP (which ~~comes default~~ is now depreciated), VIEW BELOW** 

if you want to use my protcol (thank you), you can switch `"streamBackend"` in the `config.json` to cv2, and turn off cv2-ng  
this requires no dependencies other than cv2 (and gstreamer if you pick that as cv2's backend)  
be aware that this has the same latency as WHIP/cv2-ng, but at a lower framerate (quality good now (wahoo!))

cv2-ng is still cv2, but uses vidgear as a backend, which provides EXTREMELY FAST framerates and super low latency  
i highly recommend using this, and not webrtc

i also highly reccomend using tiles and not something like daemon, due to the reduced bandwidth usage and cpu usage on both the client and server  
and thank you [chafey for your libjpeg-turbowasm](https://github.com/chafey/libjpeg-turbojs) build, due to we reduced cpu usage on the client from ~35% to 10%!!  
now whip is a depreciated option and isn't worth using anymore

## using WebRTC WHIP for streaming
since WHIP is so new, you need obs studio 30.0.0  
take these steps so PyVisor can automatically start streaming using obs  

1. install obs studio v30
2. create a new scene named "KVM"
3. add a video capture device to the sources
4. set it as your capture card, set up fps, etc.
5. go to `settings -> stream -> service`
6. select `WHIP`
7. set server as `http://localhost:8889/kvm/whip`
8. hit `ok`
9. get mediamtx from their repository, put the binary in `./mediamtx`, and edit the config.json
10. profit

# contributing
nobody ever contributes but if you want to go ahead, its the same as always:
- dont complicate the code
- dont make it racist
- go ham
