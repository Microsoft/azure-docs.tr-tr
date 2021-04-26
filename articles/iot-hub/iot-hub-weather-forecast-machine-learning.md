---
title: IoT Hub verilerle Azure Machine Learning Studio (klasik) kullanarak hava durumu tahmini
description: IoT Hub 'ınızın bir sensörden topladığı sıcaklık ve nem verilerine bağlı olarak yağmur olma olasılığını tahmin etmek için Azure Machine Learning Studio (klasik) kullanın.
author: robinsh
manager: philmea
keywords: Hava durumu tahmin makinesi öğrenimi
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 455d78ed21403952046448dd4447b5ec54f77c00
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566988"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) IoT Hub 'ınızdaki algılayıcı verilerini kullanarak hava durumu tahmini

![Uçtan uca diyagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Makine öğrenimi, bilgisayarların, gelecekteki davranışları, sonuçları ve eğilimleri tahmin etmek için mevcut verilerden öğrenmesini sağlayan bir veri bilimi tekniğidir. Azure Machine Learning Studio (klasik), tahmine dayalı modelleri analiz çözümleri olarak hızlı bir şekilde oluşturmayı ve dağıtmayı mümkün kılan bir bulut tahmine dayalı analiz hizmetidir. Bu makalede, Azure IoT Hub 'ınızdan sıcaklık ve nem verilerini kullanarak hava durumu tahmini (yağmur şansı) için Azure Machine Learning Studio (klasik) kullanmayı öğreneceksiniz. Yağmur, hazırlanan Hava durumu tahmin modelinin çıktıdır. Model, sıcaklık ve nem oranına göre yağmur olasılığını tahmin etmek için geçmiş veriler üzerine kurulmuştur.

## <a name="prerequisites"></a>Önkoşullar

- [Raspberry PI Çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md) öğreticisini veya cihaz öğreticilerinin birini doldurun. Örneğin, [node.jsIle Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) 'ye gidebilir veya [Telemetriyi gönder](quickstart-send-telemetry-dotnet.md) hızlı başlangıçlarından birine gidebilirsiniz. Bu makaleler aşağıdaki gereksinimleri kapsar:
  - Etkin bir Azure aboneliği.
  - Aboneliğiniz kapsamındaki bir Azure IoT Hub 'ı.
  - Azure IoT Hub 'ınıza ileti gönderen bir istemci uygulaması.
- [Azure Machine Learning Studio (klasik)](https://studio.azureml.net/) hesap.
- Bir [Azure depolama hesabı](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)olan **genel amaçlı v2** hesabı tercih edilir, ancak Azure Blob depolamayı destekleyen tüm Azure depolama hesapları da çalışacaktır.

> [!Note]
> Bu makale Azure Stream Analytics ve diğer ücretli hizmetleri kullanır. Verilerin Azure bölgeleri arasında aktarılması gerektiğinde Azure Stream Analytics ek ücretler tahakkuk etmelidir. Bu nedenle, kaynak grubunuzun, IoT Hub ve Azure depolama hesabınızın yanı sıra bu öğreticide daha sonra eklenen Machine Learning Studio (klasik) çalışma alanı ve Azure Stream Analytics Işinin aynı Azure bölgesinde yer aldığından emin olmak iyi bir uygulamadır. [Bölgeye göre Azure ürün kullanılabilirliği sayfasında](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)Azure Machine Learning Studio (klasik) ve diğer Azure hizmetleri için bölgesel destek ' i kontrol edebilirsiniz.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Hava durumu tahmin modelini Web hizmeti olarak dağıtma

Bu bölümde, Azure AI kitaplığından Hava durumu tahmin modelini alırsınız. Ardından, sıcaklığın ve nem verilerinin temizlenmesi için modele bir R-Script modülü eklersiniz. Son olarak, modeli bir tahmine dayalı Web hizmeti olarak dağıtırsınız.

### <a name="get-the-weather-prediction-model"></a>Hava durumu tahmin modelini al

Bu bölümde, hava durumu tahmin modelini Azure Yapay Zeka Galerisi alın ve Azure Machine Learning Studio (klasik) içinde açın.

1. [Hava durumu tahmin modeli sayfasına](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)gidin.

   ![Azure Yapay Zeka Galerisi içinde hava durumu tahmin modeli sayfasını açın](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Modeli Microsoft Azure Machine Learning Studio (klasik) içinde açmak için **Studio 'Da aç (klasik)** seçeneğini belirleyin. IoT Hub 'ınız ve **Galeri açılır listesinden kopyalama denemesinde** doğru çalışma alanının yakınında bir bölge seçin.

   ![Hava durumu tahmin modelini Azure Machine Learning Studio (klasik) açın](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Sıcaklık ve nem verilerini temizlemek için bir R-betik modülü ekleme

Modelin doğru şekilde davranması için sıcaklık ve nem verileri sayısal verilere dönüştürülebilir olmalıdır. Bu bölümde, sıcaklık veya nem için veri değerlerine sahip satırları kaldıran, sayısal değerlere dönüştürülemeyen bir R-Script modülü eklersiniz.

1. Azure Machine Learning Studio (klasik) penceresinin sol tarafında, Araçlar panelini genişletmek için oku seçin. Arama kutusuna "Execute" yazın. **R betiği Yürüt** modülünü seçin.

   ![R betik modülünü Yürüt seçeneğini belirleyin](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. **Execute r betiği** modülünü **Temizleme eksik veri** modülünün yanına sürükleyin ve diyagramda var olan **r betik modülünü yürütün** . **Eksik verileri temizleme** ve **R betiği çalıştırma** modüllerini arasındaki bağlantıyı silin ve ardından yeni modülün giriş ve çıkışlarını gösterildiği gibi bağlayın.

   ![R betiği yürütme modülü Ekle](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Özellikler penceresini açmak için yeni **R betiği Yürüt** modülünü seçin. Aşağıdaki kodu kopyalayıp **R betik** kutusuna yapıştırın.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   İşiniz bittiğinde, Özellikler penceresi şuna benzer görünmelidir:

   ![R betik modülünü yürütmek için kod ekleme](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Tahmine dayalı Web hizmeti dağıtma

Bu bölümde, modeli doğrular, modele göre tahmine dayalı bir Web hizmeti ayarlarsınız ve sonra Web hizmetini dağıtırsınız.

1. Modeldeki adımları doğrulamak için **Çalıştır** ' ı seçin. Bu adımın tamamlanması birkaç dakika sürebilir.

   ![Adımları doğrulamak için denemeyi çalıştırın](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. **Web hizmeti** tahmine  >  **dayalı Web hizmeti** ayarla ' yı seçin. Tahmine dayalı deneme diyagramı açılır.

   ![Hava durumu tahmin modelini Azure Machine Learning Studio dağıtma (klasik)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Tahmine dayalı deneme diyagramında, **Web hizmeti giriş** modülü ve en üstteki **veri kümesindeki sütunları seç** arasındaki bağlantıyı silin. Ardından, **Web hizmeti giriş** modülünü **puan modeli** modülünün yakınında bir yere sürükleyin ve gösterildiği gibi bağlayın:

   ![Azure Machine Learning Studio iki modülü bağlama (klasik)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Modeldeki adımları doğrulamak için **Çalıştır** ' ı seçin.

1. Modeli bir Web hizmeti olarak dağıtmak için **Web Hizmeti Dağıt** ' ı seçin.

1. Modelin panosunda, **istek/yanıt** için **Excel 2010 veya önceki bir çalışma kitabını** indirin.

   > [!Note]
   > Bilgisayarınızda Excel 'in daha yeni bir sürümünü çalıştırıyor olsanız bile **excel 2010 veya önceki bir çalışma kitabını** indirdiğinizden emin olun.

   ![Istek yanıtı uç noktası için Excel 'i indirin](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Excel çalışma kitabını açın, **Web hizmeti URL 'si** ve **erişim anahtarı**' nı bir yere getirin.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics işi oluşturma, yapılandırma ve çalıştırma

### <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

1. [Azure Portal](https://portal.azure.com/) **kaynak oluştur**' u seçin. Arama kutusuna "Stream Analytics işi" yazın ve sonuçlar açılan listesinden **Stream Analytics iş** ' ı seçin. **Stream Analytics işi** bölmesi açıldığında **Oluştur**' u seçin.
1. İş için aşağıdaki bilgileri girin.

   **İş adı**: İşin adı. Adın genel olarak benzersiz olması gerekir.

   **Abonelik**: varsayılan değerden farklıysa aboneliğinizi seçin.

   **Kaynak grubu**: IoT Hub 'ınızın kullandığı kaynak grubunu kullanın.

   **Konum**: kaynak grubunuzda aynı konumu kullanın.

   Tüm diğer alanları varsayılan olarak bırakın.

   ![Azure 'da Stream Analytics işi oluşturma](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. **Oluştur**’u seçin.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics işine giriş ekleme

1. Stream Analytics işini açın.
1. **İş topolojisi**'nin altında **Girişler**'i seçin.
1. **Giriş** bölmesinde, **akış girişi Ekle**' yi seçin ve açılan listeden **IoT Hub** ' yi seçin. **Yeni giriş** bölmesinde **aboneliklerinizden Seç IoT Hub** seçin ve aşağıdaki bilgileri girin:

   **Giriş diğer adı**: girişin benzersiz diğer adı.

   **Abonelik**: varsayılan değerden farklıysa aboneliğinizi seçin.

   **IoT Hub**: aboneliğinizden IoT Hub 'ını seçin.

   **Paylaşılan erişim ilkesi adı**:  **hizmeti** seçin. ( **Iothubowner** da kullanabilirsiniz.)

   **Tüketici grubu**: oluşturduğunuz tüketici grubunu seçin.

   Tüm diğer alanları varsayılan olarak bırakın.

   ![Azure 'da Stream Analytics işine bir giriş ekleme](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. **Kaydet**’i seçin.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics işine çıkış ekleme

1. **İş topolojisi**'nin altında **Çıkışlar**'ı seçin.
1. **Çıktılar** bölmesinde **Ekle**' yi seçin ve ardından açılan listeden **BLOB depolama/Data Lake Storage** ' u seçin. **Yeni çıkış** bölmesinde **aboneliklerinizden depolamayı Seç** ' i seçin ve aşağıdaki bilgileri girin:

   **Çıkış diğer adı**: Çıkışın benzersiz diğer adı.

   **Abonelik**: varsayılan değerden farklıysa aboneliğinizi seçin.

   **Depolama hesabı**: BLOB depolama alanınızı depolama hesabı. Bir depolama hesabı oluşturabilir veya var olan bir hesap kullanabilirsiniz.

   **Kapsayıcı**: Blobun kaydedildiği kapsayıcı. Bir kapsayıcı oluşturabilir veya var olan bir kapsayıcı kullanabilirsiniz.

   **Olay serileştirme biçimi**: **CSV**'yi seçin.

   ![Azure 'da Stream Analytics işine bir çıktı ekleyin](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. **Kaydet**’i seçin.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dağıttığınız Web hizmetini çağırmak için Stream Analytics işine bir işlev ekleyin

1. **Iş topolojisi** altında **işlevler**' i seçin.
1. **İşlevler** bölmesinde **Ekle**' yi seçin ve açılan listeden **Azure ML Studio** ' yi seçin. (Azure **ml hizmeti** değil, **Azure ML Studio**' yi seçtiğinizden emin olun.) **Yeni işlev** bölmesinde **Azure Machine Learning işlev ayarlarını el ile sağlayın** ve aşağıdaki bilgileri girin:

   **Işlev diğer adı**: ENTER `machinelearning` .

   **URL**: Excel çalışma kitabından Not ETTIĞINIZ Web hizmeti URL 'sini girin.

   **Anahtar**: Excel çalışma kitabından Not ETTIĞINIZ erişim anahtarını girin.

   ![Azure 'da Stream Analytics işine bir işlev ekleme](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. **Kaydet**’i seçin.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics işinin sorgusunu yapılandırma

1. **İş topolojisi**'nin altında **Sorgu**'yu seçin.
1. Mevcut kodu aşağıdaki kodla değiştirin:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   `[YourInputAlias]` değerini işin giriş diğer adıyla değiştirin.

   `[YourOutputAlias]` değerini işin çıkış diğer adıyla değiştirin.

1. **Sorguyu Kaydet**' i seçin.

> [!Note]
> **Test sorgusu**' nu seçerseniz şu iletiyle karşılaşırsınız: Machine Learning işlevlerle sorgu testi desteklenmez. Lütfen sorguyu değiştirin ve yeniden deneyin. Bu iletiyi güvenle yoksayabilir ve ileti kutusunu kapatmak için **Tamam** ' ı seçebilirsiniz. Sonraki bölüme geçmeden önce sorguyu kaydettiğinizden emin olun.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

Stream Analytics işinde, sol bölmedeki **genel bakış** ' ı seçin. Ardından Şimdi **Başlat ' ı seçin**  >    >  . İş düzgün bir şekilde başlatıldıktan sonra, **Durduruldu** olan iş durumu **Çalışıyor** olarak değiştirilir.

![Stream Analytics işini çalıştırma](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Hava durumu tahminini görüntülemek için Microsoft Azure Depolama Gezgini kullanın

IoT Hub 'ınıza sıcaklık ve nem verileri toplamaya ve göndermeye başlamak için istemci uygulamasını çalıştırın. IoT Hub 'ınızın aldığı her ileti için Stream Analytics işi, yağmur olma olasılığını üretmek için hava durumu tahmin Web hizmetini çağırır. Sonuç daha sonra Azure Blob depolama alanına kaydedilir. Azure Depolama Gezgini, sonucu görüntülemek için kullanabileceğiniz bir araçtır.

1. [Microsoft Azure Depolama Gezgini indirin ve yükleyin](https://storageexplorer.com/).
1. Azure Depolama Gezgini açın.
1. Azure hesabınızda oturum açın.
1. Aboneliğinizi seçin.
1. Depolama hesabınızı > depolama **hesaplarınız** > **BLOB kapsayıcıları** > kapsayıcınızı > için aboneliğinizi seçin.
1. Sonucu görmek için bir. csv dosyası indirin. Son sütun, yağmur olma olasılığını kaydeder.

   ![Azure Machine Learning Studio Hava durumu tahmin sonucu alın (klasik)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Özet

IoT Hub 'ınızın aldığı sıcaklık ve nem verilerine bağlı olarak yağmur olma olasılığını üretmek için Azure Machine Learning Studio (klasik) başarıyla kullandınız.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]