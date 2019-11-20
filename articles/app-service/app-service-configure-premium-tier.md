---
title: PremiumV2 katmanını Yapılandırma-Azure App Service | Microsoft Docs
description: Yeni PremiumV2 fiyatlandırma katmanına ölçeklenerek Azure App Service Web, mobil ve API uygulamanız için daha iyi performans hakkında bilgi edinin.
keywords: app service, azure app service, ölçek, ölçeklenebilir, app service planı, app service maliyeti
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aa1e522e395f869f73c0b250623665a9b175384e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067226"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Azure App Service için PremiumV2 katmanını yapılandırma

Yeni **PremiumV2** fiyatlandırma katmanı, daha hızlı IŞLEMCILER, SSD depolaması sağlar ve mevcut fiyatlandırma katmanlarının bellek-çekirdek oranını iki katına çıkarır. Performans avantajı sayesinde uygulamalarınızı daha az örnek üzerinde çalıştırarak tasarruf edebilirsiniz. Bu makalede, **PremiumV2** katmanında bir uygulama oluşturmayı veya bir uygulamayı **PremiumV2** katmanına ölçeklendirmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bir uygulamayı **PremiumV2**'e ölçeklendirmek Için, **PremiumV2**'den daha düşük bir fiyatlandırma katmanında çalışan bir Azure App Service uygulamasına sahip olmanız ve uygulamanın PremiumV2 'yi destekleyen bir App Service dağıtımında çalışıyor olması gerekir.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 kullanılabilirliği

**PremiumV2** katmanı, hem _Windows_ hem de _Linux_'ta App Service kullanılabilir.

**PremiumV2** , çoğu Azure bölgesinde kullanılabilir. Bölgenizde mevcut olup olmadığını görmek için [Azure Cloud Shell](../cloud-shell/overview.md)şu Azure CLI komutunu çalıştırın:

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>PremiumV2 katmanında uygulama oluşturma

Bir App Service uygulamasının fiyatlandırma katmanı, üzerinde çalıştığı [App Service planında](overview-hosting-plans.md) tanımlanmıştır. App Service bir planı kendisi veya uygulama oluşturma kapsamında oluşturabilirsiniz.

<a href="https://portal.azure.com" target="_blank">Azure portal</a>App Service planını yapılandırırken **fiyatlandırma katmanı**' nı seçin. 

**Üretim**' ı seçin, ardından **P1V2**, **P2V2**veya **P3V2**öğesini seçin ve **Uygula**' ya tıklayın.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> **P1V2**, **P2V2**ve **P3V2** as seçeneklerini görmüyorsanız veya seçenekler gri ise, **PremiumV2** büyük olasılıkla App Service planını içeren temel App Service dağıtımında kullanılabilir değildir. Daha fazla ayrıntı için bkz. [Desteklenmeyen bir kaynak grubu ve bölge kombinasyonuna ölçeği artırma](#unsupported) .

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Mevcut bir uygulamayı PremiumV2 katmanına ölçeklendirme

Mevcut bir uygulamayı **PremiumV2** katmanına ölçeklendirmeden önce **PremiumV2** 'nin kullanılabilir olduğundan emin olun. Bilgi için bkz. [PremiumV2 kullanılabilirliği](#availability). Kullanılabilir değilse, bkz. [Desteklenmeyen bir kaynak grubu ve bölge birleşiminden ölçeği artırma](#unsupported).

Barındırma ortamınıza bağlı olarak, ölçeklendirme için ek adımlar gerekebilir. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, App Service uygulama sayfanızı açın.

App Service uygulaması sayfanızın sol gezinti bölmesinde **Ölçek yukarı (App Service planı)** öğesini seçin.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**Üretim**' ı seçin, ardından **P1V2**, **P2V2**veya **P3V2**öğesini seçin ve **Uygula**' ya tıklayın.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

İşlem başarılı bir şekilde tamamlanerdiğinde, uygulamanızın genel bakış sayfası bir **PremiumV2** katmanında olduğunu gösterir.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Bir hata alırsanız

Temel App Service dağıtımı PremiumV2 ' i desteklemiyorsa bazı App Service planları PremiumV2 katmanına kadar ölçeklendiremez.  Daha fazla ayrıntı için bkz. [Desteklenmeyen bir kaynak grubu ve bölge kombinasyonuna ölçeği artırma](#unsupported) .

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Desteklenmeyen bir kaynak grubu ve bölge birleşiminden ölçeği büyütme

Uygulamanız **PremiumV2** ' nin kullanılamadığı bir App Service dağıtımında çalışıyorsa veya uygulamanız Şu anda **PremiumV2**' yi desteklemeyen bir bölgede çalışıyorsa, **PremiumV2**'in avantajlarından yararlanmak için uygulamanızı yeniden dağıtmanız gerekir.  İki seçeneğiniz vardır:

- Yeni bir kaynak grubu oluşturun ve yeni bir uygulama oluşturun ve **Yeni** kaynak grubunda planı App Service, oluşturma işlemi sırasında istediğiniz Azure bölgenizi seçin.  Yeni App Service planının oluşturulduğu sırada **PremiumV2** planını seçmelisiniz.  Bu, kaynak grubu, App Service planı ve Azure bölgesinin birleşimini, **PremiumV2**destekleyen bir App Service dağıtımında oluşturulan App Service planının oluşmasına neden olur.  Ardından uygulama kodunuzu yeni oluşturulan uygulama ve App Service planına yeniden dağıtın. İsterseniz, maliyetleri kaydetmek için App Service planını daha sonra **PremiumV2** ' dan ölçeklendirebilirsiniz ve yine de **PremiumV2**kullanarak yeniden daha sonra tekrar tekrar ölçeklendirirsiniz.
- Uygulamanız zaten mevcut bir **Premium** katmanda çalıştırılıyorsa, uygulamanızı tüm uygulama ayarları, bağlantı dizeleri ve dağıtım yapılandırmasıyla **PremiumV2**kullanan yeni bir App Service planına kopyalayabilirsiniz.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    **Uygulamayı Kopyala** sayfasında, Istediğiniz bölgede **PremiumV2** kullanarak bir App Service planı oluşturabilir ve kopyalamak istediğiniz uygulama ayarlarını ve yapılandırmayı belirtebilirsiniz.

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

[Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/overview)kullanarak **PremiumV2** katmanında uygulama oluşturmayı otomatik hale getirebilirsiniz.

### <a name="azure-cli"></a>Azure CLI

Aşağıdaki komut _P1V2_içinde bir App Service planı oluşturur. Bunu Cloud Shell çalıştırabilirsiniz. Seçenekleri `--sku` P1V2, _P2V2_ve _P3V2_' dir.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki komut _P1V2_içinde bir App Service planı oluşturur. Seçenekleri `-WorkerSize` _küçük_, _Orta_ve _büyük_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Daha fazla kaynak

[Azure 'da bir uygulamayı ölçeklendirme](manage-scale-up.md)  
[Örnek sayısını el ile veya otomatik olarak ölçeklendirme](../monitoring-and-diagnostics/insights-how-to-scale.md)
