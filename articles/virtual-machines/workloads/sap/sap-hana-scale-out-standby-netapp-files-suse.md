---
title: SLES 'de Azure NetApp Files ile birlikte bekleme ile genişleme SAP HANA | Microsoft Docs
description: SUSE Linux Enterprise Server Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümü ile SAP HANA genişleme sistemi dağıtmayı öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: radeltch
ms.openlocfilehash: 37a3923abf32259499d876074ceda29c27e87d3c
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551977"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Bu makalede, paylaşılan depolama birimleri için [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) kullanarak Azure sanal makinelerinde (VM) bekleme moduna sahip bir genişleme yapılandırmasında yüksek düzeyde kullanılabilir SAP HANA sisteminin nasıl dağıtılacağı açıklanır.  

Örnek yapılandırmalarda, yükleme komutlarında ve bu durumda, HANA örneği **03** ' dır ve Hana sistem kimliği **HN1**' dir. Örnekler, HANA 2,0 SP4 ve SUSE Linux Enterprise Server SAP 12 SP4 tabanlıdır. 

Başlamadan önce, aşağıdaki SAP notları ve incelemeleri inceleyin:

* [Azure NetApp Files belgeleri][anf-azure-doc] 
* SAP Note [1928533] şunları içerir:  
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure üzerinde Windows ve Linux için gereken SAP çekirdek sürümü
* SAP Note [2015553]: Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler
* SAP Note [2205917]: SAP uygulamaları için SUSE Linux Enterprise Server önerilen işletim sistemi ayarlarını içerir
* SAP Note [1944799]: SAP uygulamaları için SUSE LINUX ENTERPRISE Server Için SAP yönergeleri içerir
* SAP Note [2178632]: Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir
* SAP Note [2191498]: Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümünü içerir
* SAP Note [2243692]: Azure 'da LINUX 'ta SAP lisanslama hakkında bilgi içerir
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 hakkında genel bilgileri içerir
* SAP Note [1999351]: SAP Için Azure Gelişmiş izleme uzantısı ile ilgili ek sorun giderme bilgileri içerir
* SAP Note [1900823]: SAP HANA depolama gereksinimleriyle ilgili bilgiler içerir
* [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux için gereken tüm sap notlarını içerir
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SUSE SAP ha En Iyi Yöntem Kılavuzu][suse-ha-guide]: NetWeaver yüksek kullanılabilirliği ayarlamak için gerekli tüm bilgileri ve şirket Içi SAP HANA sistem çoğaltmasını (genel temel olarak kullanılmak üzere) ve daha ayrıntılı bilgiler sağlar.
* [SUSE yüksek kullanılabilirlik uzantısı 12 SP3 sürüm notları][suse-ha-12sp3-relnotes]
* [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları][anf-sap-applications-azure]
* [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Genel Bakış

HANA yüksek kullanılabilirliği elde etmek için bir yöntem, konak otomatik yük devretmeyi yapılandırmasıdır. Konak otomatik yük devretmeyi yapılandırmak için, HANA sistemine bir veya daha fazla sanal makine ekler ve bunları bekleme düğümleri olarak yapılandırırsınız. Etkin düğüm başarısız olduğunda, bir bekleme düğümü otomatik olarak alır. Azure sanal makineler ile sunulan yapılandırmada, [Azure NetApp Files üzerinde NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md)kullanarak otomatik yük devretme elde edersiniz.  

> [!NOTE]
> Bekleme düğümünün tüm veritabanı birimlerine erişmesi gerekir. HANA birimlerinin NFSv4 birimleri olarak bağlanması gerekir. NFSv4 protokolündeki geliştirilmiş dosya Kiralama tabanlı kilitleme mekanizması, balıklığa karşı kullanılır `I/O` . 

> [!IMPORTANT]
> Desteklenen yapılandırmayı derlemek için, HANA verilerini ve günlük birimlerini NFSv 4.1 birimleri olarak dağıtmanız ve NFSv 4.1 protokolünü kullanarak bağlamanız gerekir. NFSv3 ile HANA konak otomatik yük devretme yapılandırması, bekleme düğümüyle desteklenmez.

![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Önceki diyagramda SAP HANA ağ önerilerini izleyen bir Azure sanal ağı içinde üç alt ağ temsil edilir: 
* İstemci iletişimi için
* Depolama sistemiyle iletişim için
* Dahili HANA düğümler arası iletişim için

Azure NetApp birimleri, [Azure NetApp Files atanan](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)ayrı alt ağdadır.  

Bu örnek yapılandırma için alt ağlar şunlardır:  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files altyapısını ayarlama 

Azure NetApp Files altyapısına yönelik kuruluma devam etmeden önce [Azure NetApp Files belgeleri][anf-azure-doc]hakkında bilgi edinin. 

Azure NetApp Files çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)kullanılabilir. Seçtiğiniz Azure bölgesinin Azure NetApp Files sunmadığını denetleyin.  

Azure bölgesine göre Azure NetApp Files kullanılabilirliği hakkında daha fazla bilgi için bkz. [Azure bölgesine göre Azure NetApp Files kullanılabilirliği][anf-avail-matrix].  

Azure NetApp Files dağıtmadan önce [Azure NetApp Files yönergeler Için kaydolun][anf-register]bölümüne giderek Azure NetApp Files ekleme isteyin. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files kaynaklarını dağıtma  

Aşağıdaki yönergelerde, [Azure Sanal ağınızı](../../../virtual-network/virtual-networks-overview.md)zaten dağıttığınız varsayılmaktadır. Azure NetApp Files kaynakların dağıtılacağı Azure NetApp Files kaynakları ve VM 'Ler, aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında dağıtılmalıdır.  

1. Kaynakları henüz dağıtmadıysanız [Azure NetApp Files ekleme](../../../azure-netapp-files/azure-netapp-files-register.md)isteyin.  

2. [Bir NetApp hesabı oluşturma](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)bölümündeki yönergeleri Izleyerek seçtiğiniz Azure bölgesindeki bir NetApp hesabı oluşturun.  

3. [Bir Azure NetApp Files kapasite havuzu ayarlama](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)bölümündeki yönergeleri izleyerek Azure NetApp Files bir kapasite havuzu ayarlayın.  

   Bu makalede sunulan HANA mimarisi, *Ultra hizmet* düzeyinde tek bir Azure NetApp Files kapasite havuzu kullanır. Azure 'daki HANA iş yükleri için Azure NetApp Files *Ultra* veya *Premium* [hizmet düzeyi](../../../azure-netapp-files/azure-netapp-files-service-levels.md)kullanmanızı öneririz.  

4. [Azure NetApp Files için bir alt ağ devretmek](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)içindeki yönergelerde açıklandığı gibi Azure NetApp Files için bir alt ağ atayın.  

5. [Azure NetApp Files IÇIN NFS birimi oluşturma](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)bölümündeki yönergeleri izleyerek Azure NetApp Files birimleri dağıtın.  

   Birimleri dağıtmakta olduğunuz gibi, **Nfsv 4.1** sürümünü seçtiğinizden emin olun. Şu anda, NFSv 4.1 erişimi bir izin verilenler listesine eklenmelidir. Birimleri belirlenen Azure NetApp Files [alt ağına](/rest/api/virtualnetwork/subnets)dağıtın. Azure NetApp birimlerinin IP adresleri otomatik olarak atanır. 
   
   Azure NetApp Files kaynaklarının ve Azure VM 'lerinin aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında olması gerektiğini unutmayın. Örneğin, **HN1**-Data-Mnt00001, **HN1**-log-mnt00001 ve benzeri, birim adları ve NFS://10.23.1.5/**HN1**-Data-mnt00001, NFS://10.23.1.4/**HN1**-log-mnt00001, vb., Azure NetApp Files birimlerinin dosya yollarıdır.  

   * Volume **HN1**-Data-mnt00001 (NFS://10.23.1.5/**HN1**-Data-mnt00001)
   * Volume **HN1**-Data-mnt00002 (NFS://10.23.1.6/**HN1**-Data-mnt00002)
   * Volume **HN1**-log-mnt00001 (NFS://10.23.1.4/**HN1**-log-mnt00001)
   * Volume **HN1**-log-mnt00002 (NFS://10.23.1.6/**HN1**-log-mnt00002)
   * Volume **HN1**-shared (NFS://10.23.1.4/**HN1**-Shared)
   
   Bu örnekte, her bir HANA verisi ve günlük birimi için ayrı bir Azure NetApp Files birimi kullandık. Daha küçük veya üretken olmayan sistemlerde daha uygun maliyetli bir yapılandırma için, tüm veri takmaları ve tüm günlüklerin tek bir birime takar.  

### <a name="important-considerations"></a>Önemli noktalar

SUSE yüksek kullanılabilirlik mimarisinde SAP NetWeaver için Azure NetApp Files oluştururken aşağıdaki önemli noktalara dikkat edin:

- En düşük kapasite havuzu 4 tebibayt (Tib).  
- En küçük birim boyutu 100 Gibibyte (gib).
- Azure NetApp Files ve Azure NetApp Files birimlerinin takılabileceği tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](../../../virtual-network/virtual-network-peering-overview.md) olmalıdır.  
- Seçilen sanal ağ Azure NetApp Files için temsilci atanmış bir alt ağa sahip olmalıdır.
- Azure NetApp Files bir birimin verimlilik, [Azure NetApp Files Için hizmet düzeyinde](../../../azure-netapp-files/azure-netapp-files-service-levels.md)belgelendiği gibi birim kotasının ve hizmet düzeyinin bir işlevidir. HANA Azure NetApp birimlerini boyutlandırdığınızda, sonuçta elde edilen aktarım hızı HANA sistem gereksinimlerini karşıladığından emin olun.  
- Azure NetApp Files [dışarı aktarma ilkesiyle](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)izin verilen istemcileri, erişim türünü (okuma-yazma, salt okuma vb.) denetleyebilirsiniz. 
- Azure NetApp Files özelliği henüz bölge duyarlı değildir. Şu anda, özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmaz. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun.  
-  

> [!IMPORTANT]
> SAP HANA iş yükleri için düşük gecikme süresi kritik öneme sahiptir. Sanal makinelerin ve Azure NetApp Files birimlerinin yakın bir yerde dağıtıldığından emin olmak için Microsoft temsilcinizle birlikte çalışın.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA veritabanı için boyutlandırma

Azure NetApp Files bir birimin verimlilik, [Azure NetApp Files Için hizmet düzeyinde](../../../azure-netapp-files/azure-netapp-files-service-levels.md)belgelendiği gibi birim boyutu ve hizmet düzeyi işlevindedir. 

SAP altyapısını Azure 'da tasarlarken, en düşük işleme özelliklerine çeviren SAP 'ye göre bazı minimum depolama gereksinimlerini göz önünde bulundurun:

- 1 MB g/ç boyutlarına sahip/Hana/log/saniyede 250 megabayt (MB/s) üzerinde okuma/yazma özelliğini etkinleştirin.  
- 16 MB ve 64-MB g/ç boyutları için/Hana/Data için en az 400 MB/sn okuma etkinliğini etkinleştirin.  
- 16 MB ve 64-MB g/ç boyutları ile/Hana/Data için en az 250 MB/sn yazma etkinliğini etkinleştirin. 

Birim kotasının 1 TiB başına [Azure NetApp Files verimlilik limitleri](../../../azure-netapp-files/azure-netapp-files-service-levels.md) şunlardır:
- Premium depolama katmanı-64 MIB/s  
- Ultra depolama katmanı-128 MIB/sn  

Veri ve günlük SAP minimum aktarım hızı gereksinimlerini ve/Hana/Shared yönergelerini karşılamak için önerilen boyutlar şöyle olacaktır:

| Birim | Boyut<br>Premium depolama katmanı | Boyut<br>Ultra depolama katmanı | Desteklenen NFS Protokolü |
| --- | --- | --- | --- |
| /Hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /Hana/Data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/Shared | 4 çalışan düğümü başına en fazla (512 GB, 1xRAM) | 4 çalışan düğümü başına en fazla (512 GB, 1xRAM) | V3 veya v 4.1 |

Bu makalede sunulan düzen SAP HANA yapılandırması, Azure NetApp Files Ultra Storage katmanını kullanarak şöyle olacaktır:

| Birim | Boyut<br>Ultra depolama katmanı | Desteklenen NFS Protokolü |
| --- | --- | --- |
| /Hana/log/mnt00001 | 2 TiB | v 4.1 |
| /Hana/log/mnt00002 | 2 TiB | v 4.1 |
| /Hana/Data/mnt00001 | 3,2 TiB | v 4.1 |
| /Hana/Data/mnt00002 | 3,2 TiB | v 4.1 |
| /Hana/Shared | 2 TiB | V3 veya v 4.1 |

> [!NOTE]
> Burada belirtilen Azure NetApp Files boyutlandırma önerileri, SAP 'nin altyapı sağlayıcıları için önerdiği minimum gereksinimleri karşılamaya yöneliktir. Gerçek müşteri dağıtımları ve iş yükü senaryolarında bu boyutlar yeterli olmayabilir. Bu önerileri bir başlangıç noktası olarak kullanın ve belirli iş yükünüzün gereksinimlerine göre uyarlayın.  

> [!TIP]
> Birimleri *çıkarmanız* , sanal makineleri durdurmanız veya SAP HANA durdurmanız gerekmeden Azure NetApp Files birimleri dinamik olarak yeniden boyutlandırabilirsiniz. Bu yaklaşım, uygulamanızın hem beklenen hem de öngörülemeyen işleme taleplerini karşılamak için esneklik sağlar.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Linux sanal makinelerini Azure portal aracılığıyla dağıtma

Öncelikle Azure NetApp Files birimleri oluşturmanız gerekir. Ardından aşağıdaki adımları uygulayın:
1. Azure sanal ağınızda [Azure sanal ağ alt ağları](../../../virtual-network/virtual-network-manage-subnet.md) [oluşturun.](../../../virtual-network/virtual-networks-overview.md) 
1. VM 'Leri dağıtın. 
1. Ek ağ arabirimlerini oluşturun ve ağ arabirimlerini ilgili VM 'lere ekleyin.  

   Her sanal makine üç Azure sanal ağ alt ağına (ve) karşılık gelen üç ağ arabirimine sahiptir `client` `storage` `hana` . 

   Daha fazla bilgi için bkz. [Azure 'da birden çok ağ arabirimi kartı Ile Linux sanal makinesi oluşturma](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> SAP HANA iş yükleri için düşük gecikme süresi kritik öneme sahiptir. Düşük gecikme süresi elde etmek için, sanal makinelerin ve Azure NetApp Files birimlerinin yakın bir yerde dağıtıldığından emin olmak için Microsoft temsilcinizle birlikte çalışın. SAP HANA Azure NetApp Files kullanan [yeni SAP HANA sistemi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) eklediğinizde gerekli bilgileri iletin. 
 
Sonraki yönergelerde, kaynak grubunu, Azure sanal ağını ve üç Azure sanal ağ alt ağını zaten oluşturmuş olduğunuz varsayılmaktadır: `client` , `storage` ve `hana` . VM 'Leri dağıtırken, istemci ağ arabiriminin VM 'lerde birincil arabirim olması için istemci alt ağını seçin. Ayrıca, depolama alt ağı ağ geçidi aracılığıyla Azure NetApp Files atanmış alt ağa açık bir yol yapılandırmanız gerekecektir. 

> [!IMPORTANT]
> Seçtiğiniz işletim sisteminin, kullanmakta olduğunuz belirli VM türlerinde SAP HANA için SAP sertifikalı olduğundan emin olun. Bu türlere yönelik SAP HANA sertifikalı VM türlerinin ve işletim sistemi sürümlerinin listesi için, [SAP HANA sertifikalı IaaS platformları](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) sitesine gidin. Söz konusu türe yönelik SAP HANA tarafından desteklenen işletim sistemi sürümlerinin tam listesini almak için listelenen VM türünün ayrıntılarına tıklayın.  

1. SAP HANA için bir kullanılabilirlik kümesi oluşturun. En fazla güncelleştirme etki alanını ayarladığınızdan emin olun.  

2. Aşağıdaki adımları uygulayarak üç sanal makine (**hanadb1**, **hanadb2**, **hanadb3**) oluşturun:  

   a. Azure galerisinde SAP HANA için desteklenen bir SLES4SAP görüntüsü kullanın. Bu örnekte SLES4SAP 12 SP4 görüntüsü kullandık.  

   b. Daha önce SAP HANA için oluşturduğunuz kullanılabilirlik kümesini seçin.  

   c. İstemci Azure sanal ağ alt ağını seçin. [Hızlandırılmış ağ](../../../virtual-network/create-vm-accelerated-networking-cli.md)' ı seçin.  

   Sanal makineleri dağıtırken, ağ arabirimi adı otomatik olarak oluşturulur. Kolaylık sağlaması için Bu yönergelerde, **hanadb1-Client**, **hanadb2-Client** ve **Hanadb3-Client** şeklinde istemci Azure sanal ağ alt ağına eklenen otomatik olarak oluşturulan ağ arabirimlerine başvuracağız. 

3. Sanal ağ alt ağı için bir tane olmak üzere, her bir sanal makine için bir tane olmak üzere üç ağ arabirimi oluşturun `storage` (Bu örnekte, **hanadb1-Storage**, **hanadb2-** Storage ve **hanadb3-Storage**).  

4. Sanal ağ alt ağı için bir tane olmak üzere, her bir sanal makine için bir tane olmak üzere üç ağ arabirimi oluşturun `hana`  (Bu örnekte, **hanadb1-Hana**, **hanadb2-Hana** ve **hanadb3-Hana**).  

5. Aşağıdaki adımları uygulayarak, yeni oluşturulan sanal ağ arabirimlerini karşılık gelen sanal makinelere ekleyin:  

    a. [Azure Portal](https://portal.azure.com/#home)sanal makineye gidin.  

    b. Sol bölmede **sanal makineler**' i seçin. Sanal makine adını (örneğin, **hanadb1**) filtreleyin ve ardından sanal makineyi seçin.  

    c. **Genel bakış** bölmesinde, sanal makineyi serbest bırakmak için **Durdur** ' u seçin.  

    d. Ağ **' ı** seçin ve ardından ağ arabirimini ekleyin. **Ağ arabirimi Ekle** aşağı açılan listesinde, `storage` ve alt ağları için önceden oluşturulmuş ağ arabirimlerini seçin `hana` .  
    
    e. **Kaydet**’i seçin. 
 
    f. Kalan sanal makineler için b ile e arasındaki adımları yineleyin (bizim örneğimizde,  **hanadb2** ve **hanadb3**).
 
    örneğin: Sanal makineleri şimdilik durdurulmuş durumda bırakın. Daha sonra, yeni eklenen tüm ağ arabirimleri için [hızlandırılmış ağı](../../../virtual-network/create-vm-accelerated-networking-cli.md) etkinleştireceğiz.  

6. `storage` `hana` Aşağıdaki adımları uygulayarak ve alt ağları için ek ağ arabirimleri için hızlandırılmış ağı etkinleştirin:  

    a. Azure portal [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) açın. [](https://portal.azure.com/#home)  

    b. `storage`Ve alt ağlarına eklenen ek ağ arabirimleri için hızlandırılmış ağı etkinleştirmek üzere aşağıdaki komutları yürütün `hana` .  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Aşağıdaki adımları uygulayarak sanal makineleri başlatın:  

    a. Sol bölmede **sanal makineler**' i seçin. Sanal makine adını (örneğin, **hanadb1**) filtreleyin ve ardından seçin.  

    b. **Genel bakış** bölmesinde **Başlat**' ı seçin.  

## <a name="operating-system-configuration-and-preparation"></a>İşletim sistemi yapılandırması ve hazırlığı

Sonraki bölümlerdeki yönergelere aşağıdakilerden biri eklenir:
* **[A]**: tüm düğümlere uygulanabilir
* **[1]**: yalnızca düğüm 1 için geçerlidir
* **[2]**: yalnızca düğüm 2 ' de geçerlidir
* **[3]**: yalnızca düğüm 3 ' e uygulanabilir

Aşağıdaki adımları uygulayarak işletim sistemini yapılandırın ve hazırlayın:

1. **[A]** sanal makinelerdeki konak dosyalarını koruyun. Tüm alt ağların girdilerini dahil edin. Bu örnek için aşağıdaki girişler eklenmiştir `/etc/hosts` .  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** istenmeyen ana bilgisayar adından kaçınmak üzere depolama için ağ ARABIRIMI için DHCP ve bulut yapılandırması ayarlarını değiştirin.  

    Aşağıdaki yönergelerde, depolama ağ arabiriminin olduğu varsayılır `eth1` . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** bir ağ yolu ekleyin, böylece Azure NetApp Files iletişim, depolama ağı arabiriminden geçer.  

    Aşağıdaki yönergelerde, depolama ağ arabiriminin olduğu varsayılır `eth1` .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Değişiklikleri etkinleştirmek için VM 'yi yeniden başlatın.  

3. **[A]** işletim sistemini, [Azure NetApp Files kullanarak MICROSOFT Azure NetApp SAP uygulamalarında][anf-sap-applications-azure]açıklandığı gibi, NFS ile NetApp sistemlerinde SAP HANA çalıştırmak için hazırlayın. NetApp yapılandırma ayarları için */etc/sysctl.exe d/NetApp-Hana-conf* yapılandırma dosyası oluşturun.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Azure yapılandırma ayarları için Microsoft ile */etc/sysctl.exe. d/MS-az.exe conf* yapılandırma dosyası oluşturun.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> SAP konak aracısının bağlantı noktası aralıklarını yönetmesine izin vermek için net.ipv4.ip_local_port_range ve net.ipv4.ip_local_reserved_ports açıkça sysctl yapılandırma dosyalarında ayarlamayı önleyin. Daha fazla bilgi için bkz. SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

4. **[A]** [Azure NetApp Files kullanarak MICROSOFT Azure NetApp SAP uygulamalarında][anf-sap-applications-azure]önerildiği gibi sunrpc ayarlarını ayarlayın.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp Files birimleri bağlama

1. **[A]** Hana veritabanı birimleri için bağlama noktaları oluşturun.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **HN1**-Shared üzerinde/usr/SAP için düğüme özel dizinler oluşturun.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS etki alanı ayarını doğrulayın. Etki alanının varsayılan Azure NetApp Files etki alanı olarak yapılandırıldığından emin olun, yani **`defaultv4iddomain.com`** ve eşleme **hiç kimse** olarak ayarlanmıştır.  

    > [!IMPORTANT]
    > VM 'de NFS etki alanını `/etc/idmapd.conf` Azure NetApp Files ' deki varsayılan etki alanı yapılandırmasıyla eşleşecek şekilde ayarladığınızdan emin olun: **`defaultv4iddomain.com`** . NFS istemcisindeki (yani, VM) ve NFS sunucusunun etki alanı yapılandırması arasında uyuşmazlık varsa (örneğin, Azure NetApp yapılandırması), VM 'Lere bağlı Azure NetApp birimlerinde dosya izinleri olarak görüntülenir `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** doğrula `nfs4_disable_idmapping` . **Y** olarak ayarlanmalıdır. Bulunduğu dizin yapısını oluşturmak için `nfs4_disable_idmapping` Mount komutunu yürütün. Erişim çekirdek/sürücü için ayrıldığından,/sys/modules altında dizini el ile oluşturamazsınız.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** SAP HANA grubunu ve kullanıcıyı el ile oluşturun. Bu grup sapsys ve Kullanıcı **hn1** adm kimlikleri, ekleme sırasında belirtilen kimliklere ayarlanmalıdır. (Bu örnekte, kimlikler **1001** olarak ayarlanır.) Kimlikler doğru şekilde ayarlanmamışsa birimlere erişemezsiniz. Grup sapsys ve Kullanıcı hesapları **hn1** adm ve Sapadm kimlikleri tüm sanal makinelerde aynı olmalıdır.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** paylaşılan Azure NetApp Files birimlerini bağlama.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** düğüme özgü birimleri **hanadb1** üzerinde bağlayın.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** düğüme özgü birimleri **hanadb2** üzerine bağlayın.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** düğüme özgü birimleri **hanadb3** üzerinde bağlayın.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** tüm Hana birimlerinin NFS protokol sürümü **NFSv4** bağlandığından emin olun.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Yükleme  

Bu örnekte, Azure ile bekleme moduna sahip genişleme yapılandırmasında SAP HANA dağıtmak için HANA 2,0 SP4 kullandık.  

### <a name="prepare-for-hana-installation"></a>HANA yüklemesine hazırlanma

1. **[A]** Hana yüklemesinden önce kök parolasını ayarlayın. Yükleme tamamlandıktan sonra kök parolayı devre dışı bırakabilirsiniz. Farklı Çalıştır `root` komutu `passwd` .  

2. **[1]** bir parola istenmeden **hanadb2** ve **hanadb3** için SSH aracılığıyla oturum açabildiğinizi doğrulayın.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Hana 2,0 SP4 için gerekli olan ek paketleri yükler. Daha fazla bilgi için bkz. SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** SAP HANA `data` ve `log` dizinlerin sahipliğini **hn1** adm olarak değiştirin.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA yüklemesi

1. **[1]** [SAP HANA 2,0 yükleme ve güncelleştirme kılavuzundaki](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)yönergeleri izleyerek SAP HANA yükleme. Bu örnekte, ana, bir çalışan ve tek bir bekleme düğümüyle SAP HANA genişleme bir şekilde yüklenir.  

   a. HANA yükleme yazılımı dizininden **hdblcm** programını başlatın. Parametresini kullanın `internal_network` ve Iç Hana düğümler arası iletişim için kullanılan alt ağ için adres alanını geçirin.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. İsteminde aşağıdaki değerleri girin:

     * **Eylem Seç** için: **1** girin (Install için)
     * **Yükleme Için ek bileşenler** için: **2, 3** girin
     * Yükleme yolu için: ENTER tuşuna basın (varsayılan olarak/Hana/Shared değerini alır)
     * **Yerel ana bilgisayar adı** için: varsayılan değer kabul etmek için ENTER tuşuna basın
     * **Sisteme ana bilgisayar eklemek istiyor musunuz?**: **y** girin
     * **Eklenecek virgülle ayrılmış ana bilgisayar adları** için: ENTER **hanadb2, hanadb3**
     * **Kök Kullanıcı adı** [root]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Kök kullanıcı parolası** için: kök kullanıcının parolasını girin
     * Host hanadb2 için roller için: **1**  girin (çalışan için)
     * Host hanadb2 için **konak yük devretme grubu** için [varsayılan]: varsayılan değer kabul etmek için ENTER tuşuna basın
     * Host hanadb2 için **depolama bölüm numarası** [<<assign automatically>>]: varsayılanı kabul etmek Için ENTER tuşuna basın
     * Host hanadb2 için **çalışan grubu** için [varsayılan]: varsayılan değer kabul etmek için ENTER tuşuna basın
     * Host hanadb3 için **seçme rolleri** için: **2** girin (bekleme için)
     * Host hanadb3 için **konak yük devretme grubu** için [varsayılan]: varsayılan değer kabul etmek için ENTER tuşuna basın
     * Host hanadb3 için **çalışan grubu** için [varsayılan]: varsayılan değer kabul etmek için ENTER tuşuna basın
     * **SAP HANA SISTEM kimliği** Için: **HN1** girin
     * **Örnek numarası** [00]: **03** girin
     * **Yerel konak çalışan grubu** için [varsayılan]: varsayılan değer kabul etmek için ENTER tuşuna basın
     * **Sistem kullanımını Seç/dizini girin [4]**: **4** girin (özel için)
     * **Veri birimlerinin konumu** için [/Hana/Data/hn1]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Günlük birimlerinin konumu** için [/Hana/log/hn1]: varsayılanı kabul etmek için ENTER tuşuna basın
     * **Maksimum bellek ayırmayı kısıtlamak için?** [n]: **n** girin
     * Host hanadb1 [hanadb1] **Için sertifika ana bilgisayar adı** : varsayılanı kabul etmek için ENTER tuşuna basın
     * Host hanadb2 [hanadb2] **Için sertifika ana bilgisayar adı** : varsayılanı kabul etmek için ENTER tuşuna basın
     * Host hanadb3 [hanadb3] **Için sertifika ana bilgisayar adı** : varsayılanı kabul etmek için ENTER tuşuna basın
     * **Sistem Yöneticisi (hn1adm) parolası** için: parolayı girin
     * **Sistem veritabanı kullanıcısı (sistem) parolası** için: sistemin parolasını girin
     * **Sistem veritabanı kullanıcı (sistem) parolasını onaylayın**: sistem parolasını girin
     * **Makine yeniden başlatıldıktan sonra sistem yeniden başlatma için mi?** [n]: **n** girin 
     * **Devam etmek Istiyor musunuz (e/h)**: Özeti doğrulayıp her şey iyi görünüyorsa **y** girin


2. **[1]** Verify global.ini  

   global.ini görüntüleyin ve iç SAP HANA düğümler arası iletişimin yapılandırmasının yerinde olduğundan emin olun. **İletişim** bölümünü doğrulayın. Alt ağ için adres alanına sahip olmalıdır `hana` ve `listeninterface` olarak ayarlanmalıdır `.internal` . **İnternal_hostname_resolution** bölümünü doğrulayın. Bu, alt ağa ait olan HANA sanal makinelerinin IP adreslerine sahip olmalıdır `hana` .  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** istemci IP adreslerinin istemci iletişimi için kullanıldığından emin olmak için konak eşlemesi ekleyin. Bölüm ekleyin `public_host_resolution` ve istemci alt ağından ılgılı IP adreslerini ekleyin.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** değişiklikleri etkinleştirmek için SAP HANA yeniden başlatın.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** istemci arabiriminin `client` iletişim IÇIN alt ağdan IP adreslerini kullandığını doğrulayın.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Yapılandırmayı doğrulama hakkında daha fazla bilgi için bkz. SAP Note [2183363-SAP HANA iç ağ yapılandırması](https://launchpad.support.sap.com/#/notes/2183363).  

6. Temel Azure NetApp Files depolama SAP HANA iyileştirmek için aşağıdaki SAP HANA parametrelerini ayarlayın:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**üzerinde**
   - `async_write_submit_active`**üzerinde**
   - `async_write_submit_blocks`**Tümü**

   Daha fazla bilgi için [Azure NetApp Files kullanarak Microsoft Azure NetApp SAP uygulamaları][anf-sap-applications-azure]bölümüne bakın. 

   SAP HANA 2,0 sistemlerinden başlayarak, içindeki parametreleri ayarlayabilirsiniz `global.ini` . Daha fazla bilgi için bkz. SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   SAP HANA 1,0 sistem sürümleri SPS12 ve önceki sürümlerinde, bu parametreler yükleme sırasında SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798)' de açıklandığı gibi ayarlanabilir.  

7. Azure NetApp Files tarafından kullanılan depolamanın, 16 terabayta (TB) ait bir dosya boyutu sınırlaması vardır. SAP HANA, depolama sınırlamasından örtük bir şekilde haberdar değildir ve 16 TB 'lık dosya boyutu sınırına ulaşıldığında otomatik olarak yeni bir veri dosyası oluşturmaz. SAP HANA, 16 TB 'ın ötesinde dosyayı büyütmeye çalışırsa, bu girişim hatalara ve sonunda bir dizin sunucusu kilitlenmesine neden olur. 

   > [!IMPORTANT]
   > SAP HANA, depolama alt sisteminin [16 TB sınırının](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) ötesinde veri dosyalarını büyümeye çalışmasını engellemek için, içinde aşağıdaki parametreleri ayarlayın `global.ini` .  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 daha fazla bilgi Için bkz. SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285)' i unutmayın. 

## <a name="test-sap-hana-failover"></a>Test SAP HANA yük devretme 

> [!NOTE]
> Bu makale, *ana* ve *bağımlı* hüküm, Microsoft 'un artık kullandığı koşullara yönelik başvurular içerir. Bu koşullar yazılımdan kaldırıldığında, bu makaleden kaldıracağız.

1. SAP HANA çalışan düğümünde düğüm kilitlenmesinin benzetimini yapın. Şunları yapın: 

   a. Düğüm kilitlenmesinin benzetimini yapmadan önce, ortamın durumunu yakalamak için aşağıdaki komutları **hn1** adm olarak çalıştırın:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Düğüm kilitlenmesinin benzetimini yapmak için, bu örnekte **hanadb2** olan çalışan düğümünde kök olarak aşağıdaki komutu çalıştırın:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Yük devretme tamamlamayı sistem için izleyin. Yük devretme tamamlandığında, durumu yakala ve aşağıdaki gibi görünmelidir:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Bir düğüm çekirdek Panic ile karşılaştığında, `kernel.panic` *Tüm* Hana sanal makinelerinde 20 saniyeye ayarlayarak SAP HANA yük devretmeyle gecikmelerden kaçının. Yapılandırma tarihinde yapılır `/etc/sysctl` . Değişikliği etkinleştirmek için sanal makineleri yeniden başlatın. Bu değişiklik yapılmadıysa, bir düğüm çekirdek Panic ile karşılaşıyorsa yük devretme 10 veya daha fazla dakika sürebilir.  

2. Aşağıdaki işlemleri gerçekleştirerek ad sunucusunu sonlandırın:

   a. Testten önce, **hn1** adm olarak aşağıdaki komutları çalıştırarak ortamın durumunu kontrol edin:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Bu durumda **hanadb1** olan etkin ana düğümde **hn1** adm olarak aşağıdaki komutları çalıştırın:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Bekleme düğümü **hanadb3** , ana düğüm olarak ele alınacaktır. Yük devretme testi tamamlandıktan sonra kaynak durumu aşağıda verilmiştir:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. **Hanadb1** (diğer bir deyişle, ad sunucusunun sonlandıralındığı sanal MAKINEDE) Hana örneğini yeniden başlatın. **Hanadb1** düğümü, ortama yeniden katılacaktır ve bekleme rolünü tutar.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb1** üzerinde SAP HANA başlatıldıktan sonra, aşağıdaki durumu bekler:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Daha sonra, şu anda etkin olan ana düğümdeki (yani, Node **hanadb3**) ad sunucusunu sonlandırın.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Node **hanadb1** , ana düğümün rolünü sürdürecek. Yük devretme testi tamamlandıktan sonra durum şöyle görünür:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. **Hanadb3** üzerinde SAP HANA başlatın, bu, bekleme düğümü olarak hazırlanmaya hazır olacak.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb3** üzerinde SAP HANA başlatıldıktan sonra durum aşağıdaki gibi görünür:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](./hana-vm-operations-netapp.md)
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha].