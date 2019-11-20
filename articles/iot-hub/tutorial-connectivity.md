---
title: Azure IoT Hub cihaz bağlantısını kontrol etme
description: Geliştirme sırasında IOT hub'ınıza cihaz bağlantı sorunlarını gidermek için IOT Hub araçlarını kullanın.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom: mvc
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: caa249dda4215dfcef13df96d2dd4245cae49efd
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595762"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Öğretici: IOT hub'ınızla bağlantıyı test etmek için sanal cihaz kullanma

Bu öğreticide, cihaz bağlantısını test etmek için Azure IOT Hub'ı portal araçları ve Azure CLI komutlarını kullanırsınız. Bu öğreticide masaüstü bilgisayarınızda çalıştırdığınız bir cihaz simülatörü de kullanılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Cihazın kimlik doğrulamasını denetleme
> * Cihazın bulut bağlantısını denetleme
> * Bulut-cihaz bağlantısını denetleme
> * Cihaz çift eşitlemesini denetleme

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide çalıştırdığınız CLI betikleri [Azure CLI için Microsoft Azure IoT uzantısını](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) kullanır. Bu uzantıyı yüklemek için aşağıdaki CLI komutunu çalıştırın:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Bu öğreticide çalıştırdığınız cihaz simülatörü uygulaması Node.js kullanarak yazılır. Geliştirme makinenize Node.js v10.x.x veya sonraki bir sürümü gerekir.

[nodejs.org](https://nodejs.org) adresinden birden fazla platform için Node.js’yi indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

```cmd/sh
node --version
```

[https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip ) adresinden örnek cihaz simülatörü Node.js projesini indirin ve ZIP arşivini ayıklayın.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Bir önceki hızlı başlangıç ya da öğreticide ücretsiz veya standart katman IOT hub'ı oluşturduysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Cihaz kimlik doğrulamasını denetleme

Hub ile herhangi bir veri alışverişi yapmak için cihazın hub'ınızda kimliğini doğrulaması gerekir. Cihazlarınızı yönetmek ve kullanmakta olduğunuz kimlik doğrulama anahtarlarını denetlemek için portalın **Cihaz Yönetimi** bölümündeki **IoT Cihazları** aracını kullanabilirsiniz. Öğreticinin bu bölümünde yeni bir test cihazı ekler, anahtarını alır ve test cihazının hub'a bağlanıp bağlanamadığını denetlersiniz. Daha sonra bir cihaz eski bir anahtarı kullanmaya çalıştığında ne olacağını izlemek için kimlik doğrulama anahtarını sıfırlarsınız. Öğreticinin bu bölümünde bir cihazı oluşturmak, yönetmek ve izlemek için Azure portalı ve örnek Node.js cihaz simülatörü kullanılır.

Portalda oturum açın ve IoT Hub'ınıza gidin. Ardından **IoT cihazları** aracına gidin:

![IoT cihazları aracı](media/tutorial-connectivity/iot-devices-tool.png)

Yeni bir cihaz kaydetmek için **+ Ekle**’ye tıklayın, **Cihaz Kimliği**’ni **MyTestDevice** olarak ayarlayın ve **Kaydet**’e tıklayın:

![Yeni cihaz ekleme](media/tutorial-connectivity/add-device.png)

**MyTestDevice**’ın bağlantı dizesini almak için cihazlar listesinde ona tıklayın ve ardından **Bağlantı dizesi birincil anahtarı** değerini kopyalayın. Bağlantı dizesi cihaz için *paylaşılan erişim anahtarını* içerir.

![Cihaz bağlantı dizesini alma](media/tutorial-connectivity/copy-connection-string.png)

**MyTestDevice**’ın IoT hub'ınıza telemetri göndermesini denemek için daha önce indirdiğiniz Node.js simülasyon cihazı uygulamasını çalıştırın.

Geliştirme makinenizdeki terminal penceresinde, indirdiğiniz örnek Node.js projesinin kök klasörüne gidin. Ardından gidin **IOT hub\Tutorials\ConnectivityTests** klasör.

Terminal penceresinde, aşağıdaki komutları çalıştırarak gerekli kitaplıkları yükleyin ve simülasyon cihazı uygulamasını çalıştırın. Cihaz Portalı'nda ne zaman eklediğiniz Not yapılan cihaz bağlantı dizesini kullanın.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Hub'ınıza bağlanmaya çalışırken terminal penceresinde bilgiler gösterilir:

![Simülasyon cihazına bağlanma](media/tutorial-connectivity/sim-1-connected.png)

Şimdi IoT hub’ınız tarafından üretilen cihaz anahtarını kullanarak bir cihazın kimliğini başarıyla doğruladınız.

### <a name="reset-keys"></a>Anahtarları sıfırlama

Bu bölümde, cihaz anahtarını sıfırlar ve simülasyon cihazı bağlanmaya çalıştığında çıkan hatayı incelersiniz.

**MyTestDevice**’ın birincil cihaz anahtarını sıfırlamak için aşağıdaki komutları çalıştırın:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

Geliştirme makinenizdeki terminal penceresinde simülasyon cihazı uygulamasını yeniden çalıştırın:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Uygulama bağlanmaya çalıştığında bu kez, bir kimlik doğrulama hatası görürsünüz:

![Bağlantı hatası](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Paylaşılan erişim imzası (SAS) belirtecini üretme

Cihazınız IoT Hub cihaz SDK'lerinden birini kullanıyorsa, SDK kitaplık kodu hub ile kimlik doğrulaması gerçekleştirmek için kullanılan SAS belirtecini oluşturur. SAS belirteci, hub'ınızın adı, cihazınızın adı ve cihaz anahtarı ile oluşturulur.

Bazı senaryolarda, örneğin bir bulut protokol ağ geçidi veya bir özel kimlik doğrulama düzenin bir parçası olarak, SAS belirtecini kendiniz oluşturmanız gerekebilir. SAS oluşturma kodunuzdaki sorunları gidermek için test sırasında kullanılacak bir bilinen iyi SAS belirteci oluşturmak kullanışlıdır.

> [!NOTE]
> SimulatedDevice-2.js örneği, SDK’lı ve SDK’sız SAS belirteci oluşturma örnekleri içerir.

CLI kullanarak, geçerli olduğu bilinen bir SAS belirteci oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Oluşturulan SAS belirteci tam metnini not edin. Bir SAS belirteci aşağıdaki gibi görünür: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

Geliştirme makinenizdeki terminal penceresinde, indirdiğiniz örnek Node.js projesinin kök klasörüne gidin. Ardından gidin **IOT hub\Tutorials\ConnectivityTests** klasör.

Terminal penceresinde, aşağıdaki komutları çalıştırarak gerekli kitaplıkları yükleyin ve simülasyon cihazı uygulamasını çalıştırın:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

SAS belirtecini kullanarak hub'ınıza bağlanmaya çalışılırken terminal penceresinde bilgiler gösterilir:

![Belirteç ile simülasyon cihazını bağlama](media/tutorial-connectivity/sim-2-connected.png)

Şimdi bir CLI komutu tarafından oluşturulmuş bir test SAS belirteci kullanarak bir cihazın kimliğini başarıyla doğruladınız. **SimulatedDevice 2.js** dosyası, kodda bir SAS belirteci üretmeyi gösteren örnek kodlar içerir.

### <a name="protocols"></a>Protokoller

Bir cihaz IOT hub'ınıza bağlanmak için aşağıdaki protokollerden birini kullanabilir:

| Protocol | Giden bağlantı noktası |
| --- | --- |
| MQTT |8883 |
| WebSockets üzerinden MQTT |443 |
| AMQP |5671 |
| WebSockets üzerinden AMQP |443 |
| HTTPS |443 |

Giden bağlantı noktası bir güvenlik duvarı tarafından engellenirse cihaz bağlanamaz:

![Bağlantı noktası engellendi](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Cihazın bulut bağlantısını denetleme

Bir cihaz bağlandıktan sonra genellikle IoT hub'ınıza telemetri göndermeye çalışır. Bu bölümde, cihaz tarafından gönderilen telemetrinin hub'ınızı ulaşıp ulaşmadığını nasıl doğrulayacağınız gösterilir.

İlk olarak, aşağıdaki komutu kullanarak sanal cihazınız için geçerli bağlantı dizesini alın:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

İletiler gönderen bir simülasyon cihazını çalıştırma için gidin **IOT hub\Tutorials\ConnectivityTests** indirdiğiniz kod klasörü.

Terminal penceresinde, aşağıdaki komutları çalıştırarak gerekli kitaplıkları yükleyin ve simülasyon cihazı uygulamasını çalıştırın:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

Hub'ınıza telemetri göndermeye çalışırken terminal penceresinde bilgiler gösterilir:

![Simülasyon cihazı ileti gönderirken](media/tutorial-connectivity/sim-3-sending.png)

Kullanabileceğiniz **ölçümleri** portalındaki IOT hub'ınıza telemetri iletilerini ulaşıyor olduğunu doğrulayın. **Kaynak** açılan listesinde IoT hub'ınızı seçin, ölçüm olarak **Gönderilen telemetri iletilerini** seçin ve zaman aralığını **Son bir saat** olarak ayarlayın. Grafikte simülasyon cihazı tarafından gönderilen iletilerin toplam sayısı gösterilmiştir:

![IoT Hub ölçümlerini gösterme](media/tutorial-connectivity/metrics-portal.png)

Simülasyon cihazını başlattıktan sonra ölçümlerin kullanılabilir hale gelmesi birkaç dakika alır.

## <a name="check-cloud-to-device-connectivity"></a>Bulut-cihaz bağlantısını denetleme

Bu bölümde bulut-cihaz bağlantısını denetlemek için bir test doğrudan yöntem çağrısını nasıl yapabileceğiniz gösterilmiştir. Hub’ınızdan gelen doğrudan yöntem çağrılarını dinlemek için, geliştirme makinenizde bir simülasyon cihazı cihaz çalıştırırsınız.

Terminal penceresinde, simülasyon cihazı uygulamasını çalıştırmak için aşağıdaki komutları kullanın:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Cihazda bir doğrudan yöntem çağırmak için bir CLI komutu kullanın:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Doğrudan yöntem çağrısı aldığında, simülasyon cihazı konsola bir ileti yazdırır:

![Simülasyon cihazı doğrudan yöntem çağrısı aldığında](media/tutorial-connectivity/receive-method-call.png)

Simülasyon cihazı başarıyla doğrudan yöntem çağrısı aldığında, hub'a bir onay gönderir:

![Doğrudan yöntem alındığı bildirimi](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Çift eşitlemeyi denetleme

Cihazlar cihaz ve hub arasında durum eşitlemek için çiftler kullanın. Bu bölümde, bir cihaza _istenen özellikleri_ göndermek ve cihaz tarafından gönderilen _bildirilen özellikleri_ okumak için CLI komutları kullanırsınız.

Bu bölümde kullandığınız simülasyon cihazı her başladığında bildirilen özellikleri hub’a gönderir ve istenen özellikleri her aldığında konsola yazdırır.

Terminal penceresinde, simülasyon cihazı uygulamasını çalıştırmak için aşağıdaki komutları kullanın:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Hub’ın cihazdan bildirilen özellikleri aldığını doğrulamak için aşağıdaki CLI komutunu kullanın:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Komut çıktısında, bildirilen özellikler bölümünde **devicelaststarted** özelliğini görebilirsiniz. Bu özellik, simülasyon cihazının son başlatıldığı tarih ve saati gösterir.

![Bildirilen özellikleri görüntüleme](media/tutorial-connectivity/reported-properties.png)

Hub’ın cihaza istenen özellikleri gönderdiğini doğrulamak için aşağıdaki CLI komutunu kullanın:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Simülasyon cihazı, hub’dan istenen özellik güncelleştirmesi aldığında bir ileti yazdırır:

![İstenen özellikleri alma](media/tutorial-connectivity/desired-properties.png)

Simülasyon cihazı, istenen özelliklerde yapılan değişiklikleri almanın yanı sıra her başladığında istenen özellikleri otomatik olarak denetler.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse portaldan IoT hub’ı ve kaynak grubunu silin. Bunu yapmak için, IoT hub’ınızı içeren **tutorials-iot-hub-rg** kaynak grubunu seçin ve **Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihaz anahtarlarınızı denetlemeyi, cihaz bulut bağlantısını denetlemeyi, bulut-cihaz bağlantısını denetlemeyi ve cihaz çift eşitlemesini denetlemeyi öğrendiniz. IoT hub'ınızı izleme hakkında daha fazla bilgi için IoT Hub izlemeye yönelik nasıl yapılır makalesini inceleyin.

> [!div class="nextstepaction"]
> [Tanılama ile izleme](iot-hub-monitor-resource-health.md)
