---
title: Traffic Manager Azure App Service ile trafiği denetleme
description: Bu makalede, Azure App Service ilişkili olduğu için Azure Traffic Manager için Özet bilgiler sağlanmaktadır.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bb63b25ee9257a402a9887bc8ed8aa83370f3ea0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066418"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Azure Traffic Manager Azure App Service trafiğini denetleme
> [!NOTE]
> Bu makalede, Azure App Service ilişkili olduğu için Microsoft Azure Traffic Manager Özet bilgiler sağlanmaktadır. Bu makalenin sonundaki bağlantılar ziyaret edilerek Azure Traffic Manager hakkında daha fazla bilgi bulabilirsiniz.
> 
> 

## <a name="introduction"></a>Giriş
Web istemcilerinden gelen isteklerin Azure App Service uygulamalara nasıl dağıtıldığını denetlemek için Azure Traffic Manager kullanabilirsiniz. App Service uç noktaları bir Azure Traffic Manager profiline eklendiğinde Azure Traffic Manager, App Service uygulamalarının durumunu (çalışıyor, durduruldu veya silindi) izleyerek trafik gönderilecek uç noktaları belirleyebilir.

## <a name="routing-methods"></a>Yönlendirme yöntemleri
Azure Traffic Manager dört farklı yönlendirme yöntemi kullanır. Bu yöntemler Azure App Service ilgili olarak aşağıdaki listede açıklanmaktadır.

* **[Öncelik](../traffic-manager/traffic-manager-routing-methods.md#priority):** tüm trafik için bir birincil uygulama kullanın ve birincil veya yedekleme uygulamalarının kullanılamadığı durumlarda yedeklemeler sağlayın.
* **[Ağırlıklı](../traffic-manager/traffic-manager-routing-methods.md#weighted):** bir dizi uygulama genelinde eşit veya ağırlığa göre trafiği dağıtın.
* **[Performans](../traffic-manager/traffic-manager-routing-methods.md#performance):** farklı coğrafi konumlarda uygulamalar varsa, en düşük ağ gecikmesi bakımından "en yakın" uygulamayı kullanın.
* **[Coğrafi](../traffic-manager/traffic-manager-routing-methods.md#geographic):** kullanıcıları, DNS sorgusunun kaynaklandığı coğrafi konuma göre belirli uygulamalara yönlendirin. 

Daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service ve Traffic Manager profilleri
App Service uygulama trafiğinin denetimini yapılandırmak için, Azure Traffic Manager 'de daha önce açıklanan dört Yük Dengeleme yönteminden birini kullanan bir profil oluşturun ve ardından, şu ana kadar olan trafiği denetlemek istediğiniz uç noktaları (Bu durumda App Service) ekleyin. profilinizi. Azure Traffic Manager trafiği uygun şekilde yönlendirmek için uygulamanızın durumu (çalışıyor, durduruldu veya silindi) düzenli olarak profile iletilir.

Azure Traffic Manager Azure ile kullanırken aşağıdaki noktaları aklınızda bulundurun:

* Aynı bölgedeki yalnızca uygulama dağıtımları için App Service, uygulama moduna girmeden yük devretme ve hepsini bir kez deneme özelliği zaten sağlamaktadır.
* Farklı bir Azure bulut hizmetiyle birlikte App Service kullanan aynı bölgedeki dağıtımlar için, karma senaryoları etkinleştirmek üzere her iki uç nokta türünü birleştirebilirsiniz.
* Bir profilde bölge başına yalnızca bir App Service uç noktası belirtebilirsiniz. Bir bölge için uç nokta olarak bir uygulama seçtiğinizde, o bölgedeki geri kalan uygulamalar söz konusu profile yönelik seçim için kullanılamaz hale gelir.
* Bir Azure Traffic Manager profilinde belirttiğiniz App Service uç noktaları, profilde uygulamanın Yapılandır sayfasında **etki alanı adları** bölümünün altında görüntülenir, ancak bu, yapılandırılamaz.
* Bir profile bir uygulama ekledikten sonra, Uygulama Portalı sayfasının panosundaki **site URL 'si** , bir tane ayarladıysanız uygulamanın özel etkı alanı URL 'sini görüntüler. Aksi takdirde, Traffic Manager profil URL 'sini (örneğin, `contoso.trafficmanager.net`) görüntüler. Uygulamanın hem doğrudan etki alanı adı hem de Traffic Manager URL 'SI, **etki alanı adları** bölümünün altındaki uygulamanın yapılandırma sayfasında görünür.
* Özel etki alanı adlarınız beklendiği gibi çalışır, ancak uygulamalarınıza eklenmesinin yanı sıra DNS eşlemenizi Traffic Manager URL 'sine işaret etmek üzere de yapılandırmanız gerekir. App Service uygulaması için özel etki alanı ayarlama hakkında daha fazla bilgi için, bkz. [mevcut bir özel DNS adını Azure App Service eşleme](app-service-web-tutorial-custom-domain.md).
* Yalnızca standart veya Premium modda olan uygulamaları bir Azure Traffic Manager profiline ekleyebilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar
Azure Traffic Manager kavramsal ve teknik bir genel bakış için bkz. [Traffic Manager genel bakış](../traffic-manager/traffic-manager-overview.md).

App Service ile Traffic Manager kullanma hakkında daha fazla bilgi için Azure [Web siteleri ile azure Traffic Manager kullanan](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) blog gönderilerine ve [Azure Traffic Manager artık Azure Web siteleri ile tümleştirilebilir](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

