---
title: 'Using Cyberghost VPN on ArchLinux'
date: 2015-03-15 18:41:13 +0000
tags: archlinux vpn openvpn
---
Last week I started using a VPN, You can read in this [lifehacker article on why you could use one][lifehacker].

I started using it for two reasons:

* Ciphered and tunneled communications
* Geolocated video

For me It's more a matter of security and freedom too, the other reasons are secondary.

I use Cyberghost, but you can see [in this extense article which one to choose][anonymous] if you're interested.

I chose Cyberghost because they are in Europe (Romania), because they don't keep any logs and I like [what they stand for][stand]. They have some free proxies if you want to test the service first, but they will probably go much slower.

**Note**: You can just use the Network Manager in GNOME/KDE and others, use this tutorial if you want to configure it via the command line.

To get the VPN working on Arch, [you install OpenVPN][openvpn].

<pre><code class="language-bash"># pacman -S openvpn
</code></pre>

We go to the cyberghost website, logged in, My Devices.

![](/content/images/2015/11/1426443764.png)

Then you go below and save the configuration, download the Cyberghost.opvn file.

Move the file `Cyberghost.opvn` to `/etc/openvpn` and rename it to `vpn-de.conf` (you can name it whatever you want, I have it this way to switch between countries, vpn-*country-code*).

We need two more files, we create an `/etc/openvpn/user.txt` and we add our Cyberghost credentials (user, password) in different lines, like this.

<pre><code class="language-bash">javi
myPassword
</code></pre>

The second file, we get the script to update-resolv (DNS) every time we open/close the VPN connection, you can get it from [this gist][resolv] linked from the ArchWiki. We put it in `/etc/openvpn/update-resolv-conf.sh` and make it executable `chmod +x /etc/openvpn/update-resolv-conf.sh`.

We've got all the files, now let's edit our `/etc/openvpn/vpn-de.conf` to update DNS with our update-resolv script and to get the `user.txt` credentials. We'll need to do this changes in all the `/etc/openvpn/vpn-<country>.conf` per country cyberghost configurations (if we have more than one configuration).

We change the line

<pre><code class="language-bash">auth-user-pass
</code></pre>

for

<pre><code class="language-bash">
auth-user-pass /etc/openvpn/user.txt
</code></pre>


and before the certificate tag `&lt;ca&gt;` we add the lines to update the DNS in this same file.

<pre><code class="language-bash">...

up /etc/openvpn/update-resolv-conf.sh
down /etc/openvpn/update-resolv-conf.sh
</code></pre>

That's it! We can now enjoy our vpn using the following command.

<pre><code class="language-bash"># systemctl start openvpn@vpn-de
</code></pre>

where vpn-de is our `/etc/openvpn/vpn-de.conf` profile/configuration, if you change the name (for example `/etc/openvpn/cyberghost.conf`) your command will be...

<pre><code class="language-bash"># systemctl start openvpn@cyberghost
</code></pre>

If this looks tedious you could just use the network manager in GNOME/KDE or others, but what's the fun in that right? :-)

**UPDATE 29th Apr 2017**: [There is a new updated article regarding best VPN services](https://thebestvpn.com/)

[resolv]: https://raw.githubusercontent.com/masterkorp/openvpn-update-resolv-conf/master/update-resolv-conf.sh
[servers]: http://www.cyberghostvpn.com/en_us/server
[stand]: http://www.cyberghostvpn.com/en_us
[anonymous]: http://torrentfreak.com/which-vpn-services-take-your-anonymity-seriously-2014-edition-140315/2/
[lifehacker]: http://lifehacker.com/5940565/why-you-should-start-using-a-vpn-and-how-to-choose-the-best-one-for-your-needs
[openvpn]: https://wiki.archlinux.org/index.php/OpenVPN
