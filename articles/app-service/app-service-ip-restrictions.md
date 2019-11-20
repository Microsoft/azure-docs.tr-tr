---
title: Erişimi kısıtla-Azure App Service | Microsoft Docs
description: Azure App Service ile erişim kısıtlamalarını kullanma
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cee6fc9fb5cc10a2b3442e146ef5688ed74290bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088446"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service erişim kısıtlamaları #

Erişim kısıtlamaları, uygulamanıza ağ erişimini denetleyen, öncelikli bir siparişli izin verme/reddetme listesi tanımlamanızı sağlar. Listede IP adresleri veya Azure sanal ağ alt ağları bulunabilir. Bir veya daha fazla giriş olduğunda, listenin sonunda bulunan örtülü bir "Tümünü Reddet" vardır.

Erişim kısıtlamaları özelliği, dahil olmak üzere tüm App Service barındırılan iş yükleri ile çalışır; Web Apps, API Apps, Linux uygulamaları, Linux kapsayıcı uygulamaları ve Işlevleri.

Uygulamanıza bir istek yapıldığında, KIMDEN adresi erişim kısıtlamaları listenizdeki IP adresi kurallarına göre değerlendirilir. KIMDEN adresi Microsoft. Web 'e hizmet uç noktaları ile yapılandırılmış bir alt ağdaysa kaynak alt ağ, erişim kısıtlamaları listenizdeki sanal ağ kurallarına göre karşılaştırılır. Listedeki kurallara göre adrese erişime izin verilmiyorsa, hizmet bir [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) durum koduyla yanıt verir.

Erişim kısıtlamaları özelliği, kodunuzun çalıştırıldığı çalışanların ana bilgisayarlarının yukarı akış olan App Service ön uç rollerinde uygulanır. Bu nedenle, erişim kısıtlamaları etkin bir ağ ACL 'lardır.

Bir Azure sanal ağından (VNet) Web uygulamanıza erişimi kısıtlama özelliği, [hizmet uç noktaları][serviceendpoints]olarak adlandırılır. Hizmet uç noktaları, seçilen alt ağlardan çok kiracılı bir hizmete erişimi sınırlamanıza olanak tanır. Hem ağ tarafında hem de etkinleştirildiği hizmette etkin olmalıdır. App Service Ortamı barındırılan uygulamalarla trafiği kısıtlamak için çalışmaz.  Bir App Service Ortamı kullanıyorsanız, uygulamanıza erişimi IP adresi kurallarıyla kontrol edebilirsiniz.

![erişim kısıtlamaları akışı](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Portalda erişim kısıtlama kuralları ekleme ve bunları Düzenle ##

Uygulamanıza bir erişim kısıtlama kuralı eklemek için, menüyü kullanarak **ağ**>**erişim kısıtlamalarını** açın ve **erişim kısıtlamalarını Yapılandır** ' a tıklayın.

![App Service ağ seçenekleri](media/app-service-ip-restrictions/access-restrictions.png)  

Erişim kısıtlamaları kullanıcı arabiriminden, uygulamanız için tanımlanan erişim kısıtlama kuralları listesini gözden geçirebilirsiniz.

![erişim kısıtlamalarını Listele](media/app-service-ip-restrictions/access-restrictions-browse.png)

Listede, uygulamanızdaki tüm geçerli kısıtlamalar gösterilir. Uygulamanızda VNet kısıtlaması varsa, tablo Microsoft. Web için hizmet uç noktalarının etkinleştirilip etkinleştirilmediğini gösterir. Uygulamanızda tanımlı kısıtlama olmadığında, uygulamanız her yerden erişilebilir olacaktır.  

## <a name="adding-ip-address-rules"></a>IP adresi kuralları ekleme

Yeni erişim kısıtlama kuralı eklemek için **[+] ekle** seçeneğine tıklayabilirsiniz. Bir kural eklendikten sonra, hemen geçerli olur. Kurallar, en düşük sayıdan başlayıp yukarı giderek öncelik sırasına göre zorlanır. Tek bir kural eklendikten sonra, etkin olan örtük bir reddetme yok.

Bir kural oluştururken, izin ver/Reddet ' i ve ayrıca kural türünü seçmeniz gerekir. Ayrıca öncelik değerini ve erişimi kısıtladığınız şeyleri sağlamanız gerekir.  İsteğe bağlı olarak kurala bir ad ve açıklama ekleyebilirsiniz.  

![IP erişim kısıtlama kuralı ekleme](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

IP adresi tabanlı bir kural ayarlamak için bir IPv4 veya IPv6 türü seçin. IPv4 ve IPv6 adresleri için CıDR gösteriminde IP adresi gösterimi belirtilmelidir. Tam bir adres belirtmek için, ilk dört sekizlinin IP adresinizi gösterdiği ve/32 maskedir. 1.2.3.4/32 gibi bir şey kullanabilirsiniz. Tüm adresler için IPv4 CıDR gösterimi 0.0.0.0/0 ' dır. CıDR gösterimi hakkında daha fazla bilgi edinmek için, [sınıfsız etki alanları arası yönlendirmeyi](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)okuyabilirsiniz. 

## <a name="service-endpoints"></a>Hizmet uç noktaları

Hizmet uç noktaları, seçili Azure sanal ağ alt ağlarına erişimi kısıtlamanıza olanak sağlar. Belirli bir alt ağa erişimi kısıtlamak için, bir sanal ağ türüyle bir kısıtlama kuralı oluşturun. Erişimine izin vermek veya erişimi reddetmek istediğiniz abonelik, VNet ve alt ağı seçebilirsiniz. Seçtiğiniz alt ağ için hizmet uç noktaları zaten Microsoft. Web ile etkinleştirilmemişse, bunu yapamamasını isteyen kutuyu görmüyorsanız sizin için otomatik olarak etkinleştirilir. Uygulamada etkinleştirmek istediğiniz durum, alt ağda hizmet uç noktalarını etkinleştirme izinleriniz varsa alt ağın büyük ölçüde ilişkili olduğu durumdur. Alt ağda hizmet uç noktalarını etkinleştirmek için başka bir kişiye ihtiyacınız varsa, kutuyu denetleyebilir ve uygulamanızın alt ağda daha sonra etkinleştirildiği olasılığına içinde hizmet uç noktaları için yapılandırılmış olmasını sağlayabilirsiniz. 

![VNet erişim kısıtlama kuralı ekleme](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Hizmet uç noktaları, bir App Service Ortamı çalışan uygulamalara erişimi kısıtlamak için kullanılamaz. Uygulamanız bir App Service Ortamı olduğunda, IP erişim kuralları ile uygulamanıza erişimi denetleyebilirsiniz. 

Hizmet uç noktaları ile uygulamanızı uygulama ağ geçitleri veya diğer WAF cihazları ile yapılandırabilirsiniz. Ayrıca, çok katmanlı uygulamaları güvenli arka uçlarla da yapılandırabilirsiniz. Bazı olasılıklar hakkında daha fazla ayrıntı için, [ağ özelliklerini okuyun ve App Service](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Erişim kısıtlama kurallarını yönetme

Var olan bir erişim kısıtlama kuralını düzenlemek için herhangi bir satıra tıklayabilirsiniz. Düzenlemeler, öncelik Sıralamalı değişiklikler dahil hemen geçerlidir.

![erişim kısıtlama kuralını düzenleme](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Bir kuralı düzenlediğinizde bir IP adresi kuralı ve bir sanal ağ kuralı arasındaki türü değiştiremezsiniz. 

![erişim kısıtlama kuralını düzenleme](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Bir kuralı silmek için, kuraldaki **...** öğesine tıklayın ve ardından **Kaldır**' a tıklayın.

![erişim kısıtlama kuralını Sil](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Tek bir IP adresini engelleme ##

İlk IP kısıtlama kuralınızı eklerken, hizmet 2147483647 önceliğine sahip bir açık **reddetme** kuralı ekler. Uygulamada, **tüm açık reddetme** kuralı, son kural olarak yürütülür ve **izin verme** kuralı kullanılarak açıkça ızın verilmeyen herhangi bir IP adresine erişimi engeller.

Kullanıcıların tek bir IP adresini veya IP adresi bloğunu açıkça engellemek istedikleri, ancak başka her şeye izin veren senaryo için, açıkça bir **Tüm kurala Izin verme** kuralı eklemek gereklidir.

![tek IP adresini engelle](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM sitesi 

Uygulamanıza erişimi denetleyebilmenin yanı sıra, uygulamanız tarafından kullanılan SCM sitesine erişimi de kısıtlayabilirsiniz. SCM sitesi, Web dağıtımı uç noktasıdır ve kudu konsolundan de bulunur. Uygulamadan SCM sitesine ayrı olarak erişim kısıtlamaları atayabilir veya hem uygulama hem de SCM sitesi için aynı kümeyi kullanabilirsiniz. Uygulamanızı uygulamanızla aynı kısıtlamalara sahip olacak şekilde denetlediğinizde, her şey göz aşımına uğrar. Kutuyu boş bırakırsanız, daha önce SCM sitesinde sahip olduğunuz ayarlar uygulanır. 

![erişim kısıtlamalarını Listele](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Erişim kısıtlama kuralları programlama yoluyla düzenleme ##

Şu anda yeni erişim kısıtlamaları özelliği için CLı veya PowerShell yoktur, ancak değerler Kaynak Yöneticisi uygulama yapılandırmasında bir [Azure REST API](https://docs.microsoft.com/rest/api/azure/) put işlemiyle el ile ayarlanabilir. Örnek olarak, gerekli JSON 'u eklemek için resources.azure.com kullanabilir ve ıpsecurityrestrictions bloğunu düzenleyebilirsiniz.

Bu bilgilerin Kaynak Yöneticisi konumu:

management.azure.com/subscriptions/**ABONELIK kimliği**/ResourceGroups/**kaynak grupları**/Providers/Microsoft.Web/Sites/**Web uygulaması adı**/config/Web? api-Version = 2018-02-01

Önceki örnek için JSON sözdizimi şöyledir:

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>İşlev Uygulaması IP kısıtlamaları

App Service planlarla aynı işlevselliğe sahip her iki Işlev uygulaması için de IP kısıtlamaları mevcuttur. IP kısıtlamalarının etkinleştirilmesi, izin verilmeyen IP 'Ler için Portal kod düzenleyicisini devre dışı bırakır.

[Daha fazla bilgi edinin](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
