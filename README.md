

# mmwaveAPI

You run CLI and MMWAVELINK at the same time in two different cmd prompts

## DCA1000EVM CLI(Command Line Interface that controls DCA)
1. Make sure radar board is in SOP4 mode (only SOP0 pin should have a jumper).
2. Configure food.setup.json file to configure paths and ethernet connections.
3. Run CLI/DCA1000EVM_CLI_Control.exe in cmd prompt to open program and see if a menu pops up to confirm the program is working.
4. General Execution flow (In Cmd prompt)
    a. DCA1000EVM_CLI_Control.exe fpga food.setup.json          # Connects to the Fpga
    b. DCA1000EVM_CLI_Control.exe record food.setup.json        # Configures record command to fpga (DCA ARM)
    c. DCA1000EVM_CLI_Control.exe start_record food.setup.json  # Triggers the reading of packets. *Run this AFTER you run mmwavelink.exe*
        # A popup window for the record should appear. While the popup window is open, the fpga is reading packets and it closes when record stops.
    d. DCA1000EVM_CLI_Control.exe query_status food.setup.json  # Optional but you can run during the record to see if packets are sent.

## MMWAVELINK (API that sets up radar parameters and data formats)
1. Make sure radar board is in SOP4 mode (only SOP0 pin should have a jumper).
2. Navigate to MMWAVE_LINK/mmwave_dfp_02_02_04_00/ti/example/mmWaveLink_SingleChip_Example/Debug/
3. Configure mmwaveconfig.txt to setup parameters. 
4. Run mmwavelink_example.exe
5. Immediately run "DCA1000EVM_CLI_Control.exe start_record food.setup.json" on the separate cmd prompt
6. Refer to mmwavelink_output images for successful output
7. mmwavelink_outputFinal demonstrates when frame is triggered by mmwavelink.

## DEBUGGING
1. Make sure your radar board is on the right SOP. If you are getting System disconnected message on step 4a of the CLI instructions this is probably the case.
2. Make sure firewall isnt blocking any of your programs
3. Check ethernet connection in Network and Sharing Center
4. Make sure all drivers are there
5. If you still get system disconnected message even if your SOP jumpers are correct, close any programs that are hogging the socket connection (close any open python plots or outputs)

### MMWAVELINK OUTPUT
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/mmwavelink_output1.png)
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/mmwavelink_output2.png)
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/mmwavelink_outputFinal.png)
### DCA1000EVM CLI OUTPUT
![Model](https://github.com/Real-Time-MIMO/mmwaveAPI/blob/main/dcaCLI_output1.png)
