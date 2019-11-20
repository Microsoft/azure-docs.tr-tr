---
title: Azure PowerShell ile Azure IoT Hub için ileti yönlendirmeyi yapılandırma
description: Azure PowerShell kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırın. İletideki özelliklere bağlı olarak, bir depolama hesabına veya Service Bus kuyruğuna yönlendirin.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084432"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Öğretici: IoT Hub ileti yönlendirmeyi yapılandırmak için Azure PowerShell kullanma

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Betiği karşıdan yükle (isteğe bağlı)

Bu öğreticinin ikinci bölümünde, IoT Hub iletileri göndermek için bir Visual Studio uygulaması indirip çalıştırırsınız. İndirmenin, Azure Resource Manager şablonu ve parametreler dosyasının yanı sıra Azure CLı ve PowerShell betikleri içeren bir klasör vardır. 

Tamamlanmış betiği görüntülemek istiyorsanız, [Azure IoT C# örnekleri](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)' ni indirin. Master. zip dosyasını ayıklayın. Azure CLı betiği, **iothub_routing_psh. ps1**olarak/iot-hub/Tutorials/Routing/SimulatedDevice/resources/.

## <a name="create-your-resources"></a>Kaynaklarınızı oluşturun

PowerShell ile kaynakları oluşturarak başlayın.

### <a name="use-powershell-to-create-your-base-resources"></a>Temel kaynaklarınızı oluşturmak için PowerShell 'i kullanma

Aşağıdaki betiği kopyalayıp Cloud Shell yapıştırın ve ENTER tuşuna basın. Betiği tek seferde bir satır çalıştırır. Betiğin bu ilk bölümü, bu öğreticide depolama hesabı, IoT Hub, Service Bus ad alanı ve Service Bus kuyruğu dahil temel kaynakları oluşturur. Öğreticiye giderek, her bir komut bloğunu kopyalayıp çalıştırmak için Cloud Shell yapıştırın.

IoT Hub adı ve depolama hesabı adı gibi genel olarak benzersiz olması gereken birkaç kaynak adı vardır. Bunun daha kolay olması için, bu kaynak adlarına *rasgelevalue*adlı rastgele bir alfasayısal değer eklenir. Rasgeledeğeri, komut dosyasının en üstünde bir kez oluşturulur ve komut dosyası boyunca gerektiğinde kaynak adlarına eklenir. Rastgele olmasını istemiyorsanız, bunu boş bir dizeye veya belirli bir değere ayarlayabilirsiniz. 

> [!IMPORTANT]
> İlk betikte ayarlanan değişkenler de yönlendirme betiği tarafından kullanılır, bu nedenle tüm betiği aynı Cloud Shell oturumunda çalıştırın. Yönlendirmeyi ayarlamak için betiği çalıştırmak üzere yeni bir oturum açarsanız, bazı değişkenlerin değerleri eksik olur. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Sanal cihaz oluşturma

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Temel kaynaklar ayarlandığına göre, ileti yönlendirmeyi yapılandırabilirsiniz.

## <a name="set-up-message-routing"></a>İleti yönlendirmeyi ayarlama

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Bir yönlendirme uç noktası oluşturmak için [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint)kullanın. Uç nokta için mesajlaşma yolu oluşturmak üzere [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute)komutunu kullanın.

### <a name="route-to-a-storage-account"></a>Depolama hesabına yönlendirme 

İlk olarak, depolama hesabı için uç noktayı ayarlayın, ardından ileti yolunu oluşturun.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Bunlar, Cloud Shell oturumunuz dahilinde ayarlanması gereken komut dosyası tarafından kullanılan değişkenlerdir:

**resourceGroup**: Bu alanın iki oluşumu vardır: her ikisini de kaynak grubunuza ayarlayın.

**ad**: Bu alan, yönlendirmenin uygulanacağı IoT Hub adıdır.

**EndpointName**: Bu alan, uç noktayı tanımlayan addır. 

**EndpointType**: Bu alan bitiş noktası türüdür. Bu değer `azurestoragecontainer`, `eventhub`, `servicebusqueue`veya `servicebustopic`olarak ayarlanmalıdır. Burada amacınıza göre `azurestoragecontainer`olarak ayarlayın.

**SubscriptionID**: Bu alan, Azure hesabınız için SubscriptionID olarak ayarlanır.

**StorageConnectionString**: Bu değer, önceki betikte ayarlanan depolama hesabından alınır. Bu, depolama hesabına erişmek için yönlendirme tarafından kullanılır.

**ContainerName**: Bu alan, depolama hesabındaki, verilerin yazılacağı kapsayıcının adıdır.

**Kodlama**: bu alanı `AVRO` ya da `JSON`olarak ayarlayın. Bu, depolanan verilerin biçimini belirler. Varsayılan değer AVRO ' dir.

**RouteName**: Bu alan, ayarladığınız yolun adıdır. 

**koşul**: Bu alan, bu uç noktaya gönderilen iletileri filtrelemek için kullanılan sorgudur. Depolamaya yönlendirilmekte olan iletilerin sorgu koşulu `level="storage"`.

**etkin**: bu alan `true`varsayılan olarak, ileti yolunun oluşturulduktan sonra etkinleştirilmesi gerektiğini gösterir.

Bu betiği kopyalayın ve Cloud Shell pencerenize yapıştırın.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Sonraki adım, depolama hesabı için yönlendirme uç noktası oluşturmaktır. Ayrıca, sonuçların depolanacağı kapsayıcıyı da belirtirsiniz. Depolama hesabı oluşturulduğunda kapsayıcı oluşturulmuştur.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Ardından, depolama uç noktası için ileti yolunu oluşturun. İleti yolu, sorgu belirtimini karşılayan iletilerin nereye gönderileceğini belirtir.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Service Bus kuyruğuna yönlendirme

Şimdi Service Bus kuyruğu için yönlendirmeyi ayarlayın. Service Bus sırasının bağlantı dizesini almak için, doğru haklara sahip bir yetkilendirme kuralı oluşturmanız gerekir. Aşağıdaki betik, `sbauthrule`adlı Service Bus kuyruğu için bir yetkilendirme kuralı oluşturur ve hakları `Listen Manage Send`olarak ayarlar. Bu yetkilendirme kuralı kurulduktan sonra, kuyruğa yönelik bağlantı dizesini almak için bu ayarı kullanabilirsiniz.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Şimdi Service Bus sıra anahtarını almak için yetkilendirme kuralını kullanın. Bu yetkilendirme kuralı, bağlantı dizesini daha sonra komut dosyasında almak için kullanılacaktır.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Şimdi Service Bus kuyruğu için yönlendirme uç noktasını ve ileti yolunu ayarlayın. Bunlar, Cloud Shell oturumunuz dahilinde ayarlanması gereken komut dosyası tarafından kullanılan değişkenlerdir:

**EndpointName**: Bu alan, uç noktayı tanımlayan addır. 

**EndpointType**: Bu alan bitiş noktası türüdür. Bu değer `azurestoragecontainer`, `eventhub`, `servicebusqueue`veya `servicebustopic`olarak ayarlanmalıdır. Burada amacınıza göre `servicebusqueue`olarak ayarlayın.

**RouteName**: Bu alan, ayarladığınız yolun adıdır. 

**koşul**: Bu alan, bu uç noktaya gönderilen iletileri filtrelemek için kullanılan sorgudur. Service Bus kuyruğuna yönlendirilmekte olan iletilerin sorgu koşulu `level="critical"`.

Service Bus kuyruğu için ileti yönlendirmenin Azure PowerShell aşağıda verilmiştir.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Portalda ileti yönlendirmeyi görüntüleme

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları ayarlamış olduğunuza ve ileti rotalarına sahip olduğunuza göre, IoT Hub 'ına ileti gönderme ve farklı hedeflere yönlendirilme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
> [Bölüm 2-ileti yönlendirme sonuçlarını görüntüleme](tutorial-routing-view-message-routing-results.md)