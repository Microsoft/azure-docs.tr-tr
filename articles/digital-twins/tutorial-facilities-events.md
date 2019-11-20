---
title: 'Öğretici: Azure dijital bir TWINS alanından olayları yakalama'
description: Azure Digital Twins'i Logic Apps ile tümleştirerek alanlarınızdan bildirim almayı öğrenmek için bu öğreticideki adımları izleyin.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 545e1757f4f3669957d8f6755cdbd9a2b29513b6
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129167"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Öğretici: Logic Apps kullanarak Azure dijital TWINS boşluklarından bildirimler alma

Azure dijital TWINS örneğinizi dağıttıktan ve belirli koşulları izlemek için özel işlevler uyguladıktan sonra, izlenen koşullar gerçekleştiğinde Office yöneticinize e-posta ile bildirim gönderebilirsiniz.

[İlk öğreticide](tutorial-facilities-setup.md), bir sanal binasının uzamsal grafiğini yapılandırdınız. Binadaki bir oda, hareket, karbon dioksit ve sıcaklık için algılayıcılar içerir. [İkinci öğreticide](tutorial-facilities-udf.md), grafiğiniz ve Kullanıcı tanımlı bir işlevi bu algılayıcı değerlerini izleyip, Oda boş olduğunda bildirimleri tetikleyip ve sıcaklık ve karbon dioksit rahat bir aralıkta olduğunda tetiklersiniz. 

Bu öğreticide, oda uygun olduğunda e-posta göndermek için bu bildirimleri Azure Logic Apps ile tümleştirme adımları gösterilmektedir. Bir ofis yöneticisi bu bilgileri kullanarak çalışanların en verimli toplantı odasını ayırmalarına yardımcı olabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Olayları Azure Event Grid ile tümleştirin.
> * Olayları Logic Apps bildirin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide Azure Digital Twins kurulumunu [yapılandırmış](tutorial-facilities-setup.md) ve [sağlamış](tutorial-facilities-udf.md) olduğunuz kabul edilmektedir. Devam etmeden önce aşağıdakilere sahip olduğunuzdan emin olun:

- Bir [Azure hesabı](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Çalışan bir Digital Twins örneği.
- Çalışma makinenize indirilmiş ve ayıklanmış [Digital Twins C# örnekleri](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- Örneği çalıştırmak için geliştirme makinenizde [sürüm 2.1.403 veya sonraki bir sürümü .NET Core SDK](https://www.microsoft.com/net/download) . Doğru sürümün yüklendiğini doğrulamak için `dotnet --version` çalıştırın.
- Bildirim e-postaları göndermek için bir [Office 365](https://products.office.com/home) hesabı.

> [!TIP]
> Yeni bir örnek sağlıyorsanız benzersiz bir dijital TWINS örnek adı kullanın.

## <a name="integrate-events-with-event-grid"></a>Olayları Event Grid ile tümleştirme

Bu bölümde, Azure dijital TWINS örneğinden olayları toplamak için [Event Grid](../event-grid/overview.md) ayarlarsınız ve bunları Logic Apps gibi bir [olay işleyicisine](../event-grid/event-handlers.md) yönlendirebilirsiniz.

### <a name="create-an-event-grid-topic"></a>Olay kılavuzu oluşturma konusu

[Olay Kılavuzu konusu](../event-grid/concepts.md#topics) , Kullanıcı tanımlı işlev tarafından oluşturulan olayları yönlendirmek için bir arabirim sağlar. 

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **Kaynak oluştur**'u seçin. 

1. **Event Grid Konu Başlığı** araması yapın ve sonuçlardan seçin. **Oluştur**'u seçin.

1. Event Grid konu başlığınız için bir **Ad** girin ve **Abonelik** seçimi yapın. Dijital TWINS örneğiniz için kullandığınız veya oluşturduğunuz **kaynak grubunu** ve **konumunu**seçin. **Oluştur**'u seçin. 

    [![olay kılavuzu oluşturma konusu](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Kaynak grubunuzda olay kılavuzu konusuna göz atın, **genel bakış**' ı seçin ve **Konu uç noktasının** değerini geçici bir dosyaya kopyalayın. Sonraki bölümde bu URL 'ye ihtiyacınız olacak. 

1. **Erişim anahtarları**' nı seçin ve **anahtar 1** ile **anahtar 2** ' yi geçici bir dosyaya kopyalayın. Sonraki bölümde uç noktayı oluşturmak için bu değerlere ihtiyacınız olacak.

    [![Event Grid anahtarları](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Olay Kılavuzu konusu için bir uç nokta oluşturma

1. Komut penceresinde, dijital TWINS örneğinin **Occupancy-quickstart\src** klasöründe olduğunuzdan emin olun.

1. Visual Studio Code düzenleyicisinde **actions\createEndpoints.YAML** dosyasını açın. Aşağıdaki içeriğe sahip olduğundan emin olun:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Yer tutucu `<Primary connection string for your Event Grid>` **anahtar 1**değeri ile değiştirin.

1. Yer tutucu `<Secondary connection string for your Event Grid>`, **anahtar 2**değeri ile değiştirin.

1. **Yol** için yer tutucuyu olay Kılavuzu konusunun yoluyla değiştirin. **Konu uç noktası** URL 'sinden **https://** ve sondaki kaynak yollarını kaldırarak bu yolu alın. Şu biçime benzer görünmelidir: *EventGridAdı.Konumunuz.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Değerleri girerken tırnak işaretlerini dahil etmeyin. YAML dosyasındaki iki nokta üst üsteden sonra en az bir boşluk karakteri olduğundan emin olun. YAML dosya içeriklerinizi, [Bu araç](https://onlineyamltools.com/validate-yaml)gibi herhangi bir çevrimiçi YAML doğrulayıcısı kullanarak da doğrulayabilirsiniz.

1. Dosyayı kaydedin ve kapatın. Komut penceresinde aşağıdaki komutu çalıştırın ve istendiğinde oturum açın. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Bu komut Event Grid için uç nokta oluşturur. 

   [Event Grid için ![uç noktaları](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Olayları Logic Apps bildirme

Diğer hizmetlerden alınan olaylara yönelik otomatik görevler oluşturmak için [Azure Logic Apps](../logic-apps/logic-apps-overview.md) hizmetini kullanabilirsiniz. Bu bölümde, bir [olay Kılavuzu konusunun](../event-grid/overview.md)yardımıyla, uzamsal sensörlerden yönlendirilen olaylar için e-posta bildirimleri oluşturmak üzere Logic Apps ayarlarsınız.

1. [Azure Portal](https://portal.azure.com)sol bölmesinde **kaynak oluştur**' u seçin.

1. **Mantıksal Uygulama** araması yapın ve yeni bir kaynak oluşturun. **Oluştur**'u seçin.

1. Mantıksal uygulama kaynağınız için bir **ad** girin ve ardından **abonelik**, **kaynak grubu**ve **konum**' u seçin. **Oluştur**'u seçin.

    [![Logic Apps kaynağı oluşturma](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. Logic Apps kaynağınızı dağıtıldığında açın ve **mantıksal uygulama Tasarımcısı** bölmesini açın. 

1. **Bir Event Grid kaynak olayı gerçekleştiği zaman** ' ı seçin. İstendiğinde Azure hesabınızla kiracınızda oturum açın. İstenirse Event Grid kaynağınız için **erişime Izin ver** ' i seçin. Seçin **devam**.

1. **Kaynak olayı gerçekleştiğinde (Önizleme)** penceresinde: 
   
   a. Olay Kılavuzu konusunu oluşturmak için kullandığınız **aboneliği** seçin.

   b. **Kaynak türü**için **Microsoft. Eventgrid. konular** ' ı seçin.

   c. **Kaynak adı**için açılan kutudan Event Grid kaynağınızı seçin.

   [![Logic App Designer bölmesi](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. **Yeni adım** düğmesini seçin.

1. **Eylem seçin** penceresinde:

   a. **parse json** araması yapın ve **JSON Ayrıştır** eylemini seçin.

   b. **İçerik** alanında, **dinamik içerik** listesinden **gövde** ' yi seçin.

   c. **Şema oluşturmak için örnek yük kullanma** öğesini seçin. Aşağıdaki JSON yükünü yapıştırın ve **bitti**' yi seçin.

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    Bu yükte kurgusal değerler bulunur. Logic Apps bir *şema*oluşturmak için bu örnek yükü kullanır.

    [Event Grid için JSON penceresini ![Logic Apps ayrıştırma](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. **Yeni adım** düğmesini seçin.

1. **Eylem seçin** penceresinde:

   a. **Eylemler** listesinden **Denetim > koşulu** veya arama **koşulu** ' nı seçin. 

   b. İlk **değer seçin** metin kutusunda, **JSON 'u Ayrıştır** penceresi Için **dinamik içerik** listesinden **EventType** öğesini seçin.

   c. İkinci **bir değer seçin** metin kutusu içinde `UdfCustom`girin.

   [Seçili koşulları ![](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. **Eğer true ise** penceresinde:

   a. **Eylem Ekle**' yi seçin ve **Office 365 Outlook**' u seçin.

   b. **Eylemler** listesinden **e-posta gönder (v2)** seçeneğini belirleyin. **Oturum aç** ' ı seçin ve e-posta hesabı kimlik bilgilerinizi kullanın. İstenirse **erişime Izin ver** ' i seçin.

   c. **Alıcı** kutusuna bildirimlerin gönderilmesi için e-posta adresinizi yazın. **Konu**alanında, **daha düşük hava kalitesi Için metin olarak dijital TWINS bildirimi**yazın. Ardından, **JSON ayrıştırma**için **dinamik Içerik** listesinden **topologyobjectıd** ' yi seçin.

   d. Aynı penceredeki **gövde** altında şuna benzer bir metin girin: **bir odada zayıf hava kalitesi ve sıcaklığın ayarlanması gerekir**. **Dinamik içerik** listesinden öğeleri kullanarak ayrıntılı bir şekilde çekinmeyin.

   [![Logic Apps "e-posta gönderme" seçimleri](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. **Mantıksal uygulama Tasarımcısı** bölmesinin en üstündeki **Kaydet** düğmesini seçin.

1. Bir komut penceresinde dijital TWINS örneğinin **cihaz bağlantısı** klasörüne gidip `dotnet run`çalıştırarak algılayıcı verilerinin benzetimini yaptığınızdan emin olun.

Birkaç dakika içinde, bu Logic Apps kaynağından e-posta bildirimleri almaya başlamanız gerekir. 

   [e-posta bildirimi ![](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Bu e-postaları almayı durdurmak için portalda Logic Apps kaynağına gidin ve **genel bakış** bölmesini seçin. Seçin **devre dışı**.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu noktada Azure dijital İkizlerini keşfetmeye durdurmak istiyorsanız, bu öğreticide oluşturulan kaynakları silmek çekinmeyin:

1. Sol menüden [Azure portalında](https://portal.azure.com)seçin **tüm kaynakları**dijital İkizlerini kaynak grubunuzu seçin ve seçin **Sil**.

    > [!TIP]
    > Dijital İkizlerini örneğinizin silme sorun olduysa, bir hizmet güncelleştirmesi düzeltme alındı. Örneğiniz silme yeniden deneyin.

2. Gerekirse, iş makinenizdeki örnek uygulamaları silin.

## <a name="next-steps"></a>Sonraki adımlar

Sensör verilerinizi görselleştirmeyi, eğilimleri çözümlemeyi ve anormallikleri belirlemek için sonraki Öğreticiye gidin:

> [!div class="nextstepaction"]
> [Öğretici: Time Series Insights'ı kullanarak Azure Digital Twins alanlarınızdan gelen olayları görselleştirme ve analiz etme](tutorial-facilities-analyze.md)

Ayrıca, Azure dijital TWINS 'te uzamsal zeka grafikleri ve nesne modelleri hakkında daha fazla bilgi edinebilirsiniz:

> [!div class="nextstepaction"]
> [Digital Twins nesne modellerini ve uzamsal zeka grafını anlama](concepts-objectmodel-spatialgraph.md)
