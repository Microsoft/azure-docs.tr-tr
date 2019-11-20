---
title: Azure IoT Central bir durum verileri önceliklendirme panosu oluşturun | Microsoft Docs
description: Azure IoT Central uygulama şablonlarını kullanarak bir durum verileri önceliklendirme panosu oluşturmayı öğrenin.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 40e850bcbd177b15c91e57ec369c6b04963ffb84
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132271"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Öğretici: Power BI sağlayıcı panosu oluşturma

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Sürekli hasta izleme Çözümünüzü oluştururken, hasta verilerini görselleştirmek için bir hastane ekibine yönelik bir pano oluşturmak isteyebilirsiniz. Bu öğretici, IoT Central sürekli hasta izleme uygulama şablonınızdan gerçek zamanlı bir akış panosu Power BI oluşturma adımlarında size yol gösterecektir.

>[!div class="mx-imgBorder"]
>![Pano GIF](media/dashboard-gif-3.gif)

Temel mimari bu yapıyı izler:

>[!div class="mx-imgBorder"] 
>![sağlayıcısı önceliklendirme panosu](media/dashboard-architecture.png)

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

* Azure [Event Hubs ad alanı ve Olay Hub 'ı](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* Olay Hub 'ınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı Azure Event Hubs tetikleyicisiyle başlatmak için [boş bir mantıksal uygulama](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)gerekir.

* Power BI hizmeti hesabı. Henüz bir tane yoksa, [Power BI hizmeti için ücretsiz bir deneme hesabı oluşturabilirsiniz](https://app.powerbi.com/). Daha önce Power BI kullanmadıysanız, [Power BI kullanmaya başlama konusunda](https://docs.microsoft.com/power-bi/service-get-started)yararlı olabilir.

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Azure Event Hubs sürekli veri dışarı aktarma ayarlama
İlk olarak Azure IoT Central uygulama şablonınızdan, aboneliğinizdeki Azure Olay Hub 'ına sürekli bir veri dışarı aktarma ayarlamanız gerekir. Bu işlemi, [Event Hubs dışarı aktarmak](https://docs.microsoft.com/azure/iot-central/preview/howto-export-data)Için bu Azure IoT Central öğreticisindeki adımları izleyerek yapabilirsiniz. Bu öğreticinin amaçları doğrultusunda yalnızca Telemetriyi dışa aktarmanız gerekir.

## <a name="create-a-power-bi-streaming-dataset"></a>Power BI akış veri kümesi oluşturma

1. Power BI hesabınızda oturum açın.

2. Tercih ettiğiniz çalışma alanınızda, araç çubuğunun sağ üst köşesindeki **+ Oluştur** düğmesini seçerek yeni bir akış veri kümesi oluşturun. Panonuzda olmasını istediğiniz her hasta için ayrı bir veri kümesi oluşturmanız gerekir.

    >[!div class="mx-imgBorder"] 
    >![akış veri kümesi oluşturma](media/create-streaming-dataset.png)

3. Veri kümenizin kaynağı için **API** 'yi seçin.

4. Veri kümeniz için bir **ad** (örneğin, bir hasta adı) girin ve sonra akışınızdan değerleri doldurun. Sürekli hasta izleme uygulama şablonundaki sanal cihazlardan gelen değerlere göre aşağıdan bir örnek görebilirsiniz. Örnek iki hastaya sahiptir:

    * Akıllı Knee ayracından veri bulunan oyuncak Siller
    * Akıllı vitals düzeltme ekiyle veri içeren Yesenia Sanford

    >[!div class="mx-imgBorder"] 
    >![veri kümesi değerlerini girin](media/enter-dataset-values.png)

Power BI veri kümeleri hakkında daha fazla bilgi edinmek için bu belgeyi [Power BI gerçek zamanlı akışta](https://docs.microsoft.com/power-bi/service-real-time-streaming)okuyabilirsiniz.

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Mantıksal uygulamanızı Azure 'a bağlama Event Hubs
Mantıksal uygulamanızı Azure Event Hubs bağlamak için, [azure Event Hubs ve Azure Logic Apps ile olay gönderme](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action)konusunda bu belgede özetlenen yönergeleri izleyebilirsiniz. Önerilen bazı parametreler şunlardır:

|Parametre|Değer|
|---|---|
|İçerik türü|uygulama/json|
|Interval|3|
|Frequency|Saniye|

Bu adımın sonunda, mantıksal uygulama Tasarlayıcıınızın şöyle görünmesi gerekir:

>[!div class="mx-imgBorder"] 
>![Logic Apps Event Hubs Connect](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Mantıksal uygulamanızdan Power BI veri akışı
Sonraki adım, Olay Hub 'ından gelen verileri daha önce oluşturduğunuz Power BI veri kümelerine akışa almak için ayrıştıracaktır.

1. Bunu yapabilmeniz için önce, cihazınızdan Olay Hub 'ına gönderilen JSON yükünü anlamanız gerekir. Bu [örnek şemaya](https://docs.microsoft.com/azure/iot-central/preview/howto-export-data#telemetry) bakarak ve iletileri incelemek için [Service Bus Gezgini](https://github.com/paolosalvatori/ServiceBusExplorer) 'ni kullanarak bunu yapabilirsiniz. Sürekli hasta izleme uygulamaları kullanıyorsanız, iletileriniz şöyle görünür:

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

**Özelliklerinin**

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

2. JSON yüklerinizi incedığınıza göre, mantıksal uygulama tasarlayana geri dönüp **+ yeni adım**' ı seçin. **Başlangıç değişkenini** bir sonraki adımla bulun ve ekleyin ve aşağıdaki parametreleri girin:

    |Parametre|Değer|
    |---|---|
    |Ad|Arabirim adı|
    |Tür|Dize|

    **Kaydet**'e tıklayın. 

3. **String**olarak Type ile **Body** adlı başka bir değişken ekleyin. Mantıksal uygulamanız şu eylemleri ekledi:

    >[!div class="mx-imgBorder"]
    >değişkenleri başlatmak ![](media/initialize-string-variables.png)
    
4. **+ Yeni adım** ' ı seçin ve BIR **ayrıştırma JSON** eylemi ekleyin. **Özellikleri ayrıştırmak**için bunu yeniden adlandırın. Içerik için Olay Hub 'ından gelen **Özellikler** ' i seçin. En altta **şema oluşturmak için örnek yük kullan** ' ı seçin ve yukarıdaki özellikler bölümünden örnek yükü yapıştırın.

5. Sonra, **değişken ayarla** eylemini seçin ve **arabirim adı** değişkeninizi ayrıştırılmış JSON özelliklerindeki **ıothub-Interface-Name** ile güncelleştirin.

6. Sonraki eyleminiz olarak **bölünmüş** bir denetim ekleyin ve **arabirim adı** değişkenini on parametresi olarak seçin. Bunu, verileri doğru veri kümesine huni için kullanacaksınız.

7. Azure IoT Central uygulamanızda, akıllı Vintals düzeltme durumu verileri için arabirim adını ve **cihaz şablonları** görünümündeki akıllı Knee küme ayracı sistem durumu verilerini bulun. Her arabirim adı için **anahtar** denetimi için iki farklı durum oluşturun ve denetimi uygun şekilde yeniden adlandırın. **Sonlandırma** denetimini kullanmak istediğiniz durumu seçmek için varsayılan durumu ayarlayabilirsiniz.

    >[!div class="mx-imgBorder"] 
    >Bölünmüş denetim](media/split-by-interface.png) ![

8. **Akıllı vitals düzeltme** durumu IÇIN BIR **JSON JSON** eylemi ekleyin. Içerik için Olay Hub 'ından gelen **içerik** ' i seçin. Şemayı oluşturmak için yukarıdaki akıllı Vintals düzeltme ekinin örnek yüklerini kopyalayıp yapıştırın.

9. Bir **değişken kümesi** eylemi ekleyin ve **gövde** değişkenini adım 7 ' deki ayrıştırılmış JSON öğesinden gelen **gövdesiyle** güncelleştirin.

10. Bir sonraki eyleminiz olarak bir **koşul** denetimi ekleyin ve koşulu **gövde**, **içerir**, duyun olarak **ayarlayın.** Bu, Power BI veri kümesini doldurmadan önce akıllı Vinals düzeltme ekiyle gelen doğru veri kümesine sahip olduğunuzdan emin olur. 7-9 adımları şöyle görünür:

    >[!div class="mx-imgBorder"] 
    >![akıllı vitals koşul Ekle](media/smart-vitals-pbi.png)

11. Koşulun **gerçek** durumu için, **bir veri kümesine satır ekle** Power BI işlevine çağıran bir eylem ekleyin. Bunun için Power BI oturum açmanız gerekir. **Yanlış** durum, **sonlandırma** denetimini yeniden kullanabilir.

12. Uygun **çalışma alanını**, **veri kümesini**ve **tabloyu**seçin. Power BI ' de akış veri kümenizi oluştururken belirttiğiniz parametreleri Olay Hub 'ınızdan gelen ayrıştırılmış JSON değerlerine eşleyin. Doldurulmuş eylemleriniz şuna benzemelidir:

    >[!div class="mx-imgBorder"] 
    >![Power BI satır ekleme](media/add-rows-yesenia.png)

13. **Akıllı Knee küme ayracı** anahtar durumu için, içeriği ayrıştırmak için 7. adıma benzer bir **JSON JSON** eylemi ekleyin. Ardından Power BI içindeki oyuncak Sillerinizi veri kümenizi güncelleştirmek için **bir veri kümesine satır ekleyin** .

    >[!div class="mx-imgBorder"] 
    >![akıllı vitals koşul Ekle](media/knee-brace-pbi.png)

14. **Kaydet** ' e basın ve ardından mantıksal uygulamanızı çalıştırın.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Hasta için gerçek zamanlı bir pano oluşturun
Şimdi Power BI dönün ve **+ Oluştur** ' u seçerek yeni bir **Pano**oluşturun. Panonuza bir ad verin ve **Oluştur**' a basın.

Üst gezinti çubuğunda üç noktayı seçin ve ardından **+ kutucuk Ekle**' yi seçin.

>[!div class="mx-imgBorder"] 
>panoya kutucuk eklemek ![](media/add-tile.png)

Uygulamanızı eklemek ve özelleştirmek istediğiniz kutucuğun türünü seçin, ancak dilediğiniz gibi.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla kaynaklarınızı silin:

1. Azure portal, oluşturduğunuz Olay Hub 'ını ve Logic Apps kaynaklarını silebilirsiniz.

2. IoT Central uygulamanız için Yönetim sekmesine gidin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Sürekli hasta izleme mimarisi kılavuzunu](concept-continuous-patient-monitoring-architecture.md)gözden geçirin.
