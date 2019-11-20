---
title: Uygulama senaryoları ve tasarımı | Microsoft Docs
description: Service Fabric içindeki bulut uygulamalarının kategorilerine genel bakış. Durum bilgisiz ve durum bilgisi olmayan hizmetler kullanan uygulama tasarımını açıklar.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: d72e124248887690ca26c2676e8036cb1b5c4339
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303508"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric uygulama senaryoları
Azure Service Fabric, birçok iş uygulaması ve hizmeti türünü yazıp çalıştırabileceğiniz güvenilir ve esnek bir platform sunar. Bu uygulamalar ve mikro hizmetler durum bilgisiz veya durum bilgisi olabilir ve verimliliği en üst düzeye çıkarmak için sanal makineler arasında kaynak dengelenebilir. 

Service Fabric benzersiz mimarisi, uygulamalarınızda neredeyse gerçek zamanlı veri analizi, bellek içi hesaplama, paralel işlemler ve olay işleme işlemlerini gerçekleştirmenize olanak sağlar. Değişen kaynak gereksinimlerinize bağlı olarak uygulamalarınızı kolayca veya azaltabilirsiniz (gerçekten de veya dışarı) ölçeklendirebilirsiniz.

Uygulamalar oluşturmaya yönelik tasarım kılavuzu için [Azure Service Fabric mikro hizmet mimarisini](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) ve [Service Fabric kullanarak uygulama tasarımı için en iyi yöntemleri](service-fabric-best-practices-applications.md)okuyun.

Aşağıdaki uygulama türleri için Service Fabric platformunu kullanmayı göz önünde bulundurun:

* **Veri toplama, işleme ve IoT**: Service Fabric büyük ölçeği işler ve durum bilgisi olan hizmetler aracılığıyla düşük gecikme süresine sahiptir. Cihaz ve hesaplama verilerinin birlikte bulunduğu milyonlarca cihazda verileri işlemeye yardımcı olabilir.

    Service Fabric kullanarak IoT Hizmetleri oluşturan müşteriler, [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL yapımı](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Elektrik](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)ve [kafes sistemleri](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)içerir.

* **Oyun ve oturum tabanlı etkileşimli uygulamalar**: Service Fabric, uygulamanızın çevrimiçi oyun veya anlık mesajlaşma gibi düşük gecikmeli okuma ve yazma işlemleri gerektirmesi durumunda yararlıdır. Service Fabric, ayrı bir mağaza veya önbellek oluşturmak zorunda kalmadan bu etkileşimli ve durum bilgisi olan uygulamalar oluşturmanıza olanak sağlar. [Oyun hizmetlerinde Service Fabric kullanma](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)hakkında tasarım kılavuzu için [Azure oyun çözümlerini](https://azure.microsoft.com/solutions/gaming/) ziyaret edin.

    Oyun Hizmetleri olan müşteriler [sonraki oyunları](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) ve [daha fazlasını](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)içerir. Etkileşimli oturumları olan müşteriler [, Hololens ile birlikte](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)yer alır.

* **Veri analizi ve iş akışı işleme**: Service Fabric ' de iyileştirilmiş okuma ve yazma işlemleri için olayları veya veri akışını güvenilir bir şekilde işlemesi gereken uygulamalar. Service Fabric Ayrıca, sonuçların güvenilir olması ve bir sonraki işleme aşamasına hiçbir kayıp olmadan geçirilmesi gereken uygulama işleme işlem hatlarını destekler. Bu işlem hatları, veri tutarlılığı ve hesaplama garantisi açısından önemli olan işlem ve finansal sistemleri içerir.

    İş akışı hizmetleri oluşturan müşteriler [Zeiss grubunu](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [çekirdek Iş çözümlerini](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)ve [Société genel](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)' i içerir.

* **Verileri hesaplama**: Service Fabric yoğun veri hesaplamasını gerçekleştiren durum bilgisi olan uygulamalar oluşturmanıza olanak sağlar. Service Fabric, uygulamalarda işleme (hesaplama) ve verilerin birlikte kullanılmasına izin verir. 

   Normalde, uygulamanız verilere erişim gerektirdiğinde, bir dış veri önbelleği veya depolama katmanı ile ilişkili ağ gecikme süresi hesaplama süresini sınırlandırır. Durum bilgisi olan Service Fabric Hizmetleri, daha iyileştirilmiş okuma ve yazma işlemlerini etkinleştirerek gecikmeyi ortadan kaldırır. 
   
   Örneğin, müşteriler için neredeyse gerçek zamanlı öneri seçimlerini gerçekleştiren bir uygulama düşünün. Bu, 100 milisaniyeden daha kısa bir gidiş dönüş süresi gereksinimidir. Service Fabric hizmetlerinin gecikme ve performans özellikleri, kullanıcıya, uzak depolama alanından gerekli verileri getirmek zorunda kalmadan standart uygulama modeliyle karşılaştırıldığında, yanıt veren bir deneyim sağlar. Öneri seçimi hesaplaması, veri ve kurallarla birlikte bulunduğundan sistem daha hızlı yanıt veriyor.

    Hesaplama Hizmetleri oluşturan müşteriler, [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) ve [ınfosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)' i içerir.

* **Yüksek oranda kullanılabilir hizmetler**: Service Fabric birden çok ikincil hizmet çoğaltması oluşturarak hızlı yük devretme sağlar. Bir düğüm, işlem veya tek hizmet donanım ya da başka bir hata nedeniyle kapalıysa, ikincil çoğaltmalardan biri minimum hizmet kaybı olan birincil çoğaltmaya yükseltilir.

* **Ölçeklenebilir hizmetler**: tek tek hizmetler bölümlenebilir ve bu da durum küme genelinde ölçeklenmeye olanak tanır. Tek tek hizmetler de oluşturulabilir ve anında kaldırılabilir. Birkaç düğüm üzerindeki birkaç örnek için hizmeti, birçok düğümdeki binlerce örneğe ölçeklendirebilir ve gerektiğinde bunları yeniden ölçeklendirdirebilirsiniz. Bu hizmetleri derlemek ve bunların tüm yaşam döngülerini yönetmek için Service Fabric kullanabilirsiniz.

## <a name="application-design-case-studies"></a>Uygulama tasarımı örnek olay incelemeleri
Uygulama tasarlamak için Service Fabric nasıl kullanıldığını gösteren örnek olay incelemeleri, Azure sitelerindeki [müşteri hikayeleri](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) ve [mikro hizmetlerde](https://azure.microsoft.com/solutions/microservice-applications/) yayımlanır.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Durum bilgisiz ve durum bilgisi olan mikro hizmetlerden oluşan uygulamalar tasarlama
Azure Cloud Services çalışan rolleri ile uygulama oluşturmak, durum bilgisi olmayan bir hizmete örnektir. Bunun aksine, durum bilgisi olan mikro hizmetler, yetkili durumlarını isteğin ötesinde ve yanıtı olarak korur. Bu işlevsellik, çoğaltma tarafından desteklenen işlem garantisi sağlayan basit API 'Ler aracılığıyla yüksek kullanılabilirlik ve tutarlılık sağlar. 

Service Fabric ' de durum bilgisi olan hizmetler, yalnızca veritabanları ve diğer veri depolarından değil, tüm uygulama türleri için yüksek kullanılabilirlik sağlar. Bu, doğal bir ilerleme. Uygulamalar, NoSQL veritabanlarına yönelik yüksek kullanılabilirlik için tamamen ilişkisel veritabanlarını kullanarak zaten taşınmıştır. Artık uygulamaların kendileri, güvenilirlik, tutarlılık veya kullanılabilirlikten ödün vermeden, ek performans kazançları için kendilerine ait "etkin" durum ve veri içerebilir.

Mikro hizmetlerden oluşan uygulamalar oluştururken, genellikle durum bilgisiz olmayan ve durum bilgisi olan iş orta katman hizmetlerini çağıran durum bilgisiz Web Apps (ASP.NET ve Node. js gibi) birleşimine sahip olursunuz. Uygulamalar ve hizmetler, Service Fabric dağıtım komutları aracılığıyla aynı Service Fabric kümesine dağıtılır. Bu hizmetlerin her biri, ölçek, güvenilirlik ve kaynak kullanımıyla ilgili olarak bağımsızdır. Bu bağımsızlık geliştirme ve yaşam döngüsü yönetiminde çeviklik ve esneklik artar.

Durum bilgisi olan mikro hizmetler, yalnızca durum bilgisiz uygulamaların kullanılabilirlik ve gecikme süresi gereksinimlerini karşılamak için geleneksel olarak gerekli olan ek kuyruklar ve önbellekler gereksinimini kaldırdıklarından uygulama tasarımlarını basitleştirir. Durum bilgisi olan hizmetler yüksek kullanılabilirlik ve düşük gecikme süresine sahip olduğundan uygulamanızda yönetmek için daha az ayrıntı vardır. 

Aşağıdaki diyagramlarda, durum bilgisiz ve durum bilgisi olan bir uygulama tasarlama arasındaki farklar gösterilmektedir. [Reliable Services](service-fabric-reliable-services-introduction.md) ve [Reliable Actors](service-fabric-reliable-actors-introduction.md) programlama modellerinden yararlanarak, durum bilgisi olan hizmetler yüksek aktarım hızı ve düşük gecikme süresi sağlarken uygulama karmaşıklığını azaltır.

Aşağıda durum bilgisi olmayan hizmetler kullanan bir örnek uygulama vardır: ![Uygulama durumsuz hizmetler @ no__t-1

Durum bilgisi olmayan hizmetler kullanan bir örnek uygulama aşağıda verilmiştir: ![Uygulaması durum bilgisi olan hizmetler @ no__t-1

## <a name="next-steps"></a>Sonraki adımlar

* [Desenler ve senaryolar](service-fabric-patterns-and-scenarios.md)hakkında daha fazla bilgi edinin.

* Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) ve [Reliable Actors](service-fabric-reliable-actors-get-started.md) programlama modelleriyle durum bilgisiz ve durum bilgisi olmayan hizmetler oluşturmaya başlayın.
* [Azure 'da mikro hizmetler oluşturma](https://docs.microsoft.com/azure/architecture/microservices/)konusunda rehberlik için Azure mimari Merkezi ziyaret edin.
* Uygulama tasarım kılavuzu için [Azure Service Fabric uygulama ve küme en iyi uygulamalarına](service-fabric-best-practices-overview.md) gidin.

* Ayrıca aşağıdaki konulara bakın:
  * [Mikro hizmetler hakkında bilgi](service-fabric-overview-microservices.md)
  * [Hizmet durumunu tanımlama ve yönetme](service-fabric-concepts-state.md)
  * [Service Fabric hizmetlerinin kullanılabilirliği](service-fabric-availability-services.md)
  * [Service Fabric hizmetlerini ölçeklendirme](service-fabric-concepts-scalability.md)
  * [Bölüm Service Fabric Hizmetleri](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
