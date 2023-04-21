
<img src="https://github.com/Real-Time-MIMO/JetsonHardwareSetup/blob/Test_Setup/imgs/Logo_border_name_cropped.png" width="200" height="100">



# Steps on the new Linux box

1. Downloaded entire JetsonHardwareSetup folder
2. go into DCA1000.../Release and target DCAconfig.json to edit this file in a text editor (make sure to create a testdata folder to have all the stored data go into there)
3. Install the dependencies as described in [installation instructions](./docs/install.md).  
    * Make sure to follow correct OS procedure with ARM architechture.
4. Go to Network Connections and double click on Wired connection for the DCA ethernet (Wired connection 1 for the Jetson). Navigate to IPV4 settings and manually set the IP address to 192.168.33.30 with a Netmask of 255.255.255.0 (this will be automatically changed to 24, don't worry about it). These addresses should match with the DCAconfig.json file. An example is shown below

<img src="https://user-images.githubusercontent.com/12723018/233515674-bec797f6-04a0-4a79-96eb-b0869d814f4b.png" width="400" height="400">





# mmwaveAPI

You run DCA and MMWAVELINK at the same time in two different Terminals. These will be called the DCA Terminals and RADAR Terminals
> Open two different terminals for DCA and RADAR
## DCA Terminal (Command Line Interface that controls DCA1000EVM)
1. Make sure radar board (AWR2243BOOST board) is in SOP4 mode (only SOP0 pin should have a jumper). 
    <p align="center">
        <img src="https://github.com/Real-Time-MIMO/mmwaveAPI/blob/AD_NS_cli_setup/imgs/awr2243_sop4mode.jpg"  width="400" height="300">
    </p>
 > Note how the SOP0 has a jumper connected
 
 > SOP1 and SOP2 both have their jumpers disconnected from the AWR2243BOOST board
2. Edit food.setup.json file (text editor or gedit) to configure paths and ethernet connections to DCA
> Edit fileBasePath to read as follows: 
~~~~ 
"fileBasePath": "/home/fusionsense/"...
~~~~ 
where the ellipses is the desired filepath. The DCA1000ConfigPort should be 4096, and the DCA1000DataPort should be 4098

3. Add the RF library path. This can be done with the following commands in Terminal. Use the absolute path for the location of the libRF_API.so location (our example below)
~~~~~
$ LD_LIBRARY_PATH=/home/fusionsense/Documents/CLI_Setup_AWR2243/DCA1000/SourceCode/Release
$ export LD_LIBRARY_PATH
~~~~~

4. Navigate to .../CLI_Setup_AWR2243/DCA1000/SourceCode/Release. Then run DCA1000EVM_CLI_Control.exe in Terminal to open program and see if a menu pops up to confirm the program is working.
~~~~~
$ sudo ./DCA1000EVM_CLI_Control fpga food.setup.json
~~~~~
The output should be as follows:
> FPGA Configuration command : Success

The following commands are used in sequence to record ADC data (in Terminal)
~~~~
$ ./DCA1000EVM_CLI_Control fpga food.setup.json          
~~~~
> This Connects to the Fpga
~~~~
$ ./DCA1000EVM_CLI_Control record food.setup.json
~~~~
> This Configures record command to fpga (DCA ARM)
~~~~
$ ./DCA1000EVM_CLI_Control.exe start_record food.setup.json
~~~~
> Triggers the reading of packets. *Run this AFTER you run mmwavelink.exe* if it works, a blinking green light will appear on the DCA
~~~~
$ ./DCA1000EVM_CLI_Control query_status food.setup.json
~~~~
> Optional but you can run during the record to see if packets are sent.
~~~~
$ ./DCA1000EVM_CLI_Control stop_record food.setup.json
~~~~
> Stops the recording, needed for the next step. This step will timeout. After this, go the next step to reset the fpga for the next recording phase
~~~~
$ ./DCA1000EVM_CLI_Control reset_fpga food.setup.json
~~~~
> This step is IMPORTANT! This resets the fpga so that another set of frames can be read in later. After this step, the first setup command must be run ($ ./DCA1000EVM_CLI_Control fpga food.setup.json)

## RADAR Terminal (API that sets up radar parameters and data formats)
1. Make sure radar board is in SOP4 mode (only SOP0 pin should have a jumper).
2. Navigate to Documents/CLI_Setup_AWR2243/setup_radar
3. Configure mmwaveconfig.txt as needed to setup chirp and antenna parameters. 
4. To start the chirps from the radar board, enter the following Terminal commands
~~~~
$ mkdir build
$ cd build
$ cmake ..
$ make -j4
~~~~
> This will setup the radar to launch mmWaveLink based on the mmwaveconfig.txt parameters
~~~~
$ sudo ./setup_radar
~~~~
> The line will trigger the frames and allow recording on the DCA

![image](https://user-images.githubusercontent.com/12723018/233453917-526e39f6-4bd7-4a97-9373-050ae800b340.png)
> If successful, this should be the last line

5. Upon seeing the mmWaveLink success, run the start_record command on the DCA Terminal shown below
~~~~
$ DCA1000EVM_CLI_Control start_record food.setup.json
~~~~
7. Refer to mmwavelink_output images for successful output
8. mmwavelink_outputFinal demonstrates when frame is triggered by mmwavelink.

## OVERALL FLOW
To reiterate, the overall flow is as follows:
1. Follow the DCA Terminal setup stopping after the record command (do not run start_record yet)
2. Follow the RADAR Terminal commands stopping after the make -j4 command (do not run $ sudo ./setup_radar until ready)
3. Run $ sudo ./setup_radar to start sending chirps
4. Run $ ./DCA1000EVM_CLI_Control.exe start_record food.setup.json to start recording frames
5. Run whichever data collection and visualization software to use Radar data
6. Follow the rest of the DCA Terminal commands to stop recording and reset the FPGA

## DEPENDENCIES
For ease of use, we will use Conda. Because this is running on a Jetson which has ARM architechture, we will install Archiconda. This is an open source repository found in [Archiconda on Github](https://github.com/Archiconda/build-tools/releases). 


## DEBUGGING
1. Make sure your radar board is on the right SOP (jumper on SOP0 only). If you are getting System disconnected message this is probably the reason.
2. Make sure firewall isnt blocking any of your programs
3. Check ethernet connection in Network and Sharing Center and make sure it matches the connections listed in the .json config file
4. Make sure you have the necessary dependencies (Dependencies section to be added later)
5. If you still get system disconnected message even if your SOP jumpers are correct, close any programs that are hogging the socket connection (close any open python plots or outputs) and run the reset_fpga command

### MMWAVELINK OUTPUT
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/mmwavelink_output1.png)
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/mmwavelink_output2.png)
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/mmwavelink_outputFinal.png)
### DCA1000EVM CLI OUTPUT
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/dcaCLI_output1.png)
