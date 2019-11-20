---
title: Azure IoT Hub aracılığıyla cihaz üretici yazılımını güncelleştirme | Microsoft Docs
description: IoT Hub 'ınıza bağlı bir arka uç uygulamasından tetiklenebilecek bir cihaz üretici yazılımı güncelleştirme işlemini nasıl uygulayacağınızı öğrenin.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom: mvc
ms.openlocfilehash: 0665a20bfd8253b28936044abe515862b32f1b43
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888753"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Öğretici: Cihaz yazılımı güncelleştirme işlemi gerçekleştirme

IoT hub'ınıza bağlı cihazların üretici yazılımını güncelleştirmeniz gerekebilir. Örneğin üretici yazılımına yeni özellik eklemek veya güvenlik yaması uygulamak isteyebilirsiniz. Birçok IoT senaryosunda fiziksel cihazlarınızı ziyaret edip cihaz yazılımı güncelleştirmelerini el ile uygulamak zordur. Bu öğreticide hub'ınıza bağlı bir arka uç uygulaması üzerinden cihaz yazılımı güncelleştirme işlemini uzaktan başlatma ve izleme adımları anlatılmaktadır.

Cihaz yazılımı güncelleştirme işlemini oluşturmak ve izlemek için bu öğreticideki arka uç uygulamasında IoT hub'ınızda bir _yapılandırma_ oluşturulur. IoT Hub [otomatik cihaz yönetimi](iot-hub-auto-device-config.md) bu yapılandırmayı kullanarak tüm soğutucu cihazlarınızdaki _cihaz ikizi istenen özellikler_ kümesini güncelleştirir. İstenen özellikler, gerekli olan cihaz yazılımı güncelleştirmesinin ayrıntılarını belirtir. Soğutucu cihazlar yazılım güncelleştirme işlemini çalıştırırken _cihaz ikizi bildirilen özelliklerini_ kullanarak durumlarını arka uç uygulamasına bildirir. Arka uç uygulaması bu yapılandırmayı kullanarak cihazdan gönderilen bildirilen özellikleri izleyebilir ve cihaz yazılımı güncelleştirme işlemini tamamlanıncaya kadar izleyebilir:

![Cihaz yazılımı güncelleştirme işlemi](media/tutorial-firmware-update/Process.png)

Bu öğreticide, aşağıdaki görevleri tamamlayacaksınız:

> [!div class="checklist"]
> * Bir IoT hub oluşturun ve cihaz kimlik kayıt defterine bir test cihazı ekleyin.
> * Cihaz yazılımı güncelleştirmesini tanımlamak için bir yapılandırma oluşturun.
> * Bir cihazdaki cihaz yazılımı güncelleştirme işleminin simülasyonunu yapın.
> * Cihaz yazılımı güncelleştirmesi devam ederken cihazdan durum güncelleştirmelerini alın.

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

Bu öğreticiyi tamamlayabilmeniz için Azure aboneliğinizin cihaz kimliği kayıt defterine cihaz eklenmiş bir IOT hub olması gerekir. Cihaz kimliği kayıt defterindeki giriş, bu öğreticide çalıştırdığınız simülasyon cihazının hub’ınıza bağlanmasına imkan tanır.

Aboneliğinizde zaten ayarlanmış bir IoT hub yoksa aşağıdaki CLI betiğiyle bir IoT hub ayarlayabilirsiniz. Bu betikte IoT hub için **tutorial-iot-hub** adı kullanılır ve betiği çalıştırırken bu adı kendi benzersiz adınızla değiştirmeniz gerekir. Betik, kaynak grubunu ve hub’ı **Orta ABD** bölgesinde oluşturur ve bunu size daha yakın bir konum olacak şekilde değiştirebilirsiniz. Betik, IoT hub hizmetinizin arka uç örnek uygulamasında IoT hub’ınıza bağlanmak için kullanacağınız bağlantı dizesini döndürür:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

Bu öğreticide **MyFirmwareUpdateDevice** adlı bir simülasyon cihazı kullanılır. Aşağıdaki betik bu cihazı cihaz kimliği kayıt defterinize ekler, bir etiket değeri belirler ve bunun bağlantı dizesini alır:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Bu komutları bir Windows komut isteminde veya Powershell isteminde çalıştırıyorsanız, JSON dizelerini alıntı yapma hakkında bilgi için [azure-iot-cli-extension tips](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips) sayfasına bakın.

## <a name="start-the-firmware-update"></a>Cihaz yazılımı güncelleştirmesini başlatma

[devicetype](iot-hub-automatic-device-management.md#create-a-configuration) etiketine sahip tüm soğutucularda cihaz yazılımı güncelleştirme işlemini başlatmak için arka uç uygulamasında bir **otomatik cihaz yönetimi yapılandırması** oluşturursunuz. Bu bölümde şunları nasıl yapabileceğinizi öğrenirsiniz:

* Arka uç uygulamasından yapılandırma oluşturma.
* İşi tamamlanana kadar izleme.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Cihaz yazılımı yükseltme işlemini arka uç uygulamasından başlatmak için istenen özellikleri kullanma

Yapılandırmayı oluşturan arka uç uygulama kodunu görüntülemek için indirdiğiniz örnek Node.js projesindeki **iot-hub/Tutorials/FirmwareUpdate** klasörüne gidin. Sonra ServiceClient.js dosyasını bir metin düzenleyicide açın.

Arka uç uygulaması şu yapılandırmayı oluşturur:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

Yapılandırma şu bölümlerden oluşur:

* `content`, seçilen cihazlara gönderilen cihaz yazılımının istenen özelliklerini belirtir.
* `metrics`, cihaz yazılımı güncelleştirmesinin durumunu bildiren sorguları belirtir.
* `targetCondition`, cihaz yazılımı güncelleştirmesini alacak cihazları seçer.
* `priorty`, bu yapılandırmanın diğer yapılandırmalara göre önceliğini belirler.

Arka uç uygulaması, istenen özellikleri ayarlayacak yapılandırmayı oluşturmak için aşağıdaki kodu kullanır:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Uygulama, yapılandırmayı oluşturduktan sonra cihaz yazılımı güncelleştirmesini izler:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Yapılandırma raporu iki ölçüm türü bildirir:

* Hedeflenen cihaz sayısını ve güncelleştirmenin uygulandığı cihaz sayısını bildiren sistem ölçümleri.
* Yapılandırmaya eklediğiniz sorgular tarafından oluşturulan özel ölçümler. Bu öğreticide sorgular, güncelleştirme işleminin her aşamasındaki cihaz sayısını bildirmektedir.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Cihazda, cihaz yazılımı yükseltme isteğine yanıt verme

Arka uç uygulaması tarafından gönderilen cihaz yazılımı istenen özelliklerini işleyen cihaz kodu simülasyonunu görüntülemek için indirdiğiniz örnek Node.js projesindeki **iot-hub/Tutorials/FirmwareUpdate** klasörüne gidin. Sonra SimulatedDevice.js dosyasını bir metin düzenleyicide açın.

Cihaz simülasyon uygulaması, cihaz ikizindeki **properties.desired.firmware** istenen özellik güncelleştirmeleri için bir işleyici oluşturur. İşleyicide, güncelleştirme işlemini başlatmadan önce istenen özellikler üzerinde bazı temel denetimler gerçekleştirir:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Cihaz yazılımını güncelleştirme

**initiateFirmwareUpdateFlow** işlevi, güncelleştirmeyi çalıştırır. Bu işlev, güncelleştirme işleminin aşamalarını sırasıyla çalıştırmak için **waterfall** işlevini kullanır. Bu örnekte cihaz yazılımı güncelleştirme işlemi dört aşamalıdır. Birinci aşama görüntüyü indirir, ikinci aşama sağlama toplamı kullanarak görüntüyü doğrular, üçüncü aşama görüntüyü uygular ve son aşama cihazı yeniden başlatır:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Güncelleştirme işlemi sırasında simülasyon cihazı bildirilen özellikleri kullanarak ilerleme durumunu belirtir:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Aşağıdaki kod parçacığı, indirme aşamasının uygulamasını gösterir. İndirme aşamasında simülasyon cihazı durum bilgilerini arka uç uygulamasına göndermek için bildirilen özellikleri kullanır:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Örneği çalıştırma

Bu bölümde arka uç uygulamasının simülasyon cihazındaki cihaz yazılımı güncelleştirme işlemini yönetme amacıyla yapılandırmayı oluşturmasını gözlemlemek için iki örnek uygulama çalıştıracaksınız.

Simülasyon cihazını ve arka uç uygulamalarını çalıştırmak için cihaz ve hizmet bağlantı dizelerine sahip olmanız gerekir. Bu öğreticinin başlangıcında kaynakları oluştururken bağlantı dizelerini not almıştınız.

Simülasyon cihazı uygulamasını çalıştırmak için bir kabuk veya komut istemi penceresi açın ve indirdiğiniz Node.js projesinin **iot-hub/Tutorials/FirmwareUpdate** klasörüne gidin. Sonra aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Arka uç uygulamasını çalıştırmak için başka bir kabuk veya komut istemi penceresi açın. Sonra indirdiğiniz Node.js projesindeki **iot-hub/Tutorials/FirmwareUpdate** klasörüne gidin. Sonra aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Aşağıdaki ekran görüntüsünde simülasyon cihazı uygulamasından alınan çıkış ve arka uç uygulamasının cihaz yazılımı istenen özellik güncelleştirmesine verdiği yanıt gösterilmiştir:

![Sanal cihaz](./media/tutorial-firmware-update/SimulatedDevice.png)

Aşağıdaki ekran görüntüsünde arka uç uygulamasından alınan çıkış gösterilmiş ve uygulamanın cihaz yazılımının istenen özelliklerini güncelleştirmek için yapılandırmayı nasıl oluşturduğu vurgulanmıştır:

![Arka uç uygulaması](./media/tutorial-firmware-update/BackEnd1.png)

Aşağıdaki ekran görüntüsünde arka uç uygulamasından alınan çıkış gösterilmiş ve uygulamanın simülasyon cihazından cihaz yazılımı güncelleştirme ölçümlerini nasıl izlediği vurgulanmıştır:

![Arka uç uygulaması](./media/tutorial-firmware-update/BackEnd2.png)

Otomatik cihaz yapılandırması oluşturma zamanında çalıştığından ve beş dakikada bir çalıştığı için, arka uç uygulamasına gönderilen her durum güncelleştirmesini göremeyebilirsiniz. Ölçümleri portalda görüntülemek için IoT Hub'ınızın **Automatic device management -> IoT device configuration** (Otomatik cihaz yönetimi -> IoT cihazı yapılandırması) bölümüne gidin:

![Yapılandırmayı portalda görüntüleme](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiyi tamamlamayı planlıyorsanız, kaynak grubunu ve IoT hub’ı değiştirmeden bırakın ve sonra bunları yeniden kullanın.

Artık gerekli değilse portaldan IoT hub’ı ve kaynak grubunu silin. Bunu yapmak için, IoT hub’ınızı içeren **tutorial-iot-hub-rg** kaynak grubunu seçin ve **Sil**’e tıklayın.

Alternatif olarak, CLI kullanın:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bağlı cihazlarınıza, cihaz yazılımı güncelleştirme işlemi uygulamayı öğrendiniz. Cihaz bağlantısını test etmek için Azure IoT Hub Portal araçları 'nı ve Azure CLı komutlarını nasıl kullanacağınızı öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [IoT hub’ınızla bağlantıyı test etmek için bir sanal cihaz kullanma](tutorial-connectivity.md)
