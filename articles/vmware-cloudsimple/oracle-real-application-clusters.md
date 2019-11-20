---
title: CloudSimple tarafından Azure VMware çözümü-Oracle RAC için CloudSimple özel bulutunuzu Iyileştirin
description: Yeni bir kümenin nasıl dağıtılacağını ve Oracle gerçek uygulama kümeleri (RAC) yüklemesi ve yapılandırması için bir VM 'yi iyileştirme işlemini açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972419"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Oracle RAC 'yi yüklemek için CloudSimple özel bulutunuzu iyileştirin

CloudSimple özel bulut ortamınızda Oracle gerçek uygulama kümelerini (RAC) dağıtabilirsiniz. Bu kılavuzda, yeni bir kümenin nasıl dağıtılacağı ve Oracle RAC çözümü için bir sanal makinenin nasıl iyileştirileceği açıklanmaktadır. Bu konudaki adımları tamamladıktan sonra, Oracle RAC 'yi yükleyip yapılandırabilirsiniz.

## <a name="storage-policy"></a>Depolama Ilkesi

Oracle RAC 'nin başarılı uygulanması kümede yeterli sayıda düğüm gerektiriyor.  VSAN depolama ilkesinde,, veritabanı, günlük ve yineleme disklerini depolamak için kullanılan veri disklerine tolerans (FTT) uygulanır.  Hatalara etkin bir şekilde tolerans için gereken düğüm sayısı 2 ' dir. burada N, FTT değeridir.

Örnek: İstenen FTT 2 ise, kümedeki düğümlerin toplam sayısı 2 * 2 + 1 = 5 olmalıdır.

## <a name="overview-of-deployment"></a>Dağıtıma genel bakış

Aşağıdaki bölümlerde, Oracle RAC için CloudSimple özel bulut ortamınızın nasıl ayarlanacağı açıklanır.

1. Disk yapılandırması için en iyi uygulamalar
2. CloudSimple özel bulut vSphere kümesi dağıt
3. Oracle RAC için ağ ayarlama
4. VSAN depolama ilkeleri kurulumu
5. Oracle VM 'Leri oluşturma ve paylaşılan VM diskleri oluşturma
6. VM-konak benzeşim kurallarını ayarlama

## <a name="best-practices-for-disk-configuration"></a>Disk yapılandırması için en iyi uygulamalar

Oracle RAC sanal makinelerinde, belirli bir işlev için kullanılan birden çok disk vardır.  Paylaşılan diskler, Oracle RAC kümesi tarafından kullanılan tüm sanal makinelere bağlanır.  İşletim sistemi ve yazılım yükleme diskleri yalnızca ayrı sanal makinelere bağlanır.  

![Oracle RAC sanal makine disklerine genel bakış](media/oracle-vm-disks-overview.png)

Aşağıdaki örnek aşağıdaki tabloda tanımlanan diskleri kullanır.

| Disk                                      | Amaç                                       | Paylaşılan disk |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | İşletim sistemi diski                         | Hayır          |
| ÇIZGISI                                      | Oracle Grid yazılımının yükleneceği konum     | Hayır          |
| VERITABANINIZI                                  | Oracle veritabanı yazılımının konumunu yükler | Hayır          |
| ORAHOME                                   | Oracle veritabanı ikilileri için temel konum    | Hayır          |
| VERI1, VERI2, DATA3, DATA4                | Oracle veritabanı dosyalarının depolandığı disk   | Evet         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Günlük disklerini Yinele                                | Evet         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Oylama diskleri                                  | Evet         |
| FRA1, FRA2                                | Hızlı kurtarma alanı diskleri                      | Evet         |

![Oracle sanal makine diski yapılandırması](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Sanal makine yapılandırması

* Her sanal makine, dört SCSI denetleyicisi ile yapılandırılır.
* SCSI denetleyici türü, VMware Paravirtual olarak ayarlanır.
* Birden çok sanal disk (. vmdk) oluşturulur.
* Diskler farklı SCSI denetleyicilerine bağlanır.
* Çoklu yazıcı paylaşım türü paylaşılan küme diskleri için ayarlanır.
* vSAN depolama ilkesi, disklerin yüksek oranda kullanılabilirliğini sağlamak için tanımlanır.

### <a name="operating-system-and-software-disk-configuration"></a>İşletim sistemi ve yazılım diski yapılandırması

Her Oracle sanal makinesi konak işletim sistemi, takas, yazılım yüklemesi ve diğer IŞLETIM sistemi işlevleri için birden çok disk ile yapılandırılır.  Bu diskler, sanal makineler arasında paylaşılmaz.  

* Her bir sanal makine için üç disk, sanal diskler olarak yapılandırılır ve Oracle RAC sanal makinelerine bağlanır.
    * OS Diski
    * Oracle Grid dosya yükleme dosyalarını depolamak için disk
    * Oracle veritabanı yüklemesi dosyalarını depolamaya yönelik disk
* Diskler **ölçülü kaynak sağlanmış**olarak yapılandırılabilir.
* Her disk ilk SCSI denetleyicisine (SCSI0) bağlanır.  
* Paylaşım, **paylaşım yok**olarak ayarlanır.
* Yedeklilik, depolama üzerinde vSAN ilkeleri kullanılarak tanımlanır.  

![Oracle RAC veri diski grubu yapılandırması](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Veri diski yapılandırması

Veri diskleri, birincil olarak veritabanı dosyalarını depolamak için kullanılır.  

* Dört disk, sanal diskler olarak yapılandırılır ve tüm Oracle RAC sanal makinelerine bağlanır.
* Her disk, farklı bir SCSI denetleyicisine bağlanır.
* Her sanal disk, **kalın sağlama Eager sıfırlandı**olarak yapılandırılır.  
* Paylaşım **birden çok yazıcı**olarak ayarlanmıştır.  
* Diskler otomatik depolama yönetimi (ASM) disk grubu olarak yapılandırılmalıdır.  
* Yedeklilik, depolama üzerinde vSAN ilkeleri kullanılarak tanımlanır.  
* ASM artıklığı **dış** artıklık olarak ayarlanmıştır.

![Oracle RAC veri diski grubu yapılandırması](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Günlük diski yapılandırmasını Yinele

Yineleme günlük dosyaları, veritabanına yapılan değişikliklerin bir kopyasını depolamak için kullanılır.  Günlük dosyaları, verilerin herhangi bir hatadan sonra kurtarılması gerektiğinde kullanılır.

* Yineleme günlüğü disklerinin birden çok disk grubu olarak yapılandırılması gerekir.  
* Altı disk oluşturulur ve tüm Oracle RAC sanal makinelerinde bağlanır.
* Diskler farklı SCSI denetleyicilerine bağlanır
* Her sanal disk, **kalın sağlama Eager sıfırlandı**olarak yapılandırılır.
* Paylaşım **birden çok yazıcı**olarak ayarlanmıştır.  
* Diskler iki ASM disk grubu olarak yapılandırılmış olmalıdır.
* Her ASM disk grubu, farklı SCSI denetleyicilerindeki üç disk içerir.  
* ASM artıklığı **normal** artıklık olarak ayarlanmıştır.
* Beş yineleme günlük dosyası her iki ASM yineleme günlüğü grubunda oluşturulur

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC yineleme günlüğü disk grubu yapılandırması](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle oylama disk yapılandırması

Oylama diskleri, herhangi bir bölünmüş beyana durumu önlemek için ek bir iletişim kanalı olarak çekirdek disk işlevselliği sağlar.

* Tüm Oracle RAC sanal makinelerinde beş disk oluşturulup bağlanır.
* Diskler bir SCSI denetleyicisine bağlanır
* Her sanal disk, **kalın sağlama Eager sıfırlandı**olarak yapılandırılır.
* Paylaşım **birden çok yazıcı**olarak ayarlanmıştır.  
* Diskler bir ASM disk grubu olarak yapılandırılmalıdır.  
* ASM artıklığı **yüksek** artıklık olarak ayarlanmıştır.

![Oracle RAC oylama disk grubu yapılandırması](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle hızlı kurtarma alanı disk yapılandırması (isteğe bağlı)

Hızlı kurtarma alanı (FRA), Oracle ASM disk grubu tarafından yönetilen dosya sistemidir.  FRA, yedekleme ve kurtarma dosyaları için paylaşılan bir depolama konumu sağlar. Oracle, hızlı kurtarma alanında arşivlenmiş günlükler ve Flashback günlükleri oluşturur. Oracle kurtarma Yöneticisi (RMAN) isteğe bağlı olarak, yedekleme kümelerini ve görüntü kopyalarını hızlı kurtarma alanında saklayabilir ve medya kurtarma sırasında dosyaları geri yüklerken kullanır.

* Tüm Oracle RAC sanal makinelerinde iki disk oluşturulup bağlanır.
* Diskler farklı bir SCSI denetleyicisine bağlandı
* Her sanal disk, **kalın sağlama Eager sıfırlandı**olarak yapılandırılır.
* Paylaşım **birden çok yazıcı**olarak ayarlanmıştır.  
* Diskler bir ASM disk grubu olarak yapılandırılmalıdır.  
* ASM artıklığı **dış** artıklık olarak ayarlanmıştır.

![Oracle RAC oylama disk grubu yapılandırması](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple özel bulut vSphere kümesi dağıt

Özel bulutunuzda bir vSphere kümesi dağıtmak için şu işlemi izleyin:

1. CloudSimple portalından [özel bir bulut oluşturun](create-private-cloud.md). CloudSimple, yeni oluşturulan özel bulutta ' cloudowner ' adlı bir varsayılan vCenter kullanıcısı oluşturur. Varsayılan özel bulut kullanıcısı ve izin modeli hakkında daha fazla bilgi için bkz. [özel bulut izin modelini öğrenme](learn-private-cloud-permissions.md).  Bu adım özel bulutunuz için birincil yönetim kümesini oluşturur.

2. CloudSimple portalından, [özel bulutu](expand-private-cloud.md) yeni bir kümeyle genişletin.  Bu küme, Oracle RAC dağıtmak için kullanılacaktır.  İstenen hataya dayanıklılık (en az üç düğüm) temelinde düğüm sayısını seçin.

## <a name="set-up-networking-for-oracle-rac"></a>Oracle RAC için ağ ayarlama

1. Özel bulutunuzda, biri Oracle ortak ağı ve diğeri de Oracle özel ağı için olmak üzere [Iki VLAN oluşturun](create-vlan-subnet.md)ve uygun alt ağ cıdrs atayın.
2. VLAN 'Lar oluşturulduktan sonra, [özel bulut vCenter üzerinde dağıtılmış bağlantı noktası gruplarını](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)oluşturun.
3. Oracle ortamı için yönetim kümenizde bir [DHCP ve DNS sunucusu sanal makinesi](dns-dhcp-setup.md) ayarlayın.
4. Özel bulutta yüklü [DNS sunucusunda DNS Iletmeyi yapılandırın](on-premises-dns-setup.md#create-a-conditional-forwarder) .

## <a name="set-up-vsan-storage-policies"></a>VSAN depolama ilkelerini ayarlama

vSAN ilkeleri VM disklerinde depolanan veriler için tolerans ve disk şeritleme başarısızlıklarını tanımlar.  VM oluşturulurken oluşturulan depolama ilkesinin VM disklerinde uygulanması gerekir.

1. Özel bulutunuzun [vSphere Istemcisinde oturum açın](https://docs.azure.cloudsimple.com/vsphere-access) .
2. Üstteki menüden **ilkeler ve profiller**' i seçin.
3. Sol menüden **VM depolama ilkeleri** ' ni seçin ve ardından **VM depolama ilkesi oluştur**' u seçin.
4. İlke için anlamlı bir ad girin ve **İleri**' ye tıklayın.
5. **İlke yapısı** bölümünde, **vSAN depolaması için kuralları etkinleştir** ' i seçin ve **İleri**' ye tıklayın.
6. **VSAN** > **kullanılabilirliği** bölümünde, site olağanüstü durum toleransı için **hiçbiri** ' ni seçin. Hatalara yönelik tolerans için, istenen FTT için **RAID-yansıtma** seçeneğini belirleyin.
    ![vSAN ayarları](media/oracle-rac-storage-wizard-vsan.png).
7. **Gelişmiş** bölümünde, nesne başına disk şeritleri sayısını seçin. Nesne alanı ayırması için, **kalın sağlanmış**' ı seçin. **Nesne sağlamasını devre dışı bırak**' ı seçin. **İleri**' ye tıklayın.
8. Uyumlu vSAN veri depolarının listesini görüntülemek için ekrandaki yönergeleri izleyin, ayarları gözden geçirin ve kurulumu tamamlayın.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle için Oracle VM 'Leri oluşturma ve paylaşılan VM diskleri oluşturma

Oracle için bir VM oluşturmak için, var olan bir VM 'yi kopyalayın veya yeni bir tane oluşturun.  Bu bölümde, yeni bir VM oluşturma ve ardından temel işletim sistemi yüklendikten sonra ikinci bir tane oluşturmak için nasıl klonlanmakta olduğunuz açıklanmaktadır.  VM 'Ler oluşturulduktan sonra, bunlara bir disk ekleme oluşturabilirsiniz.  Oracle kümesi, depolama, veri, Günlükler ve yineleme günlükleri için paylaşılan diskler kullanır.

### <a name="create-vms"></a>VM oluşturma

1. VCenter 'da **konaklar ve kümeler** simgesine tıklayın. Oracle için oluşturduğunuz kümeyi seçin.
2. Kümeye sağ tıklayın ve **Yeni sanal makine**' yi seçin.
3. **Yeni sanal makine oluştur** ' u seçin ve **İleri**' ye tıklayın.
4. Makineyi adlandırın, Oracle VM 'nin konumunu seçin ve **İleri**' ye tıklayın.
5. Küme kaynağını seçin ve **İleri**' ye tıklayın.
6. Küme için vSAN veri deposunu seçin ve **İleri**' ye tıklayın.
7. Varsayılan ESXi 6,5 uyumluluğu seçimini koruyun ve **İleri**' ye tıklayın.
8. Oluşturmakta olduğunuz VM için ISO Konuk işletim sistemini seçin ve **İleri**' ye tıklayın.
9. İşletim sistemini yüklemek için gereken sabit disk boyutunu seçin.
10. Uygulamayı farklı bir cihaza yüklemek için **yeni cihaz ekle**' ye tıklayın.
11. Ağ seçenekleri ' ni seçin ve ortak ağ için oluşturulan dağıtılmış bağlantı noktası grubunu atayın.
12. Ek ağ arabirimleri eklemek için **yeni cihaz ekle** ' ye tıklayın ve özel ağ için oluşturulan dağıtılmış bağlantı noktası grubunu seçin.
13. Yeni DC/DVD sürücüsü için, tercih edilen işletim sistemi yüklemesi için ISO 'yu içeren veri deposu ISO dosyasını seçin. Daha önce ISOs ve Şablonlar klasörüne yüklediğiniz dosyayı seçin ve **Tamam**' a tıklayın.
14. Ayarları gözden geçirin ve yeni VM 'yi oluşturmak için **Tamam** ' ı tıklatın.
15. VM 'yi açma. İşletim sistemini ve gerekli tüm güncelleştirmeleri yükler

İşletim sistemi yüklendikten sonra ikinci bir VM 'yi kopyalayabilirsiniz. VM girişine sağ tıklayın ve ve Kopyala seçeneğini belirleyin.

### <a name="create-shared-disks-for-vms"></a>VM 'Ler için paylaşılan diskler oluşturma

Oracle, verileri depolamak için paylaşılan disk kullanır, günlüğü günlüğe kaydeder ve günlük dosyalarını yineler.  VCenter üzerinde paylaşılan bir disk oluşturup her iki VM 'ye de bağlayabilirsiniz.  Daha yüksek performans için, veri disklerini farklı SCSI denetleyicilerle, vCenter 'da paylaşılan bir disk oluşturma ve ardından bir sanal makineye iliştirme adımları gösterilmektedir. vCenter Flash Client, VM özelliklerini değiştirmek için kullanılır.

#### <a name="create-disks-on-the-first-vm"></a>İlk VM 'de disk oluşturma

1. VCenter 'da Oracle VM 'lerinden birine sağ tıklayın ve **Ayarları Düzenle**' yi seçin.
2. Yeni cihaz bölümünde **SCSI denetleyicisi** ' ni seçin ve **Ekle**' ye tıklayın.
3. Yeni cihaz bölümünde **Yeni sabit disk** ' i seçin ve **Ekle**' ye tıklayın.
4. Yeni sabit diskin özelliklerini genişletin.
5. Sabit diskin boyutunu belirtin.
6. VM depolama ilkesini, daha önce tanımladığınız vSAN depolama ilkesi olacak şekilde belirtin.
7. VSAN veri deposundaki bir klasör olarak konumu seçin. Konum, tarama ve diskleri ikinci bir VM 'ye ekleme konusunda yardımcı olur.
8. Disk sağlama için, **kalın sağlama Eager sıfırlandı**' ı seçin.
9. Paylaşım için **birden çok yazıcı**belirtin.
10. Sanal cihaz düğümü için adım 2 ' de oluşturulan yeni SCSI denetleyicisini seçin.

    ![İlk VM 'de disk oluşturma](media/oracle-rac-new-hard-disk.png)

Oracle verileri, günlükleri ve yineleme günlük dosyaları için gereken tüm yeni diskler için 2 – 10 arasındaki adımları yineleyin.

#### <a name="attach-disks-to-second-vm"></a>İkinci VM 'ye disk iliştirme

1. VCenter 'da Oracle VM 'lerinden birine sağ tıklayın ve **Ayarları Düzenle**' yi seçin.
2. Yeni cihaz bölümünde **SCSI denetleyicisi** ' ni seçin ve **Ekle**' ye tıklayın.
3. Yeni cihaz bölümünde, **var olan sabit disk** ' i seçin ve **Ekle**' ye tıklayın.
4. İlk VM için diskin oluşturulduğu konuma gidin ve VMDK dosyasını seçin.
5. VM depolama ilkesini, daha önce tanımladığınız vSAN depolama ilkesi olacak şekilde belirtin.
6. Disk sağlama için, **kalın sağlama Eager sıfırlandı**' ı seçin.
7. Paylaşım için **birden çok yazıcı**belirtin.
8. Sanal cihaz düğümü için adım 2 ' de oluşturulan yeni SCSI denetleyicisini seçin.

    ![İlk VM 'de disk oluşturma](media/oracle-rac-existing-hard-disk.png)

Oracle verileri, günlükleri ve yineleme günlük dosyaları için gereken tüm yeni diskler için 2 – 7 arasındaki adımları yineleyin.

## <a name="set-up-vm-host-affinity-rules"></a>VM konak benzeşim kurallarını ayarlama

Sanal makineden konağa benzeşim kuralları VM 'nin istenen konakta çalıştığından emin olun.  Oracle VM 'nin yeterli kaynakları olan konakta ve belirli lisans gereksinimlerini karşılayacak şekilde çalıştığından emin olmak için vCenter üzerinde kurallar tanımlayabilirsiniz.

1. CloudSimple portalında, cloudowner kullanıcısının [ayrıcalıklarını ilerletin](escalate-private-cloud-privileges.md) .
2. Özel bulutunuzun [vSphere Istemcisinde oturum açın](https://docs.azure.cloudsimple.com/vsphere-access) .
3. VSphere istemcisinde, Oracle VM 'lerinin dağıtıldığı kümeyi seçin ve **Yapılandır**' a tıklayın.
4. Yapılandır altında **VM/konak grupları**' nı seçin.
5. **+** öğesine tıklayın.
6. Bir VM grubu ekleyin. Tür olarak **VM grubu** ' nu seçin. Grubun adını girin. VM 'Leri seçin ve sonra grubu oluşturmak için **Tamam** ' ı tıklatın.
6. Bir konak grubu ekleyin. Tür olarak **konak grubu** ' nu seçin. Grubun adını girin. VM 'Lerin çalıştırılacağı Konakları seçin ve sonra grubu oluşturmak için **Tamam** ' ı tıklatın.
7. Bir kural oluşturmak için **VM/konak kuralları**' na tıklayın.
8. **+** öğesine tıklayın.
9. Kural için bir ad girin ve **Etkinleştir**' i işaretleyin.
10. Kural türü için, **barındıracak sanal makineler**' i seçin.
11. Oracle VM 'Leri içeren VM grubunu seçin.
12. Select **Bu gruptaki konaklarda çalıştırılmalıdır**.
13. Oluşturduğunuz konak grubunu seçin.
14. Kuralı oluşturmak için **Tamam**'a tıklayın.

## <a name="references"></a>Referanslar

* [VSAN Ilkeleri hakkında](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Paylaşılan VMDK için VMware Multi-Writer özniteliği](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
