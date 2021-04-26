---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95986557"
---
> [!NOTE]
> Azure App Service için özel bir DNS adı yapılandırmak üzere Azure DNS kullanabilirsiniz. Daha fazla bilgi için bkz. [Bir Azure hizmeti için özel etki alanı ayarları sağlamak üzere Azure DNS'yi kullanma](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

1. Etki alanı sağlayıcınızın web sitesinde oturum açın.

1. DNS kayıtlarını yönetme sayfasını bulun. Her etki alanı sağlayıcısının kendi DNS kayıtları arabirimi vardır, bu nedenle sağlayıcının belgelerine başvurun. Sitede **Domain Name**, **DNS** veya **Name Server Management** etiketli alanları bulun.

   Genellikle, hesap bilgilerinizi görüntüleyerek ve **etki alanlarım** gibi bir bağlantı arayarak DNS kayıtları sayfasını bulabilirsiniz. Bu sayfaya gidin ve **bölge dosyası**, **DNS kayıtları** veya **Gelişmiş yapılandırma** gibi bir bağlantı adlı bağlantıyı arayın.

   DNS kayıtları sayfasının bir örneğini aşağıdaki ekran görüntüsünde görebilirsiniz:

   ![Örnek bir DNS kayıtları sayfasını gösteren ekran görüntüsü.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Örnek ekran görüntüsünde, bir kayıt oluşturmak için **Ekle** ' yi seçin. Bazı sağlayıcıların farklı kayıt türlerini eklemek için farklı bağlantıları vardır. Yeniden belirtelim; sağlayıcının belgelerine başvurun.

> [!NOTE]
> GoDaddy gibi bazı sağlayıcılarda, DNS kayıtlarında yapılan değişiklikler ayrı bir **Değişiklikleri Kaydet** bağlantısı seçilene kadar geçerlilik kazanmaz.
