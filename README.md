Due to many questions how to setup the cameras and the equipment I decided to write a quick tutorial.

## What is needed?

The hardware setup obviously requires a steel dartbaord. In addition, proper dart recognition depends on homogeneous lighting, the darts must not cast string shadows. A 360 degree LED light ring is recommended, because the cameras can be attached to in order to have a view on the whole board. At least three cameras are required to be acaptue all threee darts thrown. Each camera should have a distortion-free lense. And last but not least, a controller device is needed that the cameras are connected to. Any machine capable of running Linux will do, but in terms of performance a Rasbperry Pi 3B+ class device is the minimum requirement for image processing to run properly. Decent results requires faster devices, for example a NVIDA Jetson Nano. 

The software setup basically requires getting a recent Linux system running. Various guides brought up by an internet search and a some basic Linux knowledge is sufficient here. Once this is running, an Autodarts.io account must be created to obtains a board ID and an API key. More about this will be explained later.

## Camera adjustments

Setting up the cameras used to be a big topic, but due to a very stable codebase the setup has become quite easy. JUst stick to the following rules:

- The cameras must have a good view on the whole board.
- Each camera's view should be between 35 and 55 degrees to the board surface
- The cameras should be distriibutes eually around the board, which means each at 120 degrees relative to each other.
hit much more often than other, so multiple camers should have a good view on the dart there from different angels. One or two dart in the 20 segment should not block the camera's view and thus increase the chance of detection errors. 

The camera model does not matter too much, as long as it can capture a goot image of the board given the lighting that is used. However, a lense with miniomal distortion is recommended. 
As a reference, my setup is using cameras with field of view (FOV) of 65 degrees, they are 30cm away from the wall (where the board hangs). The distance from lense to bullseye is about 36cm. There is no need to copy this setup exactly.

<img src="images/camToBoard.jpg" width="40%" height="40%">

## Linux system setup

Install one of the available Linux distribution according to the instructon for your device. 
- For a Raspberry Pi picking the "Lite" version at the [download page](https://www.raspberrypi.com/software/operating-systems/) is sufficient. A good installation guide is available [here](https://siytek.com/how-to-install-raspbian-to-sd-card-using-etcher/).
- For a normal computer or laptop, any Linux dstribution will do, for example [Ubuntu 20.04 LTS](https://ubuntu.com/download/desktop). Detailed installation instructions can be found [here](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview).

Once the basic Linux system is installed and running, OpenCV must be installed. Follow the tutorial for the [Raspberry Pi](https://lindevs.com/install-precompiled-opencv-on-raspberry-pi/?fbclid=IwAR1sQwRH1FWbewNg4_Aomga-ZBbx3Di25C2mHrVqGTVxwiIKS31R0Pa8q5Y) or a [PC/Notebook with Ubuntu 20.04](https://vitux.com/opencv_ubuntu/)


### Camera setup

Connet the three cameras and check that each one is working correctly. The best way to do this is with a little tool called v4l-utils, install it via (will propably asked for a password):

    sudo apt-get install v4l-utils

When the installation is finished you can use the following command to see all connected devices.

    v4l2-ctl --list-devices

Which shows the following on my device

    bcm2835-codec-decode (platform:bcm2835-codec):
            /dev/video10
            /dev/video11
            /dev/video12
            /dev/video18
            /dev/media1

    bcm2835-isp (platform:bcm2835-isp):
            /dev/video13
            /dev/video14
            /dev/video15
            /dev/video16
            /dev/media0

    Aukey-PC-LM1E Camera: Aukey-PC- (usb-3f980000.usb-1.1.3):
            /dev/video7
            /dev/video8
            /dev/media4

    Aukey-PC-LM1E Camera: Aukey-PC- (usb-3f980000.usb-1.2):
            /dev/video1
            /dev/video3
            /dev/media2

    Aukey-PC-LM1E Camera: Aukey-PC- (usb-3f980000.usb-1.3):
            /dev/video5
            /dev/video6
            /dev/media3

    Cannot open device /dev/video0, exiting.

### Get Autodarts running

Reach out in Discord to be put on the waiting list and please only reach out if you've done the steps described before.
This step is best done in autodarts.io in the [configuration section](#configure-autodarts). 

### Board Manager

The board manager is to setup and adjust your cameras. Once you run your Autodarts program on your Linux system you can access the Board Manager via the ip address of your device and the port 3180. If you're running the program on a local computer like a laptop you need to type the following in your browsers url: http://127.0.0.1:3180 or if you are using a Raspberry Pi or the Jetson Nano than you need to replace 127.0.0.1 with your ip address of the device.

### Configure Autodarts

After you accessed the Board Manager, you can click on Config in the top menu. There you can see the Auth section where you need to fill in the Board ID and the API Key which is needed to connect your Board to Autodarts.io. Also you need to change the USB Device IDs so that the tool will access the correct cameras. Run

    v4l2-ctl --list-devices

Which shows the following on my device

    bcm2835-codec-decode (platform:bcm2835-codec):
            /dev/video10
            /dev/video11
            /dev/video12
            /dev/video18
            /dev/media1

    bcm2835-isp (platform:bcm2835-isp):
            /dev/video13
            /dev/video14
            /dev/video15
            /dev/video16
            /dev/media0

    Aukey-PC-LM1E Camera: Aukey-PC- (usb-3f980000.usb-1.1.3):
            /dev/video7
            /dev/video8
            /dev/media4

    Aukey-PC-LM1E Camera: Aukey-PC- (usb-3f980000.usb-1.2):
            /dev/video1
            /dev/video3
            /dev/media2

    Aukey-PC-LM1E Camera: Aukey-PC- (usb-3f980000.usb-1.3):
            /dev/video5
            /dev/video6
            /dev/media3

    Cannot open device /dev/video0, exiting.
    pi@raspberrypi:~ $

In this example you can see that my cameras (Aukey-PC-LM1E Camera) were detected and are running. What you need to do now is to add the first video numbers into the USB Device Ids. For me it's 7, 1 and 5.

I'm using 1280x720 as resolution. You can put this to a lower resolution when your system is slow but in general it should be 1280x720. All the other settings can leave as they are.

### Calibration

After you did the configuration you can start with the calibration which is the part where it is good to be as much precise as you can because it effects the recogognized segements.

For the calibration you just need to drag the points (3-19, 6-10, 11-14, 20-1) to the upper double wire like you see in the picture.

<img src="images/calibration.JPG" width="40%" height="40%">

When this is done for all three cameras you can click on the blue disk button on the right side. After you clicked the button you will see the segents edges overlayed to your board. If you did it very precise than the edges should match with your wires. If not you propably bought cams with distortion. But even than the recognition is also working very well. Just give it a try :)

This happens when your cameras have too much distortion.

<img src="images/WithMuchDistortion.JPG" width="40%" height="40%">

As soon as you are done, you can start your board. You can do this either via the "Motion, Dart, Config" tab or on Autodarts.io after you're logged in. When you are on Autodarts.io, you can create a game.

Game On!

### Setup autostart for Autodarts

If you are using a device just for the Autodarts recognition, it could propably very usefull to have it started everytime you are starting the device. For that you can add the Autodarts tool the startup routine. For that you need to create a new file with the command:

    sudo nano /etc/systemd/system/autodarts.service

This opens a text editor called nano. You can copy the following text into it but don't forget to change the username to the user you are logged in at the moment. In this example it is pi.

    # board.service

    [Unit]
    Description=Start/Stop Autodarts board service
    Wants=network.target
    After=network.target

    [Service]
    User=pi
    ExecStart=/usr/local/bin/autodarts
    Restart=on-failure
    KillSignal=SIGINT
    RestartSec=5s

    [Install]
    WantedBy=multi-user.target

As soon as you pasted the code above into the file, you can close the file with Ctrl + X. You will be asked if you want to save so you need to press Y followed by an enter.

The next step is to modify some rights. For that you can copy and paste this:

    sudo chmod 644 /etc/systemd/system/autodarts.service

Once done you need to reload the startup deamon with:

    sudo systemctl daemon-reload

And enable your startup file you have created before

    sudo systemctl enable autodarts.service

Last but not least you can restart your system and than your machine is starting with autodarts running

    sudo reboot

## Working setups

### **NullP0ints setup**

Parts of the setup:

- Winmau Blade 5
- Winmau Plasma LED ring
- McDart Catchring Premium
- Raspberry 3B+
- Aukey PC-LM1E Cams (They are working but they're having little distortion which causes a litte offset which leads to a little less correct recognition)

<img src="images/NullP0intCamDistortion1.jpg" width="40%" height="40%">
<img src="images/NullP0intCamDistortion2.jpg" width="40%" height="40%">
<img src="images/NullP0intCamDistortion3.jpg" width="40%" height="40%">
<img src="images/SetupFromFront.jpg" width="40%" height="40%">
<img src="images/CamHolderFromFront.jpg" width="40%" height="40%">
<img src="images/CamHolderBehind.jpg" width="40%" height="40%">

### **Bomber74 setup**

- Winmau Blade 5
- Winmau Plasma LED ring
- OV2710 Cams
- Self printed cam holders

<img src="images/Bomber74_PlasmaHolder.png" width="20%" height="20%">
<img src="images/Bomber74_boardView.png" width="40%" height="40%">

### Cameras which are not working

- https://www.amazon.de/dp/B09GJRKVLK/ref=cm_sw_r_apan_glt_i_dl_APRJ92Y280T4AWR0TSFA?_encoding=UTF8&th=1
- https://www.amazon.de/dp/B089N76BB2/ref=cm_sw_r_cp_api_glt_i_5VQ17G6RTD4KR2GN2HY4?_encoding=UTF8&psc=1
