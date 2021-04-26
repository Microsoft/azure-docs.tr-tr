---
title: Azure Service Fabric uygulama modeli
description: Uygulama ve hizmet bildirim dosyalarını kullanarak Azure Service Fabric uygulamaları ve Hizmetleri modelleme ve açıklama.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85963742"
---
# <a name="model-an-application-in-service-fabric"></a>Service Fabric bir uygulama modelleme
Bu makalede, Azure Service Fabric uygulama modeline genel bir bakış ve bildirim dosyaları aracılığıyla bir uygulama ve hizmetin tanımlanması sunulmaktadır.

## <a name="understand-the-application-model"></a>Uygulama modelini anlama
Uygulama, belirli bir işlevi veya işlevleri gerçekleştiren bir Bileşen Hizmetleri koleksiyonudur. Bir hizmet, tüm ve tek başına işlevleri gerçekleştirir ve diğer hizmetlerden bağımsız olarak başlatılabilir ve çalıştırılabilir.  Bir hizmet, koddan, yapılandırmadan ve verilerden oluşur. Her hizmet için, kod yürütülebilir ikili dosyalardan oluşur, yapılandırma çalışma zamanında yüklenebilecek hizmet ayarlarından oluşur ve veriler, hizmet tarafından tüketilen rastgele statik verilerden oluşur. Bu hiyerarşik uygulama modelindeki her bileşeni bağımsız olarak sürümlenebilir ve yükseltilebilir.

![Service Fabric uygulama modeli][appmodel-diagram]

Uygulama türü, bir uygulamanın kategorileştirdayalıdır ve bir hizmet türü paketinden oluşur. Hizmet türü bir hizmetin kategorileştirsahiptir. Kategori farklı ayarlara ve yapılandırmalara sahip olabilir, ancak çekirdek işlevsellik aynı kalır. Bir hizmetin örnekleri aynı hizmet türünün farklı hizmet yapılandırması çeşitlemelerdir.  

Uygulama ve hizmetlerin sınıfları (veya "türleri"), XML dosyaları (uygulama bildirimleri ve hizmet bildirimleri) aracılığıyla açıklanır.  Bildirimler, uygulamaları ve Hizmetleri anlatmaktadır ve kümenin görüntü deposundan hangi uygulamaların örneklenebilir şablonlardır.  Bildirimler, [uygulama ve hizmet bildirimlerinde](service-fabric-application-and-service-manifests.md)ayrıntılı olarak ele alınmıştır. ServiceManifest.xml ve ApplicationManifest.xml dosyası için şema tanımı, Service Fabric SDK 'Sı ve *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* araçları ile birlikte yüklenir. XML şeması [Servicefabricservicemodel. xsd şema belgelerinde](service-fabric-service-model-schema.md)belgelenmiştir.

Farklı uygulama örneklerinin kodu, aynı Service Fabric düğümü tarafından barındırıldığında bile ayrı süreçler olarak çalışır. Ayrıca, her bir uygulama örneğinin yaşam döngüsü bağımsız olarak yönetilebilir (örneğin, yükseltilebilir). Aşağıdaki diyagramda, uygulama türlerinin hizmet türlerinden nasıl oluşturulduğu gösterilir. Bu, sırasıyla kod, yapılandırma ve veri paketlerinden oluşur. Diyagramı basitleştirmek için, yalnızca için kod/yapılandırma/veri paketleri `ServiceType4` gösterilir, ancak her hizmet türü bu paket türlerini içerebilir.

![Uygulama türlerini ve hizmet türlerini Service Fabric][cluster-imagestore-apptypes]

Kümede etkin bir veya daha fazla hizmet türü örneği olabilir. Örneğin, durum bilgisi olan hizmet örnekleri veya çoğaltmalar, kümedeki farklı düğümlerde bulunan çoğaltmalar arasında durumu çoğaltarak yüksek güvenilirlik elde edin. Çoğaltma temelde, bir kümedeki bir düğüm başarısız olsa bile hizmetin kullanılabilir olması için artıklık sağlar. [Bölümlenmiş bir hizmet](service-fabric-concepts-partitioning.md) , kümedeki düğümler arasında durumunu daha da ayırır (ve bu duruma kadar erişim desenleri).

Aşağıdaki diyagramda uygulamalar ve hizmet örnekleri, bölümler ve çoğaltmalar arasındaki ilişki gösterilmektedir.

![Bir hizmet içindeki bölümler ve çoğaltmalar][cluster-application-instances]

> [!TIP]
> Http:// &lt; yourclusteraddress &gt; : 19080/Explorer adresinde bulunan Service Fabric Explorer aracını kullanarak bir kümedeki uygulamaların yerleşimini görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Service Fabric Explorer kümeniz görselleştirme](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Sonraki adımlar
- [Uygulama ölçeklenebilirliği](service-fabric-concepts-scalability.md)hakkında bilgi edinin.
- Hizmet [durumu](service-fabric-concepts-state.md), [bölümleme](service-fabric-concepts-partitioning.md)ve [kullanılabilirlik](service-fabric-availability-services.md)hakkında bilgi edinin.
- [Uygulama ve hizmet bildirimlerinde](service-fabric-application-and-service-manifests.md)uygulamaların ve hizmetlerin nasıl tanımlandıkları hakkında bilgi edinin.
- [Uygulama barındırma modelleri](service-fabric-hosting-model.md) , dağıtılan bir hizmetin ve hizmet ana bilgisayar işleminin çoğaltmaları (veya örnekleri) arasındaki ilişkiyi anlatmaktadır.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


