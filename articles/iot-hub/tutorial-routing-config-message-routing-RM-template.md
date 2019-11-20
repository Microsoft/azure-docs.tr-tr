---
title: Azure Resource Manager şablonu kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırma
description: Azure Resource Manager şablonu kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırma
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084400"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Öğretici: IoT Hub ileti yönlendirmeyi yapılandırmak için Azure Resource Manager şablonu kullanma

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>İleti yönlendirme

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Şablon ve parametre dosyasını indirin

Bu öğreticinin ikinci bölümünde, IoT Hub iletileri göndermek için bir Visual Studio uygulaması indirip çalıştırırsınız. Bu indirmenin, Azure CLı ve PowerShell betikleri ile birlikte Azure Resource Manager şablonu ve parametreleri dosyasını içeren bir klasör vardır.

Devam edin ve [Azure IoT C# örneklerini](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) şimdi indirin. Master. zip dosyasını ayıklayın. Kaynak Yöneticisi şablonu ve parametreler dosyası, **template_iothub. JSON** ve **template_iothub_parameters. JSON**olarak/iot-hub/Tutorials/Routing/SimulatedDevice/resources/.

## <a name="create-your-resources"></a>Kaynaklarınızı oluşturun

Tüm kaynaklarınızı oluşturmak için bir Azure Resource Manager (RM) şablonu kullanacaksınız. Azure CLı ve PowerShell betikleri, tek seferde birkaç satır çalıştırabilir. Bir RM şablonu tek bir adımda dağıtılır. Bu makalede, her birini anlamanıza yardımcı olmak için bölümler ayrı olarak gösterilir. Daha sonra, şablonu nasıl dağıtacağınızı ve test için sanal cihaz oluşturmayı gösterir. Şablon dağıtıldıktan sonra, portalda ileti yönlendirme yapılandırması ' nı görüntüleyebilirsiniz.

IoT Hub adı ve depolama hesabı adı gibi genel olarak benzersiz olması gereken birkaç kaynak adı vardır. Kaynakların adlandırılmasını kolaylaştırmak için bu kaynak adları, geçerli tarih/saatten oluşturulan rastgele alfasayısal bir değer eklemek üzere ayarlanır. 

Şablona bakarsanız, geçirilen parametreyi alan ve *Rasgelevalue değerini* parametresine bağlayan bu kaynaklar için değişkenlerin ayarlandığını görürsünüz. 

Aşağıdaki bölümde kullanılan parametreler açıklanmaktadır.

### <a name="parameters"></a>Parametreler

Bu parametrelerin çoğu varsayılan değerlere sahiptir. **_İn** ile biten olanlar, genel olarak benzersiz hale getirilmesi Için *rasgeledeğer* ile birleştirilir. 

**rasgelevalue**: Bu değer, şablonu dağıtırken geçerli tarih/saat içinden oluşturulur. Bu alan, şablonda oluşturulduğu gibi parametreler dosyasında değildir.

**SubscriptionID**: Bu alan, şablonu dağıttığınız abonelik için ayarlanır. Bu alan, sizin için ayarlandığından Parametreler dosyasında değildir.

**IoTHubName_in**: Bu alan, genel olarak benzersiz olması Için rasgeledeğeri ile birleştirilmiş temel IoT Hub adıdır.

**konum**: Bu alan, "westus" gibi dağıttığınız Azure bölgesidir.

**consumer_group**: Bu alan, yönlendirme uç noktasından gelen iletiler için bir tüketici grubu kümesidir. Azure Stream Analytics sonuçları filtrelemek için kullanılır. Örneğin, her şeyi alacağınız tüm akış, veya consumer_group **contoso**olarak ayarlanan verilerle birlikte geliyorsa, yalnızca bu girişleri göstermek için bir Azure Stream Analytics akışı (ve Power BI raporu) ayarlayabilirsiniz. Bu alan, Bu öğreticinin 2. bölümünde kullanılır.

**sku_name**: Bu alan, IoT Hub için ölçeklendiriliyor. Bu değer S1 veya üzeri olmalıdır; Bu öğreticide, birden çok uç noktaya izin verilmediğinden ücretsiz bir katman çalışmıyor.

**sku_units**: bu alan **sku_name**ve kullanılabilecek IoT Hub birimi sayısıdır.

**d2c_partitions**: Bu alan, olay akışı için kullanılan bölüm sayısıdır.

**storageAccountName_in**: Bu alan, oluşturulacak depolama hesabının adıdır. İletiler, depolama hesabındaki bir kapsayıcıya yönlendirilir. Bu alan, genel olarak benzersiz hale getirilmesi için Rasgeledeğeri ile birleştirilir.

**Storagecontainername**: Bu alan, depolama hesabına yönlendirilen iletilerin depolandığı kapsayıcının adıdır.

**storage_endpoint**: Bu alan, ileti yönlendirme tarafından kullanılan depolama hesabı uç noktasının adıdır.

**service_bus_namespace_in**: Bu alan, oluşturulacak Service Bus ad alanının adıdır. Bu değer, genel olarak benzersiz hale getirmek için Rasgeledeğeri ile birleştirilir.

**service_bus_queue_in**: Bu alan, iletileri yönlendirme için kullanılan Service Bus sırasının adıdır. Bu değer, genel olarak benzersiz hale getirmek için Rasgeledeğeri ile birleştirilir.

**AuthRules_sb_queue**: Bu alan, kuyruğa yönelik bağlantı dizesini almak için kullanılan Service Bus kuyruğu için yetkilendirme kurallarıdır.

### <a name="variables"></a>Değişkenler

Bu değerler şablonda kullanılır ve genellikle parametrelerden türetilir.

**queueAuthorizationRuleResourceId**: Bu alan, Service Bus kuyruğu yetkilendirme kuralının RESOURCEID alanıdır. RESOURCEID, sıranın bağlantı dizesini almak için kullanılır.

**Iothubname**: Bu alan, rasgeledeğeri bitiştirilmiş olduktan sonra IoT Hub adıdır. 

**storageAccountName**: Bu alan, rastgele değer eklendikten sonra depolama hesabının adıdır. 

**service_bus_namespace**: Bu alan, rasgelevalue eklenmiş olduktan sonra ad alanıdır.

**service_bus_queue**: Bu alan, rasgelevalue eklenmiş olduktan sonra Service Bus kuyruk adıdır.

**Sbversion**: kullanılacak Service Bus API sürümü. Bu durumda, "2017-04-01" olur.

### <a name="resources-storage-account-and-container"></a>Kaynaklar: depolama hesabı ve kapsayıcı

Oluşturulan ilk kaynak depolama hesabıdır ve iletilerin yönlendirildiği kapsayıcı ile birlikte. Kapsayıcı, depolama hesabı altındaki bir kaynaktır. Depolama hesabı için bir `dependsOn` yan tümcesine sahiptir ve depolama hesabının kapsayıcının öncesinde oluşturulmasını gerektirir.

Bu bölümün şöyle görünmesi aşağıda verilmiştir:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Kaynaklar: Service Bus ad alanı ve kuyruk

Oluşturulan ikinci kaynak, iletilerin yönlendirildiği Service Bus kuyruğu ile birlikte Service Bus ad alanıdır. SKU standart olarak ayarlanır. API sürümü değişkenlerden alınır. Ayrıca, bu bölümü dağıtırken Service Bus ad alanını etkinleştirmek üzere ayarlanır (durum: etkin). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Bu bölüm Service Bus kuyruğu oluşturur. Betiğin bu bölümünde, ad alanının kuyruktan önce oluşturulmasını sağlayan bir `dependsOn` yan tümcesi vardır.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Kaynaklar: IoT Hub ve ileti yönlendirme

Depolama hesabı ve Service Bus kuyruğu oluşturuldığına göre, iletileri bunlara yönlendiren IoT Hub oluşturursunuz. RM şablonu, Service Bus kaynakları ve depolama hesabı oluşturulmadan önce hub 'ı oluşturmayı denememesi için `dependsOn` yan tümceleri kullanır. 

IoT Hub bölümünün ilk bölümü aşağıda verilmiştir. Şablonun bu bölümü bağımlılıkları ayarlar ve özelliklerle başlar.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Sonraki bölüm, IoT Hub için ileti yönlendirme yapılandırması bölümüdür. Öncelikle bitiş noktaları için bölümdür. Şablonun bu bölümü, bağlantı dizeleri de dahil olmak üzere Service Bus kuyruğu ve depolama hesabı için yönlendirme uç noktalarını ayarlar.

Kuyruğun bağlantı dizesini oluşturmak için, satır içi alınan Queueauthorizationkurallarını Resourcedıd öğesine ihtiyacınız vardır. Depolama hesabı için bağlantı dizesi oluşturmak üzere birincil depolama anahtarını alır ve bağlantı dizesinin biçiminde kullanırsınız.

Uç nokta yapılandırması Ayrıca BLOB biçimini `AVRO` veya `JSON`olarak ayarladığınız yerdir.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Bu sonraki bölüm, uç noktalara ileti yolları içindir. Her uç nokta için bir küme bulunur, bu nedenle Service Bus kuyruğu ve diğeri depolama hesabı kapsayıcısı için bir tane vardır.

Depolamaya yönlendirilmekte olan iletilerin sorgu koşulunun `level="storage"`olduğunu ve Service Bus kuyruğuna yönlendirilmekte olan iletilerin sorgu koşulunun `level="critical"`olduğunu unutmayın.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Bu JSON, Hub için varsayılan bilgileri ve SKU 'YU içeren IoT Hub bölümünün geri kalanını gösterir.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Kaynaklar: Service Bus kuyruğu yetkilendirme kuralları

Service Bus kuyruğu yetkilendirme kuralı, Service Bus sırasının bağlantı dizesini almak için kullanılır. Service Bus ad alanı ve Service Bus sırasından önce oluşturulmadığından emin olmak için bir `dependsOn` yan tümcesi kullanır.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Kaynaklar: Tüketici grubu

Bu bölümde, Bu öğreticinin ikinci bölümünde Azure Stream Analytics tarafından kullanılacak IoT Hub verileri için bir tüketici grubu oluşturacaksınız.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Kaynaklar: çıktılar

Görüntülenecek dağıtım betiğine bir değer göndermek istiyorsanız bir çıkış bölümü kullanırsınız. Şablonun bu bölümü Service Bus kuyruğu için bağlantı dizesini döndürür. Bir değer döndürülmesi gerekli değildir, bu, sonuçların çağırma betiğine nasıl döndürüldüğünü gösteren bir örnek olarak eklenmiştir.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>RM şablonunu dağıtma

Şablonu Azure 'a dağıtmak için şablonu ve parametreler dosyasını Azure Cloud Shell yükleyin ve ardından şablonu dağıtmak için bir komut dosyası yürütün. Azure Cloud Shell açın ve oturum açın. Bu örnek PowerShell kullanır.

Dosyaları karşıya yüklemek için, menü çubuğunda **dosyaları karşıya yükle/indir** simgesini seçin ve ardından karşıya yükle ' yi seçin.

![Karşıya yükle/Indir dosyaları vurgulanmış menü çubuğu Cloud Shell](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Yerel diskinizdeki dosyaları bulmak için açılan dosya gezginini kullanın ve sonra **Aç**' ı seçin.

Dosyalar karşıya yüklendikten sonra, bir sonuç iletişim kutusu aşağıdaki görüntüye benzer bir şey gösterir.

![Karşıya yükle/Indir dosyaları vurgulanmış menü çubuğu Cloud Shell](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Dosyalar, Cloud Shell örneğiniz tarafından kullanılan paylaşıma yüklenir. 

Dağıtımı gerçekleştirmek için betiği çalıştırın. Bu betiğin son satırı döndürülecek şekilde ayarlanan değişkeni alır--Service Bus kuyruğu bağlantı dizesi.

Betik, bu değişkenleri ayarlar ve kullanır:

**$RGName** , şablonun dağıtılacağı kaynak grubu adıdır. Bu alan, şablon dağıtılmadan önce oluşturulur.

**$Location** , şablon Için kullanılacak Azure konumudur (örneğin, "westus").

**DeploymentName** , döndürülen değişken değerini almak için dağıtıma atadığınız addır.

PowerShell betiği aşağıda verilmiştir. Bu PowerShell betiğini kopyalayın ve Cloud Shell penceresine yapıştırın ve sonra çalıştırmak için ENTER tuşuna basın.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Betik hatalarıyla karşılaşırsanız, betiği yerel olarak düzenleyebilir, Cloud Shell yeniden karşıya yükleyebilir ve betiği yeniden çalıştırabilirsiniz. Betik başarıyla çalışmaya başladıktan sonra, bir sonraki adımla devam edin.

## <a name="create-simulated-device"></a>Sanal cihaz oluşturma

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Portalda ileti yönlendirmeyi görüntüleme

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tüm kaynakları ayarlamış olduğunuza ve ileti yolları yapılandırıldıktan sonra, yönlendirilen iletilerle ilgili bilgileri nasıl işleyeceğini ve görüntüleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Bölüm 2-ileti yönlendirme sonuçlarını görüntüleme](tutorial-routing-view-message-routing-results.md)
