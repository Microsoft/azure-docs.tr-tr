---
title: PremiumV3 katmanını yapılandırma
description: Yeni PremiumV3 fiyatlandırma katmanına ölçeklenerek Azure App Service Web, mobil ve API uygulamanız için daha iyi performans hakkında bilgi edinin.
keywords: app service, azure app service, ölçek, ölçeklenebilir, app service planı, app service maliyeti
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fbd841626e2a074bc0a35cd1b4ac094e267b34a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833355"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Azure App Service için PremiumV3 katmanını yapılandırma

Yeni **PremiumV3** fiyatlandırma katmanı, daha hızlı IŞLEMCILER, SSD depolaması ve mevcut fiyatlandırma katmanlarının (çift **PremiumV2** katmanı) bellek-çekirdek oranını çok daha da sağlar. Performans avantajı sayesinde uygulamalarınızı daha az örnek üzerinde çalıştırarak tasarruf edebilirsiniz. Bu makalede, **PremiumV3** katmanında bir uygulama oluşturmayı veya bir uygulamayı **PremiumV3** katmanına ölçeklendirmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bir uygulamayı **PremiumV3**'e ölçeklendirmek Için, **PremiumV3**'den daha düşük bir fiyatlandırma katmanında çalışan bir Azure App Service uygulamasına sahip olmanız ve uygulamanın PremiumV3 'yi destekleyen bir App Service dağıtımında çalışıyor olması gerekir.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 kullanılabilirliği

**PremiumV3** katmanı hem Windows kapsayıcıları hem de Linux kapsayıcıları dahil olmak üzere hem yerel hem de kapsayıcı uygulamalar için kullanılabilir.

> [!NOTE]
> Önizleme dönemi boyunca **Premium kapsayıcı** katmanında çalışan tüm Windows kapsayıcıları, olduğu gibi çalışmaya devam eder, ancak **Premium kapsayıcı** katmanı önizlemede olmaya devam edecektir. **PremiumV3** katmanı, **Premium kapsayıcı** katmanının resmi yerini alır. 

**PremiumV3** , bazı Azure bölgelerinde kullanılabilir ve ek bölgelerde kullanılabilir. Bölgenizde mevcut olup olmadığını görmek için [Azure Cloud Shell](../cloud-shell/overview.md)şu Azure CLI komutunu çalıştırın:

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>PremiumV3 katmanında uygulama oluşturma

Bir App Service uygulamasının fiyatlandırma katmanı, üzerinde çalıştığı [App Service planında](overview-hosting-plans.md) tanımlanmıştır. App Service bir planı kendisi veya uygulama oluşturma kapsamında oluşturabilirsiniz.

<a href="https://portal.azure.com" target="_blank">Azure portal</a>App Service planını yapılandırırken **fiyatlandırma katmanı**' nı seçin. 

**Üretim**' ı seçin, ardından **P1V3**, **P2V3** veya **P3V3** öğesini seçin ve **Uygula**' ya tıklayın.

![Uygulamanız için önerilen fiyatlandırma katmanlarını gösteren ekran görüntüsü.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> **P1V3**, **P2V3** ve **P3V3** as seçeneklerini görmüyorsanız veya seçenekler gri ise, **PremiumV3** büyük olasılıkla App Service planını içeren temel App Service dağıtımında kullanılabilir değildir. Daha fazla ayrıntı için bkz. [Desteklenmeyen bir kaynak grubu ve bölge kombinasyonuna ölçeği artırma](#unsupported) .

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Mevcut bir uygulamayı PremiumV3 katmanına ölçeklendirme

Mevcut bir uygulamayı **PremiumV3** katmanına ölçeklendirmeden önce **PremiumV3** 'nin kullanılabilir olduğundan emin olun. Bilgi için bkz. [PremiumV3 kullanılabilirliği](#availability). Kullanılabilir değilse, bkz. [Desteklenmeyen bir kaynak grubu ve bölge birleşiminden ölçeği artırma](#unsupported).

Barındırma ortamınıza bağlı olarak, ölçeklendirme için ek adımlar gerekebilir. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, App Service uygulama sayfanızı açın.

App Service uygulaması sayfanızın sol gezinti bölmesinde **Ölçek yukarı (App Service planı)** öğesini seçin.

![App Service planınızın nasıl ölçeklenebilmesini gösteren ekran görüntüsü.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**Üretim**' ı seçin, ardından **P1V3**, **P2V3** veya **P3V3** öğesini seçin ve **Uygula**' ya tıklayın.

![Uygulamanız için önerilen fiyatlandırma katmanlarını gösteren ekran görüntüsü.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

İşlem başarılı bir şekilde tamamlanerdiğinde, uygulamanızın genel bakış sayfası bir **PremiumV3** katmanında olduğunu gösterir.

![Uygulamanızın genel bakış sayfasında PremiumV3 fiyatlandırma katmanını gösteren ekran görüntüsü.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Bir hata alırsanız

Temel App Service dağıtımı PremiumV3 ' i desteklemiyorsa bazı App Service planları PremiumV3 katmanına kadar ölçeklendiremez. Daha fazla ayrıntı için bkz. [Desteklenmeyen bir kaynak grubu ve bölge kombinasyonuna ölçeği artırma](#unsupported) .

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Desteklenmeyen bir kaynak grubu ve bölge birleşiminden ölçeği büyütme

Uygulamanız **PremiumV3** ' nin kullanılamadığı bir App Service dağıtımında çalışıyorsa veya uygulamanız Şu anda **PremiumV3**' yi desteklemeyen bir bölgede çalışıyorsa, **PremiumV3**'in avantajlarından yararlanmak için uygulamanızı yeniden dağıtmanız gerekir.  İki seçeneğiniz vardır:

- Yeni bir kaynak grubunda ve yeni bir App Service planına sahip bir uygulama oluşturun. App Service planını oluştururken bir **PremiumV3** katmanını seçin. Bu adım, App Service planının **PremiumV3** destekleyen bir dağıtım birimine dağıtılmasını sağlar. Ardından, uygulama kodunuzu yeni oluşturulan uygulamaya yeniden dağıtın. Maliyetleri kaydetmek için App Service planını daha düşük bir katmana ölçeklendirseniz bile, dağıtım birimi onu desteklediğinden, her zaman **PremiumV3** 'e geri dönebilirsiniz.
- Uygulamanız zaten mevcut bir **Premium** katmanda çalıştırılıyorsa, uygulamanızı tüm uygulama ayarları, bağlantı dizeleri ve dağıtım yapılandırmasıyla, **PremiumV3** kullanan yeni bir App Service planında yeni bir kaynak grubuna kopyalayabilirsiniz.

    ![Uygulamanızın nasıl klonileceğini gösteren ekran görüntüsü.](media/app-service-configure-premium-tier/clone-app.png)

    **Uygulamayı Kopyala** sayfasında, Istediğiniz bölgede **PremiumV3** kullanarak bir App Service planı oluşturabilir ve kopyalamak istediğiniz uygulama ayarlarını ve yapılandırmayı belirtebilirsiniz.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Premium kapsayıcıdan Premium v3 SKU 'suna taşıma

Preview Premium kapsayıcı SKU 'SU kullanan bir uygulamanız varsa ve yeni Premium v3 SKU 'suna geçmek istiyorsanız, **PremiumV3** avantajlarından yararlanmak için uygulamanızı yeniden dağıtmanız gerekir. Bunu yapmak için, [Desteklenmeyen bir kaynak grubu ve bölge birleşiminin ölçek](#scale-up-from-an-unsupported-resource-group-and-region-combination) bölümündeki ilk seçeneğe bakın

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

[Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/)kullanarak **PremiumV3** katmanında uygulama oluşturmayı otomatik hale getirebilirsiniz.

### <a name="azure-cli"></a>Azure CLI

Aşağıdaki komut _P1V3_ içinde bir App Service planı oluşturur. Bunu Cloud Shell çalıştırabilirsiniz. Seçenekleri `--sku` P1V3, _P2V3_ ve _P3V3_' dir.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki komut _P1V3_ içinde bir App Service planı oluşturur. Seçenekleri `-WorkerSize` _küçük_, _Orta_ ve _büyük_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Diğer kaynaklar

Azure 'da bir [uygulamayı ölçeklendirme](manage-scale-up.md) 
 [Örnek sayısını el ile veya otomatik olarak Ölçeklendir](../azure-monitor/autoscale/autoscale-get-started.md)
