---
title: 'Öğretici: tetiklenecek IoT Hub olaylarını kullanın Azure Logic Apps'
description: 'Öğretici: Azure Event Grid olay yönlendirme hizmetini kullanarak IoT Hub olaylarına göre Azure Logic Apps eylemleri gerçekleştirmek için otomatikleştirilmiş işlemler oluşturun.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106784"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Öğretici: Mantıksal uygulamalar'ı kullanarak Azure IOT Hub olayları hakkında e-posta bildirimleri gönderme

Azure Event Grid, aşağı akış iş uygulamalarınızda eylemler tetikleyerek IoT Hub'daki olaylara karşılık vermenize olanak tanır.

Bu makale IoT Hub ve Event Grid kullanan örnek bir yapılandırmada size yol gösterir. Sonuna geldiğinizde, IoT hub'ınıza her cihaz eklendiğinde bildirim e-postası gönderecek şekilde ayarlanmış bir Azure mantıksal uygulamanız olacaktır. 

## <a name="prerequisites"></a>Önkoşullar

* Azure Logic Apps tarafından desteklenen Office 365 Outlook, Outlook.com veya Gmail gibi bir e-posta sağlayıcısından alınmış e-posta hesabı. Bu e-posta hesabı olay bildirimlerini göndermek için kullanılır. Desteklenen Logic App bağlayıcılarının tam listesi için bkz. [Bağlayıcılara genel bakış](https://docs.microsoft.com/connectors/)
* Etkin bir Azure hesabı. Hesabınız yoksa [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).
* Azure'da bir IoT Hub'ı. Henüz oluşturmadıysanız, yönergeler için bkz. [IoT Hub'ı kullanmaya başlama](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

İlk olarak, bir mantıksal uygulama oluşturun ve sanal makineniz için kaynak grubunu izleyen bir Olay kılavuzu tetikleyicisi ekleyin. 

### <a name="create-a-logic-app-resource"></a>Mantıksal uygulama kaynağı oluşturma

1. [Azure portalında](https://portal.azure.com) **Kaynak oluştur** > **Tümleştirme** > **Mantıksal Uygulama**’yı seçin.

   ![Mantıksal uygulama oluşturma](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Mantıksal uygulamanıza aboneliğiniz içinde benzersiz olan bir ad verin, ardından IoT Hub'ınızla aynı aboneliği, kaynak grubunu ve konumu seçin. 
3. **Oluştur**'u seçin.

4. Kaynak oluşturulduktan sonra mantıksal uygulamanıza gidin. 

5. Logic Apps tasarımcı, daha hızlı bir şekilde başlamak için ortak desenlere ilişkin şablonlar gösterir. Mantıksal uygulamanızı sıfırdan oluşturabilmek için Logic App Tasarımcısı'nda **Şablonlar**'ın altından **Boş Mantıksal Uygulama**'yı seçin.

### <a name="select-a-trigger"></a>Tetikleyici seçme

Tetikleyici, mantıksal uygulamanızı başlatan belirli bir olaydır. Bu öğreticide, iş akışını başlatan tetikleyici HTTP üzerinden bir istek alır.  

1. Bağlayıcılar ve tetikleyiciler arama çubuğunda **HTTP** yazın.
2. Tetikleyici olarak **İstek - Bir HTTP isteği alındığında**'yı seçin. 

   ![HTTP istek tetikleyicisini seçme](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. **Şema oluşturmak için örnek yük kullanma** öğesini seçin. 

   ![HTTP istek tetikleyicisini seçme](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Aşağıdaki örnek JSON kodunu metin kutusuna yapıştırın ve **Bitti**'yi seçin:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

5. **İsteğinize Uygulama/JSON olarak ayarlanmış bir Content-Type üst bilgisi eklemeyi unutmayın** önerisinin bulunduğu bir açılan bildirim alabilirsiniz. Bu öneriyi güvenle yoksayabilir ve sonraki bölüme geçebilirsiniz. 

### <a name="create-an-action"></a>Bir eylem oluşturun

Eylemler, tetikleyici mantıksal uygulama iş yükünü başlattıktan sonra gerçekleşen adımlardır. Bu öğreticide, e-posta sağlayıcınızdan bir e-posta bildirimi gönderme eylemi kullanılır. 

1. **Yeni adım**'ı seçin. Bu işlemin ardından, **Eylem seçin** penceresi açılır.

2. **E-posta**'yı arayın.

3. E-posta sağlayıcınıza uygun bağlayıcıyı bulun ve seçin. Bu öğreticide **Office 365 Outlook** kullanılır. Diğer e-posta sağlayıcılarının adımları da bunlara benzer. 

   ![E-posta sağlayıcısının bağlayıcısını seçme](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. **E-posta gönder** eylemini seçin. 

5. İstenirse, e-posta hesabınızda oturum açın. 

6. E-posta şablonunuzu oluşturun. 
   * **Kime**: Bildirim e-postalarını alacak olan e-posta adresini girin. Bu öğreticide, test etmek için erişebileceğiniz bir e-posta hesabı kullanın. 
   * **Konu** ve **Gövde**: E-postanızın metnini yazın. Olay verileri temelinde dinamik içerik eklemek için seçici aracından JSON özelliklerini seçin.  

   E-posta şablonunuz, aşağıdaki örneğe benzer görünebilir:

   ![E-posta bilgilerini doldurma](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Mantıksal uygulamanızı kaydedin. 

### <a name="copy-the-http-url"></a>HTTP URL'sini kopyalama

Logic Apps Tasarımcısı'ndan çıkmadan önce, mantıksal uygulamalarınızın tetikleyici için dinlediği URL'yi kopyalayın. Bu URL'yi, Event Grid'i yapılandırmak için kullanırsınız. 

1. **Bir HTTP isteği alındığında** tetikleyici yapılandırma kutusunu tıklayarak genişletin. 
2. Yanındaki kopyala düğmesini seçerek **HTTP POST URL** değerini kopyalayın. 

   ![HTTP POST URL değerini kopyalama](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Sonraki bölümde başvurabilmek için bu URL'yi saklayın. 

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub olayları için aboneliği yapılandırma

Bu bölümde, IoT Hub'ınızı gerçekleşen olayları yayımlamak için yapılandıracaksınız. 

1. Azure portalında IoT Hub'ınıza gidin. 
2. **Olaylar**'ı seçin.

   ![Event Grid ayrıntılarını açma](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. **Olay aboneliği**’ni seçin. 

   ![Yeni olay aboneliği oluşturma](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Olay aboneliğini aşağıdaki değerlerle oluşturun: 
   * **Olay Türü**: Tüm olay türlerine abone ol'un işaretini kaldırın ve menüde **Cihaz Oluşturuldu**'yu seçin.
   * **Uç Nokta Ayrıntıları**: Uç Nokta Türü olarak **Web Kancası**'nı seçin, Uç nokta seçin'e tıklayın, mantıksal uygulamanızdan kopyaladığınız URL'yi yapıştırın ve seçimi onaylayın.

     ![uç nokta URL'si seçme](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Olay Aboneliği Ayrıntıları**: Açıklayıcı bir ad girin ve **Olay Kılavuz Şeması**'nı seçin

   İşiniz bittiğinde, form aşağıdaki örnekteki gibi görünmelidir: 

    ![Örnek olay aboneliği formu](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Olay aboneliğini buraya kaydedebilir ve IoT Hub'ınızda oluşturulan her cihaz için bildirimler alabilirsiniz. Bu öğreticide, belirli cihazları filtrelemek için isteğe bağlı alanları kullanalım. Formun üst kısmında **ek özellikler** ' i seçin. 

6. Aşağıdaki filtreleri oluşturun:

   * **Konu Şununla Başlar**: Building1 içindeki cihaz olaylarını filtrelemek için `devices/Building1_` değerini girin.
   * **Konu Şununla Biter**: Sıcaklıkla ilgili cihaz olaylarını filtrelemek için `_Temperature` değerini girin.

5. Olay aboneliğini kaydetmek için **Oluştur**'u seçin.

## <a name="create-a-new-device"></a>Yeni cihaz oluşturma

Olay bildirim e-postasını tetiklemek için yeni bir cihaz oluşturarak mantıksal uygulamanızı test edin. 

1. IoT Hub'ınızda **IoT Cihazları**'nı seçin. 
2. **Add (Ekle)** seçeneğini belirleyin.
3. **Cihaz Kimliği** için `Building1_Floor1_Room1_Temperature` girin.
4. **Kaydet**’i seçin. 
5. Olay abonelik filtrelerini test etmek için farklı cihaz kimlikleri olan birden çok cihaz ekleyebilirsiniz. Şu örnekleri deneyin: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

IoT Hub'ınıza birkaç cihaz ekledikten sonra, hangisinin mantıksal uygulamayı tetiklediğini görmek için e-postanızı gözden geçirin. 

## <a name="use-the-azure-cli"></a>Azure CLI kullanma

Azure portalı kullanmak yerine, IoT Hub adımlarını Azure CLI'yi kullanarak gerçekleştirebilirsiniz. Ayrıntılar için, Azure CLI'nin [olay aboneliği oluşturma](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) ve [IoT cihazı oluşturma](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity) sayfalarına bakın

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide Azure aboneliğinize ücret uygulanmasına neden olan kaynaklar kullanılmıştır. Öğreticideki denemeleriniz ve sonuçlarınızın testi bittiğinde, korumak istemediğiniz kaynakları devre dışı bırakın veya silin. 

Mantıksal uygulamanızda yapılan çalışmayı kaybetmek istemiyorsanız, bunu silmek yerine devre dışı bırakın. 

1. Mantıksal uygulamanıza gidin.
2. **Genel Bakış** dikey penceresinde **Sil**'i veya **Devre Dışı Bırak**'ı seçin. 

Her aboneliğin tek bir ücretsiz IoT Hub'ı olabilir. Bu öğretici için ücretsiz bir hub oluşturduysanız, ücretleri önlemek için bunu silmeniz gerekmez.

1. IoT Hub'ınıza gidin. 
2. **Genel Bakış** dikey penceresinde **Sil**'i seçin. 

IoT Hub'ınızı korusanız bile, oluşturduğunuz olay aboneliğini silmek isteyebilirsiniz. 

1. IoT Hub'ınızda **Event Grid**'i seçin.
2. Kaldırmak istediğiniz olay aboneliğini seçin. 
3. **Sil**’i seçin. 

## <a name="next-steps"></a>Sonraki adımlar

* [Event Grid kullanıp olayları tetikleyerek IoT Hub'ı olaylarına tepki verme](../iot-hub/iot-hub-event-grid.md) hakkında daha fazla bilgi edinin.
* [Cihazla bağlantılı ve bağlantısız olayları nasıl sıralayacağınızı öğrenin](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* [Event Grid](overview.md) ile başka neler yapabileceğinizi öğrenin.


