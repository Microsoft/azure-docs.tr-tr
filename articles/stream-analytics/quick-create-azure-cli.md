---
title: Hızlı başlangıç-Azure CLı kullanarak Azure Stream Analytics işi oluşturma
description: Bu hızlı başlangıçta Azure Stream Analytics işi oluşturmak için Azure CLı 'nın nasıl kullanılacağı gösterilmektedir.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: bb8f2d77c04e01c47318042337db819ac2f36d46
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863200"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure Stream Analytics işi oluşturma

Bu hızlı başlangıçta, Azure CLı 'yı kullanarak gerçek zamanlı algılayıcı iletilerini 27 ' den büyük bir sıcaklık okuması ile filtreleyen bir Stream Analytics işi tanımlayabilirsiniz. Stream Analytics işiniz IoT Hub verileri okur, verileri dönüştürür ve BLOB depolama alanındaki verileri bir kapsayıcıya geri yazar. Bu hızlı başlangıçta kullanılan giriş verileri bir Raspberry PI Çevrimiçi simülatör tarafından oluşturulmuştur.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bir kaynak grubu oluşturun. Tüm Azure kaynakları bir kaynak grubuna dağıtılmalıdır. Kaynak grupları, ilgili Azure kaynaklarını düzenlemenizi ve yönetmenizi sağlar.

   Bu hızlı başlangıç için, *streamanalyticsrg* adlı bir kaynak grubunu aşağıdaki [az Group Create](/cli/azure/group#az_group_create) komutuyla *eastus* konumunda oluşturun:

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Girdi verilerini hazırlama

Stream Analytics işini tanımladıktan önce, işin girişi için kullanılan verileri hazırlayın.

Aşağıdaki Azure CLı kod blokları, iş için gereken giriş verilerini hazırlayan komutlardır. Kodu anlamak için bölümleri gözden geçirin.

1. [Az IoT Hub Create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) komutunu kullanarak bir IoT Hub oluşturun. Bu örnek, **MyASAIoTHub** adlı bir IoT Hub oluşturur. IoT Hub adları benzersiz olduğundan, kendi IoT Hub adınızla birlikte getirmeniz gerekir. Aboneliğiniz ile kullanılabilirse ücretsiz katmanı kullanmak için SKU 'YU F1 olarak ayarlayın. Aksi takdirde, sonraki en düşük katmanı seçin.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    IoT Hub 'ı oluşturulduktan sonra, [az IoT Hub Show-Connection-String](/cli/azure/iot/hub) komutunu kullanarak IoT Hub bağlantı dizesini alın. IoT Hub, Stream Analytics işinize girdi olarak eklediğinizde, tüm bağlantı dizesini kopyalayın ve kaydedin.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. [Az ıothub Device-Identity Create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) komutunu kullanarak IoT Hub bir cihaz ekleyin. Bu örnek, **MyASAIoTDevice** adlı bir cihaz oluşturur.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. [Az IoT Hub cihazı-Identity Show-Connection-String](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show_connection_string) komutunu kullanarak cihaz bağlantı dizesini alın. Tüm bağlantı dizesini kopyalayın ve Raspberry PI simülatörü oluştururken kaydedin.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Çıkış örneği:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>BLOB depolama hesabı oluşturma

Aşağıdaki Azure CLı kod blokları, iş çıktısı için kullanılan bir BLOB depolama hesabı oluşturur. Kodu anlamak için bölümleri gözden geçirin.

1. [az storage account create](/cli/azure/storage/account) komutuyla bir genel amaçlı depolama hesabı oluşturun. Genel amaçlı depolama hesabı; bloblar, dosyalar, tablolar ve kuyruklar olmak üzere dört hizmet için de kullanılabilir.

   Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. [Az Storage Account Keys List](/cli/azure/storage/account/keys) komutunu çalıştırarak depolama hesabınızın anahtarını alın. Sonraki adımda kullanmak için bu anahtarı kaydedin.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. [az storage container create](/cli/azure/storage/container) komutunu kullanarak blobları depolamak için bir kapsayıcı oluşturun. Kapsayıcıyı oluşturmak için işlemi yetkilendirmek üzere depolama hesabı anahtarını kullanın. Veri işlemlerini Azure CLı ile yetkilendirme hakkında daha fazla bilgi için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](../storage/blobs/authorize-data-operations-cli.md).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

Aşağıdaki Azure CLı kod blokları Stream Analytics işi oluşturur. Kodu anlamak için bölümleri gözden geçirin

1. [Az Stream-Analytics Job Create](/cli/azure/stream-analytics/job#az_stream_analytics_job_create) komutuyla bir Stream Analytics işi oluşturun.

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>İş girdisini yapılandırma

[Az Stream-Analytics Input](/cli/azure/stream-analytics/input#az_stream_analytics_input_create) cmdlet 'ini kullanarak işinize bir giriş ekleyin. Bu cmdlet iş adı, iş girdisi adı, kaynak grubu adı ve iş girdisi tanımını parametre olarak alır. İş giriş tanımı, işin girişini yapılandırmak için gereken özellikleri içeren bir JSON dosyasıdır. Bu örnekte, giriş olarak bir IoT Hub oluşturacaksınız.

Yerel makinenizde `datasource.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin. Değerini, `sharedAccessPolicyKey` `SharedAccessKey` önceki bölümde kaydettiğiniz IoT Hub bağlantı dizesinin bölümüyle değiştirdiğinizden emin olun.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Yerel makinenizde `serialization.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Ardından, `az stream-analytics input create` cmdlet'ini çalıştırın. Değişkenin değerini, `datasource` iş giriş tanımı json dosyasını depoladığınız yol ile ve `serialization` değişken DEĞERI serileştirme json dosyasını depoladığınız yolla değiştirdiğinizden emin olun.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>İş çıktısını yapılandırma

[Az Stream-Analytics output Create](/cli/azure/stream-analytics/output#az_stream_analytics_output_create) cmdlet 'ini kullanarak işinize bir çıktı ekleyin. Bu cmdlet iş adı, iş çıktısı adı, kaynak grubu adı ve iş çıktısı tanımını parametre olarak alır. İş çıkış tanımı, işin çıkışını yapılandırmak için gereken özellikleri içeren bir JSON dosyasıdır. Bu örnek, çıktı olarak blob depolama kullanır.

Yerel makinenizde `datasink.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin. Değerini, `accountKey` $storageAccountKey değerinde depolanan değer olan depolama hesabınızın erişim anahtarı ile değiştirdiğinizden emin olun.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Ardından, `az stream-analytics output` cmdlet'ini çalıştırın. `datasource`Değişkenin değerini iş çıkış tanımı json dosyasını depoladığınız yol ile ve `serialization` değişken DEĞERI serileştirme json dosyasını depoladığınız yolla değiştirdiğinizden emin olun.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Dönüşüm sorgusunu tanımlama

[Az Stream-Analytics dönüştürme Create](/cli/azure/stream-analytics/transformation#az_stream_analytics_transformation_create) cmdlet 'ini kullanarak işiniz için bir dönüşüm ekleyin. Bu cmdlet iş adı, iş dönüşümü adı, kaynak grubu adı ve iş dönüşümü tanımını parametre olarak alır. 

`az stream-analytics transformation create`Cmdlet 'ini çalıştırın.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>IoT simülatörü çalıştırma

1. [Raspberry PI Azure IoT çevrimiçi simülatörünü](https://azure-samples.github.io/raspberry-pi-web-simulator/)açın.

2. 15. satırdaki yer tutucusunu, önceki bölümde kaydettiğiniz tüm Azure IoT Hub cihaz bağlantı dizesi ile değiştirin.

3. **Çalıştır**'a tıklayın. Çıktıda, IoT Hub gönderilen algılayıcı verileri ve iletileri gösterilmelidir.

    ![Raspberry Pi Azure IoT Çevrimiçi Simülatörü](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics işini başlatıp çıktıyı denetleyin

[Az Stream-Analytics Job start](/cli/azure/stream-analytics/job#az_stream_analytics_job_start) cmdlet 'ini kullanarak işi başlatın. Bu cmdlet iş adı, kaynak grubu adı, çıktı başlangıç modu ve başlangıç saatini parametre olarak alır. `OutputStartMode`; `JobStartTime`, `CustomTime` veya `LastOutputEventTime` değerlerini kabul eder.

Aşağıdaki cmdlet’i çalıştırdıktan sonra iş başlarsa çıktı olarak `True` değeri döndürülür. Depolama kapsayıcısında, dönüştürülmüş verilerle birlikte bir çıktı klasörü oluşturulur.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, silme işlemini atlayıp işi şimdilik durdurabilirsiniz. Bu işi kullanmaya devam edemeyecekecekseniz, aşağıdaki cmdlet 'i çalıştırarak bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silin:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure CLı kullanarak basit bir Stream Analytics işi dağıttınız. Stream Analytics işlerini [Azure portalı](stream-analytics-quick-create-portal.md) ve [Visual Studio](stream-analytics-quick-create-vs.md)’yu kullanarak da dağıtabilirsiniz.

Diğer girdi kaynaklarını yapılandırma ve gerçek zamanlı algılama hakkında bilgi almak için aşağıdaki makaleye geçin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics kullanarak gerçek zamanlı sahtekarlık algılama](stream-analytics-real-time-fraud-detection.md)