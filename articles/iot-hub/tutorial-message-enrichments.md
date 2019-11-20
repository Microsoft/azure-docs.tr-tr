---
title: Öğretici-Azure IoT Hub ileti zenginleştirme kullanma
description: Azure IoT Hub iletileri için ileti zenginlerinin nasıl kullanılacağını gösteren öğretici
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 0dd6c410040eea9eb4039ab5da183cc0b6799493
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005757"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>Öğretici: Azure IoT Hub ileti zenginleştirme kullanma

*İleti zenginleştirmelerinin* iletileri belirlenen uç noktaya gönderilmeden önce ek bilgilerle iletileri *damgalamak* IoT Hub yeteneğidir. İleti zenginleştirmelerinin tek bir nedeni, aşağı akış işlemeyi basitleştirmek için kullanılabilecek verileri içermektir. Örneğin, cihaz ikizi etiketiyle cihaz telemetri iletileri zenginleştirilmesi, bu bilgilere yönelik cihaz ikizi API çağrıları yapmak için müşterilerin yükünü azaltabilir. Daha fazla bilgi için bkz. [iletinin Enzenginleştirmelerinin genel bakışı](iot-hub-message-enrichments-overview.md).

Bu öğreticide, Azure CLı 'yi kullanarak, iki farklı depolama kapsayıcısının işaret eden iki uç nokta da dahil olmak üzere kaynakları **ayarlayabilirsiniz.** Ardından, ileti **zenginleştirmelerinin yalnızca gelişmiş** depolama kapsayıcısı ile uç noktaya gönderilen iletilere uygulanacağını yapılandırmak için [Azure Portal](https://portal.azure.com) kullanırsınız. IoT Hub iletileri, her iki depolama kapsayıcılarına yönlendirilen gönderirsiniz. Yalnızca **zenginleştirilmiş** depolama kapsayıcısı için uç noktaya gönderilen iletiler de zenginleştirilebilir.

Bu öğreticiyi tamamlamak için gerçekleştireceğiniz görevler şunlardır:

**IoT Hub ileti zenginleştirme kullanma**
> [!div class="checklist"]
> * Azure CLı 'yı kullanarak, kaynakları oluşturma--bir IoT Hub, iki uç nokta içeren bir depolama hesabı ve Yönlendirme yapılandırması.
> * İleti zenginleştirme 'yi yapılandırmak için Azure portal kullanın.
> * Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulama çalıştırın.
> * Sonuçları görüntüleyin ve ileti sorgularının beklendiği gibi çalıştığını doğrulayın.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliğiniz olmalıdır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* [Visual Studio](https://www.visualstudio.com/)’yu yükleyin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Örnek kodu alma

[IoT cihaz benzetimini](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) indirin ve sıkıştırmayı açın. Bu depoda, IoT Hub 'ına ileti göndermek için kullanacağınız bir de dahil olmak üzere çeşitli uygulamalar vardır.

Bu indirme, ileti zenginleştirmelerini test etmek için kullanılan kaynakları oluşturmaya yönelik betiği de içerir. Betik/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azclı ' dir. Şimdilik, betiğe bakabilir ve onu kullanabilirsiniz. Ayrıca, komut dosyasını doğrudan makaleden kopyalayabilirsiniz.

Teste başlamaya hazırsanız, IoT Hub 'ınıza ileti göndermek için bu indirden cihaz benzetimi uygulamasını kullanacaksınız.

## <a name="set-up-and-configure-resources"></a>Kaynakları ayarlama ve yapılandırma

Azure CLı betiği, gerekli kaynakları oluşturmanın yanı sıra ayrı depolama kapsayıcıları olan uç noktalara iki yol da yapılandırır. Yönlendirmeyi yapılandırma hakkında daha fazla bilgi için bkz. [yönlendirme öğreticisi](tutorial-routing.md). Kaynaklar kurulduktan sonra, her bitiş noktası için ileti zenginlerinizi yapılandırmak üzere [Azure Portal](https://portal.azure.com) kullanır ve ardından test adımına geçin.

> [!NOTE]
> Tüm iletiler her iki uç noktaya yönlendirilir, ancak yalnızca yapılandırılan ileti zenginleştirmelerinin bulunduğu uç noktaya giden iletiler zenginleştirilebilir.
>

Aşağıdaki betiği kullanabilir veya betiği indirilen deponun/Resources klasöründe açabilirsiniz. Betiğinin gerçekleştireceği adımlar aşağıda verilmiştir:

* Bir IoT Hub oluşturma.
* Depolama hesabı oluşturma.
* Depolama hesabında iki kapsayıcı oluşturun; bir tane, zenginleştirilmiş iletiler ve diğeri de zenginleştirilmiş iletiler için.
* İki farklı depolama hesabı için yönlendirmeyi ayarlayın.
    * Her depolama hesabı kapsayıcısı için bir uç nokta oluşturun.
    * Her depolama hesabı kapsayıcısı uç noktası için bir yol oluşturun.

IoT Hub adı ve depolama hesabı adı gibi genel olarak benzersiz olması gereken birkaç kaynak adı vardır. Betiği daha kolay çalıştırmak için, bu kaynak adlarına rastgele *değer*adı verilen rastgele alfasayısal bir değer eklenir. Rasgeledeğeri, komut dosyasının en üstünde bir kez oluşturulur ve komut dosyası boyunca gerektiğinde kaynak adlarına eklenir. Rastgele olmasını istemiyorsanız, bunu boş bir dizeye veya belirli bir değere ayarlayabilirsiniz.

Daha önce yapmadıysanız, bir [Cloud Shell penceresi](https://shell.azure.com) açın ve Bash olarak ayarlandığından emin olun. Betiği sıkıştırmasız depoda açın, tümünü seçmek için CTRL-A, sonra da kopyalamak için CTRL-C kullanın. Alternatif olarak, aşağıdaki CLı betiğini kopyalayabilir veya doğrudan Cloud Shell ' de açabilirsiniz. Komut satırına sağ tıklayıp **Yapıştır**' ı seçerek betiği Cloud Shell penceresine yapıştırın. Betik tek seferde bir ifade çalıştırır. Betiği çalışmayı bitirdikten sonra, son komutu çalıştırdığından emin olmak için **ENTER** ' u seçin. Aşağıdaki kod bloğu, ne yaptığını açıklayan yorumlarla birlikte kullanılan betiği gösterir.

Komut dosyası tarafından oluşturulan kaynaklar aşağıda verilmiştir. **Zenginleştirme, kaynağın zenginleştirilmiş** iletiler için olduğu anlamına gelir. **Orijinal** , kaynağın zenginleştirilmiş iletiler için olduğu anlamına gelir.

| Ad | Değer |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| kapsayıcı adı | Özgün  |
| kapsayıcı adı | zenginleştirilmiş  |
| IoT cihaz adı | Contoso-test-cihaz |
| IoT Hub adı | ContosoTestHubMsgEn |
| depolama hesabı adı | contosostorage |
| uç nokta adı 1 | ContosoStorageEndpointOriginal |
| uç nokta adı 2 | ContosoStorageEndpointEnriched|
| yol adı 1 | Contosostoragerouteorjinal |
| yol adı 2 | Contosostoragerouerricric |

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Bu noktada, kaynakların hepsi ayarlanır ve yönlendirme yapılandırılır. İleti yönlendirme yapılandırmasını portalda görüntüleyebilir ve zenginleştirilmiş depolama kapsayıcısına giden iletiler için ileti **zenginleştirmelerinin** kurulumunu yapabilirsiniz.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Yönlendirmeyi görüntüleme ve ileti zenginleştirme yapılandırma

1. **Kaynak grupları**' nı seçerek IoT Hub gidin ve bu öğretici için ayarlanmış kaynak grubunu seçin (**ContosoResources_MsgEn**). Listede IoT Hub bulun ve seçin. IoT Hub 'ı için **Ileti yönlendirmeyi** seçin.

   ![İleti yönlendirmeyi seçin](./media/tutorial-message-enrichments/select-iot-hub.png)

   İleti yönlendirme bölmesinde üç sekme vardır-- **yollar**, **Özel uç noktalar**ve **zenginleştirme iletileri**. Komut dosyası tarafından ayarlanan yapılandırmayı görmek için ilk iki sekmeye gidebilirsiniz. İleti zenginleştirme eklemek için üçüncü sekmeyi kullanın. **Gelişmiş**olarak adlandırılan depolama kapsayıcısı için uç noktaya giden iletileri zenginleştirme. Ad ve değeri girin ve ardından açılır listeden **ContosoStorageEndpointEnriched** uç noktasını seçin. İşte IoT Hub adı ' na ekleyen bir zenginleştirme örneği aşağıda verilmiştir:

   ![İlk zenginleştirme Ekle](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Bu değerleri ContosoStorageEndpointEnriched uç noktası için listeye ekleyin.

   | Anahtar | Değer | Uç nokta (açılan liste) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | Azurestoraygecontainers > ContosoStorageEndpointEnriched |
   | deviceLocation | $twin.tags.location | Azurestoraygecontainers > ContosoStorageEndpointEnriched |
   |Ister | 6ce345b8-1e4a-411e-9398-d34587459a3a | Azurestoraygecontainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Cihazınızda bir ikizi yoksa, buraya yerleştirdiğiniz değer ileti zenginlerinin değeri için bir dize olarak damgalanacaktır. Cihaz ikizi bilgilerini görmek için portalda hub 'ınıza gidip **IoT cihazları**' nı seçin, cihazınızı seçin ve ardından sayfanın en üstündeki **cihaz ikizi** ' yi seçin.
   >
   > İkizi bilgilerini düzenleyerek etiketleri (konum gibi) ekleyebilir ve isterseniz belirli bir değere ayarlayabilirsiniz. Daha fazla bilgi için bkz. [IoT Hub'daki cihaz ikizlerini kavrama ve kullanma](iot-hub-devguide-device-twins.md)

3. İşiniz bittiğinde, bölmenizi şu resme benzer şekilde görünmelidir:

   ![Tüm enzenginler eklenen tablo](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Değişiklikleri kaydetmek için **Uygula** ' yı seçin.

## <a name="send-messages-to-the-iot-hub"></a>IoT Hub ileti gönderin

İleti zenginleştirmelerinin uç nokta için yapılandırıldığına göre, IoT Hub ileti göndermek için sanal cihaz uygulamasını çalıştırın. Hub, aşağıdakileri gerçekleştiren ayarlarla ayarlanmıştır:

* Depolama uç noktası ContosoStorageEndpointOriginal yönlendirilen iletiler uyumlu olmaz ve depolama kapsayıcısı `original`depolanır.

* Depolama uç noktası ContosoStorageEndpointEnriched yönlendirilen iletiler, `enriched`ve depolama kapsayıcısında depolanır.

Sanal cihaz uygulaması, sıkıştırılmış olmayan indirme uygulamalarınızdan biridir. Uygulama, [yönlendirme öğreticisindeki](tutorial-routing.md)farklı ileti yönlendirme yöntemlerinin her biri için ileti gönderir; Buna Azure Storage dahildir.

Kodu Visual Studio 'da açmak için çözüm dosyasına (IoT_SimulatedDevice. sln) çift tıklayın ve ardından Program.cs ' yi açın. İşaret `{your hub name}`için IoT Hub 'ı adını değiştirin. IoT Hub ana bilgisayar adının biçimi **{hub adınız}. Azure-Devices.net**. Bu öğreticide, hub ana bilgisayar adı **ContosoTestHubMsgEn.Azure-Devices.net**' dir. Sonra, işaretleyici `{your device key}`için kaynak oluşturmak üzere betiği çalıştırırken daha önce kaydettiğiniz Cihaz anahtarını değiştirin.

Cihaz anahtarınız yoksa portaldan alabilirsiniz. Oturum açtıktan sonra, **kaynak grupları**' na gidin, kaynak grubunuzu seçin ve IoT Hub seçin. Test cihazınız için **IoT cihazları** ' na bakın ve cihazınızı seçin. **Birincil anahtar** ' ın yanındaki Kopyala simgesini seçerek Pano 'ya kopyalayın.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Çalıştırma ve test etme

Konsol uygulamasını çalıştırın. Birkaç dakika bekleyin. Gönderilmekte olan iletiler, uygulamanın konsol ekranında görüntülenir.

Uygulama, IoT hub'ına her saniye yeni bir cihazdan buluta iletisi gönderir. İleti, cihaz kimliği, sıcaklık, nem düzeyi ve ileti düzeyi (varsayılan `normal` değeriyle) bilgileriyle bir JSON seri hale getirilmiş nesnesi içerir. Rastgele bir `critical` veya `storage`düzeyi atar, bu da iletinin depolama hesabına veya varsayılan uç noktaya yönlendirilmesine neden olur. Depolama hesabındaki **zenginleştirilmiş** kapsayıcıya gönderilen iletiler zenginleştirilebilir.

Birkaç depolama iletisi gönderildikten sonra, verileri görüntüleyin.

1. **Kaynak grupları**' nı seçin, sonra kaynak grubunuzu bulun (ContosoResourcesMsgEn) ve seçin.

2. Depolama hesabınızı (contosostorage) seçin. Ardından sol taraftaki seçim bölmesinden **Depolama Gezgini (Önizleme)** öğesini seçin.

   ![Depolama Gezginini seçin](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Kullanılabilecek iki kapsayıcıyı görmek için **BLOB kapsayıcıları** ' nı seçin.

   ![Depolama hesabındaki kapsayıcılara bakın](./media/tutorial-message-enrichments/show-blob-containers.png)

**Zenginleştirilmiş** olarak adlandırılan kapsayıcıda iletiler, iletilere eklenmiş olan ileti içerir. Kapsayıcıda **özgün** olarak adlandırılan iletiler, zenginleştirmesiz ham iletilere sahip olur. En Alta ulaşana ve en son ileti dosyasını açana kadar kapsayıcılardan birinin detayına gidin ve bu kapsayıcıdaki iletilere hiçbir zenginleştirme olmadığını doğrulamak için diğer kapsayıcı için aynısını yapın.

Zenginleştirilmiş iletilere göz atadığınızda, hub adına "My IoT Hub", ayrıca konum ve müşteri KIMLIĞI ile birlikte aşağıdaki gibi bir ileti görürsünüz:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Aşağıda, zenginleştirilmiş bir ileti verilmiştir. "My IoT Hub", "devicelocation" ve "CustomerID" burada gösterilmez, çünkü bunlar, enrichments tarafından eklenecek alanlardır ve bu uç noktanın hiç bir zenginleştirmesiz olması.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz tüm kaynakları kaldırmak istiyorsanız, kaynak grubunu silin. Bu eylem grubun içerdiği tüm kaynakları siler. Bu durumda, IoT Hub, depolama hesabı ve kaynak grubunun kendisini kaldırır.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLı kullanarak kaynakları Temizleme

Kaynak grubunu kaldırmak için [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) komutunu kullanın. `$resourceGroup`, Bu öğreticinin başlangıcında **Contosoresources** olarak ayarlanmıştır.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki adımları kullanarak ileti zenginleştirmelerinin IoT Hub iletileri ekleme ve test edilmiştir:

**IoT Hub ileti zenginleştirme kullanma**
> [!div class="checklist"]
> * Azure CLı 'yı kullanarak, kaynakları oluşturun--bir IoT Hub, iki uç noktaya sahip bir depolama hesabı ve Yönlendirme yapılandırması.
> * İleti zenginleştirme 'yi yapılandırmak için Azure portal kullanın.
> * Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulama çalıştırın.
> * Sonuçları görüntüleyin ve ileti sorgularının beklendiği gibi çalıştığını doğrulayın.

İleti zenginleştirmelerinin hakkında daha fazla bilgi için bkz. [iletinin enzenginleştirmelerinin Özeti](iot-hub-message-enrichments-overview.md).

İleti yönlendirme hakkında daha fazla bilgi için şu makalelere bakın:

* [Farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma](iot-hub-devguide-messages-d2c.md)

* [Öğretici: IoT Hub yönlendirme](tutorial-routing.md)