---
title: Bir bulut klasöründen içerik eşitleme-Azure App Service
description: Bir bulut klasöründen içerik eşitlemesi aracılığıyla Azure App Service uygulamanızı nasıl dağıtacağınızı öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 033a9adfd81dc52858ab58e3170e69af6f56c323
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070623"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Bir bulut klasöründen içerik eşitleme Azure App Service
Bu makalede, içeriğinizi Dropbox ve OneDrive 'dan [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) eşitlenecek. 

İsteğe bağlı içerik eşitleme dağıtımı App Service [kudu dağıtım altyapısı](https://github.com/projectkudu/kudu/wiki)tarafından desteklenir. Uygulama kodunuzla ve içeriklerle, belirtilen bir bulut klasöründe çalışabilir ve ardından bir düğmeye tıklayarak App Service eşitleyebilirsiniz. İçerik eşitleme, kudu derleme sunucusunu kullanır. 

## <a name="enable-content-sync-deployment"></a>İçerik eşitleme dağıtımını etkinleştir

İçerik eşitlemesini etkinleştirmek için [Azure portal](https://portal.azure.com)App Service uygulama sayfanıza gidin.

Sol taraftaki menüden **Deployment Center** > **OneDrive** veya **Dropbox** > **Yetkilendir**' e tıklayın. Yetkilendirme istemlerini izleyin. 

![](media/app-service-deploy-content-sync/choose-source.png)

Yalnızca OneDrive veya Dropbox ile bir kez yetkilendirme yapmanız gerekir. Zaten yetkiniz varsa **devam**' a tıklamanız yeterlidir. **Hesabı Değiştir**' i tıklatarak yetkili OneDrive veya Dropbox hesabını değiştirebilirsiniz.

![](media/app-service-deploy-content-sync/continue.png)

**Yapılandır** sayfasında, eşitlenmesini istediğiniz klasörü seçin. Bu klasör, OneDrive veya Dropbox 'ta aşağıdaki belirtilen içerik yolu altında oluşturulur. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

İşiniz bittiğinde **devam**' a tıklayın.

**Özet** sayfasında, seçeneklerinizi doğrulayıp **son**' a tıklayın.

## <a name="synchronize-content"></a>İçeriği eşitler

Bulut klasörünüzdeki içerikleri App Service eşitlemek istediğinizde, **Dağıtım Merkezi** sayfasına dönün ve **Eşitle**' ye tıklayın.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > API 'lerde temeldeki farklılıklar nedeniyle, **OneDrive iş** Şu anda desteklenmiyor. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>İçerik eşitleme dağıtımını devre dışı bırak

İçerik eşitlemesini devre dışı bırakmak için [Azure portal](https://portal.azure.com)App Service uygulama sayfanıza gidin.

Sol menüde **Dağıtım Merkezi** > **bağlantısı kes**' e tıklayın.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yerel git deposundan dağıtma](deploy-local-git.md)
