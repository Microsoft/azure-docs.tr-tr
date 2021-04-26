---
title: Redsıs için Azure önbelleği için bölge yedekliliği etkinleştirme (Önizleme)
description: Premium ve kurumsal katman Azure önbelleğiniz için, Redsıs örnekleri için bölge yedekliliği ayarlamayı öğrenin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0623f47528d0530838f62c28cf5546e1e66c187b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508274"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Redsıs için Azure önbelleği için bölge yedekliliği etkinleştirme (Önizleme)
Bu makalede, Azure portal kullanarak bölgesel olarak yedekli bir Azure önbellek örneğini yapılandırmayı öğreneceksiniz.

Redsıs standart, Premium ve kurumsal katmanları için Azure önbelleği, her bir önbelleği iki adanmış sanal makinede (VM) barındırarak yerleşik artıklık sağlar. Bu VM 'Ler ayrı [Azure hata ve güncelleştirme etki alanları](../virtual-machines/availability.md) ve yüksek oranda kullanılabilir olmasına rağmen, veri merkezi düzeyindeki hatalara açıktır. Redin için Azure Cache Ayrıca, Premium ve kurumsal katmanlarında bölge yedekliliği destekler. Bölgesel olarak yedekli bir önbellek, birden fazla [kullanılabilirlik](../availability-zones/az-overview.md)alanına yayılan sanal makinelerde çalışır. Daha yüksek esnekliği ve kullanılabilirlik sağlar.

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)

> [!NOTE]
> Bu özellik şu anda önizleme aşamasındadır. ilgilendiğiniz durumlarda [bizimle iletişim kurun](mailto:azurecache@microsoft.com) .
>

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
    | **Önbellek türü** | [Premium veya kurumsal katman](https://azure.microsoft.com/pricing/details/cache/) önbelleği seçin. |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'Ne genel bakış](cache-overview.md). |
   
1. **Gelişmiş** sayfasında, Premium katman önbelleği için **çoğaltma sayısı**' nı seçin.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Çoğaltma sayısı":::

1. **Kullanılabilirlik alanları**' nı seçin. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Kullanılabilirlik alanları":::

1. Diğer seçenekleri varsayılan ayarlarında bırakın. 

    > [!NOTE]
    > Bölge artıklığı desteği, şu anda kümelenmemiş ve coğrafi olmayan çoğaltmasız önbelleklerle çalışır. Ayrıca, özel bağlantıyı, ölçeklendirmeyi, veri kalıcılığını veya içeri/dışarı aktarmayı desteklemez.
    >

1. **Oluştur**’a tıklayın. 
   
    Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır.
   
    > [!NOTE]
    > Kullanılabilirlik alanları, bir önbellek oluşturulduktan sonra değiştirilemez.
    >

## <a name="next-steps"></a>Sonraki Adımlar
Redsıs özellikleri için Azure önbelleği hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Redsıs Premium hizmet katmanları için Azure önbelleği](cache-overview.md#service-tiers)