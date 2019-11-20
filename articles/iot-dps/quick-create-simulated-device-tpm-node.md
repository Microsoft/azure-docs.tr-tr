---
title: 'Hızlı başlangıç: node. js kullanarak Azure IoT Hub sanal bir TPM cihazı sağlama'
description: Azure Hızlı Başlangıcı - Azure IoT Hub Cihazı Sağlama Hizmeti için Node.js cihaz SDK'sını kullanarak sanal bir TPM cihazı oluşturma ve sağlama. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1495b8b62f775fcc8ae68fe08d9736beb72d67c6
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903542"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Hızlı başlangıç: IoT Hub cihaz sağlama hizmeti için Node. js cihaz SDK 'sını kullanarak sanal bir TPM cihazı oluşturma ve sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Bu adımlar, Windows işletim sistemi çalıştıran geliştirme makinenizde sanal makine oluşturmayı, Windows TPM simülatörünü cihazın [Donanım Güvenlik Modülü (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) olarak çalıştırmayı ve örnek kodlar kullanarak bu sanal cihazı Cihaz Sağlama Hizmeti ve IoT hub’ınızla bağlamayı gösterir. 

Otomatik sağlama işlemini bilmiyorsanız, [Otomatik sağlama kavramlarını](concepts-auto-provisioning.md) gözden geçirdiğinizden emin olun. Ayrıca devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalıyla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun. 

Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:
- [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
- [Bireysel kayıtlar](concepts-service.md#individual-enrollment): Tek bir cihazı kaydetmek için kullanılır.

Bu makalede bireysel kayıtlar gösterilmektedir.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Ortamı hazırlama 

1. Makinenizde [Node.js v4.0 veya üzeri](https://nodejs.org) bir sürümün yüklü olduğundan emin olun.

1. `git` uygulamasının makinenizde yüklü olduğundan ve komut penceresinden erişilebilir ortam değişkenlerine eklendiğinden emin olun. Yüklenecek [ araçlarının son sürümleri için ](https://git-scm.com/download/)Software Freedom Conservancy’nin Git istemci araçlarına`git` bakın. Bunlara yerel Git deponuzla etkileşim kurmak için kullanabileceğiniz bir komut satırı uygulaması olan **Git Bash** dahildir. 


## <a name="simulate-a-tpm-device"></a>TPM cihazının benzetimini yapma

1. Bir komut istemi veya Git Bash’i açın. `azure-utpm-c` GitHub deposunu kopyalayın:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. GitHub kök klasörüne gidin ve [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) simülatörünü çalıştırın. 2321 ve 2322 bağlantı noktalarında bulunan bir yuva üzerinden dinler. Bu komut penceresini kapatmayın; Hızlı Başlangıç kılavuzu sonuna kadar simülatörü çalışır durumda tutmanız gerekir: 

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
    > Yukarıdaki paketleri yüklerken bazı bilinen sorunlarla karşılaşabilirsiniz. Bu sorunları çözmek için, `npm install --global --production windows-build-tools`Yönetici olarak çalıştır**modunda açtığınız komut istemini kullanarak** komutunu çalıştırın; yolu, yüklü sürümünüzle değiştirdikten sonra `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` komutunu çalıştırın ve ardından yukarıdaki yükleme komutlarını yeniden çalıştırın.
    >

1. Kayıt sırasında kullandığınız bileşenleri içeren aşağıdaki paketleri yükleyin:

   - TPM ile çalışan bir güvenlik istemcisi: `azure-iot-security-tpm`
   - cihazın, Cihaz Sağlama Hizmeti’ne bağlanması için bir aktarım: `azure-iot-provisioning-device-http` veya `azure-iot-provisioning-device-amqp`
   - aktarım veya güvenlik istemcisini kullanmak için bir istemci: `azure-iot-provisioning-device`

     Cihaz kaydedildikten sonra, kayıt sırasında sağlanan kimlik bilgilerini kullanarak cihazınızı bağlamak için her zamanki IoT Hub Cihaz İstemcisi paketlerini kullanabilirsiniz. Şunlar gerekir:

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

1. Çıktı penceresinde cihaz kaydı için gereken **_Onay Anahtarını_** ve **_Kayıt Kimliğini_** görüntülenir. Bu değerleri not alın. 


## <a name="create-a-device-entry"></a>Cihaz girdisi oluşturma

1. Azure portalında oturum açın, sol taraftaki menüden **Tüm kaynaklar** düğmesine tıklayın ve Cihaz Sağlama hizmetinizi açın.

1. Cihaz Sağlama Hizmeti özet dikey penceresinde, **Kayıtları yönet**’i seçin. **Bireysel Kayıtlar** sekmesini seçin ve en üstteki **Bireysel kayıt ekle** düğmesine tıklayın. 

1. **Kayıt Ekle** altına aşağıdaki bilgileri girin:
   - Kimlik onay **Mekanizması** olarak *TPM* seçeneğini belirleyin.
   - TPM cihazınızın *Kayıt kimliği* ve *Onay anahtarı* bilgilerini girin.
   - İsteğe bağlı olarak, aşağıdaki bilgileri sağlayabilirsiniz:
       - Sağlama hizmetinizle bağlanacak IoT hub'ını seçin.
       - Benzersiz bir cihaz kimliği girin. Cihazınızı adlandırırken gizli veriler kullanmaktan kaçının.
       - **Başlangıç cihaz ikizi durumu** alanını cihaz için istenen başlangıç yapılandırmasına göre güncelleştirin.
   - Tamamlandığında **Kaydet** düğmesine tıklayın. 

     ![Portal dikey penceresinde cihaz kayıt bilgilerini girme](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Kayıt başarıyla tamamlanınca cihazınızın *Kayıt Kimliği* listenin altında *Bireysel Kayıtlar* sekmesinde görünür. 


## <a name="register-the-device"></a>Cihazı kaydetme

1. Azure portalında Cihaz Sağlama hizmetiniz için **Genel Bakış** dikey penceresini seçin ve **_Genel Cihaz Uç Noktası_** ile **_Kimlik Kapsamı_** değerlerini not alın.

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

1. **globalDeviceEndpoint** ve **idScope** değişkenlerini ekleyin ve **ProvisioningDeviceClient** örneğini oluştururken bunları kullanın. **{globalDeviceEndpoint}** ve **{idScope}** değerlerini **1. Adım _’daki_** Genel Cihaz Uç Noktası **_ve_** Kimlik Kapsamı değerleriyle değiştirin:
   
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

1. IoT hub bilgilerinizi almak için cihaz önyüklemesi ve Cihaz Sağlama Hizmetine bağlanma benzetimi gerçekleştiren iletilere dikkat edin. Sanal cihazınızın, sağlama hizmetinizle bağlantılı IoT hub ile başarıyla sağlanmasından sonra cihaz kimliği hub'ın **IoT Cihazları** dikey penceresinde görünür. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device/hub-registration.png) 

    Cihazınız için *başlangıç cihaz ikizi durumu* ayarının kayıt girişindeki varsayılan değerini değiştirdiyseniz istenen ikili durumu hub'dan çekerek ona göre hareket edebilir. Daha fazla bilgi için bkz. [IoT Hub'daki cihaz ikizlerini kavrama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneği üzerinde çalışmaya ve inceleme yapmaya devam etmeyi planlıyorsanız bu Hızlı Başlangıç’ta oluşturulan kaynakları silmeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın:

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
1. Makinenizde TPM simülatörü penceresini kapatın.
1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından Cihaz Sağlama hizmetini seçin. Hizmetinizin kayıtları **yönetme** dikey penceresini açın ve sonra **bireysel** Kayıtlar sekmesine tıklayın. bu hızlı BAŞLANGıÇTA kaydettiğiniz cihazın *kayıt kimliği* ' ni seçin ve üstteki **Sil** düğmesine tıklayın. 
1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından IoT hub’ınızı seçin. Hub'ınızın **IoT Cihazları** dikey penceresini açıp bu Hızlı Başlangıç adımlarını kullanarak kaydettiğiniz cihazın *CİHAZ KİMLİĞİ* değerini seçip en üstte bulunan **Sil** düğmesine tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta makinenizde bir TPM sanal cihazı oluşturdunuz ve IoT Hub Cihaz Sağlama Hizmeti ile IoT hub'ınıza sağladınız. TPM cihazınızı programlı bir şekilde kaydetmeyi öğrenmek için TPM cihazının programlı kaydının yer aldığı Hızlı Başlangıç adımlarına gidin. 

> [!div class="nextstepaction"]
> [Azure Hızlı Başlangıcı - TPM cihazını Azure IoT Hub Cihaz Sağlama Hizmeti'ne kaydetme](quick-enroll-device-tpm-node.md)
