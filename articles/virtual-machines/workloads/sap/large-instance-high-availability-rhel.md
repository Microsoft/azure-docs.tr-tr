---
title: Azure büyük örnekler RHEL 'de SAP için yüksek kullanılabilirlik
description: Red Hat Enterprise Linux bir Paceoluşturucu kümesi kullanarak SAP HANA veritabanı yük devretmesini nasıl otomatikleştirebileceğinizi öğrenin.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: f7b6e6efbbd17655b4f68d79ac26ee34ae754a3b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728455"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Azure büyük örnekler RHEL 'de SAP için yüksek kullanılabilirlik

> [!NOTE]
> Bu makale, Microsoft 'un artık kullanmadığını gösteren bir terim olan *kara liste* dönemi başvuruları içerir. Bu terim yazılımlardan kaldırıldığında, bu makaleyi kaldıracağız.

Bu makalede, bir SAP HANA veritabanı yük devretmesini otomatikleştirmek üzere RHEL 7,6 ' de Paceoluşturucu kümesini nasıl yapılandıracağınızı öğreneceksiniz. Bu kılavuzdaki adımları tamamlayabilmeniz için Linux, SAP HANA ve Paceyapıcısı hakkında iyi bir fikir sahibi olmanız gerekir.

Aşağıdaki tabloda, bu makale boyunca kullanılan ana bilgisayar adları yer almaktadır. Makaledeki kod blokları, çalıştırılması gereken komutların yanı sıra bu komutların çıkışını gösterir. Her komutta hangi düğüme başvurulduğunu hemen ödeyin.

| Tür | Konak adı | Düğüm|
|-------|-------------|------|
|Birincil konak|`sollabdsm35`|düğüm 1|
|İkincil konak|`sollabdsm36`|düğüm 2|

## <a name="configure-your-pacemaker-cluster"></a>Pacemaker kümenizi yapılandırma


Kümeyi yapılandırmaya başlayabilmeniz için önce düğümler arasında güven sağlamak üzere SSH anahtar değişimini ayarlayın.

1. Her iki düğüm üzerinde özdeş oluşturmak için aşağıdaki komutları kullanın `/etc/hosts` .

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  SSH anahtarlarını oluşturma ve değiştirme.
    1. SSH anahtarları oluşturun.

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. Anahtarları, passwordless SSH için diğer konaklara kopyalayın.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Her iki düğümde de SELinux 'u devre dışı bırakın.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Sunucuları yeniden başlatın ve ardından SELinux 'un durumunu doğrulamak için aşağıdaki komutu kullanın.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. NTP yapılandırma (ağ zaman Protokolü). Her iki küme düğümü için saat ve saat dilimleri eşleşmelidir. Dosya içeriğini açmak ve doğrulamak için aşağıdaki komutu kullanın `chrony.conf` .
    1. Aşağıdaki içerikler yapılandırma dosyasına eklenmelidir. Gerçek değerleri ortamınıza göre değiştirin.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Zaman hatası hizmetini etkinleştirin. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Sistemi güncelleştirme
    1. İlk olarak, SBD cihazını yüklemeye başlamadan önce en son güncelleştirmeleri sisteme yüklemeniz gerekir.
    1. Sistemin tamamen güncelleştirilmesini istemiyorsanız, önerilse bile aşağıdaki paketleri en düşük düzeyde güncelleştirin.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. SAP HANA ve RHEL-HA depoları ' nı yükler.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Tüm düğümlere Paceyapıcısı, SBD, Openıpmı, ıpmıtool ve fencing_sbd araçları 'nı yükler.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>Izleme 'yi yapılandırma

Bu bölümde, Izleme 'yi yapılandırmayı öğreneceksiniz. Bu bölümde, `sollabdsm35` `sollabdsm36` Bu makalenin başlangıcında başvurulan iki ana bilgisayar kullanılır.

1. İzleme arka plan programının herhangi bir sistemde çalıştığından emin olun.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Yükleme sırasında yüklenecek olan varsayılan Linux izleme, UCS ve HPE SDFlex sistemleri tarafından desteklenmeyen ICO İzleyicisi 'dir. Bu nedenle, bu izleme devre dışı bırakılmalıdır.
    1. Sistemde yanlış izleme yüklendi ve yüklendi:
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Ortamdan yanlış sürücüyü kaldırın:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Sürücünün bir sonraki sistem önyüklemesi sırasında yüklenmediğini doğrulamak için sürücünün blocklistelenmesi gerekir. ICO modüllerini engelleme için, dosyanın sonuna şunu ekleyin `50-blacklist.conf` :
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Dosyayı ikincil konağa kopyalayın.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. IPMI hizmeti başlatılmışsa test edin. IPMı zamanlayıcının çalışmadığı önemli bir öneme sahiptir. Zamanlayıcı yönetimi, SBD paceoluşturucu hizmetinden yapılır.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Varsayılan olarak, gerekli cihaz/dev/izleme oluşturulmaz.

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. IPMı İzleyicisi 'ni yapılandırın.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. İzleme yapılandırma dosyasını ikinciye kopyalayın.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  IPMI hizmetini etkinleştirip başlatın.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Artık ıPMı hizmeti başlatılmış ve/dev/izleme aygıtı oluşturuldu ancak Zamanlayıcı hala durdurulmuş. Daha sonra SBD, izleme sıfırlamasını yönetecek ve ıPMı zamanlayıcısını etkinleştirmeyecektir.
7.  /Dev/izleyici 'nin var olduğunu ancak kullanımda olmadığını kontrol edin.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD yapılandırması
Bu bölümde, SBD 'i yapılandırmayı öğrenirsiniz. Bu bölümde, `sollabdsm35` `sollabdsm36` Bu makalenin başlangıcında başvurulan iki ana bilgisayar kullanılır.

1.  Iscsı veya FC diskinin her iki düğümde de göründüğünden emin olun. Bu örnek, FC tabanlı bir SBD cihazı kullanır. SBD uçuşlama hakkında daha fazla bilgi için bkz. [RHEL yüksek kullanılabilirlik kümeleri Için tasarım kılavuzu-SBD konuları](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Faccess.redhat.com%2Farticles%2F2941601&data=04%7C01%7Cralf.klahr%40microsoft.com%7Cd49d7a3e3871449cdecc08d8c77341f1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637478645171139432%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=c%2BUAC5gmgpFNWZCQFfiqcik8CH%2BmhH2ly5DsOV1%2FE5M%3D&reserved=0).
2.  LUN KIMLIĞI tüm düğümlerde aynı olmalıdır.
  
3.  SBD cihazının çok yollu durumunu denetleyin.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  SBD disklerini oluşturma ve küme temel yapı 'yı ayarlama. Bu adımın ilk düğümde yürütülmesi gerekir.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  SBD yapılandırmasını Düğüm2 'e kopyalayın.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  SBD diskinin her iki düğümden de görünür olup olmadığını denetleyin.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  SBD cihazını SBD yapılandırma dosyasına ekleyin.

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Küme başlatma
Bu bölümde, kümeyi başlatırsınız. Bu bölümde, `sollabdsm35` `sollabdsm36` Bu makalenin başlangıcında başvurulan iki ana bilgisayar kullanılır.

1.  Küme kullanıcı parolasını ayarlayın (tüm düğümler).
    ```
    passwd hacluster
    ```
2.  BILGISAYARLARı tüm sistemlerde başlatın.
    ```
    systemctl enable pcsd
    ```
  

3.  Güvenlik duvarını durdurun ve devre dışı bırakın (tüm düğümler).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Pcsd hizmetini başlatın.
    ```
    systemctl start pcsd
    ```

5.  Küme kimlik doğrulamasını yalnızca Düğüm1 adresinden çalıştırın.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  Kümeyi oluşturun.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Küme durumunu denetleyin.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Bir düğüm, güvenlik duvarı çalışmaya devam ediyorsa küme denetimine katılmaz.

9. SBD cihazını oluşturma ve etkinleştirme
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. Kümeyi durdurun küme hizmetlerini (tüm düğümlerde) yeniden başlatın.

    ```
    pcs cluster stop --all
    ```

11. Küme hizmetlerini yeniden başlatın (tüm düğümlerde).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync, SBD hizmetini başlatmalıdır.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Kümeyi yeniden başlatın (pcsd 'den otomatik olarak başlatılmazsa).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Stonith ayarlarını etkinleştirin.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Yeni küme durumunu şimdi bir kaynakla denetleyin.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Artık ıPMı zamanlayıcının çalıştırılması ve/dev/izleyici cihazının SBD tarafından açılması gerekir.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. SBD durumunu denetleyin.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Çekirdek kilitlenerek, SBD birleştirme sınamasını test edin.

    * Çekirdek kilitlenmeyi tetikler.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * Çalıştırmak için ikinci test, bılgısayar komutlarını kullanarak bir düğümün dilimini kullanmaktır.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. SAP HANA kümelemesinin geri kalanı için şu ayarı yaparak STONITH 'yi devre dışı bırakabilirsiniz:

   * PCs özellik kümesi `stonith-enabled=false`
   * Sistemin beklenmedik şekilde yeniden başlatmaları önleyebileceğiniz için, kümenin kurulumu sırasında STONITH 'nin devre dışı bırakılması daha kolay olacaktır.
   * Üretken kullanım için bu parametrenin true olarak ayarlanması gerekir. Bu parametre true olarak ayarlanmamışsa, küme desteklenmeyecektir.
   * PCs özellik kümesi `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>Kümeye HANA tümleştirmesi

Bu bölümde, HANA 'yı kümeyle tümleştirin. Bu bölümde, `sollabdsm35` `sollabdsm36` Bu makalenin başlangıcında başvurulan iki ana bilgisayar kullanılır.

HANA 'yı tümleştirmek için iki seçenek vardır. İlk seçenek, QAS sistemini çalıştırmak için ikincil sistemi kullanabileceğiniz, uygun maliyetli bir çözümdür. Bu yöntemi, küme yazılımı, işletim sistemi veya HANA üzerinde güncelleştirmeleri test etmek için hiçbir sistem kalmayacak ve yapılandırma güncelleştirmeleri PRD sisteminin planlanmamış kapalı kalma süresine neden olabileceği için önerilmez. Ayrıca, PRD sisteminin ikincil sistemde etkinleştirilmesi gerekiyorsa, ikincil düğümde QAS kapatılmalıdır. İkinci seçenek ise QAS sistemini bir kümeye yüklemek ve PRD için ikinci bir küme kullanmaktır. Bu seçenek ayrıca, üretime yerleştirmadan önce tüm bileşenleri test etmenize olanak tanır. Bu makalede ikinci seçeneğin nasıl yapılandırılacağı gösterilir.


* Bu işlem şu sayfada RHEL açıklaması oluşturur:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>HSR 'yi yapılandırmak için izlenecek adımlar

1.  Bunlar, Düğüm1 (birincil) üzerinde yürütülecek eylemlerdir.
    1. Veritabanı günlük modunun normal olarak ayarlandığından emin olun.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA sistem çoğaltması, yalnızca ilk yedekleme gerçekleştirildikten sonra çalışır. Aşağıdaki komut dizinde bir başlangıç yedeklemesi oluşturur `/tmp/` . Veritabanı için uygun bir yedekleme dosya sistemi seçin. 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. Bu veritabanının tüm veritabanı kapsayıcılarını yedekleyin.
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Kaynak sistemde HSR işlemini etkinleştirin.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Birincil sistemin durumunu denetleyin.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Bunlar, Düğüm2 (ikincil) üzerinde yürütülecek eylemlerdir.
     1. Veritabanını durdurun.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Yalnızca SAP HANA 2.0 için, SAP HANA sistemini `PKI SSFS_HR2.KEY` ve `SSFS_HR2.DAT` dosyalarını birincil düğümden ikincil düğüme kopyalayın.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Çoğaltma sitesi olarak ikincil öğesini etkinleştirin.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Veritabanını başlatın.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Veritabanı durumunu kontrol edin.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Çoğaltma durumu hakkında daha fazla bilgi edinmek için de mümkündür:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Günlük çoğaltma modu açıklaması

Günlük çoğaltma modu hakkında daha fazla bilgi için bkz. [RESMI SAP belgeleri](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>HANA sistem çoğaltması için ağ kurulumu


Çoğaltma trafiğinin çoğaltma için doğru VLAN 'ı kullandığından emin olmak için, içinde düzgün şekilde yapılandırılması gerekir `global.ini` . Bu adımı atlarsanız, HANA çoğaltma için erişim VLAN 'ıNı kullanacaktır ve bu da istenmeyen bir işlem olabilir.


Aşağıdaki örneklerde, ikincil bir siteye sistem çoğaltması için ana bilgisayar adı çözümleme yapılandırması gösterilmektedir. Üç farklı ağ tanımlanabilir:

* 10.0.1. * aralığında adreslere sahip ortak ağ

* Her sitedeki konaklar arasındaki iç SAP HANA iletişimi için ağ: 192.168.1. *

* Sistem çoğaltma için adanmış ağ: 10.5.1. *

İlk örnekte, `[system_replication_communication]listeninterface` parametresi olarak ayarlanmıştır `.global` ve yalnızca komşu çoğaltma sitesinin Konakları belirtilir.

Aşağıdaki örnekte, `[system_replication_communication]listeninterface` parametresi olarak ayarlanmıştır `.internal` ve her iki sitenin da tüm Konakları belirtilmiştir.

  

Daha fazla bilgi için bkz. [SAP HANA sistem çoğaltması Için ağ yapılandırması](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html).

  

Sistem çoğaltma için, dosyayı düzenlemek gerekli değildir `/etc/hosts` , iç (' sanal ') konak adlarının, `global.ini` sistem çoğaltması için adanmış bir ağ oluşturmak üzere dosyadaki IP adresleriyle eşlenmesi gerekir. Bunun sözdizimi şöyledir:

global.ini

[system_replication_hostname_resolution]

<IP-address_site>=<iç-konak-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Bir pacemaker kümesinde SAP HANA yapılandırma
Bu bölümde, bir pacemaker kümesinde SAP HANA yapılandırmayı öğreneceksiniz. Bu bölümde, `sollabdsm35` `sollabdsm36` Bu makalenin başlangıcında başvurulan iki ana bilgisayar kullanılır.

Aşağıdaki önkoşulların karşılandığından emin olun:  

* Pacemaker kümesi, belgelere göre yapılandırılır ve uygun ve çalışır şekilde çalışır

* Başlatma ve durdurma küme tarafından yönetilebilmesi için önyüklemede başlatma SAP HANA tüm küme düğümlerinde devre dışı bırakıldı

* SAP HANA sistem çoğaltma ve SAP araçlarını kullanarak alma, küme düğümleri arasında düzgün çalışıyor

* SAP HANA, her iki küme düğümünden küme tarafından kullanılabilen izleme hesabını içerir

* Her iki düğüm de ' yüksek kullanılabilirlik ' ve ' RHEL for SAP HANA ' (RHEL 6, RHEL 7) kanallarına abone oldu

  

* Genel olarak, CıB yalnızca bilgisayar kabuğu 'ndan otomatik olarak güncelleştirildiğinden, lütfen tüm PC komutlarını yalnızca düğümünden çalıştırın.

* [Çekirdek ilkesi hakkında daha fazla bilgi](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Yapılandırma adımları 
1. Bilgisayarları yapılandırın.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Corosync 'i yapılandırın.
    Daha fazla bilgi için bkz. [RHEL 7 yüksek kullanılabilirlik kümenizi pacemaker ve Corosync Ile nasıl yapılandırabilirim](https://access.redhat.com/solutions/1293523).
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  Kopyalanmış SAPHanaTopology kaynağı oluşturun.
    SAPHanaTopology kaynağı her düğümde SAP HANA sistem çoğaltmasının durumunu ve yapılandırmasını topluyor. SAPHanaTopology için aşağıdaki özniteliklerin yapılandırılması gerekir.
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1    interleave=true
       ```

    | Öznitelik adı | Description  |
    |---|---|
    | SID | SAP HANA yüklemesinin SAP sistem tanımlayıcısı (SID). Tüm düğümler için aynı olmalıdır. |
    | Örneksayısı | 2 basamaklı SAP örneği Idntifier.|

    * Kaynak durumu
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       InstanceNumber 2-digit SAP Instance identifier.
       pcs resource show SAPHanaTopology_HR2_00-clone
   
       Clone: SAPHanaTopology_HR2_00-clone
   
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
   
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
       type=SAPHanaTopology)
   
        Attributes: InstanceNumber=00 SID=HR2
   
        Operations: monitor interval=60 timeout=60
       (SAPHanaTopology_HR2_00-monitor-interval-60)
   
        start interval=0s timeout=180
       (SAPHanaTopology_HR2_00-start-interval-0s)
   
        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
   
       ```

4.  Birincil/Ikincil SAPHana kaynağı oluşturun.
    * SAPHana kaynağı SAP HANA veritabanını başlatma, durdurma ve yeniden konumlandırmaktan sorumludur. Bu kaynağın birincil/Ikincil küme kaynağı olarak çalıştırılması gerekir. Kaynak aşağıdaki özniteliklere sahiptir.

| Öznitelik adı            | Gerekli mi? | Varsayılan değer | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | Yes       | Yok          | SAP HANA yüklemesinin SAP sistem tanımlayıcısı (SID). Tüm düğümler için aynı olmalıdır.                                                                                                                                                                                                                                                                                                                                                                                       |
| Örneksayısı            | Yes       | yok          | 2 basamaklı SAP örnek tanımlayıcısı.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | hayır        | evet           | Küme, birincil yerel olarak yeniden başlatmak yerine ikincil örneğe geçiş yapmayı tercih ediyor mu? ("Hayır": yerel olarak yeniden başlatmayı tercih edin; "Evet": uzak siteye devralmayı tercih etme                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | hayır        | FALSE         | Birinci SAP HANA birincil, devralındıktan ve DUPLICATE_PRIMARY_TIMEOUT sonra ikincil olarak kaydedilmelidir mi? ("false": Hayır, el ile müdahale gerekli olacaktır; "true": Evet, eski birincil, kaynak Aracısı tarafından ikincil olarak kaydedilecek                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | hayır        | 7200          | Bir çift birincil durum oluşursa, birincil zaman damgaları arasında zaman farkı (saniye cinsinden). Zaman farkı zaman aralığından azsa, küme "bekleme" durumundaki bir veya her iki örneği de barındırır. Bu, yöneticiye yük devretme konusunda tepki verme şansı vermektir. Başarısız olan eski birincil süre, saat farkı geçtikten sonra kaydedilecek. Bu yeni birincili kaydedildikten sonra, sistem çoğaltması tarafından tüm verilerin üzerine yazılır. |

5.  HANA kaynağını oluşturun.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Sanal IP adresi kaynağı oluşturun.
    Küme, SAP HANA birincil örneğine ulaşmak için sanal IP adresine sahip olacaktır. Aşağıda IP 10.7.0.84/24 ile IPaddr2 kaynağı oluşturmak için örnek komut verilmiştir.
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Kısıtlama oluşturun.
    * Doğru işlem için sapetatopolojisi kaynaklarının SAPHana kaynaklarını başlatmadan önce başlatıldığından ve ayrıca sanal IP adresinin SAPHana 'nın birincil kaynağının çalıştığı düğümde mevcut olduğundan emin olunması gerekir. Bunu başarmak için aşağıdaki 2 kısıtlamaların oluşturulması gerekir.
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>SAPHana kaynağının el ile taşınmasını başka bir düğüme test etme

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP HANA tarafından kümeye göre)


SAPHana kaynağını bir düğümden diğerine taşımayı test etmek için aşağıdaki komutu kullanın. `--primary`SAPHana kaynağının dahili olarak nasıl çalıştığı için aşağıdaki komutu çalıştırırken seçeneğinin kullanılmamalıdır.
```pcs resource move SAPHana_HR2_00-primary```

Her PC kaynağı taşıma komutu çağırma sonrasında, küme, kaynağın taşınmasını sağlamak için konum kısıtlamaları oluşturur. Gelecekte otomatik yük devretmeye izin vermek için bu kısıtlamaların kaldırılması gerekir.
Bunları kaldırmak için aşağıdaki komutu kullanabilirsiniz.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* HANA 'da doğrulama olarak oturum açın.

  * indirgenen konak:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Yükseltilen konak:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Seçeneği ile `AUTOMATED_REGISTER=false` , geri ve ileri geçiş yapılamaz.

Bu seçenek false olarak ayarlandıysa, düğümü yeniden kaydetmeniz gerekir:
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

Artık birincil olan Düğüm2, ikincil ana bilgisayar işlevi görür.

İndirgenen konağın kaydını otomatik hale getirmek için bu seçeneği true olarak ayarlamayı düşünün.
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

Otomatik kaydetmeyi tercih etmeksizin müşteri senaryosuna bağlıdır. Bir reregistering, işlem ekibi için bir yük devri daha kolay olacak şekilde otomatik olarak düğüm. Ancak, her şeyin beklendiği gibi çalıştığından emin olmak için, daha önce ek testler çalıştırmak üzere düğümü el ile kaydetmek isteyebilirsiniz.
