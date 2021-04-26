---
title: Hızlı başlangıç-Azure IoT Hub Node.js kullanarak sanal TPM cihazı sağlama
description: Hızlı başlangıç-Azure IoT Hub cihaz sağlama hizmeti (DPS) için Node.js cihaz SDK 'sını kullanarak sanal bir TPM cihazı oluşturun ve sağlayın. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 6e7e986f658570553763001afdd58d7bb1880f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968188"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Hızlı başlangıç: IoT Hub cihaz sağlama hizmeti için Node.js cihaz SDK 'sını kullanarak sanal bir TPM cihazı oluşturma ve sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Bu hızlı başlangıçta, bir Windows bilgisayarda sanal bir IoT cihazı oluşturacaksınız. Sanal cihaz, donanım güvenlik modülü (HSM) olarak bir TPM simülatörü içerir. Cihaz sağlama hizmeti (DPS) ile tek bir kayıt kullanarak bu sanal cihazı IoT Hub 'ınıza bağlamak için cihaz örnek Node.js kodunu kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

- [Sağlama](about-iot-dps.md#provisioning-process) kavramları hakkında bilgi edinin.
- [Azure Portal Ile cihaz sağlama hizmeti IoT Hub ayarlama](./quick-setup-auto-provision.md)işlemi tamamlandı.
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Ortamı hazırlama 

1. Makinenizde [Node.js v4.0 veya üzeri](https://nodejs.org) bir sürümün yüklü olduğundan emin olun.

1. `git` uygulamasının makinenizde yüklü olduğundan ve komut penceresinden erişilebilir ortam değişkenlerine eklendiğinden emin olun. Yüklenecek `git` araçlarının son sürümleri için [Software Freedom Conservancy’nin Git istemci araçlarına](https://git-scm.com/download/) bakın. Bunlara yerel Git deponuzla etkileşim kurmak için kullanabileceğiniz bir komut satırı uygulaması olan **Git Bash** dahildir. 


## <a name="simulate-a-tpm-device"></a>TPM cihazının benzetimini yapma

1. Bir komut istemi veya Git Bash’i açın. `azure-utpm-c` GitHub deposunu kopyalayın:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. GitHub kök klasörüne gidin ve sanal cihazın [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 'si olması için [TPM](/windows/device-security/tpm/trusted-platform-module-overview) simülatörünü çalıştırın. 2321 ve 2322 bağlantı noktalarında bulunan bir yuva üzerinden dinler. Bu komut penceresini kapatmayın; Bu hızlı başlangıç kılavuzunun sonuna kadar bu simülatörü çalışır durumda tutmanız gerekir: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. **registerdevice** adlı yeni, boş bir klasör oluşturun. Komut isteminizde aşağıdaki komutu kullanarak **registerdevice** klasöründe bir package.json dosyası oluşturun. `npm` tarafından sorulan tüm soruları yanıtladığınızdan emin olun veya size uygun geliyorsa varsayılanları kabul edin:
   
    ```cmd/sh
    npm init
    ```

1. Aşağıdaki precursor paketlerini yükleyin:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Yukarıdaki paketleri yüklerken bazı bilinen sorunlarla karşılaşabilirsiniz. Bu sorunları çözmek için, **Yönetici olarak çalıştır** modunda açtığınız komut istemini kullanarak `npm install --global --production windows-build-tools` komutunu çalıştırın; yolu, yüklü sürümünüzle değiştirdikten sonra `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` komutunu çalıştırın ve ardından yukarıdaki yükleme komutlarını yeniden çalıştırın.
    >

1. Kayıt sırasında kullandığınız bileşenleri içeren aşağıdaki paketleri yükleyin:

   - TPM ile çalışan bir güvenlik istemcisi: `azure-iot-security-tpm`
   - cihazın, Cihaz Sağlama Hizmeti’ne bağlanması için bir aktarım: `azure-iot-provisioning-device-http` veya `azure-iot-provisioning-device-amqp`
   - aktarım veya güvenlik istemcisini kullanmak için bir istemci: `azure-iot-provisioning-device`

     Cihaz kaydedildikten sonra, kayıt sırasında sağlanan kimlik bilgilerini kullanarak cihazınızı bağlamak için her zamanki IoT Hub Cihaz İstemcisi paketlerini kullanabilirsiniz. Şunlara ihtiyacınız vardır:

   - cihaz istemcisi: `azure-iot-device`
   - aktarım: `azure-iot-device-amqp`, `azure-iot-device-mqtt` veya `azure-iot-device-http` öğelerinden herhangi biri
   - önceden yüklemiş olduğunuz güvenlik istemcisi: `azure-iot-security-tpm`

     > [!NOTE]
     > Aşağıdaki örneklerde `azure-iot-provisioning-device-http` ve `azure-iot-device-mqtt` aktarımları kullanılır.
     > 

     **registerdevice** klasöründe komut istemcinizde aşağıdaki komutları çalıştırarak bu paketlerin hepsini aynı anda yükleyebilirsiniz:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Bir metin düzenleyicisi kullanarak **registerdevice** klasöründe yeni bir **ExtractDevice.js** dosyası oluşturun.

1. Aşağıdaki `require` deyimlerini **ExtractDevice.js** dosyasının başlangıcına ekleyin:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Yöntemi uygulamak için aşağıdaki işlevi ekleyin:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. **ExtractDevice.js** dosyasını kaydedip kapatın. Örneği çalıştırın:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. Çıkış penceresinde, cihaz kaydı için gereken **_onay anahtarı_** ve **_kayıt kimliği_** görüntülenir. Bu değerleri not alın. 


## <a name="create-a-device-entry"></a>Cihaz girdisi oluşturma

Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:

- [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
- [Bireysel](concepts-service.md#individual-enrollment)kayıtlar: tek bir cihazı kaydetmek için kullanılır.

Bu makalede, bireysel kayıtlar gösterilmektedir.

1. Azure portal oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve cihaz sağlama hizmetinizi açın.

1. Cihaz sağlama hizmeti menüsünden kayıtları **Yönet**' i seçin. **Bireysel** kayıtlar sekmesini seçin ve üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 

1. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin:
   - Kimlik onay *Mekanizması* olarak **TPM** seçeneğini belirleyin.
   - Daha önce not ettiğiniz değerlerden TPM cihazınızın *kayıt kimliği* ve *onay anahtarı* ' nı girin.
   - Sağlama hizmetinizle bağlanacak IoT hub'ını seçin.
   - İsteğe bağlı olarak, aşağıdaki bilgileri sağlayabilirsiniz:
       - Benzersiz bir *CIHAZ kimliği* girin. Cihazınızı adlandırırken gizli veriler kullanmaktan kaçının. Bir tane sağlamadıysanız, bunun yerine cihazı tanımlamak için kayıt KIMLIĞI kullanılır.
       - **Başlangıç cihaz ikizi durumu** alanını cihaz için istenen başlangıç yapılandırmasına göre güncelleştirin.
   - Tamamlandıktan sonra **Kaydet** düğmesine basın. 

     ![Portal dikey penceresinde cihaz kayıt bilgilerini girme](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Kayıt başarıyla tamamlanınca cihazınızın *Kayıt Kimliği* listenin altında *Bireysel Kayıtlar* sekmesinde görünür. 


## <a name="register-the-device"></a>Cihazı kaydetme

1. Azure portal, cihaz sağlama hizmetiniz için **genel bakış** dikey penceresini seçin ve **_genel cihaz uç noktası_** ve **_kimlik kapsamı_** değerlerini aklınızda edin.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Bir metin düzenleyicisi kullanarak **registerdevice** klasöründe yeni bir **RegisterDevice.js** dosyası oluşturun.

1. Aşağıdaki `require` deyimlerini **RegisterDevice.js** dosyasının başlangıcına ekleyin:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Node.js için Azure IoT SDK'sı**, _AMQP_, _AMQP WS_ ve _MQTT WS_ gibi ek protokolleri destekler.  Diğer örnekler için bkz. [Node.js için Cihaz Sağlama Hizmeti SDK'sı örnekleri](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. **globalDeviceEndpoint** ve **idScope** değişkenlerini ekleyin ve **ProvisioningDeviceClient** örneğini oluştururken bunları kullanın. **{globalDeviceEndpoint}** ve **{idScope}** değerlerini **1. Adım**’daki **_Genel Cihaz Uç Noktası_** ve **_Kimlik Kapsamı_** değerleriyle değiştirin:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Yöntemi cihazda uygulamak için aşağıdaki işlevi ekleyin:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. **RegisterDevice.js** dosyasını kaydedip kapatın. Örneği çalıştırın:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. IoT hub bilgilerinizi almak için cihaz önyüklemesi ve Cihaz Sağlama Hizmetine bağlanma benzetimi gerçekleştiren iletilere dikkat edin. Sanal cihazınızın, sağlama hizmetinize bağlı olan IoT Hub 'ına başarıyla sağlanması sırasında, cihazın **IoT cihazları** dikey PENCERESINDE cihaz kimliği görüntülenir. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device/hub-registration.png) 

    Cihazınız için *başlangıç cihaz ikizi durumu* ayarının kayıt girişindeki varsayılan değerini değiştirdiyseniz istenen ikili durumu hub'dan çekerek ona göre hareket edebilir. Daha fazla bilgi için bkz. [IoT Hub'daki cihaz ikizlerini kavrama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
1. Makinenizde TPM simülatörü penceresini kapatın.
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. Hizmetiniz için kayıtları **Yönet** dikey penceresini açın ve **bireysel** kayıtlar sekmesini seçin. Bu hızlı başlangıçta kaydettiğiniz cihazın *kayıt kimliği* ' nin yanındaki onay kutusunu işaretleyin ve bölmenin en üstündeki **Sil** düğmesine basın. 
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. Hub 'ınız için **IoT cihazları** dikey penceresini açın, bu hızlı başlangıçta kaydettiğiniz CIHAZıN *cihaz kimliği* ' nin yanındaki onay kutusunu işaretleyin ve ardından bölmenin en üstündeki **Sil** düğmesine basın.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, makinenizde bir TPM sanal cihazı oluşturdunuz ve IoT Hub cihaz sağlama hizmetini kullanarak IoT Hub 'ınıza sağladınız. TPM cihazınızı programlı bir şekilde kaydetmeyi öğrenmek için bir TPM cihazının programlı kaydı için hızlı başlangıç 'a geçin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-TPM cihazını Azure IoT Hub cihaz sağlama hizmeti 'ne kaydetme](quick-enroll-device-tpm-node.md)