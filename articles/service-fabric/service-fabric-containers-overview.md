---
title: Service Fabric ve kapsayıcılara genel bakış | Microsoft Docs
description: Mikro hizmet uygulamalarını dağıtmak için Service Fabric ve kapsayıcıların kullanımına genel bakış. Bu makalede, kapsayıcıların nasıl kullanılabileceği ve Service Fabric içindeki kullanılabilir yetenekler hakkında genel bir bakış sunulmaktadır.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: atsenthi
ms.openlocfilehash: 2ed3a9d4b1ec219d22a9e01e7acec5d7e950289b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599769"
---
# <a name="service-fabric-and-containers"></a>Service Fabric ve kapsayıcılar

## <a name="introduction"></a>Giriş

Azure Service Fabric; ölçeklenebilir ve güvenilir mikro hizmetleri ve kapsayıcıları paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştırmayı sağlayan bir dağıtılmış sistemler platformudur.

Service Fabric, mikro hizmetleri bir makine kümesi genelinde dağıtmaya yönelik Microsoft 'un [kapsayıcı Orchestrator](service-fabric-cluster-resource-manager-introduction.md) ' dir. , Microsoft 'ta hizmetler çalıştıran ve büyük ölçekli bir ölçekte öğrendiğimiz dersler avantajlarından Service Fabric.

Mikro hizmetler, [Service Fabric programlama modelleri](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) kullanmaktan [tercih ettiğiniz herhangi bir kodun](service-fabric-guest-executables-introduction.md) dağıtılmasına kadar birçok yolla geliştirilebilir. Ya da yalnızca [kapsayıcıları dağıtmak ve yönetmek](service-fabric-containers-overview.md)istiyorsanız, Service Fabric de harika bir seçenektir.

Varsayılan olarak, Service Fabric bu Hizmetleri işlem olarak dağıtır ve etkinleştirir. Süreçler, bir kümedeki kaynakların en hızlı etkinleştirilmesini ve en yüksek yoğunluklu kullanımını sağlar. Service Fabric Ayrıca, kapsayıcı görüntülerinde Hizmetleri dağıtabilir. Aynı uygulamadaki Hizmetleri süreçler ve kapsayıcılardaki hizmetler de karıştırabilirsiniz.

Sağ tarafta geçiş yapmak ve Service Fabric kapsayıcıları denemek için hızlı başlangıç, öğretici veya örnek kullanmayı deneyin:  

[Hızlı Başlangıç: Service Fabric için bir Linux kapsayıcı uygulaması dağıtma](service-fabric-quickstart-containers-linux.md)  
[Hızlı Başlangıç: Service Fabric için bir Windows kapsayıcı uygulaması dağıtma](service-fabric-quickstart-containers.md)  
[Mevcut bir .NET uygulamasını Kapsayıcılı hale getirme](service-fabric-host-app-in-a-container.md)  
[Service Fabric Kapsayıcı Örnekleri](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Kapsayıcılar nelerdir

Kapsayıcılar, uygulamanın içinde çalışması için sabit bir ortam sağlayarak, uygulamaları farklı bilgi işlem ortamlarında güvenilir bir şekilde çalıştırma sorununu çözüyor. Kapsayıcılar, bir uygulamayı ve kitaplık ve yapılandırma dosyaları gibi tüm bağımlılıklarını, bir uygulamayı kapsayıcı içinde çalıştırmak için gereken her şeyi içeren kendi yalıtılmış ' Box ' olarak kaydırır. Kapsayıcının çalıştığı her yerde, her zaman içindeki uygulamanın, bağımlı kitaplıklarının doğru sürümleri, tüm yapılandırma dosyaları ve çalışması gereken diğer her şey gibi çalışması gereken her şey vardır.

Kapsayıcılar doğrudan çekirdeğin üzerine çalışır ve dosya sisteminin ve diğer kaynakların yalıtılmış bir görünümüne sahiptir. Bir kapsayıcıdaki uygulamanın, kapsayıcısı dışındaki diğer uygulamalar veya işlemlerle ilgili hiçbir bilgisi yoktur. Her uygulama ve çalışma zamanı, bağımlılıkları ve sistem kitaplıkları, kapsayıcının işletim sisteminin yalıtılmış görünümüne tam ve özel erişimi olan bir kapsayıcı içinde çalışır. Farklı bilgi işlem ortamlarında çalışması gereken tüm uygulamanızın bağımlılıklarını sağlamayı kolaylaştıran güvenlik ve kaynak yalıtımının yanı sıra, Service Fabric ile kapsayıcıları kullanmanın önemli avantajları vardır. Bu, aksi halde Hizmetleri bir işle.

Sanal makinelerle karşılaştırıldığında kapsayıcılar aşağıdaki avantajları sağlar:

* **Küçük**: Kapsayıcılar, verimliliği artırmak için tek bir depolama alanı ve katman sürümü ve güncelleştirme kullanır.
* **Hızlı**: Kapsayıcılar, genellikle Saniyeler içinde daha hızlı başlayabilmeleri için bir işletim sisteminin tamamını önyüklemek zorunda kalmaz.
* **Taşınabilirlik**: Kapsayıcılı bir uygulama görüntüsü bulutta, şirket içinde, sanal makinelerde veya doğrudan fiziksel makinelerde çalışmak üzere olabilir.
* **Kaynak**İdaresi: Bir kapsayıcı, ana bilgisayarında tüketebileceği fiziksel kaynakları sınırlayabilir.

### <a name="container-types-and-supported-environments"></a>Kapsayıcı türleri ve desteklenen ortamlar

Service Fabric hem Linux hem de Windows üzerinde kapsayıcıları destekler ve Windows üzerinde Hyper-V yalıtım modunu destekler.

#### <a name="docker-containers-on-linux"></a>Linux üzerinde Docker Kapsayıcıları

Docker, Linux çekirdek kapsayıcıları üzerinde kapsayıcılar oluşturmak ve yönetmek için API 'Ler sağlar. Docker Hub, kapsayıcı görüntülerini depolamak ve almak için merkezi bir depo sağlar.
Linux tabanlı bir öğretici için bkz. [Linux üzerinde ilk Service Fabric kapsayıcı uygulamanızı oluşturma](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Windows Server kapsayıcıları

Windows Server 2016, yalıtım düzeyine göre farklılık gösteren iki farklı kapsayıcı türü sağlar. Windows Server kapsayıcıları ve Docker kapsayıcıları, hem ad alanı hem de dosya sistemi yalıtımı içerdiğinden benzerdir çünkü çekirdek, üzerinde çalıştıkları konakla birlikte paylaşımda bulunur. Linux 'ta, bu yalıtım geleneksel olarak cgroups ve ad alanları tarafından sağlanmış ve Windows Server kapsayıcıları benzer şekilde davranır.

Hyper-V desteğiyle Windows kapsayıcıları, hiçbir kapsayıcı işletim sistemi çekirdeğini başka bir kapsayıcı veya konakla paylaştığı için daha fazla yalıtım ve güvenlik sağlar. Bu daha yüksek güvenlik yalıtımıyla, Hyper-V etkin kapsayıcılar, büyük olasılıkla çok kiracılı senaryolara yöneliktir.
Windows tabanlı bir öğretici için bkz. [Windows 'da ilk Service Fabric kapsayıcı uygulamanızı oluşturma](service-fabric-get-started-containers.md).

Aşağıdaki şekilde, kullanılabilecek farklı sanallaştırma ve yalıtım düzeyi türleri gösterilmektedir.
![Service Fabric platform][Image1]

## <a name="scenarios-for-using-containers"></a>Kapsayıcıları kullanmaya yönelik senaryolar

Bir kapsayıcının iyi bir seçenek olduğu tipik örnekler aşağıda verilmiştir:

* **IIS kaldırma ve kaydırma**: Mevcut bir [ASP.NET MVC](https://www.asp.net/mvc) uygulamasını ASP.NET Core geçirmek yerine bir kapsayıcıya yerleştirebilirsiniz. Bu ASP.NET MVC uygulamaları Internet Information Services (IIS) bağımlıdır. Bu uygulamaları, önceden düzenlenen IIS görüntüsünden kapsayıcı görüntülerine paketleyebilir ve Service Fabric ile dağıtabilirsiniz. Windows kapsayıcıları hakkında bilgi için bkz. [Windows Server 'Da kapsayıcı görüntüleri](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) .

* **Kapsayıcıları ve Service Fabric mikro hizmetleri karıştırma**: Uygulamanızın bir parçası için mevcut bir kapsayıcı görüntüsünü kullanın. Örneğin, uygulamanızın Web ön ucu için [NGINX kapsayıcısını](https://hub.docker.com/_/nginx/) ve daha yoğun arka uç hesaplaması için durum bilgisi olan hizmetleri kullanabilirsiniz.

* **"Gürültülü komşular" hizmetlerinin etkisini azaltın**: Bir hizmetin bir konakta kullandığı kaynakları kısıtlamak için kapsayıcıların kaynak idare yeteneğini kullanabilirsiniz. Hizmetler çok sayıda kaynak tüketebilir ve diğerlerinin performansını etkileiyorsa (uzun süreli, sorgu benzeri bir işlem), bu Hizmetleri kaynak İdaresi olan kapsayıcılara yerleştirmeyi göz önünde bulundurun.

## <a name="service-fabric-support-for-containers"></a>Kapsayıcılar için Service Fabric desteği

Service Fabric, Linux üzerinde Docker kapsayıcıları ve Windows Server 2016 ' de Windows Server kapsayıcıları ile birlikte Hyper-V yalıtım modu desteğini destekler. 

Service Fabric, kapsayıcının birden çok hizmet çoğaltmalarının yerleştirildiği bir uygulama konağını temsil ettiği bir [uygulama modeli](service-fabric-application-model.md) sağlar. Service Fabric Ayrıca, yerleşik Service Fabric programlama modellerini kullanmadığınız [Konuk yürütülebilir bir senaryoyu](service-fabric-guest-executables-introduction.md) destekler, bunun yerine bir kapsayıcı içinde herhangi bir dil veya çerçeve kullanılarak yazılmış mevcut bir uygulamayı paketleyin. Bu senaryo, kapsayıcılar için ortak kullanım durumdur.

Ayrıca, [bir kapsayıcı içinde Service Fabric Hizmetleri](service-fabric-services-inside-containers.md)de çalıştırabilirsiniz. Kapsayıcılar içinde Service Fabric Hizmetleri çalıştırmaya yönelik destek şu anda sınırlı.

Service Fabric, Kapsayıcılı mikro hizmetlerden oluşan uygulamalar oluşturmanıza yardımcı olan birkaç kapsayıcı özelliği sağlar; örneğin:

* Kapsayıcı görüntüsü dağıtımı ve etkinleştirmesi.
* Azure kümelerinde varsayılan olarak kaynak değerlerini ayarlama dahil kaynak yönetimi.
* Depo kimlik doğrulaması.
* Konak bağlantı noktası eşlemesinde kapsayıcı bağlantı noktası.
* Kapsayıcı-kapsayıcı bulma ve iletişim.
* Ortam değişkenlerini yapılandırma ve ayarlama yeteneği.
* Kapsayıcıda güvenlik kimlik bilgilerini ayarlama yeteneği.
* Kapsayıcılar için farklı ağ modları seçimi.

Azure Kubernetes hizmeti ile bir Kubernetes kümesi oluşturma, Azure Container Registry 'da özel bir Docker kayıt defteri oluşturma ve daha fazlası gibi Azure 'da kapsayıcı desteğine yönelik kapsamlı bir genel bakış için bkz. [kapsayıcılar Için Azure](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, çalışan kapsayıcılar için Service Fabric destek hakkında bilgi edindiniz. Daha sonra, bunları nasıl kullanacağınızı göstermek için özelliklerin her birine örnek olarak gideceğiz.

[Linux üzerinde ilk Service Fabric kapsayıcı uygulamanızı oluşturma](service-fabric-get-started-containers-linux.md)  
[Windows 'da ilk Service Fabric kapsayıcı uygulamanızı oluşturma](service-fabric-get-started-containers.md)  
[Windows kapsayıcıları hakkında daha fazla bilgi edinin](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png