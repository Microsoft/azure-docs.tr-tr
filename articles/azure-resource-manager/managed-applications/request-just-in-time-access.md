---
title: Tam zamanında erişim isteme
description: Azure yönetilen uygulamalar yayımcılarının, yönetilen bir uygulamaya tam zamanında erişim isteme işlemini açıklar.
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: b7c067ca82ea7fbe7eb9c0f3d50b7fe75dfb47df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377372"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure yönetilen uygulamalar için tam zamanında erişimi etkinleştirme ve isteme

Yönetilen uygulamanızın tüketicileri, yönetilen kaynak grubuna kalıcı erişim sağlamak için yeniden bozulabilir. Yönetilen bir uygulamanın yayımcısı olarak, müşterilerin yönetilen kaynaklara erişmeniz gerektiğinde tam olarak bilmesini tercih edebilirsiniz. Azure yönetilen uygulamalar, müşterilere yönetilen kaynaklara erişim verme konusunda daha fazla denetim sağlamak için tam zamanında (JıT) erişim adlı bir özellik sunar. Bu özellik şu anda önizleme sürümündedir.

JıT erişimi, sorun giderme ve bakım için yönetilen bir uygulamanın kaynaklarına yükseltilmiş erişim isteme imkanı sağlar. Kaynaklara her zaman salt okuma erişiminizin olması gerekir, ancak belirli bir süre için daha fazla erişime sahip olabilirsiniz.

Erişim vermek için iş akışı:

1. Market 'e yönetilen bir uygulama eklersiniz ve bu JıT erişiminin kullanılabilir olduğunu belirtebilirsiniz.

1. Dağıtım sırasında, tüketici yönetilen uygulamanın bu örneği için JıT erişimi sunar.

1. Dağıtımdan sonra tüketici, JıT erişimi ayarlarını değiştirebilir.

1. Yönetilen kaynakları gidermeniz veya güncelleştirmeniz gerektiğinde erişim isteği gönderirsiniz.

1. Tüketici isteğinizi onaylar.

Bu makalede, yayımcılara JıT erişimi sağlamak ve istekleri göndermek için gereken eylemlere odaklanılır. JıT erişim isteklerini onaylama hakkında bilgi edinmek için bkz. [Azure yönetilen uygulamalarında tam zamanında erişimi onaylama](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Kullanıcı arabirimine JıT erişim adımı ekleme

CreateUiDefinition.jsdosya üzerinde, tüketicilerin JıT erişimini etkinleştirmesine imkan tanıyan bir adım ekleyin. Teklifinizin JıT özelliğini desteklemek için, aşağıdaki içeriği dosyaya CreateUiDefinition.jsekleyin.

"Adımlar" içinde:

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

"Çıktılar" içinde:

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JıT erişimi önizlemededir. JıT yapılandırma şeması, gelecekteki yinelemelerde değişebilir.

## <a name="enable-jit-access"></a>JıT erişimini etkinleştir

Iş Ortağı Merkezi 'nde teklifinizi oluştururken JıT erişimini etkinleştirdiğinizden emin olun.

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)'Nde ticari Market portalında oturum açın.

1. Yeni bir yönetilen uygulama oluşturma kılavuzu için [Azure uygulama oluşturma teklifi oluşturma](../../marketplace/create-new-azure-apps-offer.md)bölümündeki adımları izleyin.

1. **Teknik yapılandırma** sayfasında, **tam zamanında (JIT) erişimi etkinleştir** onay kutusunu seçin.

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="Tam zamanında erişimi etkinleştir":::

Kullanıcı arabiriminize bir JıT yapılandırma adımı eklediniz ve ticari Market sunumunda JıT erişimini etkinleştirdiniz. Müşteriler yönetilen uygulamanızı dağıtırken, [kendi örnekleri IÇIN JIT erişimini açabilir](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Erişim izni isteme

Tüketicinin yönetilen kaynaklarına erişmeniz gerektiğinde belirli bir rol, süre ve süre için bir istek gönderirsiniz. Tüketici, isteği onaylamalıdır.

Bir JıT erişim isteği göndermek için:

1. Erişmeniz gereken yönetilen uygulama için **JIT erişimini** seçin.

1. **Uygun roller**' i seçin ve ISTEDIĞINIZ rolün Eylem sütununda **Etkinleştir** ' i seçin.

   ![Erişim isteğini etkinleştir](./media/request-just-in-time-access/send-request.png)

1. **Rolü etkinleştir** formunda, rolünüzün etkin olması için bir başlangıç saati ve süresi seçin. İsteği göndermek için **Etkinleştir** ' i seçin.

   ![Erişimi etkinleştir](./media/request-just-in-time-access/activate-access.png) 

1. Yeni JıT isteğinin tüketiciye başarıyla gönderildiğini görmek için bildirimleri görüntüleyin.

   ![Bildirim](./media/request-just-in-time-access/in-progress.png)

   Şimdi, tüketicinin [isteğinizi onaylamasını](approve-just-in-time-access.md#approve-requests)beklemeniz gerekir.

1. Yönetilen bir uygulama için tüm JıT isteklerinin durumunu görüntülemek için **JIT erişimi** ve **istek geçmişi**' ni seçin.

   ![Durumu görüntüle](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Bilinen sorunlar

JıT erişimi isteyen hesabın asıl KIMLIĞI, yönetilen uygulama tanımına açık bir şekilde eklenmelidir. Hesap yalnızca pakette belirtilen bir gruba eklenemez. Bu sınırlama gelecekteki bir sürümde düzeltilecektir.

## <a name="next-steps"></a>Sonraki adımlar

JıT erişimi için istekleri onaylama hakkında bilgi edinmek için bkz. [Azure yönetilen uygulamalarında tam zamanında erişimi onaylama](approve-just-in-time-access.md).
