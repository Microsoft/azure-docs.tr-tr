---
title: Azure IoT Hub ile ölçümleri ve tanılama günlüklerini ayarlama ve kullanma
description: Azure IoT Hub ile ölçümleri ve tanılama günlüklerini ayarlamayı ve kullanmayı öğrenin. Bu, hub 'ınızın sahip olabileceği sorunları tanılamanıza yardımcı olması için analiz edilecek verileri sağlar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 88101aacab8b4745ce6bc9180521e66500086edd
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084371"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Öğretici: IoT Hub ile ölçümleri ve tanılama günlüklerini ayarlama ve kullanma

Üretimde çalışan bir IoT Hub çözümünüz varsa, bazı ölçümleri ayarlamak ve tanılama günlüklerini etkinleştirmek istersiniz. Daha sonra bir sorun oluşursa, sorunu tanılamanıza ve daha hızlı düzelmenize yardımcı olacak veriye bakacaksınız. Bu makalede, tanılama günlüklerinin nasıl etkinleştirileceğini ve bunların hatalara karşı nasıl kontrol yükleneceğini göreceksiniz. Ayrıca, izlenecek bazı ölçümleri ve ölçümler belirli bir sınıra geldiğinde harekete gelen uyarıları da ayarlayabilirsiniz. Örneğin, gönderilen telemetri iletilerinin sayısı belirli bir sınırı aştığında veya kullanılan ileti sayısı IoT Hub için günde izin verilen iletilerin kotasına yakın olduğunda size gönderilen bir e-posta alabilirsiniz. 

Örnek kullanım örneği, pompalara 'nin IoT Hub ile iletişim Kuran IoT cihazları olduğu bir gaz istasyonlarıdır. Kredi kartları onaylanır ve son işlem bir veri deposuna yazılır. IoT cihazları hub 'a bağlanmayı ve ileti göndermeyi durıyorsa, nelerin açık olduğuna ilişkin bir görünürlük yoksa, düzeltilmesi çok daha zordur.

Bu öğretici, IoT Hub 'ına ileti göndermek için [IoT Hub yönlendirmenin](tutorial-routing.md) Azure örneğini kullanır.

Bu öğreticide, aşağıdaki görevleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Azure CLı kullanarak bir IoT Hub, sanal cihaz ve depolama hesabı oluşturun.  
> * Tanılama günlüklerini etkinleştirin.
> * Ölçümleri etkinleştirin.
> * Bu ölçümler için uyarıları ayarlayın. 
> * Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulamayı indirip çalıştırın. 
> * Uyarılar çalışmaya başlamadan önce uygulamayı çalıştırın. 
> * Ölçüm sonuçlarını görüntüleyin ve tanılama günlüklerini denetleyin. 

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- [Visual Studio](https://www.visualstudio.com/)’yu yükleyin. 

- Posta alan bir e-posta hesabı.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Kaynakları ayarlama

Bu öğreticide, bir IoT Hub, bir depolama hesabı ve sanal bir IoT cihazı gerekir. Bu kaynaklar Azure CLI veya Azure PowerShell kullanılarak oluşturulabilir. Tüm kaynaklar için aynı kaynak grubunu ve konumunu kullanın. Sonunda kaynak grubunu silerek her şeyi tek adımda kaldırabilirsiniz.

Bunlar gerekli adımlardır.

1. Bir [kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. 

2. IoT Hub 'ı oluşturun.

3. Standard_LRS çoğaltmasıyla standart bir V1 depolama hesabı oluşturun.

4. Hub'ınıza iletiler gönderen simülasyon cihazı için cihaz kimliği oluşturun. Test aşaması için anahtarı kaydedin.

### <a name="set-up-resources-using-azure-cli"></a>Azure CLı kullanarak kaynakları ayarlama

Bu betiği kopyalayıp Cloud Shell'e yapıştırın. Zaten oturum açmış olduğunuzu varsayarak, betiği bir kerede bir satır olmak üzere çalıştırır. Yeni kaynaklar ContosoResources kaynak grubunda oluşturulur.

Genel olarak benzersiz olması gereken değişkenlere `$RANDOM` eklenmiştir. Betik çalıştırıldığında ve değişkenler ayarlandığında, rastgele bir sayısal dize oluşturulur ve sabit dizenin sonuna eklenerek benzersiz hale getirilmesi sağlanır.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Cihaz kimliğini oluştururken şu hatayı alabilirsiniz: *IoT Hub ContosoTestHub ilke ıothubowner için anahtar bulunamadı*. Bu hatayı onarmak için Azure CLı IoT uzantısını güncelleştirin ve ardından komut dosyasında son iki komutu yeniden çalıştırın. 
>
>Uzantıyı güncelleştirme komutu aşağıda verilmiştir. Bunu Cloud Shell örneğiniz içinde çalıştırın.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Tanılama günlüklerini etkinleştirme 

Yeni bir IoT Hub oluşturduğunuzda [tanılama günlükleri](../azure-monitor/platform/resource-logs-overview.md) varsayılan olarak devre dışıdır. Bu bölümde, hub 'ınız için tanılama günlüklerini etkinleştirin.

1. İlk olarak, portalda hub 'ınız yoksa, **kaynak grupları** ' na tıklayın ve contoso-Resources kaynak grubu ' na tıklayın. Görünen kaynak listesinden hub 'ı seçin. 

2. IoT Hub dikey penceresindeki **izleme** bölümünü arayın. **Tanılama ayarları**'na tıklayın. 

   ![IoT Hub dikey penceresinin Tanılama Ayarları bölümünü gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Aboneliğin ve kaynak grubunun doğru olduğundan emin olun. **Kaynak türü**altında **Tümünü Seç**seçeneğinin işaretini kaldırın, sonra **IoT Hub**bulun ve denetleyin. ( *Tümünü yeniden Seç* ' in yanındaki onay işaretini koyar, tam olarak yoksayın.) **Kaynak**altında Hub adını seçin. Ekranınız şu görüntü gibi görünmelidir: 

   ![IoT Hub dikey penceresinin Tanılama Ayarları bölümünü gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Şimdi **tanılamayı aç**' a tıklayın. Tanılama ayarları bölmesi görüntülenir. Tanılama günlükleri ayarlarınızın adını "Diags-Hub" olarak belirtin.

5. **Arşivi bir depolama hesabına**çekin. 

   ![Bir depolama hesabına arşivlemek için tanılamayı ayarlamayı gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    **Yapılandırma** ' ya tıklayarak **bir depolama hesabı seçin** ekranını görüntüleyin, doğru bir (*contosostoragemon*) seçin ve Tanılama ayarları bölmesine dönmek için **Tamam** ' ı tıklatın. 

   ![Bir depolama hesabına arşivlemek için tanılama günlüklerinin ayarlanmasını gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. **Günlük**altında **bağlantıları** ve **cihaz telemetrisini**denetleyin ve her biri için **bekletme (gün)** ile 7 gün ayarlayın. Tanılama ayarları ekranımızda şu görüntüyle görünmeli:

   ![Son tanılama günlüğü ayarlarını gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Ayarları kaydetmek için **Kaydet**’e tıklayın. Tanılama ayarları bölmesini kapatın.

Daha sonra, tanılama günlüklerine baktığımızda, cihaz için bağlanma ve bağlantıyı kesme günlüğünü görebileceksiniz. 

## <a name="set-up-metrics"></a>Ölçümleri ayarlama 

Artık iletilerin hub 'a ne zaman gönderileceğini izlemek için bazı ölçümler ayarlayın. 

1. IoT Hub 'ın ayarlar bölmesinde, **izleme** bölümünde **ölçümler** seçeneğine tıklayın.

2. Ekranın üst kısmında, **son 24 saat (otomatik)** seçeneğine tıklayın. Görüntülenen açılır menüde **zaman aralığı**için **son 4 saat** ' i seçin ve **zaman parçalı yapısını** **1 dakika**, yerel saat olarak ayarlayın. Bu ayarları kaydetmek için **Uygula** ' ya tıklayın. 

   ![Ölçüm zaman ayarlarını gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Varsayılan olarak bir ölçüm girişi vardır. Kaynak grubunu varsayılan ve ölçüm ad alanı olarak bırakın. **Ölçüm** açılan listesinde, **gönderilen telemetri iletileri**' ni seçin. **Toplamayı** **Sum**olarak ayarlayın.

   ![Gönderilen telemetri iletileri için ölçüm eklemeyi gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Şimdi grafiğe başka bir ölçüm eklemek için **ölçüm Ekle** ' ye tıklayın. Kaynak grubunuzu (**ContosoTestHub**) seçin. **Ölçüm**altında, **kullanılan toplam ileti sayısını**seçin. **Toplama**için **Ort**' ı seçin. 

   Artık ekranınızda, *gönderilen telemetri iletileri*için küçültülmüş olan ölçüm ve ayrıca *kullanılan toplam ileti sayısı*için yeni ölçüm gösterilmektedir.

   ![Gönderilen telemetri iletileri için ölçüm eklemeyi gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   **Panoya sabitle**' ye tıklayın. Bu işlem, yeniden erişebilmek için Azure portal panoya sabitleyebilir. Panoya sabitleyemez, ayarlarınız korunmaz.

## <a name="set-up-alerts"></a>Uyarılar ayarlayın

Portalda hub 'a gidin. **Kaynak grupları**' na tıklayın, *contosoresources*' i ve sonra IoT Hub *ContosoTestHub*' yi seçin 

IoT Hub henüz [Azure izleyici ölçümlerine](/azure/azure-monitor/platform/data-collection#metrics) geçirilmedi; [Klasik uyarılar](/azure/azure-monitor/platform/alerts-classic.overview)kullanmanız gerekir.

1. **İzleme**altında **Uyarılar** ' a tıklayın, ana uyarı ekranını gösterir. 

   ![Klasik uyarıların nasıl bulunacağını gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Buradan klasik uyarılara ulaşmak için **Klasik Uyarıları görüntüle**' ye tıklayın. 

    ![Klasik uyarılar ekranını gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Şu alanları doldurun: 

    **Abonelik**: bu alanı geçerli aboneliğiniz olarak bırakın.

    **Kaynak**: bu alanı *ölçümler*olarak ayarlayın.

    **Kaynak grubu**: bu alanı geçerli kaynak grubunuz, *contosoresources*olarak ayarlayın. 

    **Kaynak türü**: bu alanı IoT Hub olarak ayarlayın. 

    **Kaynak**: IoT Hub 'ınızı, *ContosoTestHub*seçin.

3. Yeni bir uyarı ayarlamak için **ölçüm uyarısı Ekle (klasik)** seçeneğine tıklayın.

    Şu alanları doldurun:

    **Ad**: uyarı kuralınız için *telemetri iletileri*gibi bir ad sağlayın.

    **Açıklama**: uyarı için *1000 telemetri iletisi gönderildiğinde uyarı*gibi bir açıklama belirtin. 

    **Kaynak**: bunu *ölçümler*olarak ayarlayın.

    **Abonelik**, **kaynak grubu**ve **kaynak** **Klasik Uyarıları görüntüle** ekranında seçtiğiniz değerlere ayarlanmalıdır. 

    **Ölçüyü** *gönderilen telemetri iletileri*olarak ayarlayın.

    ![Gönderilen telemetri iletileri için klasik uyarı ayarlamayı gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Grafik sonrasında aşağıdaki alanları ayarlayın:

   **Koşul**: *değerinden büyük*olarak ayarlayın.

   **Threshold**: 1000 olarak ayarlayın.

   **Süre**: *son 5 dakika içinde*olarak ayarlanır.

   **Bildirim e-posta alıcıları**: e-posta adresinizi buraya koyun. 

   ![Uyarıların alt yarısında gösterilen ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Uyarıyı kaydetmek için **Tamam** ' ı tıklatın. 

5. Şimdi, *kullanılan toplam ileti sayısı*için başka bir uyarı ayarlayın. Bu ölçüm, kullanılan ileti sayısı IoT Hub kotasına yaklaştığı zaman bir uyarı göndermek istiyorsanız yararlı olur. Bu, hub 'ın iletileri reddetme yakında gönderileceğini bilmenizi sağlamak için yararlıdır.

   **Klasik Uyarıları görüntüle** ekranında, **ölçüm uyarısı Ekle (klasik)** seçeneğine tıklayın ve ardından **Kural Ekle** bölmesinde bu alanları girin.

   **Ad**: uyarı kuralınız için, *kullanılan ileti sayısı*gibi bir ad sağlayın.

   **Açıklama**: size, *kotasından yakın sürede uyarı*gibi bir açıklama sağlayın.

   **Kaynak**: bu alanı *ölçümler*olarak ayarlayın.

    **Abonelik**, **kaynak grubu**ve **kaynak** **Klasik Uyarıları görüntüle** ekranında seçtiğiniz değerlere ayarlanmalıdır. 

    **Ölçüyü** *, kullanılan toplam ileti sayısına*ayarlayın.

6. Grafik altında aşağıdaki alanları girin:

   **Koşul**: *değerinden büyük*olarak ayarlayın.

   **Threshold**: 1000 olarak ayarlayın.

   **Süre**: bu alanı *son 5 dakika içinde*olacak şekilde ayarlayın. 

   **Bildirim e-posta alıcıları**: e-posta adresinizi buraya koyun. 

   Kuralı kaydetmek için **Tamam** ' ı tıklatın. 

5. Şimdi klasik uyarılar bölmesinde iki uyarı görmeniz gerekir: 

   ![Yeni uyarı kurallarıyla klasik uyarılar ekranını gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Uyarılar bölmesini kapatın. 
    
    Bu ayarlarla, gönderilen ileti sayısı 400 ' den büyük olduğunda ve kullanılan toplam ileti sayısı SAYıYı aştığında bir uyarı alacaksınız.

## <a name="run-simulated-device-app"></a>Simülasyon Cihazı uygulamasını çalıştırma

Betik ayarlama bölümünün başlarında, IoT cihazı kullanarak simülasyonu yapılacak bir cihaz ayarlamıştınız. Bu bölümde, IoT Hub'a cihazdan buluta iletiler gönderen bir cihazın simülasyonunu yapan bir .NET konsol uygulaması indireceksiniz.  

[IoT Cihaz Simülasyonu](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) çözümünü indirin. Bu bağlantı, içindeki çeşitli uygulamalarla bir depoyu indirir; Aradığınız çözüm IoT-Hub/öğreticiler/yönlendirme/.

Kodu Visual Studio'da açmak için çözüm dosyasına (SimulatedDevice.sln) çift tıklayın, sonra da Program.cs'yi açın. `{iot hub hostname}` değerini IoT hub'ı konak adıyla değiştirin. IoT hub'ı konak adı **{iot-hub-adı}.azure-devices.net** biçimindedid. bu öğreticide, hub konak adı olarak **ContosoTestHub.azure-devices.net** kullanılır. Ardından, `{device key}` değerini daha önce simülasyon cihazını ayarlarken kaydettiğiniz cihaz anahtarıyla değiştirin. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Çalıştırma ve test etme 

Program.cs ' de, `Task.Delay` 1000 ' den 10 ' a değiştirin. Bu, iletilerin 1 saniye ile. 01 saniyeye gönderilmesi arasındaki süreyi azaltır. Bu gecikmeyi kısaltaştırma, gönderilen ileti sayısını artırır.

```csharp
await Task.Delay(10);
```

Konsol uygulamasını çalıştırın. Birkaç dakika bekleyin (10-15). Sanal cihazdan gönderilen iletileri uygulamanın konsol ekranındaki hub 'a görebilirsiniz.

### <a name="see-the-metrics-in-the-portal"></a>Portalda ölçümlere bakın

Panodan ölçümlerinizi açın. Zaman değerlerini *1 dakikalık*bir zaman düzeyi ile *son 30 dakika* olarak değiştirin. Bu, gönderilen telemetri iletilerini ve grafikte kullanılan toplam ileti sayısını, grafiğin alt kısmındaki en son sayılarla gösterir.

   ![Ölçümleri gösteren ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Uyarılara bakın

Uyarılara geri dönün. **Kaynak grupları**' na tıklayın, *contosoresources*' i seçin ve ardından hub *ContosoTestHub*seçin. Merkez için görünen Özellikler sayfasında **Uyarılar**' ı seçin ve ardından **Klasik uyarıları görüntüleyin**. 

Gönderilen ileti sayısı sınırı aştığında, e-posta uyarıları almaya başlayabilirsiniz. Etkin bir uyarı olup olmadığını görmek için hub 'ınıza gidin ve **Uyarılar**' ı seçin. Bu işlem, etkin olan uyarıları ve herhangi bir uyarı olup olmadığını gösterir. 

   ![Uyarıların tetiklendiğinin gösterildiği ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Telemetri iletileri için uyarıya tıklayın. Bu, ölçüm sonucunu ve sonuçları içeren bir grafiği gösterir. Ayrıca, uyarı tetiklemek için gönderilen e-posta Şu görüntüye benzer şekilde görünür:

   ![Uyarıları gösteren e-postanın ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Tanılama günlüklerine bakın

Tanılama günlüklerinizi blob depolamaya aktarılacak şekilde ayarlarsınız. Kaynak grubunuza gidin ve *contosostoragemon*depolama hesabınızı seçin. Bloblar ' ı seçin ve ardından kapsayıcı *öngörüleri*' ni açın. Geçerli tarihi bulana ve en son dosyayı seçene kadar ayrıntıya gidin. 

   ![Tanılama günlüklerini görmek için depolama kapsayıcısının ayrıntılarına gidilme ekran görüntüsü.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

İndirmek için **İndir** ' e tıklayın ve açın. Aygıtın, hub 'a ileti gönderdiği şekilde bağlanıp bağlantısını kestiğini görürsünüz. Örnek:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Bu öğreticide oluşturduğunuz tüm kaynakları kaldırmak için kaynak grubunu silin. Bu eylem grubun içerdiği tüm kaynakları siler. Bu durumda, IoT Hub, depolama hesabı ve kaynak grubunun kendisini kaldırır. Panoya sabitlenmiş ölçümler varsa, her birinin sağ üst köşesindeki üç noktaya tıklayarak ve **Kaldır**' ı seçerek bunları el ile kaldırmanız gerekir.

Kaynak grubunu kaldırmak için [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) komutunu kullanın.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki görevleri gerçekleştirerek ölçüm ve tanılama günlüklerinin nasıl kullanılacağını öğrendiniz:

> [!div class="checklist"]
> * Azure CLı kullanarak bir IoT Hub, sanal cihaz ve depolama hesabı oluşturun.  
> * Tanılama günlüklerini etkinleştirin. 
> * Ölçümleri etkinleştirin.
> * Bu ölçümler için uyarıları ayarlayın. 
> * Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulamayı indirip çalıştırın. 
> * Uyarılar çalışmaya başlamadan önce uygulamayı çalıştırın. 
> * Ölçüm sonuçlarını görüntüleyin ve tanılama günlüklerini denetleyin. 

IoT cihazı durumunun nasıl yönetileceğini öğrenmek için sonraki öğreticiye geçin. 

> [!div class="nextstepaction"]
> [Cihazlarınızı bir arka uç hizmetinden yapılandırma](tutorial-device-twins.md)