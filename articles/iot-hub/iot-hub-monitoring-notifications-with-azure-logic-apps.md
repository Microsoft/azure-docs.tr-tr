---
title: IoT uzaktan izleme ve bildirimler Azure Logic Apps | Microsoft Docs
description: IoT Hub 'ınızda IoT sıcaklık izleme için Azure Logic Apps kullanın ve algılanan tüm bozukluklar için posta kutunuza otomatik olarak e-posta bildirimleri gönderin.
author: robinsh
keywords: IoT izleme, IoT bildirimleri, IoT sıcaklık izleme
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564950"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT Hub 'ınızı ve posta kutunuzu bağlama Azure Logic Apps IoT uzaktan izleme ve bildirimler

![Uçtan uca diyagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) , şirket içi ve bulut hizmetlerinde, bir veya daha fazla kuruluşta ve çeşitli protokollerde iş akışlarını düzenlemenize yardımcı olabilir. Mantıksal uygulama tetikleyici ile başlar ve ardından koşullar ve yineleyiciler gibi yerleşik denetimler kullanılarak sıralanbilen bir veya daha fazla eylem gelir. Bu esneklik, IoT izleme senaryoları için ideal bir IoT çözümü Logic Apps sağlar. Örneğin, bir IoT Hub uç noktasındaki bir cihazdan telemetri verilerinin varışı, bir Azure Storage blob 'undaki verileri ambara yönelik mantıksal uygulama iş akışlarını başlatabilir, veri anormallarını uyarmak için e-posta uyarıları gönderebilir, bir cihaz hata bildirirse bir teknisyen ziyaret edebilir ve bu şekilde devam eder.

Bu makalede, IoT Hub 'ınızı ve posta kutunuzun sıcaklık izleme ve bildirimler için bağlanacağı bir mantıksal uygulama oluşturmayı öğreneceksiniz. Cihazınızda çalışan istemci kodu, `temperatureAlert` IoT Hub 'ınıza gönderdiği her telemetri iletisindeki bir uygulama özelliği ayarlar. İstemci kodu 30 C üzerinde bir sıcaklık algıladığında, bu özelliği olarak ayarlar `true` ; Aksi takdirde, özelliğini olarak ayarlar `false` .

IoT Hub 'ınıza ulaşan iletiler, gövdede bulunan telemetri verileri ve uygulama özelliklerinde bulunan özelliği ile şuna benzer şekilde görünür `temperatureAlert` (sistem özellikleri gösterilmez):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

IoT Hub ileti biçimi hakkında daha fazla bilgi edinmek için bkz. [IoT Hub Iletileri oluşturma ve okuma](iot-hub-devguide-messages-construct.md).

Bu konu başlığında, `temperatureAlert` özelliğin Service Bus uç noktasına olduğu iletileri göndermek Için IoT Hub 'ınızda yönlendirme ayarlarsınız `true` . Ardından Service Bus uç noktasına ulaşan iletilerde tetiklenen bir mantıksal uygulama ayarlarsınız ve size bir e-posta bildirimi gönderir.

## <a name="prerequisites"></a>Önkoşullar

* [Raspberry PI Çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md) öğreticisini veya cihaz öğreticilerinin birini doldurun. Örneğin, [node.jsIle Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) 'ye gidebilir veya [Telemetriyi gönder](quickstart-send-telemetry-dotnet.md) hızlı başlangıçlarından birine gidebilirsiniz. Bu makaleler aşağıdaki gereksinimleri kapsar:

  * Etkin bir Azure aboneliği.
  * Aboneliğiniz kapsamındaki bir Azure IoT Hub 'ı.
  * Azure IoT Hub 'ınıza telemetri iletileri gönderen cihazınızda çalışan bir istemci uygulaması.

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus ad alanı ve kuyruk oluşturma

Service Bus ad alanı ve kuyruğu oluşturun. Bu konunun ilerleyen kısımlarında, bir mantık uygulaması tarafından çekilecek ve bildirim e-postası göndermek için bir sıcaklık uyarısı içeren iletileri Service Bus kuyruğuna yönlendirmek üzere IoT Hub 'ınızda bir yönlendirme kuralı oluşturursunuz.

### <a name="create-a-service-bus-namespace"></a>Service Bus ad alanı oluşturma

1. [Azure Portal](https://portal.azure.com/) **+ kaynak**  >  **tümleştirme**  >  **Service Bus** oluştur ' u seçin.

1. **Ad alanı oluştur** bölmesinde aşağıdaki bilgileri sağlayın:

   **Ad**: Service Bus ad alanının adı. Ad alanı, Azure genelinde benzersiz olmalıdır.

   **Fiyatlandırma katmanı**: açılan listeden **temel** ' yı seçin. Temel katman Bu öğretici için yeterlidir.

   **Kaynak grubu**: IoT Hub 'ınızın kullandığı kaynak grubunu kullanın.

   **Konum**: IoT Hub 'ınızın kullandığı konumu kullanın.

   ![Azure portal bir Service Bus ad alanı oluşturma](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. **Oluştur**’u seçin. Sonraki adıma geçmeden önce dağıtımın tamamlanmasını bekleyin.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Ad alanına Service Bus kuyruğu ekleme

1. Service Bus ad alanını açın. Service Bus ad alanına almanın en kolay yolu kaynak grubundan kaynak **grupları** seçmek, kaynak grubunuzu seçmek ve ardından kaynak listesinden Service Bus ad alanını seçmesidir.

1. **Service Bus ad alanı** bölmesinde **+ kuyruk**' u seçin.

1. Sıra için bir ad girin ve ardından **Oluştur**' u seçin. Sıra başarıyla oluşturulduğunda, **kuyruk oluştur** bölmesi kapanır.

   ![Azure portal bir Service Bus kuyruğu ekleme](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. **Service Bus ad alanı** bölmesine geri dönün, **varlıklar** altında **Kuyruklar**' ı seçin. Listeden Service Bus kuyruğunu açın ve sonra **paylaşılan erişim ilkeleri**  >  **+ Ekle**' yi seçin.

1. İlke için bir ad girin, **Yönet**' i işaretleyin ve ardından **Oluştur**' u seçin.

   ![Azure portal bir Service Bus kuyruğu ilkesi ekleme](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>IoT Hub 'ınıza özel bir uç nokta ve yönlendirme kuralı ekleme

IoT Hub 'ınıza Service Bus kuyruğu için özel bir uç nokta ekleyin ve bir sıcaklık uyarısı içeren iletileri, mantıksal uygulamanız tarafından alınacakları uç noktaya yönlendirmek için bir ileti yönlendirme kuralı oluşturun. Yönlendirme kuralı, `temperatureAlert = "true"` `temperatureAlert` cihaz üzerinde çalışan istemci kodu tarafından ayarlanan uygulama özelliğinin değerine bağlı olarak iletileri iletmek için bir yönlendirme sorgusu kullanır. Daha fazla bilgi için bkz. [ileti özelliklerine göre ileti yönlendirme sorgusu](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Özel uç nokta ekleme

1. IoT Hub 'ınızı açın. IoT Hub 'a almanın en kolay yolu kaynak **grupları** kaynak bölmesinden seçmeniz, kaynak grubunuzu seçmeniz ve ardından kaynak listesinden IoT Hub 'ınızı seçmeniz gerekir.

1. **Mesajlaşma** altında **ileti yönlendirme**' yi seçin. **İleti yönlendirme** bölmesinde **Özel uç noktalar** sekmesini seçin ve **+ Ekle**' yi seçin. Aşağı açılan listeden **Service Bus kuyruğu**' nu seçin.

   ![Service Bus kuyruğu seçeneğini vurgulayan ekran görüntüsü.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. **Service Bus uç noktası Ekle** bölmesinde aşağıdaki bilgileri girin:

   **Uç nokta adı**: bitiş noktasının adı.

   **Service Bus ad alanı**: oluşturduğunuz ad alanını seçin.

   **Service Bus kuyruğu**: oluşturduğunuz kuyruğu seçin.

   ![Azure portal IoT Hub 'ınıza bir uç nokta ekleyin](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. **Oluştur**’u seçin. Uç nokta başarıyla oluşturulduktan sonra, sonraki adıma geçin.

### <a name="add-a-routing-rule"></a>Yönlendirme kuralı ekleme

1. **İleti yönlendirme** bölmesine geri dönün, **rotalar** sekmesini seçin ve **+ Ekle**' yi seçin.

1. **Yol Ekle** bölmesinde aşağıdaki bilgileri girin:

   **Ad**: yönlendirme kuralının adı.

   **Uç nokta**: oluşturduğunuz uç noktayı seçin.

   **Veri kaynağı**: **cihaz telemetri iletileri**' ni seçin.

   **Yönlendirme sorgusu**: girin `temperatureAlert = "true"` .

   ![Azure portal yönlendirme kuralı ekleme](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. **Kaydet**’i seçin. **İleti yönlendirme** bölmesini kapatabilirsiniz.

## <a name="create-and-configure-a-logic-app"></a>Mantıksal uygulama oluşturma ve yapılandırma

Yukarıdaki bölümde, IoT Hub 'ınızı Service Bus kuyruğuna bir sıcaklık uyarısı içeren iletileri yönlendirmek üzere ayarlarsınız. Şimdi, Service Bus kuyruğu izlemek için bir mantıksal uygulama ayarlarsınız ve kuyruğa bir ileti eklendiğinde e-posta bildirimi gönderilir.

### <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

1. **Kaynak**  >  **tümleştirme**  >  **mantıksal uygulaması** oluştur ' u seçin.

1. Aşağıdaki bilgileri girin:

   **Ad**: mantıksal uygulamanın adı.

   **Kaynak grubu**: IoT Hub 'ınızın kullandığı kaynak grubunu kullanın.

   **Konum**: IoT Hub 'ınızın kullandığı konumu kullanın.

   ![Azure portal bir mantıksal uygulama oluşturun](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. **Oluştur**’u seçin.

### <a name="configure-the-logic-app-trigger"></a>Mantıksal uygulama tetikleyicisini yapılandırma

1. Mantıksal uygulamayı açın. Mantıksal uygulamayı almanın en kolay yolu kaynak bölmesinde kaynak **grupları** seçmek, kaynak grubunuzu seçmek ve ardından kaynak listesinden mantıksal uygulamanızı seçmesidir. Mantıksal uygulamayı seçtiğinizde Logic Apps Tasarımcısı açılır.

1. Logic Apps tasarımcısında **Şablonlar** ' a kaydırın ve **boş mantıksal uygulama**' yı seçin.

   ![Azure portal boş bir mantıksal uygulama ile başlayın](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. **Tümü** sekmesini seçin ve ardından **Service Bus**' yi seçin.

   ![Mantıksal uygulamanızı oluşturmaya başlamak için Service Bus seçin Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. **Tetikleyiciler** altında **bir veya daha fazla ileti bir sıraya ulaştığında (otomatik olarak tamamlanır)** seçeneğini belirleyin.

   ![Azure portal mantıksal uygulamanız için tetikleyiciyi seçin](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus bağlantısı oluşturun.
   1. Bir bağlantı adı girin ve listeden Service Bus ad alanınızı seçin. Sonraki ekran açılır.

      ![Bir sıraya veya daha fazla ileti geldiğinde bir sıraya (otomatik tamamlamayı) bir veya daha fazla ileti ulaştığında oluşan ekran görüntüsü.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Service Bus ilkesini (RootManageSharedAccessKey) seçin. Ardından  **Oluştur**' u seçin.

      ![Azure portal mantıksal uygulamanız için bir hizmet veri yolu bağlantısı oluşturma](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Son ekranda, **kuyruk adı** için, açılan listeden oluşturduğunuz kuyruğu seçin. `175` **En fazla ileti sayısı** için girin.

      ![Mantıksal uygulamanızdaki Service Bus bağlantısı için en fazla ileti sayısını belirtin](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Değişikliklerinizi kaydetmek için Logic Apps tasarımcısının en üstündeki menüden **Kaydet** ' i seçin.

### <a name="configure-the-logic-app-action"></a>Mantıksal uygulama eylemini yapılandırma

1. Bir SMTP hizmet bağlantısı oluşturun.

   1. **Yeni adım**'ı seçin. **Bir eylem seçin** bölümünde **Tümü** sekmesini seçin.

   1. `smtp`Arama kutusuna yazın, arama sonucunda **SMTP** hizmetini seçin ve ardından **e-posta gönder**' i seçin.

      ![Azure portal mantıksal uygulamanızda bir SMTP bağlantısı oluşturma](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Posta kutunuzun SMTP bilgilerini girin ve ardından **Oluştur**' u seçin.

      ![Azure portal mantıksal uygulamanıza SMTP bağlantı bilgilerini girin](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook. com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en)ve [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)için SMTP bilgilerini alın.

      > [!NOTE]
      > Bağlantıyı kurmak için TLS/SSL 'yi devre dışı bırakmanız gerekebilir. Bu durumda ve bağlantı kurulduktan sonra TLS 'yi yeniden etkinleştirmek istiyorsanız, bu bölümün sonundaki isteğe bağlı adıma bakın.

   1. **E-posta gönder** adımında **yeni parametre Ekle** açılır listesinden **Kimden**,, **Konu** ve **gövde**' **yi seçin**. Seçim kutusunu kapatmak için ekrandaki herhangi bir yere tıklayın veya dokunun.

      ![SMTP bağlantısı e-posta alanlarını seçin](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. E-posta **adresinizi ve ' yi,**  `High temperature detected` **Konu** ve **gövde** için girin. **Bu akış iletişim kutusunda kullanılan uygulamalardan ve bağlayıcılardan dinamik Içerik Ekle** iletişim kutusu açılırsa, kapatmak için **Gizle** ' yi seçin. Bu öğreticide dinamik içerik kullanmayın.

      ![SMTP bağlantısı e-posta alanlarını doldur](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. SMTP bağlantısını kaydetmek için **Kaydet** ' i seçin.

1. Seçim E-posta sağlayıcınızla bağlantı kurmak için TLS 'yi devre dışı bırakmanız ve yeniden etkinleştirmek istiyorsanız aşağıdaki adımları izleyin:

   1. **Mantıksal uygulama** bölmesindeki **geliştirme araçları** altında **API bağlantıları**' nı seçin.

   1. API bağlantıları listesinden SMTP bağlantısını seçin.

   1. **SMTP API bağlantısı** bölmesinde, **genel** altında, **API bağlantısını Düzenle**' yi seçin.

   1. **API bağlantısını Düzenle** bölmesinde **SSL 'yi etkinleştir**' i seçin, e-posta hesabınız için parolayı yeniden girin ve **Kaydet**' i seçin.

      ![Azure portal mantıksal uygulamanızda SMTP API bağlantısını düzenleyin](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Mantıksal uygulamanız artık Service Bus kuyruğundan sıcaklık uyarılarını işlemeye hazır ve e-posta hesabınıza bildirim gönderiyor.

## <a name="test-the-logic-app"></a>Mantıksal uygulamayı test etme

1. İstemci uygulamasını cihazınızda başlatın.

1. Fiziksel bir cihaz kullanıyorsanız, sıcaklık 30 derece uzunluğu aşana kadar ısı sensörine yakın bir ısı kaynağı dikkatle getirin. Çevrimiçi simülatör kullanıyorsanız, istemci kodu 30 C 'yi aşan telemetri iletilerini rastgele olarak çıktı.

1. Mantıksal uygulama tarafından gönderilen e-posta bildirimlerini almaya başlamanız gerekir.

   > [!NOTE]
   > E-posta hizmeti sağlayıcınız, e-postayı gönderen sizin olduğundan emin olmak için gönderici kimliğini doğrulamanız gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub 'ınızı ve posta kutunuzu, sıcaklık izleme ve bildirimler için bağlayan bir mantıksal uygulamayı başarıyla oluşturdunuz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]