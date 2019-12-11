
pstree -p   

    systemd(1)─┬─NetworkManager(447)─┬─{NetworkManager}(466)
           │                     └─{NetworkManager}(469)
           ├─VBoxService(621)─┬─{VBoxService}(622)
           │                  ├─{VBoxService}(623)
           │                  ├─{VBoxService}(624)
           │                  ├─{VBoxService}(625)
           │                  ├─{VBoxService}(626)
           │                  ├─{VBoxService}(627)
           │                  └─{VBoxService}(628)
           ├─agetty(482)
           ├─auditd(419)───{auditd}(420)
           ├─chronyd(452)
           ├─dbus-broker-lau(467)───dbus-broker(468)
           ├─gssproxy(453)─┬─{gssproxy}(454)
           │               ├─{gssproxy}(455)
           │               ├─{gssproxy}(456)
           │               ├─{gssproxy}(457)
           │               └─{gssproxy}(458)
           ├─sshd(473)───sshd(642)───sshd(653)───bash(654)───zsh(770)───zsh(3702)───bash(3794+
           ├─sssd(450)─┬─sssd_be(470)
           │           └─sssd_nss(478)
           ├─systemd(646)───(sd-pam)(648)
           ├─systemd-journal(400)
           ├─systemd-logind(542)
           └─systemd-udevd(410)─┬─systemd-udevd(5591)
                                └─systemd-udevd(5592)

  localtime  = current time zone 
  Universal time  =  Standard time zone 
  RTC = Hardware clock, pile qui garde l'heure même quand le pc est éteins (perte du net ou autre)

    sudo timedatectl set-timezone Europe/Prague



sudo timedatectl


                   Local time: ven. 2019-11-29 14:29:04 CET
           Universal time: ven. 2019-11-29 13:29:04 UTC
                 RTC time: ven. 2019-11-29 14:29:03
                Time zone: Europe/Prague (CET, +0100)
    System clock synchronized: yes
                  NTP service: inactive
              RTC in local TZ: yes

Static : assigné par l'admin sys et utilisé pour initialisé le kernel pendant le boot, hostname traditionnel de l'user
Dynamic : assigner par mdns ou dhcp pendant le run time
Pretty: un hostname sans restriction de charactère non lus par la machine.
Pour des machines de prod, celui a utilisé et le static.

    DHCP4.OPTION[1]:                        dhcp_lease_time = 86400
    DHCP4.OPTION[2]:                        dhcp_rebinding_time = 75600
    DHCP4.OPTION[3]:                        dhcp_renewal_time = 43200
    DHCP4.OPTION[4]:                        dhcp_server_identifier = 10.0.2.2
    DHCP4.OPTION[5]:                        domain_name = auvence.co
    DHCP4.OPTION[6]:                        domain_name_servers = 10.0.2.3
    DHCP4.OPTION[7]:                        expiry = 1575112190
    DHCP4.OPTION[8]:                        ip_address = 10.0.2.15
    DHCP4.OPTION[9]:                        requested_broadcast_address = 1
    DHCP4.OPTION[10]:                       requested_dhcp_server_identifier = 1
    DHCP4.OPTION[11]:                       requested_domain_name = 1
    DHCP4.OPTION[12]:                       requested_domain_name_servers = 1
    DHCP4.OPTION[13]:                       requested_domain_search = 1
    DHCP4.OPTION[14]:                       requested_host_name = 1
    DHCP4.OPTION[15]:                       requested_interface_mtu = 1
    DHCP4.OPTION[16]:                       requested_ms_classless_static_routes = 1
    DHCP4.OPTION[17]:                       requested_nis_domain = 1
    DHCP4.OPTION[18]:                       requested_nis_servers = 1
    DHCP4.OPTION[19]:                       requested_ntp_servers = 1
    DHCP4.OPTION[20]:                       requested_rfc3442_classless_static_routes = 1
    DHCP4.OPTION[21]:                       requested_root_path = 1
    DHCP4.OPTION[22]:                       requested_routers = 1
    DHCP4.OPTION[23]:                       requested_static_routes = 1
    DHCP4.OPTION[24]:                       requested_subnet_mask = 1
    DHCP4.OPTION[25]:                       requested_time_offset = 1
    DHCP4.OPTION[26]:                       requested_wpad = 1
    DHCP4.OPTION[27]:                       routers = 10.0.2.2
    DHCP4.OPTION[28]:                       subnet_mask = 255.255.255.0

sudo systemctl stop NetworkManager.service
sudo systemctl disable NetworkManager.service

    Removed /etc/systemd/system/network-online.target.wants/NetworkManager-wait-online.service.
    Removed /etc/systemd/system/multi-user.target.wants/NetworkManager.service.
    Removed /etc/systemd/system/dbus-org.freedesktop.nm-dispatcher.service.

sudo systemctl start systemd-networkd.service
sudo systemctl enable systemd-networkd.service

    Created symlink /etc/systemd/system/dbus-org.freedesktop.network1.service → /usr/lib/systemd/system/systemd-networkd.service.
    Created symlink /etc/systemd/system/multi-user.target.wants/systemd-networkd.service → /usr/lib/systemd/system/systemd-networkd.service.
    Created symlink /etc/systemd/system/sockets.target.wants/systemd-networkd.socket → /usr/lib/systemd/system/systemd-networkd.socket.
    Created symlink /etc/systemd/system/network-online.target.wants/systemd-networkd-wait-online.service → /usr/lib/systemd/system/systemd-networkd-wait-online.service.
 
<br>

    [Match]
    Key=enp0s3
    
    [Network]
    Address=10.0.2.15/24
    DNS=1.1.1.1


sudo systemctl start systemd-resolved.service
sudo systemctl enable systemd-resolved.service

    Created symlink /etc/systemd/system/dbus-org.freedesktop.resolve1.service → /usr/lib/systemd/system/systemd-resolved.service.
    Created symlink /etc/systemd/system/multi-user.target.wants/systemd-resolved.service → /usr/lib/systemd/system/systemd-resolved.service.

sudo ss -laputn | grep resolve

    udp     UNCONN   0        0          127.0.0.53%lo:53            0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=18))                                    
    udp     UNCONN   0        0                0.0.0.0:5355          0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=12))                                    
    udp     UNCONN   0        0                   [::]:5355             [::]:*       users:(("systemd-resolve",pid=10989,fd=14))                                    
    tcp     LISTEN   0        128              0.0.0.0:5355          0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=13))                                    
    tcp     LISTEN   0        128        127.0.0.53%lo:53            0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=19))                                    
    tcp     LISTEN   0        128                 [::]:5355             [::]:*       users:(("systemd-resolve",pid=10989,fd=15)) 
    
Effectuer une commande de résoution de nom avec dig:  

    dig google.com127.0.0.53
  
Résultat:  
dig google.com @127.0.0.53 

    ; <<>> DiG 9.11.13-RedHat-9.11.13-2.fc31 <<>> google.com @127.0.0.53
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30216
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
    
    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 65494
    ;; QUESTION SECTION:
    ;google.com.                    IN      A
    
    ;; ANSWER SECTION:
    google.com.             233     IN      A       216.58.215.46
    
    ;; Query time: 100 msec
    ;; SERVER: 127.0.0.53#53(127.0.0.53)
    ;; WHEN: ven. nov. 29 16:42:25 CET 2019
    ;; MSG SIZE  rcvd: 55
    
    Effectuer une requête DNS avec systemd-resolve:
    systemd-resolve google.fr
    google.fr: 172.217.19.227                      -- link: enp0s8
    
    -- Information acquired via protocol DNS in 1.2ms.
    -- Data is authenticated: no

  
  
  
Un lien symbolique pointant vers /run/systemd/resolve/stub-resolv.conf :  
  
Supprimer le fichier : 

    sudo rm -rf /etc/resolv.conf

  
  
Mise en place du lien symbolique :  

    sudo ln -s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf


dnssec
sudo cat /etc/systemd/resolved.conf

    [Resolve]
    DNS=1.1.1.1
    #FallbackDNS=1.1.1.1 8.8.8.8 1.0.0.1 8.8.4.4 2606:4700:4700::1111 2001:4860:4860::8888 2606:4700:4700::1001 2001:4860:4860::8844
    #Domains=
    #LLMNR=yes
    #MulticastDNS=yes
    DNSSEC=force
    DNSOverTLS=yes
    #Cache=yes
    #DNSStubListener=yes
    #ReadEtcHosts=yes

systemctl status 452

    ● chronyd.service - NTP client/server
       Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
       Active: active (running) since Thu 2019-12-05 11:19:56 UTC; 18min ago
         Docs: man:chronyd(8)
               man:chrony.conf(5)
      Process: 449 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
      Process: 465 ExecStartPost=/usr/libexec/chrony-helper update-daemon (code=exited, status=0/>
     Main PID: 452 (chronyd)
        Tasks: 1 (limit: 1146)
       Memory: 2.0M
          CPU: 70ms
       CGroup: /system.slice/chronyd.service
               └─452 /usr/sbin/chronyd

 mkdir graphe
cd graphe
systemd-analyze plot > graphe.svg

cat graphe.svg | grep "sshd.service"

    <text class="right" x="2734.235" y="4614.000">sshd.service (197ms)</text>

cgroups

d-Bus
