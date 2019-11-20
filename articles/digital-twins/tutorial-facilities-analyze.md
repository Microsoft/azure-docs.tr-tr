---
title: 'Öğretici: Azure Digital TWINS kurulumundan olayları çözümleme'
description: Bu öğreticideki adımları kullanarak, Azure dijital TWINS boşluklarınızın Azure Time Series Insights ile olayları görselleştirmeyi ve çözümlemeyi öğrenin.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 96238da73a0bf6816635a71d13ea2ae6762d1955
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170398"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Öğretici: Time Series Insights kullanarak Azure dijital TWINS 'deki olayları görselleştirin ve çözümleyin

Azure dijital TWINS örneğinizi dağıttıktan ve belirli koşulları izlemek için özel bir işlev uyguladıktan sonra, eğilimleri ve anormallikleri aramak için, boşluklardan gelen olayları ve verileri görselleştirebilirsiniz.

[İlk öğreticide](tutorial-facilities-setup.md), bir sanal binasının uzamsal grafiğini, hareket, karbon ve sıcaklık algılayıcıları içeren bir odada yapılandırmış olursunuz. [İkinci öğreticide](tutorial-facilities-udf.md) grafınızı ve bir kullanıcı tanımlı işlev sağladınız. İşlevi bu algılayıcı değerlerini izler ve doğru koşullara ilişkin bildirimleri tetikler. Diğer bir deyişle, oda boştur ve sıcaklık ve karbon dioksit düzeyleri normaldir.

Bu öğretici, Azure dijital TWINS kurulumundan gelen bildirimleri ve verileri Azure Time Series Insights ile nasıl tümleştirebileceğinizi gösterir. Daha sonra sensör değerlerini zaman içinde görselleştirebilirsiniz. En fazla kullanımı elde eden ve günün en yoğun zamanlarında yer alan eğilimler için arama yapabilirsiniz. Ayrıca, hangi odaların stuffier ve hotetmesinin yanı sıra binadaki bir alanın, hatalı hava düzeyi olduğunu belirten sürekli yüksek sıcaklık değerleri gönderip göndermediğini de tespit edebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Event Hubs kullanarak veri akışı yapın.
> * Time Series Insights ile çözümleyin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide Azure Digital Twins kurulumunu [yapılandırmış](tutorial-facilities-setup.md) ve [sağlamış](tutorial-facilities-udf.md) olduğunuz kabul edilmektedir. Devam etmeden önce aşağıdakilere sahip olduğunuzdan emin olun:

- Bir [Azure hesabı](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Çalışan bir Digital Twins örneği.
- Çalışma makinenize indirilmiş ve ayıklanmış [Digital Twins C# örnekleri](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- Örneği çalıştırmak için geliştirme makinenizde [sürüm 2.1.403 veya sonraki bir sürümü .NET Core SDK](https://www.microsoft.com/net/download) . Doğru sürümün yüklendiğini doğrulamak için `dotnet --version` çalıştırın.

> [!TIP]
> Yeni bir örnek sağlıyorsanız benzersiz bir dijital TWINS örnek adı kullanın.

## <a name="stream-data-by-using-event-hubs"></a>Event Hubs kullanarak veri akışı

Verilerinizi akışa almak üzere bir işlem hattı oluşturmak için [Event Hubs](../event-hubs/event-hubs-about.md) hizmetini kullanabilirsiniz. Bu bölümde, Azure dijital TWINS ve Time Series Insights örnekleri arasında bağlayıcı olarak olay hub 'ınızı nasıl oluşturacağınız gösterilmektedir.

### <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **Kaynak oluştur**'u seçin.

1. **Event Hubs** araması yapın ve sonuçlardan seçin. **Oluştur**'u seçin.

    [![Event Hubs ad alanı oluşturma](./media/tutorial-facilities-analyze/create-event-hubs.png)](./media/tutorial-facilities-analyze/create-event-hubs.png#lightbox)

1. Event Hubs ad alanınız için bir **ad** girin. **Fiyatlandırma katmanı**, **aboneliğiniz**, dijital TWINS örneğiniz için kullandığınız **kaynak grubu** ve **konum**için **Standart** ' ı seçin. **Oluştur**'u seçin.

1. Event Hubs ad alanı dağıtımı ' nda **genel bakış** bölmesini seçin, sonra **Kaynağa Git**' i seçin.

    [Dağıtımdan sonra Event Hubs ad alanını ![](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. Event Hubs ad alanına **genel bakış** bölmesinde, üstteki **Olay Hub** 'ı düğmesini seçin.
    [![Olay Hub 'ı düğmesi](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. Olay Hub 'ınız için bir **ad** girin ve **Oluştur**' u seçin.

   Olay Hub 'ı dağıtıldıktan sonra, **etkin** durumdaki Event Hubs ad alanının **Event Hubs** bölmesinde görünür. **Genel bakış** bölmesini açmak için bu olay hub 'ını seçin.

1. Üstteki **Tüketici grubu** düğmesini seçin ve Tüketici grubu için **tsıevents** gibi bir ad girin. **Oluştur**'u seçin.

    [Olay Hub 'ı Tüketici grubu ![](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Tüketici grubu oluşturulduktan sonra, Olay Hub 'ının **genel bakış** bölmesinin altındaki listede görüntülenir.

1. Olay Hub 'ınız için **paylaşılan erişim ilkeleri** bölmesini açın ve **Ekle** düğmesini seçin. İlke adı olarak **ManageSend** girin, tüm onay kutularının seçili olduğundan emin olun ve **Oluştur**' u seçin.

    [![Olay Hub 'ı bağlantı dizeleri](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Ad alanınız yerine olay hub örneğiniz için bir SAS Ilkesi oluşturmadiğinizi doğrulayın.

1. Oluşturduğunuz **ManageSend** ilkesini açın ve **Bağlantı dizesi--birincil anahtar** ile **Bağlantı dizesi--ikincil anahtar** değerlerini geçici bir dosyaya kopyalayın. Sonraki bölümde Olay Hub 'ı için bir uç nokta oluşturmak üzere bu değerlere ihtiyacınız olacak.

### <a name="create-an-endpoint-for-the-event-hub"></a>Olay Hub 'ı için bir uç nokta oluşturma

1. Komut penceresinde, Azure Digital TWINS örneğinin **Occupancy-quickstart\src** klasöründe olduğunuzdan emin olun.

1. **Actions\createEndpoints.YAML** dosyasını Düzenleyicinizde açın. İçeriğini aşağıdakilerle değiştirin:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Yer tutucuları `Primary_connection_string_for_your_event_hub`, Olay Hub 'ı için **birincil anahtar olan bağlantı dizesinin** değeri ile değiştirin. Bu bağlantı dizesinin biçiminin aşağıdaki gibi olduğundan emin olun:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Yer tutucuları `Secondary_connection_string_for_your_event_hub`, Olay Hub 'ı için **İkincil anahtar olan bağlantı dizesinin** değeri ile değiştirin. Bu bağlantı dizesinin biçiminin aşağıdaki gibi olduğundan emin olun: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Yer tutucuları `Name_of_your_Event_Hub`, Olay Hub 'ınızın adıyla değiştirin.

    > [!IMPORTANT]
    > Değerleri girerken tırnak işaretlerini dahil etmeyin. YAML dosyasındaki iki nokta üst üsteden sonra en az bir boşluk karakteri olduğundan emin olun. [Bu araç](https://onlineyamltools.com/validate-yaml)gibi herhangi bir çevrimiçi YAML doğrulayıcısı kullanarak YAML dosya içeriklerinizi da doğrulayabilirsiniz.

1. Dosyayı kaydedin ve kapatın. Komut penceresinde aşağıdaki komutu çalıştırın ve istendiğinde Azure hesabınızda oturum açın.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Olay Hub 'ınız için iki uç nokta oluşturur.

   [Event Hubs için ![uç noktaları](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Time Series Insights ile analiz gerçekleştirme

1. [Azure Portal](https://portal.azure.com)sol bölmesinde **kaynak oluştur**' u seçin. 

1. **Time Series Insights** genel KULLANıLABILIRLIK (GA) kaynağını arayın ve seçin. **Oluştur**'u seçin.

1. Time Series Insights örneğiniz için bir **Ad** girin ve **Abonelik** girişinizi seçin. Dijital TWINS örneğiniz için kullandığınız **kaynak grubunu** ve **konumunuzu**seçin. **İleri ' yi seçin: olay kaynağı** düğmesi veya **olay kaynağı** sekmesi.

    [Time Series Insights örneği oluşturmak için ![seçimleri](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. **Olay kaynağı** sekmesinde, bir **ad**girin, **kaynak türü**olarak **Olay Hub** 'ı seçin ve diğer değerlerin doğru seçildiğinden emin olun. **Olay Hub 'ı erişim ilkesi adı**için **ManageSend** ' ı seçin ve ardından **Olay Hub 'ı Tüketici grubu**için önceki bölümde oluşturduğunuz tüketici grubunu seçin. **İncele ve oluştur**’u seçin.

    [Olay kaynağı oluşturmak için ![seçimleri](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. **Gözden geçir + oluştur** bölmesinde, girdiğiniz bilgileri gözden geçirin ve **Oluştur**' u seçin.

1. Dağıtım bölmesinde, az önce oluşturduğunuz Time Series Insights kaynağını seçin. Time Series Insights ortamınız için **genel bakış** bölmesini açar.

1. Üstteki **ortama git** düğmesini seçin. Veri erişim uyarısı alırsanız, Time Series Insights örneğiniz için **veri erişim ilkeleri** bölmesini açın, **Ekle**' yi seçin, rol olarak **katkıda bulunan** ' i seçin ve uygun kullanıcıyı seçin.

1. **Ortama git** düğmesi [Time Series Insights Gezginini](../time-series-insights/time-series-insights-explorer.md)açar. Herhangi bir olayı göstermez, dijital TWINS örnekinizdeki **cihaz bağlantısı** projesine göz atarak cihaz olaylarının benzetimini yapın ve `dotnet run`çalıştırın.

1. Birkaç benzetimli olay oluşturulduktan sonra, Time Series Insights Explorer 'a geri dönün ve üstteki Yenile düğmesini seçin. Sanal algılayıcı verileriniz için oluşturulan analitik grafikleri görmeniz gerekir. 

    [Time Series Insights Gezgininde grafik ![](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. Time Series Insights Gezgini 'nde, odalarınızdan, sensörlerden ve diğer kaynaklardaki farklı olaylar ve veriler için grafikler ve heyomaps oluşturabilirsiniz. Sol tarafta, kendi görselleştirmelerinizi oluşturmak için **Ölçü** **ve aşağı** açılan kutuları kullanın. 

   Örneğin, sensörlerinizin her biri için bir ısı haritasını oluşturmak için **Ölçü** ve **digitaltwıns-sensorhardwareıd** **için** **olayları** seçin. Isı haritasını aşağıdaki görüntüye benzer olacaktır:

   [Time Series Insights Gezgininde ![heatmap](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu noktanın ötesinde Azure dijital TWINS 'in araştırmalarını durdurmak istiyorsanız bu öğreticide oluşturulan kaynakları silebilirsiniz:

1. [Azure Portal](https://portal.azure.com)sol menüden **tüm kaynaklar**' ı seçin, dijital TWINS kaynak grubunuzu seçin ve **Sil**' i seçin.

    > [!TIP]
    > Dijital İkizlerini örneğinizin silme sorun olduysa, bir hizmet güncelleştirmesi düzeltme alındı. Örneğiniz silme yeniden deneyin.

2. Gerekirse, iş makinenizdeki örnek uygulamaları silin.

## <a name="next-steps"></a>Sonraki adımlar

Azure dijital TWINS 'te uzamsal zeka grafikleri ve nesne modelleri hakkında daha fazla bilgi edinmek için sonraki makaleye gidin.

> [!div class="nextstepaction"]
> [Digital Twins nesne modellerini ve uzamsal zeka grafını anlama](concepts-objectmodel-spatialgraph.md)
