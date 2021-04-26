---
title: Azure Stream Analytics işinden Olay Hub 'ına erişmek için Yönetilen kimlikler kullanma (Önizleme)
description: Bu makalede, Azure Event Hubs giriş ve çıkışındaki Azure Stream Analytics işinizin kimliğini doğrulamak için yönetilen kimliklerin nasıl kullanılacağı açıklanır.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018760"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Azure Stream Analytics işinden Olay Hub 'ına erişmek için Yönetilen kimlikler kullanma (Önizleme)

Azure Stream Analytics hem Azure Event Hubs girişi hem de çıktısı için yönetilen kimlik kimlik doğrulamasını destekler. Yönetilen kimlikler, Kullanıcı tabanlı kimlik doğrulama yöntemlerinin sınırlamalarını ortadan kaldırır; bu nedenle, her 90 günde bir oluşan parola değişiklikleri veya kullanıcı belirteci süre sonu gibi yeniden kimlik doğrulaması yapılması gerekir. El ile kimlik doğrulaması gereksinimini kaldırdığınızda Stream Analytics dağıtımlarınız tamamen otomatikleştirilebilir.  

Yönetilen kimlik, belirli bir Stream Analytics işini temsil eden Azure Active Directory kayıtlı yönetilen bir uygulamadır. Yönetilen uygulama, bir güvenlik duvarı veya sanal ağ (VNet) Event Hubs dahil olmak üzere, hedeflenen bir kaynağın kimliğini doğrulamak için kullanılır. Güvenlik duvarlarını atlama hakkında daha fazla bilgi için bkz. [Özel uç noktalar aracılığıyla Azure Event Hubs ad alanlarına erişime Izin verme](../event-hubs/private-link-service.md#trusted-microsoft-services).

Bu makalede Azure portal aracılığıyla bir Stream Analytics işinin Event Hubs girişi veya çıktısı için yönetilen kimliğin nasıl etkinleştirileceği gösterilmektedir.Yönetilen kimliği etkinleştirmeden önce, önce bir Stream Analytics işi ve Olay Hub kaynağınız olması gerekir.

### <a name="limitation"></a>Sınırlama
Önizleme sırasında, yönetilen kimlik doğrulama modu kullanılırken Azure portal Event Hubs örnekleme girişi çalışmaz.

## <a name="create-a-managedidentity"></a>Yönetilen kimlik oluşturma  

İlk olarak, Azure Stream Analytics işiniz için yönetilen bir kimlik oluşturursunuz.  

1. Azure portal, Azure Stream Analytics işinizi açın.  

1. Sol gezinti menüsünde, Yapılandır altında bulunan **yönetilen kimlik**' i seçin   . ** Sonra, **sistem tarafından atanan yönetilen kimliği kullan**' ın yanındaki kutuyu Işaretleyin   ve **Kaydet**' i seçin.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Sistem tarafından atanan yönetilen kimlik":::  

1. Stream Analytics işinin kimliği için bir hizmet sorumlusu Azure Active Directory oluşturulur. Yeni oluşturulan kimliğin yaşam döngüsü Azure tarafından yönetilir. Stream Analytics işi silindiğinde, ilişkili kimlik (hizmet sorumlusu) Azure tarafından otomatik olarak silinir.  

   Yapılandırmayı kaydettiğinizde, hizmet sorumlusunun nesne KIMLIĞI (OID) aşağıda gösterildiği gibi asıl KIMLIK olarak listelenir:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Asıl KIMLIK":::

   Hizmet sorumlusu Stream Analytics işle aynı ada sahiptir. Örneğin, işinizin adı ise  `MyASAJob` , hizmet sorumlusunun adı da olur  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Olay Hub 'ına erişim için Stream Analytics işi izinleri verme

Stream Analytics işinin, yönetilen kimlik kullanarak Olay Hub 'ınıza erişmesi için, oluşturduğunuz hizmet sorumlusunun Olay Hub 'ında özel izinleri olmalıdır.

1. Olay Hub 'ınızdaki **Access Control (IAM)** bölümüne gidin.

1. **+ Ekle** ve **rol ataması Ekle**' yi seçin.

1. **Rol ataması Ekle** sayfasında, aşağıdaki seçenekleri girin:

   |Parametre|Değer|
   |---------|-----|
   |Rol|Azure Event Hubs veri sahibi|
   |Şunlara erişim ata:|Kullanıcı, Grup veya hizmet sorumlusu|
   |Şunu seçin:|Stream Analytics işinizin adını girin|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Rol ataması ekle":::

1. **Kaydet** ' i seçin ve ardından bir dakika bekleyin ve değişiklikler yayılır.

Ayrıca, bu rolü olay hub 'ı ad alanı düzeyinde de verebilirsiniz. Bu, izinleri bunun altında oluşturulan tüm Event Hubs doğal olarak yayacaktır. Diğer bir deyişle, bir ad alanı altındaki tüm Event Hubs Stream Analytics işiniz için yönetilen kimlik doğrulama kaynağı olarak kullanılabilir.

## <a name="create-anevent-hub-input-or-output"></a>Olay Hub 'ı girişi veya çıkışı oluşturma  

Yönetilen kimliğiniz yapılandırıldıktan sonra, Olay Hub 'ı kaynağını Stream Analytics işinize giriş veya çıkış olarak eklemeye hazırsınız demektir.  

### <a name="add-the-event-hub-as-an-input"></a>Olay Hub 'ını giriş olarak ekleme 

1. Stream Analytics işinize gidin ve **Iş topolojisi** altındaki **girişler** sayfasına gidin.

1. **Olay Hub 'ı > akış girişi Ekle**' yi seçin. Giriş özellikleri penceresinde, Olay Hub 'ınızı arayıp seçin ve *kimlik doğrulama modu* açılan menüsünden **yönetilen kimlik** ' i seçin.

1. Özellikleri geri kalanı doldurun ve **Kaydet**' i seçin.

### <a name="add-the-event-hub-as-an-output"></a>Olay Hub 'ını çıkış olarak ekleme

1. Stream Analytics işinize gidin ve **Iş topolojisi** altındaki **çıktılar** sayfasına gidin.

1. **> Olay Hub 'ı Ekle**' yi seçin. Çıkış özellikleri penceresinde, Olay Hub 'ınızı arayıp seçin ve *kimlik doğrulama modu* açılan menüsünden **yönetilen kimlik** ' i seçin.

1. Özellikleri geri kalanı doldurun ve **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics çıkışı Event Hubs](event-hubs-output.md)
* [Event Hubs’dan veri akışı sağlama](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
