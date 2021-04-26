---
title: Service Fabric programlama modeline genel bakış
description: 'Service Fabric, hizmetler oluşturmak için iki çerçeve sunar: aktör çerçevesi ve hizmetler çerçevesi. Basitlik ve denetim açısından ayrı bir denge sunar.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575729"
---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric programlama modeline genel bakış

Service Fabric hizmetlerinizi yazmak ve yönetmek için birçok yol sunar. Hizmetler, platformun özelliklerinden ve uygulama çerçevelerinden tam olarak yararlanmak için Service Fabric API 'Lerini kullanmayı seçebilir. Hizmetler Ayrıca, Service Fabric kümesinde barındırılan bir kapsayıcıda çalışan herhangi bir dilde veya kodda yazılmış derlenmiş yürütülebilir program da olabilir.

## <a name="guest-executables"></a>Konuk yürütülebilir dosyaları

[Konuk yürütülebilir dosyası](service-fabric-guest-executables-introduction.md) , uygulamanızda bir hizmet olarak çalıştırılabilen mevcut, rastgele bir yürütülebilir dosyadır (herhangi bir dilde yazılmış). Konuk yürütülebilir dosyaları, Service Fabric SDK API 'Lerini doğrudan çağırmaz. Ancak, Service Fabric tarafından kullanıma sunulan REST API 'Lerini çağırarak hizmet bulunabilirliği, özel sistem durumu ve yükleme raporlaması gibi platform tekliflerinin özelliklerinden de faydalanır. Ayrıca, tam uygulama yaşam döngüsü desteği de vardır.

İlk [Konuk yürütülebilir uygulamanızı](service-fabric-deploy-existing-app.md)dağıtarak Konuk yürütülebilir dosyaları kullanmaya başlayın.

## <a name="containers"></a>Kapsayıcılar

Varsayılan olarak, Service Fabric Hizmetleri işlem olarak dağıtır ve etkinleştirir. Service Fabric Ayrıca, [kapsayıcılardaki](service-fabric-containers-overview.md)Hizmetleri dağıtabilir. Service Fabric, Windows Server 2016 ve üzeri sürümlerde Linux kapsayıcıları ve Windows kapsayıcıları dağıtımını destekler. Kapsayıcı görüntüleri, herhangi bir kapsayıcı deposundan çekilir ve makineye dağıtılabilir. Mevcut uygulamaları Konuk yürütülebilir dosyalar olarak dağıtabilir, durum bilgisiz veya durum bilgisi olan güvenilir hizmetler veya kapsayıcılarda Reliable Actors Service Fabric ve aynı uygulamadaki kapsayıcılardaki süreçler ve hizmetlerde Hizmetleri karıştırabilirsiniz.

[Windows veya Linux 'ta hizmetlerinizin Kapsayıcılı hale getirme hakkında daha fazla bilgi edinin](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services, Service Fabric platformuyla tümleştirilen ve platform özelliklerinin tam kümesinden faydalanabilir hizmetler yazmak için kullanılan hafif bir çerçevedir. Reliable Services, Service Fabric çalışma zamanının hizmetlerinizin yaşam döngüsünü yönetmesine ve hizmetlerinizin çalışma zamanıyla etkileşime girmesine izin veren en az bir API kümesi sağlar. Uygulama çerçevesi en düşük düzeydedir, tasarım ve uygulama seçenekleri üzerinde tam denetim sağlar ve ASP.NET Core gibi başka bir uygulama çerçevesini barındırmak için kullanılabilir.

Reliable Services, hizmetin her örneğinin eşit olarak oluşturulduğu ve Azure DB veya Azure Tablo depolaması gibi bir dış çözümde kalıcı olduğu Web sunucuları gibi çoğu hizmet platformlarına benzer şekilde durum bilgisiz olabilir.

Service Fabric dışlamalı, Reliable Services durumu durum bilgisi olabilir, burada durum, güvenilir koleksiyonlar kullanılarak doğrudan hizmette kalıcı hale getirilir. Durum, çoğaltma aracılığıyla yüksek oranda kullanılabilir hale getirilir ve bölümleme aracılığıyla dağıtılır, Service Fabric tarafından otomatik olarak yönetilir.

[Reliable Services hakkında daha fazla bilgi edinin](service-fabric-reliable-services-introduction.md) veya [ilk güvenilir hizmetinizi yazmaya](service-fabric-reliable-services-quick-start.md)başlayın.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core, Web uygulamaları, IoT uygulamaları ve mobil arka uçlar gibi modern bulut tabanlı Internet 'e bağlı uygulamalar oluşturmaya yönelik açık kaynaklı, platformlar arası bir çerçevedir. Service Fabric, güvenilir koleksiyonlardan ve Service Fabric gelişmiş düzenleme özelliğinden yararlanan hem durum bilgisi olmayan hem de durum bilgisi olan ASP.NET Core uygulamaları yazmak için ASP.NET Core ile tümleşir.

[Service Fabric ASP.NET Core hakkında daha fazla bilgi edinin](service-fabric-reliable-services-communication-aspnetcore.md) veya [ilk ASP.NET Core Service Fabric uygulamanızı yazarak](service-fabric-tutorial-create-dotnet-app.md)çalışmaya başlayın.

## <a name="reliable-actors"></a>Reliable Actors

Reliable Services üzerine inşa edilen güvenilir aktör çerçevesi, hesaplama [aktör modeline](https://en.wikipedia.org/wiki/Actor_model)göre [sanal aktör](https://research.microsoft.com/en-us/projects/orleans/) modelini uygulayan bir uygulama çerçevesidir. Güvenilir aktör çerçevesi, *aktör* olarak adlandırılan tek iş parçacıklı yürütme ile bağımsız işlem ve durum birimleri kullanır. Güvenilir aktör çerçevesi, aktörler ve önceden ayarlanmış durum kalıcılığı ve genişleme yapılandırmalarına yönelik yerleşik iletişim sağlar.

Reliable Actors, Reliable Services yerleşik bir uygulama çerçevesi olduğundan, platform tarafından sunulan özelliklerin tam kümesinden Service Fabric platformu ve avantajları ile tamamen tümleşiktir.

[Reliable Actors hakkında daha fazla bilgi edinin](service-fabric-reliable-actors-introduction.md) veya [ilk güvenilir aktör hizmetinizi yazmaya](service-fabric-reliable-actors-get-started.md) başlayın

[ASP.NET Core kullanarak ön uç hizmeti oluşturma](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Sonraki adımlar

[Service Fabric ve kapsayıcılara genel bakış](service-fabric-containers-overview.md)

[Reliable Services özelliğine genel bakış](service-fabric-reliable-services-introduction.md)

[Reliable Actors hizmetine genel bakış](service-fabric-reliable-actors-introduction.md)

[Service Fabric ve ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
