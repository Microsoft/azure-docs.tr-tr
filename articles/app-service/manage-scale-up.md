---
title: Özellikleri ve kapasiteleri ölçeklendirme-Azure App Service | Microsoft Docs
description: Kapasite ve özellik eklemek için Azure App Service bir uygulamayı nasıl ölçeklendireceğinizi öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 91a6681c2ef4a6d0ac01889e6e92e752975e90a5
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74089468"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Azure App Service bir uygulamayı büyütme

Bu makalede uygulamanızın Azure App Service nasıl ölçeklenmesi gösterilmektedir. Ölçeklendirme, ölçek artırma ve genişleme için iki iş akışı bulunur ve bu makalede ölçeği artırma iş akışı açıklanmaktadır.

* [Ölçeği artırma](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): daha fazla CPU, bellek, disk alanı ve adanmış sanal makineler (VM), özel etki alanları ve sertifikalar, hazırlama yuvaları, otomatik ölçeklendirme ve daha fazlası gibi ek özellikler alın. Uygulamanızın ait olduğu App Service planının fiyatlandırma katmanını değiştirerek ölçeği değiştirmiş olursunuz.
* [Ölçeği genişletme](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): UYGULAMANıZı çalıştıran VM örneği sayısını artırın.
  Fiyatlandırma katmanınıza bağlı olarak 30 örneğe kadar ölçeği değiştirebilirsiniz. **Yalıtılmış** katmandaki [App Service ortamlar](environment/intro.md) , ölçek genişletme sayınızı 100 örneğe daha da artırır. Ölçeği genişletme hakkında daha fazla bilgi için bkz. [örnek sayısını el ile veya otomatik olarak ölçeklendirme](../monitoring-and-diagnostics/insights-how-to-scale.md). Burada, otomatik ölçeklendirmeyi kullanarak, önceden tanımlanmış kurallara ve zamanlamalara göre örnek sayısını otomatik olarak ölçeklendirmeye yönelik bir bilgi edinebilirsiniz.

Ölçek ayarları, [App Service planınızdaki](../app-service/overview-hosting-plans.md)tüm uygulamaları uygulamak ve etkilemek için yalnızca saniyeler sürer.
Kodunuzun değiştirilmesini veya uygulamanızı yeniden dağıtmanıza gerek kalmaz.

Bireysel App Service planlarının fiyatlandırma ve özellikleri hakkında bilgi için bkz. [App Service fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> **Ücretsiz** katmandan bir App Service planını değiştirmeden önce, Azure aboneliğiniz için yerine [harcama limitlerini](https://azure.microsoft.com/pricing/spending-limits/) kaldırmanız gerekir. Microsoft Azure App Service aboneliğinizin seçeneklerini görüntülemek veya değiştirmek için, bkz. [Microsoft Azure abonelikleri][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Fiyatlandırma katmanınızı büyütme

> [!NOTE]
> **PremiumV2** katmanına kadar ölçeklendirmek için bkz. [App Service Için PremiumV2 katmanını yapılandırma](app-service-configure-premium-tier.md).
>

1. Tarayıcınızda [Azure portalı][portal]’nı açın.

1. App Service uygulama sayfanızda, sol menüden **Ölçek yukarı (App Service planı)** öğesini seçin.
   
3. Katmanınızı seçin ve ardından **Uygula**' yı seçin. Farklı kategorileri (örneğin, **Üretim**) seçin ve ayrıca daha fazla katman göstermek için **ek seçeneklere bakın** .
   
    ![Azure uygulamanızı ölçeklendirmek için gidin.][ChooseWHP]

    İşlem tamamlandığında yeşil başarı onay işaretiyle bir bildirim açılır penceresi görürsünüz.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>İlgili kaynakları ölçeklendirin
Uygulamanız Azure SQL veritabanı veya Azure depolama gibi diğer hizmetlere bağımlıysa, bu kaynakları ayrı olarak ölçeklendirebilirsiniz. Bu kaynaklar App Service planı tarafından yönetilmez.

1. Uygulamanızın **genel bakış** sayfasında **kaynak grubu** bağlantısını seçin.
   
    ![Azure uygulamanızın ilgili kaynaklarını ölçeklendirin](./media/web-sites-scale/RGEssentialsLink.png)

2. **Kaynak grubu** sayfasının **Özet** bölümünde, ölçeklendirmek istediğiniz kaynağı seçin. Aşağıdaki ekran görüntüsünde bir SQL veritabanı kaynağı gösterilmektedir.
   
    ![Azure uygulamanızı ölçeklendirmek için kaynak grubu sayfasına gidin](./media/web-sites-scale/ResourceGroup.png)

    İlgili kaynağı ölçeklendirmek için, belirli kaynak türüne yönelik belgelere bakın. Örneğin, tek bir SQL veritabanının ölçeğini ölçeklendirmek için bkz. [Azure SQL veritabanı 'nda tek veritabanı kaynaklarını ölçeklendirme](../sql-database/sql-database-single-database-scale.md). MySQL için Azure veritabanı kaynağını ölçeklendirmek için bkz. [MySQL kaynaklarını ölçeklendirme](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Fiyatlandırma katmanlarını karşılaştırın

Her fiyatlandırma katmanının VM boyutları gibi ayrıntılı bilgiler için bkz. [App Service fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/app-service).

Hizmet sınırları, Kotalar ve kısıtlamalar tablosu ve her katmandaki desteklenen özellikler için bkz. [App Service sınırları](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Diğer kaynaklar

[Örnek sayısını el ile veya otomatik olarak ölçeklendirme](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[App Service için PremiumV2 katmanını yapılandırma](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
