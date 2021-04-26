---
title: Hızlı başlangıç-Azure PowerShell kullanarak Stream Analytics işi oluşturma
description: Bu hızlı başlangıçta, bir Azure Stream Analytics işini dağıtmak ve çalıştırmak için Azure PowerShell modülünün nasıl kullanılacağı gösterilmektedir.
author: enkrumah
ms.author: ebnkruma
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 193c8b1f47960b9b42c61a9b0e394e5ebedf7bf7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867610"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak Stream Analytics işi oluşturma

Azure PowerShell modülü, PowerShell cmdlet 'leri veya betikleri kullanılarak Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta Azure PowerShell modülü kullanarak bir Azure Stream Analytics işi dağıtma ve çalıştırma işleminin ayrıntıları verilmektedir.

Örnek iş, bir IoT Hub cihazından akış verilerini okur. Giriş verileri bir Raspberry PI Çevrimiçi simülatör tarafından oluşturulur. Sonra Stream Analytics işi, verileri 27 ° ' dan büyük bir sıcaklığa sahip iletileri filtrelemek için Stream Analytics sorgu dilini kullanarak dönüştürür. Son olarak, sonuç çıktı olaylarını BLOB depolama alanındaki bir dosyaya yazar.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun.](https://azure.microsoft.com/free/)

* Bu hızlı başlangıç Azure PowerShell modülünü gerektirir. Yerel makinenizde yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps).

* Bazı IoT Hub eylemleri Azure PowerShell desteklenmez ve Azure CLI sürüm 2.0.70 veya üzeri ile Azure CLı için IoT uzantısı kullanılarak tamamlanmalıdır. [Azure CLI 'Yı yükleyip](/cli/azure/install-azure-cli) `az extension add --name azure-iot` IoT uzantısını yüklemek için kullanın.


## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Komutuyla Azure aboneliğinizde oturum açın `Connect-AzAccount` ve açılır tarayıcıda Azure kimlik bilgilerinizi girin:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Birden fazla aboneliğiniz varsa, aşağıdaki cmdlet 'leri çalıştırarak bu hızlı başlangıç için kullanmak istediğiniz aboneliği seçin. `<your subscription name>` değerini aboneliğinizin adıyla değiştirdiğinizden emin olun:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>Girdi verilerini hazırlama

Stream Analytics işini tanımlamadan önce, işe girdi olarak yapılandırılan verileri hazırlayın.

Aşağıdaki Azure CLı kod bloğu, iş için gereken giriş verilerini hazırlamak için birçok komut yapar. Kodu anlamak için bölümleri gözden geçirin.

1. PowerShell pencerenizde, Azure hesabınızda oturum açmak için [az Login](/cli/azure/authenticate-azure-cli) komutunu çalıştırın.

    Başarıyla oturum açtığınızda Azure CLı, aboneliklerinizin bir listesini döndürür. Bu hızlı başlangıç için kullanmakta olduğunuz aboneliği kopyalayın ve bu aboneliği seçmek için [az Account set](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) komutunu çalıştırın. Önceki bölümde PowerShell ile seçtiğiniz aboneliği seçin. Öğesini aboneliğinizin adıyla değiştirdiğinizden emin olun `<your subscription name>` .

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. [Az IoT Hub Create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) komutunu kullanarak bir IoT Hub oluşturun. Bu örnek, **MyASAIoTHub** adlı bir IoT Hub oluşturur. IoT Hub adları benzersiz olduğundan, kendi IoT Hub adınızla birlikte getirmeniz gerekir. Aboneliğiniz ile kullanılabilirse ücretsiz katmanı kullanmak için SKU 'YU F1 olarak ayarlayın. Aksi takdirde, sonraki en düşük katmanı seçin.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    IoT Hub 'ı oluşturulduktan sonra, [az IoT Hub Show-Connection-String](/cli/azure/iot/hub) komutunu kullanarak IoT Hub bağlantı dizesini alın. IoT Hub, Stream Analytics işinize girdi olarak eklediğinizde, tüm bağlantı dizesini kopyalayın ve kaydedin.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. [Az ıothub Device-Identity Create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) komutunu kullanarak IoT Hub bir cihaz ekleyin. Bu örnek, **MyASAIoTDevice** adlı bir cihaz oluşturur.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. [Az IoT Hub cihazı-Identity Show-Connection-String](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show_connection_string) komutunu kullanarak cihaz bağlantı dizesini alın. Tüm bağlantı dizesini kopyalayın ve Raspberry PI simülatörü oluştururken kaydedin.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Çıkış örneği:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>BLOB depolama oluştur

Aşağıdaki Azure PowerShell kod bloğu, iş çıktısı için kullanılan blob depolaması oluşturmak için komutları kullanır. Kodu anlamak için bölümleri gözden geçirin.

1. [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) cmdlet 'ini kullanarak standart bir genel amaçlı depolama hesabı oluşturun.  Bu örnek, yerel olarak yedekli depolama (LRS) ve BLOB şifrelemesi (varsayılan olarak etkindir) ile **myasaquickstartstorage** adlı bir depolama hesabı oluşturur.

2. Kullanılacak depolama hesabını tanımlayan `$storageAccount.Context` depolama hesabı bağlamını alın. Depolama hesaplarıyla çalışırken kimlik bilgilerini tekrar tekrar sağlamak yerine bağlama başvurursunuz.

3. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)kullanarak bir depolama kapsayıcısı oluşturun.

4. Kod tarafından çıkarılan depolama anahtarını kopyalayın ve akış işinin çıkışını daha sonra oluşturmak için bu anahtarı kaydedin.

    ```powershell
    $storageAccountName = "myasaquickstartstorage"
    $storageAccount = New-AzStorageAccount `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName `
      -Location $location `
      -SkuName Standard_LRS `
      -Kind Storage

    $ctx = $storageAccount.Context
    $containerName = "container1"

    New-AzStorageContainer `
      -Name $containerName `
      -Context $ctx

    $storageAccountKey = (Get-AzStorageAccountKey `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName).Value[0]

    Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:"
    Write-Host $storageAccountKey -ForegroundColor Cyan
    ```

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

[New-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) cmdlet 'i ile Stream Analytics işi oluşturun. Bu cmdlet, iş adı, kaynak grubu adı ve iş tanımını parametre olarak alır. İş adı, işinizi tanımlayan herhangi bir kolay ad olabilir. Yalnızca alfasayısal karakter, kısa çizgi ve alt çizgi içerebilir ve 3 ila 63 karakter uzunluğunda olmalıdır. İş tanımı bir iş oluşturmak için gereken özellikleri içeren bir JSON dosyasıdır. Yerel makinenizde `JobDefinition.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin:

```json
{
  "location":"WestUS2",
  "properties":{
    "sku":{
      "name":"standard"
    },
    "eventsOutOfOrderPolicy":"adjust",
    "eventsOutOfOrderMaxDelayInSeconds":10,
    "compatibilityLevel": 1.1
  }
}
```

Ardından, `New-AzStreamAnalyticsJob` cmdlet'ini çalıştırın. Değişkenin değerini, `jobDefinitionFile` iş tanımı json dosyasını depoladığınız yol ile değiştirin.

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force
```

## <a name="configure-input-to-the-job"></a>İş girdisini yapılandırma

[New-AzStreamAnalyticsInput](/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) cmdlet 'ini kullanarak işinize bir giriş ekleyin. Bu cmdlet iş adı, iş girdisi adı, kaynak grubu adı ve iş girdisi tanımını parametre olarak alır. İş girdisi tanımı işin girdisini yapılandırmak için gereken özellikleri içeren bir JSON dosyasıdır. Bu örnekte, giriş olarak bir BLOB depolama alanı oluşturacaksınız.

Yerel makinenizde `JobInputDefinition.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin. Değerini, `accesspolicykey` `SharedAccessKey` önceki bölümde kaydettiğiniz IoT Hub bağlantı dizesinin bölümüyle değiştirdiğinizden emin olun.

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Devices/IotHubs",
            "properties": {
                "iotHubNamespace": "MyASAIoTHub",
                "sharedAccessPolicyName": "iothubowner",
                "sharedAccessPolicyKey": "accesspolicykey",
                "endpoint": "messages/events",
                "consumerGroupName": "$Default"
                }
        },
        "compression": {
            "type": "None"
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "IoTHubInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Sonra, `New-AzStreamAnalyticsInput` cmdlet 'ini çalıştırın, `jobDefinitionFile` değişkenin değerini iş GIRIŞ tanımı json dosyasını depoladığınız yolla değiştirdiğinizden emin olun.

```powershell
$jobInputName = "IoTHubInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName
```

## <a name="configure-output-to-the-job"></a>İş çıktısını yapılandırma

[New-AzStreamAnalyticsOutput](/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) cmdlet 'ini kullanarak işinize bir çıktı ekleyin. Bu cmdlet iş adı, iş çıktısı adı, kaynak grubu adı ve iş çıktısı tanımını parametre olarak alır. İş çıktısı tanımı işin çıktısını yapılandırmak için gereken özellikleri içeren bir JSON dosyasıdır. Bu örnek, çıktı olarak blob depolama kullanır.

Yerel makinenizde `JobOutputDefinition.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin. `accountKey` değerini, depolama hesabınızın $storageAccountKey değerinde depolanmış değeri olan erişim anahtarıyla değiştirdiğinizden emin olun.

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "asaquickstartstorage",
                      "accountKey": "<storage account key>"
                    }
                ],
                "container": "container1",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "BlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Ardından, `New-AzStreamAnalyticsOutput` cmdlet'ini çalıştırın. `jobOutputDefinitionFile` değişkeninin değerini, iş çıktısı tanımı JSON dosyasını depoladığınız yol ile değiştirdiğinizden emin olun.

```powershell
$jobOutputName = "BlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force
```

## <a name="define-the-transformation-query"></a>Dönüşüm sorgusunu tanımlama

[New-AzStreamAnalyticsTransformation](/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) cmdlet 'ini kullanarak işiniz için bir dönüşüm ekleyin. Bu cmdlet iş adı, iş dönüşümü adı, kaynak grubu adı ve iş dönüşümü tanımını parametre olarak alır. Yerel makinenizde `JobTransformationDefinition.json` adlı bir dosya oluşturun ve içine aşağıdaki JSON verilerini ekleyin. JSON dosyası, dönüşüm sorgusunu tanımlayan bir sorgu parametresi içerir:

```json
{
    "name":"MyTransformation",
    "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",
    "properties":{
        "streamingUnits":1,
        "script":null,
        "query":" SELECT * INTO BlobOutput FROM IoTHubInput HAVING Temperature > 27"
    }
}
```

Ardından, `New-AzStreamAnalyticsTransformation` cmdlet'ini çalıştırın. `jobTransformationDefinitionFile`Değişkenin değerini iş dönüştürme tanımı json dosyasını depoladığınız yolla değiştirdiğinizden emin olun.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="run-the-iot-simulator"></a>IoT simülatörü çalıştırma

1. [Raspberry PI Azure IoT çevrimiçi simülatörünü](https://azure-samples.github.io/raspberry-pi-web-simulator/)açın.

2. 15. satırdaki yer tutucusunu, önceki bölümde kaydettiğiniz tüm Azure IoT Hub cihaz bağlantı dizesi ile değiştirin.

3. **Çalıştır**'a tıklayın. Çıktıda, IoT Hub gönderilen algılayıcı verileri ve iletileri gösterilmelidir.

    ![Raspberry Pi Azure IoT Çevrimiçi Simülatörü](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics işini başlatıp çıktıyı denetleyin

[Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) cmdlet 'ini kullanarak işi başlatın. Bu cmdlet iş adı, kaynak grubu adı, çıktı başlangıç modu ve başlangıç saatini parametre olarak alır. `OutputStartMode`; `JobStartTime`, `CustomTime` veya `LastOutputEventTime` değerlerini kabul eder. Bu değerlerin her birinin ne anlama geldiği hakkında daha fazla bilgi için PowerShell belgelerindeki [parametreler](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) bölümüne bakın.

Aşağıdaki cmdlet’i çalıştırdıktan sonra iş başlarsa çıktı olarak `True` değeri döndürülür. Depolama kapsayıcısında, dönüştürülmüş verilerle birlikte bir çıktı klasörü oluşturulur.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, silme işlemini atlayıp işi şimdilik durdurabilirsiniz. Bu işi kullanmaya devam edemeyecekecekseniz, aşağıdaki cmdlet 'i çalıştırarak bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silin:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta PowerShell kullanarak basit bir Stream Analytics işi dağıttınız. Stream Analytics işlerini [Azure portalı](stream-analytics-quick-create-portal.md) ve [Visual Studio](stream-analytics-quick-create-vs.md)’yu kullanarak da dağıtabilirsiniz.

Diğer girdi kaynaklarını yapılandırma ve gerçek zamanlı algılama hakkında bilgi almak için aşağıdaki makaleye geçin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics kullanarak gerçek zamanlı sahtekarlık algılama](stream-analytics-real-time-fraud-detection.md)