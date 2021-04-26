---
title: Standart Azure CDN HTTPS 'yi zorlamak için bir kural altyapısı kullanma | Microsoft Docs
description: Belirli içerik türlerinin teslimini engellemeyi, önbelleğe alma ilkesi tanımlamayı ve HTTP üstbilgilerini değiştirmeyi de kapsayan, Azure CDN HTTP isteklerini nasıl işleyeceğini özelleştirmek için Microsoft standart Azure Content Delivery Network (Azure CDN) kural altyapısını kullanın. Bu makalede, kullanıcıları HTTPS 'ye yönlendirmek için bir kural oluşturmayı öğrenin.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b94798580b2d4ba746c152486c0de753cf64d2c9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564909"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN için standart kurallar altyapısını ayarlama

Bu makalede, Azure Content Delivery Network (Azure CDN) için standart kurallar altyapısının nasıl ayarlanacağı ve kullanılacağı açıklanır.

## <a name="standard-rules-engine"></a>Standart kurallar altyapısı

HTTP isteklerinin işlenme biçimini özelleştirmek için Azure CDN Standart kurallar altyapısını kullanabilirsiniz. Örneğin, belirli protokolleri kullanmak, önbelleğe alma ilkesi tanımlamak veya bir HTTP üst bilgisini değiştirmek için, Rules altyapısını kullanarak içerik teslimini uygulayabilirsiniz. Bu makalede, kullanıcıları otomatik olarak HTTPS 'ye yönlendiren bir kuralın nasıl oluşturulacağı gösterilmektedir. 

> [!NOTE]
> Bu makalede açıklanan kurallar altyapısı yalnızca Microsoft 'un standart Azure CDN için kullanılabilir. 

## <a name="redirect-users-to-https"></a>Kullanıcıları HTTPS 'ye yönlendir

1. Microsoft profilinizde Azure Content Delivery Network ' ye gidin.

1. **CDN profili** sayfasında, kuralları oluşturmak istediğiniz uç noktayı seçin.
  
1. **Kurallar altyapısı** sekmesini seçin.
   
    **Kurallar altyapısı** bölmesi açılır ve kullanılabilir genel kuralların listesini görüntüler. 
   
    [![Yeni kurallar sayfası Azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Birden çok kuralın listelenme sırası kuralların işlenme sırasını etkiler. Bir kuralda belirtilen eylemlerin sonraki bir kuralla üzerine yazılabilir.
   >

1. **Kural Ekle** ' yi seçin ve bir kural adı girin. Kural adları bir harfle başlamalıdır ve yalnızca rakam ve harf içermelidir.

1. Kuralın uygulandığı isteklerin türünü belirlemek için bir eşleştirme koşulu oluşturun:
    1. **Koşul Ekle**' yi seçin ve ardından **İstek Protokolü** eşleştirme koşulunu seçin.
    1. **İşleç** olarak **Eşittir**’i seçin.
    1. **Değer** için **http**' yi seçin.
   
   [![Azure CDN kuralı eşleştirme koşulu](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > **Koşul Ekle** açılır listesinden birden çok eşleşme koşulu arasından seçim yapabilirsiniz. Eşleşme koşullarının ayrıntılı bir listesi için bkz. [standart kurallar altyapısında koşulları eşleştirme](cdn-standard-rules-engine-match-conditions.md).
   
1. Eşleşme koşulunu karşılayan isteklere uygulanacak eylemi seçin:
   1. **Eylem Ekle**' yi ve ardından **URL yeniden yönlendirme**' yi seçin.
   1. **Tür** için **bulunan (302)** öğesini seçin.
   1. **Protokol** için **https**' yi seçin.
   1. Gelen değerleri kullanmak için diğer tüm alanları boş bırakın.
   
   [![Azure CDN kuralı eylemi](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > **Eylem Ekle** açılır listesinden birden çok eylem arasından seçim yapabilirsiniz. Eylemlerin ayrıntılı bir listesi için bkz. [standart kurallar altyapısındaki eylemler](cdn-standard-rules-engine-actions.md).

6. Yeni kuralı kaydetmek için **Kaydet** ' i seçin. Kural artık kullanılabilir.
   
   > [!IMPORTANT]
   > Kural değişikliklerinin Azure CDN üzerinden yayılması 15 dakikaya kadar sürebilir.
   >
   

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CDN genel bakış](cdn-overview.md)
- [Standart kural altyapısı başvurusu](cdn-standard-rules-engine-reference.md)
- [Standart kurallar altyapısındaki koşulları Eşleştir](cdn-standard-rules-engine-match-conditions.md)
- [Standart kurallar altyapısındaki eylemler](cdn-standard-rules-engine-actions.md)
