---
title: Azure sanal makine ANF yapılandırmasını SAP HANA | Microsoft Docs
description: SAP HANA için depolama önerilerini Azure NetApp Files.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, anlık görüntü
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666610"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri

Azure NetApp Files, **/Hana/Shared**, **/Hana/Data** ve **/Hana/log** birimleri için kullanılabilen yerel NFS paylaşımları sağlar. **/Hana/Data** ve **/Hana/log** birimleri IÇIN ANF tabanlı NFS paylaşımlarının KULLANıLMASı, v 4.1 NFS protokolünün kullanımını gerektirir. NFS Protokolü v3, paylaşımları ANF üzerinde dayandırırken **/Hana/Data** ve **/Hana/log** birimlerinin kullanımı için desteklenmez. 


> [!IMPORTANT]
> Azure NetApp Files uygulanan NFS v3 protokolünün **/Hana/Data** ve **/Hana/log** için **kullanılması desteklenmez.** NFS 4,1 kullanımı, **/Hana/Data** ve **/Hana/log** birimleri için bir işlev noktasından bir görünüm açısından zorunludur. Öte yandan, **/Hana/Shared** BIRIMI için NFS v3 veya NFS v 4.1 protokolü, bir görünüm noktasından kullanılabilir.

## <a name="important-considerations"></a>Önemli noktalar

SAP NetWeaver ve SAP HANA için Azure NetApp Files düşünürken, aşağıdaki önemli noktalara dikkat edin:

- En düşük kapasite havuzu 4 TiB 'dir  
- En küçük birim boyutu 100 GiB 'dir
- Azure NetApp Files ve Azure NetApp Files birimlerinin bağlı olduğu tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](../../../virtual-network/virtual-network-peering-overview.md) olmalıdır
- Düşük gecikme süresi boyunca sanal makinelerin Azure NetApp depolama alanına yakın bir şekilde dağıtılmasını sağlamak önemlidir.  
- Seçilen sanal ağın bir alt ağı olmalıdır, Azure NetApp Files atanmış
- Veritabanı sunucusundan ANF birimine gecikme süresinin ölçüldüğü ve 1 milisaniyenin altında olduğundan emin olun
- Azure NetApp biriminin performansı, [Azure NetApp Files Için hizmet düzeyinde](../../../azure-netapp-files/azure-netapp-files-service-levels.md)belgelendiği gibi birim kotasının ve hizmet düzeyinin bir işlevidir. HANA Azure NetApp birimlerini boyutlandırdığınızda, elde edilen aktarım hızının HANA sistem gereksinimlerini karşıladığından emin olun
- Daha büyük bir birimde daha fazla performans elde etmek için "birleştirme" birimlerini deneyin. Örneğin,/sapmnt,/usr/SAP/Trans,... için bir birim kullanın. Mümkünse  
- Azure NetApp Files, [dışarı aktarma ilkesi](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)sunar: izin verilen istemcileri, erişim türünü (okuma&yazma, salt okuma, vb.) denetleyebilirsiniz. 
- Azure NetApp Files Özellik henüz bölge farkında değildir. Şu anda Azure NetApp Files özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmaz. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun.   
- <b>SID</b>adm IÇIN Kullanıcı kimliği ve sanal MAKINELERDEKI grup kimliği, `sapsys` Azure NetApp Files yapılandırma ile aynı olmalıdır. 

> [!IMPORTANT]
> SAP HANA iş yükleri için düşük gecikme süresi kritik öneme sahiptir. Sanal makinelerin ve Azure NetApp Files birimlerinin yakın bir yerde dağıtıldığından emin olmak için Microsoft temsilcinizle birlikte çalışın.  

> [!IMPORTANT]
> <b>SID</b>adm Kullanıcı kimliği ve `sapsys` sanal makine Ile Azure NetApp YAPıLANDıRMASı arasındaki grup kimliği arasında UYUŞMAZLıK varsa, VM 'ye bağlanan Azure NetApp birimlerindeki dosyaların izinleri olarak görüntülenecektir `nobody` . <b></b> `sapsys` Azure NetApp Files için [Yeni bir sistem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) oluştururken, SID adm ve grup kimliği için doğru Kullanıcı kimliğini belirttiğinizden emin olun.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA veritabanı için boyutlandırma

Azure NetApp biriminin performansı, [Azure NetApp Files Için hizmet düzeyi](../../../azure-netapp-files/azure-netapp-files-service-levels.md)bölümünde belgelendiği gibi, birim boyutu ve hizmet düzeyi işlevindedir. 

Anlaşılması gereken, boyutun performans ilişkisine ve SVM 'nin (depolama sanal makinesi) bir LIF (mantıksal arabirim) için fiziksel limitlerin olduğu önem taşımaktadır.

Aşağıdaki tabloda, yedeklemeleri depolamak için büyük bir "standart" birimi oluşturmak ve tek bir LIF 'ın fiziksel bant genişliği kapasitesi aşılacağı için 12 TB 'den büyük bir "Ultra" birim oluşturmak mantıklı değildir. 

Bir LIF ve tek bir Linux oturumu için en yüksek aktarım hızı 1,2 ile 1,4 GB/sn arasındadır. /Hana/Data için daha fazla işleme ihtiyacınız varsa, veri yeniden yüklemesi sırasında g/ç etkinliğini veya birden çok NFS paylaşımındaki birden çok Hana veri dosyası üzerinde Hana işlemi 'i ayırmak için SAP HANA veri birimi bölümleme kullanabilirsiniz. HANA veri hacmi şeridi hakkında daha fazla bilgi için şu makaleleri okuyun:

- [HANA Yönetici Kılavuzu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog hakkında SAP HANA – veri birimlerini bölümleme](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP Note #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP Note #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Boyut  | Aktarım hızı standart | Verimlilik Premium | Verimlilik Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/sn | 64 MB/sn | 128 MB/sn |
| 2 TB | 32 MB/sn | 128 MB/sn | 256 MB/sn |
| 4 TB | 64 MB/sn | 256 MB/sn | 512 MB/sn |
| 10 TB | 160 MB/sn | 640 MB/sn | 1.280 MB/sn |
| 15 TB | 240 MB/sn | 960 MB/sn | 1.400 MB/sn |
| 20 TB | 320 MB/sn | 1.280 MB/sn | 1.400 MB/sn |
| 40 TB | 640 MB/sn | 1.400 MB/sn | 1.400 MB/sn |

Verilerin depolama arka ucunda aynı SSD 'ye yazıldığını anlamak önemlidir. Kapasite havuzundaki performans kotası, ortamı yönetebilmek için oluşturulmuştur.
Depolama KPI 'Leri tüm HANA veritabanı boyutları için eşittir. Neredeyse tüm durumlarda bu varsayım gerçekliği ve müşteri beklentisini yansıtmaz. HANA sistemlerinin boyutu, küçük bir sistemin depolama üretilen iş üretimi gerektirmesidir; büyük bir sistem yüksek depolama alanı işleme gerektirir. Ancak genel olarak daha büyük HANA veritabanı örnekleri için daha fazla verimlilik gereksinimi bekleyebilir. SAP 'nin temel alınan donanımla ilgili boyutlandırma kurallarının bir sonucu olarak, benzer HANA örnekleri de bir örnek yeniden başlatıldıktan sonra verileri yükleme gibi görevlerde daha fazla CPU kaynağı ve daha yüksek paralellik sağlar. Sonuç olarak, birim boyutları müşteri beklentileri ve gereksinimlerine benimsemelidir. Yalnızca saf kapasite gereksinimlerine göre değil.

SAP altyapısını Azure 'da tasarlarken, SAP tarafından en düşük işleme özelliklerine (üretimler sistemleri için) en düşük aktarım hızı özelliklerine çeviren bazı minimum depolama verimlilik gereksinimleri (üretim sistemleri için) farkında olmanız gerekir:

| Birim türü ve g/ç türü | SAP tarafından talep edilen en düşük KPI | Premium hizmet düzeyi | Ultra hizmet düzeyi |
| --- | --- | --- | --- |
| Günlük birimi yazma | 250 MB/sn | 4 TB | 2 TB |
| Veri birimi yazma | 250 MB/sn | 4 TB | 2 TB |
| Okunan veri hacmi | 400 MB/sn | 6,3 TB | 3,2 TB |

Üç KPI 'nin tümü talep edildiğinden, **/Hana/Data** biriminin en düşük okuma gereksinimlerini karşılamak için daha büyük kapasiteye doğru boyutlandırılması gerekir.

Yüksek bant genişliği gerektirmeyen HANA sistemleri için, ANF birim boyutları daha küçük olabilir. Bir HANA sisteminin daha fazla verimlilik gerektirmesi durumunda, kapasiteyi çevrimiçi olarak yeniden boyutlandırarak birim uyarlanmıştır. Yedekleme birimleri için hiçbir KPI tanımlanmamıştır. Ancak yedekleme birimi üretimi iyi bir ortam için gereklidir. Günlük – ve veri hacmi performansı, müşteri beklentilerine göre tasarlanmalıdır.

> [!IMPORTANT]
> Tek bir NFS biriminde dağıttığınız kapasiteden bağımsız olarak üretilen iş, bir sanal makinedeki bir tüketici tarafından 1.2-1.4 GB/sn bant genişliği yararlanılabilir aralığında platoya 'ya bekleniyor. Bu, ANF teklifinin temel mimarisiyle ve NFS 'nin çevresindeki ilgili Linux oturum sınırlarının ile ilgilidir. [Azure NetApp Files Için performans kıyaslama test sonuçları](../../../azure-netapp-files/performance-benchmarks-linux.md) makalesinde belgelenen performans ve verimlilik numaraları, birden fazla istemci VM 'sine sahip BIR paylaşılan NFS biriminde ve birden çok oturumla bir sonuç olarak yapılmıştır. Bu senaryo, SAP 'de ölçüdiğimiz senaryoya göre farklılık gösteren bir senaryodur. Bir NFS birimine göre tek bir VM 'den üretilen iş verimini ölçyoruz. ANF üzerinde barındırılıyor.

Veri ve günlük SAP minimum aktarım hızı gereksinimlerini karşılamak ve **/Hana/Shared** yönergelerine göre, önerilen boyutlar şöyle görünür:

| Birim | Boyut<br /> Premium depolama katmanı | Boyut<br /> Ultra depolama katmanı | Desteklenen NFS Protokolü |
| --- | --- | --- |
| /Hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /Hana/Data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/paylaşılan ölçek yukarı | Min (1 TB, 1 x RAM)  | Min (1 TB, 1 x RAM) | V3 veya v 4.1 |
| /Hana/paylaşılan genişleme | 1 x çalışan düğümü<br /> 4 çalışan düğüm başına  | 1 x çalışan düğümü<br /> 4 çalışan düğüm başına  | V3 veya v 4.1 |
| /Hana/Logbackup | 3 x RAM  | 3 x RAM | V3 veya v 4.1 |
| /Hana/Backup | 2 x RAM  | 2 x RAM | V3 veya v 4.1 |

Tüm birimlerde NFS v 4.1 kesinlikle önerilir

Yedekleme birimlerinin boyutları tahminler. Tam gereksinimlerin iş yükü ve işlem işlemlerine göre tanımlanması gerekir. Yedeklemeler için, farklı SAP HANA örnekleri için birçok birimi, daha düşük bir hizmet düzeyine sahip olabilecek bir (veya iki) daha büyük birimlere birleştirebilirsiniz.

> [!NOTE]
> Azure NetApp Files, bu belgede belirtilen boyutlandırma önerileri, SAP 'nin altyapı sağlayıcıları doğrultusunda belirtien düşük gereksinimleri hedefliyor. Gerçek müşteri dağıtımları ve iş yükü senaryolarında bu yeterli olmayabilir. Bu önerileri bir başlangıç noktası olarak kullanın ve belirli iş yükünüzün gereksinimlerine göre uyarlayın.  

Bu nedenle, zaten Ultra disk depolaması için listelenen ANF birimleri için benzer aktarım hızını dağıtmayı düşünebilirsiniz. Ayrıca, zaten Ultra disk tablolarında gerçekleştirilen farklı VM SKU 'Larının birimleri için listelenen boyutların boyutlarını göz önünde bulundurun.

> [!TIP]
> Birimlere gerek duymadan Azure NetApp Files birimleri dinamik olarak yeniden boyutlandırabilir `unmount` , sanal makineleri durdurabilir veya SAP HANA durdurabilirsiniz. Bu, uygulamanızı hem beklenen hem de öngörülemeyen üretilen iş taleplerini karşılamak için esneklik sağlar.

Bir SAP HANA genişleme yapılandırması ile birlikte bekleyen bir yapılandırma, [Azure VM 'lerde bulunan ve SUSE Linux Enterprise Server Azure NetApp Files Ile Azure VM 'lerinde bekleme düğümüyle birlikte SAP HANA](./sap-hana-scale-out-standby-netapp-files-suse.md)olarak yayımlanmış.


## <a name="availability"></a>Kullanılabilirlik
ANF sistem güncelleştirmeleri ve yükseltmeleri, müşteri ortamını etkilemeden uygulanır. Tanımlanan [SLA% 99,99 ' dir](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Birimler ve IP adresleri ve kapasite havuzları
ANF ile, temeldeki altyapının nasıl oluşturulduğunu anlamak önemlidir. Kapasite havuzu yalnızca bir yapıdır ve bu, ANF için bir faturalandırma modeli oluşturmayı kolaylaştırır. Bir kapasite havuzunun, temel alınan altyapıyla fiziksel bir ilişkisi yoktur. Yalnızca bir kapasite havuzu oluşturursanız, ücretlendirilebilecek bir kabuk oluşturulur. Bir birim oluşturduğunuzda, ilk SVM (depolama sanal makinesi) birkaç NetApp sisteminin kümesinde oluşturulur. Bu SVM için birime erişmek üzere tek bir IP oluşturulur. Birkaç birim oluşturursanız, tüm birimler bu çok denetleyicili NetApp kümesi üzerinden bu SVM 'ye dağıtılır. Yalnızca bir IP alıp alsanız bile veriler birkaç denetleyicilerde dağıtılır. ANF, yapılandırılmış depolamanın birimleri veya/ve kapasitesi önceden tanımlanmış dahili bir düzeye ulaştığında müşteri iş yüklerini otomatik olarak dağıtan bir mantığa sahiptir. Birimlere erişmek için yeni bir IP adresi atandığından bu gibi durumlar fark edebilirsiniz.

##<a name="log-volume-and-log-backup-volume"></a>Günlük birimi ve günlük yedekleme birimi
Çevrimiçi yineleme günlüğünü yazmak için "günlük birimi" (**/Hana/log**) kullanılır. Bu nedenle, bu birimde bulunan açık dosyalar bulunur ve bu birimin anlık görüntüsünü almak için hiçbir fikir vermez. Çevrimiçi Yinele günlük dosyası doluysa veya bir yedek günlük yedeklemesi yürütüldüğünde, çevrimiçi yineleme günlüğü, günlük yedekleme birimine arşivlenir veya yedeklenir. Makul yedekleme performansı sağlamak için, günlük yedekleme birimi iyi bir işleme gerektirir. Depolama maliyetlerini iyileştirmek için, birden çok HANA örneğinin günlük yedekleme hacmini birleştirmek mantıklı olabilir. Birden çok HANA örneğinin aynı birimden yararlanması ve yedeklemelerini farklı dizinlere yazması için. Bu tür bir birleştirme kullanarak, birimi biraz daha büyük hale getirmeniz gerektiğinden, ile daha fazla aktarım hızı sağlayabilirsiniz. 

Aynı, kullandığınız birim için tam HANA veritabanı yedeklemeleri yazmak için geçerlidir.  
 

## <a name="backup"></a>Backup
Akış yedeklemelerinin yanı sıra Azure [sanal makinelerinde SAP HANA yönelik makale yedekleme Kılavuzu](./sap-hana-backup-guide.md)' nda açıklandığı gibi SAP HANA veritabanlarını yedekleyerek, Azure NetApp Files depolama tabanlı anlık görüntü yedeklemeleri gerçekleştirme olasılığını açar. 

SAP HANA şunları destekler:

- SAP HANA 1,0 SPS7 üzerinde depolama tabanlı anlık görüntü yedeklemeleri
- SAP HANA 2,0 SPS4 ' den çok veritabanı kapsayıcısı (MDC) HANA ortamları için depolama tabanlı anlık görüntü yedekleme desteği


Depolama tabanlı anlık görüntü yedeklemeleri oluşturma işlemi, dört adımlı basit bir yordamdır. 
1. HANA (iç) veritabanı anlık görüntüsü oluşturma-sizin veya araçların gerçekleştirmesi gereken bir etkinlik 
1. SAP HANA depolama üzerinde tutarlı bir durum oluşturmak için verileri datafiles 'a yazar-HANA, bir HANA anlık görüntüsü oluşturma sonucunda bu adımı gerçekleştirir
1. Depolama alanı üzerinde **/Hana/Data** biriminde bir anlık görüntü oluşturun; bir adım veya araçların gerçekleştirmesi gerekir. **/Hana/log** biriminde anlık görüntü gerçekleştirmeniz gerekmez
1. HANA (iç) veritabanı anlık görüntüsünü silme ve normal işlemi sürdürme-bir adım veya araçların gerçekleştirmesi gerekir

> [!WARNING]
> Son adımın eksik olması, SAP HANA bellek talebine önemli bir etkiye sahiptir ve SAP HANA bir durmasına yol açabilir

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA için ANF anlık görüntü yedeklemesi](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP HANA part2 için ANF anlık görüntü yedeklemesi](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Bu anlık görüntü yedekleme yordamı çeşitli araçlar kullanılarak çeşitli yollarla yönetilebilir. GitHub 'da bulunan "ntaphana_azure. Kopyala" Python betiğinin bir örneği, [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) hiçbir bakım veya destek olmadan "olduğu gibi" sağlanan örnek koddur.



> [!CAUTION]
> Tek bir anlık görüntü, bir anlık görüntü aldığınız birimle aynı fiziksel depolamada bulunduğundan korumalı bir yedekleme değildir. Gün başına en az bir anlık görüntüyü farklı bir konuma "korumak" zorunludur. Bu, uzak bir Azure bölgesindeki veya Azure Blob depolamada aynı ortamda yapılabilir.


Commkasadan yedekleme ürünleri kullanıcıları için, ikinci bir seçenek de Commkasaıntellisnap V. 11.21 ve üzeri bir seçenektir. Commkasasının bu veya sonraki sürümleri Azure NetApp Files desteği sunar. [Commkasakalisnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) makalesi daha fazla bilgi sağlar.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Azure Blob depolamayı kullanarak anlık görüntüyü yedekleme
Azure Blob depolama 'ya yedekleme, ANF tabanlı HANA veritabanı depolama anlık görüntü yedeklemelerini kaydetmek için ekonomik ve hızlı bir yöntemdir. Anlık görüntüleri Azure Blob depolamaya kaydetmek için AzCopy aracı tercih edilir. Bu aracın en son sürümünü indirin ve örneğin, GitHub 'daki Python betiğinin yüklendiği bin dizininde yükleyin.
En son AzCopy aracını indirin:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

En gelişmiş özellik EŞITLEME seçeneğidir. EŞITLE seçeneğini kullanırsanız AzCopy kaynağı ve hedef dizini eşitlenmiş halde tutar. **--Delete-Destination** parametresinin kullanımı önemlidir. Bu parametre olmadan AzCopy, hedef sitedeki dosyaları silmez ve hedef taraftaki alan kullanımı artar. Azure depolama hesabınızda bir Blok Blobu kapsayıcısı oluşturun. Sonra blob kapsayıcısı için SAS anahtarını oluşturun ve Snapshot klasörünü Azure Blob kapsayıcısı ile eşitler.

Örneğin, günlük bir anlık görüntünün verileri korumak için Azure Blob kapsayıcısına eşitlenmesi gerekiyorsa. Ve yalnızca bir anlık görüntü tutulmalıdır, aşağıdaki komut kullanılabilir.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Sonraki adımlar
Makaleyi okuyun:

- [Azure sanal makineleri için yüksek kullanılabilirlik SAP HANA](./sap-hana-availability-overview.md)