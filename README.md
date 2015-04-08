Courtesy of Dr. Rolf Freitag: http://www.true-random.com/homepage/projects/wifi/index.html

freewifi_autoconnect.sh: Auto connect to open (and online) WIFI(s), with trying all WLAN devices.
It scanns for free WIFIs and connects to the one with the best quality.
It checks if the WIFI/access point is really online.
If no open and online WIFI could be connected or after a disconnect it
restarts with a new and fast scan for WIFIs, to make the PC nearly always
online.
To avoid problems with duplicate ESSIDs and hidden ESSIDs this script uses
MACs.
The error "Operation not possible due to RF-kill" is usually caused by an deactivated device, e. g. via a WiFi switch.
This script needs the common environment, e. g. NO null globbing.

Because DHCP changes /etc/resolv.conf, you should check that file when you use a network without DHCP afterwards.

DHCP Clients in this script:
The default is to use dhcpcd and if it is not availible dhclient will be used.
dhcpcd runs in background and actively manages re-requests after expired
lease time etc. pp. without needing to be called again while dhclient only
requests an IP (and gateway etc.) and then exits.
dhcpcd does not exit when the lease time is infinite but that should be no big
problem because with an infinite lease time re-requests are not necessary.
So dhcpcd is better and should be installed but this script also works with dhclient.

2012-06:    Added optional parameters: The first for autoconnect (1 for true, 0 for only scanning), which is usefull
            for scanning (WiFi monitoring), the second is the filter for connecting: The ESSID must be equal, which is usefull
            for selecting wifis, the third is the inverse filter, for connecting this ESSID will be ignored, wich is usefull
            for not connecting WiFis, e. g. known honeypots.
The first parameter is NOT optional if other parameter are used!
Example: ./freewifi_autoconnect.sh 0.
This does not connect (only scanning).
This filtering works only at connecting WiFis. If you want to filter the output, you have to filter e. g. via
./freewifi_autoconnect.sh 2>&1 | grep "Tonline"
or
./freewifi_autoconnect.sh 2>&1 | grep -v "Tonline"

2012-09:    Added output for the wifiscan program, for exponential smoothing of the WiFi Quality and Signal Level,
			and sorting due to one of the parameters (Channel, MAC, ESSID, ...).
            The output fomat is CSV: <Channel>,<Encryption>,<Quality>,<Level>,<MAC>,<ms-time>,"<ESSID>",
            with file names of format: <MAC>,<Channel>,<hexdump of "<ESSID>">,<ms-time>
            To ensure that "form follows function" and not to waste time by formatting the script, the script is now
            formatted with the Bash Script Beautifier beautify_bash.py from http://www.arachnoid.com/python/python_programs/beautify_bash.py,
            called with only this script as parameter.
2013-04:    Added the country setting for the region NZ to minimize the difference between the transmission range and
            reception range by setting TxPower to 500 mW. This works with my adapter WL0162 under openSuSE 12.3 and Kubuntu 12.10,
            but the values measures with a WiFi router and RFExplorer do show no change.
            -
            Second changes: Skipping of IPV4LL addresses and no setting of the channel with iwconfig, because the 2013
            version of iwconfig can't do that and also does not set the other parameters, so that DHCP can't work.
            I also removed the wget option --proxy=off because DHCP can set a proxy, wget automatically uses a
            proxy (via the environment variables) and this script starts with unsetting the proxy environment variables.
            So this script can go online even behind an dual-homed bastion host.
            More general scanning of the iwlist output and using only "wlan" devices, because that seems to be the only way
            of finding WiFi devices. This works with the dozen old WiFi adapters and my new one.
            Added showing of the external IP and setting a low MTU, because tests with weak connections worked much better
            with an MTU of 512.
2013-09-09: Shorter timeouts, eleminated the bug of endeless looping after the connection got lost. Works well with free WiFis
            and with lost connections. After about 40 seconds a dead connection gets terminated and the seaarch for a new connection
            starts.
2013-10-08: Added timeout from the coreutils to the dhclient command, because it sometimes hangs. Shorter timeout for dhcpcd.
2013-10-19: Added -A option to dhcpcd, enabled rts handshaking and increase the retry level to 30.
2013-10-20: Set dhclient as default DHCP client program because in my first test configuration after dhcpcd the dig does NOT work!
            In another configuration i could see the problem that only dhcpcd could get an IP from the AP, so sometimes
            dhclient should be used and sometimes dhcpcd should be used.
            Added coreutils timeout to wget because even with --tries 1 wget does 6 tries.
2013-11-08: Several changes because changing the MAC via ifconfig does not work anymore and macchanger is not the right tool
            for most WiFi adapters because at several only a few bits of the MAC can be toggled.
            Added online-help and orthogonalised the filtering of AP names.
2013-12-05: Added mode 4 for radio links, which does connection checking with simple pings to the AP.
2014-05-10: Now it works with ESSIDs with double dots, e. g. re:publica.
2015-02:    Mode 4 (first parameter is 4, checking the connection only by ICMP pinging the AP) tests passed in hotels with a slow
            internet connection, added Mode 5 which is like 4 but with ARP instead of ICMP pinging the AP, because more and more 
            APs do a) not connect to the internet directly and b) do not answer ICMP pings. I found this in a hotel in Santiago 
            (Chile), in an Iberia airplane (IBE6830) and on the international airport Madrid. In the plane and at the airport
            the new mode 5 worked without problems.

TODO: ESSIDs with wildcards or spaces as command line option or in a file.


Dr. Rolf Freitag, 2010 - ...
