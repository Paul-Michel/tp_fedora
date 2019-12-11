# I. Systemd-basics

## 1. First step
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

## 2. Gestion du temps
  localtime  = current time zone 
  Universal time  =  Standard time zone 
  RTC = Hardware clock, pile qui garde l'heure même quand le pc est éteins (perte du net ou autre)

    

> sudo timedatectl set-timezone Europe/Prague
> 
> 
> 
> sudo timedatectl


                   Local time: ven. 2019-11-29 14:29:04 CET
           Universal time: ven. 2019-11-29 13:29:04 UTC
                 RTC time: ven. 2019-11-29 14:29:03
                Time zone: Europe/Prague (CET, +0100)
    System clock synchronized: yes
                  NTP service: inactive
              RTC in local TZ: yes
## 3. Gestion de noms
Static : assigné par l'admin sys et utilisé pour initialisé le kernel pendant le boot, hostname traditionnel de l'user
Dynamic : assigner par mdns ou dhcp pendant le run time
Pretty: un hostname sans restriction de charactère non lus par la machine.
Pour des machines de prod, celui a utilisé et le static.
## 4. Gestion du réseau
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

Commande a effectuer pour désactiver et stop le service :
> sudo systemctl stop NetworkManager.service sudo systemctl disable
> NetworkManager.service

    Removed /etc/systemd/system/network-online.target.wants/NetworkManager-wait-online.service.
    Removed /etc/systemd/system/multi-user.target.wants/NetworkManager.service.
    Removed /etc/systemd/system/dbus-org.freedesktop.nm-dispatcher.service.

Commande a effectuer pour lancer et activer le démarrage du service :
> sudo systemctl start systemd-networkd.service sudo systemctl enable
> systemd-networkd.service

    Created symlink /etc/systemd/system/dbus-org.freedesktop.network1.service → /usr/lib/systemd/system/systemd-networkd.service.
    Created symlink /etc/systemd/system/multi-user.target.wants/systemd-networkd.service → /usr/lib/systemd/system/systemd-networkd.service.
    Created symlink /etc/systemd/system/sockets.target.wants/systemd-networkd.socket → /usr/lib/systemd/system/systemd-networkd.socket.
    Created symlink /etc/systemd/system/network-online.target.wants/systemd-networkd-wait-online.service → /usr/lib/systemd/system/systemd-networkd-wait-online.service.
 
Edition de la carte réseaux : 

> nano enp0s3.network

    [Match]
    Key=enp0s3
    
    [Network]
    Address=10.0.2.15/24
    DNS=1.1.1.1

Commande pour activer et démarrer le service :
> sudo systemctl start systemd-resolved.service sudo systemctl enable
> systemd-resolved.service

    Created symlink /etc/systemd/system/dbus-org.freedesktop.resolve1.service → /usr/lib/systemd/system/systemd-resolved.service.
    Created symlink /etc/systemd/system/multi-user.target.wants/systemd-resolved.service → /usr/lib/systemd/system/systemd-resolved.service.

Commande a effectuer pour vérifier que le serveur dns tourne localement :
> sudo ss -laputn | grep resolve

    udp     UNCONN   0        0          127.0.0.53%lo:53            0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=18))                                    
    udp     UNCONN   0        0                0.0.0.0:5355          0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=12))                                    
    udp     UNCONN   0        0                   [::]:5355             [::]:*       users:(("systemd-resolve",pid=10989,fd=14))                                    
    tcp     LISTEN   0        128              0.0.0.0:5355          0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=13))                                    
    tcp     LISTEN   0        128        127.0.0.53%lo:53            0.0.0.0:*       users:(("systemd-resolve",pid=10989,fd=19))                                    
    tcp     LISTEN   0        128                 [::]:5355             [::]:*       users:(("systemd-resolve",pid=10989,fd=15)) 
    
Effectuer une commande de résoution de nom avec dig:  

    

> dig google.com127.0.0.53

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

   

>  sudo rm -rf /etc/resolv.conf

  
  
Mise en place du lien symbolique :  

> sudo ln -s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
## 6. Gestion d'unité basique
Pour trouver l'unité associé au processus chronyd

> systemctl status 452

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
# II. Boot et Logs
Commande a réaliser afin de générer un graph de la séquence de boot :

>  mkdir graphe 
>  cd graphe 
>  systemd-analyze plot > graphe.svg

cat graphe.svg | grep "sshd.service"

    <text class="right" x="2734.235" y="4614.000">sshd.service (197ms)</text>

# III. Mécanismes manipulés par systemd

Identifier le cgroup utilisé par votre session SSH :

> ps -e -o pid,cmd,cgroup

    1464 sshd: vagrant@pts/0         0::/user.slice/user-1000.slice/session-3.scope

> cat /proc/1464/cgroup

    0::/user.slice/user-1000.slice/session-3.scope
Modifier la RAM dédiée a la session utilisateur :

> sudo systemctl set-property user-1000.slice MemoryMax=510M 
> cat /sys/fs/cgroup/user.slice/user-1000.slice/memory.max

    534773760

> sudo systemctl set-property user-1000.slice MemoryMax=512M 
> cat /sys/fs/cgroup/user.slice/user-1000.slice/memory.max

    536870912


    

> sudo cat /etc/systemd/system.control/user-1000.slice.d/50-MemoryMax.conf

    This is a drop-in unit file extension, created via "systemctl set-property"
    or an equivalent operation. Do not edit.
    [Slice]
    MemoryMax=536870912

## 2. D-Bus
Partie non compris + erreur de d-bus.

## 3.Restriction et isolation
> systemctl status run-u51.service

    ● run-u51.service - /bin/bash
       Loaded: loaded (/run/systemd/transient/run-u51.service; transient)
    Transient: yes
       Active: active (running) since Wed 2019-12-11 09:12:22 UTC; 42s ago
     Main PID: 5302 (bash)
        Tasks: 3 (limit: 1146)
       Memory: 2.2M
          CPU: 62ms
       CGroup: /system.slice/run-u51.service
               ├─5302 /bin/bash
               ├─5320 systemctl status run-u51.service
               └─5321 less
Identifier le cgroup utilisé : 

> ps -e -o pid,cmd,cgroup

       5301 systemd-run --wait -t /bin/ 0::/user.slice/user-1000.slice/session-3.scope
Ajouter des restrictions cgroups :
> sudo systemd-run -p MemoryMax=512M --wait -t /bin/bash

    Running as unit: run-u80.service
    Press ^] three times within 1s to disconnect TTY.
Ajouter un traçage réseaux :
> sudo systemd-run -p IPAccounting=true --wait -t /bin/bash

    Running as unit: run-u84.service
    Press ^] three times within 1s to disconnect TTY.
Ajout des restrictions réseau : 
> sudo systemd-run  -p IPAddressAllow=192.168.56.0/24 -p IPAddressDeny=any /bin/bash

    Running as unit: run-r012be849185e4319a4cfa74f6cdeea55.service

    PING 192.168.56.0 (192.168.56.0) 56(84) bytes of data.
    ^C
    --- 192.168.56.0 ping statistics ---
    3 packets transmitted, 0 received, 100% packet loss, time 2080ms

systemd-nspawn ne fonctionnant pas, je n'ai pas pu réaliser cette partie.

> sudo systemd-nspawn --ephemeral --private-network -D / bash

    sudo: systemd-nspawn: command not found
# IV. Systemd units in-depth
## 1. Exploration de services existant
Observer l'unité auditd.service : 

> sudo systemctl status auditd.service

    ● auditd.service - Security Auditing Service
       Loaded: loaded (/usr/lib/systemd/system/auditd.service; enabled; vendor preset: enabled)
       Active: active (running) since Wed 2019-12-11 08:49:09 UTC; 1h 0min ago
         Docs: man:auditd(8)
               https://github.com/linux-audit/audit-documentation
      Process: 427 ExecStart=/sbin/auditd (code=exited, status=0/SUCCESS)
      Process: 432 ExecStartPost=/sbin/augenrules --load (code=exited, status=0/SUCCESS)
     Main PID: 429 (auditd)
        Tasks: 2 (limit: 1146)
       Memory: 4.8M
          CPU: 83ms
       CGroup: /system.slice/auditd.service
               └─429 /sbin/auditd

> sudo systemctl cat auditd.service

    # /usr/lib/systemd/system/auditd.service
    [Unit]
    Description=Security Auditing Service
    DefaultDependencies=no
    ## If auditd is sending or recieving remote logging, copy this file to
    ## /etc/systemd/system/auditd.service and comment out the first After and
    ## uncomment the second so that network-online.target is part of After.
    ## then comment the first Before and uncomment the second Before to remove
    ## sysinit.target from "Before".
    After=local-fs.target systemd-tmpfiles-setup.service
    ##After=network-online.target local-fs.target systemd-tmpfiles-setup.service
    Before=sysinit.target shutdown.target
    ##Before=shutdown.target
    Conflicts=shutdown.target
    RefuseManualStop=yes
    ConditionKernelCommandLine=!audit=0
    Documentation=man:auditd(8) https://github.com/linux-audit/audit-documentation
    
    [Service]
    Type=forking
    PIDFile=/run/auditd.pid
    ExecStart=/sbin/auditd
    ## To not use augenrules, copy this file to /etc/systemd/system/auditd.service
    ## and comment/delete the next line and uncomment the auditctl line.
    ## NOTE: augenrules expect any rules to be added to /etc/audit/rules.d/
    ExecStartPost=-/sbin/augenrules --load
    #ExecStartPost=-/sbin/auditctl -R /etc/audit/audit.rules
    # By default we don't clear the rules on exit. To enable this, uncomment
    # the next line after copying the file to /etc/systemd/system/auditd.service
    #ExecStopPost=/sbin/auditctl -R /etc/audit/audit-stop.rules
    
    ### Security Settings ###
    MemoryDenyWriteExecute=true
    LockPersonality=true
    ProtectControlGroups=true
    ProtectKernelModules=true
    
    [Install]
    WantedBy=multi-user.target
## 2. Création de service simple
Créer un fichier qui comporte le suffixe service :
> sudo touch /etc/systemd/system/test.service 
> sudo nano /etc/systemd/system/test.service

    [Unit]
    Description=Le service de test
    After=network.target
    StartLimitIntervalSec=0
    [Service]
    Type=simple
    Restart=always
    RestartSec=1
    User=vagrant
    ExecStart=la commande pour lancer le serveur web 
    ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp`
    ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp`
    
    [Install]
    WantedBy=multi-user.target
Enable le service au démarrage :

> sudo systemctl enable test.service

    Created symlink /etc/systemd/system/multi-user.target.wants/test.service → /etc/systemd/system/test.service.

cette commande créer un liens symbolique entre le service et les services a lancer au démarrage.
## 4.Event-based activation
Installation docker : 
> yum install docker

Faire en sorte que docker démarre tout seul si il est solicité :

    # Création d'un fichier .service
    $ sudo vim /etc/systemd/system/docker.service
    # Création d'un fichier .socket correspondant
    $ sudo vim /etc/systemd/system/docker.socket
    # Syntaxe du fichier
    $ cat /etc/systemd/system/dock.socket
    [Unit]
    Description=docker socket
    [Socket]
    ListenStream=0.0.0.0:80
    ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp
    ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp --permanent
    ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp
    ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp --permanent
    [Install]
    WantedBy=sockets.target

