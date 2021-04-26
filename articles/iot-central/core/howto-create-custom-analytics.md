---
title: Azure IoT Central özel Analize genişletin | Microsoft Docs
description: Çözüm geliştiricisi olarak, bir IoT Central uygulamasını özel analiz ve görselleştirmeler yapmak üzere yapılandırın. Bu çözüm Azure Databricks kullanır.
author: philmea
ms.author: philmea
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9ada9947b217944d9aec9f785f4716bfe43315b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872776"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure Databricks kullanarak özel analizlerle Azure IoT Central’ın kapsamını genişletme

Bu nasıl yapılır kılavuzunda, çözüm geliştiricisi olarak, IoT Central uygulamanızın özel analizler ve görselleştirmeler ile nasıl genişletileceği gösterilmektedir. Örnek, IoT Central telemetri akışını analiz etmek ve [kutu çizimleri](https://wikipedia.org/wiki/Box_plot)gibi görselleştirmeler oluşturmak için bir [Azure Databricks](/azure/azure-databricks/) çalışma alanı kullanır.  

Bu nasıl yapılır Kılavuzu, [yerleşik analiz araçlarıyla](./howto-create-custom-analytics.md)daha önce yapabilecekleri IoT Central nasıl uzatılamayacak hakkında sizi gösterir.

Bu nasıl yapılır kılavuzunda şunları yapmayı öğreneceksiniz:

* *Sürekli veri dışa aktarma* kullanarak bir IoT Central uygulamasından Telemetriyi akışla.
* Cihaz telemetrisini analiz etmek ve çizmek için bir Azure Databricks ortamı oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

### <a name="iot-central-application"></a>IoT Central uygulaması

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde aşağıdaki ayarlarla bir IoT Central uygulaması oluşturun:

| Ayar | Değer |
| ------- | ----- |
| Fiyatlandırma planı | Standart |
| Uygulama şablonu | Mağaza içi analiz – koşul izleme |
| Uygulama adı | Varsayılanı kabul edin veya kendi adınızı seçin |
| URL | Varsayılanı kabul edin veya kendi benzersiz URL ön ekini seçin |
| Dizin | Azure Active Directory kiracınız |
| Azure aboneliği | Azure aboneliğiniz |
| Region | En yakın bölgeniz |

Bu makaledeki örnekler ve ekran görüntüleri **Birleşik Devletler** bölgesini kullanır. Size yakın bir konum seçin ve tüm kaynaklarınızı aynı bölgede oluşturduğunuzdan emin olun.

Bu uygulama şablonu telemetri gönderen iki sanal termostat cihaz içerir.

### <a name="resource-group"></a>Kaynak grubu

Oluşturduğunuz diğer kaynakları içerecek şekilde **IoTCentralAnalysis** adlı [bir kaynak grubu oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.ResourceGroup) kullanın. Azure kaynaklarınızı IoT Central uygulamanızla aynı konumda oluşturun.

### <a name="event-hubs-namespace"></a>Event Hubs ad alanı

Aşağıdaki ayarlarla [bir Event Hubs ad alanı oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.EventHub) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Ad    | Ad alanı adınızı seçin |
| Fiyatlandırma katmanı | Temel |
| Abonelik | Aboneliğiniz |
| Kaynak grubu | IoTCentralAnalysis |
| Konum | Doğu ABD |
| İşleme Birimleri | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks çalışma alanı

Aşağıdaki ayarlarla [Azure Databricks bir hizmet oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.Databricks) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Çalışma alanı adı    | Çalışma alanınızın adını seçin |
| Abonelik | Aboneliğiniz |
| Kaynak grubu | IoTCentralAnalysis |
| Konum | Doğu ABD |
| Fiyatlandırma Katmanı | Standart |

Gerekli kaynakları oluşturduğunuzda, **IoTCentralAnalysis** kaynak grubunuz aşağıdaki ekran görüntüsüne benzer şekilde görünür:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="IoT Central analiz kaynak grubunun görüntüsü.":::

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Bir IoT Central uygulamasını bir olay hub 'ına sürekli olarak telemetri dışarı aktarmak için yapılandırabilirsiniz. Bu bölümde, IoT Central uygulamanızdan telemetri almak için bir olay hub 'ı oluşturursunuz. Olay Hub 'ı işleme için Stream Analytics işinize telemetri sunar.

1. Azure portal, Event Hubs ad alanına gidin ve **+ Event hub ' ı** seçin.
1. Olay Hub 'ınızı **centralexport** olarak adlandırın.
1. Ad uzayındaki Olay Hub 'ları listesinde **centralexport**' yi seçin. Ardından **paylaşılan erişim ilkeleri**' ni seçin.
1. **+ Ekle**'yi seçin. **Gönderme** ve **dinleme** talepleri ile **sendlisten** adlı bir ilke oluşturun.
1. İlke hazır olduğunda, listeden seçin ve ardından **bağlantı dizesinin birincil anahtar** değerini kopyalayın.
1. Bu bağlantı dizesini not alın, daha sonra Databricks not defterinizi Olay Hub 'ından okumak üzere yapılandırdığınızda bu bağlantıyı kullanın.

Event Hubs ad alanınız aşağıdaki ekran görüntüsüne benzer şekilde görünür:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="Event Hubs ad alanının görüntüsü.":::

## <a name="configure-export-in-iot-central"></a>IoT Central dışarı aktarmayı yapılandırma

Bu bölümde, uygulamayı sanal cihazınızdan Olay Hub 'ınıza Telemetriyi akışa almak üzere yapılandırırsınız.

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde, daha önce oluşturduğunuz IoT Central uygulamasına gidin. Dışarı aktarmayı yapılandırmak için, önce bir hedef oluşturun:

1. **Veri dışa aktarma** sayfasına gidin ve **hedefler**' i seçin.
1. **+ Yeni hedef**' i seçin.
1. Bir hedef oluşturmak için aşağıdaki tablodaki değerleri kullanın:

    | Ayar | Değer |
    | ----- | ----- |
    | Hedef adı | Telemetri Olay Hub 'ı |
    | Hedef türü | Azure Event Hubs |
    | Bağlantı dizesi | Daha önce bir nota yaptığınız Olay Hub 'ı bağlantı dizesi |

    **Olay Hub 'ı** **centralexport** olarak gösterilir.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Veri dışa aktarma hedefini gösteren ekran görüntüsü.":::

1. **Kaydet**’i seçin.

Dışarı aktarma tanımını oluşturmak için:

1. **Veri dışa aktarma** sayfasına gidin ve **+ yeni dışarı aktar**' ı seçin.

1. Dışarı aktarmayı yapılandırmak için aşağıdaki tablodaki değerleri kullanın:

    | Ayar | Değer |
    | ------- | ----- |
    | Dışarı aktarma adı | Olay Hub 'ı dışarı aktarma |
    | Etkin | Açık |
    | Dışarı aktarılacak veri türü | Telemetri |
    | Hedefler | **+ Hedef**' i seçin ve ardından **telemetri Olay Hub 'ını** seçin |

1. **Kaydet**’i seçin.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Veri dışa aktarma tanımını gösteren ekran görüntüsü.":::

Devam etmeden önce dışarı aktarma durumunun **veri dışa aktarma** sayfasında **sağlıklı** olmasını bekleyin.

## <a name="configure-databricks-workspace"></a>Databricks çalışma alanını yapılandırma

Azure portal, Azure Databricks hizmetinize gidin ve **çalışma alanını Başlat**' ı seçin. Tarayıcınızda yeni bir sekme açılır ve çalışma alanınızda oturum açar.

### <a name="create-a-cluster"></a>Küme oluşturma

**Azure Databricks** sayfasında, ortak görevler listesi altında **Yeni küme**' ı seçin.

Kümenizi oluşturmak için aşağıdaki tablodaki bilgileri kullanın:

| Ayar | Değer |
| ------- | ----- |
| Küme Adı | centralanalysis |
| Küme modu | Standart |
| Databricks Runtime sürümü | 5,5 LTS (Scala 2,11, Spark 2.4.5) |
| Python sürümü | 3 |
| Otomatik ölçeklendirmeyi etkinleştir | No |
| İşlem yapılmadan dakika sonra Sonlandır | 30 |
| Çalışan türü | Standard_DS3_v2 |
| Çalışanlarınız | 1 |
| Sürücü türü | Çalışan ile aynı |

Bir küme oluşturmak birkaç dakika sürebilir, devam etmeden önce küme oluşturma işleminin tamamlanmasını bekleyin.

### <a name="install-libraries"></a>Kitaplıkları yükler

**Kümeler** sayfasında, küme durumu **çalışmaya** kadar bekleyin.

Aşağıdaki adımlarda, örnek ihtiyaçlarınızı kümeye aktarma işlemi gösterilmektedir:

1. **Kümeler** sayfasında, **centralanalysis** etkileşimli kümesinin durumunun **çalışıp çalışmadığını** bekleyin.

1. Kümeyi seçin ve ardından **Kitaplıklar** sekmesini seçin.

1. **Kitaplıklar** sekmesinde **Yeni yüklensin**' i seçin.

1. **Kitaplığı yükler** sayfasında, kitaplık kaynağı olarak **Maven** ' ı seçin.

1. **Koordinatlar** metin kutusuna aşağıdaki değeri girin:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Kitaplığı kümeye yüklemek için **yüklemeyi** seçin.

1. Kitaplık durumu şimdi **yüklendi**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Yüklü kitaplığın ekran görüntüsü.":::

### <a name="import-a-databricks-notebook"></a>Databricks Not defterini içeri aktarma

IoT Central telemetrinizi analiz etmek ve görselleştirmek için Python kodunu içeren bir Databricks Not defteri 'ni içeri aktarmak için aşağıdaki adımları kullanın:

1. Databricks ortamınızdaki **çalışma alanı** sayfasına gidin. Hesap adınızın yanındaki açılan menüyü seçin ve ardından **Içeri aktar**' ı seçin.

1. Bir URL 'den içeri aktarmayı seçin ve şu adresi girin: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Not defterini içeri aktarmak için **Içeri aktar**' ı seçin.

1. İçeri aktarılan Not defterini görüntülemek için **çalışma alanını** seçin:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Içeri aktarılan Not defteri 'nin ekran görüntüsü.":::

5. Daha önce kaydettiğiniz Event Hubs bağlantı dizesini eklemek için ilk Python hücresindeki kodu düzenleyin:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Analizi Çalıştır

Analizi çalıştırmak için, Not defterini kümeye eklemeniz gerekir:

1. **Ayrılmış** ' i seçin ve ardından **centralanalysis** kümesini seçin.
1. Küme çalışmıyorsa başlatın.
1. Not defterini başlatmak için Çalıştır düğmesini seçin.

Son hücrede bir hata görebilirsiniz. Bu durumda, önceki hücrelerin çalıştığını denetleyin, bazı verilerin depolama alanına yazılmasını bekleyip bir dakika bekleyin ve ardından son hücreyi yeniden çalıştırın.

### <a name="view-smoothed-data"></a>Düzleştirilmiş verileri görüntüleme

Not defteri 'nde, cihaz türüne göre geçen ortalama nem sayısını görmek için 14 hücresine kaydırın. Bu çizim, akış telemetri geldiğinde sürekli güncelleştirmeler:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Düzleştirilmiş telemetri grafiğinin ekran görüntüsü.":::

Not defterinde grafiğin boyutunu değiştirebilirsiniz.

### <a name="view-box-plots"></a>Görünüm kutusu çizimleri

Not defteri 'nde, [kutu çizimleri](https://en.wikipedia.org/wiki/Box_plot)görmek için aşağı kaydırın. Kutu çizimleri, statik verileri temel alır, böylece onları güncelleştirmek için hücreyi yeniden çalıştırmanız gerekir:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Kutu çizimleri ekran görüntüsü.":::

Not defterindeki çizimleri yeniden boyutlandırabilirsiniz.

## <a name="tidy-up"></a>Tidy yukarı

Bu nasıl yapılır ve gereksiz maliyetlerin önüne geçmek için Azure portal **IoTCentralAnalysis** kaynak grubunu silin.

Uygulama içindeki **Yönetim** sayfasından IoT Central uygulamasını silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır kılavuzunda şunları öğrenirsiniz:

* *Sürekli veri dışa aktarma* kullanarak bir IoT Central uygulamasından Telemetriyi akışla.
* Telemetri verilerini analiz etmek ve çizmek için bir Azure Databricks ortamı oluşturun.

Artık özel analizler oluşturmayı bildiğinize göre, önerilen sonraki adım [Azure IoT Central verilerinizi Power BI panosunda görselleştirmeyi ve çözümlemeyi](howto-connect-powerbi.md)öğrenmektir.
