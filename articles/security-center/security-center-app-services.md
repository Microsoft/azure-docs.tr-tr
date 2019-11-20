---
title: Azure App Service Web uygulamalarınızı ve API 'lerinizi koruyun
description: Bu makale, Azure Güvenlik Merkezi 'nde Azure App Service Web uygulamalarınızı ve API 'lerinizi korumaya başlamanıza yardımcı olur.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: be9331ccd548628bfc27172c4f6e625bdba1632c
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158906"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service Web uygulamalarınızı ve API 'lerinizi koruyun

Azure App Service, altyapıyı yönetmek zorunda kalmadan Web uygulamalarınızı ve API 'lerinizi oluşturmak ve barındırmak için tam olarak yönetilen bir platformdur. Kurumsal düzeyde performans, güvenlik ve uyumluluk gereksinimlerini karşılamak için yönetim, izleme ve operasyonel içgörüler sağlar. Daha fazla bilgi için bkz. [Azure App Service](https://azure.microsoft.com/services/app-service/).

Azure App Service planınız için Gelişmiş tehdit korumasını etkinleştirmek üzere şunları yapmanız gerekir:

* Azure Güvenlik Merkezi 'nin standart fiyatlandırma katmanına abone olma
* App Service planını aşağıda gösterildiği gibi etkinleştirin. Güvenlik Merkezi, App Service ile yerel olarak tümleşiktir ve dağıtım ve ekleme gereksinimini ortadan kaldırır; tümleştirme saydamdır.
* Adanmış makinelerle ilişkili bir App Service planına sahip olmanız gerekir. Desteklenen planlar şunlardır: temel, standart, Premium, yalıtılmış veya Linux. Güvenlik Merkezi, ücretsiz, paylaşılan veya tüketim planlarını desteklemez. Daha fazla bilgi için bkz. [App Service planları](https://azure.microsoft.com/pricing/details/app-service/plans/).

App Service planı etkinken, Güvenlik Merkezi, App Service planınız kapsamındaki kaynakları değerlendirir ve bulguları temelinde güvenlik önerileri oluşturur. Güvenlik Merkezi, App Service çalıştığı VM örneğini ve yönetim arabirimini korur. Ayrıca, App Service ' de çalışan uygulamalarınıza gönderilen istekleri ve yanıtları izler.

Güvenlik Merkezi, bulut ölçeğinden yararlanır ve Azure 'un ortak Web uygulaması saldırılarını izlemek için bir bulut sağlayıcısı olarak sahip olduğu görünürlüğe sahiptir. Güvenlik Merkezi, uygulamalarınızda saldırıları bulabilir ve gelişen saldırıları tanımlayabilir. saldırganlar keşif aşamasında olsa bile, Azure 'da barındırılan birden çok uygulama üzerinde güvenlik açıklarını belirlemek için tarama yapın. Azure-Native hizmeti olarak Güvenlik Merkezi, bu PaaS için temeldeki işlem düğümlerini kapsayan ana bilgisayar tabanlı güvenlik analizlerini sunmaya yönelik benzersiz bir konumda de bulunur ve Güvenlik Merkezi 'nin önceden kullanılmış olan Web uygulamalarına yönelik saldırıları algılamasını sağlar.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>App Service izlemeyi ve korumayı etkinleştirme

1. Azure portal, Güvenlik Merkezi ' ni seçin.
2. **Fiyatlandırma & ayarları** ' na gidin ve bir abonelik seçin.
3. **Fiyatlandırma katmanı**altında, **App Service** satırında planınızı **etkin**olarak değiştirin.

    [uygulama hizmetlerini Standart katman aboneliğinizde etkinleştirmek ![](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> **Kaynak miktarınız** için listelenen örnek sayısı, fiyatlandırma katmanı dikey penceresini açtığınızda bu abonelikte çalışan tüm App Service planlardaki toplam işlem örneği sayısını temsil eder.
>
> Azure App Service çeşitli planlar sunmaktadır. App Service planınız bir Web uygulamasının çalışması için işlem kaynakları kümesini tanımlar. Bunlar geleneksel web barındırma içindeki sunucu grupları ile eşdeğerdir. Bir veya daha fazla uygulama aynı bilgi işlem kaynaklarında (veya aynı App Service planında) çalışacak şekilde yapılandırılabilir.
>
>Sayıyı doğrulamak için, Azure portalında ' App Service planlar ' bölümüne gidin; burada her plan tarafından kullanılan işlem örneklerinin sayısını görebilirsiniz. 






App Service izlemeyi ve önerilerini devre dışı bırakmak için bu işlemi tekrarlayın ve **App Service** planınızı **devre dışı**olarak değiştirin.



## <a name="see-also"></a>Ayrıca bkz.
Bu makalede, Azure Güvenlik Merkezi'nde izleme işlevlerini nasıl kullanacağınız hakkında bilgi edindiniz. Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md): Azure Güvenlik Merkezi'nde güvenlik ayarlarını yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md): Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Uygulama hizmetleri](security-center-virtual-machine-protection.md#app-services): sistem durumu özetleri ile App Service ortamlarınızın listesini görüntüleyin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md): İş ortağı çözümlerinizin sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md): Hizmet kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik Blogu](https://blogs.msdn.com/b/azuresecurity/): Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulabilirsiniz.
