---
title: Öğretici-tetiklenecek IoT Hub olaylarını kullanın Azure Logic Apps
description: Bu öğreticide, IoT Hub olaylara göre Azure Logic Apps eylemleri gerçekleştirmek üzere otomatik işlemler oluşturmak için Azure Event Grid olay yönlendirme hizmetinin nasıl kullanılacağı gösterilmektedir.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3772988e9bf4f733323adae6c0527c8b811c4d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624464"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Öğretici: Event Grid ve Logic Apps kullanarak Azure IoT Hub olayları hakkında e-posta bildirimleri gönderme

Azure Event Grid, aşağı akış iş uygulamalarınızda eylemler tetikleyerek IoT Hub'daki olaylara karşılık vermenize olanak tanır.

Bu makalede IoT Hub ve Event Grid kullanan bir örnek yapılandırma gösterilmektedir. Son olarak, bir cihaz IoT Hub 'ınıza her bağlanıp bağlantısı kesildiğinde bir bildirim e-postası gönderecek şekilde ayarlanmış bir Azure mantıksal uygulamanız vardır. Event Grid, bağlantının kesilmesi için zamanında bildirim almak üzere kullanılabilir. Ölçümler ve Tanılamalar birkaç sürebilir (yani, 20 veya daha fazla), günlüklerde/uyarılarda göstermek için dakikalar içinde bir sayı koymak istemezsiniz. Bu, kritik altyapı için kabul edilemez olabilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Office 365 Outlook veya Outlook.com gibi Azure Logic Apps tarafından desteklenen herhangi bir e-posta sağlayıcısından bir e-posta hesabı. Bu e-posta hesabı olay bildirimlerini göndermek için kullanılır.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Portalda Azure Cloud Shell terminali kullanarak hızlı bir şekilde yeni bir IoT Hub oluşturabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın. 

1. Sayfanın sağ üst köşesinde Cloud Shell düğmesini seçin.

   ![Cloud Shell düğmesi](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Yeni bir kaynak grubu oluşturmak için şu komutu çalıştırın:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. IoT Hub 'ı oluşturmak için aşağıdaki komutu çalıştırın:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Cloud Shell terminalini en aza indirin. Öğreticinin ilerleyen kısımlarında daha sonra Shell 'e geri döneceksiniz.

## <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

Ardından, bir Logic App oluşturun ve IoT Hub 'ından istekleri işleyen bir HTTP Event Grid tetikleyicisi ekleyin. 

### <a name="create-a-logic-app-resource"></a>Mantıksal uygulama kaynağı oluşturma

1. [Azure Portal](https://portal.azure.com), **kaynak oluştur**' u seçin, sonra arama kutusuna "Logic App" yazın ve geri dön ' ü seçin. Sonuçlardan **Logic App** ' i seçin.

   ![Mantıksal uygulama oluşturma](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Sonraki ekranda **Oluştur**' u seçin. 

1. Mantıksal uygulamanıza aboneliğiniz içinde benzersiz olan bir ad verin, ardından IoT Hub'ınızla aynı aboneliği, kaynak grubunu ve konumu seçin. 

   ![Mantıksal uygulama oluşturma alanları](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. **Gözden geçir ve oluştur**’u seçin.

1. Ayarlarınızı doğrulayın ve ardından **Oluştur**' u seçin.

1. Kaynak oluşturulduktan sonra **Kaynağa Git**' i seçin. 

1. Logic Apps tasarımcısında, **şablonları** görmek için sayfayı aşağı kaydırın. Mantıksal uygulamanızı sıfırdan oluşturabilmeniz için **boş mantıksal uygulama** ' yı seçin.

### <a name="select-a-trigger"></a>Tetikleyici seçme

Tetikleyici, mantıksal uygulamanızı başlatan belirli bir olaydır. Bu öğreticide, iş akışını başlatan tetikleyici HTTP üzerinden bir istek alır.  

1. Bağlayıcılar ve tetikleyiciler arama çubuğunda **HTTP** yazın.

1. Sonuçlarda ilerleyin ve Istek ' ı seçin. tetikleyici olarak **BIR http isteği alındığında** . 

   ![HTTP istek tetikleyicisini seçme](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. **Şema oluşturmak için örnek yük kullanma** öğesini seçin. 

   ![Örnek yük kullanma](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. *Cihaza bağlı olay şeması* JSON 'sini metin kutusuna yapıştırın, sonra **bitti**' yi seçin:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Bu olay bir cihaz IoT Hub 'ına bağlandığında yayınlar.

> [!NOTE]
> **İsteğinize Uygulama/JSON olarak ayarlanmış bir Content-Type üst bilgisi eklemeyi unutmayın** önerisinin bulunduğu bir açılan bildirim alabilirsiniz. Bu öneriyi güvenle yoksayabilir ve sonraki bölüme geçebilirsiniz. 

### <a name="create-an-action"></a>Eylem oluşturma

Eylemler, tetikleyici mantıksal uygulama iş yükünü başlattıktan sonra gerçekleşen adımlardır. Bu öğreticide, e-posta sağlayıcınızdan bir e-posta bildirimi gönderme eylemi kullanılır. 

1. **Yeni adım**'ı seçin. Bu **işlem, bir eylemi seçmek** için bir pencere açar.

1. **Outlook** için arama yapın.

1. E-posta sağlayıcınıza uygun bağlayıcıyı bulun ve seçin. Bu öğretici **Outlook.com** kullanır. Diğer e-posta sağlayıcılarının adımları da bunlara benzer. 

   ![E-posta sağlayıcısının bağlayıcısını seçme](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. **E-posta gönder (v2)** eylemini seçin. 

1. **Oturum aç** ' ı seçin ve e-posta hesabınızda oturum açın. Uygulamanın bilgilerinize erişmesine izin vermek için **Evet** ' i seçin.

1. E-posta şablonunuzu oluşturun. 

   * **Kime**: Bildirim e-postalarını alacak e-posta adresini girin. Bu öğreticide, test için erişebileceğiniz bir e-posta hesabı kullanın. 

   * **Konu**: Konunun metnini girin. Konu metin kutusuna tıkladığınızda, içerilecek dinamik içerik ' i seçebilirsiniz. Örneğin, bu öğretici kullanır `IoT Hub alert: {eventType}` . Dinamik içeriği göremiyorsanız, **dinamik Içerik Ekle** köprüsünü seçin--Bu, açıp kapatır.

   * **Gövde**: E-postanız için metni yazın. Olay verilerine göre dinamik içerik eklemek için seçici aracından JSON özelliklerini seçin. Dinamik içeriği göremiyorsanız **gövde** metin kutusu altında **dinamik içerik Ekle** köprüsünü seçin. İstediğiniz alanları göstermezse, önceki eylemden alanları dahil etmek için dinamik içerik ekranında *daha fazla* ' ya tıklayın.

   E-posta şablonunuz, aşağıdaki örneğe benzer görünebilir:

   ![E-posta bilgilerini doldurma](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Logic Apps Tasarımcısı’nda **Kaydet**’i seçin.  

### <a name="copy-the-http-url"></a>HTTP URL'sini kopyalama

Logic Apps Tasarımcısı'ndan çıkmadan önce, mantıksal uygulamalarınızın tetikleyici için dinlediği URL'yi kopyalayın. Bu URL'yi, Event Grid'i yapılandırmak için kullanırsınız. 

1. **Bir HTTP isteği alındığında** tetikleyici yapılandırma kutusunu tıklayarak genişletin. 

1. Yanındaki kopyala düğmesini seçerek **HTTP POST URL** değerini kopyalayın. 

   ![HTTP POST URL değerini kopyalama](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Sonraki bölümde başvurabilmek için bu URL'yi saklayın. 

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub olayları için aboneliği yapılandırma

Bu bölümde, IoT Hub'ınızı gerçekleşen olayları yayımlamak için yapılandıracaksınız. 

1. Azure portalında IoT Hub'ınıza gidin. Bunu, **kaynak grupları**' nı seçerek ve ardından Bu öğreticinin kaynak grubunu seçip kaynak listesinden IoT Hub 'ınızı seçerek yapabilirsiniz.

1. **Olaylar**'ı seçin.

   ![Event Grid ayrıntılarını açma](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. **Olay aboneliği**’ni seçin. 

   ![Yeni olay aboneliği oluşturma](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Olay aboneliğini aşağıdaki değerlerle oluşturun: 

   1. **OLAY ABONELİK AYRINTILARI** bölümünde:
      1. Olay aboneliği için bir **ad** sağlayın. 
      2. **Olay şeması** Için **Event Grid şeması** ' nı seçin. 
   2. **KONU AYRINTILARI** bölümünde:
      1. **Konu türü**'nün **IoT Hub** olarak ayarlandığını doğrulayın. 
      2. IoT hub adının **Kaynak** alanındaki değer olarak ayarlandığını doğrulayın. 
      3. Sizin için oluşturulacak **Sistem konusu** için bir ad girin. Sistem konuları hakkında bilgi edinmek için bkz. [sistem konularına genel bakış](system-topics.md).
   3. **OLAY TÜRLERİ** bölümünde:
      1. **Olay Türlerine Filtrele** açılan menüsünü seçin.
      1. **Oluşturulan** **Cihaz ve cihaz** bağlantısı kesildi onay kutularını seçili bırakarak yalnızca **cihaz bağlı** ve **cihaz bağlantısı kesilen** cihazların seçimini kaldırın.

         ![Abonelik olay türlerini seçme](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. **UÇ NOKTA AYRINTILARI** bölümünde: 
       1. **Uç nokta türünü** **Web kancası** olarak seçin.
       2. **Bir uç nokta seçin**'i seçin ve mantıksal uygulamanızdan kopyaladığınız URL’yi yapıştırıp seçimi onaylayın.

         ![uç nokta URL'si seçme](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         İşiniz bittiğinde, bölme aşağıdaki örnekteki gibi görünmelidir: 

         ![Örnek olay aboneliği formu](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  **Oluştur**’u seçin.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Telemetriyi bağlayan yeni bir cihazın benzetimini yapın

Azure CLı kullanarak bir cihaz bağlantısını hızla taklit ederek mantıksal uygulamanızı test edin. 

1. Terminalinizi yeniden açmak için Cloud Shell düğmesini seçin.

1. Sanal cihaz kimliği oluşturmak için aşağıdaki komutu çalıştırın:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. IoT Hub ve Telemetriyi göndermek için cihazınızın bağlanmasına benzetim yapmak üzere aşağıdaki komutu çalıştırın:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Sanal cihaz IoT Hub bağlandığında, size bir "DeviceConnected" olayı bildiren bir e-posta gönderilir.

1. Simülasyon tamamlandığında, size "DeviceConnected bağlantısı kesilen" bir olay bildiren bir e-posta gönderilir. 

    ![Örnek Uyarı postası](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide Azure aboneliğinize ücret uygulanmasına neden olan kaynaklar kullanılmıştır. Öğreticiyi denemeyi ve sonuçlarınızı test etmeyi tamamladığınızda, saklamak istemediğiniz kaynakları devre dışı bırakın veya silin. 

Bu öğreticide oluşturulan tüm kaynakları silmek için kaynak grubunu silin. 

1. **Kaynak grupları**' nı seçin ve ardından Bu öğretici için oluşturduğunuz kaynak grubunu seçin.

2. Kaynak grubu bölmesinde **kaynak grubunu sil**' i seçin. Kaynak grubu adını girmeniz istenir ve ardından onu silebilirsiniz. İçinde bulunan tüm kaynaklar da kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

* [Event Grid kullanıp olayları tetikleyerek IoT Hub'ı olaylarına tepki verme](../iot-hub/iot-hub-event-grid.md) hakkında daha fazla bilgi edinin.
* [Cihazla bağlantılı ve bağlantısız olayları nasıl sıralayacağınızı öğrenin](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* [Event Grid](overview.md) ile başka neler yapabileceğinizi öğrenin.

Desteklenen mantıksal uygulama bağlayıcılarının tüm listesi için bkz. 

> [!div class="nextstepaction"]
> [Bağlayıcılara genel bakış](/connectors/).