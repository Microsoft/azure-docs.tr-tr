---
title: include dosyası
description: include dosyası
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: da36cb5c5d2db20b89f80d381f48632c7528c193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002484"
---
1. Önbellek oluşturmak için [Azure Portal](https://portal.azure.com) oturum açın ve **kaynak oluştur**' u seçin.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Bir kaynak oluştur, sol gezinti bölmesinde vurgulanır.":::

   
1. **Yeni** sayfada **veritabanları** ' nı seçin ve ardından **redsıs için Azure önbelleği**' ni seçin.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Yeni, veritabanları vurgulanır ve redin için Azure önbelleği vurgulanır.":::
   
1. **Yeni Redis Cache** sayfasında, yeni önbelleğiniz için ayarları yapılandırın.
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı, yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık kısa çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* *\<DNS name> . Redis.cache.Windows.net* olacaktır. | 
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Konum** | Açılır ve bir konum seçin. | Önbelleğinizi kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |
   | **Fiyatlandırma katmanı** | Açılır ve bir [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/cache/)seçin. |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'Ne genel bakış](../articles/azure-cache-for-redis/cache-overview.md). |

1. **Ağ** sekmesini seçin veya sayfanın altındaki **ağ** düğmesine tıklayın.

1. **Ağ** sekmesinde, bağlantı yönteminizi seçin.

1. **İleri: Gelişmiş** sekmesini seçin veya sayfanın altındaki **İleri: Gelişmiş** düğmesine tıklayın.

1. Bir temel veya standart önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bir bağlantı noktasını etkinleştirmek istiyorsanız geçişi etkinleştir ' i seçin. Ayrıca, hangi redo sürümü kullanmak istediğinizi seçebilirsiniz, 4 veya (ÖNIZLEME) 6.

    :::image type="content" source="media/redis-cache-create/redis-version.png" alt-text="Redsıs sürüm 4 veya 6.":::

1. Premium önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bağlantı noktası, kümeleme ve veri kalıcılığı için ayarları yapılandırın. Ayrıca, hangi redo sürümü kullanmak istediğinizi seçebilirsiniz, 4 veya (ÖNIZLEME) 6. 

1. **Sonraki: Etiketler** sekmesini seçin veya sayfanın altındaki **Sonraki: Etiketler** düğmesine tıklayın.

1. İsteğe bağlı olarak, **Etiketler** sekmesinde, kaynağı sınıflandırmak istiyorsanız ad ve değeri girin. 

1. **Gözden geçir ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, gözden geçir + Oluştur sekmesine götürülürsünüz.

1. Yeşil doğrulama başarılı iletisi göründüğünde **Oluştur**' u seçin.

Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır. 