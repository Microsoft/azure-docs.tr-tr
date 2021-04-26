---
title: Azure 'da SAP HANA için STONITH ile yüksek kullanılabilirlik ayarı (büyük örnekler) | Microsoft Docs
description: SUSE 'de STONITH kullanarak Azure 'da SAP HANA için yüksek kullanılabilirlik (büyük örnekler) oluşturun
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3dd2a618f22036fd0826a99207d83a3add390c7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645317"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>STONITH kullanarak SUSE’de yüksek kullanılabilirlik ayarlama
Bu belgede, SUSE Işletim sisteminde STONITH cihazını kullanarak yüksek kullanılabilirliği ayarlamak için ayrıntılı adım adım yönergeler sağlanmaktadır.

**Vazgeçme:** *Bu kılavuz, kurulumun BAŞARıYLA çalıştığı Microsoft Hana büyük örnekler ortamında test edilirken türetilir. HANA büyük örnekleri için Microsoft hizmet yönetimi ekibi, Işletim sistemini desteklemediğinden, daha fazla sorun giderme veya işletim sistemi katmanında açıklama için SUSE 'e başvurmanız gerekebilir. Microsoft hizmet yönetimi ekibi, STONITH cihazını ayarlayabilir ve tamamen destekler ve bu cihaz sorunlarını gidermek için sorun gidermeye dahil edilebilir.*
## <a name="overview"></a>Genel Bakış
SUSE kümeleme kullanarak yüksek kullanılabilirliği ayarlamak için aşağıdaki önkoşulların karşılanması gerekir.
### <a name="pre-requisites"></a>Ön koşullar
- HANA büyük örnekleri sağlandı
- İşletim sistemi kayıtlı
- HANA büyük örnek sunucuları, yamaları/paketleri almak için SMT sunucusuna bağlanır
- İşletim sisteminde en son düzeltme eklerinin yüklü olduğu
- NTP (saat sunucusu) ayarlandı
- HA kurulumu üzerinde SUSE belgelerinin en son sürümünü okuyun ve anlayın

### <a name="setup-details"></a>Kurulum Ayrıntıları
Bu kılavuzda aşağıdaki kurulum kullanılmaktadır:
- İşletim sistemi: SAP için SLES 12 SP1
- HANA büyük örnekler: 2xS192 (dört yuva, 2 TB)
- HANA sürümü: HANA 2,0 SP1
- Sunucu adları: sapprdhdb95 (Düğüm1) ve sapprdhdb96 (Düğüm2)
- STONITH cihazı: Iscsı tabanlı STONITH cihazı
- HANA büyük örnek düğümlerinden birinde NTP ayarlama

HSR ile HANA büyük örneklerini ayarlarken, Microsoft hizmet yönetimi ekibini STONITH 'yi ayarlamak için isteyebilirsiniz. Zaten sağlanan HANA büyük örneklerine sahip olan mevcut bir müşteriniz varsa ve mevcut dikey pencereler için STONITH cihazı ayarlandıysa, hizmet isteği formunda (SRF) Microsoft hizmet yönetimi ekibine aşağıdaki bilgileri sağlamanız gerekir. SRF formunu Technical Account Manager aracılığıyla veya HANA büyük örnek ekleme için Microsoft kişinizde isteyebilirsiniz. Yeni müşteriler, sağlama sırasında STONITH cihazı talep edebilir. Girişler, sağlama isteği formunda kullanılabilir.

- Sunucu adı ve sunucu IP adresi (örneğin, myhanaserver1, 10.35.0.1)
- Konum (örneğin, ABD Doğu)
- Müşteri adı (örneğin, Microsoft)
- SID-HANA sistem tanımlayıcısı (örneğin, H11)

STONITH aygıtı yapılandırıldıktan sonra, Microsoft hizmet yönetimi ekibi, STONITH kurulumunu yapılandırmak için kullanabileceğiniz, Iscsı depolamanın SBD cihaz adını ve IP adresini sağlar. 

STONITH kullanarak uçtan uca HA ayarlamak için aşağıdaki adımların izlenmesi gerekir:

1.  SBD cihazını tanımla
2.  SBD cihazını başlatma
3.  Kümeyi yapılandırma
4.  Softköpek Izleyicisi 'ni ayarlama
5.  Düğümü kümeye ekleyin
6.  Kümeyi doğrulama
7.  Kaynakları kümeye yapılandırma
8.  Yük devretme işlemini test etme

## <a name="1---identify-the-sbd-device"></a>1. SBD cihazını tanımla
Bu bölümde, Microsoft hizmet yönetimi ekibi, STONITH yapılandırıldıktan sonra kurulum için SBD cihazının nasıl belirleneceği açıklanmaktadır. **Bu bölüm yalnızca mevcut müşteri için geçerlidir**. Yeni bir müşteriyseniz, Microsoft hizmet yönetimi ekibi sizin için SBD cihaz adı sağlar ve bu bölümü atlayabilirsiniz.

1,1 */etc/Île/ınitiatorname.exe* değiştirme 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft hizmet yönetimi bu dizeyi sağlar. Her **iki** düğümdeki dosyayı da değiştirin, ancak düğüm numarası her düğümde farklı olur.

![Ekran görüntüsü bir düğüm için ınitiatorname değerlerini içeren bir ınitiatorname dosyası gösterir.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 */etc/IDL/SCC SID.conf* değiştirme: set *Node.Session.Timeo.replacement_timeout = 5* ve *Node. Startup = Automatic*. **Her iki** düğümdeki dosyayı değiştirin.

1,3 bulma komutunu yürütür, dört oturum gösterir. Her iki düğümde de çalıştırın.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![Ekran görüntüsü, iscıadm bulma komutunun sonuçlarını içeren bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 Iscsı cihazında oturum açmak için komutu yürütün ve dört oturumu gösterir. **Her iki düğümde de** çalıştırın.

```
iscsiadm -m node -l
```
![Ekran görüntüsü, IBir bir konsol penceresini, IBU bir](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 rescan betiği Yürüt: *rescan-scsi-Bus.sh*.  Bu betik, sizin için oluşturulan yeni diskleri gösterir.  Her iki düğümde de çalıştırın. Sıfırdan büyük bir LUN numarası görmeniz gerekir (örneğin: 1, 2 vb.)

```
rescan-scsi-bus.sh
```
![Ekran görüntüsü, komut dosyası sonuçlarının bulunduğu bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 cihaz adını almak için *fdisk – l* komutunu çalıştırın. Her iki düğümde de çalıştırın. **178 MiB** boyutunda bir cihaz seçin.

```
  fdisk –l
```

![Ekran görüntüsü f disk komutunun sonuçlarını içeren bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. SBD cihazını başlatın

2,1 **her iki düğümde de** SBD cihazını başlatın

```
sbd -d <SBD Device Name> create
```
![Ekran görüntüsü s b d oluştur komutunun sonucuna sahip bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 cihaza yazılan öğeleri denetleyin. **Her iki düğümde de** yap

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. kümeyi yapılandırma
Bu bölümde, SUSE HA kümesini ayarlama adımları açıklanmaktadır.
### <a name="31-package-installation"></a>3,1 paket yüklemesi
3.1.1 lütfen ha_sles ve SAPHanaSR-doc desenlerinin yüklendiğini kontrol edin. Yüklü değilse, onları yükleme. **Her iki düğüme de** yükler.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![Ekran görüntüsü, bir konsol penceresini gösterir ve bu da, örüntüsünün sonucunu gösterir. ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![ Ekran görüntüsü, SAPHanaSR-doc komutunun sonucuna sahip bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 kümeyi ayarlama
3.2.1, *ha-Cluster-init* komutunu kullanabilir ya da kümeyi ayarlamak için YaST2 sihirbazını kullanabilirsiniz. Bu durumda, YaST2 Sihirbazı kullanılır. Bu adımı **yalnızca birincil düğümde** gerçekleştirirsiniz.

YaST2> yüksek kullanılabilirliği > küme ![ ekran görüntüsü, yüksek kullanılabilirlik ve küme seçiliyken YaST denetim merkezini gösterir. ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![ Ekran görüntüsünde, Install ve Cancel seçeneklerinin bulunduğu bir iletişim kutusu gösterilir.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Halk2 paketi zaten yüklü olduğundan **iptal** ' e tıklayın.

![Ekran görüntüsü iptal seçeneğiniz hakkında bir ileti gösterir.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

**Devam** ' a tıklayın

Beklenen değer = dağıtılan düğümlerin sayısı (Bu durumda 2) ![ ekran görüntüsünde güvenlik kimlik doğrulamasını etkinleştir onay kutusu Ile küme güvenliği gösterilmektedir.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
**İleri** ekran görüntüsü ' ne tıklayın 
 ![ , eşitleme konağı ve eşitleme dosya listeleriyle küme yapılandırma penceresini gösterir.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Düğüm adları ekleyin ve ardından "önerilen dosyaları Ekle" ye tıklayın

"Csync2 aç" düğmesine tıklayın

"Önceden paylaşılan anahtarlar oluştur" düğmesine tıklayın, açılan pencerenin altında görüntülenir

![Ekran görüntüsünde, anahtarınızın oluşturulduğunu belirten bir ileti gösterilir.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

**Tamam 'a** tıklayın

Kimlik doğrulaması, Csync2 ' deki IP adresleri ve önceden paylaşılan anahtarlar kullanılarak gerçekleştirilir. Anahtar dosyası csync2-k/etc/csync2/key_hagroup ile oluşturulur. Dosya key_hagroup oluşturulduktan sonra kümenin tüm üyelerine el ile kopyalanmalıdır. **Dosyayı düğüm 1 ' den Düğüm2 ' e kopyalamanız emin olun**.

![Ekran görüntüsü, anahtarı kümenin tüm üyelerine kopyalamak için gereken seçenekleri içeren bir küme yapılandırma iletişim kutusunu gösterir.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

**Sonraki** 
 ![ ekran görüntüsü, küme hizmeti penceresini gösterir.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Varsayılan seçenekte önyükleme devre dışı bırakıldı, bu nedenle önyükleme sırasında paceyapıcısı başlatılmalıdır. Kurulum gereksinimlerinize göre seçim yapabilirsiniz.
**İleri** ' ye tıklayın ve küme yapılandırması tamamlanmıştır.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Softköpek Izleyicisi 'ni ayarlama
Bu bölüm, izleme (softköpek) yapılandırmasını açıklar.

4,1 **her iki** düğüm üzerinde */etc/Init.exe* için aşağıdaki satırı ekleyin.
```
modprobe softdog
```
![Ekran görüntüsü, softköpek hattı eklenen bir önyükleme dosyası gösterir.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 **her iki** düğümde da */Etc/sysconfig/SBD* dosyasını aşağıdaki gibi güncelleştirin:
```
SBD_DEVICE="<SBD Device Name>"
```
![Ekran görüntüsü S b D_DEVICE değeri eklenen s b dosyasını gösterir.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 aşağıdaki komutu çalıştırarak **her iki düğümde de** çekirdek modülünü yükleyin
```
modprobe softdog
```
![Ekran görüntüsü, konsol penceresinin bir parçasını modaraştırma softköpek ile gösterir.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 ve **her iki düğümde de** aşağıdaki gibi softköpek 'nın çalıştığından emin olun:
```
lsmod | grep dog
```
![Ekran görüntüsü, bir konsol penceresinin bir parçasını, l s mod komutunun çalıştırılması sonucuyla gösterir.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 **her iki düğümde de** SBD cihazını Başlat
```
/usr/share/sbd/sbd.sh start
```
![Ekran görüntüsü, başlangıç komutuyla bir konsol penceresinin bir parçasını gösterir.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 **her iki düğümde de** SBD arka plan programını test edin. İki girişi **her iki** düğümde yapılandırdıktan sonra görürsünüz
```
sbd -d <SBD Device Name> list
```
![Ekran görüntüsü, iki girişi görüntüleyen bir konsol penceresinin bir parçasını gösterir.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 düğümlerinden **birine** bir test iletisi gönderin
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Ekran görüntüsü, iki girişi görüntüleyen bir konsol penceresinin bir parçasını gösterir.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

**ikinci** düğüm üzerinde 4,8 (Düğüm2) ileti durumunu kontrol edebilirsiniz
```
sbd  -d <SBD Device Name> list
```
![Ekran görüntüsü, diğer üye için bir test değeri görüntüleyen üyelerden birine sahip bir konsol penceresinin bir parçasını gösterir.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 SBD yapılandırmasını benimsemek için, */Etc/sysconfig/SBD* dosyasını aşağıdaki gibi güncelleştirin. **Her iki** düğümdeki dosyayı güncelleştirme
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 **birincil düğümde** pacemaker hizmetini başlatın (Düğüm1)
```
systemctl start pacemaker
```
![Ekran görüntüsü, pacemaker 'ı başlattıktan sonra durumu gösteren bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Paceoluşturucu hizmeti *başarısız olursa*, *Senaryo 5: paceoluşturucu hizmeti başarısız oldu* bölümüne başvurun

## <a name="5---joining-the-cluster"></a>5. kümeye katılma
Bu bölümde, düğümü kümeye nasıl katılabileceğinizi anlatmaktadır.

### <a name="51-add-the-node"></a>5,1 düğümü ekleme
Düğüm2 kümeye katılmasına izin vermek için **Düğüm2** üzerinde aşağıdaki komutu çalıştırın.
```
ha-cluster-join
```
Kümeye katılırken bir *hata* alırsanız, bkz. *Senaryo 6: düğüm 2 kümeye katılamadı*.

## <a name="6---validating-the-cluster"></a>6. küme doğrulanıyor

### <a name="61-start-the-cluster-service"></a>6,1 küme hizmetini başlatma
**Her iki** düğümdeki kümeyi ilk kez denetlemek ve isteğe bağlı olarak başlatmak için.
```
systemctl status pacemaker
systemctl start pacemaker
```
![Ekran görüntüsü, pacemaker durumuna sahip bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 durumu izleme
**Her iki düğümün de** çevrimiçi olduğundan emin olmak için *crm_mon* komutunu çalıştırın. Bunu, kümenin **tüm düğümlerinde** çalıştırabilirsiniz
```
crm_mon
```
![Ekran görüntüsünde, c r m_mon sonuçlarının bulunduğu bir konsol penceresi gösterilmektedir.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
Ayrıca, *https:// \<node IP> : 7630* küme durumunu denetlemek için havk 'da da oturum açabilirsiniz. Varsayılan Kullanıcı hacluster ve parola Linux olur. Gerekirse, *passwd* komutunu kullanarak parolayı değiştirebilirsiniz.

## <a name="7-configure-cluster-properties-and-resources"></a>7. küme özelliklerini ve kaynaklarını yapılandırma 
Bu bölümde, küme kaynaklarını yapılandırma adımları açıklanmaktadır.
Bu örnekte, aşağıdaki kaynağı ayarlayın, SUSE HA kılavuzuna başvurarak Rest (gerekirse) yapılandırılabilir. Yapılandırmayı yalnızca **düğümlerden birinde** gerçekleştirin. Birincil düğüm üzerinde yapın.

- Küme önyüklemesi
- STONITH cihazı
- Sanal IP adresi


### <a name="71-cluster-bootstrap-and-more"></a>7,1 kümesi önyükleme ve daha fazlası
Küme önyüklemesi Ekle. Dosyayı oluşturun ve metni şu şekilde ekleyin:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Yapılandırmayı kümeye ekleyin.
```
crm configure load update crm-bs.txt
```
![Ekran görüntüsü, c r m komutunu çalıştıran bir konsol penceresinin bir parçasını gösterir.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 STONITH cihazı
Kaynak STONITH 'yi ekleyin. Dosyayı oluşturun ve metni aşağıdaki şekilde ekleyin.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Yapılandırmayı kümeye ekleyin.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 sanal IP adresi
Kaynak sanal IP 'si ekleyin. Dosyayı oluşturun ve metni aşağıda gösterildiği gibi ekleyin.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Yapılandırmayı kümeye ekleyin.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 kaynakları doğrulama

Komut *crm_mon* çalıştırdığınızda, iki kaynağı orada görebilirsiniz.
![Ekran görüntüsü iki kaynakla bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ayrıca, *https:// \<node IP address> : 7630/CIB/Live/State* konumundaki durumu görebilirsiniz.

![Ekran görüntüsü iki kaynağın durumunu gösterir.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. yük devretme işlemini test etme
Yük devretme sürecini test etmek için Düğüm1 üzerindeki paceoluşturucu hizmetini durdurun ve kaynakları Düğüm2 ' ye devreder.
```
Service pacemaker stop
```
Şimdi, **Düğüm2** üzerindeki paceyapıcısı hizmetini durdurun ve kaynakları **Düğüm1** 'e devredildi.

**Yük devretmeden önce**  
![Ekran görüntüsü, yük devretmeden önce iki kaynağın durumunu gösterir.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Yük devretmeden sonra**  
![Ekran görüntüsü, yük devretmeden sonra iki kaynağın durumunu gösterir.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![Ekran görüntüsünde, yük devretmeden sonra kaynakların durumuyla birlikte bir konsol penceresi gösterilir.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. sorun giderme
Bu bölümde, kurulum sırasında karşılaşılabilecek birkaç hata senaryosu açıklanmaktadır. Bu sorunları hissetmeyebilir.

### <a name="scenario-1-cluster-node-not-online"></a>Senaryo 1: küme düğümü çevrimiçi değil
Düğümlerden herhangi biri, Küme Yöneticisi 'nde çevrimiçi görünmüyorsa, çevrimiçi duruma getirmek için aşağıdakileri deneyebilirsiniz.

Iscsı hizmetini başlatma
```
service iscsid start
```

Artık bu Iscsı düğümünde oturum açabiliyor olmanız gerekir
```
iscsiadm -m node -l
```
Beklenen çıkış aşağıdaki gibi görünür
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Senaryo 2: YaST2 grafik görünümü göstermez
YaST2 grafik ekranı, bu belgede yüksek kullanılabilirlik kümesini ayarlamak için kullanılır. YaST2, gösterildiği gibi grafik penceresiyle birlikte açılmadığından ve QT hatası fırladıysanız, adımları aşağıdaki şekilde gerçekleştirin. Grafik penceresiyle açılırsa, adımları atlayabilirsiniz.

**Hata**

![Ekran görüntüsü bir konsol penceresinin bir parçasını hata iletisiyle gösterir.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Beklenen çıkış**

![Ekran görüntüsü, yüksek kullanılabilirlik ve küme vurgulanacak YaST denetim merkezini gösterir.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

YaST2 grafik görünümü ile açılmadığından, aşağıdaki adımları izleyin.

Gereken paketleri yükleyin. Kullanıcı "root" olarak oturum açmış olmanız ve paketleri indirmek/yüklemek için SMT ayarlamış olmanız gerekir.

Paketleri yüklemek için, yazılım yönetimi>bağımlılıklarını>yast>yazılımı kullanın. "önerilen paketleri Install..." seçeneğini>. Aşağıdaki ekran görüntüsünde beklenen ekranlar gösterilmektedir.
>[!NOTE]
>Her iki düğümden YaST2 grafik görünümüne erişebilmeniz için her iki düğümde de adımları gerçekleştirmeniz gerekir.

![Ekran görüntüsü YaST denetim merkezini görüntüleyen bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Bağımlılıklar altında "önerilen paketleri yüklensin" ![ ekran görüntüsünde, önerilen paketleri Install seçili olan bir konsol penceresi gösterilir.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Değişiklikleri gözden geçirin ve Tamam 'a basın

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Paket yükleme devam ![ ediyor ekran görüntüsü, yüklemenin ilerlemesini görüntüleyen bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

İleri’ye tıklayın

![Ekran görüntüsü başarı iletisi içeren bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Son ' a tıklayın

Ayrıca libqt4 ve libyuı-QT paketlerini de yüklemeniz gerekir.
```
zypper -n install libqt4
```
![Ekran görüntüsünde, libqt4 paketini yükleyen bir konsol penceresi gösterilmektedir.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Ekran görüntüsü, libyuı-QT paketini yükleyen bir konsol penceresini gösterir. ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![ Ekran görüntüsü, devam eden libyuı-QT paketini yükleyen bir konsol penceresini gösterir.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
YaST2, burada gösterildiği gibi grafik görünümünü hemen açabiliyor olmalıdır.
![Ekran görüntüsü, yazılım ve çevrimiçi güncelleştirme seçiliyken YaST denetim merkezini gösterir.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Senaryo 3: YaST2 yüksek kullanılabilirlik seçeneği değil
Yüksek kullanılabilirlik seçeneğinin YaST2 denetim merkezinde görünür olması için ek paketleri yüklemeniz gerekir.

YaST2>yazılım>yazılım yönetimi>kullanma aşağıdaki desenleri seçin

- SAP HANA sunucusu temeli
- C/C++ derleyicisi ve araçları
- Yüksek kullanılabilirlik
- SAP uygulama sunucusu temeli

Aşağıdaki ekranda, desenleri yüklemek için gereken adımlar gösterilmektedir.

Yazılım Yönetimi > YaST2 > yazılım kullanımı

![Ekran görüntüsünde, yükleme işlemini başlatmak için yazılım ve çevrimiçi güncelleştirme seçiliyken YaST Denetim Merkezi görüntülenir.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Desenleri seçin

![Ekran görüntüsü, C/C++ derleyicisi ve araçlar öğesindeki ilk kalıbı seçmeyi gösterir. ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![ Ekran görüntüsü C/C++ derleyicisi ve araçlar öğesindeki ikinci deseninin seçilmesi gösterilmektedir.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

**Kabul et** 'e tıklayın

![Ekran görüntüsü, bağımlılıkları çözümlemek için paketlerin değiştiği değiştirilen paketler iletişim kutusunu gösterir.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

**Devam** ' a tıklayın

![Ekran görüntüsü, yükleme durumunu gerçekleştirme sayfasını gösterir.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Yükleme tamamlandığında **İleri** ' ye tıklayın

![Ekran görüntüsü yükleme raporunu gösterir.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Senaryo 4: HANA yüklemesi GCC derlemeleri hatasıyla başarısız oluyor
HANA yüklemesi aşağıdaki hatayla başarısız olur.

![Ekran görüntüsü, işletim sisteminin g c c 5 derlemelerini gerçekleştirmeye hazırlanmadığından bir hata iletisi gösterir.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Sorunu onarmak için aşağıdaki şekilde kitaplıklarını (libgcc_sl ve libstdc + + 6) yüklemeniz gerekir.

![Ekran görüntüsü gerekli kitaplıkları yükleyen bir konsol penceresini gösterir.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Senaryo 5: Paceoluşturucu hizmeti başarısız oluyor

Paceoluşturucu hizmeti başlatılırken aşağıdaki sorun oluştu.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Bunu çözmek için */usr/lib/systemd/System/fstrim.exe* dosyasındaki şu satırı silin

```
Persistent=true
```

![Ekran görüntüsünde, silinecek kalıcı = true değerine sahip f s kırpma dosyası gösterilir.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Senaryo 6: düğüm 2 kümeye katılamıyor

Düğüm2, *ha-Cluster-JOIN* komutunu kullanarak var olan kümeye birleştirilirken aşağıdaki hata oluştu.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Ekran görüntüsü, bir I P adresinden S S H anahtarlarını alamıyor hata iletisiyle bir konsol penceresi gösterir.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Bunları onarmak için her iki düğümde de aşağıdakileri çalıştırın

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Ekran görüntüsü, ilk düğümde komutunu çalıştıran bir konsol penceresinin bir parçasını gösterir.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![Ekran görüntüsü ikinci düğümde komutunu çalıştıran bir konsol penceresinin parçasını gösterir.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Yukarıdaki düzeltmeyle sonra, Düğüm2 kümeye eklenmelidir

![Ekran görüntüsünde, başarılı bir ha-Cluster-JOIN komutu içeren bir konsol penceresi gösterilmektedir.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. genel belgeler
Aşağıdaki makalelerde SUSE HA kurulumu hakkında daha fazla bilgi edinebilirsiniz: 

- [SAP HANA SR performansı için Iyileştirilmiş senaryo](https://www.suse.com/support/kb/doc/?id=000019450 )
- [Depolama temelli sınırlama](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog-SAP HANA için Paceoluşturucu kümesi kullanılıyor-Bölüm 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog-SAP HANA için Paceoluşturucu kümesi kullanılıyor-2. Bölüm](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
