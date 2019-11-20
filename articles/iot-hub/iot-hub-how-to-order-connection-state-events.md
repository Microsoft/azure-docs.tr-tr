---
title: Cihaz bağlantısı olaylarını sıralama fr Azure IoT Hub w/Azure Cosmos DB
description: Bu makalede, en son bağlantı durumunu korumak için Azure Cosmos DB kullanılarak cihaz bağlantı olaylarının Azure IoT Hub nasıl sipariş edileceğini ve kaydedilecek açıklanmaktadır
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954262"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Azure IoT Hub cihaz bağlantısı olaylarını Azure Cosmos DB kullanarak sıralama

Azure Event Grid, etkinlik tabanlı uygulamalar oluşturmanıza ve iş çözümlerinizde IoT olaylarını kolayca tümleştirmenize yardımcı olur. Bu makalede, Cosmos DB ' de en son cihaz bağlantısı durumunu izlemek ve depolamak için kullanılabilen bir kurulum adım adım açıklanmaktadır. Cihaz bağlantısı kesik ve cihaz bağlantısı kesilen olaylarda bulunan sıra numarasını kullanacağız ve en son durumu Cosmos DB olarak depolar. Cosmos DB bir koleksiyona karşı yürütülen bir uygulama mantığı olan saklı bir yordam kullanacağız.

Sıra numarası, onaltılık bir sayının dize gösterimidir. Daha büyük sayıyı belirlemek için dize karşılaştırma kullanabilirsiniz. Dizeyi onaltılı olarak dönüştürüyorsanız sayı 256 bitlik bir sayı olacaktır. Sıra numarası kesinlikle artıyor ve en son olay diğer olaylardan daha yüksek bir sayıya sahip olacaktır. Bu, sık kullanılan cihaz bağlantısı ve bağlantınız varsa ve Azure Event Grid etkinlik sıralamasını desteklemediğinden bir aşağı akış eylemini tetiklemek için yalnızca en son olayın kullanılmasını sağlamak istiyorsanız kullanışlıdır.

## <a name="prerequisites"></a>Önkoşullar

* Etkin bir Azure hesabı. Hesabınız yoksa [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).

* Etkin bir Azure Cosmos DB SQL API hesabı. Henüz bir tane oluşturmadıysanız, bkz. bir anlatım için [veritabanı hesabı oluşturma](../cosmos-db/create-sql-api-java.md#create-a-database-account) .

* Veritabanınızdaki bir koleksiyon. İzlenecek yol için [koleksiyon ekleme](../cosmos-db/create-sql-api-java.md#add-a-container) bölümüne bakın. Koleksiyonunuzu oluştururken bölüm anahtarı için `/id` kullanın.

* Azure'da bir IoT Hub'ı. Henüz oluşturmadıysanız, yönergeler için bkz. [IoT Hub'ı kullanmaya başlama](iot-hub-csharp-csharp-getstarted.md).

## <a name="create-a-stored-procedure"></a>Saklı yordam oluşturma

İlk olarak, bir saklı yordam oluşturun ve bunu, gelen olayların sıra numaralarını karşılaştıran ve veritabanındaki cihaz başına en son olayı kaydeden bir mantığı çalıştıracak şekilde ayarlayın.

1. Cosmos DB SQL API 'niz ' nde, **Yeni saklı yordam** > **Veri Gezgini** > **öğeleri** ' ni seçin.

   ![Saklı yordam oluştur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Saklı yordam KIMLIĞI için **Latestdeviceconnectionstate** girin ve **saklı yordam gövdesinde**aşağıdakileri yapıştırın. Bu kodun, saklı yordam gövdesinde var olan herhangi bir kodu değiştirmesini unutmayın. Bu kod, cihaz KIMLIĞI başına bir satır tutar ve en yüksek sıra numarasını tanımlayarak bu cihaz KIMLIĞININ en son bağlantı durumunu kaydeder.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Saklı yordamı Kaydet:

    ![saklı yordamı Kaydet](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

İlk olarak, bir mantıksal uygulama oluşturun ve sanal makineniz için kaynak grubunu izleyen bir Olay kılavuzu tetikleyicisi ekleyin.

### <a name="create-a-logic-app-resource"></a>Mantıksal uygulama kaynağı oluşturma

1. [Azure Portal](https://portal.azure.com) **+ kaynak oluştur**' u seçin, **tümleştirme** ' i ve ardından **mantıksal uygulama**' yı seçin.

   ![Mantıksal uygulama oluşturma](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Mantıksal uygulamanıza aboneliğiniz içinde benzersiz olan bir ad verin, ardından IoT Hub'ınızla aynı aboneliği, kaynak grubunu ve konumu seçin.

   ![Yeni mantıksal uygulama](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Mantıksal uygulamayı oluşturmak için **Oluştur** ' u seçin.

   Mantıksal uygulamanız için bir Azure kaynağı oluşturdunuz. Azure mantıksal uygulamanızı dağıttıktan sonra Logic Apps Tasarımcısı'nda hızlı bir başlangıç yapmanıza yardımcı olacak ortak desen şablonları gösterilir.

   > [!NOTE]
   > Mantıksal uygulamanızı bulup açmak için **kaynak grupları** ' nı seçin ve bu nasıl yapılır için kullandığınız kaynak grubunu seçin. Ardından yeni mantıksal uygulamanızı seçin. Bu, mantıksal uygulama Tasarımcısı ' nı açar.

4. Mantıksal uygulama Tasarımcısı ' nda, sık kullanılan Tetikleyicileri görene kadar sağa kaydırın. Mantıksal uygulamanızı sıfırdan oluşturabilmeniz için **Şablonlar**' ın altında **boş mantıksal uygulama** ' yı seçin.

### <a name="select-a-trigger"></a>Tetikleyici seçme

Tetikleyici, mantıksal uygulamanızı başlatan belirli bir olaydır. Bu öğreticide, iş akışını başlatan tetikleyici HTTP üzerinden bir istek alır.

1. Bağlayıcılar ve Tetikleyiciler arama çubuğunda **http** yazın ve ENTER tuşuna basın.

2. Tetikleyici olarak **İstek - Bir HTTP isteği alındığında**'yı seçin.

   ![HTTP istek tetikleyicisini seçme](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. **Şema oluşturmak için örnek yük kullanma** öğesini seçin.

   ![Şema oluşturmak için örnek yük kullanma](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Aşağıdaki örnek JSON kodunu metin kutusuna yapıştırın ve **Bitti**'yi seçin:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Örnek JSON yükünü Yapıştır](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. **İsteğinize Uygulama/JSON olarak ayarlanmış bir Content-Type üst bilgisi eklemeyi unutmayın** önerisinin bulunduğu bir açılan bildirim alabilirsiniz. Bu öneriyi güvenle yoksayabilir ve sonraki bölüme geçebilirsiniz.

### <a name="create-a-condition"></a>Koşul oluşturma

Mantıksal uygulama iş akışınızda, koşullar belirli bir koşulu geçirdikten sonra belirli eylemleri çalıştırmaya yardımcı olur. Koşul karşılandığında, istenen bir eylem tanımlanabilir. Bu öğretici için koşul, eventType tarafından cihazın bağlı veya cihazın bağlantısının kesilmediğini denetledir. Bu eylem, saklı yordamı veritabanınızda yürütmek olacaktır.

1. **+ Yeni adım** **' ı seçin ve ardından** **koşulu**bulun ve seçin. **Bir değer Seç** ' e tıklayın ve dinamik içerik ' i gösteren bir kutu açılır ve seçilebilir alanlar görüntülenir. Yalnızca cihaza bağlı ve cihaz bağlantısı kesik olayları için bunu yürütmek üzere alanları aşağıda gösterildiği gibi girin:

   * Değer seçin: **EventType** --bu alana tıkladığınızda görüntülenen dinamik içerikte bulunan alanlardan bunu seçin.
   * **İle biten**"eşittir" olarak değiştirin.
   * Bir değer seçin: **nesiyonu**.

     ![Fill koşulu](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. **True ise** iletişim kutusunda **Eylem Ekle**' ye tıklayın.
  
   ![True ise Eylem Ekle](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Cosmos DB arayın ve **Azure Cosmos DB Çalıştır saklı yordamını** seçin

   ![CosmosDB araması](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. **Bağlantı adı** için **cosmosdb-Connection** ' ı girin ve tablodaki girişi seçin ve ardından **Oluştur**' u seçin. **Saklı yordam Yürüt** panelini görürsünüz. Alanlar için değerleri girin:

   **VERITABANı kimliği**: ToDoList

   **Koleksıyon kimliği**: öğeler

   **Sproc kimliği**: latestdeviceconnectionstate

5. **Yeni parametre Ekle**' yi seçin. Görüntülenen açılan menüde, **saklı yordamın** **bölüm anahtarı** ve parametreleri ' nin yanındaki kutuları işaretleyin ve ardından ekranda başka herhangi bir yere tıklayın. bölüm anahtarı değeri için bir alan ve saklı yordamın parametreleri için bir alan ekler.

   ![mantıksal uygulama eylemini doldur](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Şimdi aşağıda gösterildiği gibi bölüm anahtarı değerini ve parametrelerini girin. Köşeli ayraçları ve çift tırnak işaretlerini gösterildiği gibi yazdığınızdan emin olun. Burada kullanabileceğiniz geçerli değerleri almak için **dinamik Içerik Ekle** ' ye tıklamanız gerekebilir.

   ![mantıksal uygulama eylemini doldur](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Bölmenin **her biri için**bulduğu en üst kısmında, **önceki adımlardan bir çıkış seçin**altında BT **gövdesinin** seçili olduğundan emin olun.

   ![mantıksal uygulamayı her biri için doldur](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Mantıksal uygulamanızı kaydedin.

### <a name="copy-the-http-url"></a>HTTP URL'sini kopyalama

Logic Apps tasarımcısından çıkmadan önce, mantıksal uygulamanızın dinlediği URL 'YI bir tetikleyici için kopyalayın. Bu URL'yi, Event Grid'i yapılandırmak için kullanırsınız.

1. **Bir HTTP isteği alındığında** tetikleyici yapılandırma kutusunu tıklayarak genişletin.

2. Yanındaki kopyala düğmesini seçerek **HTTP POST URL** değerini kopyalayın.

   ![HTTP POST URL değerini kopyalama](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Sonraki bölümde başvurabilmek için bu URL'yi saklayın.

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub olayları için aboneliği yapılandırma

Bu bölümde, IoT Hub'ınızı gerçekleşen olayları yayımlamak için yapılandıracaksınız.

1. Azure portalında IoT Hub'ınıza gidin.

2. **Olaylar**'ı seçin.

   ![Event Grid ayrıntılarını açma](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. **+ Olay aboneliği**' ni seçin.

   ![Yeni olay aboneliği oluşturma](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. **Olay aboneliği ayrıntılarını**doldur: açıklayıcı bir ad belirtin ve **Event Grid şeması**' nı seçin.

5. **Olay türleri** alanlarını girin. Açılan listede, yalnızca **cihaz** bağlantısı ' nı seçin ve menüden **cihaz bağlantısı kesildi** ' ı seçin. Ekranda başka bir yere tıklayarak listeyi kapatın ve seçimlerinizi kaydedin.

   ![Aranacak olay türlerini ayarla](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. **Uç nokta ayrıntıları**Için uç nokta türünü **Web kancası** olarak seçin ve uç nokta seç ' e tıklayın ve mantıksal uygulamanızdan kopyaladığınız URL 'yi yapıştırın ve seçimi onaylayın.

   ![Uç nokta URL 'si seçin](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Form artık aşağıdaki örneğe benzer şekilde görünmelidir:

   ![Örnek olay aboneliği formu](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Olay aboneliğini kaydetmek için **Oluştur**'u seçin.

## <a name="observe-events"></a>Olayları gözlemleyin

Artık olay aboneliğiniz ayarlanmış olduğuna göre bir cihaz bağlayarak test edelim.

### <a name="register-a-device-in-iot-hub"></a>IoT Hub bir cihazı kaydetme

1. IoT Hub'ınızda **IoT Cihazları**'nı seçin.

2. Bölmenin en üstünde **+ Ekle** ' yi seçin.

3. **Cihaz Kimliği** için `Demo-Device-1` girin.

4. **Kaydet**’i seçin.

5. Farklı cihaz kimliklerine sahip birden çok cihaz ekleyebilirsiniz.

   ![Hub 'a eklenen cihazlar](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Cihaza yeniden tıklayın; Artık bağlantı dizeleri ve anahtarlar doldurulacak. Bağlantı dizesini (daha sonra kullanılmak üzere **birincil anahtar** ) kopyalayın.

   ![Cihaz için ConnectionString](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Start Raspberry PI simülatör

Cihaz bağlantısının benzetimini yapmak için Raspberry PI Web simülatörünü kullanalım.

[Start Raspberry PI simülatör](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Raspberry PI Web simülatörü üzerinde örnek bir uygulama çalıştırma

Bu, cihaza bağlı bir olayı tetikler.

1. Kodlama alanında, 15. satırdaki yer tutucusunu, önceki bölümün sonunda kaydettiğiniz Azure IoT Hub cihaz bağlantı dizeniz ile değiştirin.

   ![Cihaz bağlantı dizesinde Yapıştır](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. **Çalıştır**'ı seçerek uygulamayı çalıştırın.

Algılayıcı verilerini ve IoT Hub 'ınıza gönderilen iletileri gösteren aşağıdaki çıktıya benzer bir şey görürsünüz.

   ![Uygulamayı çalıştırma](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Simülatörü durdurmak ve **cihaz bağlantısı kesilen** bir olayı tetiklemek için **Durdur** ' a tıklayın.

Artık algılayıcı verilerini toplamak ve IoT Hub 'ınıza göndermek için örnek bir uygulama çalıştırmıştır.

### <a name="observe-events-in-cosmos-db"></a>Cosmos DB olayları gözlemleyin

Yürütülen saklı yordamın sonuçlarını Cosmos DB belgenizde görebilirsiniz. İşte bu şekilde görünür. Her satır, cihaz başına en son cihaz bağlantı durumunu içerir.

   ![Outcome](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Azure CLI kullanma

[Azure Portal](https://portal.azure.com)kullanmak yerine, IoT Hub ADıMLARı Azure CLI kullanarak gerçekleştirebilirsiniz. Ayrıntılar için, [olay aboneliği oluşturmak](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) ve [IoT cihazı oluşturmak](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)için Azure CLI sayfalarına bakın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide Azure aboneliğinize ücret uygulanmasına neden olan kaynaklar kullanılmıştır. Öğreticiyi denemeyi ve sonuçlarınızı test etmeyi tamamladığınızda, saklamak istemediğiniz kaynakları devre dışı bırakın veya silin.

Mantıksal uygulamanızda yapılan çalışmayı kaybetmek istemiyorsanız, bunu silmek yerine devre dışı bırakın.

1. Mantıksal uygulamanıza gidin.

2. **Genel bakış** dikey penceresinde **Sil** veya **devre dışı bırak**' ı seçin.

    Her aboneliğin tek bir ücretsiz IoT Hub'ı olabilir. Bu öğretici için ücretsiz bir hub oluşturduysanız, ücretleri önlemek için bunu silmeniz gerekmez.

3. IoT Hub'ınıza gidin.

4. **Genel bakış** dikey penceresinde **Sil**' i seçin.

    IoT Hub'ınızı korusanız bile, oluşturduğunuz olay aboneliğini silmek isteyebilirsiniz.

5. IoT Hub'ınızda **Event Grid**'i seçin.

6. Kaldırmak istediğiniz olay aboneliğini seçin.

7. **Sil**’i seçin.

Azure portal bir Azure Cosmos DB hesabını kaldırmak için hesap adına sağ tıklayın ve **Hesabı Sil**' e tıklayın. [Azure Cosmos DB hesabı silme](https://docs.microsoft.com/azure/cosmos-db/manage-account)hakkında ayrıntılı yönergeler için bkz.

## <a name="next-steps"></a>Sonraki adımlar

* [Eylemleri tetiklemek için Event Grid kullanarak IoT Hub olaylarına yeniden davranıma](../iot-hub/iot-hub-event-grid.md) hakkında daha fazla bilgi edinin

* [IoT Hub olaylar öğreticisini deneyin](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Event Grid](../event-grid/overview.md) ile yapabileceğiniz diğer şeyler hakkında bilgi edinin