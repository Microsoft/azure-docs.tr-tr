---
title: Öğretici - Azure CDN önbelleğe alma kurallarını ayarlama | Microsoft Docs
description: Bu öğreticide, bir Azure CDN genel ve bir özel önbelleğe alma kuralı ayarlarsınız.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 27c04533f4a2c0bdfafaae1a3e182e9b01e35437
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058944"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Öğretici: Azure CDN önbelleğe alma kurallarını ayarlama

> [!NOTE] 
> Önbelleğe alma kuralları yalnızca Verizon **Azure CDN ve Akamai** profillerindeki standart **Azure CDN Standart** için kullanılabilir. **Microsoft profillerinin Azure CDN** Için, Verizon profiller **'den Azure CDN Premium** için [standart kurallar altyapısını](cdn-standard-rules-engine-reference.md) kullanmanız gerekir, benzer Işlevler için **Manage** Portal 'daki [Verizon Premium Rules altyapısını](./cdn-verizon-premium-rules-engine.md) kullanmanız gerekir.
 

Bu öğreticide, Azure Content Delivery Network (CDN) önbelleğe alma kurallarını kullanarak varsayılan önbellek süre sonu davranışının hem genel olarak hem de URL yolu ve dosya uzantısı gibi özel koşullarla ayarlanması veya değiştirilmesi açıklanmıştır. Azure CDN iki tür önbelleğe alma kuralı sağlar:
- Genel önbelleğe alma kuralları: Profilinizdeki her uç nokta için bir genel önbelleğe alma kuralı ayarlayabilirsiniz. Bu işlem uç noktaya yönelik tüm istekleri etkiler. Genel önbelleğe alma kuralı ayarlandığında tüm HTTP önbellek yönergesi üst bilgilerini geçersiz kılar.

- Özel önbelleğe alma kuralları: Profilinizdeki her uç nokta için bir veya daha fazla özel önbelleğe alma kuralı ayarlayabilirsiniz. Özel önbelleğe alma kuralları ayarlandığında belirli yollar ve dosya uzantılarıyla eşleşir, sırasıyla işlenir ve genel önbelleğe alma kuralını geçersiz kılar. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Önbelleğe alma kuralları sayfasını açın.
> - Genel önbelleğe alma kuralı oluşturun.
> - Özel önbelleğe alma kuralı oluşturun.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir CDN profili ve en az bir CDN uç noktası oluşturmanız gerekir. Daha fazla bilgi için bkz. [Hızlı Başlangıç: Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Azure CDN önbelleğe alma kuralları sayfasını açın

1. [Azure portalında](https://portal.azure.com) bir CDN profili ve ardından bir uç nokta seçin.

2. Ayarların altındaki sol bölmede **Önbelleğe alma kuralları**’nı seçin.

   ![CDN Önbelleğe alma kuralları düğmesi](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   **Önbelleğe alma kuralları** sayfası görüntülenir.

   ![CDN Önbelleğe alma kuralları sayfası](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Genel önbelleğe alma kurallarını ayarlama

Aşağıda gösterilen şekilde bir genel önbelleğe alma kuralı oluşturun:

1. **Genel önbelleğe alma kuralları**’nın altında **Sorgu dizesi önbelleğe alma davranışı** seçeneğini **Sorgu dizelerini yoksay** olarak ayarlayın.

2. **Önbelleğe alma davranışı** seçeneğini **Eksikse ayarla** olarak ayarlayın.
       
3. **Önbellek sona erme süresi** için, **Günler** alanına 10 yazın.

    Genel önbelleğe alma kuralı, uç noktaya yönelik tüm istekleri etkiler. Bu kural, mevcut kaynak önbellek yönergesi üst bilgilerini onaylar (`Cache-Control` veya `Expires`). Aksi takdirde, belirtilmemişlerse, önbelleği 10 güne ayarlar. 

    ![Genel önbelleğe alma kuralları](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Özel önbelleğe alma kurallarını ayarlama

Aşağıda gösterilen şekilde bir özel önbelleğe alma kuralı oluşturun:

1. **Özel önbelleğe alma kuralları** altında, **Eşleşme koşulu** seçeneğini **Yol** ve **Eşleşme değeri** seçeneğini ise `/images/*.jpg` olarak ayarlayın.
    
2. **Önbelleğe alma davranışı** seçeneğini **Geçersiz kıl** olarak ayarlayın ve **Günler** alanına 30 yazın.
       
    Bu özel önbelleğe alma kuralı, uç noktanızdaki `/images` klasöründe bulunan tüm `.jpg` resim dosyalarında 30 günlük önbellek süresi ayarlar. Kaynak sunucu tarafından gönderilen tüm `Cache-Control` veya `Expires` HTTP üst bilgilerini geçersiz kılar.

    ![Özel önbelleğe alma kuralları](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda önbelleğe alma kuralları oluşturdunuz. Artık bu önbelleğe alma kurallarını kullanmak istemiyorsanız, aşağıdaki adımları gerçekleştirerek kaldırabilirsiniz:
 
1. Bir CDN profili ve ardından kaldırmak istediğiniz önbelleğe alma kurallarını içeren uç noktayı seçin.

2. Ayarların altındaki sol bölmede **Önbelleğe alma kuralları**’nı seçin.

3. **Genel önbelleğe alma kuralları** altında **Önbelleğe alma davranışı** seçeneğini **Ayarlı değil** olarak ayarlayın.
 
4. **Özel önbelleğe alma kuralları** altında, silmek istediğiniz kuralın yanındaki onay kutusunu seçin.

5. **Sil**’i seçin.

6. Sayfanın üst kısmından **Kaydet**’i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> - Önbelleğe alma kuralları sayfasını açın.
> - Genel önbelleğe alma kuralı oluşturun.
> - Özel önbelleğe alma kuralı oluşturun.

Ek önbelleğe alma kuralı ayarlarını yapılandırma konusunda bilgi edinmek için bir sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure CDN önbelleğe alma davranışını önbelleğe alma kurallarıyla denetleme](cdn-caching-rules.md)