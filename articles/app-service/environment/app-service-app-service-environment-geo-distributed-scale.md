---
title: App Service ortamları ile coğrafi olarak dağıtılmış ölçek-Azure
description: Traffic Manager ve App Service ortamları ile coğrafi dağıtım kullanarak uygulamaları yatay olarak ölçeklendirirsiniz.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: eaefebc569f5bf5461ff7c4407fa77a0c62d4fe8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070212"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>App Service Ortamları ile Coğrafi Olarak Dağıtılmış Ölçek
## <a name="overview"></a>Genel Bakış

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Çok yüksek ölçek gerektiren uygulama senaryoları, bir uygulamanın tek bir dağıtımına sunulan işlem kaynağı kapasitesini aşabilir.  Oylama uygulamaları, spor olayları ve teledenetlenen eğlence olayları, son derece yüksek ölçek gerektiren senaryolara örnek olarak verilebilir. Yüksek ölçekli gereksinimler, tek bir bölgede birden fazla uygulama dağıtımı ve bölgeler arasında, çok fazla yük gereksinimini işlemek üzere, uygulamalar arasında yatay olarak ölçeklendirerek karşılanacak.

App Service ortamlar yatay ölçek genişletme için ideal bir platformdur.  Bilinen bir istek oranını destekleyebilen bir App Service Ortamı yapılandırması seçildikten sonra, geliştiriciler istenen en yüksek yük kapasitesini karşılamak için "tanımlama bilgisi kesici" olarak ek App Service ortamları dağıtabilir.

Örneğin, bir App Service Ortamı yapılandırması üzerinde çalışan bir uygulamanın saniyede 20 KB istek (RPS) işlemek için test edildiğini varsayalım.  İstenen en yüksek yük kapasitesi 100K RPS ise, beş (5) App Service ortamlar oluşturulup uygulamanın en yüksek tahmini yükü işleyebileceğinden emin olmak için yapılandırılabilir.

Müşteriler genellikle özel bir (veya Vanity) etki alanı kullanarak uygulamalara erişdiklerinden, geliştiricilerin tüm App Service Ortamı örneklerine uygulama istekleri dağıtması için bir yol gerekir.  Bunu gerçekleştirmenin harika bir yolu da, [Azure Traffic Manager profilini][AzureTrafficManagerProfile]kullanarak özel etki alanını çözümlemelidir.  Traffic Manager profili, bireysel App Service ortamlarının tümünü gösterecek şekilde yapılandırılabilir.  Traffic Manager, Traffic Manager profilindeki Yük Dengeleme ayarlarına bağlı olarak, App Service ortamlarının tamamında müşteri dağıtmayı otomatik olarak işleyecek.  Bu yaklaşım, App Service ortamlarının tümünün tek bir Azure bölgesinde dağıtılıp dağıtılmadığına veya birden çok Azure bölgesinde dünya genelinde dağıtılıp dağıtılmasından bağımsız olarak çalışmaktadır.

Ayrıca, müşteriler uygulamalara gösterim etki alanı üzerinden erişdiklerinden, müşteriler bir uygulamayı çalıştıran App Service ortamların sayısını farkında değildir.  Sonuç olarak, geliştiriciler, gözlemlenen trafik yüküne göre ortamları hızlı ve kolay bir şekilde ekleyebilir ve kaldırabilir App Service.

Aşağıdaki kavramsal diyagramda, tek bir bölgedeki üç App Service ortamda yatay olarak ölçeklendirilmiş bir uygulama gösterilmektedir.

![Kavramsal mimari][ConceptualArchitecture] 

Bu konunun geri kalanında, birden çok App Service ortamı kullanarak örnek uygulama için dağıtılmış bir topoloji ayarlama ile ilgili adımlar adım adım gösterilmektedir.

## <a name="planning-the-topology"></a>Topolojiyi planlama
Dağıtılmış bir uygulama ayak izi oluşturmadan önce, daha önce bazı parça bilgilerine sahip olmaya yardımcı olur.

* **Uygulama için özel etki alanı:**  Müşterilerin uygulamaya erişmek için kullanacağı özel etki alanı adı nedir?  Örnek uygulama için özel etki alanı adı`www.scalableasedemo.com`
* **Traffic Manager etki alanı:**  Bir [Azure Traffic Manager profili][AzureTrafficManagerProfile]oluştururken bir etki alanı adının seçilmesi gerekir.  Bu ad, Traffic Manager tarafından yönetilen bir etki alanı girdisini kaydetmek için *trafficmanager.net* sonekiyle birleştirilir.  Örnek uygulama için, seçilen ad *ölçeklenebilir-Ao-demo*' dir.  Sonuç olarak, Traffic Manager tarafından yönetilen tam etki alanı adı *Scalable-ASE-demo.trafficmanager.net*' dir.
* **Uygulama parmak izini ölçeklendirmeye yönelik strateji:**  Uygulama parmak izi tek bir bölgedeki birden çok App Service ortamına dağıtılır mi?  Birden çok bölge mi?  Her iki yaklaşımdan de karıştırma ve eşleme yapılsın mı?  Karar, müşteri trafiğinin nereden kaynaklanmayacak beklentileri temel almalıdır, bunun yanı sıra bir uygulamanın destekleme arka uç altyapısının ne kadar iyi ölçekleyebileceğini de bilmelidir.  Örneğin,% 100 durum bilgisiz olmayan bir uygulamayla, Azure bölgesi başına birden çok App Service ortamının bir birleşimi kullanılarak, birden fazla Azure bölgesinde dağıtılan App Service ortamları ile çarpılarak bir uygulama daha büyük bir şekilde ölçeklendirilebilir.  ' Den seçim yapabileceğiniz 15 + ortak Azure bölgesi sayesinde müşteriler gerçek anlamda dünya genelinde bir hiper ölçekli uygulama parmak izi oluşturabilir.  Bu makalede kullanılan örnek uygulama için, tek bir Azure bölgesinde üç App Service ortamı oluşturulmuştur (Orta Güney ABD).
* **App Service ortamları için adlandırma kuralı:**  Her App Service Ortamı benzersiz bir ad gerektirir.  Bir veya iki App Service ortamının ötesinde, her bir App Service Ortamı tanımlamanızı sağlayacak bir adlandırma kuralına sahip olmak yararlıdır.  Örnek uygulama için basit bir adlandırma kuralı kullanılmıştır.  Üç App Service ortamının adları *fe1ase*, *fe2ase*ve *fe3ase*.
* **Uygulamalar için adlandırma kuralı:**  Uygulamanın birden çok örneği dağıtılırsa, dağıtılan uygulamanın her örneği için bir ad gereklidir.  App Service ortamların en az bilinen, çok kullanışlı bir özelliği, aynı uygulama adının birden çok App Service ortamında kullanılabilir olması olabilir.  Her App Service Ortamı benzersiz bir etki alanı sonekine sahip olduğundan, geliştiriciler her ortamda aynı uygulama adının aynısını yeniden kullanmayı seçebilir.  Örneğin, bir geliştirici şu şekilde adlandırılan uygulamalar olabilir: *MyApp.foo1.p.azurewebsites.net*, *MyApp.Foo2.p.azurewebsites.net*, *MyApp.Foo3.p.azurewebsites.net*, vb.  Örnek uygulama için, her bir uygulama örneğinin de benzersiz bir adı vardır.  Kullanılan uygulama örneği adları *webfrontend1*, *webfrontend2*ve *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Traffic Manager profili ayarlanıyor
Birden çok App Service ortamında bir uygulamanın birden fazla örneği dağıtıldığında, bireysel uygulama örnekleri Traffic Manager kaydedilebilir.  Örnek uygulama için, müşterileri aşağıdaki dağıtılan uygulama örneklerinden birine yönlendirebileceği *Scalable-ASE-demo.trafficmanager.net* için bir Traffic Manager profili gerekir:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  İlk App Service Ortamı dağıtılan örnek uygulamanın bir örneği.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  İkinci App Service Ortamı dağıtılan örnek uygulamanın bir örneği.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Üçüncü App Service Ortamı dağıtılan örnek uygulamanın bir örneği.

**Aynı** Azure bölgesinde çalışan birden çok Azure App Service uç noktasını kaydetmek için en kolay yol Powershell [Azure Resource Manager Traffic Manager desteğine][ARMTrafficManager]sahiptir.  

İlk adım bir Azure Traffic Manager profili oluşturmaktır.  Aşağıdaki kod, profilin örnek uygulama için nasıl oluşturulduğunu gösterir:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

*Relativednsname* parametresinin *ölçeklenebilir-Ao-demo*olarak nasıl ayarlandığına dikkat edin.  Bu, etki alanı adı *Scalable-ASE-demo.trafficmanager.net* oluşturma ve bir Traffic Manager profiliyle ilişkilendirilmesi.

*TrafficRoutingMethod* parametresi, tüm kullanılabilir uç noktalarda müşteri yükünün nasıl yayılacağı belirleme Traffic Manager yük dengeleme ilkesini tanımlar.  Bu örnekte *ağırlıklı* Yöntem seçilmiştir.  Bu, müşteri isteklerinin her bir uç nokta ile ilişkili görece ağırlıklarla ilgili tüm kayıtlı uygulama uç noktaları arasında yayılmasını sağlayacaktır. 

Profil oluşturulduğunda, her bir uygulama örneği profile yerel bir Azure uç noktası olarak eklenir.  Aşağıdaki kod her bir ön uç Web uygulamasına bir başvuru getirir ve sonra her uygulamayı *Targetresourceıd* parametresi aracılığıyla Traffic Manager uç noktası olarak ekler.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Her bir uygulama örneği için *Add-AzureTrafficManagerEndpointConfig* ' e bir çağrı nasıl olduğunu fark edin.  Her PowerShell komutunda *Targetresourceıd* parametresi, dağıtılan üç uygulama örneklerinden birine başvurur.  Traffic Manager profili, profilde kayıtlı olan üç uç noktanın yükünü yayacaktır.

Üç uç noktanın hepsi, *Ağırlık* parametresi için aynı değeri (10) kullanır.  Bu, müşteri isteklerinin tüm üç uygulama örneğine görece eşit olarak yayılmasına Traffic Manager neden olur. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Uygulamanın özel etki alanını Traffic Manager etki alanında gösterme
Gerekli son adım, uygulamanın özel etki alanını Traffic Manager etki alanında göstermelidir.  Örnek uygulama için bu işaret üzerine gelin `www.scalableasedemo.com`. `scalable-ase-demo.trafficmanager.net`  Bu adımın, özel etki alanını yöneten etki alanı kaydedicisi ile tamamlanması gerekir.  

Kaydedicinizin etki alanı yönetim araçlarını kullanarak, Traffic Manager etki alanında özel etki alanını işaret eden bir CNAME kayıtlarının oluşturulması gerekir.  Aşağıdaki resimde bu CNAME yapılandırmasının nasıl göründüğü hakkında bir örnek gösterilmektedir:

![Özel etki alanı için CNAME][CNAMEforCustomDomain] 

Bu konuda ele alınmasa da, her bir uygulama örneğinin özel etki alanının da kayıtlı olması gerektiğini unutmayın.  Aksi takdirde, bir istek bir uygulama örneğine bunu yapıyorsa ve uygulamanın uygulamayla birlikte kayıtlı özel etki alanı yoksa, istek başarısız olur.  

Bu örnekte, özel etki alanı olur `www.scalableasedemo.com`ve her uygulama örneğine ilişkili özel etki alanı vardır.

![Özel Etki Alanı][CustomDomain] 

Azure App Service uygulamalarla özel bir etki alanı kaydetmenin bir üst sınırı için, [özel etki alanlarını kaydettirme][RegisterCustomDomain]konusunda aşağıdaki makaleye bakın.

## <a name="trying-out-the-distributed-topology"></a>Dağıtılmış topoloji deneniyor
Traffic Manager ve DNS yapılandırmasının nihai sonucu, için `www.scalableasedemo.com` istekleri aşağıdaki sırayla akacaktır:

1. Tarayıcı veya cihaz, için DNS araması yapar`www.scalableasedemo.com`
2. Etki alanı kaydedicisinde CNAME girişi DNS aramasının Azure Traffic Manager yeniden yönlendirilmesine neden olur.
3. Azure Traffic Manager DNS sunucularından birine karşı *Scalable-ASE-demo.trafficmanager.net* IÇIN bir DNS araması yapılır.
4. Yük Dengeleme ilkesine (Traffic Manager profili oluştururken daha önce kullanılan *TrafficRoutingMethod* parametresi) bağlı olarak, Traffic Manager yapılandırılmış uç noktalardan birini seçer ve bu uç noktanın FQDN 'sini tarayıcıya veya cihaza döndürür.
5. Uç noktanın FQDN 'Si App Service Ortamı çalışan bir uygulama örneğinin URL 'Si olduğundan, tarayıcı veya cihaz, bir Microsoft Azure DNS sunucusunun FQDN 'yi bir IP adresine çözümmesini isteyecektir. 
6. Tarayıcı veya cihaz HTTP/S isteğini IP adresine gönderir.  
7. İstek, App Service ortamlarından birinde çalışan uygulama örneklerinden birine gönderilir.

Aşağıdaki konsol resmi, örnek uygulamanın özel etki alanı için bir DNS aramasını, üç örnek App Service ortamlarından birinde çalışan bir uygulama örneğine başarıyla çözümlemenizi sağlar (Bu örnekte üç App Service ortamının ikinci adı):

![DNS arama][DNSLookup] 

## <a name="additional-links-and-information"></a>Ek bağlantılar ve bilgiler
PowerShell [Azure Resource Manager Traffic Manager Destek][ARMTrafficManager]' i okuyun.  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
