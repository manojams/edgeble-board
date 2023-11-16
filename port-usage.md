##  1. Audio

    ## 1.1  List the playback devices

        *  Plug in the headset and use the following command to view the sound card information :- 

        root@neu6b-io:/# aplay -l
        **** List of PLAYBACK Hardware Devices ****
        card 0: rockchipes8316 [rockchip-es8316], device 0: dailink-multicodecs es8316.7-0011-0
        [dailink-multicodecs es8316.7-0011-0]
        Subdevices: 1/1
        Subdevice #0: subdevice #0
        card 2: rockchiphdmi1 [rockchip-hdmi1], device 0: rockchip-hdmi1 i2s-hifi-0 [rockchip-hdmi1
        i2s-hifi-0]
        Subdevices: 1/1
        Subdevice #0: subdevice #0

    ## 1.2  Play the Audio

        *  Audio Plays sounds through card 0 (The name of the Audio card is rockchipes8316)

        root@neu6b-io:/# aplay -D hw:0,0 01.wav
        Playing WAVE '01.wav' : Signed 16 bit Little Endian, Rate 44100 Hz, Stereo

##  2. Mic

*  Plug the headset into the mic port

        root@neu6b-io:/# arecord -d 30 -f S16_LE -r 44100 -c 2 mic.wav

*  Insert the headphones into the Audio port and use the following command to play the recorded audio

        root@neu6b-io:/# aplay -D hw:0,0 mic.wav

##  3. CAN

* Test as loopback mode

* On host minicom :-

        root@neu6b-io:/# dmesg | grep can0
         22.378540] rockchip_canfd fea50000.can can0: rockchip_canfd_get_berr_counter
        RX_ERR_CNT=0x00000000, TX_ERR_CNT=0x00000000
        [ 22.548005] rockchip_canfd fea50000.can can0: rockchip_canfd_get_berr_counter
        RX_ERR_CNT=0x00000000, TX_ERR_CNT=0x00000000
        [ 22.548306] rockchip_canfd fea50000.can can0: rockchip_canfd_get_berr_counter
        RX_ERR_CNT=0x00000000, TX_ERR_CNT=0x00000000
        [ 24.423541] rockchip_canfd fea50000.can can0: rockchip_canfd_get_berr_counter
        RX_ERR_CNT=0x00000000, TX_ERR_CNT=0x00000000

* Set can bitrate :-

        root@neu6b-io:/# ip link set can0 type can bitrate 250000 loopback on

* Bringing CAN interface up  :-

        root@neu6b-io:/# ip link set up can0

* On target open a terminal for receiving :-

        rock@neueb-io:-$ candump can0

![can-1](https://github.com/manojams/docs-edg-test-setup/assets/97679353/c86685b5-18ca-4062-a948-dfc402f0ee3a)


* On host minicom:

        root@neu6b-io:/# ip -details -statistics link show can0
        3: can0: <NOARP,UP,LOWER_UP,ECHO> mtu 16 qdisc pfifo_fast state UP mode DEFAULT
        group default qlen 10
        link/can promiscuity 0 minmtu 0 maxmtu 0
        can <LOOPBACK> state ERROR-ACTIVE (berr-counter tx 0 rx 0) restart-ms 1
        bitrate 250000 sample-point 0.871
        tq 30 prop-seg 57 phase-seg1 57 phase-seg2 17 sjw 1
        rockchip_canfd: tseg1 1..128 tseg2 1..128 sjw 1..128 brp 1..256 brp-inc 2
        clock 198000000
        re-started bus-errors arbit-lost error-warn error-pass bus-off
        0 0 0 0 0 0 numtxqueues 1 numrxqueues 1
        gso_max_size 65536 gso_max_segs 65535
        RX: bytes packets errors dropped missed mcast
        0 0 0 0 0 0
        TX: bytes packets errors dropped carrier collsns
        0 0 0 0 0 0

* Send data :-

        root@neu6b-io:/# cansend can0 001#1122334455667788

* On Target :-
![can-2](https://github.com/manojams/docs-edg-test-setup/assets/97679353/e84e04cd-6567-418f-b688-3093fed6c8cd)



##  4. Ethernet

*  get from debug or adb by ifconfig

        root@neu6b-io:~# ifconfig -a
        can0: flags=128<NOARP>  mtu 16
                unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 10  (UNSPEC)
                RX packets 0  bytes 0 (0.0 B)
                RX errors 0  dropped 0  overruns 0  frame 0
                TX packets 0  bytes 0 (0.0 B)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
                device interrupt 86  

        enP2p33s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
                inet 192.168.0.144  netmask 255.255.255.0  broadcast 192.168.0.255
                inet6 fe80::66a7:af63:b82:a5ff  prefixlen 64  scopeid 0x20<link>
                ether 3a:e5:6a:eb:b6:bf  txqueuelen 1000  (Ethernet)
                RX packets 25  bytes 2968 (2.8 KiB)
                RX errors 0  dropped 1  overruns 0  frame 0
                TX packets 17  bytes 1790 (1.7 KiB)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
                device interrupt 163  

        lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
                inet 127.0.0.1  netmask 255.0.0.0
                inet6 ::1  prefixlen 128  scopeid 0x10<host>
                loop  txqueuelen 1000  (Local Loopback)
                RX packets 0  bytes 0 (0.0 B)
                RX errors 0  dropped 0  overruns 0  frame 0
                TX packets 0  bytes 0 (0.0 B)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

        wwan0: flags=4098<BROADCAST,MULTICAST>  mtu 1500
                ether aa:45:ee:f6:1d:32  txqueuelen 1000  (Ethernet)
                RX packets 0  bytes 0 (0.0 B)
                RX errors 0  dropped 0  overruns 0  frame 0
                TX packets 0  bytes 0 (0.0 B)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

*  PING Test

        root@neu6b-io:~# ping -I enP2p33s0 google.com
        PING google.com (216.58.200.142) from 192.168.0.144 enP2p33s0: 56(84) bytes of data.
        64 bytes from maa05s10-in-f14.1e100.net (216.58.200.142): icmp_seq=1 ttl=118 time=15.3 ms
        64 bytes from maa05s10-in-f14.1e100.net (216.58.200.142): icmp_seq=2 ttl=118 time=15.3 ms
        64 bytes from maa05s10-in-f14.1e100.net (216.58.200.142): icmp_seq=3 ttl=118 time=15.3 ms
        --- google.com ping statistics ---
        13 packets transmitted, 13 received, 0% packet loss, time 12022ms
        rtt min/avg/max/mdev = 15.240/15.288/15.342/0.027 ms


##  5. FAN

*  List pwm and its functionalities

        root@neu6b-io:/# ls /sys/devices/platform/fd8b0020.pwm/pwm/pwmchip2/
        device  export  npwm  power  pwm0  subsystem  uevent  unexport

*  set period

        root@neu6b-io:/# echo 10000 > /sys/devices/platform/fd8b0020.pwm/pwm/pwmchip2/pwm0/period 

*  set duty_cycle 

        root@neu6b-io:/# echo 5000 > /sys/devices/platform/fd8b0020.pwm/pwm/pwmchip2/pwm0/duty_cycle 

*  set polarity

        root@neu6b-io:/# echo normal > /sys/devices/platform/fd8b0020.pwm/pwm/pwmchip2/pwm0/polarity       

*  Run Fan
 
        root@neu6b-io:/# echo 1 > /sys/devices/platform/fd8b0020.pwm/pwm/pwmchip2/pwm0/enable

*  stop Fan

        root@neu6b-io:/# echo 0 > /sys/devices/platform/fd8b0020.pwm/pwm/pwmchip2/pwm0/enable


##  6. RTC 

*  get from debug by dmesg

        root@neu6b-io:~# dmesg | grep rtc
        [    7.147782] rockchip-vop2 fdd90000.vop: [drm:vop2_crtc_atomic_enable] Update mode to 3840x2160p60, type: 11(if:1000) for vp0 dclk: 594000000
        [    7.147933] rockchip-vop2 fdd90000.vop: [drm:vop2_crtc_atomic_enable] dclk_out0 div: 0 dclk_core0 div: 1
        [    7.148230] rockchip-vop2 fdd90000.vop: [drm:vop2_crtc_atomic_enable] set dclk_vop0 to 297000000, get 297000000
        [    7.638234] rtc-hym8563 6-0051: rtc information is valid
        [    7.643576] rtc-hym8563 6-0051: registered as rtc0
        [    7.644591] rtc-hym8563 6-0051: setting system clock to 2021-01-19T17:45:30 UTC (1611078330

*  get clock information by hwclock

        root@neu6b-io:~# hwclock -r
        2021-01-19 18:03:04.976986+00:00
        root@neu6b-io:~# date
        Thu Dec 22 12:13:07 UTC 2022
        root@neu6b-io:~# hwclock -w
        root@neu6b-io:~# hwclock -r
        2022-12-22 12:13:38.599542+00:00
        root@neu6b-io:~# date
        Thu Dec 22 12:15:58 UTC 2022

*  set Time zone 

        root@neu6b-io:~# timedatectl set-timezone Asia/Kolkata
        root@neu6b-io:~# timedatectl 
                Local time: Thu 2022-12-22 17:53:09 IST
                Universal time: Thu 2022-12-22 12:23:09 UTC
                        RTC time: Thu 2022-12-22 12:23:09
                        Time zone: Asia/Kolkata (IST, +0530)
        System clock synchronized: no
                NTP service: n/a
                RTC in local TZ: no

        root@neu6b-io:~# timedatectl set-ntp true
        Failed to set ntp: NTP not supported
        root@neu6b-io:~# systemctl status systemd-timesyncd
        Unit systemd-timesyncd.service could not be found.


        root@neu6b-io:~# date -s '2023-10-03 20:27:34'

        root@neu6b-io:~# timedatectl 
                Local time: Tue 2023-10-03 20:27:56 IST
                Universal time: Tue 2023-10-03 14:57:56 UTC
                        RTC time: Thu 2022-12-22 12:30:09
                        Time zone: Asia/Kolkata (IST, +0530)
        System clock synchronized: no
                NTP service: n/a
                RTC in local TZ: no


        root@neu6b-io:~# hwclock -r                                                                                          
        2022-12-22 18:00:41.917175+05:30                                                                                     
        root@neu6b-io:~# hwclock --systohc                                                                                   
        root@neu6b-io:~# hwclock -r
        2023-10-03 20:28:44.513664+05:30

*  Board powered off via power button, battery removed, after 10 mins inserted battery and powered on via power button

        root@neu6b-io:/# hwclock -r
        2023-10-03 20:29:47.349676+05:30
        root@neu6b-io:/# date
        Tue Oct  3 20:29:50 IST 2023

##  7. RS232

*  List serial Ports

        root@neu6b-io:/# dmesg | grep ttyS
        [ 6.906675] feb90000.serial: ttyS6 at MMIO 0xfeb90000 (irq = 90, base_baud = 1500000) is a
        16550A
        [ 6.907000] feba0000.serial: ttyS7 at MMIO 0xfeba0000 (irq = 91, base_baud = 1500000) is a
        16550A

*  Short TX and RX Pins as shown in Picture 
![rs232-1](https://github.com/manojams/docs-edg-test-setup/assets/97679353/e88f1938-e7d3-460f-917d-4f556bbe9898)



*  On target open Minicom 

        root@rock-5b:~# sudo minicom -D /dev/ttyS6
![rs232-2](https://github.com/manojams/docs-edg-test-setup/assets/97679353/5bdddb26-cc04-4535-8cc1-abdfacd5aac0)


*  Turn off ECHO, press E 

*  Now, whatever we type on keyboard gets printed on minicom as shown.
![rs232-3](https://github.com/manojams/docs-edg-test-setup/assets/97679353/06fc9f4a-9e16-4196-ae1d-5db95956a992)

##  8. HDMI RX Test 

* get the list of hdmi devices :-

        $root@fakemachine:~# v4l2-ctl --list-devices
        rk_hdmirx (fdee0000.hdmirx-controller):
                /dev/video20

        rkisp-statistics (platform: rkisp):
                /dev/video18
                /dev/video19

        rkcif-mipi-lvds1 (platform:rkcif):
                /dev/media0

        rkisp_mainpath (platform:rkisp1-vir0):
                /dev/video11
                /dev/video12
                /dev/video13
                /dev/video14
                /dev/video15
                /dev/video16
                /dev/video17
                /dev/media1


- After the HDMI RX device is connected , it will be registered as a video device in the kernel, and 
the generated node is /dev/video20. 

* Check the device information

        root@fakemachine:~# v4l2-ctl -d /dev/video20 -D
        Driver Info:
        Driver name      : rk_hdmirx
        Card type        : rk_hdmirx
        Bus info         : fdee0000.hdmirx-controller
        Driver version   : 5.10.110
        Capabilities     : 0x84201000
                Video Capture Multiplanar
                Streaming
                Extended Pix Format
                Device Capabilities
        Device Caps      : 0x04201000
                Video Capture Multiplanar
                Streaming
                Extended Pix Format

* Confirmation of resolution and image format

        root@fakemachine:~# v4l2-ctl -d /dev/video20 --get-fmt-video
        Format Video Capture Multiplanar:
        Width/Height      : 1920/1080
        Pixel Format      : 'NV24' (Y/CbCr 4:4:4)
        Field             : None
        Number of planes  : 1
        Flags             : premultiplied-alpha, 0x000000fe
        Colorspace        : Unknown (0x96000047)
        Transfer Function : Default
        YCbCr/HSV Encoding: Unknown (0x000000ff)
        Quantization      : Default
        Plane 0           :
           Bytes per Line : 1920
           Size Image     : 6220800


* Get the digital video timings

        root@fakemachine:~# v4l2-ctl -d /dev/video20 --get-dv-timings
        DV timings:
        Active width: 1920
        Active height: 1080
        Total width: 2200
        Total height: 1125
        Frame format: progressive
        Polarities: -vsync -hsync
        Pixelclock: 148500000 Hz (60.00 frames per second)
        Horizontal frontporch: 84
        Horizontal sync: 48
        Horizontal backporch: 148
        Vertical frontporch: 4
        Vertical sync: 5
        Vertical backporch: 36
        Standards: 
        Flags: 


* Capture image files by setting resolution and pixel formats        

        root@fakemachine:~# v4l2-ctl --verbose -d /dev/video20 --set-fmt-video=width=1920,height=1080,pixelformat='NV24' --stream-mmap=4 --stream-skip=3 --stream-count=5 --stream-to=/home/openaia/sampleYuvfile.yuv --stream-poll

sampleYuvfile.yuv is will be present in /home/openaia/ in Target Board.

* Using the ffplay on Linux host :- 

        ffplay -f rawvideo -video_size 1920x1080 /media/manoj/rootfs/home/openaia/sampleYuvfile.yuv



