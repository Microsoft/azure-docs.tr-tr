---
title: RHEL üzerinde ANF ile SAP HANA ölçeği yüksek kullanılabilirliği | Microsoft Docs
description: Azure sanal makinelerinde (VM 'Ler) ANF ile SAP HANA yüksek kullanılabilirlik sağlayın.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 774344c4215088482b110de91f8951bae4a41d25
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365833"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux Azure NetApp Files ile SAP HANA ölçeği yüksek kullanılabilirliği

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Bu makalede, HANA dosya sistemleri Azure NetApp Files (ANF) kullanılarak NFS aracılığıyla bağlandığında, ölçek dağıtımı 'nda SAP HANA sistem çoğaltmasının nasıl yapılandırılacağı açıklanır. Örnek yapılandırma ve yükleme komutları, örnek numarası **03** ve Hana sistem kimliği **HN1** kullanılır. SAP HANA çoğaltma bir birincil düğümden ve en az bir ikincil düğümden oluşur.

Bu belgedeki adımlar aşağıdaki öneklerle işaretlendiğinde, anlamı aşağıdaki gibidir:

- **[A]**: adım tüm düğümlere uygulanır
- **[1]**: adım yalnızca Düğüm1 için geçerlidir
- **[2]**: adım yalnızca Düğüm2 için geçerlidir
 
Önce aşağıdaki SAP notlarını ve kağıtları okuyun:

- SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533), şunları içerir:
    - SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi.
    - Azure VM boyutları için önemli kapasite bilgileri.
    - Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri.
    - Microsoft Azure üzerinde Windows ve Linux için gereken SAP çekirdek sürümü.
- SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553) , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) , Hana ortamı için önerilen dosya sistemini listeler.
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) Red Hat Enterprise Linux için önerilen işletim sistemi ayarlarını içerir.
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) , Red Hat Enterprise Linux Için SAP HANA kılavuz içerir.
- SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632) , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498) , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
- SAP Note [2243692](https://launchpad.support.sap.com/#/notes/2243692) , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
- SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351) , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
- [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
- [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
- [Linux 'ta SAP için Azure sanal makineleri dağıtımı][deployment-guide]
- [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
- [Pacemaker kümesinde sistem çoğaltmasını SAP HANA.](https://access.redhat.com/articles/3004101)
- Genel RHEL belgeleri
    - [Yüksek kullanılabilirlik Add-On genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Yüksek kullanılabilirlik Add-On yönetimi.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Yüksek kullanılabilirlik Add-On başvurusu.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [HANA dosya sistemleri NFS paylaşımlarındaytığında, bir pacemaker kümesinde Scale-Up SAP HANA sistem çoğaltmasını yapılandırma](https://access.redhat.com/solutions/5156571)
- Azure 'a özgü RHEL belgeleri:
    - [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler.](https://access.redhat.com/articles/3131341)
    - [Microsoft Azure Red Hat Enterprise Linux 7,4 (ve üzeri) High-Availability kümesini yükleme ve yapılandırma.](https://access.redhat.com/articles/3252491)
    - [Microsoft Azure kullanım için Red Hat Enterprise Linux SAP HANA yüklemesi.](https://access.redhat.com/solutions/3193782)
    - [HANA dosya sistemleri NFS paylaşımlarındaytığında, genişleme sistem çoğaltmasını yukarı Paceoluşturucu kümesi SAP HANA yapılandırma](https://access.redhat.com/solutions/5156571)
- [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları](https://www.netapp.com/us/media/tr-4746.pdf)
- [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Genel Bakış

Geleneksel olarak, ölçek artırma ortamında, SAP HANA için tüm dosya sistemleri yerel depolamadan bağlanır. Red Hat Enterprise Linux üzerinde SAP HANA sistem çoğaltmasının yüksek kullanılabilirliğini ayarlama, [RHEL üzerinde sistem çoğaltmasını SAP HANA ayarlama](./sap-hana-high-availability-rhel.md) bölümünde yayımlanır

[Azure NetApp Files](../../../azure-netapp-files/index.yml) NFS paylaşımlarında ölçek artırma sisteminin yüksek oranda kullanılabilir duruma getirmek SAP HANA için, bir düğüm ANF üzerinde NFS paylaşımlarına ERIŞIMI kaybettiğinde Hana kaynaklarının kurtarılmasını sağlamak üzere kümede bazı ek kaynak yapılandırmasına ihtiyacımız vardır.  Küme, NFS takmaları yönetir ve bu da kaynakların sistem durumunu izlemesine olanak tanır. Dosya sistemi takmaları ve SAP HANA kaynakları arasındaki bağımlılıklar zorlanır.  

![ANF 'de HA ölçeğini SAP HANA](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA dosya sistemleri, her düğümdeki Azure NetApp Files kullanılarak NFS paylaşımlarına bağlanır. /Hana/Data,/Hana/log ve/Hana/Shared dosya sistemleri her düğüm için benzersizdir. 

Düğüm1 üzerine bağlandı (**hanadb1**)

- 10.32.2.4:/**hanadb1**-Data-mnt00001 on/Hana/Data
- 10.32.2.4:/**hanadb1**-log-mnt00001 on/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 on/Hana/Shared

Düğüm2 üzerine bağlandı (**hanadb2**)

- 10.32.2.4:/**hanadb2**-Data-mnt00001 on/Hana/Data
- 10.32.2.4:/**hanadb2**-log-mnt00001 on/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 on/Hana/Shared

> [!NOTE]
> /Hana/Shared,/Hana/Data ve/Hana/log dosya sistemleri iki düğüm arasında paylaşılmaz. Her küme düğümünün kendine ait ayrı dosya sistemleri vardır.   

SAP HANA sistem çoğaltma yapılandırması, ayrılmış bir sanal konak adı ve sanal IP adresleri kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. Aşağıdaki listede yük dengeleyicinin yapılandırması gösterilmektedir:

- Ön uç yapılandırması: hn1-DB için IP adresi 10.32.0.10
- Arka uç yapılandırması: HANA sistem çoğaltmasının parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
- Araştırma bağlantı noktası: bağlantı noktası 62503
- Yük Dengeleme kuralları: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (temel Azure yük dengeleyici kullanılıyorsa)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Azure NetApp dosya altyapısını ayarlama

Azure NetApp Files altyapısına yönelik kuruluma devam etmeden önce, Azure [NetApp dosyaları belgelerini](../../../azure-netapp-files/index.yml)öğrenmeye çalışın.

Azure NetApp Files çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)kullanılabilir. Seçtiğiniz Azure bölgesinin Azure NetApp Files sunmadığını denetleyin.

Azure bölgesine göre Azure NetApp Files kullanılabilirliği hakkında daha fazla bilgi için bkz. [Azure bölgesine göre Azure NetApp Files kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Azure NetApp Files dağıtmadan önce [Azure NetApp Files yönergeler Için kaydolun](../../../azure-netapp-files/azure-netapp-files-register.md)bölümüne giderek Azure NetApp Files ekleme isteyin.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files kaynaklarını dağıtma

Aşağıdaki yönergelerde, [Azure Sanal ağınızı](../../../virtual-network/virtual-networks-overview.md)zaten dağıttığınız varsayılmaktadır. Azure NetApp Files kaynakların dağıtılacağı Azure NetApp Files kaynakları ve VM 'Ler, aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında dağıtılmalıdır.

1. Kaynakları henüz dağıtmadıysanız [Azure NetApp Files ekleme](../../../azure-netapp-files/azure-netapp-files-register.md)isteyin.

2. [Bir NetApp hesabı oluşturma](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)bölümündeki yönergeleri Izleyerek seçtiğiniz Azure bölgesindeki bir NetApp hesabı oluşturun.

3.  [Bir Azure NetApp Files kapasite havuzu ayarlama](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)bölümündeki yönergeleri izleyerek Azure NetApp Files bir kapasite havuzu ayarlayın.

    Bu makalede sunulan HANA mimarisi, *Ultra* hizmet düzeyinde tek bir Azure NetApp Files kapasite havuzu kullanır. Azure 'daki HANA iş yükleri için Azure NetApp Files *Ultra* veya *Premium* [hizmet düzeyi](../../../azure-netapp-files/azure-netapp-files-service-levels.md)kullanmanızı öneririz.

4.  [Azure NetApp Files için bir alt ağ devretmek](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)içindeki yönergelerde açıklandığı gibi Azure NetApp Files için bir alt ağ atayın.

5.  [Azure NetApp Files IÇIN NFS birimi oluşturma](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)bölümündeki yönergeleri izleyerek Azure NetApp Files birimleri dağıtın.

    Birimleri dağıttığınızda, NFSv 4.1 sürümünü seçtiğinizden emin olun. Birimleri belirlenen Azure NetApp Files alt ağına dağıtın. Azure NetApp birimlerinin IP adresleri otomatik olarak atanır.

    Azure NetApp Files kaynaklarının ve Azure VM 'lerinin aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında olması gerektiğini unutmayın. Örneğin, hanadb1-Data-mnt00001, hanadb1-log-mnt00001 ve benzeri, birim adları ve nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001, vb., Azure NetApp Files birimlerinin dosya yollarıdır.
    
    **Hanadb1** üzerinde

    - Volume hanadb1-Data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    **Hanadb2** üzerinde

    - Volume hanadb2-Data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Önemli noktalar

SAP HANA ölçeği artırma sistemleri için Azure NetApp Files oluştururken, aşağıdaki farkında dikkat edin:

- En düşük kapasite havuzu 4 tebibayt (Tib).
- En küçük birim boyutu 100 Gibibyte (gib).
- Azure NetApp Files ve Azure NetApp Files birimlerinin takılabileceği tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](../../../virtual-network/virtual-network-peering-overview.md) olmalıdır.
- Seçili sanal ağın Azure NetApp Files için temsilci atanmış bir alt ağı olmalıdır.
- Azure NetApp Files bir birimin verimlilik, [Azure NetApp Files Için hizmet düzeyinde](../../../azure-netapp-files/azure-netapp-files-service-levels.md)belgelendiği gibi birim kotasının ve hizmet düzeyinin bir işlevidir. HANA Azure NetApp birimlerini boyutlandırdığınızda, sonuçta elde edilen aktarım hızı HANA sistem gereksinimlerini karşıladığından emin olun.
- Azure NetApp Files [dışarı aktarma ilkesiyle](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)izin verilen istemcileri, erişim türünü (okuma-yazma, salt okuma vb.) denetleyebilirsiniz.
- Azure NetApp Files özelliği henüz bölge duyarlı değil. Şu anda, özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmadı. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun.

> [!IMPORTANT]
> SAP HANA iş yükleri için düşük gecikme süresi kritik öneme sahiptir. Sanal makinelerin ve Azure NetApp Files birimlerinin yakınlık halinde dağıtıldığından emin olmak için Microsoft temsilcinizle birlikte çalışın.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA veritabanının boyutlandırması

Azure NetApp Files bir birimin verimlilik, [Azure NetApp Files Için hizmet düzeyinde](../../../azure-netapp-files/azure-netapp-files-service-levels.md)belgelendiği gibi birim boyutu ve hizmet düzeyi işlevindedir.

SAP altyapısını Azure 'da tasarlarken, en düşük işleme özelliklerine çeviren SAP 'ye göre bazı minimum depolama gereksinimlerini göz önünde bulundurun:

- /Hana/log/250 megabayt/sn (MB/s) ile 1 MB g/ç boyutları ile okuma-yazma.
- 16 MB ve 64-MB g/ç boyutları için/Hana/Data için en az 400 MB/sn etkinliğini okuyun.
- 16 MB ve 64-MB g/ç boyutları ile/Hana/Data için en az 250 MB/sn etkinliğini yazın.

Birim kotasının 1 TiB başına [Azure NetApp Files verimlilik limitleri](../../../azure-netapp-files/azure-netapp-files-service-levels.md) şunlardır:

- Premium depolama katmanı-64 MIB/s.
- Ultra Storage katmanı-128 MIB/s.

/Hana/Data ve/Hana/log için SAP minimum aktarım hızı gereksinimlerini ve/Hana/Shared yönergelerini karşılamak için önerilen boyutlar şöyle olacaktır:

|    Birim    | Premium Depolama katmanının boyutu | Ultra Storage katmanının boyutu | Desteklenen NFS Protokolü |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /Hana/log   |            4 TiB             |           2 TiB            |          v 4.1          |
|  /Hana/Data  |           6,3 TiB            |          3,2 TiB           |          v 4.1          |
| /Hana/Shared |           1 x RAM            |          1 x RAM           |          V3 veya v 4.1    |


> [!NOTE]
> Burada belirtilen Azure NetApp Files boyutlandırma önerileri, SAP 'nin altyapı sağlayıcıları için önerdiği minimum gereksinimleri karşılamaya yöneliktir. Gerçek müşteri dağıtımları ve iş yükü senaryolarında bu boyutlar yeterli olmayabilir. Bu önerileri bir başlangıç noktası olarak kullanın ve belirli iş yükünüzün gereksinimlerine göre uyarlayın.

> [!TIP]
> Birimleri *çıkarmanız* , sanal makineleri durdurmanız veya SAP HANA durdurmanız gerekmeden Azure NetApp Files birimleri dinamik olarak yeniden boyutlandırabilirsiniz. Bu yaklaşım, uygulamanızın hem beklenen hem de öngörülemeyen işleme taleplerini karşılamak için esneklik sağlar.

> [!NOTE]
> Bu makaledeki/Hana/Shared adlı tüm komutlar NFSv 4.1/Hana/Shared Volumes için sunulmuştur.
> /Hana/Shared birimlerini NFSv3 birimleri olarak dağıttıysanız, NFSv3 için/Hana/Shared bağlama komutlarını ayarlamayı unutmayın.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Linux sanal makinesini Azure portal aracılığıyla dağıtma 

Öncelikle Azure NetApp Files birimleri oluşturmanız gerekir. Ardından aşağıdaki adımları uygulayın:

1.  Bir kaynak grubu oluşturun.
2.  Sanal ağ oluşturun.
3.  Bir kullanılabilirlik kümesi oluşturun. En fazla güncelleştirme etki alanını ayarlayın.
4.  Yük Dengeleyici (iç) oluşturun. Standart yük dengeleyiciyi öneririz.
    2. adımda oluşturulan sanal ağı seçin.
5.  Sanal makine oluştur 1 (**hanadb1**). 
6.  Sanal makine oluştur 2 (**hanadb2**).  
7.  Sanal makine oluştururken, tüm bağlama noktalarımız Azure NetApp Files NFS paylaşımlarında olacak şekilde hiçbir disk ekliyoruz. 

> [!IMPORTANT]
> Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.    

> [!NOTE] 
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](./high-availability-guide-standard-load-balancer-outbound-connections.md).

8.  Standart yük dengeleyici kullanıyorsanız, bu yapılandırma adımlarını izleyin:
    1.  İlk olarak, bir ön uç IP havuzu oluşturun:
        1.  Yük dengeleyiciyi açın, **ön uç IP havuzu**' nu seçin ve **Ekle**' yi seçin.
        1.  Yeni ön uç IP havuzunun adını girin (örneğin, **Hana-ön uç**).
        1.  **Atamayı** **statik** olarak ayarlayın ve IP adresini (örneğin, **10.32.0.10**) girin.
        1.  **Tamam**’ı seçin.
        1.  Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.
    1.  Sonra, bir arka uç havuzu oluşturun:
        1.  Yük dengeleyiciyi açın, **arka uç havuzları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni arka uç havuzunun adını girin (örneğin, **Hana arka ucu**).
        1.  **Sanal makine Ekle**' yi seçin.
        1.  * * Sanal makine * * öğesini seçin.
        1.  SAP HANA kümesinin sanal makinelerini ve IP adreslerini seçin.
        1.  **Add (Ekle)** seçeneğini belirleyin.
    1.  Sonra, bir sistem durumu araştırması oluşturun:
        1.  Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-HP**).
        1.  Protokol ve bağlantı noktası 625 **03** olarak TCP ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
        1.  **Tamam**’ı seçin.
    1.  Sonra, Yük Dengeleme kurallarını oluşturun:
        1.  Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni yük dengeleyici kuralının adını girin (örneğin, **Hana-lb**).
        1.  Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**, **Hana-arka uç** ve **Hana-HP**) seçin.
        1.  **Ha bağlantı noktalarını** seçin.
        1.  **Kayan IP**'yi etkinleştirdiğinizden emin olun.
        1.  **Tamam**’ı seçin.


9. Alternatif olarak, senaryonuz temel yük dengeleyiciyi kullanmayı belirlemesi durumunda aşağıdaki yapılandırma adımlarını izleyin:
    1.  Yük dengeleyiciyi yapılandırın. İlk olarak, bir ön uç IP havuzu oluşturun:
        1.  Yük dengeleyiciyi açın, **ön uç IP havuzu**' nu seçin ve **Ekle**' yi seçin.
        1.  Yeni ön uç IP havuzunun adını girin (örneğin, **Hana-ön uç**).
        1.  **Atamayı** **statik** olarak ayarlayın ve IP adresini (örneğin, **10.32.0.10**) girin.
        1.  **Tamam**’ı seçin.
        1.  Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.
    1.  Sonra, bir arka uç havuzu oluşturun:
        1.  Yük dengeleyiciyi açın, **arka uç havuzları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni arka uç havuzunun adını girin (örneğin, **Hana arka ucu**).
        1.  **Sanal makine Ekle**' yi seçin.
        1.  Adım 3 ' te oluşturulan kullanılabilirlik kümesini seçin.
        1.  SAP HANA kümesinin sanal makinelerini seçin.
        1.  **Tamam**’ı seçin.
    1.  Sonra, bir sistem durumu araştırması oluşturun:
        1.  Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-HP**).
        1.  Protokol ve bağlantı noktası 625 **03** olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
        1.  **Tamam**’ı seçin.
    1.  SAP HANA 1,0 için, Yük Dengeleme kurallarını oluşturun:
        1.  Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3 **03** 15).
        1.  Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**) seçin.
        1.  **Protokolü** **TCP** olarak ayarlayın ve bağlantı noktası 3 **03** 15 girin.
        1.  **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
        1.  **Kayan IP**'yi etkinleştirdiğinizden emin olun.
        1.  **Tamam**’ı seçin.
        1.  Bağlantı noktası 3 **03** 17 için bu adımları tekrarlayın.
    1.  SAP HANA 2,0 için, sistem veritabanı için Yük Dengeleme kurallarını oluşturun:
        1.  Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3 **03** 13).
        1.  Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**) seçin.
        1.  **Protokolü** **TCP** olarak ayarlayın ve bağlantı noktası 3 **03** 13 yazın.
        1.  **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
        1.  **Kayan IP**'yi etkinleştirdiğinizden emin olun.
        1.  **Tamam**’ı seçin.
        1.  Bağlantı noktası 3 **03** 14 için bu adımları tekrarlayın.
    1.  SAP HANA 2,0 için, önce Kiracı veritabanı için Yük Dengeleme kurallarını oluşturun:
        1.  Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
        1.  Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3 **03** 40).
        1.  Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **Hana-ön uç**).
        1.  **Protokolü** **TCP** olarak ayarlayın ve bağlantı noktası 3 **03** 40 yazın.
        1.  **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
        1.  **Kayan IP**'yi etkinleştirdiğinizden emin olun.
        1.  **Tamam**’ı seçin.
        1.  Bağlantı noktaları 3 **03** 41 ve 3 **03** 42 için bu adımları yineleyin.

SAP HANA için gereken bağlantı noktaları hakkında daha fazla bilgi için, [SAP HANA kiracı veritabanları](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) Kılavuzu veya SAP Note [2388694](https://launchpad.support.sap.com/#/notes/2388694)' de [kiracı veritabanlarına yönelik bölüm bağlantılarını](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) okuyun.

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. Parametre **net.ipv4.tcp_timestamps** **0** olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](../../../load-balancer/load-balancer-custom-probe-overview.md). Ayrıca bkz. SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Azure NetApp Files birimini bağlama

1. **[A]** Hana veritabanı birimleri için bağlama noktaları oluşturun. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** NFS etki alanı ayarını doğrulayın. Etki alanının varsayılan Azure NetApp Files etki alanı olarak yapılandırıldığından emin olun, yani **defaultv4iddomain.com** ve eşleme **hiç bir kimse** olarak ayarlanmıştır.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > SANAL makinede bulunan/etc/ıdmapd.exe içindeki NFS etki alanını Azure NetApp Files: **defaultv4iddomain.com** konumundaki varsayılan etki alanı yapılandırmasıyla eşleşecek şekilde ayarladığınızdan emin olun. NFS istemcisindeki (yani, VM) ve NFS sunucusunun etki alanı yapılandırması arasında uyuşmazlık varsa (örneğin, Azure NetApp yapılandırması), VM 'Lere bağlı Azure NetApp birimlerinde dosya izinleri hiç kimse tarafından görüntülenir.
    

3. **[1]** Düğüm1 üzerinde düğüme özgü birimleri bağlama (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Düğüm2 üzerinde düğüme özgü birimleri bağlama (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** tüm Hana birimlerinin NFS protokol sürümü NFSv4 bağlandığından emin olun.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** **nfs4_disable_idmapping** doğrulayın. **Y** olarak ayarlanmalıdır. **Nfs4_disable_idmapping** bulunduğu dizin yapısını oluşturmak için Mount komutunu yürütün. Erişim çekirdek/sürücü için ayrıldığından,/sys/modules altında dizini el ile oluşturamazsınız.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   **Nfs_disable_idmapping** parametresinin nasıl değiştirileceği hakkında daha fazla bilgi için bkz [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) .. 


## <a name="sap-hana-installation"></a>SAP HANA yükleme

1. **[A]** tüm konaklar için konak adı çözümlemesi ayarlayın.

   Bir DNS sunucusu kullanabilir ya da tüm düğümlerdeki/etc/hosts dosyasını değiştirebilirsiniz. Bu örnekte,/etc/hosts dosyasının nasıl kullanılacağı gösterilmektedir. Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** Hana yapılandırması için RHEL

   RHEL 'yi, RHEL sürümünüze bağlı olarak aşağıdaki SAP notunda açıklandığı şekilde yapılandırın

   - [2292690-SAP HANA DB: RHEL 7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: RHEL 8 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: GCC 6. x ile derlenen SAP uygulamaları çalıştırma](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: GCC 7. x ile derlenen SAP uygulamalarını çalıştırma](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: GCC 9. x ile derlenen SAP uygulamalarını çalıştırma](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** SAP HANA yüklemesi

   HANA 2,0 SPS 01, MDC varsayılan seçenektir. HANA sistemi yüklediğinizde, SYSTEMDB ve aynı SID 'ye sahip bir kiracı birlikte oluşturulur. Bazı durumlarda, varsayılan kiracıyı istemezsiniz. Bu durumda, yükleme ile birlikte ilk kiracı oluşturmak istemiyorsanız, SAP Note [2629711](https://launchpad.support.sap.com/#/notes/2629711) ' i izleyebilirsiniz.

    HANA DVD 'sindeki **hdblcm** programını çalıştırın. Komut istemine aşağıdaki değerleri girin:  
    Yükleme seç: **1** girin (yükleme için)  
    Yükleme için ek bileşenler seçin: **1** girin.  
    Yükleme yolunu girin [/Hana/Shared]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Yerel ana bilgisayar adını girin [..]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Sisteme ek konaklar eklemek istiyor musunuz? (e/h) [n]: **n**  
    SAP HANA sistem KIMLIĞINI girin: **HN1** girin.  
    Örnek numarasını girin [00]: **03** girin  
    Veritabanı modunu seçin/Dizin [1] girin: varsayılanı kabul etmek için ENTER tuşuna basın  
    Sistem kullanımı seçin/dizin girin [4]: **4** girin (özel için)  
    Veri birimlerinin konumunu girin [/Hana/Data]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Günlük birimlerinin konumunu girin [/Hana/log]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Maksimum bellek ayırmayı kısıtla mı? [n]: varsayılan değer kabul etmek için ENTER tuşuna basın  
    '... ' Konağının sertifika ana bilgisayar adını girin [...]: varsayılanı kabul etmek için ENTER tuşuna basın  
    SAP konak Aracısı Kullanıcı (sapadm) parolasını girin: konak Aracısı Kullanıcı parolasını girin  
    SAP konak aracısı kullanıcısı (sapadm) parolasını onaylayın: onaylamak için konak Aracısı Kullanıcı parolasını yeniden girin  
    Sistem Yöneticisi (hn1adm) parolasını girin: Sistem Yöneticisi parolasını girin  
    Sistem Yöneticisi (hn1adm) parolasını onaylayın: onaylamak için sistem yöneticisi parolasını yeniden girin  
    Sistem Yöneticisi ana dizinini girin [/usr/sap/HN1/home]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Sistem Yöneticisi oturum açma kabuğunu girin [/bin/sh]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Sistem Yöneticisi kullanıcı KIMLIĞINI girin [1001]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Kullanıcı grubu KIMLIĞINI girin (sapsys) [79]: varsayılanı kabul etmek için ENTER tuşuna basın  
    Veritabanı kullanıcı (SISTEM) parolasını girin: veritabanı kullanıcı parolasını girin  
    Veritabanı kullanıcı (SISTEM) parolasını onaylayın: onaylamak için veritabanı kullanıcı parolasını yeniden girin  
    Makine yeniden başlatıldıktan sonra sistem yeniden başlatılsın mı? [n]: varsayılan değer kabul etmek için ENTER tuşuna basın  
    Devam etmek istiyor musunuz? (e/h): özeti doğrulayın. Devam etmek için **y** girin  

4. **[A]** SAP konak aracısını yükselt

   [SAP yazılım merkezi](https://launchpad.support.sap.com/#/softwarecenter) 'nden en son SAP konak Aracısı arşivini indirin ve aracıyı yükseltmek için aşağıdaki komutu çalıştırın. Arşiv yolunu, indirdiğiniz dosyayı işaret etmek için değiştirin:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** güvenlik duvarını yapılandırma

   Azure yük dengeleyici araştırma bağlantı noktası için güvenlik duvarı kuralı oluşturun.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>SAP HANA sistem çoğaltmasını yapılandırma

SAP HANA sistem çoğaltmasını yapılandırmak için [SAP HANA sistem çoğaltmasını](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) ayarlama bölümündeki adımları izleyin. 

## <a name="cluster-configuration"></a>Küme yapılandırması

Bu bölümde, Azure NetApp Files kullanılarak NFS paylaşımlarına SAP HANA yüklendiğinde kümenin sorunsuz çalışması için gereken adımlar açıklanmaktadır. 

### <a name="create-a-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma

Bu HANA sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere Azure 'daki [Red Hat Enterprise Linux Paceyapıcısı ayarlama](./high-availability-guide-rhel-pacemaker.md) bölümündeki adımları izleyin.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>Python sistem çoğaltma kancasını uygulama

Bu, kümeyle tümleştirmeyi iyileştirmek ve bir küme yük devretmesi gerektiğinde algılamayı iyileştirmek için önemli bir adımdır. SAPHanaSR Python kancasını yapılandırmak kesinlikle önerilir.    

1. **[A]** Hana "sistem çoğaltma kancasını" yükler. Kanca 'nin hem HANA DB düğümlerine yüklenmesi gerekir.           

   > [!TIP]
   > Python kancası yalnızca HANA 2,0 için uygulanabilir.        

   1. Kancasını olarak hazırlayın `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Her iki düğümde de HANA 'yı durdurun. <SID adm olarak Çalıştır \> :  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. `global.ini`Her küme düğümünde ayarlayın.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** küme, <SID adm için her küme düğümünde susers yapılandırması gerektirir \> . Bu örnekte, yeni bir dosya oluşturularak elde edilen. Komutları olarak yürütün `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** her iki düğümde de SAP HANA başlatın. <SID adm olarak yürütün \> .  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** kanca yüklemesini doğrulayın. \>ETKIN Hana sistem çoğaltma sitesinde <SID adm olarak yürütün.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

SAP HANA sistem çoğaltma kancasını uygulamayla ilgili daha fazla bilgi için bkz. [SAP ha/Dr sağlayıcı kancasını etkinleştirme](https://access.redhat.com/articles/3004101#enable-srhook).  

### <a name="configure-filesystem-resources"></a>Dosya sistemi kaynaklarını yapılandırma

Bu örnekte her küme düğümünün kendi HANA NFS dosya sistemleri/Hana/Shared,/Hana/Data ve/Hana/log ' a sahip olması gerekir.   

1. **[1]** kümeyi bakım moduna alın.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** **Hanadb1** takar için dosya sistemi kaynaklarını oluşturun.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** **Hanadb2** takar için dosya sistemi kaynaklarını oluşturun.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` özniteliği, her monitörün dosya sisteminde bir okuma/yazma testi gerçekleştirmesini sağlamak için izleyici işlemine eklenir. Bu öznitelik olmadan, izleme işlemi yalnızca FileSystem 'ın bağlı olduğunu doğrular. Bu bir sorun olabilir, çünkü bağlantı kaybediliyorsa dosya sistemi erişilemeyen şekilde bağlı kalabilir.

    `on-fail=fence` öznitelik, izleme işlemine de eklenir. Bu seçenekle, bir düğümde izleyici işlemi başarısız olursa, o düğüm hemen bir daha başarılı olur. Bu seçenek olmadan, varsayılan davranış, başarısız kaynağa bağlı tüm kaynakları durdurmak, sonra başarısız kaynağı yeniden başlatmak ve ardından başarısız kaynağa bağlı tüm kaynakları başlatmak olur. Bir SAPHana kaynağı başarısız kaynağa bağlı olduğunda bu davranış uzun zaman alabilir, ancak aynı zamanda tamamen başarısız olabilir. HANA yürütülebilir dosyalarını barındıran NFS sunucusuna erişilemiyorsa SAPHana kaynağı başarıyla durdurulamıyor.

4. **[1]** konum kısıtlamalarını yapılandırma

   Hanadb1 benzersiz takmaları yöneten kaynakların hanadb2 üzerinde hiçbir yerde çalıştırılbildiğinden emin olmak için konum kısıtlamalarını yapılandırın ve tam tersi de geçerlidir.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`Her düğüm için benzersiz takmaları aynı bağlama noktasını paylaştığından Bu seçenek ayarlanır. Örneğin, `hana_data1` bağlama noktası kullanır `/hana/data` ve `hana_data2` Ayrıca bağlama noktasını kullanır `/hana/data` . Bu durum, küme başlangıcında kaynak durumu işaretlendiğinde bir araştırma işlemi için yanlış pozitif duruma neden olabilir ve bu da gereksiz kurtarma davranışına neden olabilir. Bu ayar, `resource-discovery=never`

5. **[1]** öznitelik kaynaklarını yapılandırma

   Öznitelik kaynaklarını yapılandırın. Bu öznitelikler, bir düğümün NFS takmaları (/Hana/Data,/Hana/log ve/Hana/Data) bağlanacaksa true olarak ayarlanır ve aksi takdirde false olarak ayarlanır.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** konum kısıtlamalarını yapılandırma

   Hanadb1's öznitelik kaynağının hiçbir şekilde hanadb2 üzerinde çalışmadiğinden emin olmak için konum kısıtlamalarını yapılandırın ve tam tersi de geçerlidir.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** sıralama kısıtlamaları oluşturma

   Bir düğümün öznitelik kaynakları yalnızca tüm düğümün NFS takmaları bağlandıktan sonra başlayacak şekilde sıralama kısıtlamalarını yapılandırın.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Yapılandırmanız, grubun dışında dosya sistemleri içeriyorsa, `hanadb1_nfs` `hanadb2_nfs` `sequential=false` dosya sistemleri arasında sıralama bağımlılığı kalmayacak şekilde seçeneğini ekleyin. Tüm dosya sistemleri önce başlamalıdır `hana_nfs1_active` , ancak birbirleriyle ilgili herhangi bir sırada başlaması gerekmez. Daha fazla ayrıntı için bkz [. Hana dosya SISTEMLERI NFS paylaşımlardaytığında, bir Paceoluşturucu kümesinde Scale-Up SAP HANA sistem çoğaltmasını yapılandırma nasıl yaparım?](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>SAP HANA küme kaynaklarını yapılandırma

1. Kümedeki SAP HANA kaynaklarını oluşturmak için [SAP HANA kümesi kaynakları oluşturma](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) bölümündeki adımları izleyin. SAP HANA kaynakları oluşturulduktan sonra, SAP HANA kaynakları ve dosya sistemleri (NFS takmaları) arasında bir konum kuralı kısıtlaması oluşturmanız gerekir

2. **[1]** SAP HANA kaynaklarıyla NFS takmaları arasında kısıtlamalar yapılandırın

   Konum kuralı kısıtlamaları, SAP HANA kaynaklarının yalnızca tüm düğümün NFS takmaları bağlandığında bir düğümde çalıştırılabilmesi için ayarlanacak.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Kümenin ve tüm kaynakların durumunu kontrol edin
   > [!NOTE]
   > Bu makale, Microsoft 'un artık kullandığı bir terim olan *bağımlı* dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Pacemaker kümesinde HANA etkin/Read özellikli sistem çoğaltmasını yapılandırma

SAP HANA 2,0 ' den başlayarak, SAP HANA sistem çoğaltması için etkin/okuma etkin kurulum yapılmasına izin verir; burada, SAP HANA sistem çoğaltmasının ikincil sistemleri, okuma açısından yoğun iş yükleri için etkin bir şekilde kullanılabilir. Bir küme içinde bu tür kurulumu desteklemek için, istemcilerin ikincil okuma özellikli SAP HANA veritabanına erişmesini sağlayan ikinci bir sanal IP adresi gereklidir. Yük devralındıktan sonra ikincil çoğaltma sitesine hala erişilebildiğinden emin olmak için, kümenin sanal IP adresini SAPHana kaynağının ikincili ile taşıması gerekir.

İkinci sanal IP ile bir Red Hat yüksek kullanılabilirlik kümesinde HANA etkin/Read özellikli sistem çoğaltmasını yönetmek için gereken ek yapılandırma, [pacemaker KÜMESINDE Hana etkin/Read özellikli sistem çoğaltmasını yapılandırma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)bölümünde açıklanmaktadır.  

Devam etmeden önce, belgenin yukarıdaki segmentlerinde anlatıldığı gibi SAP HANA veritabanını yöneten Red Hat yüksek kullanılabilirlik kümesini tam olarak yapılandırdığınızdan emin olun.    


## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Bu bölüm, kurulumunuzu nasıl test kullanabileceğinizi açıklar. 

1. Bir teste başlamadan önce, pacemaker 'ın başarısız bir eyleme (bilgisayarların durumu aracılığıyla) sahip olmadığından emin olun, beklenmedik bir konum kısıtlaması olmadığından (örneğin, bir geçiş testinin kalan kısmını) ve HANA sistem çoğaltmasının eşitleme durumu olduğundan (örneğin, systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Bir düğüm NFS paylaşımında erişimi kaybettiğinde hata senaryosu için küme yapılandırmasını doğrulama (/Hana/Shared)

   SAP HANA kaynak aracıları, `/hana/shared` Yük devretme sırasında işlemleri gerçekleştirmek için üzerinde depolanan ikili dosyalara bağımlıdır. Dosya sistemi  `/hana/shared` , sunulan SENARYODA NFS üzerinden bağlanır.  
   Sunuculardan birinin NFS paylaşımının erişimini kaybettiğini bir hatanın benzetimini yapmak zordur. Gerçekleştirilebilecek bir test, dosya sistemini salt okunurdur olarak yeniden bağlamadır.
   Bu yaklaşım, etkin düğümde erişim kaybolursa kümenin yük devredebilmesini doğrular `/hana/shared` .     


   **Beklenen sonuç:** `/hana/shared` Salt okuma dosya sistemi olarak yapıldığında, dosya sisteminde `OCF_CHECK_LEVEL` herhangi bir `hana_shared1` şey yazamayacak ve Hana kaynak yük devretmesini gerçekleştirecek olan kaynak özniteliği, dosya sistemi üzerinde okuma/yazma işlemi gerçekleştiren kaynak özniteliği başarısız olur.  HANA düğümünüz NFS paylaşımlarına erişimi kaybettiğinde aynı sonuç beklenmektedir. 

   Teste başlamadan önce kaynak durumu:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   Şu komutu kullanarak/Hana/Shared öğesini, etkin küme düğümünde salt okuma moduna yerleştirebilirsiniz:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1, stonıth () üzerinde ayarlanan eyleme göre yeniden başlatılır veya devre dışı bırakılır `pcs property show stonith-action` .  Sunucu (hanadb1) bir kez daha olduktan sonra, HANA kaynağı hanadb2 'e taşınır. Kümenin durumunu hanadb2 adresinden kontrol edebilirsiniz.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Ayrıca, [RHEL üzerinde sistem çoğaltma SAP HANA kurulum](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)bölümünde açıklanan testleri gerçekleştirerek SAP HANA küme yapılandırmasını kapsamlı bir şekilde test etmenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](./hana-vm-operations-netapp.md)