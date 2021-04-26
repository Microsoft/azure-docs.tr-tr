---
title: 'Hızlı başlangıç: Redsıs kurumsal önbelleği oluşturma'
description: Bu hızlı başlangıçta, kurumsal katmanlarda Redsıs için Azure önbelleğinin bir örneğini oluşturmayı öğrenin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8b450ddff0952777652a957ba2ed554a4bc6497d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584831"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Hızlı başlangıç: Redsıs kurumsal önbelleği oluşturma

Redsıs ' Kurumsal katmanları için Azure önbelleği, Azure 'da tamamen tümleşik ve yönetilen [Redsıs Enterprise](https://redislabs.com/redis-enterprise/) sağlar. Bu yeni katmanlar şunlardır:
* Verileri depolamak için bir sanal makinede geçici bellek (DRAM) kullanan kuruluş
* Verileri depolamak için hem geçici hem de geçici olmayan belleği (NVMe veya SSD) kullanan Enterprise Flash.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce bir Azure aboneliğine sahip olmanız gerekir. Hesabınız yoksa bir [Hesap](https://azure.microsoft.com/)oluşturun. Daha fazla bilgi için bkz. [Kurumsal katmanlara yönelik özel konular](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Bir önbellek oluşturma
1. Önbellek oluşturmak için Azure portal oturum açın ve **kaynak oluştur**' u seçin.

1. **Yeni** sayfada **veritabanları** ' nı seçin ve ardından **redsıs için Azure önbelleği**' ni seçin.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Redsıs için Azure önbelleğini seçin":::
   
1. **Yeni Redis Cache** sayfasında, yeni önbelleğiniz için ayarları yapılandırın.
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı, yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık kısa çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* olacaktır *\<DNS name> . <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Konum** | Açılır ve bir konum seçin. | Kurumsal katmanlar seçili Azure bölgelerinde kullanılabilir. |
   | **Önbellek türü** | Açılır ve bir *Kurumsal* veya *Kurumsal Flash* katmanı ve bir boyut seçin. |  Katman, önbellek için kullanılabilen boyut, performans ve özellikleri belirler. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Kurumsal katman temelleri sekmesi":::

   > [!NOTE] 
   > Devam etmeden önce "Şartlar" altındaki kutuyu kontrol ettiğinizden emin olun.
   >

1. **İleri ' yi seçin: ağ oluşturma** ve atlama.

1. **İleri: Gelişmiş** ' i seçin ve **kümeleme ilkesini** **Kurumsal** olarak ayarlayın. Yalnızca TLS kullanmadan yeni önbelleğe bağlanmayı planlıyorsanız, **TLS olmayan erişimi** etkinleştirin. Ancak bu önerilmez.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Kurumsal katman Gelişmiş sekmesi":::

1. **İleri ' yi seçin: Etiketler** ve atla.

1. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Kurumsal katman Incelemesi + Oluştur sekmesi":::

1. Ayarları gözden geçirin ve **Oluştur**' a tıklayın.
   
   Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, redin için Azure önbelleğinin kurumsal katman örneğini oluşturmayı öğrendiniz.

> [!div class="nextstepaction"]
> [Redsıs için Azure önbelleği kullanan bir ASP.NET Web uygulaması oluşturun.](./cache-web-app-howto.md)

