---
title: IoT Hub verilerle Azure Machine Learning kullanarak hava durumu tahmini
description: IoT Hub 'ınızın bir sensörden topladığı sıcaklık ve nem verilerine bağlı olarak yağmur olma olasılığını tahmin etmek için Azure Machine Learning kullanın.
author: robinsh
manager: philmea
keywords: Hava durumu tahmin makinesi öğrenimi
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: e7114febe85af4acaf94ef7fe64fe47988fd6321
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079259"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Azure Machine Learning 'daki IoT Hub 'ından gelen algılayıcı verilerini kullanarak hava durumu tahmini

![Uçtan uca diyagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Makine öğrenimi, bilgisayarların, gelecekteki davranışları, sonuçları ve eğilimleri tahmin etmek için mevcut verilerden öğrenmesini sağlayan bir veri bilimi tekniğidir. Azure Machine Learning, tahmine dayalı modelleri analiz çözümleri olarak hızlı bir şekilde oluşturmayı ve dağıtmayı mümkün kılan bulut tabanlı ve tahmine dayalı analiz hizmetidir.

## <a name="what-you-learn"></a>Öğrenecekleriniz

Azure IoT Hub 'ınızdaki sıcaklık ve nem verilerini kullanarak hava durumu tahmini (yağmur şansı) için Azure Machine Learning kullanmayı öğreneceksiniz. Yağmur, hazırlanan Hava durumu tahmin modelinin çıktıdır. Model, sıcaklık ve nem oranına göre yağmur olasılığını tahmin etmek için geçmiş veriler üzerine kurulmuştur.

## <a name="what-you-do"></a>Yapabilecekleriniz

- Hava durumu tahmin modelini bir Web hizmeti olarak dağıtın.
- Bir tüketici grubu ekleyerek IoT Hub 'ınızı veri erişimi için hazırlayın.
- Stream Analytics işi oluşturun ve işi şu şekilde yapılandırın:
  - IoT Hub 'ınızdaki sıcaklık ve nem verilerini okuyun.
  - Web hizmetini arayarak yağmur şansı elde edin.
  - Sonucu bir Azure Blob depolama alanına kaydedin.
- Hava durumu tahminini görüntülemek için Microsoft Azure Depolama Gezgini kullanın.

## <a name="what-you-need"></a>Ne gerekiyor

- [Raspberry PI Çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md) öğreticisini veya cihaz öğreticilerinin birini doldurun; Örneğin, [Node. js Ile Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md). Bu, aşağıdaki gereksinimleri kapsar:
  - Etkin bir Azure aboneliği.
  - Aboneliğiniz kapsamındaki bir Azure IoT Hub 'ı.
  - Azure IoT Hub 'ınıza ileti gönderen bir istemci uygulaması.
- [Azure Machine Learning Studio](https://studio.azureml.net/) hesabı.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Hava durumu tahmin modelini Web hizmeti olarak dağıtma

1. [Hava durumu tahmin modeli sayfasına](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)gidin.
1. Microsoft Azure Machine Learning Studio 'da **Studio 'Da aç** ' a tıklayın.
   ![Cortana Intelligence Gallery Hava durumu tahmin modeli sayfasını açın](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Modeldeki adımları doğrulamak için **Çalıştır** ' a tıklayın. Bu adımın tamamlanması 2 dakika sürebilir.
   Hava durumu tahmin modelini Azure Machine Learning Studio ![açın](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. **Web hizmeti** > tahmine **dayalı Web hizmeti**ayarla ' ya tıklayın.
   Hava durumu tahmin modelini Azure Machine Learning Studio ![dağıtma](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Diyagramda, **Web hizmeti giriş** modülünü **puan modeli** modülünün yakınında bir yere sürükleyin.
1. **Web hizmeti giriş** modülünü, **puan modeli** modülüne bağlayın.
   ![Azure Machine Learning Studio iki modüle bağlama](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Modeldeki adımları doğrulamak için **Çalıştır** ' a tıklayın.
1. Modeli bir Web hizmeti olarak dağıtmak için **Web Hizmeti Dağıt** ' a tıklayın.
1. Modelin panosunda, **istek/yanıt**için **Excel 2010 veya önceki bir çalışma kitabını** indirin.

   > [!Note]
   > Bilgisayarınızda Excel 'in daha yeni bir sürümünü çalıştırıyor olsanız bile **excel 2010 veya önceki bir çalışma kitabını** indirdiğinizden emin olun.

   ![Istek yanıtı uç noktası için Excel 'i indirin](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Excel çalışma kitabını açın, **Web hizmeti URL 'si** ve **erişim anahtarı**' nı bir yere getirin.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics işi oluşturma, yapılandırma ve çalıştırma

### <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

1. [Azure portalında](https://portal.azure.com/), **Kaynak oluştur** > **Nesnelerin İnterneti** > **Stream Analytics işi**'ne tıklayın.
1. İş için aşağıdaki bilgileri girin.

   **İş adı**: İşin adı. Adın genel olarak benzersiz olması gerekir.

   **Kaynak grubu**: IoT Hub 'ınızın kullandığı kaynak grubunu kullanın.

   **Konum**: kaynak grubunuzda aynı konumu kullanın.

   **Panoya sabitle**: Panodan IoT hub'ınıza kolay erişim için bu seçeneği işaretleyin.

   ![Azure 'da Stream Analytics işi oluşturma](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. **Oluştur**’ tıklayın.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics işine giriş ekleme

1. Stream Analytics işini açın.
1. **İş Topolojisi**'nin altında **Girişler**'e tıklayın.
1. **Girişler** bölmesinde, **Ekle**' ye tıklayın ve ardından aşağıdaki bilgileri girin:

   **Giriş diğer adı**: girişin benzersiz diğer adı.

   **Kaynak**: **IoT Hub 'ı**seçin.

   **Tüketici grubu**: oluşturduğunuz tüketici grubunu seçin.

   ![Azure 'da Stream Analytics işine bir giriş ekleme](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. **Oluştur**’ tıklayın.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics işine çıkış ekleme

1. **İş Topolojisi**'nin altında **Çıkışlar**'a tıklayın.
1. **Çıktılar** bölmesinde, **Ekle**' ye tıklayın ve ardından aşağıdaki bilgileri girin:

   **Çıkış diğer adı**: Çıkışın benzersiz diğer adı.

   **Havuz**: **BLOB depolamayı**seçin.

   **Depolama hesabı**: BLOB depolama alanınızı depolama hesabı. Bir depolama hesabı oluşturabilir veya var olan bir hesap kullanabilirsiniz.

   **Kapsayıcı**: Blobun kaydedildiği kapsayıcı. Bir kapsayıcı oluşturabilir veya var olan bir kapsayıcı kullanabilirsiniz.

   **Olay serileştirme biçimi**: **CSV**'yi seçin.

   ![Azure 'da Stream Analytics işine bir çıktı ekleyin](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. **Oluştur**’ tıklayın.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dağıttığınız Web hizmetini çağırmak için Stream Analytics işine bir işlev ekleyin

1. **Iş topolojisi**altında **işlevler** > **Ekle**' ye tıklayın.
1. Aşağıdaki bilgileri girin:

   **Işlev diğer adı**: `machinelearning`girin.

   **Işlev türü**: **Azure ML**'yi seçin.

   **Içeri aktarma seçeneği**: **farklı bir abonelikten içeri aktar**' ı seçin.

   **URL**: Excel çalışma kitabından Not ETTIĞINIZ Web hizmeti URL 'sini girin.

   **Anahtar**: Excel çalışma kitabından Not ETTIĞINIZ erişim anahtarını girin.

   ![Azure 'da Stream Analytics işine bir işlev ekleme](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. **Oluştur**’ tıklayın.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics işinin sorgusunu yapılandırma

1. **İş Topolojisi**'nin altında **Sorgu**'ya tıklayın.
1. Mevcut kodu şu kodla değiştirin:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   `[YourInputAlias]` değerini işin giriş diğer adıyla değiştirin.

   `[YourOutputAlias]` değerini işin çıkış diğer adıyla değiştirin.

1. **Kaydet**’e tıklayın.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

Stream Analytics işinde **Başlat** > **Şimdi** > **Başlat**'a tıklayın. İş düzgün bir şekilde başlatıldıktan sonra, **Durduruldu** olan iş durumu **Çalışıyor** olarak değiştirilir.

![Stream Analytics işini çalıştırma](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Hava durumu tahminini görüntülemek için Microsoft Azure Depolama Gezgini kullanın

IoT Hub 'ınıza sıcaklık ve nem verileri toplamaya ve göndermeye başlamak için istemci uygulamasını çalıştırın. IoT Hub 'ınızın aldığı her ileti için Stream Analytics işi, yağmur olma olasılığını üretmek için hava durumu tahmin Web hizmetini çağırır. Sonuç daha sonra Azure Blob depolama alanına kaydedilir. Azure Depolama Gezgini, sonucu görüntülemek için kullanabileceğiniz bir araçtır.

1. [Microsoft Azure Depolama Gezgini indirin ve yükleyin](https://storageexplorer.com/).
1. Azure Depolama Gezgini açın.
1. Azure hesabınızda oturum açın.
1. Aboneliğinizi seçin.
1. Depolama hesabınız > **BLOB kapsayıcıları** > > depolama hesabı > depolama **hesaplarınız** ' e tıklayın.
1. Sonucu görmek için bir. csv dosyası açın. Son sütun, yağmur olma olasılığını kaydeder.

   ![Azure Machine Learning Hava durumu tahmin sonucu alın](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Özet

IoT Hub 'ınızın aldığı sıcaklık ve nem verilerine bağlı olarak yağmur olma olasılığını oluşturmak için Azure Machine Learning başarıyla kullandınız.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]