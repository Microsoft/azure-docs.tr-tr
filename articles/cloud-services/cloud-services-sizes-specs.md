---
title: Azure Cloud Services için sanal makine boyutları | Microsoft Docs
description: Azure bulut hizmeti Web ve çalışan rolleri için farklı sanal makine boyutlarını (ve kimliklerini) listeler.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 0df9ee859ae8e341537f5e832d9ff90e9736cb0c
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443006"
---
# <a name="sizes-for-cloud-services"></a>Cloud Services için Boyutlar
Bu konuda, bulut hizmeti rol örnekleri (Web rolleri ve çalışan rolleri) için kullanılabilen Boyutlar ve seçenekler açıklanmaktadır. Ayrıca, bu kaynakları kullanmayı planlarken göz önünde bulundurmanız gereken dağıtım konuları da sunar. Her boyutun, [hizmet tanımı dosyanıza](cloud-services-model-and-package.md#csdef)YERLEŞTIRDIĞINIZ bir kimliği vardır. Her boyutun fiyatları [Cloud Services fiyatlandırma](https://azure.microsoft.com/pricing/details/cloud-services/) sayfasında kullanılabilir.

> [!NOTE]
> İlgili Azure sınırlarını görmek için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Web ve çalışan rolü örnekleri için Boyutlar
Azure'da birden fazla standart boyut seçeneği vardır. Bu boyutlarla ilgili önemli noktalardan bazıları şunlardır:

* D Serisi VM'ler, daha yüksek işlem gücüne ve geçici süreli disk performansına ihtiyaç duyan uygulamaları çalıştıracak şekilde tasarlanmıştır. D Serisi VM'ler daha hızlı işlemcilere, daha yüksek bellek-çekirdek oranına ve geçici disk için katı hal sürücüsüne (SSD) sahiptir. Ayrıntılı bilgi için Azure blogundaki [Yeni D Serisi Sanal Makine Boyutları](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) duyurusunu inceleyin.
* Özgün D serisi için bir takip olan Dv3 serisi, Dv2 serisi, daha güçlü bir CPU sunar. Dv2 Serisi CPU, D Serisi CPU'dan yaklaşık %35 daha hızlıdır. Yeni nesil 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) işlemciyi temel alır ve Intel Turbo Boost Technology 2.0 ile 3,1 GHz'e varan hızlara çıkabilir. Dv2 Serisi, D Serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.
* En fazla belleği sunan G Serisi VM'ler, Intel Xeon E5 V3 ailesi işlemcilere sahip ana bilgisayarlarda çalışır.
* A serisi VM 'Ler, çeşitli donanım türlerinde ve işlemcilerde dağıtılabilir. Bu boyut, donanıma bağlı olarak, dağıtılan donanıma bakılmaksızın, çalışan örnek için tutarlı işlemci performansı sunmak üzere azaltılır. Bu boyutun dağıtıldığı fiziksel donanımı belirlemek için Sanal Makinenin içinden sanal donanımı sorgulayın.
* A0 boyutunun fiziksel donanım üzerindeki abone sayısı planlanandan fazladır. Yalnızca bu boyutta diğer müşteri dağıtımları, çalışan iş yükünüzün performansını etkileyebilir. Göreli performans aşağıda beklenen temel düzey olarak belirtilmiştir ve %15 oranında değişiklik gösterebilir.

Sanal makinenin boyutu, fiyatlandırmayı etkiler. Boyut, sanal makinenin işlem, bellek ve depolama kapasitesini de etkiler. Depolama maliyetleri, depolama hesabında kullanılan sayfa sayısına göre ayrıca hesaplanır. Ayrıntılar için bkz. [Cloud Services fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cloud-services/) ve [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

Aşağıdaki önemli noktalar boyut konusunda karar vermenize yardımcı olabilir:

* A8-A11 ve H Serisi boyutlar *yoğun işlem gücü kullanımlı örnekler* olarak da bilinir. Bu boyutları çalıştıran donanım; yüksek performanslı bilgi işlem (HPC) kümesi uygulamaları, modellemeler ve simülasyonlar gibi yoğun işlem ve ağ kullanımlı uygulamalar için tasarlanmış ve iyileştirilmiştir. A8-A11 Serisinde, Intel Xeon E5-2670 @ 2,6 GHZ, H Serisinde ise Intel Xeon E5-2667 v3 @ 3,2 GHz işlemciler kullanılmaktadır. Bu boyutları kullanma hakkında ayrıntılı bilgi ve konular için bkz. [yüksek performanslı Işlem VM boyutları](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dv3-Series, Dv2-serisi, D serisi, G serisi, daha hızlı CPU, daha iyi yerel disk performansı talep eden veya daha yüksek bellek taleplerine sahip uygulamalar için idealdir. Bu seçenekler birçok kurumsal sınıf uygulama için güçlü bir bileşim sunar.
* Azure veri merkezlerindeki fiziksel ana bilgisayarlardan bazıları A5 – A11 gibi daha büyük sanal makine boyutlarını desteklemeyebilir. Sonuç olarak, mevcut bir sanal makineyi yeni bir boyuta yeniden boyutlandırırken { **Machine Name} sanal makinesini yapılandıramadı** veya **{Machine Name} sanal makinesini oluşturamadı** ; hata iletisini görebilirsiniz. 16 Nisan 2013 tarihinden önce oluşturulan bir sanal ağda yeni bir sanal makine oluşturma; veya var olan bir bulut hizmetine yeni bir sanal makine ekleniyor. Bkz [: hata: Her dağıtım senaryosunda geçici çözümler için destek](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) forumundaki "sanal makine yapılandırılamadı".
* Ayrıca aboneliğiniz, belirli boyut ailelerinde dağıtabileceğiniz çekirdek sayısını da sınırlıyor olabilir. Artırmak istediğini kotalar için Azure Desteği'ne başvurun.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar
Azure Işlem birimi (ACU) kavramını, Azure SKU 'Larında işlem (CPU) performansını karşılaştıran ve hangi SKU 'nun performans ihtiyaçlarınızı karşılayabileceği konusunda bir yol sunacak şekilde oluşturduk.  ACU şu anda Küçük (Standard_A1) VM'de 100 olarak standart haline getirilmiş ve diğer tüm SKU'lar, standart bir karşılaştırmalı testte sunabilecekleri yaklaşık hıza göre derecelendirilmiştir.

> [!IMPORTANT]
> ACU yalnızca rehberlik etme amacı taşımaktadır. İş yükünüzle aldığınız sonuçlar farklılık gösterebilir.
>
>

<br>

| SKU Ailesi | ACU/Çekirdek |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Küçük-Extrabüyük](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [Bir v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180-240* |
| [H](#h-series) |290-300* |

\* işaretli ACU'lar, CPU frekansını artırmak ve performans artışı sağlamak için Intel® Turbo Boost teknolojisinden faydalanır. Performans artışının oranı VM boyutuna, iş yüküne ve aynı ana bilgisayarda çalışan iş yüklerine göre değişiklik gösterebilir.

## <a name="size-tables"></a>Boyut tabloları
Aşağıdaki tablolarda boyutlara ve sundukları kapasiteye yer verilmiştir.

* Depolama kapasitesi GiB veya 1024^3 bayt cinsinden gösterilmiştir. GB (1000^3 bayt) ile ölçülen diskleri GiB (1024^3 bayt) ile ölçülen disklerle karşılaştırırken GiB cinsinden verilen kapasite rakamlarının daha küçük görünebileceğini unutmayın. Örneğin: 1023 GiB = 1098,4 GB
* Disk aktarım hızı, saniye başına giriş/çıkış işlemi sayısı (IOPS) ve MB/sn (MB/sn = 10^6 bayt/sn) üzerinden ölçülür.
* Veri diskleri önbelleğe alınmış veya alınmamış modlarda çalışabilir. Önbelleğe alınmış veri diski işlemi için ana bilgisayar önbelleği **SaltOkunur** veya **OkuYaz** moduna ayarlanır. Önbelleğe alınmamış veri diski işlemi için ana bilgisayar önbelleği **Yok** moduna ayarlanır.
* Maksimum ağ bant genişliği, VM türü başına ayrılan ve atanan toplam maksimum bant genişliğidir. Maksimum bant genişliği, yeterli ağ kapasitesinin mevcut olduğundan emin olarak doğru VM'i seçme konusunda yardımcı olur. Düşük, orta, yüksek ve çok yüksek arasında geçiş yaparken üretilen iş, buna göre artar. Gerçek ağ performansı, ağ ve uygulama yüklerinin yanı sıra uygulama ağ ayarları gibi birçok faktöre bağlıdır.

## <a name="a-series"></a>A Serisi
| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama: GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1\.         | 0,768        | 20                   | 1/düşük |
| Küçük           | 1\.         | 1,75         | 225                  | 1/orta |
| Orta          | 2         | 3,5          | 490                  | 1/orta |
| Büyük           | 4         | 7            | 1000                 | 2/yüksek |
| Çok büyük      | 8         | 14           | 2040                 | 4/yüksek |
| A5              | 2         | 14           | 490                  | 1/orta |
| A6              | 4         | 28           | 1000                 | 2/yüksek |
| A7              | 8         | 56           | 2040                 | 4/yüksek |

## <a name="a-series---compute-intensive-instances"></a>A Serisi - Yoğun işlem gücü kullanımlı örnekler
Bu boyutları kullanma hakkında daha fazla bilgi ve dikkat edilmesi gereken noktalar için bkz. [yüksek performanslı Işlem VM boyutları](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama: GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2/yüksek |
| A9*             |16         | 112          | 1817                 | 4/çok yüksek |
| A10             |8          | 56           | 1817                 | 2/yüksek |
| A11             |16         | 112          | 1817                 | 4/çok yüksek |

\*RDMA özellikli

## <a name="av2-series"></a>Av2 Serisi

| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1\.         | 2            | 10                   | 1/orta                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/orta                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/yüksek                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/yüksek                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/orta                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/yüksek                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/yüksek                     |


## <a name="d-series"></a>D Serisi
| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1\.         | 3,5          | 50                   | 1/orta |
| Standard_D2     | 2         | 7            | 100                  | 2/yüksek |
| Standard_D3     | 4         | 14           | 200                  | 4/yüksek |
| Standard_D4     | 8         | 28           | 400                  | 8/yüksek |
| Standard_D11    | 2         | 14           | 100                  | 2/yüksek |
| Standard_D12    | 4         | 28           | 200                  | 4/yüksek |
| Standard_D13    | 8         | 56           | 400                  | 8/yüksek |
| Standard_D14    | 16        | 112          | 800                  | 8/çok yüksek |

## <a name="dv2-series"></a>Dv2 Serisi
| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3,5          | 50                   | 1/orta |
| Standard_D2_v2  | 2         | 7            | 100                  | 2/yüksek |
| Standard_D3_v2  | 4         | 14           | 200                  | 4/yüksek |
| Standard_D4_v2  | 8         | 28           | 400                  | 8/yüksek |
| Standard_D5_v2  | 16        | 56           | 800                  | 8/aşırı yüksek |
| Standard_D11_v2 | 2         | 14           | 100                  | 2/yüksek |
| Standard_D12_v2 | 4         | 28           | 200                  | 4/yüksek |
| Standard_D13_v2 | 8         | 56           | 400                  | 8/yüksek |
| Standard_D14_v2 | 16        | 112          | 800                  | 8/aşırı yüksek |
| Standard_D15_v2 | 20        | 140          | 1000                | 8/aşırı yüksek |

## <a name="dv3-series"></a>Dv3 serisi

| Size            | CPU çekirdekleri | Bellek: GiB   | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2/orta |
| Standard_D4_v3  | 4         | 16            | 100                  | 2/yüksek |
| Standard_D8_v3  | 8         | 32            | 200                  | 4/yüksek |
| Standard_D16_v3 | 16        | 64            | 400                  | 8/aşırı yüksek |
| Standard_D32_v3 | 32        | 128           | 800                  | 8/aşırı yüksek |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8/aşırı yüksek |

## <a name="ev3-series"></a>Ev3 serisi

| Size            | CPU çekirdekleri | Bellek: GiB   | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2/orta |
| Standard_E4_v3  | 4         | 32            | 100                  | 2/yüksek |
| Standard_E8_v3  | 8         | 64            | 200                  | 4/yüksek |
| Standard_E16_v3 | 16        | 128           | 400                  | 8/aşırı yüksek |
| Standard_E32_v3 | 32        | 256           | 800                  | 8/aşırı yüksek |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8/aşırı yüksek |


## <a name="g-series"></a>G Serisi
| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/yüksek |
| Standard_G2     | 4         | 56           | 768                  |2/yüksek |
| Standard_G3     | 8         | 112          | 1536                |4/çok yüksek |
| Standard_G4     | 16        | 224          | 3072                |8/aşırı yüksek |
| Standard_G5     | 32        | 448          | 6144                |8/aşırı yüksek |

## <a name="h-series"></a>H Serisi
Azure H Serisi sanal makineler, moleküler modelleme ve hesaplamalı akışkanlar dinamiği gibi üst düzey işlem hesaplama gereksinimlerine hitap eden yeni nesil yüksek performanslı bilgi işlem VM'leridir. Bu 8 ve 16 çekirdek VM 'Ler, DDR4 bellek ve yerel SSD tabanlı depolama özelliklerine sahip Intel Haswell E5-2667 v3 işlemci teknolojisi üzerine kurulmuştur.

H Serisi önemli miktarda CPU gücünün yanı sıra, FDR InfiniBand ile düşük gecikmeli RDMA ağ iletişimi için farklı seçeneklere ek olarak yoğun bellek kullanımlı işlem gereksinimlerini için çok sayıda bellek yapılandırması sunar.

| Size            | CPU çekirdekleri | Bellek: GiB  | Geçici depolama (SSD): GiB       | Maksimum NIC/Ağ bant genişliği |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/yüksek |
| Standard_H16    | 16        | 112          | 2000                 | 8/çok yüksek |
| Standard_H8m    | 8         | 112          | 1000                 | 8/yüksek |
| Standard_H16m   | 16        | 224          | 2000                 | 8/çok yüksek |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/çok yüksek |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/çok yüksek |

\*RDMA özellikli

## <a name="configure-sizes-for-cloud-services"></a>Cloud Services için boyutları Yapılandır
Bir rol örneğinin sanal makine boyutunu, [hizmet tanımı dosyası](cloud-services-model-and-package.md#csdef)tarafından tanımlanan hizmet modelinin bir parçası olarak belirtebilirsiniz. Rolün boyutu CPU çekirdeğinin sayısını, bellek kapasitesini ve çalışan bir örneğe ayrılan yerel dosya sistemi boyutunu belirler. Uygulamanızın kaynak gereksinimini temel alarak rol boyutunu seçin.

Rol boyutunun bir Web rolü örneği için Standard_D2 olarak ayarlanmasına yönelik bir örnek aşağıda verilmiştir:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Mevcut bir rolün boyutunu değiştirme

İş yükünüzün doğası veya yeni VM boyutları kullanılabilir hale geldiğinde, rolünüzün boyutunu değiştirmek isteyebilirsiniz. Bunu yapmak için, hizmet tanımı dosyanızdaki (yukarıda gösterildiği gibi) VM boyutunu değiştirmeniz, bulut hizmetinizi yeniden paketleyip dağıtmanız gerekir.

>[!TIP]
> Farklı ortamlarda rolünüzün farklı VM boyutlarını kullanmak isteyebilirsiniz (örn. test vs üretimi). Bunu yapmanın bir yolu, projenizde birden çok hizmet tanımı (. csdef) dosyası oluşturmak ve ardından CSPack aracını kullanarak otomatik derlemeniz sırasında ortam başına farklı bulut hizmeti paketleri oluşturmaktır. Bir bulut hizmetleri paketinin öğeleri ve bunların nasıl oluşturulacağı hakkında daha fazla bilgi edinmek için bkz. [Cloud Services modeli nedir ve nasıl paketlarım?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Boyutların bir listesini alın
Boyut listesini almak için PowerShell veya REST API kullanabilirsiniz. REST API [burada](/previous-versions/azure/reference/dn469422(v=azure.100))belgelenmiştir. Aşağıdaki kod, Cloud Services için kullanılabilir olan tüm boyutları listeedecek bir PowerShell komutlarıdır. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Sonraki adımlar
* [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md) hakkında bilgi edinin.
* HPC iş yükleri için [yüksek performanslı Işlem VM boyutları hakkında](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) daha fazla bilgi edinin.
