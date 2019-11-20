---
title: Notification Hubs ad alanının fiyatlandırma katmanını Değiştir | Microsoft Docs
description: Azure Notification Hubs ad alanının fiyatlandırma katmanını değiştirmeyi öğrenin.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 679e8b58e5876d5460b426e694df85ded992a36d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212571"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Azure Notification Hub 'ları ad alanının fiyatlandırma katmanını değiştirme
Notification Hubs üç katmanda sunulur: **ücretsiz**, **temel**ve **Standart**. Bu makalede bir Azure Notification Hubs ad alanı için fiyatlandırma katmanının nasıl değiştirileceği gösterilmektedir. 

## <a name="overview"></a>Genel Bakış
Azure Notification Hubs, **hub** en küçük kaynak/varlıktır. Genellikle bir uygulamayla eşlenir ve uygulama için destekliyoruz her bir Platform Bildirim Sistemi bir sertifika tutabilir. Uygulama karma veya yerel ve platformlar arası bir uygulama olabilir.

**Ad alanı** , Bildirim Hub 'larının koleksiyonudur. Her ad alanı genellikle ilgili ve belirli bir amaç için kullanılan hub 'lardan oluşur. Örneğin, sırasıyla geliştirme, test ve üretim amaçları için üç farklı ad alanına sahip olabilirsiniz. 

Fiyatlandırma katmanını ad alanı düzeyinde ilişkilendirebilirsiniz. Notification Hubs üç katmanı destekler: **ücretsiz**, **temel**ve **Standart**. Gereksinimlerinizi karşılayacak bir ad alanı için katmanını kullanabilirsiniz. Aşağıdaki bölümlerde bir Notification Hubs ad alanının fiyatlandırma katmanını nasıl değiştirebileceğiniz gösterilmektedir. 

## <a name="use-azure-portal"></a>Azure portalı kullanma 
Azure portal kullanırken, ad alanı sayfasındaki veya hub sayfasındaki bir ad alanı için fiyatlandırma katmanını değiştirebilirsiniz.  Bir hub sayfasında değişiklik yaparken, aslında onu ad alanı düzeyinde değiştirirsiniz. Ad alanı için fiyatlandırma katmanını ve ad alanındaki tüm Hub 'ları değiştirir. 

### <a name="change-tier-on-the-namespace-page"></a>Ad alanı sayfasında Katmanı Değiştir
Aşağıdaki yordam, ad alanı sayfasındaki bir ad alanı için fiyatlandırma katmanını değiştirme adımları sağlar. Bir ad alanı için katmanı değiştirdiğinizde, ad alanındaki tüm Hub 'lara uygulanır.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Sol taraftaki menüden **tüm hizmetler** ' i seçin. 
3. **Nesnelerin interneti** bölümünde **Notification Hub ad alanları** ' nı seçin. Metnin yanında yıldız (`*`) seçeneğini belirlerseniz, **Sık Kullanılanlar**altındaki sol gezinti çubuğuna eklenir. Bu, sonraki sürümlerde ad alanları sayfasına daha hızlı erişim sağlamanıza yardımcı olur. SıK KULLANıLANLARA ekledikten sonra **Bildirim Hub 'ı ad alanları**' nı seçin. 

    ![Tüm Hizmetler-> Notification Hub ad alanları](./media/change-pricing-tier/all-services-nhub.png)
1. **Bildirim Hub 'ı ad alanları** sayfasında, fiyatlandırma katmanını değiştirmek istediğiniz ad alanını seçin. 
2. Ad alanınız için **Bildirim Hub 'ı ad alanı** sayfasında, **temel** bileşenler bölümünde ad alanı için geçerli fiyatlandırma katmanını görebilirsiniz. Aşağıdaki görüntüde, ad alanının fiyatlandırma katmanının **boş**olduğunu görebilirsiniz. 

    ![Ad alanı sayfasındaki geçerli Fiyatlandırma Katmanı](./media/change-pricing-tier/pricing-tier-before.png)
1. Ad alanınız için **Bildirim Hub 'ı ad alanı** sayfasında **Yönet** bölümünde **fiyatlandırma katmanı** ' nı seçin. 

    ![Ad alanı sayfasında fiyatlandırma katmanını seçin](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Fiyatlandırma katmanınızı değiştirin ve **Seç** düğmesine tıklayın.    
7. **Uyarı**içindeki katman değişikliği eyleminin durumunu görürsünüz. 
8. **Genel bakış** sayfasına geçin. Yeni katmanın **temel** bileşenler bölümündeki **fiyatlandırma katmanı** alanı için gösterildiğini onaylayın.     
1. Bu adım isteğe bağlıdır. Ad alanındaki herhangi bir hub seçin. **Temel** bileşenler bölümünde aynı fiyatlandırma katmanını görtığınızdan emin olun. Ad alanındaki tüm Hub 'lar için aynı fiyatlandırma katmanını görmeniz gerekir. 

### <a name="change-tier-on-the-hub-page"></a>Hub sayfasındaki katmanı değiştirme
Aşağıdaki yordam, hub sayfasındaki bir ad alanı için fiyatlandırma katmanını değiştirme adımları sağlar. Bu adımları Hub sayfasından başlayarak yapmış olsanız da ad alanı için fiyatlandırma katmanını ve ad alanındaki tüm Hub 'ları değiştirirsiniz. 

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Sol taraftaki menüden **tüm hizmetler** ' i seçin.
3. **Nesnelerin interneti** bölümünde **Notification Hubs** ' yi seçin. 
4. Bildirim **hub**'ınızı seçin. 
5. Sol taraftaki menüde **fiyatlandırma katmanını** seçin. 
6. Fiyatlandırma katmanını değiştirin ve **Seç** düğmesine tıklayın. Bu eylem, hub 'ı içeren ad alanı için fiyatlandırma katmanı ayarını değiştirir. Bu nedenle, ad alanı sayfasında ve tüm Hub sayfalarında yeni fiyatlandırma katmanını görürsünüz. 

## <a name="use-rest-api"></a>REST API’yi kullanma
Geçerli fiyatlandırma katmanını almak ve güncelleştirmek için aşağıdaki kaynak sağlayıcısı REST API 'Lerini kullanabilirsiniz. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Bir ad alanı için geçerli fiyatlandırma katmanını al
**Geçerli ad alanı katmanını**almak için aşağıdaki örnekte gösterildiği gıbı bir get komutu gönderin: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Ad alanı için fiyatlandırma katmanını Güncelleştir
**Ad alanı katmanını güncelleştirmek**için aşağıdaki örnekte gösterildiği gıbı bir put komutu gönderin: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Sonraki adımlar
Bu katmanlar ve fiyatlandırma hakkında daha fazla bilgi için bkz. [Notification Hubs fiyatlandırması](https://azure.microsoft.com/pricing/details/notification-hubs/).
