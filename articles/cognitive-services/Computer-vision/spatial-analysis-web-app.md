---
title: Uzamsal analiz Web uygulaması dağıtma
titleSuffix: Azure Cognitive Services
description: Bir Web uygulamasında uzamsal analizler kullanmayı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 6d3be90cc81b1bcd9a55fc8e53cb9f2238e8c6de
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285986"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Nasıl yapılır: Web uygulaması sayma bir kişi dağıtma

Uzamsal analizleri kişilerin hareketini anlayan bir Web uygulamasıyla tümleştirmeyi ve fiziksel bir alanı kaplayan kişilerin sayısını izleyip izleyeceğinizi öğrenmek için bu makaleyi kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

* Uzamsal analiz kapsayıcısını dağıtma
* İşlem ve kamerayı yapılandırma
* IoT Hub bağlantısını Web uygulamasında yapılandırma
* Web uygulamasını dağıtma ve test etme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* Azure IoT Edge dağıtım yapılandırmalarının ve [Azure IoT Hub](../../iot-hub/index.yml) hakkında temel bilgiler
* Yapılandırılmış bir [ana bilgisayar](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>Uzamsal analiz kapsayıcısını dağıtma

Kapsayıcıyı çalıştırmak için erişim sağlamak üzere [istek uygulamasını](https://aka.ms/csgate) doldurun. 

Ana bilgisayarı yapılandırmak ve bir IoT Edge cihazı Azure IoT Hub 'a bağlamak için [Konak bilgisayar kurulumunu](./spatial-analysis-container.md) izleyin. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Aboneliğinizde bir Azure IoT Hub hizmeti dağıtma

İlk olarak, standart Fiyatlandırma Katmanı (S1) veya ücretsiz katman (S0) ile bir Azure IoT Hub hizmeti örneği oluşturun. Azure CLı kullanarak bu örneği oluşturmak için bu yönergeleri izleyin.

Gerekli parametreleri girin:
* Abonelik: Azure aboneliğinizin adı veya KIMLIĞI
* Kaynak grubu: kaynak grubunuz için bir ad oluşturun
* IoT Hub adı: IoT Hub için bir ad oluşturun
* Iothub adı: oluşturduğunuz IoT Hub adı 
* Edge cihaz adı: Edge cihazınız için bir ad oluşturma

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Kapsayıcıyı konak bilgisayardaki Azure IoT Edge dağıtma

Azure CLı kullanarak, uzamsal analiz kapsayıcısını ana bilgisayarda IoT modülü olarak dağıtın. Dağıtım işlemi, dağıtımınız için gerekli kapsayıcıları, değişkenleri ve konfigürasyonları özetleyen bir dağıtım bildirim dosyası gerektirir. Örnek Azure Stack Edge 'e özgü [dağıtım bildirimi](https://go.microsoft.com/fwlink/?linkid=2142179), [Azure Stack olmayan uçtan](https://go.microsoft.com/fwlink/?linkid=2152189)uca DAĞıTıM bildirimi ve Azure VM 'yi GitHub 'da, *uzamsal analiz* kapsayıcısına yönelik temel bir DAĞıTıM yapılandırması içeren, GitHub üzerinde [GPU 'ya özgü dağıtım](https://go.microsoft.com/fwlink/?linkid=2152189) bildirimiyle bulabilirsiniz. 

Alternatif olarak, IoT Hub 'ınız ile işlemler gerçekleştirmek için Visual Studio Code için Azure IoT uzantılarını kullanabilirsiniz. Daha fazla bilgi edinmek için [Visual Studio Code Azure IoT Edge modüllerini dağıtma](../../iot-edge/how-to-deploy-modules-vscode.md) bölümüne gidin.

> [!NOTE] 
> *Uzamsal-analiz-telegraf* ve *uzamsal analiz-tanılama* kapsayıcıları isteğe bağlıdır. Bunları dosyadaki *DeploymentManifest.js* kaldırmaya karar verebilirsiniz. Daha fazla bilgi için bkz. [telemetri ve sorun giderme](./spatial-analysis-logging.md) makalesi. GitHub 'daki dosyalar üzerinde üç örnek *DeploymentManifest.js* bulabilirsiniz, [Azure Stack Edge cihazları](https://go.microsoft.com/fwlink/?linkid=2142179), [Masaüstü MAKINESI](https://go.microsoft.com/fwlink/?linkid=2152189)veya [GPU ile Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

IoT Edge modülü için **ortam değişkenlerinin** çoğu, yukarıda bağlantılı dosyalardaki örnek *DeploymentManifest.js* zaten ayarlanmıştır. Dosyasında, `BILLING_ENDPOINT` `API_KEY` aşağıda gösterildiği gibi ve ortam değişkenlerini aratın. Değerleri, bitiş noktası URI 'SI ve daha önce oluşturduğunuz API anahtarı ile değiştirin. EULA değerinin "kabul et" olarak ayarlandığından emin olun. 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>İşlem parametrelerini yapılandırma

Artık *uzamsal analiz* kapsayıcısının ilk yapılandırması tamamlanana kadar, bir sonraki adım işlemler parametrelerini yapılandırmak ve bunları dağıtıma eklemektir. 

İlk adım, yukarıda belirtilen örnek dağıtım bildirimini güncelleştirmek ve operationId 'yi `cognitiveservices.vision.spatialanalysis-personcount` aşağıda gösterildiği gibi yapılandırmaktır:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Dağıtım bildirimi güncelleştirildikten sonra, kamerayı yüklemek, kamera URL 'sini yapılandırmak ve Kullanıcı adını ve parolayı yapılandırmak için kamera üreticisinin yönergelerini izleyin. 

Ardından, `VIDEO_URL` KAMERANıN RTSP URL 'sini ve kameraya bağlanmak için kimlik bilgilerini ayarlayın.

Sınır cihazında birden fazla GPU varsa, bu işlemin çalıştırılacağı GPU 'YU seçin. Aynı anda tek bir GPU üzerinde çalışan 8 ' den fazla işlemi olmayan işlemlerin yük dengelediğinizden emin olun.  

Sonra, kişileri saymak istediğiniz bölgeyi yapılandırın. Bölge çokgeni yapılandırmak için ilk olarak, kameradan bir çerçeve almak üzere üreticinin yönergelerini izleyin. Çokgenin her izdüşgenini belirlemek için çerçevede bir nokta seçin, karenin sol üst köşesine göre noktanın x, y piksel koordinatlarını alın ve ilgili kare boyutlarına göre bölün. Sonuçları köşe için x, y koordinatları olarak ayarlayın. Alanda bölge Çokgen yapılandırmasını ayarlayabilirsiniz `SPACEANALYTICS_CONFIG` .

Bu, 1920/1080 boyutundaki bir çerçeve için köşe koordinatlarının nasıl hesaplanacağını gösteren örnek bir video çerçevesidir.
![Örnek video çerçevesi](./media/spatial-analysis/sample-video-frame.jpg)

Algılanan kişilerin ne zaman sayıldığını ve olayların üretilme için de bir güven eşiği seçebilirsiniz. Tüm olayların çıkış olmasını istiyorsanız eşiği 0 olarak ayarlayın.

### <a name="execute-the-deployment"></a>Dağıtımı Yürüt

Dağıtım bildirimi tamamlandığına göre, kapsayıcıyı ana bilgisayarda bir IoT Edge modülü olarak dağıtmak için Azure CLı 'da bu komutu kullanın.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Gerekli parametreleri girin:

* IoT Hub adı: Azure IoT Hub adınız
* DeploymentManifest.js: dağıtım dosyanızın adı
* IoT Edge cihaz adı: ana bilgisayarınızın IoT Edge cihaz adı
* Abonelik: abonelik KIMLIĞINIZ veya adınız

Bu komut dağıtıma başlayacaktır ve dağıtım durumunu Azure portal Azure IoT Hub örneğiniz içinde görüntüleyebilirsiniz. Durum 417 olarak gösterilebilir: cihaz kapsayıcı görüntülerini indirene ve çalışmaya başladıktan sonra *cihazın dağıtım yapılandırması ayarlı değildir* .

### <a name="validate-that-the-deployment-was-successful"></a>Dağıtımın başarılı olduğunu doğrulama

Azure portal IoT Hub örneğinizin uzamsal analiz modülünün IoT Edge modülü ayarlarında *çalışma zamanı durumunu* bulun. *Çalışma zamanı durumu* Için **Istenen değer** ve **bildirilen değer** olmalıdır `Running` . Azure portal nasıl görüneceğine bakmak için aşağıya bakın.

![Örnek dağıtım doğrulaması](./media/spatial-analysis/deployment-verification.png)

Bu noktada, uzamsal analiz kapsayıcısı işlemi çalıştırıyor. Bu BT, işlem için AI öngörülerini yayar `cognitiveservices.vision.spatialanalysis-personcount` ve bu öngörüleri Azure IoT Hub örneğinize telemetri olarak yönlendirir. Ek kameraları yapılandırmak için, dağıtım bildirim dosyasını güncelleştirebilir ve dağıtımı yeniden çalıştırabilirsiniz.

## <a name="person-counting-web-application"></a>Web uygulaması için kişi sayma

Bu kişi, Web uygulamasını bir örnek Web uygulamasını hızlı bir şekilde yapılandırmanıza ve Azure ortamınızda barındırmanıza olanak sağlar.

### <a name="get-the-person-counting-app-container"></a>Uygulama kapsayıcısını saymak için kişi alın

Bu uygulamanın Azure Container Registry kullanılabilir bir kapsayıcı formu. İndirmek için aşağıdaki docker pull komutunu kullanın. Erişim belirteci için adresinde Microsoft ile iletişim kurun projectarchon@microsoft.com .

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Kapsayıcıyı Azure Container Registry (ACR) gönderin.

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Kapsayıcıyı yüklemek için yeni bir Azure App Service oluşturun ve gerekli parametreleri girin. Ardından **Docker** sekmesine gidip **tek kapsayıcı**' ı seçin ve ardından **Azure Container Registry**. Yukarıdaki görüntüyü gönderdiğiniz Azure Container Registry örneğinizi kullanın.

![Görüntü ayrıntılarını girin](./media/spatial-analysis/solution-app-create-screen.png)

Yukarıdaki parametreleri girdikten sonra, **gözden geçir** ' e tıklayın ve uygulamayı oluşturun.

### <a name="configure-the-app"></a>Uygulamayı yapılandırma 

Kurulumun tamamlanmasını bekleyin ve Azure portal kaynak sayfasına gidin. **Yapılandırma** bölümüne gidin ve aşağıdaki iki **uygulama ayarını** ekleyin.

* `EventHubConsumerGroup` – Azure IoT Hub tüketici grubunun dize adı, IoT Hub yeni bir tüketici grubu oluşturabilir veya varsayılan grubu kullanabilirsiniz. 
* `IotHubConnectionString` – Azure IoT Hub bağlantı dizesi, bu, Azure IoT Hub kaynağınızın anahtarlar bölümünden alınabilir ![ parametreleri yapılandırma](./media/spatial-analysis/solution-app-config-page.png)

Bu 2 ayar eklendikten sonra **Kaydet**' e tıklayın. Ardından sol gezinti menüsünde **kimlik doğrulama/yetkilendirme** ' ye tıklayın ve istediğiniz kimlik doğrulaması düzeyiyle güncelleştirin. Azure Active Directory (Azure AD) Express önerilir. 

### <a name="test-the-app"></a>Uygulamayı test etme

Azure hizmetine gidin ve dağıtımın başarılı olduğunu ve Web uygulamasının çalıştığını doğrulayın. Çalışan uygulamayı görüntülemek için yapılandırılan URL 'ye gidin: `<yourapp>.azurewebsites.net`

![Dağıtımı test etme](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>PersonCount kaynak kodunu al
Bu uygulamanın kaynak kodunu görüntülemek veya değiştirmek isterseniz, [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-spatial-analysis)bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Uzamsal analiz işlemlerini yapılandırma](./spatial-analysis-operations.md)
* [Günlüğe kaydetme ve sorun giderme](spatial-analysis-logging.md)
* [Kamera Yerleştirme Kılavuzu](spatial-analysis-camera-placement.md)
* [Bölge ve satır yerleştirme Kılavuzu](spatial-analysis-zone-line-placement.md)
