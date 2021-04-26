---
title: Azure IoT Central uygulamasına IoT Edge saydam bir ağ geçidi bağlama
description: IoT Edge saydam bir ağ geçidi aracılığıyla Cihazları IoT Central uygulamasına bağlama
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 4e88ad58c7baba1c66c30df3f4effdbf11371c18
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045340"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>IoT Edge saydam bir ağ geçidi aracılığıyla cihazları bağlama

IoT Edge cihaz, yerel bir ağdaki diğer cihazlar ve IoT Central uygulamanız arasında bağlantı sağlayan bir ağ geçidi olarak davranabilir. Cihaz IoT Central uygulamanıza doğrudan erişene zaman bir ağ geçidi kullanırsınız.

IoT Edge, [ *saydam* ve *çeviri* ağ geçidi düzenlerini](../../iot-edge/iot-edge-as-gateway.md)destekler. Bu makalede, saydam ağ geçidi deseninin nasıl uygulanacağı özetlenmektedir. Bu düzende ağ geçidi, iletileri aşağı akış cihazından IoT Central uygulamanızdaki IoT Hub uç noktasına geçirir.

Bu makalede, sanal makineler, aşağı akış aygıtını ve ağ geçidini barındırmak için kullanılır. Gerçek bir senaryoda, aşağı akış cihazı ve ağ geçidi yerel ağınızdaki fiziksel cihazlarda çalışır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Özel uygulama **> özel uygulama** şablonunu kullanarak bir IoT Central uygulaması oluşturmak için [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md) hızlı başlangıcı ' nı doldurun.

Bu makaledeki adımları izlemek için aşağıdaki dosyaları bilgisayarınıza indirin:

- [Termostat cihaz modeli](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Saydam ağ geçidi bildirimi](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Cihaz şablonu ekleme

Hem aşağı akış cihazları hem de ağ geçidi cihazı IoT Central cihaz şablonları gerektirir. IoT Central, bağlı olduktan sonra bunları görüntüleyebilmeniz ve yönetebilmeniz için aşağı akış cihazlarınız ve ağ geçidiniz arasındaki ilişkiyi modellemenizi sağlar.

Bir aşağı akış cihazına yönelik bir cihaz şablonu oluşturmak için, cihazınızın yeteneklerini modelleyen standart bir cihaz şablonu oluşturun. Bu makalede gösterilen örnek, termostat cihaz modelini kullanır.

Bir aşağı akış cihazına yönelik bir cihaz şablonu oluşturmak için:

1. Bir cihaz şablonu oluşturun ve şablon türü olarak **IoT cihazı** ' nı seçin.

1. Sihirbazın **Özelleştir** sayfasında, cihaz şablonu Için *termostat* gibi bir ad girin.

1. Cihaz şablonunu oluşturduktan sonra **modeli Içeri aktar**' ı seçin. Daha önce indirdiğiniz dosya *thermostat-1.js* gibi bir model seçin.

1. Termostat için bazı varsayılan görünümler oluşturmak için, görünümler ' i seçin ve **varsayılan görünümleri oluştur**' u seçin.

1. Cihaz şablonunu yayımlayın.

Saydam IoT Edge ağ geçidi için bir cihaz şablonu oluşturmak için:

1. Bir cihaz şablonu oluşturun ve şablon türü olarak **Azure IoT Edge** seçin.

1. Sihirbazın **Özelleştir** sayfasında, cihaz şablonu Için *sınır ağ geçidi* gibi bir ad girin.

1. Sihirbazın **Özelleştir** sayfasında, **ağ geçidi cihazını aşağı akış cihazlarıyla** denetleyin.

1. Sihirbazın **Özelleştir** sayfasında, **Araştır**' ı seçin. Daha önce indirdiğiniz dosyaya *EdgeTransparentGatewayManifest.js* yükleyin.

1. Aşağı akış cihaz şablonuna **ilişkilerde** bir giriş ekleyin.

Aşağıdaki ekran görüntüsünde, **termostat** cihaz şablonunu kullanan aşağı akış cihazlarına sahip bir IoT Edge ağ geçidi cihazının **ilişkiler** sayfası gösterilmektedir:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Bir termostat aşağı akış cihaz şablonuyla IoT Edge ağ geçidi cihaz şablonu ilişkisini gösteren ekran görüntüsü.":::

Önceki ekran görüntüsünde, hiçbir modül tanımlanmadığında bir IoT Edge ağ geçidi cihaz şablonu gösterilmektedir. IoT Edge çalışma zamanının iletileri aşağı akış cihazlarından IoT Central ilettiğinden, saydam bir ağ geçidi herhangi bir modül gerektirmez. Ağ geçidinin kendisini telemetri gönderebilmesi, özellikleri eşitlemesini veya komutları işlemesi gerekiyorsa, bu özellikleri varsayılan bileşende veya bir modülde tanımlayabilirsiniz.

Ağ geçidini ve aşağı akış cihaz şablonlarını yayımlamadan önce gerekli tüm bulut özelliklerini ve görünümlerini ekleyin.

## <a name="add-the-devices"></a>Cihazları ekleme

Cihazları IoT Central uygulamanıza eklediğinizde, aşağı akış cihazları ile saydam ağ geçidi arasındaki ilişkiyi tanımlayabilirsiniz.

Cihazları eklemek için:

1. IoT Central uygulamanızda cihazlar sayfasına gidin.

1. Saydam ağ geçidi IoT Edge cihazının bir örneğini ekleyin. Bu makalede, ağ geçidi cihaz KIMLIĞI `edgegateway` .

1. Aşağı akış cihazının bir veya daha fazla örneğini ekleyin. Bu makalede, aşağı akış cihazları kimlikleri ve kimliği olan termostats ' dir `thermostat1` `thermostat2` .

1. Cihaz listesinde, her bir aşağı akış cihazını seçin ve **ağ geçidine Ekle**' yi seçin.

Aşağıdaki ekran görüntüsünde, **aşağı akış cihazları** sayfasında bir ağ geçidine eklenmiş cihazların listesini görüntüleyebileceği gösterilmektedir:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Saydam bir ağ geçidine bağlı aşağı akış cihazlarının listesini gösteren ekran görüntüsü.":::

Saydam bir ağ geçidinde, aşağı akış cihazları ağ geçidinin barındırdığı özel bir modüle değil ağ geçidine bağlanır.

Cihazları dağıtmadan önce, şunlar gerekir:

- IoT Central uygulamanızın **kimlik kapsamı** .
- Ağ geçidi ve aşağı akış cihazları için **CIHAZ kimliği** değerleri.
- Ağ geçidi ve aşağı akış cihazları için **birincil anahtar** değerleri.

Bu değerleri bulmak için, cihaz listesindeki her bir cihaza gidin ve **Bağlan**' ı seçin. Devam etmeden önce bu değerleri bir yere göz önüne alın.

## <a name="deploy-the-gateway-and-devices"></a>Ağ geçidini ve cihazları dağıtma

Bu senaryoyu denemenizi sağlamak için aşağıdaki adımlarda, ağ geçidini ve aşağı akış cihazlarını Azure sanal makinelere nasıl dağıtacağınız gösterilmektedir. Gerçek bir senaryoda, aşağı akış cihazı ve ağ geçidi, yerel ağınızdaki fiziksel cihazlarda çalışır.

Saydam ağ geçidi senaryosunu denemek için, iki Linux sanal makinesi dağıtmak üzere aşağıdaki düğmeyi seçin. Bir sanal makine, saydam bir IoT Edge ağ geçidiyle, diğeri de bir termostat benzetimi yapan bir aşağı akış aygıtıdır:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

İki sanal makine dağıtıldığında ve çalıştırıldığında, sanal makinede IoT Edge ağ geçidi cihazının çalıştığını doğrulayın `edgegateway` :

1. IoT Central uygulamanızda **cihazlar** sayfasına gidin. IoT Edge ağ geçidi cihazı IoT Central **bağlıysa, durumu sağlanır.**

1. IoT Edge ağ geçidi cihazını açın ve **modüller sayfasında modüllerin** durumunu doğrulayın. IoT Edge çalışma zamanı başarıyla başlatılırsa, **$edgeAgent** ve **$EdgeHub** modüllerinin durumu **çalışıyor**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="IoT Edge ağ geçidinde çalışan $edgeAgent ve $edgeHub modüllerini gösteren ekran görüntüsü.":::

> [!TIP]
> Sanal makine başlatılırken birkaç dakika beklemeniz gerekebilir ve cihazın IoT Central uygulamanızda sağlanması gerekir.

## <a name="configure-the-gateway"></a>Ağ geçidini yapılandırma

IoT Edge cihazınızın saydam bir ağ geçidi olarak çalışması için, bazı sertifikalara, kimliğini herhangi bir aşağı akış cihazına kanıtlamaları gerekir. Bu makalede tanıtım sertifikaları kullanılmaktadır. Bir üretim ortamında, sertifika yetkilinizdeki sertifikaları kullanın.

Tanıtım sertifikalarını oluşturmak ve ağ geçidi cihazınıza yüklemek için:

1. Ağ geçidi cihazı sanal makinenizde bağlantı kurmak ve oturum açmak için SSH kullanın.

1. IoT Edge depoyu kopyalamak ve tanıtım sertifikalarınızı oluşturmak için aşağıdaki komutları çalıştırın:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Önceki komutları çalıştırdıktan sonra, aşağıdaki dosyalar sonraki adımlarda kullanıma hazırız:

    - *~/certs/certs/Azure-iot-test-only.root.ca.cert.pem* -IoT Edge senaryosunu test etmek için tüm diğer tanıtım sertifikalarını oluşturmak için kullanılan kök CA sertifikası.
    - *~/certs/certs/iot-Edge-Device-mycacert-Full-Chain.cert.pem* - *config. YAML* DOSYASıNDAN başvurulan bir cihaz CA sertifikası. Bir ağ geçidi senaryosunda, bu CA sertifikası IoT Edge cihazının kimliğini aşağı akış cihazlarına nasıl doğruladığını gösterir.
    - *~/certs/Private/iot-Edge-Device-mycacert.Key.pem* -cihaz CA sertifikasıyla ilişkili özel anahtar.

    Bu tanıtım sertifikaları hakkında daha fazla bilgi edinmek için bkz. [IoT Edge cihaz özelliklerini test etmek için tanıtım sertifikaları oluşturma](../../iot-edge/how-to-create-test-certificates.md).

1. *Config. YAML* dosyasını bir metin düzenleyicisinde açın. Örnek:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Ayarları bulun `Certificate settings` . Sertifika ayarlarını aşağıdaki şekilde kaldırın ve değiştirin:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    Yukarıda gösterilen örnek, **AzureUser** olarak oturum açtığınız ve "mycacert" adlı BIR cihaz CA sertifikası oluşturduğumuz varsayılmaktadır.

1. Değişiklikleri kaydedin ve IoT Edge çalışma zamanını yeniden başlatın:

    ```bash
    sudo systemctl restart iotedge
    ```

IoT Edge çalışma zamanı, değişikliklerinizin ardından başarıyla başlarsa, **$edgeAgent** ve **$edgeHub** modüllerinin durumu IoT Central ağ geçidiniz için **modüller** sayfasında **çalışıyor** olarak değişir.

Çalışma zamanı başlamazsa, *config. YAML* 'de yaptığınız değişiklikleri denetleyin ve [IoT Edge cihazınızda sorun giderme](../../iot-edge/troubleshoot.md)bölümüne bakın.

Saydam ağ geçidiniz artık yapılandırılmış ve aşağı akış cihazlarından telemetri iletmeyi başlamaya hazır.

## <a name="provision-a-downstream-device"></a>Aşağı akış cihazı sağlama

Şu anda IoT Edge IoT Central uygulamanıza otomatik olarak bir aşağı akış cihazı sağlayamaz. Aşağıdaki adımlarda, cihazı sağlama adımları gösterilmektedir `thermostat1` . Bu adımları tamamlayabilmeniz için Python 3,6 (veya üzeri) yüklü ve internet bağlantısı olan bir ortama ihtiyacınız vardır. [Azure Cloud Shell](https://shell.azure.com/) Python 3,7 önceden yüklenmiş olarak bulunur:

1. Modülünü yüklemek için aşağıdaki komutu çalıştırın `azure.iot.device` :

    ```bash
    pip install azure.iot.device
    ```

1. Sağlanması gereken Python betiğini indirmek için aşağıdaki komutu çalıştırın:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. `thermostat1`IoT Central uygulamanızda aşağı akış cihazını sağlamak için aşağıdaki komutları çalıştırın, `{your application id scope}` ve değiştirin `{your device primary key}` :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

IoT Central uygulamanızda, thermostat1 cihazının **cihaz durumunun** artık **sağlandığını** doğrulayın. 

## <a name="configure-a-downstream-device"></a>Bir aşağı akış cihazı yapılandırma

Önceki bölümde, `edgegateway` ağ geçidi olarak çalışmasını sağlamak için sanal makineyi tanıtım sertifikalarıyla yapılandırdınız. `leafdevice`Sanal makine, IoT Central bağlanmak için ağ geçidini kullanan bir termostat simülatörü yüklemeye hazırlanıyor.

`leafdevice`Sanal makine, sanal makinede oluşturduğunuz kök CA sertifikasının bir kopyasına ihtiyaç duyuyor `edgegateway` . */Home/AzureUser/certs/certs/Azure-iot-test-only.root.ca.cert.pem* dosyasını `edgegateway` sanal makineden sanal makinedeki Giriş dizininize kopyalayın `leafdevice` . Bir Linux sanal makinesine dosya kopyalamak için **SCP** komutunu kullanabilirsiniz.

Aşağı akış cihazından ağ geçidine bağlantıyı nasıl denetleyeceğinizi öğrenmek için bkz. [ağ geçidi bağlantısını test](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection)etme.

Sanal makinede termostat simülatörü çalıştırmak için `leafdevice` :

1. Python örneğini Giriş dizininize indirin:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Azure IoT cihaz Python modülünü yükler:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Örneği yapılandırmak için ortam değişkenlerini ayarlayın. ' Nin `{your device shared key}` birincil anahtarıyla değiştirin `thermostat1` . Bu değişkenler, ağ geçidi sanal makinesinin adı `edgegateway` ve termostat CIHAZıNıN kimliği olduğunu varsayar `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Bağlantı dizesinin, bir IoT Hub 'ının adı değil, ağ geçidi cihazının adını nasıl kullandığını fark edin.

1. Kodu çalıştırmak için aşağıdaki komutu kullanın:

    ```bash
    python3 simple_thermostat.py
    ```

    Bu komutun çıktısı şöyle görünür:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. IoT Central içinde Telemetriyi görmek için **thermostat1** cihazının **genel bakış** sayfasına gidin:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Aşağı akış cihazdan Telemetriyi gösteren ekran görüntüsü.":::

    **Hakkında** sayfasında, aşağı akış aygıtından gönderilen özellik değerlerini görüntüleyebilir ve **komut** sayfasında, aşağı akış cihazındaki komutları çağırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

IoT Central ile saydam bir ağ geçidinin nasıl yapılandırılacağını öğrendiğinize göre, önerilen sonraki adım [IoT Edge](../../iot-edge/about-iot-edge.md)hakkında daha fazla bilgi edineceksiniz.
