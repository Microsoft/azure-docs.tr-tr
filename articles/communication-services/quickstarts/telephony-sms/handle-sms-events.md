---
title: Hızlı başlangıç-teslim raporları ve gelen Iletiler için SMS olaylarını Işleme
titleSuffix: An Azure Communication Services quickstart
description: Azure Iletişim hizmetlerini kullanarak SMS olaylarını nasıl işleyeceğinizi öğrenin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7a88d8a0065ca7f5cb054d23295e5cfbc23489ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728598"
---
# <a name="quickstart-handle-sms-events-for-delivery-reports-and-inbound-messages"></a>Hızlı başlangıç: teslim raporları ve gelen Iletiler için SMS olaylarını Işleme

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Iletişim Hizmetleri SMS olaylarını işlemek için Azure Event Grid kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

## <a name="about-azure-event-grid"></a>Azure Event Grid hakkında

[Azure Event Grid](../../../event-grid/overview.md) , bulut tabanlı bir olay hizmetidir. Bu makalede, [iletişim hizmeti olaylarına](../../../event-grid/event-schema-communication-services.md)yönelik olaylara abone olmayı ve sonucu görüntülemek için bir olay tetiklemeyi öğreneceksiniz. Normalde olayları, olay verilerini işleyen ve eylemler gerçekleştiren bir uç noktaya gönderirsiniz. Bu makalede, iletileri toplayan ve görüntüleyen bir Web uygulamasına olay göndereceğiz.

## <a name="prerequisites"></a>Önkoşullar
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Bir Azure Iletişim hizmeti kaynağı. Daha ayrıntılı bilgi için [Azure Iletişim kaynağı oluşturma](../create-communication-resource.md) hızlı başlangıç bölümünde bulabilirsiniz.
- SMS etkin telefon numarası. [Telefon numarası alın](./get-phone-number.md).

## <a name="setting-up"></a>Ayarlanıyor

### <a name="enable-event-grid-resource-provider"></a>Event Grid kaynak sağlayıcısını etkinleştirme

Daha önce Azure aboneliğinizde Event Grid kullanmadıysanız, aşağıdaki adımları izleyerek Event Grid Kaynak sağlayıcısını kaydetmeniz gerekebilir:

Azure portalında:

1. Sol taraftaki menüden **abonelikler** ' i seçin.
2. Event Grid için kullandığınız aboneliği seçin.
3. Sol taraftaki menüde, **Ayarlar**' ın altında **kaynak sağlayıcıları**' nı seçin.
4. **Microsoft.EventGrid** öğesini bulun.
5. Kayıtlı değilse **Kaydol**'u seçin.

Kayıt işleminin tamamlanması biraz sürebilir. Durumu güncelleştirmek için **Yenile**’yi seçin. **Durum****Kayıtlı** olduğunda, devam etmeye hazırsınız demektir.

### <a name="event-grid-viewer-deployment"></a>Event Grid Viewer dağıtımı

Bu hızlı başlangıçta, olaylarını neredeyse gerçek zamanlı olarak görüntülemek için [Azure Event Grid Görüntüleyici örneğini](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) kullanacağız. Bu, kullanıcıya gerçek zamanlı bir akış deneyimi sağlar. Ayrıca, her bir olayın yükü de inceleme için kullanılabilir olmalıdır.

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Web kancalarını kullanarak SMS olaylarına abone olma

Portalda, oluşturduğunuz Azure Iletişim Hizmetleri kaynağına gidin. Iletişim hizmeti kaynağı ' nın içinde, **Iletişim Hizmetleri** sayfasının sol menüsünde bulunan **Olaylar** ' ı seçin.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Kaynağın Olaylar sayfasında olay aboneliği düğmesini seçmeyi gösteren ekran görüntüsü.":::

Oluşturma Sihirbazı 'nı girmek için **olay aboneliği Ekle** ' ye basın.

**Olay aboneliği oluştur** sayfasında, olay aboneliği için bir **ad** girin.

İzlemek istediğiniz SMS olaylarını ve olayların nereye gönderileceğini Event Grid söylemek için belirli olaylara abone olabilirsiniz. Açılan menüden abone olmak istediğiniz olayları seçin. SMS için ve seçeneğini belirleyin `SMS Received` `SMS Delivery Report Received` .

Bir **Sistem konu adı** sağlamanız istenirse, benzersiz bir dize sağlamanız yeterli. Bu alan, deneyiminizi etkilemez ve iç telemetri amaçlarıyla kullanılır.

[Azure Iletişim Hizmetleri tarafından desteklenen olayların](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)tam listesini inceleyin.

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Alınan SMS ve SMS teslim raporu tarafından seçili olan olay türlerinin alındığını gösteren ekran görüntüsü.":::

**Uç nokta türü** Için **Web kancası** seçin.

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Web kancası olarak ayarlanan uç nokta türü alanını gösteren ekran görüntüsü.":::

**Uç** nokta için uç **nokta seç**' e tıklayın ve Web uygulamanızın URL 'sini girin.

Bu durumda, hızlı başlangıçta daha önce ayarlandığımız [Azure Event Grid Görüntüleyici örneğindeki](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) URL 'yi kullanacağız. Örneğin URL 'SI şu biçimdedir: `https://{{site-name}}.azurewebsites.net/api/updates`

Sonra **Seçimi Onayla**' yı seçin.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Web kancası uç noktasının onaylandığını gösteren ekran görüntüsü.":::

## <a name="viewing-sms-events"></a>SMS olaylarını görüntüleme

### <a name="triggering-sms-events"></a>SMS olaylarını tetikleme

Olay Tetikleyicilerini görüntülemek için ilk yerde olay üretmemiz gerekir.

- `SMS Received` Iletişim Hizmetleri telefon numarası bir SMS mesajı aldığında olaylar üretilir. Bir olayı tetiklemek için, telefonunuzdan Iletişim Hizmetleri kaynağına bağlı telefon numarasına bir ileti gönderin.
- `SMS Delivery Report Received` Iletişim Hizmetleri telefon numarası kullanarak bir kullanıcıya SMS gönderdiğinizde olaylar oluşturulur. Bir olayı tetiklemek için, `Delivery Report` [gönderilen SMS](../telephony-sms/send.md)seçeneklerinde seçeneğini etkinleştirmeniz gerekir. İle telefonunuza ileti göndermeyi deneyin `Delivery Report` . Bu eylemi tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

[Azure Iletişim Hizmetleri tarafından desteklenen olayların](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)tam listesini inceleyin.

### <a name="receiving-sms-events"></a>SMS olaylarını alma

Yukarıdaki iki eylemi tamamladıktan sonra, `SMS Received` `SMS Delivery Report Received` uç noktanıza ve olayların gönderileceğini fark edersiniz. Bu olaylar, başlangıçta ayarladığımız [Azure Event Grid Viewer örneğinde](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) görünür. Tüm yükü görmek için olayın yanındaki göz simgesine basabilirsiniz. Olaylar şöyle görünür:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="SMS alınan bir olayın Event Grid şemasını gösteren ekran görüntüsü.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="SMS teslim raporu olayının Event Grid şemasını gösteren ekran görüntüsü.":::

[Olay şemaları ve diğer olay kavramları](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)hakkında daha fazla bilgi edinin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta SMS olaylarını nasıl kullanacağınızı öğrendiniz. Event Grid aboneliği oluşturarak SMS iletileri alabilirsiniz.

> [!div class="nextstepaction"]
> [SMS gönder](../telephony-sms/send.md)

Ayrıca şunları yapmak isteyebilirsiniz:


 - [Olay işleme kavramları hakkında bilgi edinin](../../../event-grid/event-schema-communication-services.md)
 - [Event Grid hakkında bilgi edinin](../../../event-grid/overview.md)
