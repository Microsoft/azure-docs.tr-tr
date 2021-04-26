---
title: Öğretici-Azure IoT Central bir durum verileri önceliklendirme panosu oluşturun | Microsoft Docs
description: Öğretici-Azure IoT Central uygulama şablonlarını kullanarak bir durum verileri önceliklendirme panosu oluşturmayı öğrenin.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: d227d934eedd31342ce419576fffe7cea17efb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748263"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Öğretici: Power BI sağlayıcı panosu oluşturma

Sürekli hasta izleme Çözümünüzü oluştururken, hasta verilerini görselleştirmek için bir hastane ekibine yönelik bir pano da oluşturabilirsiniz. Bu öğreticide, IoT Central sürekli hasta izleme uygulaması şablonundan gerçek zamanlı Power BI bir akış panosu oluşturmayı öğreneceksiniz. Kullanım örneği gerçek zamanlı verilere erişim gerektirmiyorsa, Basitleştirilmiş bir dağıtım işlemine sahip [IoT Central Power BI panosunu](../core/howto-connect-powerbi.md)kullanabilirsiniz. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="Pano GIF":::

Temel mimari bu yapıyı izler:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Sağlayıcı önceliklendirme panosu":::

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure IoT Central verileri Azure 'a aktarın Event Hubs
> * Power BI akış veri kümesi ayarlama
> * Mantıksal uygulamanızı Azure 'a bağlama Event Hubs
> * Mantıksal uygulamanızdan Power BI veri akışı
> * Hasta için gerçek zamanlı bir pano oluşturun


## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Azure IoT Central sürekli hasta izleme uygulaması şablonu. Henüz bir tane yoksa, [bir uygulama şablonu dağıtmak](overview-iot-central-healthcare.md)için adımları izleyebilirsiniz.

* Azure [Event Hubs ad alanı ve Olay Hub 'ı](../../event-hubs/event-hubs-create.md).

* Olay Hub 'ınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı Azure Event Hubs tetikleyicisiyle başlatmak için [boş bir mantıksal uygulama](../../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.

* Power BI hizmeti hesabı. Henüz bir tane yoksa, [Power BI hizmeti için ücretsiz bir deneme hesabı oluşturabilirsiniz](https://app.powerbi.com/). Daha önce Power BI kullanmadıysanız, [Power BI kullanmaya başlama konusunda](/power-bi/service-get-started)yararlı olabilir.


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Azure Event Hubs sürekli veri dışarı aktarma ayarlama
İlk olarak Azure IoT Central uygulama şablonınızdan, aboneliğinizdeki Azure Olay Hub 'ına sürekli bir veri dışarı aktarma ayarlamanız gerekir. Bu işlemi, [Event Hubs dışarı aktarmak](../core/howto-export-data.md)Için bu Azure IoT Central öğreticisindeki adımları izleyerek yapabilirsiniz. Bu öğreticinin amaçları doğrultusunda yalnızca Telemetriyi dışa aktarmanız gerekir.


## <a name="create-a-power-bi-streaming-dataset"></a>Power BI akış veri kümesi oluşturma

1. Power BI hesabınızda oturum açın.

1. Tercih ettiğiniz çalışma alanınızda, araç çubuğunun sağ üst köşesindeki **+ Oluştur** düğmesini seçerek yeni bir akış veri kümesi oluşturun. Panonuzda olmasını istediğiniz her hasta için ayrı bir veri kümesi oluşturmanız gerekir.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Akış veri kümesi oluşturma":::


1. Veri kümenizin kaynağı için **API** 'yi seçin.

1. Veri kümeniz için bir **ad** (örneğin, bir hasta adı) girin ve sonra akışınızdan değerleri doldurun. Sürekli hasta izleme uygulama şablonundaki sanal cihazlardan gelen değerlere göre aşağıdan bir örnek görebilirsiniz. Örnek iki hastaya sahiptir:

   * Akıllı Knee ayracından veriye sahip olan oyuncak Siller.
   * Akıllı Vintals düzeltme ekiyle veri içeren Yesenia Sanford.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Veri kümesi değerlerini girin":::

Power BI veri kümeleri hakkında daha fazla bilgi edinmek için bu belgeyi [Power BI gerçek zamanlı akışta](/power-bi/service-real-time-streaming)okuyabilirsiniz.


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Mantıksal uygulamanızı Azure 'a bağlama Event Hubs

Mantıksal uygulamanızı Azure Event Hubs bağlamak için, [azure Event Hubs ve Azure Logic Apps ile olay gönderme](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action)konusunda bu belgede özetlenen yönergeleri izleyebilirsiniz. Önerilen bazı parametreler şunlardır:

|Parametre|Değer|
|---|---|
|İçerik türü|uygulama/json|
|Aralık|3|
|Sıklık|Second|

Bu adımın sonunda, mantıksal uygulama Tasarlayıcıınızın şöyle görünmesi gerekir:

>[!div class="mx-imgBorder"] 
>![Logic Apps Event Hubs bağlanır](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Veri kümesi değerlerini girin":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Mantıksal uygulamanızdan Power BI veri akışı

Sonraki adım, Olay Hub 'ından gelen verileri daha önce oluşturduğunuz Power BI veri kümelerine akışa almak için ayrıştıracaktır.

Bunu yapabilmeniz için önce, cihazınızdan Olay Hub 'ına gönderilen JSON yükünü anlamanız gerekir. Bu [örnek şemaya](../core/howto-export-data.md#telemetry-format) bakarak ve iletileri incelemek için [Service Bus Gezgini](https://github.com/paolosalvatori/ServiceBusExplorer) 'ni kullanarak bunu yapabilirsiniz. Sürekli hasta izleme uygulamaları kullanıyorsanız, iletileriniz şöyle görünür:

**Akıllı vitals düzeltme eki telemetrisi**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Akıllı Knee küme ayracı telemetrisi**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Özellikler**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. JSON yüklerinizi incedığınıza göre, mantıksal uygulama tasarlayana geri dönüp **+ yeni adım**' ı seçin. **Başlangıç değişkenini** bir sonraki adımla bulun ve ekleyin ve aşağıdaki parametreleri girin:

   |Parametre|Değer|
   |---|---|
   |Ad|Arabirim adı|
   |Tür|Dize|

   **Kaydet**’i seçin. 

1. **String** olarak Type ile **Body** adlı başka bir değişken ekleyin. Mantıksal uygulamanız şu eylemleri ekledi:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Değişkenleri Başlat":::
    
1. **+ Yeni adım** ' ı seçin ve BIR **ayrıştırma JSON** eylemi ekleyin. **Özellikleri ayrıştırmak** için bunu yeniden adlandırın. Içerik için Olay Hub 'ından gelen **Özellikler** ' i seçin. En altta **şema oluşturmak için örnek yük kullan** ' ı seçin ve yukarıdaki özellikler bölümünden örnek yükü yapıştırın.

1. Sonra, **değişken ayarla** eylemini seçin ve **arabirim adı** değişkeninizi ayrıştırılmış JSON özelliklerindeki **ıothub-Interface-Name** ile güncelleştirin.

1. Sonraki eyleminiz olarak **bölünmüş** bir denetim ekleyin ve **arabirim adı** değişkenini on parametresi olarak seçin. Bunu, verileri doğru veri kümesine huni için kullanacaksınız.

1. Azure IoT Central uygulamanızda, akıllı Vintals düzeltme durumu verileri için arabirim adını ve **cihaz şablonları** görünümündeki akıllı Knee küme ayracı sistem durumu verilerini bulun. Her arabirim adı için **anahtar** denetimi için iki farklı durum oluşturun ve denetimi uygun şekilde yeniden adlandırın. **Sonlandırma** denetimini kullanmak istediğiniz durumu seçmek için varsayılan durumu ayarlayabilirsiniz.

   :::image type="content" source="media/split-by-interface.png" alt-text="Bölünmüş denetim":::

1. **Akıllı vitals düzeltme** durumu IÇIN BIR **JSON JSON** eylemi ekleyin. Içerik için Olay Hub 'ından gelen **içerik** ' i seçin. Şemayı oluşturmak için yukarıdaki akıllı Vintals düzeltme ekinin örnek yüklerini kopyalayıp yapıştırın.

1. Bir **değişken kümesi** eylemi ekleyin ve **gövde** değişkenini adım 7 ' deki ayrıştırılmış JSON öğesinden gelen **gövdesiyle** güncelleştirin.

1. Bir sonraki eyleminiz olarak bir **koşul** denetimi ekleyin ve koşulu **gövde**, **içerir**, duyun olarak **ayarlayın.** Bu, Power BI veri kümesini doldurmadan önce akıllı Vinals düzeltme ekiyle gelen doğru veri kümesine sahip olduğunuzdan emin olur. 7-9 adımları şöyle görünür:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Akıllı vitals koşul Ekle":::

1. Koşulun **gerçek** durumu için, **bir veri kümesine satır ekle** Power BI işlevine çağıran bir eylem ekleyin. Bunun için Power BI oturum açmanız gerekir. **Yanlış** durum, **sonlandırma** denetimini yeniden kullanabilir.

1. Uygun **çalışma alanını**, **veri kümesini** ve **tabloyu** seçin. Power BI ' de akış veri kümenizi oluştururken belirttiğiniz parametreleri Olay Hub 'ınızdan gelen ayrıştırılmış JSON değerlerine eşleyin. Doldurulmuş eylemleriniz şuna benzemelidir:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Power BI satır ekleme":::

1. **Akıllı Knee küme ayracı** anahtar durumu için, içeriği ayrıştırmak için 7. adıma benzer bir **JSON JSON** eylemi ekleyin. Ardından Power BI içindeki oyuncak Sillerinizi veri kümenizi güncelleştirmek için **bir veri kümesine satır ekleyin** .

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Veri kümelerine nasıl satır ekleneceğini gösteren ekran görüntüsü":::

1. **Kaydet** ' e basın ve ardından mantıksal uygulamanızı çalıştırın.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Hasta için gerçek zamanlı bir pano oluşturun

Şimdi Power BI dönün ve **+ Oluştur** ' u seçerek yeni bir **Pano** oluşturun. Panonuza bir ad verin ve **Oluştur**' a basın.

Üst gezinti çubuğunda üç noktayı seçin ve ardından **+ kutucuk Ekle**' yi seçin.

:::image type="content" source="media/add-tile.png" alt-text="Panoya kutucuk Ekle":::

Uygulamanızı eklemek ve özelleştirmek istediğiniz kutucuğun türünü seçin, ancak dilediğiniz gibi.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla kaynaklarınızı silin:

1. Azure portal, oluşturduğunuz Olay Hub 'ını ve Logic Apps kaynaklarını silebilirsiniz.

1. IoT Central uygulamanız için Yönetim sekmesine gidin ve **Sil**' i seçin.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sürekli hasta izleme Mimarisi Kılavuzu](concept-continuous-patient-monitoring-architecture.md)