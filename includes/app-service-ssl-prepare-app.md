---
title: include dosyası
description: include dosyası
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3a44dc35c27cd084e6589482f180b13b258f0b24
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470962"
---
## <a name="prepare-your-web-app"></a>Web uygulamanızı hazırlama

Özel bir SSL sertifikasını (üçüncü taraf sertifika veya App Service sertifikası) web uygulamanıza bağlamak için [App Service planınız](https://azure.microsoft.com/pricing/details/app-service/) **Temel**, **Standart**, **Premium** veya **Yalıtılmış** katmanında olmalıdır. Bu adımda, web uygulamanızın desteklenen bir fiyatlandırma katmanında olduğundan emin olacaksınız.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalı](https://portal.azure.com) açın.

### <a name="navigate-to-your-web-app"></a>Web uygulamanıza gidin

**Uygulama hizmetleri**' ni arayıp seçin.

![Uygulama hizmetlerini seçin](./media/app-service-ssl-prepare-app/app-services.png)

**Uygulama hizmetleri** sayfasında, Azure uygulamanızın adını seçin.

![Azure uygulamasına portal gezintisi](./media/app-service-ssl-prepare-app/select-app.png)

Web uygulamanızın yönetim sayfasına geldiniz.  

### <a name="check-the-pricing-tier"></a>Fiyatlandırma katmanını denetleme

Web uygulaması sayfasının sol gezinti bölmesinde **Ayarlar** bölümüne kaydırın ve **Ölçeği artır (App Service planı)** öğesini seçin.

![Ölçeği artır menüsü](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Web uygulamanızın **F1** veya **D1** katmanında olmadığından emin olun. Web uygulamanızın geçerli katmanı koyu mavi bir kutuyla vurgulanır.

![Fiyatlandırma katmanını denetleyin](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

**F1** veya **D1** katmanında özel SSL desteklenmez. Ölçeği artırmanız gerekirse sonraki bölümde verilen adımları izleyin. Aksi takdirde, **Ölçek artırma** sayfasını kapatın ve [App Service planınızı ölçeği büyütme](#scale-up-your-app-service-plan) bölümünü atlayın.

### <a name="scale-up-your-app-service-plan"></a>App Service planınızın ölçeğini artırma

Ücretsiz olmayan katmanlardan birini seçin (**B1**, **B2**, **B3**, veya **Üretim** kategorisindeki herhangi bir katmanı). Ek seçenekler için **Ek seçeneklere bakın**’a tıklayın.

**Apply (Uygula)** düğmesine tıklayın.

![Fiyatlandırma katmanı seçme](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Aşağıdaki bildirimi gördüğünüzde, ölçeklendirme işlemi tamamlanmıştır.

![Ölçek artırma bildirimi](./media/app-service-ssl-prepare-app/scale-notification.png)

