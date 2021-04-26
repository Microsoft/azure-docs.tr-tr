---
title: Redsıs için Azure önbelleği için Redsıs sürümünü ayarla (Önizleme)
description: Redsıs sürümünü yapılandırmayı öğrenin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93349146"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Redsıs için Azure önbelleği için Redsıs sürümünü ayarla (Önizleme)
Bu makalede, önbellek örneğinizle birlikte kullanılacak Redsıs yazılım sürümünü nasıl yapılandıracağınızı öğreneceksiniz. Redsıs için Azure önbelleği, Redsıs 'in en son ana sürümünü ve en az bir önceki sürümü sunmaktadır. Daha yeni Redthe Software piyasaya sürültüğünüz için bu sürümler düzenli olarak güncelleştirilecek. Kullanılabilir iki sürüm arasından seçim yapabilirsiniz. Kullanmakta olduğunuz sürüm artık desteklenmiyorsa önbelleğinizin sonraki sürüme otomatik olarak yükseltildiğini aklınızda bulundurun.

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Bir önbellek oluşturma
Önbellek oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **kaynak oluştur**' u seçin.
  
1. **Yeni** sayfada **veritabanları** ' nı seçin ve ardından **redsıs için Azure önbelleği**' ni seçin.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Redsıs için Azure önbelleği ' ni seçin.":::
   
1. **Temel bilgiler** sayfasında, yeni önbelleğiniz için ayarları yapılandırın.
   
    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonelik** | Aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. | 
    | **Kaynak grubu** | Bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
    | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı, yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık kısa çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* *\<DNS name> . Redis.cache.Windows.net* olacaktır. | 
    | **Konum** | Bir konum seçin. | Önbelleğinizi kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |
    | **Önbellek türü** | [Önbellek katmanını ve boyutunu](https://azure.microsoft.com/pricing/details/cache/)seçin. |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'Ne genel bakış](cache-overview.md). |
   
1. **Gelişmiş** sayfasında, kullanılacak bir redo sürümü seçin.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redsıs sürümü.":::

1. **Oluştur**’a tıklayın. 
   
    Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır.

    > [!NOTE]
    > Şu anda, önbellek oluşturulduktan sonra redin sürümü değiştirilemez.
    >

## <a name="next-steps"></a>Sonraki Adımlar
Redsıs özellikleri için Azure önbelleği hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Redsıs Premium hizmet katmanları için Azure önbelleği](cache-overview.md#service-tiers)
