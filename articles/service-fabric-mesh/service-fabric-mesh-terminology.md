---
title: Azure Service Fabric ağı terminolojisi | Microsoft Docs
description: Bu makalede, belgelerde kullanılan koşulları daha iyi anlamanıza yardımcı olmak üzere Azure Service Fabric ağı tarafından kullanılan terminoloji ayrıntılı olarak açıklanır.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cacf3a7419982e8c0e8d55700c903e3414cfcbb4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663018"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric kafes terminolojisi

Azure Service Fabric Mesh, geliştiricilerin sanal makineleri, depolama alanını veya ağ bileşenlerini yönetmeden mikro hizmet uygulamaları dağıtmasını sağlayan tam olarak yönetilen bir hizmettir. Bu makalede, belgelerde kullanılan koşulları daha iyi anlamanıza yardımcı olmak üzere Azure Service Fabric ağı tarafından kullanılan terminoloji ayrıntılı olarak açıklanır.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) ölçeklenebilir ve güvenilir mikro hizmetleri paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştıran açık kaynaklı bir dağıtılmış sistemler platformudur. Service Fabric, Service Fabric ağı destekleyen Orchestrator. Service Fabric, mikro hizmet uygulamalarınızı nasıl oluşturup çalıştıracağınızı gösteren seçenekler sağlar. Hizmetlerinizi yazmak için herhangi bir çerçeveyi kullanabilir ve uygulamayı birden çok ortam seçiminizden nerede çalıştıracağınızı seçebilirsiniz.

## <a name="application-and-service-concepts"></a>Uygulama ve hizmet kavramları

**Service Fabric kafes uygulaması**: Service Fabric ağ uygulamaları, [kaynak modeli](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML ve JSON kaynak dosyaları) tarafından tanımlanır ve Service Fabric çalıştığı herhangi bir ortama dağıtılabilir.

Yerel **uygulama Service Fabric**: Service Fabric yerel uygulamalar, [yerel uygulama modeliyle](/azure/service-fabric/service-fabric-application-model) (XML tabanlı uygulama ve hizmet bildirimleri) açıklanır.  Service Fabric yerel uygulamalar Service Fabric ağ içinde çalıştırılamaz.

**Uygulama**: bir Service Fabric kafes uygulaması, bir kafes uygulamasının dağıtım, sürüm oluşturma ve yaşam süresinin birimidir. Her uygulama örneğinin yaşam döngüsü bağımsız olarak yönetilebilir.  Uygulamalar bir veya daha fazla hizmet kodu paketi ve ayarlarından oluşur. Bir uygulama, Azure kaynak modeli (RM) şeması kullanılarak tanımlanır.  Hizmetler, bir RM şablonundaki uygulama kaynağının özellikleri olarak açıklanır.  Uygulama tarafından kullanılan ağlara ve birimlere uygulama tarafından başvurulur.  Bir uygulama oluştururken, uygulama, hizmet ve birimler Service Fabric kaynak modeli kullanılarak modellenir.

**Hizmet**: bir uygulamadaki bir hizmet mikro hizmeti temsil eder ve bir tamamen ve tek başına işlevi gerçekleştirir. Her hizmet, kod paketiyle ilişkili kapsayıcı görüntüsünü çalıştırmak için gereken her şeyi tanımlayan bir veya daha fazla kod paketinden oluşur.  Bir uygulamadaki hizmet çoğaltmalarının sayısı, dışarı ve dışarı ölçeklenebilirler.

**Kod paketi**: kod paketleri aşağıdakiler de dahil olmak üzere kod paketiyle ilişkili kapsayıcı görüntüsünü çalıştırmak için gereken her şeyi anlatmaktadır:

* Kapsayıcı adı, sürümü ve kayıt defteri
* Her kapsayıcı için gereken CPU ve bellek kaynakları
* Ağ uç noktaları
* Kapsayıcıda takılacak ve ayrı bir birim kaynağına başvuran birimler.

## <a name="deployment-and-application-models"></a>Dağıtım ve uygulama modelleri 

Hizmetlerinizi dağıtmak için, bunların nasıl çalıştırılacağını açıklamanız gerekir. Service Fabric üç farklı dağıtım modelini destekler:

### <a name="resource-model"></a>Kaynak modeli
Service Fabric kaynaklar Service Fabric için tek tek dağıtılabilecek her şeydir; uygulamalar, hizmetler, ağlar ve birimler dahil. Kaynaklar bir küme uç noktasına dağıtılabilecek bir JSON dosyası kullanılarak tanımlanır.  Service Fabric ağ için, Azure kaynak modeli şeması kullanılır. Bir YAML dosya şeması, tanım dosyalarını daha kolay yazmak için de kullanılabilir. Kaynaklar Service Fabric her yerde dağıtılabilir. Kaynak modeli, Service Fabric uygulamalarınızı tanımlamanın en kolay yoludur. Ana odağı Kapsayıcılı hizmetlerin basit dağıtım ve yönetimine dayanır. Daha fazla bilgi edinmek için [Service Fabric kaynak modeline giriş](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)makalesini okuyun.

### <a name="native-model"></a>Yerel model
Yerel uygulama modeli, uygulamalarınızı Service Fabric için tam alt düzey erişim sağlar. Uygulamalar ve hizmetler, XML bildirim dosyalarında kayıtlı türler olarak tanımlanır.

Yerel model, ve Java 'daki C# Service Fabric çalışma zamanı API 'lerine ve küme yönetim API 'lerine erişim sağlayan Reliable Services çerçevesini destekler. Yerel model ayrıca rastgele kapsayıcıları ve yürütülebilir dosyaları destekler.

Yerel model Service Fabric kafes ortamında desteklenmez.  Daha fazla bilgi için bkz. [programlama modeline genel bakış](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) Docker projesinin bir parçasıdır. Service Fabric, Docker Compose modelini kullanarak uygulama dağıtmaya yönelik sınırlı destek sağlar.

## <a name="environments"></a>Ortamlar

Service Fabric, birçok farklı hizmet ve ürünün temel aldığı açık kaynaklı bir platform teknolojisidir. Microsoft aşağıdaki seçenekleri sağlar:

 - **Service Fabric ağ**: Microsoft Azure Service Fabric uygulamaları çalıştırmak için tam olarak yönetilen bir hizmet.
 - **Azure Service Fabric**: Azure 'da barındırılan Service Fabric kümesi sunumu. Service Fabric kümelerinin yükseltme ve yapılandırma yönetimiyle birlikte Service Fabric ve Azure altyapısı arasında tümleştirme sağlar.
 - **Tek başına Service Fabric**: [Service Fabric kümelerini her yerde](/azure/service-fabric/service-fabric-deploy-anywhere) (Şirket içinde veya herhangi bir bulut sağlayıcısında) dağıtmak için bir yükleme ve yapılandırma araçları kümesi. Azure tarafından yönetilmiyor.
 - **Service Fabric geliştirme kümesi**: Service Fabric uygulamaların geliştirilmesi için Windows, Linux veya Mac 'te yerel bir geliştirme deneyimi sağlar.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Ortam, çerçeve ve dağıtım modeli destek matrisi
Farklı ortamların çerçeveler ve dağıtım modelleri için farklı destek düzeyi vardır. Aşağıdaki tabloda desteklenen Framework ve dağıtım modeli birleşimleri açıklanmaktadır.

| Uygulama türü | Açıklanan | Azure Service Fabric ağı | Azure Service Fabric kümeleri (herhangi bir işletim sistemi)| Yerel küme | Tek başına küme |
|---|---|---|---|---|---|
| Service Fabric kafes uygulamaları | Kaynak modeli (YAML & JSON) | Destekleniyor |Desteklenmiyor | Windows-desteklenen, Linux ve Mac-desteklenmez | Windows-desteklenmiyor |
|Yerel uygulamalar Service Fabric | Yerel uygulama modeli (XML) | Desteklenmiyor| Destekleniyor|Destekleniyor|Windows-destekleniyor|

Aşağıdaki tabloda, Service Fabric karşı farklı uygulama modelleri ve araçları açıklanmaktadır.

| Uygulama türü | Açıklanan | Visual Studio | Eclipse | SFCTL | AZ CLı | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric kafes uygulamaları | Kaynak modeli (YAML & JSON) | VS 2017 |Desteklenmiyor |Desteklenmiyor | Yalnızca desteklenen ağ ortamı | Desteklenmiyor|
|Yerel uygulamalar Service Fabric | Yerel uygulama modeli (XML) | VS 2017 ve VS 2015| Destekleniyor|Destekleniyor|Destekleniyor|Destekleniyor|

## <a name="next-steps"></a>Sonraki adımlar

Service Fabric Mesh hakkında daha fazla bilgi edinmek için [Service Fabric Mesh’e genel bakış](service-fabric-mesh-overview.md) makalesini okuyun.

[Sık sorulan sorulara](service-fabric-mesh-faq.md) yanıtlar bulun.
