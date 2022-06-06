# Hacking the Arcadyan LH1000

To acquire a Telnet connection on port 23, reset the router and take out any sim cards. Plug in a LAN cable to access the GUI.

Then from a router that is conected to the internet, plug in a LAN cable from it to the WAN on the LH1000. That should get it on the internet. (Note: if the upstream router's LAN domain is the same as that of the LH1000, then change the LAN domain on the LH1000. i.e. if the upstream domain is 192.168.0.0, then change the LH1000 domain to 192.168.1.0)

Go back to the basic screen in the Broadband tab and change user name and password to:

    test.com;CMD=$'\x20-l\x20/bin/sh';telnetd$CMD;exit;

and save. (Note this will only save in the basic screen.)

Then telnet via port 23 and you should have a root shell straight away.

The following items may not survive a reboot, especially in later versions of the firmware, as /etc is just a symbolic link to /tmp/etc, which gets flushed every reboot.

There is no dropbear service under /etc/init.d, hence it does not start up and therefore there's no ssh. You can usually start dropbear just by running

    /usr/sbin/dropbear

In later firmware versions the dropbear_rsa_host_key & dropbear_dss_host_key are missing from /etc/dropbear, which prevents dropbear from starting.

Create the requisite keys by running

    dropbearkey -t rsa -f /etc/dropbear/dropbear_rsa_host_key
    dropbearkey -t dss -f /etc/dropbear/dropbear_dss_host_key

Then start dropbear ust by running

    /usr/sbin/dropbear

Remember to set the root password by running

    echo -e "root\nroot" | passwd

Note: Earlier firmware versions don't have the chpasswd function defined in Busybox.

# Making these changes permanant

To make these dropbear changes permanent, download and extract the archive directly to the device with the following command:

        curl -skL  https://raw.githubusercontent.com/UncleSam1966/LH1000/master/runonce.tar.gz | tar -xzvf - | sh

Then run it manually with `./runonce`.
