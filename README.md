# Computer System Architecture Project

This project provides a step-by-step guide for installing Ubuntu 23.04 LTS on the Zynq ZC7010 board using Petalinux 2022.4. The following instructions will help you set up your environment and successfully install Ubuntu on your Zynq ZC7010 board.

## Prerequisites
Before starting the installation process, make sure you have the following prerequisites:

1. Zynq ZC7010 board
2. Petalinux 2022.4 software
3. Ubuntu 23.04 LTS installation files
4. SD card (8GB or larger)
5. A host computer with Linux OS (preferably Ubuntu)
6. Xillinx Vivado 2020.1

## Step 1: Set up the Host Machine
1. Install the required dependencies by executing the following command:
```shell
sudo apt-get install tofrodos iproute2 gawk gcc git make net-tools libncurses5-dev tftpd zlib1g-dev libssl-dev flex bison libselinux1 gnupg wget diffstat chrpath socat xterm autoconf libtool tar unzip texinfo zlib1g-dev gcc-multilib build-essential libsdl1.2-dev libglib2.0-dev screen pax gzip
```
2. Download the Petalinux 2022.4 installer from the Xilinx website and follow the installation instructions provided.
3. If some packages are not available they have to me manually installed. Information on the specific packages required by the Petalinux software can be found on https://docs.xilinx.com/r/en-US/ug1144-petalinux-tools-reference-guide/Steps-to-Set-Up-PetaLinux-Working-Environment

## Step 2: Prepare the SD Card
1. Insert the SD card into your host computer.
2. Identify the device name assigned to your SD card (e.g., `/dev/sdX`). Use the `lsblk` command to list all available block devices and identify the SD card.
3. Run the following command to partition and format the SD card:
```shell
sudo fdisk /dev/sdX
```
4. Inside the `fdisk` command-line interface, create a new partition table (`g` command) and then create a new primary partition (`n` command).
5. Set the partition type to FAT32 by using the `t` command and choosing the appropriate code (e.g., `b` for FAT32).
6. Write the changes to the SD card by executing the `w` command and exit `fdisk`.
7. Format the partition with the FAT32 filesystem by running the following command:
```shell
sudo mkfs.vfat /dev/sdX1
```

## Step 3: Create the Hardware Design on Xilinx Vivado
1. Launch Xilinx Vivado on your host machine.
2. Create a new project by selecting "Create Project" from the Vivado welcome screen.
3. Specify a name and location for your project, and choose an appropriate project directory.
4. Select the Zynq ZC7010 board as the target device for your project.
5. Choose "RTL Project" as the project type and click "Next".
6. Add your source files, constraints, and any other necessary design files to the project.
7. Proceed with the default settings for project options and IP integrator.
8. After completing the project creation, click on "Generate Bitstream" to generate the bitstream file for your design.
9. Once the bitstream generation is complete, export the hardware design by selecting "File" -> "Export" -> "Export Hardware."
10. Choose "Include bitstream" and click "OK" to export the hardware design.

## Step 4: Preparing Ubuntu Kernel Image
1. Open a terminal window and navigate to the project directory. Run the following command to create a new Petalinux project:
```shell
petalinux-create -t project -s project-spec/template.yml
```
2. Change the directory to the newly created Petalinux, then open the configuration menu, make the necessary changes to configure the project according to your requirements. Ensure that the Ubuntu 23.04 LTS root filesystem is selected as the rootfs type. Run the following commands:

```
cd petalinux
petalinux-config //to build the project
```

3. After configuring the project, save and exit the configuration menu. This is to install the necessary components and generate the bootable files for the Zynq ZC7010 board. Intially this can take up to 8 hours, but if we retry to rebuild the project it will rebuild within 15 minutes. Start the build process by running the following command:
```
petalinux-build
```

4. Once the build process completes, copy the generated image.ub and BOOT.bin files to the SD card.

## Step 5: Boot Ubuntu on the Zynq ZC7010 Board
1. Insert the SD card into the Zynq ZC7010 board.
2. Connect a USB keyboard and mouse to the ZC7010 board.
3. Power on the board.
4. Using any linux terminal emulator (in our case we used PuTTY) send commnads to the FPGA.
