---
title: Hızlı başlangıç-Azure Iletişim hizmetlerini kullanarak Azure Logic Apps SMS mesajları gönderin
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, Azure Iletişim Hizmetleri bağlayıcısını kullanarak Azure Logic Apps iş akışlarında SMS iletileri gönderme hakkında bilgi edinin.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 11cce223a0802c20c5fc144eed681327f989a949
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103488389"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Hızlı başlangıç: Azure Iletişim hizmetleriyle Azure Logic Apps SMS mesajları gönderme

[Azure Iletişim HIZMETLERI SMS](../../overview.md) bağlayıcısını ve [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md)kullanarak SMS iletileri gönderebileceği otomatik iş akışları veya *mantıksal uygulamalar* oluşturabilirsiniz. Bu hızlı başlangıçta, bir mantıksal uygulama iş akışındaki ilk adım olan bir tetikleyici olayına yanıt olarak nasıl otomatik olarak metin iletileri gönderileceğini gösterilmektedir. Tetikleyici olay, gelen bir e-posta iletisi, yineleme zamanlaması, [Azure Event Grid](../../../event-grid/overview.md) kaynak olayı veya [Azure Logic Apps tarafından desteklenen](/connectors/connector-reference/connector-reference-logicapps-connectors)başka bir tetikleyici olabilir.

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Mantıksal uygulama Tasarımcısı ' na açık olan Azure portal gösteren ekran görüntüsü ve Azure Iletişim Hizmetleri Bağlayıcısı için SMS gönder eylemini kullanan örnek bir mantıksal uygulama gösterir.":::

Bu hızlı başlangıç, bir tetikleyiciye yanıt vermek için bağlayıcının kullanılmasına odaklanmasına karşın, bir iş akışındaki tetikleyiciyi izleyen adımlar olan diğer eylemlere yanıt vermek için bağlayıcısını de kullanabilirsiniz. Logic Apps yeni başladıysanız, başlamadan önce [Azure Logic Apps ne olduğunu](../../../logic-apps/logic-apps-overview.md) gözden geçirin.

> [!NOTE]
> Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı veya ücretsiz olarak [bir Azure hesabı oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Etkin bir Azure Iletişim Hizmetleri kaynağıdır veya [bir Iletişim Hizmetleri kaynağı oluşturun](../create-communication-resource.md).

- Etkin bir Logic Apps kaynağı (Logic App) veya [boş bir mantıksal uygulama oluşturun, ancak kullanmak istediğiniz tetikleyiciyi kullanın](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Şu anda Azure Iletişim Hizmetleri SMS Bağlayıcısı yalnızca eylemler sağlar, bu nedenle mantıksal uygulamanız en az bir tetikleyici gerektirir.

  Bu hızlı başlangıçta, [Office 365 Outlook Bağlayıcısı](/connectors/office365/)ile kullanılabilen **Yeni bir e-posta geldiğinde** tetikleyici kullanılır.

- SMS etkin telefon numarası veya [telefon numarası alın](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>SMS eylemi ekleme

Azure Iletişim Hizmetleri SMS bağlayıcısını kullanarak, iş akışınıza **SMS gönder** eylemini yeni bir adım olarak eklemek için, mantıksal uygulama Tasarımcısı 'nda mantıksal uygulama iş akışınız [Azure Portal](https://portal.azure.com) aşağıdaki adımları izleyin:

1. Tasarımcıda, yeni eylemi eklemek istediğiniz adımın altında **yeni adım**' ı seçin. Alternatif olarak, adımlar arasında yeni eylemi eklemek için işaretçinizi Bu adımlar arasındaki oka taşıyın, artı işaretini ( **+** ) seçin ve **Eylem Ekle**' yi seçin.

1. İşlem araması **seçin** kutusunda, girin `Azure Communication Services` . Eylemler listesinden **SMS gönder**' i seçin.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Mantıksal uygulama Tasarımcısı 'nı ve SMS gönder eylemi seçili olan Azure Iletişim Hizmetleri bağlayıcısını gösteren ekran görüntüsü.":::

1. Şimdi Iletişim Hizmetleri kaynağına bir bağlantı oluşturun.

   1. Bağlantı için bir ad girin.

   1. Azure Iletişim Hizmetleri kaynağınızı seçin.

   1. **Oluştur**’u seçin.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Örnek bilgilerle SMS eylem yapılandırması Gönder ' i gösteren ekran görüntüsü.":::

1. **SMS gönder** eyleminde, aşağıdaki bilgileri sağlayın: 

   * Kaynak ve hedef telefon numaraları. Sınama amacıyla, kendi telefon numaranızı hedef telefon numarası olarak kullanabilirsiniz.

   * Göndermek istediğiniz ileti içeriği, örneğin, "Merhaba Logic Apps!".

   Örnek bilgiler içeren bir **SMS gönder** eylemi aşağıda verilmiştir:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Örnek bilgilerle SMS gönder eylemini gösteren ekran görüntüsü.":::

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Ardından, test için mantıksal uygulamanızı çalıştırın.

## <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

Mantıksal uygulamanızı el ile başlatmak için, Tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Ya da mantıksal uygulamanızın tetiklenmesi için bekleyebilirsiniz. Her iki durumda da mantıksal uygulama, belirtilen hedef telefon numarasına bir SMS iletisi göndermelidir. Mantıksal uygulamanızı çalıştırma hakkında daha fazla bilgi için, [mantıksal uygulamanızı nasıl çalıştıracağınızı](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app) inceleyin

## <a name="clean-up-resources"></a>Kaynakları temizleme

Iletişim Hizmetleri aboneliğini kaldırmak için Iletişim Hizmetleri kaynağını veya kaynak grubunu silin. Kaynak grubu silindiğinde bu gruptaki diğer kaynaklar da silinir. Daha fazla bilgi için, [Iletişim Hizmetleri kaynaklarını temizlemeyi](../create-communication-resource.md#clean-up-resources)inceleyin.

Mantıksal uygulama iş akışınızı ve ilgili kaynaklarınızı temizlemek için [Logic Apps kaynaklarının nasıl temizleyeceğini](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources)inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Logic Apps ve Azure Iletişim hizmetlerini kullanarak SMS iletileri gönderme hakkında daha fazla öğrendiniz. Daha fazla bilgi edinmek için SMS olaylarına abone olmaya devam edin:

> [!div class="nextstepaction"]
> [SMS olaylarına abone olma](./handle-sms-events.md)

Azure Iletişim hizmetlerinde SMS hakkında daha fazla bilgi için şu makalelere bakın:

- [SMS kavramları](../../concepts/telephony-sms/concepts.md)
- [Telefon numarası türleri](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
