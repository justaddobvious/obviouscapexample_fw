# obviouscapexample_fw
### obvıous. Capabilities Example Firmware

obvıous. Capabilities provide a means for capability vendors to license proprietary software to 3rd parties for use in their products.  The ecosystem involves a cloud-based platform whereby vendors can deploy capabilities for sale to 3rd parties, 3rd parties can purchase blocks of capabilities licenses, and 3rd party devices may be provisioned at manufacturing time.

The obvıous. Capabilities firmware, dubbed *Serval*, is a portion of the ecosystem to be deployed on each device manufactured by the 3rd party.  During provisioning, a certificate will be written to the device, and a modified Master Boot Record image will provide the capability vendor's software with the means to verify the certificate.

This repository firmware source is a copy of the heart rate peripheral example in the Nordic SDK.  The only change made is to demonstrate how to use a special supervisory call to verify if a capability is enabled.  This change is demonstrated in main/main.c with constant and variable declarations starting at line 117 and code starting at 312.  Additionally, a supervisory call header file in servalsvc/servalsvc.h is included.

## Building
### Prerequisites

* nRF52 DK (PCA10040)

* obvıous. Capabilities Developer Tool OR obvıous. Capabilities Manufacturing Tool

* git

* arm gcc

Download and install arm gcc from [https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads).

Set your environment variable called `GNU_INSTALL_ROOT` to point to your installation bin folder for your arm gcc installation

* Segger JLink software and documentation pack installed and in your path

Segger JLink software and documentation pack is available at [https://www.segger.com/downloads/jlink/](https://www.segger.com/downloads/jlink/)

* nRF Command Line Tools installed and in your path

nRF Command-line tools are available from Nordic at [https://www.nordicsemi.com/Software-and-Tools/Development-Tools/nRF-Command-Line-Tools](https://www.nordicsemi.com/Software-and-Tools/Development-Tools/nRF-Command-Line-Tools)

* ANT and BLE softdevice S332 6.1.1 and a key

ANT softdevices are available at [https://www.thisisant.com/developer/components/nrf52832/](https://www.thisisant.com/developer/components/nrf52832/) .  Unpack the softdevice into the resource/ folder

* ANT+ network key

The ANT+ network key is available at [https://www.thisisant.com/developer/ant-plus/ant-plus-basics/network-keys](https://www.thisisant.com/developer/ant-plus/ant-plus-basics/network-keys)

-

The Makefile contains a couple of lines to conveniently insert the Softdevice key and ANT+ network key.  Please modify and uncomment the following lines and fill in the keys once you receive them:

~~~~
# See note regarding ANT_LICENSE_KEY in /resource/ANT_s332_nrf52832_6.1.1/ANT_s332_nrf52832_6.1.1.API/include/nrf_sdm.h
#CFLAGS += -DANT_LICENSE_KEY=\"1234-5678-1234-5678-abcd-cdef-1234-5678\"
# Fill in the ANT+ network key below.
#CFLAGS += -DANT_PLUS_NETWORK_KEY=\{0x12,0x34,0x56,0x78,0x90,0xAB,0xCD,0xEF\}
~~~~
Make sure you fetch the submodules for the repository.  From the command-line interface in the root of the example folder:

~~~~
git submodule update --init
~~~~

With the nRF52 DK plugged in, from the command-line interface in the root of the example folder:

~~~~
make
~~~~

## Running

~~~~
make flash_softdevice
make flash
~~~~

This will build the firmware, flash the Softdevice (including the default Nordic MBR) and flash the application firmware.  Note that the software won't currently run because it is not provisioned.  Beginning at line 312 in `main/main.c` is the following code:

~~~~
    uint32_t err_code = servalsvc_command(&main_svc_cmd);

    APP_ERROR_CHECK(err_code);
~~~~

Therefore no lights will flash on the board and you won't be able to find the heart rate monitor broadcast.

You may now launch the obvıous. Capabilities Developer/Manufacturer tool and provision the device.  The act of provisioning and resetting the device will cause the function to return successfully upon verifying the capability in question.

