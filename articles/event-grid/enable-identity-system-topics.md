---
title: Azure Event Grid sistem konusunda yönetilen kimliği etkinleştirme
description: Bu makalede bir Azure Event Grid sistem konusu için yönetilen hizmet kimliğinin nasıl etkinleştirileceği açıklanır.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280522"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Event Grid sistem konusuna sistem tarafından yönetilen bir kimlik atama
Bu makalede, mevcut bir Event Grid sistem konusu için sistem tarafından yönetilen kimliği nasıl etkinleştireceğinizi öğreneceksiniz. Yönetilen kimlikler hakkında bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Şu anda, bir Azure kaynağında sistem konularını destekleyen bir olay aboneliği oluştururken, yeni bir sistem konusu oluştururken sistem tarafından yönetilen bir kimliği etkinleştiremezsiniz. 


## <a name="use-azure-portal"></a>Azure portalı kullanma
Aşağıdaki yordamda bir sistem konusu için sistem tarafından yönetilen kimliğin nasıl etkinleştirileceği gösterilmektedir. 

1. [Azure Portal](https://portal.azure.com) gidin.
2. Üstteki arama çubuğunda **olay Kılavuzu Sistem konularını** arayın.
3. Yönetilen kimliği etkinleştirmek istediğiniz **Sistem konusunu** seçin. 
4. Sol menüdeki **kimlik** ' i seçin. Bu seçeneği, genel konumdaki bir sistem konusu için görmezsiniz. 
5. Kimliği **etkinleştirmek için anahtarı açın.** 
1. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Bir sistem konusu için kimlik sayfası"::: 
1. Onay iletisinde **Evet** ' i seçin. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Bir sistem konusuna kimlik atama-onay"::: 
1. Sistem tarafından atanan yönetilen kimliğin nesne KIMLIĞINI görtığınızdan emin olun ve rol atamak için bir bağlantı görüntüleyin. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Bir sistem konusuna kimlik atama-tamamlandı"::: 

## <a name="global-azure-sources"></a>Küresel Azure kaynakları
Yalnızca bölgesel Azure kaynakları için sistem tarafından yönetilen kimliği etkinleştirebilirsiniz. Azure abonelikleri, kaynak grupları veya Azure haritaları gibi küresel Azure kaynaklarıyla ilişkili sistem konuları için bu ayarı etkinleştiremezsiniz. Bu genel kaynaklarla ilgili sistem konuları da belirli bir bölgeyle ilişkili değildir. Konumu **genel** olarak ayarlanmış olan sistem konusunun **kimlik** sayfasını görmezsiniz. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Konum genel olarak ayarlanan sistem konusu"::: 



## <a name="next-steps"></a>Sonraki adımlar
Kimliği, hedef üzerinde (örneğin, bir Service Bus kuyruğu) uygun bir role (örneğin, Service Bus veri gönderici) ekleyin. Ayrıntılı adımlar için bkz. [yönetilen kimlik Event Grid hedefe erişim verme](add-identity-roles.md). 