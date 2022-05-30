# vSphere-and-vCenter-setup
Configuring vSphere and vCenter plus installing your first VM

You'll need to start by downloading VMWare vSphere and vCenter from VMware's wewbsite. Google download vmware vsphere to find the downloads page. Download VMware vSphere Hypervisor (ESXi) 7.0U3d; this is the installer for ESXi, the hypervisor that we need to install first. Also download VMware vCenter Server 7.0U3e,we'll use this later.

Now we need to create bootable hardware for the ISO of ESXi that we just installed. I like to Rufus. Select your USB device from the dropdown if it's not already selected, then under boot section click on select and choose the VMware-VMvisor-installer. Lastly click start. This will take a few minutes, but once complete you'll have a bootable usb for VMware ESXi.

The nextg step is to install it in your hardware. I'm running an older workstation, so I get a hardware warning telling me that my hardware is supported for now, but won't be in the future. If you're running unspported hardware you may driver problems. Finish going through the installation, and while it's rebooting at the end unplug the installation USB. Once rebooted we're going to set the IP to static. Press F2, log in, go to Configure Management Netork, then IPv4 Configuration, and select Set static IPv4 address and network configuration. You can either set your own address, subnet mask, and default gateway from here, or the leave them blank.

I'm going to start by changing the name. I've already set it to VirtualHome, so lets change it to virtualAustin. Start by clicking on Networking.

From here flip to the TCP/IP stacks tab and select Default TCP/IP stack.

We can change our name here under Host name. On this screen we can also set our Domain name, DNS server, and IP gateways. I set up a mock Domain, vsphere.local, and used Cloudware's DNS.

The next thing to do is set up time synchronization. So on the Navigator on the left select the Manage option directly under the Host drop down.

<!---I love you <3 wifey--->

By default we should be on the system tab, so select Time & Date. Go to the Edit NTP Settings page. We want it set to Use Network Time Protocol (enable NTP client). From the drop down select Start and stop from host. Now we need to set our NTP servers. You can use your own, but I'm going to use servers from the [pool.ntp.org project](https://www.ntppool.org/en/). To do so in the NTP servers box type the following in complete with commas.

0.pool.ntp.org, 1.pool.ntp.org, 2.pool.ntp.org, 3.pool.ntp.org  
