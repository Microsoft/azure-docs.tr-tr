---
title: App Service Ortamı v1 oluşturma-Azure
description: App Service ortamı v1 için oluşturma akışı açıklaması
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 017c79ec1341c85f3bd08393dd5553f90a2f6cef
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069751"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>App Service Ortamı v1 oluşturma 

> [!NOTE]
> Bu makale App Service Ortamı v1 ile ilgilidir. Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.
> 

### <a name="overview"></a>Genel Bakış
App Service Ortamı (Ao), çok kiracılı damgalar içinde kullanılamayan gelişmiş bir yapılandırma özelliği sunan Azure App Service bir Premium hizmet seçeneğidir. Ao özelliği, temel olarak Azure App Service müşterinin sanal ağına dağıtır. App Service ortamları tarafından sunulan yetenekler hakkında daha fazla bilgi edinmek için [App Service ortamı][WhatisASE] belgelerini okuyun.

### <a name="before-you-create-your-ase"></a>ATıCı 'nizi oluşturmadan önce
Değiştiremeyeceği şeyleri unutmamak önemlidir. Oluşturulduktan sonra Ao 'niz hakkında değişiklik değiştiremezsiniz:

* Location
* Subscription
* Kaynak Grubu
* Kullanılan VNet
* Kullanılan alt ağ 
* Alt ağ boyutu

VNet 'i seçerken ve bir alt ağ belirttiğinizde, gelecekteki büyümeye yetecek kadar büyük olduğundan emin olun. 

### <a name="creating-an-app-service-environment-v1"></a>App Service Ortamı v1 oluşturma
App Service ortamı v1 oluşturmak için Azure Marketi 'nde ***App Service ortamı v1***için arama yapabilir veya **kaynak** -> **Web ve mobil** -> **App Service ortamı**oluşturma ' ya gidebilirsiniz. Bir ASEv1 oluşturmak için:

1. ATıCı 'nizin adını sağlayın. ATıCı için belirttiğiniz ad, Ao 'da oluşturulan uygulamalar için kullanılacaktır. Ao 'nun adı appsvcenvdemo ise, alt etki alanı adı şöyle olacaktır: *appsvcenvdemo.p.azurewebsites.net*. Bu nedenle *mytestapp*adlı bir uygulama oluşturduysanız, *mytestapp.appsvcenvdemo.p.azurewebsites.net*adresinde adreslenebilir. ASA 'nın adında boşluk kullanamazsınız. Adında büyük harfli karakterler kullanırsanız, etki alanı adı bu adın toplam küçük harfli sürümü olacaktır. ILB kullanıyorsanız Ao adınız alt etki alanında kullanılmaz, ancak bunun yerine Ao oluşturma sırasında açıkça belirtilir.
   
    ![][1]
2. Aboneliğinizi seçin. ATıCı 'niz için kullandığınız abonelik, bu Ao 'da oluşturduğunuz tüm uygulamalar için de geçerlidir. ASE 'nizi başka bir abonelikte yer alan bir sanal ağa yerleştirebilirsiniz.
3. Yeni bir kaynak grubu seçin veya belirtin. ASE 'niz için kullanılan kaynak grubu, VNet 'iniz için kullanılan aynı olmalıdır. Önceden var olan bir sanal ağı seçerseniz, ASE 'niz için kaynak grubu seçimi sanal ağınızın içeriğini yansıtacak şekilde güncelleştirilir.
   
    ![][2]
4. Sanal ağınızı ve konum seçimlerinizi yapın. Yeni bir VNet oluşturmayı veya önceden var olan VNet 'i seçebilirsiniz. Yeni bir VNet seçerseniz, bir ad ve konum belirtebilirsiniz. Yeni VNet 192.168.250.0/23 adres aralığına ve **varsayılan** olarak 192.168.250.0/24 olarak tanımlanmış bir alt ağa sahip olacaktır. Ayrıca, önceden var olan bir klasik veya Kaynak Yöneticisi sanal ağı seçebilirsiniz. VIP türü seçimi, Ao 'un Internet 'ten (dış) doğrudan erişilebildiğini veya bir Iç Load Balancer (ıLB) kullanıp kullanmadığını belirler. Bunlar hakkında daha fazla bilgi edinmek için [App Service ortamı bir iç Load Balancer kullanarak][ILBASE]okuyun. Dış VIP türünü seçerseniz, IP 'nin ıPSSL amaçları için kaç tane dış IP adresi oluşturduğunu seçebilirsiniz. Dahili ' ı seçerseniz, ATıCı 'nizin kullanacağı alt etki alanını belirtmeniz gerekir. ASEs, ortak adres aralıkları *veya* RFC1918 adres alanları (örn. özel adresler) kullanan sanal ağlara dağıtılabilir. Bir sanal ağı ortak adres aralığıyla birlikte kullanmak için, VNet 'i daha önce oluşturmanız gerekir. Önceden var olan bir VNet seçtiğinizde, ASE oluşturma sırasında yeni bir alt ağ oluşturmanız gerekir. **Portalda önceden oluşturulmuş bir alt ağ kullanamazsınız. Bir Resource Manager şablonu kullanarak ATıCı 'nizi oluşturursanız, önceden var olan bir alt ağ ile Ao oluşturabilirsiniz.** Bir şablondan ATıCı oluşturmak için buradaki bilgileri kullanın, [şablondan bir App Service ortamı oluşturup][ILBAseTemplate] buradan bir [ILB App Service ortamı][ASEfromTemplate]oluşturun.

### <a name="details"></a>Ayrıntılar
Ao, 2 ön uçlar ve 2 çalışan ile oluşturulur. Ön uçlar, HTTP/HTTPS uç noktaları olarak davranır ve uygulamalarınızı barındıran roller olan çalışanlara trafik gönderir. ASE oluşturulduktan sonra miktarı ayarlayabilir ve hatta bu kaynak havuzlarında otomatik ölçeklendirme kuralları ayarlayabilirsiniz. App Service Ortamı el ile ölçeklendirmeyle ilgili daha fazla ayrıntı için, bir yönetimi ve izleme: [App Service Ortamı yapılandırma][ASEConfig] 

Ao tarafından kullanılan alt ağda yalnızca bir AO bulunabilir. Alt ağ, asa dışında bir şey için kullanılamaz

### <a name="after-app-service-environment-v1-creation"></a>App Service Ortamı v1 oluşturulduktan sonra
Ao oluşturulduktan sonra şunları yapabilirsiniz:

* Ön uçların miktarı (minimum: 2)
* Çalışan miktarı (minimum: 2)
* IP SSL için kullanılabilir IP adresi miktarı
* Ön uçlar veya çalışanlar tarafından kullanılan işlem kaynak boyutları (ön uç en küçük boyutu P2 ' dir)

App Service ortamların el ile ölçeklendirilmesi, yönetimi ve izlenmesi konusunda daha fazla ayrıntı vardır: [App Service Ortamı yapılandırma][ASEConfig] 

Otomatik ölçeklendirme hakkında daha fazla bilgi için burada bir kılavuz vardır: [App Service Ortamı için otomatik ölçeklendirmeyi yapılandırma][ASEAutoscale]

Veritabanı ve depolama gibi özelleştirme için kullanılamayan ek bağımlılıklar vardır. Bunlar Azure tarafından işlenir ve sistemle birlikte gelir. Sistem depolama, tüm App Service Ortamı en fazla 500 GB destekler ve veritabanı, sistem ölçeğinde bir şekilde Azure tarafından ayarlanır.

## <a name="getting-started"></a>Başlarken
App Service Ortamı v1 'yi kullanmaya başlamak için bkz [. App Service ortamı v1 'ye giriş][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
