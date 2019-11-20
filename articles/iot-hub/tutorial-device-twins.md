---
title: Azure IoT Hub’dan cihaz durumunu eşitleme | Microsoft Docs
description: Cihazlarınızı buluttan yapılandırmak ve cihazlarınızdan durum ve uyumluluk verileri almak için cihaz ikizlerini nasıl kullanacağınızı öğrenin.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom: mvc
ms.openlocfilehash: 647182389ec0ad4cb2b80a0676812961cb9be770
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890428"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Öğretici: Bir arka uç hizmetinden cihazlarınızı yapılandırma

Cihazlarınızdan telemetri almanın yanı sıra cihazlarınızı arka uç hizmetinizden yapılandırmak isteyebilirsiniz. Cihazlarınıza bir istenen yapılandırma gönderdiğinizde bu cihazlardan durum ve uyumluluk güncelleştirmeleri almak da isteyebilirsiniz. Örneğin, bir cihaz için hedef çalışma sıcaklığı aralığı ayarlayabilir veya cihazlarınızdan üretici yazılımı sürüm bilgileri toplayabilirsiniz.

Bir cihaz ile IoT hub arasında durum bilgilerini eşitlemek için _cihaz ikizlerini_ kullanırsınız. [Cihaz ikizi](iot-hub-devguide-device-twins.md), belirli bir cihazla ilişkili olan ve IoT Hub tarafından bunları [sorgulayabileceğiniz](iot-hub-devguide-query-language.md) bulutta depolanan bir JSON belgesidir. Bir cihaz ikizi _istenen özellikleri_, _bildirilen özellikleri_ ve _etiketleri_ içerir. İstenen özellikler arka uç uygulaması tarafından ayarlanır ve bir cihaz tarafından okunur. Bildirilen özellikler bir cihaz tarafından ayarlanır ve bir arka uç uygulaması tarafından okunur. Etiketler bir arka uç uygulaması tarafından oluşturulur ve asla bir cihaza gönderilmez. Cihazlarınızı düzenlemek için etiketleri kullanırsınız. Bu öğreticide istenen ve bildirilen özellikleri kullanarak durum bilgilerini nasıl eşitleyebileceğiniz gösterilmiştir:

![İkiz özeti](media/tutorial-device-twins/DeviceTwins.png)

Bu öğreticide, aşağıdaki görevleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Bir IoT hub oluşturun ve kimlik kayıt defterine bir test cihazı ekleyin.
> * Durum bilgilerini simülasyon cihazınıza göndermek için istenen özellikleri kullanın.
> * Simülasyon cihazınızdan toplanan durum bilgilerini almak için bildirilen özellikleri kullanın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama, Node.js kullanılarak yazılır. Geliştirme makinenizde Node. js ile v10 arasındaki. x. x veya üzeri gerekir.

[nodejs.org](https://nodejs.org) adresinden birden fazla platform için Node.js’yi indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

```cmd/sh
node --version
```

https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip adresinden örnek Node.js projesini indirin ve ZIP arşivini ayıklayın.

## <a name="set-up-azure-resources"></a>Azure kaynakları ayarlama

Bu öğreticiyi tamamlayabilmeniz için Azure aboneliğinizin cihaz kimliği kayıt defterine cihaz eklenmiş bir IOT hub içermesi gerekir. Cihaz kimliği kayıt defterindeki giriş, bu öğreticide çalıştırdığınız simülasyon cihazının hub’ınıza bağlanmasına imkan tanır.

Aboneliğinizde zaten bir IoT Hub 'ı ayarlanmamışsa, aşağıdaki CLı betiği ile bir tane ayarlayabilirsiniz. Bu betikte IoT hub için **tutorial-iot-hub** adı kullanılır ve betiği çalıştırırken bu adı kendi benzersiz adınızla değiştirmeniz gerekir. Betik, kaynak grubunu ve hub’ı **Orta ABD** bölgesinde oluşturur ve bunu size daha yakın bir konum olacak şekilde değiştirebilirsiniz. Betik, IoT hub hizmetinizin arka uç örneğinde IoT hub’ınıza bağlanmak için kullanacağınız bağlantı dizesini döndürür:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

Bu öğreticide **MyTwinDevice** adlı bir simülasyon cihazı kullanılır. Aşağıdaki betik bu cihazı kimlik kayıt defterinize ekler ve bunun bağlantı dizesini alır:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Durum bilgilerini gönderme

Bir arka uç uygulamasından bir cihaza durum bilgilerini göndermek için istenen özellikleri kullanırsınız. Bu bölümde şunları nasıl yapabileceğinizi öğrenirsiniz:

* Bir cihazda istenen özellikleri alma ve işleme.
* İstenen özellikleri bir arka uç uygulamasından gönderme.

İstenen özellikleri alan simülasyon cihazı örnek kodunu görüntülemek için indirdiğiniz örnek Node.js projesinde **iot-hub/Tutorials/DeviceTwins** klasörüne gidin. Sonra SimulatedDevice.js dosyasını bir metin düzenleyicide açın.

Arka uç uygulamasından gönderilen istenen özellik değişikliklerine yanıt veren simülasyon cihazında çalışan kod aşağıdaki bölümlerde açıklanmıştır:

### <a name="retrieve-the-device-twin-object"></a>Cihaz ikizi nesnesini alma

Aşağıdaki kod bir cihaz bağlantı dizesi kullanarak IoT hub’ınıza bağlanır:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

Aşağıdaki kod istemci nesnesinden bir ikiz alır:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Örnek istenen özellikler

İstenen özelliklerinizi uygulamanız için uygun olacak şekilde dilediğiniz gibi yapılandırabilirsiniz. Bu örnekte **fanOn** adlı bir üst düzey özellik kullanılır ve geriye kalan özellikler ayrı **components** (bileşenler) olarak gruplandırılır. Aşağıdaki JSON parçacığında bu öğretici için kullanılan istenen özelliklerin yapısı gösterilmiştir:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>İşleyiciler oluşturma

İstenen özellik güncelleştirmeleri için JSON hiyerarşisinin farklı düzeylerindeki güncelleştirmelere yanıt veren işleyiciler oluşturabilirsiniz. Örneğin, bu işleyici bir arka uç uygulamasından cihaza gönderilen tüm özellik değişikliklerini algılar. **Delta** değişkeni, çözüm arka ucundan gönderilen istenen özellikleri içerir:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Aşağıdaki işleyici yalnızca **fanOn** istenen özelliğinde yapılan değişikliklere tepki verir:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Birden çok özelliğe yönelik işleyiciler

Daha önce gösterilen örnek istenen özellikler JSON öğesinde **components** altındaki **climate** düğümü **minTemperature** ve **maxTemperature** şeklinde iki özellik içerir.

Bir cihazın yerel **ikiz** nesnesi istenen ve bildirilen özelliklerin eksiksiz bir kümesini depolar. Arka uçtan gönderilen **delta**, istenen özelliklerin yalnızca bir alt kümesini güncelleştirebilir. Aşağıdaki kod parçacığında, simülasyon cihazı **minTemperature** ve **maxTemperature** özelliklerinden yalnızca biri için güncelleştirme alırsa cihazı yapılandırmak üzere diğer değer için yerel ikizdeki değeri kullanır:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

Yerel **ikiz** nesnesi istenen ve bildirilen özellikleri eksiksiz bir kümesini depolar. Arka uçtan gönderilen **delta**, istenen özelliklerin yalnızca bir alt kümesini güncelleştirebilir.

### <a name="handle-insert-update-and-delete-operations"></a>Ekleme, güncelleştirme ve silme işlemlerini işleme

Arka uçtan gönderilen istenen özellikler belirli bir istenen özellik üzerinde hangi işlemin gerçekleştirildiğini göstermez. Kodunuzun işlemi yerel olarak depolanan güncel istenen özellikler kümesinden ve hub’dan gönderilen değişikliklerden çıkarsaması gerekir.

Aşağıdaki kod parçacığında, simülasyon cihazının istenen özelliklerde **components** listesindeki ekleme, güncelleştirme ve silme işlemlerini nasıl işlediği gösterilmiştir. Bir bileşenin silinmesi gerektiğini belirtmek için nasıl **null** değerlerini kullanabileceğinizi görebilirsiniz:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Arka uçtan bir cihaza istenen özellikler gönderme

Bir cihazın istenen özellik güncelleştirmelerini almak için nasıl işleyici uyguladığını gördünüz. Bu bölümde, bir arka uç uygulamasından bir cihaza nasıl istenen özellik değişiklikleri gönderilebileceği gösterilmiştir.

İstenen özellikleri alan simülasyon cihazı örnek kodunu görüntülemek için indirdiğiniz örnek Node.js projesinde **iot-hub/Tutorials/DeviceTwins** klasörüne gidin. Sonra ServiceClient.js dosyasını bir metin düzenleyicide açın.

Aşağıdaki kod parçacığında cihaz kimliği kayıt defterine bağlanıp belirli bir cihazın ikizine nasıl erişilebileceği gösterilmiştir:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Aşağıdaki kod parçacığında arka uç uygulamasının cihaza gönderdiği farklı istenen özellik *patches* (düzeltme ekleri) gösterilmiştir:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Aşağıdaki kod parçacığında arka uç uygulamasının bir cihaza nasıl istenen özellik güncelleştirmesi gönderdiği gösterilmiştir:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Uygulamaları çalıştırma

Bu bölümde, bir arka uç uygulamasının bir simülasyon cihazı uygulamasına istenen özellik güncelleştirmeleri gönderişini izlemek için iki örnek uygulama çalıştırırsınız.

Simülasyon cihazını ve arka uç uygulamalarını çalıştırmak için cihaz ve hizmet bağlantı dizelerine sahip olmanız gerekir. Bu öğreticinin başlangıcında kaynakları oluştururken bağlantı dizelerini not almıştınız.

Simülasyon cihazı uygulamasını çalıştırmak için bir kabuk veya komut istemi penceresi açın ve indirdiğiniz Node.js projesinin **iot-hub/Tutorials/DeviceTwins** klasörüne gidin. Sonra aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Arka uç uygulamasını çalıştırmak için başka bir kabuk veya komut istemi penceresi açın. Sonra indirdiğiniz Node.js projesindeki **iot-hub/Tutorials/DeviceTwins** klasörüne gidin. Sonra aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Aşağıdaki ekran görüntüsünde simülasyon cihazı uygulamasından alınan çıkış gösterilmiş ve uygulamanın **maxTemperature** istenen özelliğine yönelik bir güncelleştirmeyi nasıl işlediği vurgulanmıştır. Her iki üst düzey işleyicinin ve iklim bileşeni işleyicilerinin nasıl çalıştığını görebilirsiniz:

![Sanal cihaz](./media/tutorial-device-twins/SimulatedDevice1.png)

Aşağıdaki ekran görüntüsünde arka uç uygulamasından alınan çıkış gösterilmiş ve uygulamanın **maxTemperature** istenen özelliğine yönelik bir güncelleştirmeyi nasıl gönderdiği vurgulanmıştır:

![Arka uç uygulaması](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Durum bilgilerini alma

Arka uç uygulamanız durum bilgilerini bir cihazdan bildirilen özellikler olarak alır. Bir cihaz, bildirilen özellikleri ayarlar ve hub’ınıza gönderir. Bir arka uç uygulaması, hub'ınızda depolanan cihaz ikizinden bildirilen özelliklerin geçerli değerlerini okuyabilir.

### <a name="send-reported-properties-from-a-device"></a>Bir cihazdan bildirilen özellikler gönderme

Bildirilen özellik değerlerine düzeltme eki olarak güncelleştirmeler gönderebilirsiniz. Aşağıdaki kod parçacığında simülasyon cihazının gönderdiği düzeltme eki için bir şablon gösterilmiştir. Simülasyon cihazı hub’a göndermeden önce düzeltme ekinin alanlarını güncelleştirir:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

Simülasyon cihazı bildirilen özellikleri içeren düzeltme ekini hub’a göndermek için aşağıdaki işlevi kullanır:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Bildirilen özellikleri işleme

Bir arka uç uygulaması cihaz ikizi aracılığıyla bir cihazın geçerli bildirilen özellik değerlerine erişir. Aşağıdaki kod parçacığında arka uç uygulamasının sanal cihaz için bildirilen özellik değerlerini nasıl okuduğu gösterilmiştir:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Uygulamaları çalıştırma

Bu bölümde, bir simülasyon cihazı uygulamasının bir arka uç uygulamasına bildirilen özellik güncelleştirmeleri gönderişini izlemek için iki örnek uygulama çalıştırırsınız.

İstenen özelliklerin bir cihaza nasıl gönderildiğini görmek için çalıştırdığınız iki örnek uygulamayı çalıştırırsınız.

Simülasyon cihazını ve arka uç uygulamalarını çalıştırmak için cihaz ve hizmet bağlantı dizelerine sahip olmanız gerekir. Bu öğreticinin başlangıcında kaynakları oluştururken bağlantı dizelerini not almıştınız.

Simülasyon cihazı uygulamasını çalıştırmak için bir kabuk veya komut istemi penceresi açın ve indirdiğiniz Node.js projesinin **iot-hub/Tutorials/DeviceTwins** klasörüne gidin. Sonra aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Arka uç uygulamasını çalıştırmak için başka bir kabuk veya komut istemi penceresi açın. Sonra indirdiğiniz Node.js projesindeki **iot-hub/Tutorials/DeviceTwins** klasörüne gidin. Sonra aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Aşağıdaki ekran görüntüsünde simülasyon cihazı uygulamasından alınan çıkış gösterilmiş ve uygulamanın hub’ınıza nasıl bir bildirilen özellik güncelleştirmesi gönderdiği vurgulanmıştır:

![Sanal cihaz](./media/tutorial-device-twins/SimulatedDevice2.png)

Aşağıdaki ekran görüntüsünde, arka uç uygulamasından alınan çıkış gösterilmektedir ve bir cihazdan bildirilen bir özellik güncelleştirmesi 'nin nasıl aldığı ve işlediği vurgulanmıştır:

![Arka uç uygulaması](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiyi tamamlamayı planlıyorsanız, kaynak grubunu ve IoT hub’ı değiştirmeden bırakın ve sonra bunları yeniden kullanın.

Artık gerekli değilse portaldan IoT hub’ı ve kaynak grubunu silin. Bunu yapmak için, IoT hub’ınızı içeren **tutorial-iot-hub-rg** kaynak grubunu seçin ve **Sil**’e tıklayın.

Alternatif olarak, CLI kullanın:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihazınız ile IoT hub’ınız arasında durum bilgilerini nasıl eşitleyebileceğinizi öğrendiniz. Cihaz ikizlerini kullanarak üretici yazılımı güncelleştirme işlemi uygulamayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Cihaz yazılımı güncelleştirme işlemi gerçekleştirme](tutorial-firmware-update.md)
