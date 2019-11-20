---
title: Tam zamanında erişim iste-Azure yönetilen uygulamalar
description: Azure yönetilen uygulamalar yayımcılarının, yönetilen bir uygulamaya tam zamanında erişim isteme işlemini açıklar.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: fdd1dcf5ab6b58a91534a465a8c92c10de0d2097
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528961"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure yönetilen uygulamalar için tam zamanında erişimi etkinleştirme ve isteme

Yönetilen uygulamanızın tüketicileri, yönetilen kaynak grubuna kalıcı erişim sağlamak için yeniden bozulabilir. Bir yönetici uygulamasının yayımcısı olarak, müşterilerin yönetilen kaynaklara erişmeniz gerektiğinde tam olarak bilmesini tercih edebilirsiniz. Azure yönetilen uygulamalar, müşterilere yönetilen kaynaklara erişim verme konusunda daha fazla denetim sağlamak için, şu anda önizleme aşamasında olan tam zamanında (JıT) erişim adlı bir özellik sunar.

JıT erişimi, sorun giderme ve bakım için yönetilen bir uygulamanın kaynaklarına yükseltilmiş erişim isteme imkanı sağlar. Kaynaklara her zaman salt okuma erişiminizin olması gerekir, ancak belirli bir süre için daha fazla erişime sahip olabilirsiniz.

Erişim vermek için iş akışı:

1. Market 'e yönetilen bir uygulama eklersiniz ve bu JıT erişiminin kullanılabilir olduğunu belirtebilirsiniz.

1. Dağıtım sırasında, tüketici yönetilen uygulamanın bu örneği için JıT erişimi sunar.

1. Dağıtımdan sonra tüketici, JıT erişimi ayarlarını değiştirebilir.

1. Yönetilen kaynakları gidermeniz veya güncelleştirmeniz gerektiğinde erişim isteği gönderirsiniz.

1. Tüketici isteğinizi onaylar.

Bu makalede, yayımcılara JıT erişimi sağlamak ve istekleri göndermek için gereken eylemlere odaklanılır. JıT erişim isteklerini onaylama hakkında bilgi edinmek için bkz. [Azure yönetilen uygulamalarında tam zamanında erişimi onaylama](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Kullanıcı arabirimine JıT erişim adımı ekleme

Createuıdefinition. JSON dosyanız, kalıcı erişim için oluşturduğunuz Kullanıcı arabirimi dosyası gibidir, ancak tüketicilerin JıT erişimini etkinleştirmesine imkan tanıyan bir adım dahil olmanız gerekir. Azure Marketi 'nde ilk yönetilen uygulama teklifinizi yayımlama hakkında daha fazla bilgi edinmek için bkz. [Market 'Te Azure yönetilen uygulamalar](publish-marketplace-app.md).

Teklifinizin JıT özelliğini desteklemek için Createuıdefinition. JSON dosyanıza aşağıdaki içeriği ekleyin:

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

Teklifinizi Market 'te tanımlarken JıT erişimini etkinleştirdiğinizden emin olun.

1. [Bulut ortağı Yayımlama portalında](https://cloudpartner.azure.com)oturum açın.

1. Market 'te yönetilen uygulamanızı yayımlamak için değerler sağlayın. **JIT erişimini etkinleştirmek** Için **Evet 'i** seçin.

   ![Tam zamanında erişimi etkinleştir](./media/request-just-in-time-access/marketplace-enable.png)

Kullanıcı arabiriminize bir JıT yapılandırma adımı eklediniz ve Market sunumunda JıT erişimini etkinleştirdiniz. Müşteriler yönetilen uygulamanızı dağıtırken, [kendi örnekleri IÇIN JIT erişimini açabilir](approve-just-in-time-access.md#enable-during-deployment).

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
