---
title: Öğretici-Azure IoT Hub ileti zenginleştirme kullanın
description: Azure IoT Hub iletileri için ileti zenginlerinin nasıl kullanılacağını gösteren öğretici
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788880"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Öğretici: Azure IoT Hub ileti zenginleştirme kullanın

*İleti zenginleştirmelerinin* , Azure IoT Hub, iletiler belirlenen uç noktaya gönderilmeden önce ek bilgilerle iletileri *damgalama* özelliğini açıklar. İleti zenginleştirmelerinin tek bir nedeni, aşağı akış işlemeyi basitleştirmek için kullanılabilecek verileri içermektir. Örneğin, cihaz ikizi etiketiyle cihaz telemetri iletileri zenginleştirilmesi, bu bilgilere yönelik cihaz ikizi API çağrıları yapmak için müşterilerin yükünü azaltabilir. Daha fazla bilgi için bkz. [ileti zenginlerinin Özeti](iot-hub-message-enrichments-overview.md).

Bu öğreticide, bir IoT Hub 'ına ait ileti zenginleştirmelerinin test etmek için gereken kaynakları oluşturmak ve yapılandırmak için iki yol görürsünüz. Kaynaklar, iki depolama kapsayıcısı olan bir depolama hesabı içerir. Bir kapsayıcı, zenginleştirilmiş iletileri barındırır ve başka bir kapsayıcı özgün iletileri barındırır. Ayrıca, iletileri alacak bir IoT Hub 'ına ait olup, zenginleştirildiğini ve bunlara göre uygun depolama kapsayıcısına yönlendirirler.

* İlk yöntem, kaynakları oluşturmak ve ileti yönlendirmeyi yapılandırmak için Azure CLı 'yi kullanmaktır. Daha sonra [Azure Portal](https://portal.azure.com)kullanarak zenginleştirme 'yi el ile tanımlarsınız.

* İkinci yöntem, ileti yönlendirme ve ileti zenginlerinin *hem kaynaklarını hem de yapılandırmasını* oluşturmak için bir Azure Resource Manager şablonu kullanmaktır.

İleti yönlendirme ve ileti zenginleştirmelerinin yapılandırması bittiğinde, IoT Hub 'ına ileti göndermek için bir uygulama kullanırsınız. Hub daha sonra bunları her iki depolama kapsayıcılarına yönlendirir. Yalnızca **zenginleştirilmiş** depolama kapsayıcısı için uç noktaya gönderilen iletiler zenginleştirilmiş.

Bu öğreticiyi tamamlamak için gerçekleştirdiğiniz görevler şunlardır:

**IoT Hub ileti zenginleştirme kullanın**
> [!div class="checklist"]
> * İlk Yöntem: Azure CLı kullanarak kaynak oluşturma ve ileti yönlendirmeyi yapılandırma. [Azure Portal](https://portal.azure.com)kullanarak iletiyi zenginleştirerek el ile yapılandırın.
> * İkinci yöntem: Kaynak Yöneticisi şablonu kullanarak kaynak oluşturun ve ileti yönlendirmeyi ve ileti zenginleştirmelerinin yapılandırın. 
> * Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulama çalıştırın.
> * Sonuçları görüntüleyin ve iletinin zenginleştirildiği gibi çalıştığını doğrulayın.

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliğiniz olmalıdır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- [Visual Studio 'yu](https://www.visualstudio.com/)yükler.

- Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu öğreticideki cihaz örneği, 8883 numaralı bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>IoT C# örnek deposunu alma

GitHub 'dan [IoT C# örneklerini](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) indirin ve bunları ayıklayın. Bu depoda, içindeki çeşitli uygulamalar, betikler ve Kaynak Yöneticisi şablonlar bulunur. Bu öğretici için kullanılacak olanlar şunlardır:

* El ile yönteminde, kaynakları oluşturmak için kullanılan bir CLı betiği vardır. Bu betik/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azclı ' dir. Bu betik, kaynakları oluşturur ve ileti yönlendirmeyi yapılandırır. Bu betiği çalıştırdıktan sonra, [Azure Portal](https://portal.azure.com)kullanarak iletiyi zenginleştirerek el ile oluşturun.
* Otomatikleştirilmiş Yöntem için bir Azure Resource Manager şablonu vardır. Şablon/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.js. Bu şablon, kaynakları oluşturur, ileti yönlendirmeyi yapılandırır ve ardından ileti zenginleştirme 'yı yapılandırır.
* Kullandığınız üçüncü uygulama, IoT Hub 'ına ileti göndermek ve ileti zenginleştirmelerinin test etmek için kullandığınız cihaz benzetim uygulamasıdır.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Azure CLı kullanarak el ile ayarlama ve yapılandırma

Azure CLı betiği, gerekli kaynakları oluşturmanın yanı sıra ayrı depolama kapsayıcıları olan uç noktalara iki yol da yapılandırır. İleti yönlendirmeyi yapılandırma hakkında daha fazla bilgi için bkz. [yönlendirme öğreticisi](tutorial-routing.md). Kaynaklar kurulduktan sonra, her bitiş noktası için ileti zenginlerinizi yapılandırmak üzere [Azure Portal](https://portal.azure.com) kullanın. Sonra test adımına devam edin.

> [!NOTE]
> Tüm iletiler her iki uç noktaya yönlendirilir, ancak yalnızca yapılandırılan ileti zenginleştirmelerinin bulunduğu uç noktaya giden iletiler zenginleştirilebilir.
>

Aşağıdaki betiği kullanabilir veya betiği indirilen deponun/Resources klasöründe açabilirsiniz. Betik aşağıdaki adımları gerçekleştirir:

* IoT Hub 'ı oluşturun.
* Depolama hesabı oluşturma.
* Depolama hesabında iki kapsayıcı oluşturun. Tek bir kapsayıcı, zenginleştirilmiş iletiler için, başka bir kapsayıcı ise zenginleştirilmiş iletiler içindir.
* İki farklı depolama hesabı için yönlendirmeyi ayarlayın:
    * Her depolama hesabı kapsayıcısı için bir uç nokta oluşturun.
    * Her depolama hesabı kapsayıcısı uç noktası için bir yol oluşturun.

IoT Hub adı ve depolama hesabı adı gibi genel olarak benzersiz olması gereken birkaç kaynak adı vardır. Betiği daha kolay çalıştırmak için, bu kaynak adlarına rastgele *değer* adı verilen rastgele alfasayısal bir değer eklenir. Rastgele değer, betiğin en üstünde bir kez oluşturulur. Bu, komut dosyası boyunca gerektiği şekilde kaynak adlarına eklenir. Değerin rastgele olmasını istemiyorsanız, bunu boş bir dizeye veya belirli bir değere ayarlayabilirsiniz.

Daha önce yapmadıysanız, bir Azure [Cloud Shell penceresi](https://shell.azure.com) açın ve Bash olarak ayarlandığından emin olun. Sıkıştırılmış depodaki betiği açın, tümünü seçmek için CTRL + A ' yı seçin ve ardından kopyalamak için CTRL + C ' yi seçin. Alternatif olarak, aşağıdaki CLı betiğini kopyalayabilir veya doğrudan Cloud Shell ' de açabilirsiniz. Komut satırına sağ tıklayıp **Yapıştır**' ı seçerek betiği Cloud Shell penceresine yapıştırın. Betik tek seferde bir ifade çalıştırır. Betiği çalışmayı bitirdikten sonra, son komutu çalıştırdığından emin olmak için **ENTER** ' u seçin. Aşağıdaki kod bloğu, ne yaptığını açıklayan yorumlarla birlikte kullanılan betiği gösterir.

Komut dosyası tarafından oluşturulan kaynaklar aşağıda verilmiştir. *Zenginleştirme* , kaynağın enzenginler içeren iletiler için olduğu anlamına gelir. *Özgün* , kaynağın zenginleştirilmiş iletiler için olduğu anlamına gelir.

| Name | Değer |
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
az extension add --name azure-iot

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

Bu noktada, kaynakların hepsi ayarlanır ve ileti yönlendirme yapılandırılır. İleti yönlendirme yapılandırmasını portalda görüntüleyebilir ve zenginleştirilmiş depolama kapsayıcısına giden iletiler için ileti **zenginleştirmelerinin** kurulumunu yapabilirsiniz.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Azure portal kullanarak ileti zenginleştirme 'yi el ile yapılandırın

1. **Kaynak grupları**' nı seçerek IoT Hub 'ınıza gidin. Daha sonra bu öğretici için ayarlanmış kaynak grubunu seçin (**Contosoresourcesmsgen**). Listede IoT Hub 'ını bulun ve seçin. IoT Hub 'ı için **ileti yönlendirmeyi** seçin.

   ![İleti yönlendirmeyi seçin](./media/tutorial-message-enrichments/select-iot-hub.png)

   İleti yönlendirme bölmesinde **yollar**, **Özel uç noktalar** ve **zenginleştirme iletileri** etiketli üç sekme vardır. Komut dosyası tarafından ayarlanan yapılandırmayı görmek için ilk iki sekmeye gidin. İleti zenginleştirme eklemek için üçüncü sekmeyi kullanın. **Gelişmiş** olarak adlandırılan depolama kapsayıcısı için uç noktaya giden iletileri zenginleştirme. Ad ve değeri girin ve ardından açılır listeden **ContosoStorageEndpointEnriched** uç noktasını seçin. Aşağıda, IoT Hub adını iletiye ekleyen bir zenginleştirme hakkında örnek verilmiştir:

   ![İlk zenginleştirme Ekle](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Bu değerleri ContosoStorageEndpointEnriched uç noktası için listeye ekleyin.

   | Anahtar | Değer | Uç nokta (açılan liste) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | Azurestoraygecontainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. Location | Azurestoraygecontainers > ContosoStorageEndpointEnriched |
   |Ister | 6ce345b8-1e4a-411E-9398-d34587459a3a | Azurestoraygecontainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Cihazınızda bir ikizi yoksa, buraya yerleştirdiğiniz değer ileti zenginleştirme değeri için bir dize olarak damgalanacaktır. Cihaz ikizi bilgilerini görmek için portalda hub 'ınıza gidip **IoT cihazları**' nı seçin. Cihazınızı seçin ve sayfanın üst kısmındaki **Device ikizi** ' ı seçin.
   >
   > İkizi bilgilerini, konum gibi Etiketler eklemek ve belirli bir değere ayarlamak için düzenleyebilirsiniz. Daha fazla bilgi için bkz. [IoT Hub cihaz TWINS 'ı anlama ve kullanma](iot-hub-devguide-device-twins.md).

3. İşiniz bittiğinde, bölmenizi şu resme benzer şekilde görünmelidir:

   ![Tüm enzenginler eklenen tablo](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Değişiklikleri kaydetmek için **Uygula** ' yı seçin. [Test iletisi zenginleştirme](#test-message-enrichments) bölümüne atlayın.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak oluşturma ve yapılandırma
Kaynakları, ileti yönlendirmeyi ve ileti zenginleştirme kaynaklarını oluşturmak ve yapılandırmak için bir Kaynak Yöneticisi şablonu kullanabilirsiniz.

1. Azure portalında oturum açın. Arama kutusunu açmak için **+ kaynak oluştur** ' u seçin. *Şablon dağıtımını* girin ve arama yapın. Sonuçlar bölmesinde **şablon dağıtımı (özel şablon kullanarak Dağıt)** öğesini seçin.

   ![Azure portal Şablon dağıtımı](./media/tutorial-message-enrichments/template-select-deployment.png)

1. **Şablon dağıtımı** bölmesinde **Oluştur** ' u seçin.

1. **Özel dağıtım** bölmesinde, **düzenleyicide kendi şablonunuzu oluştur**' u seçin.

1. **Şablonu Düzenle** bölmesinde **Dosya Yükle**' yi seçin. Windows Gezgini görüntülenir. **/İot-hub/Tutorials/Routing/SimulatedDevice/Resources**' deki sıkıştırılmış Depo dosyasında dosya **template_messageenrichments.js** bulun. 

   ![Yerel makineden şablon seç](./media/tutorial-message-enrichments/template-select.png)

1. Şablon dosyasını yerel makineden yüklemek için **Aç** ' ı seçin. Yükler ve Düzenleme bölmesinde görünür.

   Bu şablon, varsayılan adların sonuna rastgele bir değer ekleyerek bir genel benzersiz IoT Hub adı ve depolama hesabı adı kullanacak şekilde ayarlanır, böylece şablonu hiçbir değişiklik yapmadan kullanabilirsiniz.

   Şablonu yükleyerek oluşturulan kaynaklar aşağıda verilmiştir. **Zenginleştirme** , kaynağın enzenginler içeren iletiler için olduğu anlamına gelir. **Özgün** , kaynağın zenginleştirilmiş iletiler için olduğu anlamına gelir. Bunlar, Azure CLı betikte kullanılan aynı değerlerdir.

   | Name | Değer |
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

1. **Kaydet**’i seçin. **Özel dağıtım** bölmesi görüntülenir ve şablon tarafından kullanılan tüm parametreleri gösterir. Ayarlamanız gereken tek alan **kaynak grubudur**. Yeni bir tane oluşturun veya açılan listeden birini seçin.

   **Özel dağıtım** bölmesinin en üst yarısı aşağıda verilmiştir. Kaynak grubunu nerede doldurduğunuzdan bakabilirsiniz.

   ![Özel dağıtım bölmesinin üst yarısı](./media/tutorial-message-enrichments/template-deployment-top.png)

1. **Özel dağıtım** bölmesinin alt yarısı aşağıda verilmiştir. Parametrelerin geri kalanını ve hüküm ve koşulları görebilirsiniz. 

   ![Özel dağıtım bölmesinin alt yarısı](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Hüküm ve koşulları kabul etmek için onay kutusunu işaretleyin. Ardından, şablon dağıtımına devam etmek için **satın al** ' ı seçin.

1. Şablonun tam olarak dağıtılmasını bekleyin. İlerlemeyi denetlemek için ekranın üst kısmındaki zil simgesini seçin. İşiniz bittiğinde [test iletisi zenginleştirme](#test-message-enrichments) bölümüne devam edin.

## <a name="test-message-enrichments"></a>Test iletisi zenginleştirme

İletiyi zenginleştirme olarak görüntülemek için **kaynak grupları**' nı seçin. Daha sonra bu öğretici için kullanmakta olduğunuz kaynak grubunu seçin. Kaynak listesinden IoT Hub ' ı seçin ve **mesajlaşma**' a gidin. İleti yönlendirme yapılandırması ve yapılandırılan zenginler görüntülenir.

İleti zenginleştirmelerinin uç nokta için yapılandırıldığına göre, IoT Hub 'ına ileti göndermek için sanal cihaz uygulamasını çalıştırın. Merkez, aşağıdaki görevleri gerçekleştiren ayarlarla ayarlandı:

* Depolama uç noktasına yönlendirilen iletiler ContosoStorageEndpointOriginal uyumlu olmaz ve depolama kapsayıcısında depolanır `original` .

* Depolama uç noktası ContosoStorageEndpointEnriched yönlendirilen iletiler, depolama kapsayıcısına zenginleştirilirken ve depolanacaktır `enriched` .

Sanal cihaz uygulaması, sıkıştırılmış olmayan indirme uygulamalarınızdan biridir. Uygulama, Azure depolama 'yı içeren [yönlendirme öğreticisinde](tutorial-routing.md)farklı ileti yönlendirme yöntemlerinin her biri için iletiler gönderir.

Visual Studio 'da kodu açmak için **IoT_SimulatedDevice. sln** çözüm dosyasına çift tıklayın ve ardından **program. cs** dosyasını açın. İşaretleyici için IoT Hub 'ı adını değiştirin `{your hub name}` . IoT Hub ana bilgisayar adının biçimi **{hub adınız}. Azure-Devices.net**. Bu öğreticide, hub ana bilgisayar adı ContosoTestHubMsgEn.azure-devices.net ' dir. Daha sonra, işaretleyici için kaynak oluşturmak üzere betiği çalıştırdığınızda daha önce kaydettiğiniz Cihaz anahtarını değiştirin `{your device key}` .

Cihaz anahtarınız yoksa portaldan alabilirsiniz. Oturum açtıktan sonra, **kaynak grupları**' na gidin, kaynak grubunuzu seçin ve ardından IoT Hub 'ınızı seçin. Test cihazınız için **IoT cihazları** ' na bakın ve cihazınızı seçin. **Birincil anahtar** ' ın yanındaki Kopyala simgesini seçerek Pano 'ya kopyalayın.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Çalıştırma ve test etme

Birkaç dakika boyunca konsol uygulamasını çalıştırın. Gönderilmekte olan iletiler, uygulamanın konsol ekranında görüntülenir.

Uygulama, IoT hub'ına her saniye yeni bir cihazdan buluta iletisi gönderir. İleti, cihaz kimliği, sıcaklık, nem düzeyi ve ileti düzeyi (varsayılan `normal` değeriyle) bilgileriyle bir JSON seri hale getirilmiş nesnesi içerir. Rastgele bir veya düzeyi atar ve `critical` Bu `storage` , iletinin depolama hesabına veya varsayılan uç noktaya yönlendirilmesine neden olur. Depolama hesabındaki **zenginleştirilmiş** kapsayıcıya gönderilen iletiler zenginleştirilebilir.

Birkaç depolama iletisi gönderildikten sonra verileri görüntüleyin.

1. **Kaynak grupları**’nı seçin. **Contosoresourcesmsgen** kaynak grubunuzu bulun ve seçin.

2. **Contosostorage** olan depolama hesabınızı seçin. Sonra sol bölmedeki **Depolama Gezgini (Önizleme)** öğesini seçin.

   ![Depolama Gezgini seçin](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Kullanılabilecek iki kapsayıcıyı görmek için **BLOB kapsayıcıları** ' nı seçin.

   ![Depolama hesabındaki kapsayıcılara bakın](./media/tutorial-message-enrichments/show-blob-containers.png)

**Zenginleştirilmiş** olarak adlandırılan kapsayıcıda iletiler, iletilere eklenmiş olan ileti içerir. **Özgün** olarak adlandırılan kapsayıcıdaki iletiler, zenginleştirmesiz ham iletilere sahiptir. En Alta ulaşana kadar kapsayıcılardan birinin detayına gidin ve en son ileti dosyasını açın. Ardından, bu kapsayıcıdaki iletilere hiçbir zenginleştirme bulunmadığını doğrulamak için diğer kapsayıcı için aynısını yapın.

Zenginleştirilmiş iletilere göz atadığınızda, hub adı ve konum ve müşteri KIMLIĞI ile birlikte "My IoT Hub" iletisini şöyle görürsünüz:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Aşağıda, zenginleştirilmiş bir ileti bulabilirsiniz. "My IoT Hub," "devicelocation," ve "CustomerID" burada gösterilmediğine dikkat edin çünkü bu alanlar en zenginler tarafından eklenir. Bu uç noktada hiç bir zenginleştirme yok.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz tüm kaynakları kaldırmak için kaynak grubunu silin. Bu eylem grubun içerdiği tüm kaynakları siler. Bu durumda, IoT Hub, depolama hesabı ve kaynak grubunun kendisini kaldırır.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLı kullanarak kaynakları Temizleme

Kaynak grubunu kaldırmak için [az group delete](/cli/azure/group#az_group_delete) komutunu kullanın. `$resourceGroup`Bu öğreticinin başlangıcında **Contosoresourcesmsgen** olarak ayarlandığını geri çekin.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki adımları kullanarak ileti zenginleştirmelerinin IoT Hub iletileri ekleme ve test edilmiştir:

**IoT Hub ileti zenginleştirme kullanın**

> [!div class="checklist"]
> * İlk Yöntem: Azure CLı kullanarak kaynak oluşturma ve ileti yönlendirmeyi yapılandırma. [Azure Portal](https://portal.azure.com)kullanarak iletiyi zenginleştirerek el ile yapılandırın.
> * İkinci yöntem: bir Azure Resource Manager şablonu kullanarak kaynak oluşturun ve ileti yönlendirmeyi ve ileti zenginleştirmelerinin yapılandırın.
> * Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulama çalıştırın.
> * Sonuçları görüntüleyin ve iletinin zenginleştirildiği gibi çalıştığını doğrulayın.

İleti zenginleştirmelerinin hakkında daha fazla bilgi için bkz. [ileti zenginlerinin genel bakışı](iot-hub-message-enrichments-overview.md).

İleti yönlendirme hakkında daha fazla bilgi için şu makalelere bakın:

> [!div class="nextstepaction"]
> [Farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Öğretici: IoT Hub yönlendirme](tutorial-routing.md)