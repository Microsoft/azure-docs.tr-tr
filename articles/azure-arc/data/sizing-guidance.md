---
title: Boyutlandırma kılavuzu
description: Azure Arc etkin veri Hizmetleri dağıtımının boyutunu planlayın.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bbd778eabf150b734b04e004006dfeea2254ec4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077491"
---
# <a name="sizing-guidance"></a>Boyutlandırma Kılavuzu

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Boyutlandırma kılavuzlarına genel bakış

Azure Arc Data Services dağıtımı için planlama yaparken, Azure Arc veri denetleyicisini çalıştırmak için gereken doğru işlem, bellek ve depolama miktarını ve dağıttığınız SQL yönetilen örneği ve PostgreSQL hiper ölçek sunucu gruplarının sayısını planlamanız gerekir.  Kubernetes 'te Azure Arc etkin veri Hizmetleri dağıtıldığından, ek işlem düğümleri veya depolama alanı ekleyerek Kubernetes kümenize zaman içinde ek kapasite ekleme esnekliğine sahip olursunuz.  Bu kılavuz, en düşük gereksinimlere kılavuzluk sağlar ve bazı genel gereksinimler için önerilen boyutlarda rehberlik sağlar.

## <a name="general-sizing-requirements"></a>Genel boyutlandırma gereksinimleri

> [!NOTE]
> Bu makaledeki kavramları bilmiyorsanız, [Kubernetes Resource idare](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) ve [Kubernetes boyut gösterimi](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)hakkında daha fazla bilgi edinebilirsiniz.

Çekirdek numaraları, bire büyük veya buna eşit bir tamsayı değeri olmalıdır.

Dağıtım için azdata kullanılırken, bellek değerleri iki sayının bir gücüyle belirtilmelidir-Yani, son ekler kullanılarak: ki, mi veya gi.

Sınır değerleri her zaman, belirtilmişse istek değerinden büyük olmalıdır.

Çekirdekler için değerleri sınırlama SQL yönetilen örneği ve PostgreSQL hiper ölçek sunucu grupları üzerindeki faturalandırılabilir ölçümdür.

## <a name="minimum-deployment-requirements"></a>En düşük dağıtım gereksinimleri

En düşük boyut Azure Arc etkin veri Hizmetleri dağıtımı, Azure Arc veri denetleyicisi ve bir SQL yönetilen örneği ve iki çalışan düğümü olan bir PostgreSQL hiper ölçek sunucu grubu olarak kabul edilebilir.  Bu yapılandırma için, Kubernetes kümenizde en az 16 GB RAM ve 4 çekirdek _kullanılabilir_ kapasiteye sahip olmanız gerekir.  En az 8 GB RAM ve 4 çekirdek büyüklüğünde bir en düşük Kubernetes düğüm boyutuna ve tüm Kubernetes düğümlerinde kullanılabilen 16 GB RAM toplam kapasiteye sahip olduğunuzdan emin olmanız gerekir.  Örneğin, 32 GB RAM ve 4 çekirdekte 1 düğüme sahip olabilirsiniz veya her biri 16 GB RAM ve 4 çekirdeğe sahip 2 düğüme sahip olabilirsiniz.

Depolama alanı boyutlandırılmasına ilişkin ayrıntılar için bkz. [depolama-yapılandırma](storage-configuration.md) makalesi.

## <a name="data-controller-sizing-details"></a>Veri denetleyicisi boyutlandırma ayrıntıları

Veri denetleyicisi, bir API, denetleyici hizmeti, önyükleyici ve izleme veritabanları ile panolar sağlamak için Kubernetes kümenize dağıtılan bir pod koleksiyonudur.  Bu tabloda bellek ve CPU istekleri ve sınırları için varsayılan değerler açıklanmaktadır.

|Pod adı|CPU Isteği|Bellek Isteği|CPU sınırı|Bellek sınırı|Notlar|
|---|---|---|---|---|---|
|**önyükleyici**|100 milyon|100 mı|200 milyon|200Mı||
|**denetimle**|400p|2Gi|1800milyon|2Gi||
|**controldb**|200 milyon|3Gı|800d|6Gi||
|**controlwd**|10 milyon|100 mı|100 milyon|200Mı||
|**logsdb**|200 milyon|1600Mı|2|1600Mı||
|**logsui**|100 milyon|500Mı|2|2Gi||
|**metricsdb**|200 milyon|800Mı|400p|2Gi||
|**metricsdc**|100 milyon|200Mı|200 milyon|300Mı|Metricsdc, kümenizdeki her bir Kubernetes düğümünde oluşturulan bir daemonset.  Buradaki tablodaki sayılar _düğüm başına_. Veri denetleyicisini oluşturmadan önce dağıtım profili dosyanızda allowNodeMetricsCollection = false ayarlarsanız, metricsdc daemonset oluşturulmaz.|
|**metricsuı**|20 milyon|200Mı|500 milyon|200Mı||
|**mgmtproxy**|200 milyon|250Mı|500 milyon|500Mı||

Controldb için varsayılan ayarları geçersiz kılabilir ve dağıtım profili dosyanızdaki veya datacontroller YAML dosyanızdaki denetim yığınlarını kontrol edebilirsiniz.  Örnek:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Depolama alanı boyutlandırılmasına ilişkin ayrıntılar için bkz. [depolama-yapılandırma](storage-configuration.md) makalesi.

## <a name="sql-managed-instance-sizing-details"></a>SQL yönetilen örnek boyutlandırma ayrıntıları

Her SQL yönetilen örneği aşağıdaki en düşük kaynak isteklerini içermelidir:
- Bellek: 2Gi
- Çekirdekler: 1

Oluşturulan her SQL yönetilen örnek Pod, üç kapsayıcı içerir:

|Kapsayıcı adı|CPU Isteği|Bellek Isteği|CPU sınırı|Bellek sınırı|Notlar|
|---|---|---|---|---|---|
|floentbit|100 milyon|100 mı|Belirtilmemiş|Belirtilmemiş|Floentbit kapsayıcı kaynak istekleri, SQL yönetilen örneği için belirtilen isteklere _ek_ niteliğindedir.|
|yay-sqlmı|Kullanıcı tarafından belirtilen veya belirtilmemiş.|Kullanıcı tarafından belirtilen veya belirtilmemiş.|Kullanıcı tarafından belirtilen veya belirtilmemiş.|Kullanıcı tarafından belirtilen veya belirtilmemiş.|
|toplanan|Belirtilmemiş|Belirtilmemiş|Belirtilmemiş|Belirtilmemiş|

Tüm kalıcı birimler için varsayılan birim boyutu 5Gi ' dir.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL hiper ölçek sunucu grubu boyutlandırma ayrıntıları

Her PostgreSQL hiper ölçek sunucu grubu düğümü, aşağıdaki en düşük kaynak isteklerini içermelidir:
- Bellek: 256Mi
- Çekirdekler: 1

Oluşturulan her PostgreSQL hiper ölçek sunucu grubu Düzenleyicisi veya çalışan Pod üç kapsayıcı içerir:

|Kapsayıcı adı|CPU Isteği|Bellek Isteği|CPU sınırı|Bellek sınırı|Notlar|
|---|---|---|---|---|---|
|floentbit|100 milyon|100 mı|Belirtilmemiş|Belirtilmemiş|Akıcı entbit kapsayıcı kaynak istekleri, PostgreSQL hiper ölçek sunucu grubu düğümleri için belirtilen isteklere _ek_ niteliğindedir.|
|postgres|Kullanıcı tarafından belirtilen veya belirtilmemiş.|Kullanıcı tarafından belirtilen veya 256Mı (varsayılan).|Kullanıcı tarafından belirtilen veya belirtilmemiş.|Kullanıcı tarafından belirtilen veya belirtilmemiş.||
|telegraf|Belirtilmemiş|Belirtilmemiş|Belirtilmemiş|Belirtilmemiş||

## <a name="cumulative-sizing"></a>Toplu boyutlandırma

Azure Arc etkin veri Hizmetleri için gerekli olan bir ortamın genel boyutu öncelikle oluşturulacak veritabanı örneklerinin sayısı ve boyutunun bir işlevidir.  Toplam boyut, örnek sayısının büyüeceği ve küçültüleceği ve her bir veritabanı örneği için gereken kaynak miktarının değiştirileceği bilinerek önceden tahmin edilmesi zor olabilir.

Belirli bir Azure Arc etkin veri Hizmetleri ortamı için temel Boyut, 4 çekirdek ve 16 GB RAM gerektiren veri denetleyicisinin boyutudur.  Buradan, veritabanı örnekleri için gereken toplam çekirdek ve bellek toplamını ekleyebilirsiniz.  SQL yönetilen örneği için, Pod sayısı oluşturulan SQL yönetilen örnek sayısına eşittir.  PostgreSQL hiper ölçek sunucu grupları için, düğüm sayısı, çalışan düğümlerinin sayısına ve bir düzenleyici düğümü için eşdeğerdir.  Örneğin, 3 çalışan düğümü olan bir PostgreSQL sunucu grubunuz varsa toplam düğüm sayısı 4 olur.

Her veritabanı örneği için istediğiniz çekirdekler ve belleğe ek olarak, aracı kapsayıcıları için 250 milyon çekirdek ve 250Mı RAM eklemeniz gerekir.

Örnek boyutlandırma hesaplaması aşağıda verilmiştir.

Gereksinimler:

- **"SQL1"**: 16 GB RAM, 4 çekirdek IÇEREN 1 SQL yönetilen örneği
- **"SQL2"**: 256 GB RAM, 16 çekirdek IÇEREN 1 SQL yönetilen örneği
- **"Postgres1"**: 12 GB RAM, 4 çekirdek üzerinde 4 çalışan 1 PostgreSQL hiper ölçek sunucu grubu

Boyutlandırma hesaplamaları:

- "SQL1" boyutu; 4,25 16,25 Pod başına aracılar için 1 Pod * ([16 gi RAM, 4 çekirdek] + [250Mı RAM, 250gb çekirdek]).
- "SQL2" boyutu; 16,25 256,25 Pod başına aracılar için 1 Pod * ([256 gi RAM, 16 çekirdek] + + [250Mı RAM, 250D çekirdek]).
- SQL 1 ve SQL 2 ' nin toplam boyutu: (16,25 GB + 256,25 GI) = 272,5 GB RAM, (4,25 çekirdek + 16,25 çekirdek) = 20,5 çekirdek.

- "Postgres1" boyutu, Pod başına aracılar = 61,25 GB RAM, 21,25 çekirdek için: (4 çalışan Pod + 1 düzenleyici Pod) * ([12 GB RAM, 4 çekirdek] + [250mı RAM, 250D çekirdek]).

- Veritabanı örnekleri için gereken toplam kapasite: 272,5 GB RAM, SQL + 61,25 GB RAM için 20,5 çekirdek, PostgreSQL için 21,25 çekirdekleri hiper ölçek = 333,75 GB RAM, 42,5 çekirdek.

- Veritabanı örnekleri ve veri denetleyicisi için gereken toplam kapasite: 333,75 GB RAM, veritabanı örnekleri için 42,5 çekirdek + 16 GB RAM, veri denetleyicisi = 349,75 GB RAM, 46,5 çekirdek için 4 çekirdek.

Depolama alanı boyutlandırılmasına ilişkin ayrıntılar için bkz. [depolama-yapılandırma](storage-configuration.md) makalesi.

## <a name="other-considerations"></a>Diğer önemli noktalar

Çekirdek veya RAM için verilen bir veritabanı örnek boyutu isteğinin kümedeki Kubernetes düğümlerinin kullanılabilir kapasitesini aşmadığını unutmayın.  Örneğin, Kubernetes kümenizde bulunan en büyük Kubernetes düğümü 256 GB RAM ve 24 çekirdek ise, 512 GB RAM ve 48 çekirdekler isteğiyle bir veritabanı örneği oluşturabileceksiniz.  

Kubernetes düğümleri için Kubernetes düğümlerinde en az %25 oranında kullanılabilir kapasiteyi korumak ve isteğe bağlı olarak, elastik ölçekleme ve daha uzun süreli büyüme için izin vermek için bu iyi bir fikirdir.  

Boyutlandırma hesaplamalarınızda, Kubernetes sistem yığınlarının kaynak gereksinimlerine ve aynı Kubernetes kümesinde Azure Arc etkin veri hizmetleriyle kapasite paylaşımı olabilecek diğer iş yüklerine eklemeyi unutmayın.

Planlı bakım ve olağanüstü durum sürekliliği sırasında yüksek kullanılabilirlik sağlamak için, kümenizde bulunan Kubernetes düğümlerinden en az birini, belirli bir zaman noktasında kullanılamayacak şekilde planlamanız gerekir.  Kubernetes, bakım için alınmış veya bir hata nedeniyle belirli bir düğümde çalışan Pod 'leri yeniden zamanlamayı deneyecek.  Kalan düğümlerde kullanılabilir kapasite yoksa, yeniden kullanılabilir kapasite olana kadar bu düğüm oluşturma işlemi için yeniden planlanmaz.  Büyük veritabanı örnekleriyle daha fazla dikkatli olun.  Örneğin, büyük bir veritabanı örneğinin kaynak gereksinimlerini karşılamak için yeterince büyük bir Kubernetes düğümü varsa ve bu düğüm başarısız olursa, Kubernetes bu veritabanı örneğini başka bir Kubernetes düğümü üzerine zamanlayamaz.

[Bir Kubernetes küme boyutu için en fazla sınırı](https://kubernetes.io/docs/setup/best-practices/cluster-large/) göz önünde bulundurun.

Kubernetes yöneticiniz, ad alanınız/projenizde [kaynak kotalarını](https://kubernetes.io/docs/concepts/policy/resource-quotas/) ayarlamış olabilir.  Veritabanı örneği boyutlarınızı planlarken bunları aklınızda bulundurun.
