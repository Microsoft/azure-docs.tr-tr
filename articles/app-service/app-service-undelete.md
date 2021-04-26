---
title: Silinen uygulamaları geri yükleme
description: Azure App Service silinen bir uygulamayı nasıl geri yükleyeceğinizi öğrenin. Yanlışlıkla silinen bir uygulamanın zahmetli kullanmaktan kaçının.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833175"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell kullanarak silinmiş App Service uygulamasını geri yükleme

Uygulamanızı Azure App Service yanlışlıkla sildikten sonra, [az PowerShell modülündeki](/powershell/azure/)komutları kullanarak geri yükleyebilirsiniz.

> [!NOTE]
> - Silinen uygulamalar, ilk silinmeden 30 gün sonra sistemden temizlenir. Bir uygulama temizlenmeden sonra kurtarılamaz.
> - Geri alma işlevi, tüketim planı için desteklenmez.
> - App Service Ortamı çalıştıran uygulamalar hizmeti uygulamaları, anlık görüntüleri desteklemez. Bu nedenle, bir App Service Ortamı çalıştıran App Service uygulamalar için silmeyi geri alma ve kopyalama işlevselliği desteklenmez.
>

## <a name="re-register-app-service-resource-provider"></a>App Service kaynak sağlayıcısını yeniden Kaydet

Bazı müşteriler, silinen uygulamaların listesini almanın başarısız olduğu bir sorun boyunca gelebilir. Sorunu çözmek için aşağıdaki komutu çalıştırın:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Silinen uygulamaları listeleme

Silinen uygulamalar koleksiyonunu almak için kullanabilirsiniz `Get-AzDeletedWebApp` .

Silinen belirli bir uygulamayla ilgili ayrıntılar için şunları kullanabilirsiniz:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Ayrıntılı bilgiler şunları içerir:

- **Deletedsiteıd**: aynı ada sahip birden çok uygulama silindiği senaryolar için kullanılan, uygulamanın benzersiz tanımlayıcısı
- **SubscriptionID**: silinen kaynağı içeren abonelik
- **Konum**: özgün uygulamanın konumu
- **Resourcegroupname**: özgün kaynak grubunun adı
- **Ad**: özgün uygulamanın adı.
- **Yuva**: yuvanın adı.
- **Silme zamanı**: uygulamanın ne zaman silindiği  

## <a name="restore-deleted-app"></a>Silinen uygulamayı geri yükle

>[!NOTE]
> `Restore-AzDeletedWebApp` işlev uygulamaları için desteklenmez.

Geri yüklemek istediğiniz uygulama tanımlandıktan sonra öğesini kullanarak geri yükleyebilirsiniz `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Dağıtım yuvaları, uygulamanızın bir parçası olarak geri yüklenmez. Hazırlama yuvasını geri yüklemeniz gerekiyorsa `-Slot <slot-name>`  bayrağını kullanın.
>

Komut girişleri şunlardır:

- **Hedef kaynak grubu**: uygulamanın geri yükleneceği hedef kaynak grubu
- **Ad**: uygulamanın adı, genel olarak benzersiz olmalıdır.
- **Targetappserviceplanname**: uygulamaya bağlı App Service plan

Varsayılan olarak `Restore-AzDeletedWebApp` , hem uygulama yapılandırmanızı hem de tüm içerikleri geri yükler. Yalnızca içeriği geri yüklemek istiyorsanız `-RestoreContentOnly` Bu komutu kullanın.

> [!NOTE]
> Uygulama üzerinde barındırılıyorsa ve sonra bir App Service Ortamı silinirse, bu, yalnızca karşılık gelen App Service Ortamı hala mevcutsa geri yüklenebilir.
>

Tam komutunu başvurusunu buradan bulabilirsiniz: [restore-azdeletedwebapp](/powershell/module/az.websites/restore-azdeletedwebapp).
