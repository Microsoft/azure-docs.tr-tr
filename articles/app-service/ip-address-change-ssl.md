---
title: SSL IP adresi değişikliğine hazırlanma
description: SSL IP adresiniz değiştirilebiliyorsa, uygulamanızın değişiklikten sonra çalışmaya devam etmesi için ne yapılacağını öğrenin.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020968"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>SSL IP adresi değişikliğine hazırlanma

Azure App Service uygulamanızın SSL IP adresinin değiştiğini belirten bir bildirim aldıysanız, var olan SSL IP adresini serbest bırakmak ve yeni bir tane atamak için bu makaledeki yönergeleri izleyin.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>SSL IP adreslerini serbest bırakma ve yenilerini atama

1.  [Azure portalını](https://portal.azure.com) açın.

2.  Sol taraftaki gezinti menüsünde **uygulama hizmetleri**' ni seçin.

3.  Listeden App Service uygulamanızı seçin.

4.  **Ayarlar** üst bilgisinde, sol gezinti bölmesinde **SSL ayarları** ' na tıklayın.

1. TLS/SSL bağlamaları bölümünde konak adı kaydını seçin. Açılan düzenleyicide, **SSL türü** açılan menüsünde **SNI SSL** ' yi seçin ve **bağlama Ekle**' ye tıklayın. İşlem başarı iletisini gördüğünüzde, var olan IP adresi yayımlandı.

6.  **SSL bağlamaları** bölümünde, sertifikayla aynı konak adı kaydını seçin. Açılan düzenleyicide, bu kez **SSL türü** açılan menüsünde **IP tabanlı SSL** ' i seçin ve **bağlama Ekle**' ye tıklayın. İşlem başarılı iletisini gördüğünüzde, yeni bir IP adresi aldınız.

7.  Etki alanı kayıt portalınızda (üçüncü taraf DNS sağlayıcısı veya Azure DNS) bir kayıt (doğrudan IP adresinizi işaret eden DNS kaydı) yapılandırılmışsa, var olan IP adresini yeni oluşturulan bir adresle değiştirin. Sonraki bölümdeki yönergeleri izleyerek yeni IP adresini bulabilirsiniz.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Azure portalında yeni SSL IP adresini bulma

1.  Birkaç dakika bekleyin ve ardından [Azure Portal](https://portal.azure.com)açın.

2.  Sol taraftaki gezinti menüsünde **uygulama hizmetleri**' ni seçin.

3.  Listeden App Service uygulamanızı seçin.

4.  **Ayarlar** üst bilgisinde, sol gezinti bölmesinde **Özellikler** ' e tıklayın ve **sanal IP adresi** etiketli bölümü bulun.

5. IP adresini kopyalayın ve etki alanı kaydınızı veya IP mekanizmanızı yeniden yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure tarafından başlatılan bir IP adresi değişikliğine hazırlanma konusu açıklanmaktadır. Azure App Service IP adresleri hakkında daha fazla bilgi için, bkz. [Azure App Service gelen ve gıden IP adresleri](overview-inbound-outbound-ips.md).
