---
title: StorSimple Linux ana bilgisayarında MPIO yapılandırma
description: StorSimple Linux (CentOS 6,6) ana bilgisayar sunucunuzda çok Pasıg/ç (MPIO) yapılandırmak için gereken adımları öğrenin.
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 2b7ddf6423db4c471ee2065635f4e3e89f7eb7b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745742"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>CentOS çalıştıran bir StorSimple ana bilgisayarında MPIO yapılandırma
Bu makalede, CentOS 6,6 ana sunucunuzda çok paiz ıO (MPIO) yapılandırmak için gereken adımlar açıklanmaktadır. Ana bilgisayar sunucusu, Iscsı başlatıcıları aracılığıyla yüksek kullanılabilirlik için Microsoft Azure StorSimple cihazınıza bağlanır. Bu, çok yollu cihazların otomatik olarak keşfedilmesi ve yalnızca StorSimple birimleri için belirli kurulumun ayrıntılarını açıklar.

Bu yordam, StorSimple 8000 serisi cihazların tüm modellerine uygulanabilir.

> [!NOTE]
> Bu yordam bir StorSimple Cloud Appliance için kullanılamaz. Daha fazla bilgi için bkz. bulut gereciniz için konak sunucularını yapılandırma.

## <a name="about-multipathing"></a>Çoklu yol hakkında
Çoklu yol özelliği, bir konak sunucusu ile depolama cihazı arasında birden fazla g/ç yolunu yapılandırmanıza olanak tanır. Bu g/ç yolları, ayrı kablolar, anahtarlar, ağ arabirimleri ve denetleyiciler içerebilen fiziksel SAN bağlantılarıdır. Çoklu yol, tüm toplanmış yollarla ilişkili yeni bir cihaz yapılandırmak için g/ç yollarını toplar.

Çoklu yol 'nın amacı iki katdır:

* **Yüksek kullanılabilirlik**: g/ç yolunun herhangi bir öğesi (örneğin, bir kablo, anahtar, ağ arabirimi veya denetleyici) başarısız olursa, alternatif bir yol sağlar.
* **Yük Dengeleme**: depolama cihazınızın yapılandırmasına bağlı olarak, g/ç yollarındaki yükleri algılayıp ve bu yükleri dinamik olarak yeniden dengeleyerek performansı iyileştirebilirler.

### <a name="about-multipathing-components"></a>Çoklu yol bileşenleri hakkında
Linux 'ta çok sayıda çekirdek bileşeni ve Kullanıcı alanı bileşenleri aşağıda tablo olarak verilmiştir.

* **Çekirdek**: Ana bileşen, reroutes g/ç tarafından kullanılan *cihaz Eşleştiricisi* ve yollar ve yol grupları için yük devretmeyi destekler.

* **Kullanıcı-alanı**: Bunlar, cihaz Eşleyici çok yollu modülü ne yapılacağını karşılaştırarak çok yollu cihazları yöneten *çok yollu araçlardır* . Araçlar şunlardan oluşur:
   
   * **Çok yollu**: çok yollu cihazları listeler ve yapılandırır.
   * **Multipathd**: çok yollu yürüten ve yolları izleyen Daemon.
   * **Devmap-adı**: devmaps için udev 'e anlamlı bir cihaz adı sağlar.
   * **Kpartx**: çok yollu haritalar bölümlenebilir hale getirmek için doğrusal devmaps 'ı cihaz bölümlerine eşler.
   * **Multipath. conf**: yerleşik yapılandırma tablosunun üzerine yazmak için kullanılan çok yollu Daemon için yapılandırma dosyası.

### <a name="about-the-multipathconf-configuration-file"></a>Multipath. conf yapılandırma dosyası hakkında
Yapılandırma dosyası çok `/etc/multipath.conf` sayıda çoklu yol özelliğinin Kullanıcı tarafından yapılandırılabilir olmasını sağlar. `multipath`Komut ve çekirdek Daemon `multipathd` Bu dosyada bulunan bilgileri kullanır. Dosya yalnızca çok yollu cihazların yapılandırması sırasında gerçekleştirilir. Komutu çalıştırmadan önce tüm değişikliklerin yapıldığından emin olun `multipath` . Daha sonra dosyayı değiştirirseniz, değişikliklerin etkili olması için multipathd 'yi durdurmanız ve yeniden başlatmanız gerekecektir.

Çok yollu. conf beş bölümden oluşur:

- **Sistem düzeyi Varsayılanları** *(Varsayılanlar)*: sistem düzeyi varsayılanlarını geçersiz kılabilirsiniz.
- **Kara listelenen cihazlar** *(kara liste)*: cihaz Eşleyici tarafından denetlenmemelidir cihazların listesini belirtebilirsiniz.
- **Kara liste özel durumları** *(blacklist_exceptions)*: engelleme listesinde listelense bile, belirli cihazların çok yollu cihazlar olarak değerlendirileceğini belirleyebilirsiniz.
- **Depolama denetleyicisine özgü ayarlar** *(cihazlar)*: satıcı ve ürün bilgilerine sahip cihazlara uygulanacak yapılandırma ayarlarını belirtebilirsiniz.
- **Cihaza özgü ayarlar** *(multipaths)*: Bu bölümü, ayrı LUN 'lara yönelik yapılandırma ayarlarını ince ayar yapmak için kullanabilirsiniz.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Linux ana bilgisayarına bağlı StorSimple üzerinde çoklu paşeyi yapılandırma
Linux ana bilgisayarına bağlı bir StorSimple cihazı, yüksek kullanılabilirlik ve yük dengeleme için yapılandırılabilir. Örneğin, Linux ana bilgisayarının SAN 'a bağlı iki arabirimi varsa ve cihazın SAN 'a bağlı iki arabirimi varsa, bu arabirimlerin aynı alt ağ üzerinde olması için 4 yol kullanılabilir olacaktır. Ancak, cihazdaki ve ana bilgisayar arabirimindeki her bir VERI arabirimi farklı bir IP alt ağdaysa (ve yönlendirilebilir), yalnızca 2 yol kullanılabilir olur. Çoklu paizleri, tüm kullanılabilir yolları otomatik olarak bulabilir, bu yollar için bir yük dengeleme algoritması seçebilir, StorSimple birimleri için belirli yapılandırma ayarlarını uygulayabilir ve ardından çok sayıda oluşturmayı etkinleştirebilir ve doğrulayacaksınız.

Aşağıdaki yordamda, iki ağ arabirimi olan bir StorSimple cihazı iki ağ arabirimi içeren bir konağa bağlı olduğunda çoklu pasıların nasıl yapılandırılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar
Bu bölümde, CentOS sunucusu ve StorSimple cihazınız için yapılandırma önkoşulları ayrıntılı olarak yapılır.

### <a name="on-centos-host"></a>CentOS ana bilgisayarında
1. CentOS ana bilgisayarınızda 2 ağ arabiriminin etkinleştirildiğinden emin olun. Şunu yazın:
   
    `ifconfig`
   
    Aşağıdaki örnek, konakta iki ağ arabirimi ( `eth0` ve `eth1` ) varsa çıktıyı gösterir.
   
    ```output
    [root@centosSS ~]# ifconfig
    eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
        inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
        TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)

    eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
        inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
        TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)

    loLink encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:12 errors:0 dropped:0 overruns:0 frame:0
        TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:0
        RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
    ```
1. CentOS sunucunuza *iSCSI-INITIATOR-utils* ' i yükler. *İSCSI-INITIATOR-utils* yüklemek için aşağıdaki adımları gerçekleştirin.
   
   1. `root`CentOS ana bilgisayarınızda olarak oturum açın.
   1. *İSCSI başlatıcısı-yardımcı programları*'nı yükler. Şunu yazın:
      
       `yum install iscsi-initiator-utils`
   1. *Iscsı Başlatıcısı 'nın yardımcı programları* başarıyla yüklendikten sonra iSCSI hizmetini başlatın. Şunu yazın:
      
       `service iscsid start`
      
       Durumlarda, `iscsid` gerçekten başlayamayabilir ve `--force` seçenek gerekli olabilir
   1. Önyükleme sırasında Iscsı başlatıcılarınızın etkinleştirildiğinden emin olmak için, `chkconfig` hizmetini etkinleştirmek üzere komutunu kullanın.
      
       `chkconfig iscsi on`
   1. Doğru şekilde ayarlandığından emin olmak için şu komutu çalıştırın:
      
       `chkconfig --list | grep iscsi`
      
       Örnek çıktı aşağıda gösterilmiştir.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       Yukarıdaki örnekte, Iscsı ortamınızın 2, 3, 4 ve 5 çalıştırma düzeylerinde önyükleme zamanında çalışacağını görebilirsiniz.
1. *Cihaz-Eşleyici-çok yollu*'yi yükler. Şunu yazın:
   
    `yum install device-mapper-multipath`
   
    Yükleme başlatılır. Onay sorulduğunda devam etmek için **Y** yazın.

### <a name="on-storsimple-device"></a>StorSimple cihazında
StorSimple cihazınız şunları içermelidir:

* Iscsı için etkinleştirilen en az iki arabirim. StorSimple cihazınızda iki arabirimin Iscsı özellikli olduğunu doğrulamak için, StorSimple cihazınız için klasik Azure portalında aşağıdaki adımları gerçekleştirin:
  
  1. StorSimple cihazınız için klasik portalda oturum açın.
  1. StorSimple Manager hizmetinizi seçin, **cihazlar** ' a tıklayın ve belirli StorSimple cihazını seçin. **Yapılandır** ' a tıklayın ve ağ arabirimi ayarlarını doğrulayın. İki Iscsı özellikli ağ arabirimine sahip bir ekran görüntüsü aşağıda gösterilmiştir. Burada DATA 2 ve DATA 3, Iscsı için her iki 10 GbE arabirimi de etkinleştirilir.
     
      ![MPIO StorsSimple VERI 2 yapılandırması](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple VERI 3 yapılandırması](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      **Yapılandır** sayfasında
     
     1. Her iki ağ arabiriminin de Iscsı etkin olduğundan emin olun. **İSCSI etkin** alanı **Evet** olarak ayarlanmalıdır.
     1. Ağ arabirimlerinin aynı hıza sahip olduğundan emin olun, her ikisi de 1 GbE veya 10 GbE olmalıdır.
     1. Iscsı özellikli arabirimlerin IPv4 adreslerini ve daha sonra konakta kullanılmak üzere Kaydet ' i aklınızda saklayın.
* StorSimple cihazınızdaki Iscsı arabirimlerine CentOS sunucusundan ulaşılabilir olmalıdır.
      Bunu doğrulamak için, ana sunucunuzda StorSimple Iscsı özellikli ağ arabirimlerinizin IP adreslerini sağlamanız gerekir. Kullanılan komutlar ve VERI2 (10.126.162.25) ve DATA3 (10.126.162.26) ile ilgili çıktı aşağıda gösterilmiştir:
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Donanım yapılandırması
İki Iscsı ağ arabirimini yedekliliğe yönelik ayrı yollarda bağlanmanızı öneririz. Aşağıdaki şekilde, CentOS sunucunuz ve StorSimple cihazınız için yüksek kullanılabilirlik ve Yük Dengeleme çok sayıda çoklu yol için önerilen donanım yapılandırması gösterilmektedir.

![StorSimple için Linux ana bilgisayarına yönelik MPIO donanım yapılandırması](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Önceki şekilde gösterildiği gibi:

* StorSimple cihazınız, iki denetleyiciyle etkin-Pasif bir yapılandırmadır.
* Cihaz denetleyicilerinize iki SAN anahtarı bağlı.
* StorSimple cihazınızda iki Iscsı Başlatıcısı etkindir.
* CentOS ana bilgisayarınızda iki ağ arabirimi etkinleştirilmiştir.

Yukarıdaki yapılandırma, konak ve veri arabirimleri yönlendirilebilir ise, cihazınız ile ana bilgisayar arasında 4 ayrı yol görür.

> [!IMPORTANT]
> * Çoklu yol için 1 GbE ve 10 GbE ağ arabirimleri karıştırabilmeniz önerilir. İki ağ arabirimi kullanılırken, her iki arabirimin de aynı türde olması gerekir.
> * StorSimple cihazınızda, DATA0, VERI1, DATA4 ve DATA5 1 GbE arabirimlerdir, VERI2 ve DATA3 ise 10 GbE ağ arabirimlerdir. |
> 
> 

## <a name="configuration-steps"></a>Yapılandırma adımları
Çoklu yol için yapılandırma adımları otomatik bulma için kullanılabilir yolları yapılandırmak, kullanılacak yük dengeleme algoritmasını belirlemek, çok paşeyi ve son olarak yapılandırmayı doğrulamak için kullanılır. Bu adımların her biri, aşağıdaki bölümlerde ayrıntılı olarak ele alınmıştır.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>1. Adım: otomatik bulma için çoklu paşeyi yapılandırma
Çok yollu desteklenen cihazlar otomatik olarak keşfedilebilir ve yapılandırılabilir.

1. `/etc/multipath.conf`Dosyayı başlatın. Şunu yazın:
   
     `mpathconf --enable`
   
    Yukarıdaki komut bir `sample/etc/multipath.conf` Dosya oluşturacak.
1. Çok yollu hizmeti başlatın. Şunu yazın:
   
    `service multipathd start`
   
    Şu çıkışı görürsünüz:
   
    `Starting multipathd daemon:`
1. Çoklu yolların otomatik olarak bulunmasını etkinleştirin. Şunu yazın:
   
    `mpathconf --find_multipaths y`
   
    Bu, aşağıda gösterildiği gibi verilerinizin varsayılanlar bölümünü değiştirecek `multipath.conf` :
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>2. Adım: StorSimple birimlerine yönelik çoklu paşeyi yapılandırma
Varsayılan olarak, tüm cihazlar çok yollu. conf dosyasında listelenir ve atlanır. StorSimple cihazlarından birimlerde çok sayıda duruma izin vermek için engelleme listesi özel durumları oluşturmanız gerekir.

1. `/etc/mulitpath.conf` dosyasını düzenleyin. Şunu yazın:
   
    `vi /etc/multipath.conf`
1. Çok yollu. conf dosyasındaki blacklist_exceptions bölümünü bulun. StorSimple cihazınızın bu bölümde bir blok listesi özel durumu olarak listelenmesi gerekir. Bu dosyadaki ilgili satırların açıklamasını aşağıda gösterildiği gibi değiştirebilirsiniz (yalnızca kullandığınız cihazın özel modelini kullanın):
   
    ```config
    blacklist_exceptions {
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8100*"
        }
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8600*"
        }
    }
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>3. Adım: hepsini bir kez deneme çoklu paşeyi yapılandırma
Bu yük dengeleme algoritması, etkin denetleyiciye yönelik kullanılabilir çoklu yolları dengeli ve hepsini bir kez deneme biçiminde kullanır.

1. `/etc/multipath.conf` dosyasını düzenleyin. Şunu yazın:
   
    `vi /etc/multipath.conf`
1. Bölümünün altında, `defaults` öğesini olarak ayarlayın `path_grouping_policy` `multibus` . `path_grouping_policy`Belirtilmemiş multipaths için uygulanacak varsayılan yol gruplama ilkesini belirtir. Varsayılanlar bölümü aşağıda gösterildiği gibi görünecektir.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> En yaygın değerleri `path_grouping_policy` şunlardır:
> 
> * Yük devretme = öncelik grubu başına 1 yol
> * multibus = 1 öncelik grubundaki tüm geçerli yollar
> 
> 

### <a name="step-4-enable-multipathing"></a>4. Adım: çoklu paşeyi etkinleştirme
1. `multipathd`Arka plan programını yeniden başlatın. Şunu yazın:
   
    `service multipathd restart`
1. Çıktı aşağıda gösterildiği gibi olacaktır:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>5. Adım: multipaşeyi doğrulama
1. İlk olarak, aşağıdaki şekilde StorSimple cihazından Iscsı bağlantısının kurulu olduğundan emin olun:
   
   a. StorSimple cihazınızı bulun. Şunu yazın:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    DATA0 için IP adresi 10.126.162.25 ve 3260 numaralı bağlantı noktası, giden Iscsı trafiği için StorSimple cihazında aşağıda gösterildiği gibidir:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    StorSimple cihazınızın ıQN 'sini `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` önceki çıktıdan kopyalayın.

   b. Hedef ıQN 'yi kullanarak cihaza bağlanın. StorSimple cihazı, Iscsı hedefidir. Şunu yazın:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    Aşağıdaki örnek, hedef ıQN 'si olan çıktıyı gösterir `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . Çıktı, cihazınızdaki iki Iscsı özellikli ağ arabirimine başarıyla bağlandığınızı gösterir.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Burada yalnızca bir ana bilgisayar arabirimi ve iki yol görürseniz, her iki arabirimi de Iscsı için konak üzerinde etkinleştirmeniz gerekir. [Linux belgelerindeki ayrıntılı yönergeleri](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)izleyebilirsiniz.

1. Bir birim, StorSimple cihazından CentOS sunucusuna sunulur. Daha fazla bilgi için bkz. [6. Adım:](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) storsimple cihazınızda Azure Portal Ile birim oluşturma.

1. Kullanılabilir yolları doğrulayın. Şunu yazın:

    `multipath -l`

      Aşağıdaki örnek, iki ağ arabirimi için bir StorSimple cihazında, kullanılabilir iki yol ile tek bir konak ağ arabirimine bağlı olan çıktıyı gösterir.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    Aşağıdaki örnek, dört adet kullanılabilir yol ile iki konak ağı arabirimine bağlı olan bir StorSimple cihazında iki ağ arabirimi için çıktıyı gösterir.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    Yollar yapılandırıldıktan sonra, bu birimi bağlamak ve biçimlendirmek için ana bilgisayar işletim Sisteminizdeki (CentOS 6,6) belirli yönergelere bakın.

## <a name="troubleshoot-multipathing"></a>Çoklu yol sorunlarını giderme
Bu bölümde, çok sayıda yapılandırma sırasında herhangi bir sorunla karşılaşırsanız bazı yararlı ipuçları sunulmaktadır.

S. Dosya etkin olan değişiklikleri görmüyorum `multipath.conf` .

A. Dosyada herhangi bir değişiklik yaptıysanız `multipath.conf` , çoklu yol hizmetini yeniden başlatmanız gerekir. Aşağıdaki komutu yazın:

`service multipathd restart`

S. StorSimple cihazında iki ağ arabirimini ve konaktaki iki ağ arabirimini etkinleştirdim. Kullanılabilir yolları listediğimde yalnızca iki yol görüyorum. Dört kullanılabilir yol görmem bekleniyor.

A. İki yolun aynı alt ağda olduğundan ve yönlendirilebilir olduğundan emin olun. Ağ arabirimleri farklı VLAN 'larda ve yönlendirilebilir değilse, yalnızca iki yol görürsünüz. Bunu doğrulamak için bir yol, StorSimple cihazında bir ağ arabiriminden her iki konak arabirimine de ulaşabildiğinizden emin olmak. Bu doğrulama yalnızca bir destek oturumu aracılığıyla yapılabileceği için [Microsoft desteği başvurmanız](storsimple-8000-contact-microsoft-support.md) gerekecektir.

S. Kullanılabilir yolları listediğimde hiçbir çıkış görmüyorum.

A. Genellikle, çok yönlü bir yol görmemek, çok pastaya arka plan programı ile ilgili bir sorun önerir ve bu, burada dosyanın içinde yer aldığı bir sorun olabilir `multipath.conf` .

Aynı zamanda, hedefe bağlandıktan sonra bazı diskleri görebileceğinizi de unutmayın. çok yollu listelerden yanıt yoksa herhangi bir diskiniz olmadığı anlamına gelir.

* SCSI veri yolunu yeniden taramak için aşağıdaki komutu kullanın:
  
    `$ rescan-scsi-bus.sh` (sg3_utils paketinin parçası)
* Aşağıdaki komutları yazın:
  
    `$ dmesg | grep sd*`
     
     Veya
  
    `$ fdisk -l`
  
    Bunlar, son eklenen disklerin ayrıntılarını döndürür.
* StorSimple diski olup olmadığını anlamak için aşağıdaki komutları kullanın:
  
    `cat /sys/block/<DISK>/device/model`
  
    Bu, bir StorSimple diski olup olmadığını belirleyen bir dize döndürür.

Olası bir neden büyük olasılıkla eski bir nedeni de olumsuz SID PID olabilir. Iscsı oturumlarından oturumu kapatmak için aşağıdaki komutu kullanın:

`iscsiadm -m node --logout -p <Target_IP>`

Bu komutu, StorSimple cihazınız olan Iscsı hedefinde bulunan tüm bağlı ağ arabirimleri için tekrarlayın. Tüm Iscsı oturumlarından oturumu kapattıktan sonra iSCSI oturumunu yeniden kurmak için Iscsı hedefi ıQN 'sini kullanın. Aşağıdaki komutu yazın:

`iscsiadm -m node --login -T <TARGET_IQN>`


S. Cihazımın izin verildiğinden emin değilim.

A. Cihazınızın izin verilip verilmeyeceğini doğrulamak için, aşağıdaki sorun giderme etkileşimli komutunu kullanın:

```console
multipathd -k
multipathd> show devices
available block devices:
ram0 devnode blacklisted, unmonitored
ram1 devnode blacklisted, unmonitored
ram2 devnode blacklisted, unmonitored
ram3 devnode blacklisted, unmonitored
ram4 devnode blacklisted, unmonitored
ram5 devnode blacklisted, unmonitored
ram6 devnode blacklisted, unmonitored
ram7 devnode blacklisted, unmonitored
ram8 devnode blacklisted, unmonitored
ram9 devnode blacklisted, unmonitored
ram10 devnode blacklisted, unmonitored
ram11 devnode blacklisted, unmonitored
ram12 devnode blacklisted, unmonitored
ram13 devnode blacklisted, unmonitored
ram14 devnode blacklisted, unmonitored
ram15 devnode blacklisted, unmonitored
loop0 devnode blacklisted, unmonitored
loop1 devnode blacklisted, unmonitored
loop2 devnode blacklisted, unmonitored
loop3 devnode blacklisted, unmonitored
loop4 devnode blacklisted, unmonitored
loop5 devnode blacklisted, unmonitored
loop6 devnode blacklisted, unmonitored
loop7 devnode blacklisted, unmonitored
sr0 devnode blacklisted, unmonitored
sda devnode whitelisted, monitored
dm-0 devnode blacklisted, unmonitored
dm-1 devnode blacklisted, unmonitored
dm-2 devnode blacklisted, unmonitored
sdb devnode whitelisted, monitored
sdc devnode whitelisted, monitored
dm-3 devnode blacklisted, unmonitored
```


Daha fazla bilgi için, [çok sayıda sorun giderme](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)bölümüne gidin.

## <a name="list-of-useful-commands"></a>Faydalı komutların listesi
| Tür | Komut | Açıklama |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Iscsı hizmetini Başlat |
| &nbsp; |`service iscsid stop` |Iscsı hizmetini durdur |
| &nbsp; |`service iscsid restart` |Iscsı hizmetini yeniden Başlat |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Belirtilen adresteki kullanılabilir hedefleri bul |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Iscsı hedefinde oturum açma |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Iscsı hedefinden oturumu Kapat |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Iscsı Başlatıcısı adını yazdır |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Konakta bulunan Iscsı oturumunun ve birimin durumunu denetleme |
| &nbsp; |`iscsi -m session` |Ana bilgisayar ve StorSimple cihazı arasında belirlenen tüm Iscsı oturumlarını gösterir |
|  | | |
| **Çoklu yol oluşturma** |`service multipathd start` |Çok yollu Daemon Başlat |
| &nbsp; |`service multipathd stop` |Çok yollu cini durdur |
| &nbsp; |`service multipathd restart` |Çok yollu Daemon 'ı yeniden Başlat |
| &nbsp; |`chkconfig multipathd on` </br> VEYA </br> `mpathconf -with_chkconfig y` |Çok yollu arka plan programı 'nı önyükleme sırasında başlatılacak şekilde etkinleştir |
| &nbsp; |`multipathd -k` |Sorun giderme için etkileşimli konsolu Başlat |
| &nbsp; |`multipath -l` |Çok yollu bağlantıları ve cihazları listeleme |
| &nbsp; |`mpathconf --enable` |İçinde örnek mulitpath. conf dosyası oluşturma `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Sonraki adımlar
Linux ana bilgisayarında MPIO yapılandırırken, aşağıdaki CentoS 6,6 belgelerine de başvurmanız gerekebilir:

* [CentOS 'da MPIO 'YU ayarlama](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux Eğitim Kılavuzu](http://linux-training.be/linuxsys.pdf)
