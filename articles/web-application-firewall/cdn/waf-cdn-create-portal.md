---
title: 'Öğretici: Azure CDN için WAF ilkesi oluşturma-Azure portal'
description: Bu öğreticide, Azure portal kullanarak Azure CDN bir Web uygulaması güvenlik duvarı (WAF) ilkesi oluşturmayı öğreneceksiniz.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 9579d0da3347bdd4ecc627662cee42f909cbfaf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92132780"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Öğretici: Azure CDN Azure portal kullanarak bir WAF ilkesi oluşturma

Bu öğreticide, temel bir Azure Web uygulaması güvenlik duvarı (WAF) ilkesi oluşturma ve bunu Azure Content Delivery Network (CDN) üzerindeki bir uç noktaya uygulama işlemi gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * WAF ilkesi oluşturma
> * Bunu bir CDN uç noktasıyla ilişkilendirin. Bir WAF ilkesini, yalnızca Microsoft SKU 'su **Azure CDN standardında** barındırılan uç noktalarla ilişkilendirebilirsiniz.
> * WAF kurallarını yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç: bir Azure CDN profili ve uç noktası oluşturma](../../cdn/cdn-create-new-endpoint.md)bölümündeki yönergeleri izleyerek bir Azure CDN profili ve uç noktası oluşturun. 

## <a name="create-a-web-application-firewall-policy"></a>Web uygulaması güvenlik duvarı ilkesi oluşturma

İlk olarak, portalı kullanarak yönetilen bir varsayılan kural kümesi (DRS) ile temel bir WAF ilkesi oluşturun.

1. Ekranın sol üst kısmında, **kaynak oluştur** ' u seçin>**WAF** araması yapın>**Web uygulaması güvenlik duvarını** seçin > **Oluştur**' u seçin.
2. **BIR WAF Ilkesi oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | İlke            |Azure CDN (Önizleme) öğesini seçin.|
    | Abonelik            |Ön kapı abonelik adınızı seçin.|
    | Kaynak grubu          |Ön kapı kaynak grubu adınızı seçin.|
    | İlke adı             |WAF ilkeniz için benzersiz bir ad girin.|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="Bir W A F ilkesi oluşturma sayfasının, gözden geçirme + oluştur düğmesi ve çeşitli ayarlar için girilen değerler içeren ekran görüntüsü." border="false":::

3. **BIR WAF Ilkesi oluştur** sayfasının **ILIŞKILENDIRME** sekmesinde, **CDN uç noktası Ekle**' yi seçin, aşağıdaki ayarları girin ve ardından **Ekle**' yi seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | CDN profili              | CDN profili adınızı seçin.|
    | Uç Nokta           | Uç noktanızın adını seçin ve ardından **Ekle**' yi seçin.|
    
    > [!NOTE]
    > Uç nokta bir WAF ilkesiyle ilişkiliyse gri görüntülenir. Önce ilgili ilkeden bitiş noktasını kaldırmalı ve ardından uç noktayı yeni bir WAF ilkesiyle yeniden ilişkilendirmeniz gerekir.
1. **Gözden Geçir + oluştur**’u ve sonra da **Oluştur**’u seçin.

## <a name="configure-web-application-firewall-policy-optional"></a>Web uygulaması güvenlik duvarı ilkesini yapılandırma (isteğe bağlı)

### <a name="change-mode"></a>Modu Değiştir

Varsayılan olarak WAF ilkesi, bir WAF ilkesi oluştururken *algılama* modundadır. *Algılama* modunda WAF hiçbir isteği engellemez. Bunun yerine, WAF kurallarıyla eşleşen istekler WAF günlüklerinde günlüğe kaydedilir.

WAF 'yi eylemde görmek için, mod ayarlarını *algılamayı* *engelleme* olarak değiştirebilirsiniz. *Önleme* modunda, varsayılan kural KÜMESI (DRS) içinde tanımlanan kurallarla eşleşen istekler engellenir ve WAF günlüklerinde günlüğe kaydedilir.

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="Ilke ayarları bölümünün ekran görüntüsü. Mod değiştirme, önleme olarak ayarlanır." border="false":::

### <a name="custom-rules"></a>Özel kurallar

Özel kural oluşturmak için özel **kurallar** bölümünün altında **özel kural ekle** ' yi seçin. Bu, özel kural yapılandırma sayfasını açar. İki tür özel kural vardır: **eşleştirme kuralı** ve **hız sınırı** kuralı.

Aşağıdaki ekran görüntüsünde, sorgu dizesinde **blok değeri varsa** bir isteği engellemek için özel bir eşleştirme kuralı gösterilmektedir.

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="QueryString değişkeninin değeri blok değerini içerip içermediğini denetleyen özel kural yapılandırma sayfasının ekran görüntüsü." border="false":::

Hız sınırı kuralları için iki ek alan gerekir: aşağıdaki örnekte gösterildiği gibi **hız sınırı süresi** ve **hız sınırı eşiği (istekler)** :

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="Hız limiti kural yapılandırma sayfasının ekran görüntüsü. Bir hız limiti süresi liste kutusu ve hız sınırı eşiği (istekler) kutusu görünür." border="false":::

### <a name="default-rule-set-drs"></a>Varsayılan kural kümesi (DRS)

Azure yönetilen varsayılan kural kümesi varsayılan olarak etkindir. Bir kural grubundaki tek bir kuralı devre dışı bırakmak için bu kural grubundaki kuralları genişletin, kural numarasının önündeki onay kutusunu seçin ve yukarıdaki sekmede **devre dışı bırak** ' ı seçin. Kural kümesindeki tek tek kuralların eylem türlerini değiştirmek için, kural numarasının önündeki onay kutusunu seçin ve ardından yukarıdaki **eylemi Değiştir** sekmesini seçin.

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="Bir kural kümesi, kural grupları, kurallar ve Etkinleştir, devre dışı bırak ve eylemi Değiştir düğmelerini gösteren yönetilen kurallar sayfasının ekran görüntüsü. Bir kural denetlenir." border="false":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, kaynak grubunu ve tüm ilgili kaynakları kaldırın.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Web uygulaması güvenlik duvarı hakkında bilgi edinin](../overview.md)
