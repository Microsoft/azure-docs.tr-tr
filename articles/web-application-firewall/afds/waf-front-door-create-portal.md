---
title: 'Öğretici: Azure ön kapısı için Azure portal kullanarak Web uygulaması güvenlik duvarı ilkesi oluşturma'
description: Bu öğreticide, Azure portal kullanarak bir Web uygulaması güvenlik duvarı (WAF) ilkesi oluşturmayı öğreneceksiniz.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/07/2019
ms.author: victorh
ms.openlocfilehash: 602a8620ef35ea247c5865d97495ea43a7d7d93c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501116"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Öğretici: Azure ön kapıda Azure portal kullanarak Web uygulaması güvenlik duvarı ilkesi oluşturma

Bu öğreticide, temel bir Azure Web uygulaması güvenlik duvarı (WAF) ilkesi oluşturma ve Azure ön kapısının bir ön uç konağına uygulama işlemi gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * WAF ilkesi oluşturma
> * Ön uç konakla ilişkilendirin
> * WAF kurallarını yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç: ön kapı profili oluşturma](../../frontdoor/quickstart-create-front-door.md)bölümünde açıklanan yönergeleri Izleyerek bir ön kapı profili oluşturun. 

## <a name="create-a-web-application-firewall-policy"></a>Web uygulaması güvenlik duvarı ilkesi oluşturma

İlk olarak, portalı kullanarak yönetilen varsayılan kural kümesi (DRS) ile temel bir WAF ilkesi oluşturun. 

1. Ekranın sol üst kısmında, **kaynak oluştur**' u seçin > **WAF**araması yapın > **Web uygulaması güvenlik duvarı (Önizleme)** seçin > **Oluştur**' u seçin.
2. **BIR WAF Ilkesi oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik            |Ön kapı abonelik adınızı seçin.|
    | Kaynak grubu          |Ön kapı kaynak grubu adınızı seçin.|
    | İlke adı             |WAF ilkeniz için benzersiz bir ad girin.|

   ![WAF ilkesi oluşturma](../media/waf-front-door-create-portal/basic.png)

3. **BIR WAF Ilkesi oluştur** sayfasının **ilişkilendirme** sekmesinde, **ön uç Konağı Ekle**' yi seçin, aşağıdaki ayarları girin ve ardından **Ekle**' yi seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ön kapı              | Ön kapı profili adınızı seçin.|
    | Ön uç Konağı           | Ön kapı ana bilgisayarın adını ve ardından **Ekle**' yi seçin.|
    
    > [!NOTE]
    > Ön uç Konağı bir WAF ilkesiyle ilişkiliyse, gri renkte gösterilir. Önce ön uç konağını ilişkili ilkeden kaldırmalı ve ardından ön uç konağını yeni bir WAF ilkesiyle yeniden ilişkilendirmeniz gerekir.
1. **Gözden geçir + oluştur**' u seçin ve **Oluştur**' u seçin.

## <a name="configure-web-application-firewall-rules-optional"></a>Web uygulaması güvenlik duvarı kurallarını yapılandırma (isteğe bağlı)

### <a name="change-mode"></a>Modu Değiştir

Bir WAF ilkesi oluşturduğunuzda, varsayılan WAF ilkesi **algılama** modundadır. **Algılama** modunda WAF herhangi bir isteği engellemez, bunun yerine WAF kurallarıyla eşleşen istekler WAF günlüklerinde günlüğe kaydedilir.
WAF 'yi eylemde görmek için, mod ayarlarını **algılamayı** **engelleme**olarak değiştirebilirsiniz. **Önleme** modunda, varsayılan kural KÜMESI (DRS) içinde tanımlanan kurallarla eşleşen istekler engellenir ve WAF günlüklerinde günlüğe kaydedilir.

 ![WAF ilke modunu değiştir](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Özel kurallar

Özel **kurallar** bölümünün altında **özel kural ekle** seçeneğini belirleyerek özel bir kural oluşturabilirsiniz. Bu, özel kural yapılandırma sayfasını başlatır. Sorgu dizesi **blok içeriyorsa,** bir isteği engellemek için özel bir kural yapılandırmanın bir örneği aşağıda verilmiştir.

![WAF ilke modunu değiştir](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Varsayılan kural kümesi (DRS)

Azure tarafından yönetilen varsayılan kural kümesi varsayılan olarak etkindir. Bir kural grubundaki tek bir kuralı devre dışı bırakmak için bu kural grubundaki kuralları genişletin, kural numarasının önündeki **onay kutusunu** seçin ve yukarıdaki sekmede **devre dışı bırak** ' ı seçin. Kural kümesindeki tek tek kuralların eylem türlerini değiştirmek için, kural numarasının önündeki onay kutusunu seçin ve ardından yukarıdaki **eylemi Değiştir** sekmesini seçin.

 ![WAF kural kümesini Değiştir](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> Azure [Web uygulaması güvenlik duvarı hakkında bilgi edinin](../overview.md)
> [Azure ön kapısı hakkında daha fazla bilgi edinin](../../frontdoor/front-door-overview.md)