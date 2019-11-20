---
title: Azure IoT Central özel Analize genişletin | Microsoft Docs
description: Çözüm geliştiricisi olarak, bir IoT Central uygulamasını özel analiz ve görselleştirmeler yapmak üzere yapılandırın. Bu çözüm Azure Databricks kullanır.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 618216208b61051d5446f96fb5b28a451b188c35
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954116"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure Databricks kullanarak Azure IoT Central özel analiz ile genişletme

Bu nasıl yapılır kılavuzunda, çözüm geliştiricisi olarak, IoT Central uygulamanızın özel analizler ve görselleştirmeler ile nasıl genişletileceği gösterilmektedir. Örnek, IoT Central telemetri akışını analiz etmek ve [kutu çizimleri](https://wikipedia.org/wiki/Box_plot)gibi görselleştirmeler oluşturmak için bir [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) çalışma alanı kullanır.

Bu nasıl yapılır Kılavuzu, [yerleşik analiz araçlarıyla](howto-create-analytics.md)daha önce yapabilecekleri IoT Central nasıl uzatılamayacak hakkında sizi gösterir.

Bu nasıl yapılır kılavuzunda şunları yapmayı öğreneceksiniz:

* *Sürekli veri dışa aktarma*kullanarak bir IoT Central uygulamasından Telemetriyi akışla.
* Cihaz telemetrisini analiz etmek ve çizmek için bir Azure Databricks ortamı oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

### <a name="iot-central-application"></a>IoT Central uygulaması

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde aşağıdaki ayarlarla bir IoT Central uygulaması oluşturun:

| Ayar | Değer |
| ------- | ----- |
| Ödeme planı | Kullandıkça Öde |
| Uygulama şablonu | Contoso Örneği |
| Uygulama adı | Varsayılanı kabul edin veya kendi adınızı seçin |
| URL | Varsayılanı kabul edin veya kendi benzersiz URL ön ekini seçin |
| Dizin | Azure Active Directory kiracınız |
| Azure aboneliği | Azure aboneliğiniz |
| Bölge | Doğu ABD |

Bu makaledeki örnekler ve ekran görüntüleri **Doğu ABD** bölgesini kullanır. Size yakın bir konum seçin ve tüm kaynaklarınızı aynı bölgede oluşturduğunuzdan emin olun.

### <a name="resource-group"></a>Kaynak grubu

Oluşturduğunuz diğer kaynakları içerecek şekilde **IoTCentralAnalysis** adlı [bir kaynak grubu oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.ResourceGroup) kullanın. Azure kaynaklarınızı IoT Central uygulamanızla aynı konumda oluşturun.

### <a name="event-hubs-namespace"></a>Event Hubs ad alanı

Aşağıdaki ayarlarla [bir Event Hubs ad alanı oluşturmak için Azure Portal](https://portal.azure.com/#create/Microsoft.EventHub) kullanın:

| Ayar | Değer |
| ------- | ----- |
| Adı    | Ad alanı adınızı seçin |
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

![IoT Central analiz kaynak grubu](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Bir IoT Central uygulamasını bir olay hub 'ına sürekli olarak telemetri dışarı aktarmak için yapılandırabilirsiniz. Bu bölümde, IoT Central uygulamanızdan telemetri almak için bir olay hub 'ı oluşturursunuz. Olay Hub 'ı işleme için Stream Analytics işinize telemetri sunar.

1. Azure portal, Event Hubs ad alanına gidin ve **+ Event hub ' ı**seçin.
1. Olay Hub 'ınızı **centralexport**olarak adlandırın ve **Oluştur**' u seçin.
1. Ad uzayındaki Olay Hub 'ları listesinde **centralexport**' yi seçin. Ardından **paylaşılan erişim ilkeleri**' ni seçin.
1. **+ Ekle** öğesini seçin. **Dinleme** talebini **dinle** adlı bir ilke oluşturun.
1. İlke hazır olduğunda, listeden seçin ve ardından **bağlantı dizesinin birincil anahtar** değerini kopyalayın.
1. Bu bağlantı dizesini not alın, daha sonra Databricks not defterinizi Olay Hub 'ından okumak üzere yapılandırdığınızda bu bağlantıyı kullanın.

Event Hubs ad alanınız aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![Event Hubs ad alanı](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>IoT Central dışarı aktarmayı yapılandırma

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesinde, contoso şablonundan oluşturduğunuz IoT Central uygulamasına gidin. Bu bölümde, uygulamayı sanal cihazınızdan, Olay Hub 'ınıza Telemetriyi akışa almak üzere yapılandırırsınız. Dışarı aktarmayı yapılandırmak için:

1. **Sürekli veri dışa aktarma** sayfasına gidin ve **+ Yeni**' yi ve ardından **Azure Event Hubs**' yi seçin.
1. Dışarı aktarmayı yapılandırmak için aşağıdaki ayarları kullanın ve **Kaydet**' i seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Görünen Ad | Event Hubs dışarı aktar |
    | Etkin | Açık |
    | Event Hubs ad alanı | Event Hubs ad alanı adınız |
    | Olay hub'ı | centralexport |
    | Ölçümler | Açık |
    | Cihazlar | Kapalı |
    | Cihaz şablonları | Kapalı |

![Sürekli veri dışa aktarma yapılandırması](media/howto-create-custom-analytics/cde-configuration.png)

Devam etmeden önce dışa aktarma durumunun **çalışmaya** bitmesini bekleyin.

## <a name="configure-databricks-workspace"></a>Databricks çalışma alanını yapılandırma

Azure portal, Azure Databricks hizmetinize gidin ve **çalışma alanını Başlat**' ı seçin. Tarayıcınızda yeni bir sekme açılır ve çalışma alanınızda oturum açar.

### <a name="create-a-cluster"></a>Küme oluşturma

**Azure Databricks** sayfasında, ortak görevler listesi altında **Yeni küme**' ı seçin.

Kümenizi oluşturmak için aşağıdaki tablodaki bilgileri kullanın:

| Ayar | Değer |
| ------- | ----- |
| Küme Adı | centralanalysis |
| Küme modu | Standart |
| Databricks Runtime sürümü | 5,3 (Scala 2,11, Spark 2.4.0) |
| Python sürümü | 3 |
| Otomatik Ölçeklendirmeyi Etkinleştirme | Hayır |
| İşlem yapılmadan dakika sonra Sonlandır | 30 |
| Çalışan türü | Standard_DS3_v2 |
| Çalışanlarınız | 1 |
| Sürücü türü | Çalışan ile aynı |

Bir küme oluşturmak birkaç dakika sürebilir, devam etmeden önce küme oluşturma işleminin tamamlanmasını bekleyin.

### <a name="install-libraries"></a>Kitaplıkları yükler

**Kümeler** sayfasında, küme durumu **çalışmaya**kadar bekleyin.

Aşağıdaki adımlarda, örnek ihtiyaçlarınızı kümeye aktarma işlemi gösterilmektedir:

1. **Kümeler** sayfasında, **centralanalysis** etkileşimli kümesinin durumunun **çalışıp çalışmadığını**bekleyin.

1. Kümeyi seçin ve ardından **Kitaplıklar** sekmesini seçin.

1. **Kitaplıklar** sekmesinde **Yeni yüklensin**' i seçin.

1. **Kitaplığı yükler** sayfasında, kitaplık kaynağı olarak **Maven** ' ı seçin.

1. **Koordinatlar** metin kutusuna şu değeri girin: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Kitaplığı kümeye yüklemek için **yüklemeyi** seçin.

1. Kitaplık durumu şimdi **yüklendi**:

    ![Kitaplık yüklendi](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Databricks Not defterini içeri aktarma

IoT Central telemetrinizi analiz etmek ve görselleştirmek için Python kodunu içeren bir Databricks Not defteri 'ni içeri aktarmak için aşağıdaki adımları kullanın:

1. Databricks ortamınızdaki **çalışma alanı** sayfasına gidin. Hesap adınızın yanındaki açılan menüyü seçin ve ardından **Içeri aktar**' ı seçin.

1. Bir URL 'den içeri aktarmayı seçin ve şu adresi girin: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Not defterini içeri aktarmak için **Içeri aktar**' ı seçin.

1. İçeri aktarılan Not defterini görüntülemek için **çalışma alanını** seçin:

    ![İçeri aktarılan Not defteri](media/howto-create-custom-analytics/import-notebook.png)

1. Daha önce kaydettiğiniz Event Hubs bağlantı dizesini eklemek için ilk Python hücresindeki kodu düzenleyin:

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

![Düzleştirilmiş telemetri çizimi](media/howto-create-custom-analytics/telemetry-plot.png)

Not defterinde grafiğin boyutunu değiştirebilirsiniz.

### <a name="view-box-plots"></a>Görünüm kutusu çizimleri

Not defteri 'nde, [kutu çizimleri](https://en.wikipedia.org/wiki/Box_plot)görmek için aşağı kaydırın. Kutu çizimleri, statik verileri temel alır, böylece onları güncelleştirmek için hücreyi yeniden çalıştırmanız gerekir:

![Kutu çizimleri](media/howto-create-custom-analytics/box-plots.png)

Not defterindeki çizimleri yeniden boyutlandırabilirsiniz.

## <a name="tidy-up"></a>Tidy yukarı

Bu nasıl yapılır ve gereksiz maliyetlerin önüne geçmek için Azure portal **IoTCentralAnalysis** kaynak grubunu silin.

Uygulama içindeki **Yönetim** sayfasından IoT Central uygulamasını silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır kılavuzunda şunları öğrenirsiniz:

* *Sürekli veri dışa aktarma*kullanarak bir IoT Central uygulamasından Telemetriyi akışla.
* Telemetri verilerini analiz etmek ve çizmek için bir Azure Databricks ortamı oluşturun.

Artık özel analizler oluşturmayı bildiğinize göre, önerilen sonraki adım [Azure IoT Central verilerinizi Power BI panosunda görselleştirmeyi ve çözümlemeyi](howto-connect-powerbi.md)öğrenmektir.
