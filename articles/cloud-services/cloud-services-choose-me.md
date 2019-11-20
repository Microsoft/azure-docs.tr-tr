---
title: Azure Cloud Services nedir? | Microsoft Docs
description: Azure Cloud Services ne olduğunu öğrenin.
services: cloud-services
author: georgewallace
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 61369d51056607d8176d301afa945c7c77895b12
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359711"
---
# <a name="overview-of-azure-cloud-services"></a>Azure Cloud Services 'ye Genel Bakış
Azure Cloud Services bir [hizmet olarak platform](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) örneğidir. [Azure App Service](../app-service/overview.md)gibi, bu teknoloji ölçeklenebilir, güvenilir ve çalışır maliyetli uygulamaları destekleyecek şekilde tasarlanmıştır. App Service sanal makinelerde (VM) barındırıldığından aynı şekilde Azure Cloud Services de aynı şekilde barındırılır. Ancak VM 'Lerde daha fazla denetiminiz vardır. Azure Cloud Services kullanan VM 'Lere kendi yazılımınızı yükleyebilir ve bunlara uzaktan erişebilirsiniz.

![Azure Cloud Services diyagramı](./media/cloud-services-choose-me/diagram.png)

Daha fazla denetim de daha az kullanım kolaylığı anlamına gelir. Ek denetim seçeneklerine ihtiyacınız yoksa, Azure Cloud Services kıyasla App Service Web Apps özelliğinde bir Web uygulamasının çalışır duruma getirmek genellikle daha hızlı ve kolaydır.

İki tür Azure Cloud Services rolü vardır. İkisi arasındaki tek fark, rolünüzün VM 'lerde barındırılmasından farklı olur:

* **Web rolü**: Uygulamanızı IIS üzerinden otomatik olarak dağıtır ve barındırır.

* **Çalışan rolü**: IIS kullanmaz ve uygulamanızı tek başına çalıştırır.

Örneğin, basit bir uygulama yalnızca tek bir Web rolü kullanabilir ve bir Web sitesine hizmet verebilir. Daha karmaşık bir uygulama, kullanıcılardan gelen istekleri işlemek için bir Web rolü kullanabilir ve sonra bu istekleri işlenmek üzere bir çalışan rolüne geçirebilir. (Bu iletişim [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) veya [Azure kuyruk depolaması](../storage/common/storage-introduction.md)kullanabilir.)

Yukarıdaki şekilde önerirken, tek bir uygulamadaki tüm VM 'Ler aynı bulut hizmetinde çalışır. Kullanıcılar, uygulama VM 'lerine otomatik olarak yük dengelemesi yapılan istekler ile tek bir genel IP adresi aracılığıyla uygulamaya erişir. Platform, bir Azure Cloud Services uygulamasındaki VM 'Leri, tek bir donanım arızasını önleyen bir biçimde [ölçeklendirir ve dağıtır](cloud-services-how-to-scale-portal.md) .

Uygulamalar VM 'lerde çalıştırılsa da, Azure Cloud Services 'nin hizmet olarak altyapı (IaaS) değil PaaS sağladığını anlamak önemlidir. İşte bunu düşünmek için bir yol. Azure sanal makineler gibi IaaS ile öncelikle uygulamanızın çalıştırıldığı ortamı oluşturup yapılandırırsınız. Ardından, uygulamanızı bu ortama dağıtırsınız. Her VM 'de işletim sisteminin yeni düzeltme eki uygulanmış sürümlerini dağıtma gibi şeyler yaparak bu dünyanın büyük bir bölümünü yönetmekten siz sorumlusunuz. PaaS 'de, bunun aksine ortam zaten var. Yapmanız gerekir, uygulamanızı dağıtmaktır. İşletim sisteminin yeni sürümlerini dağıtma dahil, üzerinde çalıştığı platformun yönetimi sizin için işlenir.

## <a name="scaling-and-management"></a>Ölçeklendirme ve yönetim
Azure Cloud Services ile sanal makineler oluşturmazsınız. Bunun yerine, "üç web rolü örneği" ve "iki çalışan rolü örneği" gibi her bir kaç tane beğeneceğinin Azure 'a bildiren bir yapılandırma dosyası sağlarsınız. Ardından platform bunları sizin için oluşturur. Hala bu sanal makinelerin [hangi boyutta olduğunu](cloud-services-sizes-specs.md) seçmeniz gerekir, ancak bunları kendi kendinize kendiniz oluşturmazsınız. Uygulamanızın daha büyük bir yükü işlemesi gerekiyorsa, daha fazla VM isteyebilirsiniz ve Azure bu örnekleri oluşturur. Yük azaldıkça, bu örnekleri kapatabilir ve bunların ücretini ödeyerek durdurabilirsiniz.

Azure Cloud Services uygulaması genellikle iki adımlı bir işlem aracılığıyla kullanıcılara sunulur. Bir geliştirici öncelikle [uygulamayı](cloud-services-how-to-create-deploy-portal.md) platformun hazırlama alanına yükler. Geliştirici uygulamayı canlı hale getirmek için kullanıma hazırsa, üretimi üretim ile değiştirmek için Azure portal kullanırlar. [Hazırlama ve üretim arasındaki bu anahtar](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) , çalışan bir uygulamanın, kullanıcılarını bozmadan yeni bir sürüme yükseltilmesini sağlayan kapalı kalma süresi olmadan yapılabilir.

## <a name="monitoring"></a>İzleme
Azure Cloud Services Ayrıca izleme sağlar. Sanal makineler gibi, başarısız bir fiziksel sunucu algılar ve yeni bir makinede o sunucuda çalışan VM 'Leri yeniden başlatır. Ancak Azure Cloud Services, yalnızca donanım arızalarını değil, başarısız VM 'Leri ve uygulamaları da algılar. Sanal makinelerden farklı olarak, her bir Web ve çalışan rolü içinde bir aracı vardır ve bu nedenle, başarısızlık oluştuğunda yeni VM 'Ler ve uygulama örnekleri başlatabilir.

Azure Cloud Services PaaS doğası diğer etkileri de vardır. En önemli bir deyişle, Web veya çalışan rolü örneği başarısız olduğunda bu teknolojide oluşturulan uygulamaların doğru şekilde yazılması gerekir. Bunu başarmak için, bir Azure Cloud Services uygulamasının kendi sanal makinelerinin dosya sisteminde durum koruması olmaması gerekir. Sanal makinelerle oluşturulan VM 'lerden farklı olarak, Azure Cloud Services VM 'lerine yapılan yazmaları kalıcı olmaz. Sanal makine veri diski gibi bir şey yoktur. Bunun yerine, bir Azure Cloud Services uygulamasının tüm durumu Azure SQL veritabanı, blob 'lar, tablolar veya başka bir dış depolamaya açık olarak yazması gerekir. Uygulamaların bu şekilde oluşturulması, hem önemli Azure Cloud Services amaçları olan hatanın ölçeklendirilmesini ve daha dayanıklı olmasını kolaylaştırır.

## <a name="next-steps"></a>Sonraki adımlar
* [.NET 'te bir bulut hizmeti uygulaması oluşturma](cloud-services-dotnet-get-started.md) 
* [Node. js ' de bir bulut hizmeti uygulaması oluşturma](cloud-services-nodejs-develop-deploy-app.md) 
* [PHP 'de bir bulut hizmeti uygulaması oluşturma](../cloud-services-php-create-web-role.md) 
* [Python 'da bir bulut hizmeti uygulaması oluşturma](cloud-services-python-ptvs.md)



