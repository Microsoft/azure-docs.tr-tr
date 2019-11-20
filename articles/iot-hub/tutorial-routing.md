---
title: Azure CLı kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırma
description: Azure CLı ve Azure portal kullanarak Azure IoT Hub için ileti yönlendirmeyi yapılandırma
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084379"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Öğretici: IoT Hub ileti yönlendirmeyi yapılandırmak için Azure CLı ve Azure portal kullanma

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Temel kaynakları oluşturmak için Azure CLı 'yi kullanma

Bu öğretici, temel kaynakları oluşturmak için Azure CLı 'yi kullanır, ardından ileti yönlendirmeyi yapılandırmayı ve test için sanal cihazı ayarlamayı göstermek üzere [Azure Portal](https://portal.azure.com) kullanır.

Aşağıdaki betiği kopyalayıp Cloud Shell yapıştırın ve ENTER tuşuna basın. Betiği tek seferde bir satır çalıştırır. Bu işlem, depolama hesabı, IoT Hub, Service Bus ad alanı ve Service Bus kuyruğu dahil olmak üzere Bu öğreticinin temel kaynaklarını oluşturur.

IoT Hub adı ve depolama hesabı adı gibi genel olarak benzersiz olması gereken birkaç kaynak adı vardır. Bunun daha kolay olması için, bu kaynak adlarına *rasgelevalue*adlı rastgele bir alfasayısal değer eklenir. Rasgeledeğeri, komut dosyasının en üstünde bir kez oluşturulur ve komut dosyası boyunca gerektiğinde kaynak adlarına eklenir. Rastgele olmasını istemiyorsanız, bunu boş bir dizeye veya belirli bir değere ayarlayabilirsiniz.

> [!TIP]
> Hata ayıklama hakkında ipucu: Bu betik, betiği daha okunabilir hale getirmek için devamlılık simgesini (ters eğik çizgi `\`) kullanır. Betiği çalıştırırken bir sorun yaşıyorsanız Cloud Shell oturumunuzun `bash` çalıştığından ve ters eğik çizgi dışında boşluk olmadığından emin olun.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Temel kaynaklar ayarlandığına göre, [Azure Portal](https://portal.azure.com)ileti yönlendirmeyi yapılandırabilirsiniz.

## <a name="set-up-message-routing"></a>İleti yönlendirmeyi ayarlama

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Depolama hesabına yönlendirme

Şimdi depolama hesabı için yönlendirmeyi ayarlayın. İleti Yönlendirme bölmesine gider ve bir yol eklersiniz. Yolu eklerken, yol için yeni bir uç nokta tanımlayın. Bu yönlendirme ayarlandıktan sonra, **Level** özelliğinin **depolama** olarak ayarlandığı iletiler otomatik olarak bir depolama hesabına yazılır. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. [Azure Portal](https://portal.azure.com), **kaynak grupları**' nı ve ardından kaynak grubunuzu seçin. Bu öğreticide **ContosoResources** kullanılır.

2. Kaynak listesinin altındaki IoT Hub 'ını seçin. Bu öğreticide **ContosoTestHub** kullanılır.

3. **Ileti yönlendirmeyi**seçin. **Ileti yönlendirme** bölmesinde +**Ekle**' yi seçin. **Yol Ekle** bölmesinde, aşağıdaki resimde gösterildiği gibi desteklenen uç noktaları göstermek Için uç nokta alanının yanındaki +**Ekle** ' yi seçin:

   ![Bir yol için uç nokta eklemeye başlayın](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. **Blob depolama**'yı seçin. **Depolama uç noktası Ekle** bölmesini görürsünüz.

   ![Uç nokta ekleme](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Uç nokta için bir ad girin. Bu öğretici **Contosostorageendpoint**kullanır.

6. **Kapsayıcı Seç**' i seçin. Bu sizi depolama hesaplarınızın listesine götürür. Hazırlık adımlarında ayarladığınız hesabı seçin. Bu öğreticide **contosostorage** kullanılır. Söz konusu depolama hesabının içindeki kapsayıcıların listesi gösterilir. Hazırlık adımlarında ayarladığınız kapsayıcıyı **seçin** . Bu öğreticide **contosoresults** kullanılır. **Depolama uç noktası Ekle** bölmesine dönüp yaptığınız seçimleri görürsünüz.

7. Kodlamayı AVRO veya JSON olarak ayarlayın. Bu öğretici için kalan alanlarda varsayılan değerleri kullanın. Seçilen bölge JSON kodlamasını desteklemiyorsa, bu alan gri renkte görünür.

   > [!NOTE]
   > Blob adının biçimini **Blob dosya adı biçimi** ile ayarlayabilirsiniz. Varsayılan değer: `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Biçim {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} ve {mm} öğelerini içermelidir, sıralama farklı olabilir.
   >
   > Örneğin varsayılan blob dosya adı biçimini kullandığınızda hub adı ContosoTestHub ve tarih/saat 30 Ekim 2018 10:56 ise blob adı şu şekilde olacaktır: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Bloblar Avro biçiminde yazılır.
   >

8. Depolama uç noktasını oluşturmak ve rotaya eklemek için **Oluştur** ' u seçin. **Yol ekle** bölmesine dönersiniz.

9. Şimdi yönlendirme sorgusu bilgilerinin kalan kısmını tamamlayın. Bu sorgu, az önce uç nokta olarak eklediğiniz depolama kapsayıcısına ileti gönderme ölçütlerini belirtir. Ekrandaki alanları doldurun.

   **Ad**: Yönlendirme sorgunuz için bir ad girin. Bu öğretici **Contosostorageroute**kullanır.

   **Uç nokta**: Az önce ayarladığınız uç noktayı gösterir.

   **Veri kaynağı**: Açılan listeden **Cihaz Telemetri İletileri**'ni seçin.

   **Rotayı etkinleştir**: bu alanın `enabled`olarak ayarlandığından emin olun.
   
   **Yönlendirme sorgusu**: Sorgu dizesi olarak `level="storage"` girin.

   ![Depolama hesabı için yönlendirme sorgusu oluşturma](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   **Kaydet**’i seçin. İşlem bittiğinde İleti Yönlendirme bölmesine döner ve burada depolama için yeni yönlendirme sorgunuzu görebilirsiniz. Yollar bölmesini kapatın; Kaynak grubu sayfasına dönersiniz.

### <a name="route-to-a-service-bus-queue"></a>Service Bus kuyruğuna yönlendirme

Şimdi Service Bus kuyruğu için yönlendirmeyi ayarlayın. İleti Yönlendirme bölmesine gider ve bir yol eklersiniz. Yolu eklerken, yol için yeni bir uç nokta tanımlayın. Bu yol ayarlandıktan sonra, **Level** özelliğinin **kritik** olarak ayarlandığı Iletiler, bir mantıksal uygulamayı tetikleyen Service Bus kuyruğuna yazılır ve bu da bilgileri içeren bir e-posta gönderir.

1. Kaynak grubu sayfasında, IoT Hub 'ınızı seçip **Ileti yönlendirme**' yi seçin.

2. **Ileti yönlendirme** bölmesinde +**Ekle**' yi seçin.

3. **Yol Ekle** bölmesinde, uç nokta alanının yanındaki +**Ekle** ' yi seçin. **Service Bus Kuyruğu**'nu seçin. **Service Bus Uç Noktası Ekle** bölmesini görürsünüz.

   ![Service Bus uç noktası ekleme](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Şu alanları doldurun:

   **Uç Nokta Adı**: Uç nokta için bir ad girin. Bu öğretici **Contososbqueueendpoint**kullanır.
   
   **Service Bus ad alanı**: hazırlama adımlarında ayarladığınız Service Bus ad alanını seçmek için açılan listeyi kullanın. Bu öğreticide **ContosoSBNamespace** kullanılır.

   **Service Bus kuyruğu**: Service Bus kuyruğunu seçmek için açılan listeyi kullanın. Bu öğreticide **contososbqueue** kullanılır.

5. Service Bus kuyruğu uç noktasını eklemek için **Oluştur** ' u seçin. **Yol ekle** bölmesine dönersiniz.

6. Şimdi yönlendirme sorgusu bilgilerinin kalan kısmını tamamlarsınız. Bu sorgu, az önce uç nokta olarak eklediğiniz Service Bus kuyruğuna ileti gönderme ölçütlerini belirtir. Ekrandaki alanları doldurun. 

   **Ad**: Yönlendirme sorgunuz için bir ad girin. Bu öğretici **Contososbqueueroute**kullanır. 

   **Uç nokta**: Az önce ayarladığınız uç noktayı gösterir.

   **Veri kaynağı**: Açılan listeden **Cihaz Telemetri İletileri**'ni seçin.

   **Yönlendirme sorgusu**: Sorgu dizesi olarak `level="critical"` girin. 

   ![Service Bus kuyruğu için bir yönlendirme sorgusu oluşturma](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. **Kaydet**’i seçin. Yollar bölmesine dönüldüğünde, burada gösterildiği gibi yeni yönlendirme yollarınızın ikisini de görürsünüz.

   ![Az önce ayarladığınız rotalar](./media/tutorial-routing/message-routing-show-both-routes.png)

8. **Özel uç noktalar** sekmesini seçerek ayarladığınız özel uç noktaları görebilirsiniz.

   ![Yeni ayarladığınız özel uç nokta](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. İleti yönlendirme bölmesini kapatın; Kaynak grubu bölmesine dönersiniz.

## <a name="create-a-simulated-device"></a>Sanal cihaz oluşturma

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları ayarlamış olduğunuza ve ileti rotalarına sahip olduğunuza göre, IoT Hub 'ına ileti gönderme ve farklı hedeflere yönlendirilme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
> [Bölüm 2-ileti yönlendirme sonuçlarını görüntüleme](tutorial-routing-view-message-routing-results.md)
