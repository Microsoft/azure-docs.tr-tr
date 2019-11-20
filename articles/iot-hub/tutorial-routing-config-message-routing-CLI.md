---
title: Azure CLı kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırma
description: Azure CLı kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırın. İletideki özelliklere bağlı olarak, bir depolama hesabına veya Service Bus kuyruğuna yönlendirin.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 340ea35bc3ed0c889a1a851da47f7e955116e103
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084457"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Öğretici: IoT Hub ileti yönlendirmeyi yapılandırmak için Azure CLı 'yi kullanma

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Betiği karşıdan yükle (isteğe bağlı)

Bu öğreticinin ikinci bölümünde, IoT Hub iletileri göndermek için bir Visual Studio uygulaması indirip çalıştırırsınız. İndirmenin, Azure Resource Manager şablonu ve parametreler dosyasının yanı sıra Azure CLı ve PowerShell betikleri içeren bir klasör vardır.

Tamamlanmış betiği görüntülemek istiyorsanız, [Azure IoT C# örnekleri](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)' ni indirin. Master. zip dosyasını ayıklayın. Azure CLı betiği, **iothub_routing_cli. azclı**olarak/iot-hub/Tutorials/Routing/SimulatedDevice/resources/.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Kaynaklarınızı oluşturmak için Azure CLı 'yi kullanma

Aşağıdaki betiği kopyalayıp Cloud Shell yapıştırın ve ENTER tuşuna basın. Betiği tek seferde bir satır çalıştırır. Betiğin bu ilk bölümü, bu öğreticide depolama hesabı, IoT Hub, Service Bus ad alanı ve Service Bus kuyruğu dahil temel kaynakları oluşturur. Öğreticinin geri kalanında ilerlettiğinizde her bir komut bloğunu kopyalayın ve çalıştırmak için Cloud Shell yapıştırın.

> [!TIP]
> Hata ayıklama hakkında ipucu: Bu betik, betiği daha okunabilir hale getirmek için devamlılık simgesini (ters eğik çizgi `\`) kullanır. Betiği çalıştırırken bir sorun yaşıyorsanız Cloud Shell oturumunuzun `bash` çalıştığından ve ters eğik çizgi dışında boşluk olmadığından emin olun.
> 

IoT Hub adı ve depolama hesabı adı gibi genel olarak benzersiz olması gereken birkaç kaynak adı vardır. Bunun daha kolay olması için, bu kaynak adlarına *rasgelevalue*adlı rastgele bir alfasayısal değer eklenir. Rasgeledeğeri, komut dosyasının en üstünde bir kez oluşturulur ve komut dosyası boyunca gerektiğinde kaynak adlarına eklenir. Rastgele olmasını istemiyorsanız, bunu boş bir dizeye veya belirli bir değere ayarlayabilirsiniz. 

> [!IMPORTANT]
> İlk betikte ayarlanan değişkenler de yönlendirme betiği tarafından kullanılır, bu nedenle tüm betiği aynı Cloud Shell oturumunda çalıştırın. Yönlendirmeyi ayarlamak için betiği çalıştırmak üzere yeni bir oturum açarsanız, bazı değişkenlerin değerleri eksik olur.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Temel kaynaklar ayarlandığına göre, ileti yönlendirmeyi yapılandırabilirsiniz.

## <a name="set-up-message-routing"></a>İleti yönlendirmeyi ayarlama

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Yönlendirme uç noktası oluşturmak için [az IoT Hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create)kullanın. Uç nokta için ileti yolu oluşturmak için [az IoT Hub Route Create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create)kullanın.

### <a name="route-to-a-storage-account"></a>Depolama hesabına yönlendirme

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

İlk olarak, depolama hesabı için uç noktayı ayarlayın ve ardından yolu ayarlayın. 

Bunlar, Cloud Shell oturumunuz dahilinde ayarlanması gereken komut dosyası tarafından kullanılan değişkenlerdir:

**StorageConnectionString**: Bu değer, önceki betikte ayarlanan depolama hesabından alınır. Bu, depolama hesabına erişmek için ileti yönlendirme tarafından kullanılır.

  **resourceGroup**: kaynak grubunun iki oluşumu vardır; bunları kaynak grubunuza ayarlayın.

**Endpoint SubscriptionID**: Bu alan, uç nokta Için Azure SubscriptionID olarak ayarlanır. 

**EndpointType**: Bu alan bitiş noktası türüdür. Bu değer `azurestoragecontainer`, `eventhub`, `servicebusqueue`veya `servicebustopic`olarak ayarlanmalıdır. Burada amacınıza göre `azurestoragecontainer`olarak ayarlayın.

**Iothubname**: Bu alan, yönlendirmeyi yapan hub 'ın adıdır.

**ContainerName**: Bu alan, depolama hesabındaki, verilerin yazılacağı kapsayıcının adıdır.

**kodlama**: bu alan `avro` ya da `json`olacaktır. Bu, depolanan verilerin biçimini gösterir.

**RouteName**: Bu alan, ayarladığınız yolun adıdır. 

**EndpointName**: Bu alan, uç noktayı tanımlayan addır. 

**etkin**: bu alan `true`varsayılan olarak, ileti yolunun oluşturulduktan sonra etkinleştirilmesi gerektiğini gösterir.

**koşul**: Bu alan, bu uç noktaya gönderilen iletileri filtrelemek için kullanılan sorgudur. Depolamaya yönlendirilmekte olan iletilerin sorgu koşulu `level="storage"`.

Bu betiği kopyalayın ve Cloud Shell pencerenize yapıştırın ve çalıştırın.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Sonraki adım, depolama hesabı için yönlendirme uç noktası oluşturmaktır. Ayrıca, sonuçların depolanacağı kapsayıcıyı da belirtirsiniz. Kapsayıcı, depolama hesabı oluşturulduğunda daha önce oluşturulmuştur.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Ardından, depolama uç noktası için yol oluşturun. İleti yolu, sorgu belirtimini karşılayan iletilerin nereye gönderileceğini belirtir. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Service Bus kuyruğuna yönlendirme

Şimdi Service Bus kuyruğu için yönlendirmeyi ayarlayın. Service Bus sırasının bağlantı dizesini almak için, doğru haklara sahip bir yetkilendirme kuralı oluşturmanız gerekir. Aşağıdaki betik, `sbauthrule`adlı Service Bus kuyruğu için bir yetkilendirme kuralı oluşturur ve hakları `Listen Manage Send`olarak ayarlar. Bu yetkilendirme kuralı tanımlandıktan sonra, kuyruğa yönelik bağlantı dizesini almak için kullanabilirsiniz.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Şimdi Service Bus kuyruğuna bağlantı dizesini almak için yetkilendirme kuralını kullanın.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Şimdi Service Bus kuyruğu için yönlendirme uç noktasını ve ileti yolunu ayarlayın. Bunlar, Cloud Shell oturumunuz dahilinde ayarlanması gereken komut dosyası tarafından kullanılan değişkenlerdir:

**EndpointName**: Bu alan, uç noktayı tanımlayan addır. 

**EndpointType**: Bu alan bitiş noktası türüdür. Bu değer `azurestoragecontainer`, `eventhub`, `servicebusqueue`veya `servicebustopic`olarak ayarlanmalıdır. Burada amacınıza göre `servicebusqueue`olarak ayarlayın.

**RouteName**: Bu alan, ayarladığınız yolun adıdır. 

**koşul**: Bu alan, bu uç noktaya gönderilen iletileri filtrelemek için kullanılan sorgudur. Service Bus kuyruğuna yönlendirilmekte olan iletilerin sorgu koşulu `level="critical"`.

Yönlendirme uç noktası için Azure CLı ve Service Bus kuyruğu için ileti rotası aşağıda verilmiştir.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Portalda ileti yönlendirmeyi görüntüleme

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları ayarlamış olduğunuza ve ileti rotalarına sahip olduğunuza göre, IoT Hub 'ına ileti gönderme ve farklı hedeflere yönlendirilme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
> [Bölüm 2-ileti yönlendirme sonuçlarını görüntüleme](tutorial-routing-view-message-routing-results.md)
