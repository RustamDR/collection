##WIFI Config

First, make sure your wireless card is enabled. You can use rfkill.
~~~
sudo apt install rfkill
~~~

To check the status of wireless card, run
~~~
rfkill list
~~~

As you can see, my wireless card is blocked by software. To unblock it, use the following command:
~~~
rfkill unblock wifi
~~~

If you are using the desktop version of Ubuntu, then you also need to stop Network Manager with the following command, otherwise it will cause connection problem when using wpa_supplicant.
~~~
sudo systemctl stop NetworkManager
~~~

You also need to disable NeworkManager start at boot time by executing the following command.
~~~
sudo systemctl disable NetworkManager
~~~
https://www.linuxbabe.com/command-line/ubuntu-server-16-04-wifi-wpa-supplicant

Run iwconfig to find the name of your wireless interface.
~~~
iwconfig
~~~
wlan0 is a common name, but my wireless interface is called wlp3s0. You can also see that it’s not associated with any access point right now

If your wireless interface isn’t shown, perhaps you need to bring it up with the following command.
~~~
sudo ifconfig wlp3s0 up
~~~

Then find your wireless network name by scanning nearby networks with the command below. Replace wlp3s0 with your own interface name. ESSID means network name.
~~~
sudo iwlist wlp3s0 scan | grep ESSID
~~~

Now install wpa_supplicant on Ubuntu
~~~
sudo apt install wpasupplicant
~~~

We need to create a file named wpa_supplicant.conf using the wpa_passphrase utility. wpa_supplicant.conf is the configuration file describing all networks that the user wants the computer to connect to. Run the following command to create this file. Replace ESSID and Wi-fi passphrase with your own.
~~~
wpa_passphrase your-ESSID your-passphrase | sudo tee /etc/wpa_supplicant.conf
~~~

The output will be piped to tee which then write to /etc/wpa_supplicant.conf file. Now use the following command to connect your wireless card to wireless access point.
~~~
sudo wpa_supplicant -c /etc/wpa_supplicant.conf -i wlp3s0
~~~

By default, wpa_supplicant runs in the foreground. If the connection is completed, then open up another terminal window and run
~~~
iwconfig
~~~

You can see that the wireless interface is now associated with an access point.
You can press CTRL+C to stop the current wpa_supplicant process and run it in the background by adding -B option.
~~~
sudo wpa_supplicant -B -c /etc/wpa_supplicant.conf -i wlp3s0
~~~

Although we’re authenticated and connected to wireless network, but we don’t have an IP address yet. To obtain a private IP address from DHCP server, use the following command:
~~~
sudo dhclient wlp3s0
~~~

Now your wireless interface has a private IP address, which can be shown with:
~~~
ifconfig wlp3s0
~~~

Now you can access the Internet. To release the private IP address, run
~~~
sudo dhclient wlp3s0 -r
~~~

##Hidden WIFI
https://www.linuxbabe.com/command-line/ubuntu-server-16-04-wifi-wpa-supplicant
