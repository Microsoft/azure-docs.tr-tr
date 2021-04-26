---
title: Azure IoT Central yönelik verileri dönüştürme | Microsoft Docs
description: IoT cihazları, dönüştürmeniz gerekebilecek çeşitli biçimlerde veri gönderir. Bu makalede, verilerin hem IoT Central hem de giden şekilde nasıl değiştirileceği açıklanır. Açıklanan senaryolar IoT Edge ve Azure Işlevlerini kullanır.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6032300bd203db78e8cd147cf79300d6dcd9b1dc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751697"
---
# <a name="transform-data-for-iot-central"></a>IoT Central için veri dönüştürme

*Bu konu, çözüm oluşturucuları için geçerlidir.*

IoT cihazları, verileri çeşitli biçimlerde gönderir. Cihaz verilerini IoT Central uygulamanızla birlikte kullanmak için, şunlar için bir dönüşüm kullanmanız gerekebilir:

- IoT Central uygulamanızla uyumlu verilerin biçimini yapın.
- Birimleri dönüştürür.
- Yeni ölçümleri hesaplama.
- Diğer kaynaklardaki verileri zenginleştirin.

Bu makalede, giriş veya çıkış sırasında IoT Central dışında cihaz verilerini nasıl dönüştürebileceğinizi gösterir.

Aşağıdaki diyagramda dönüşümler içeren veriler için üç yol gösterilmektedir:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Veri dönüştürme yollarının Özeti hem giriş hem de çıkış" border="false":::

Aşağıdaki tabloda üç örnek dönüştürme türü gösterilmektedir:

| Dönüşüm | Açıklama | Örnek  | Notlar |
|------------------------|-------------|----------|-------|
| İleti biçimi         | JSON iletilerini dönüştürün veya değiştirin. | CSV 'den JSON 'a  | Giriş aşamasında. IoT Central yalnızca JSON iletilerini kabul eder. Daha fazla bilgi için bkz. [telemetri, özellik ve komut yükleri](concepts-telemetry-properties-commands.md). |
| Hesaplamaları           | [Azure işlevlerinin](../../azure-functions/index.yml) yürütebilmesi için matematik işlevleri. | Fahrenhayt 'den Santi$ 'a birim dönüştürme.  | IoT Central doğrudan bağlantı aracılığıyla ölçeklenebilir cihaz giriş özelliğinden yararlanmak için çıkış modelini kullanarak dönüştürme yapın. Verileri dönüştürmek, görselleştirme ve işler gibi IoT Central özellikleri kullanmanıza olanak sağlar. |
| İleti zenginleştirme     | Dış veri kaynaklarından zenginleştirme, cihaz özelliklerinde veya telemetride bulunmadı. İç zenginler hakkında daha fazla bilgi edinmek için bkz. [veri dışarı aktarma kullanarak IoT verilerini bulut hedeflerine dışa aktarma](howto-export-data.md) | Cihazlardan konum verileri kullanarak iletilere Hava durumu bilgilerini ekleyin. | IoT Central doğrudan bağlantı aracılığıyla ölçeklenebilir cihaz giriş özelliğinden yararlanmak için çıkış modelini kullanarak dönüştürme yapın. |

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Çözümü ayarlamak için bir IoT Central uygulamasına ihtiyacınız vardır. IoT Central uygulama oluşturmayı öğrenmek için bkz. [Azure IoT Central uygulaması oluşturma](quick-deploy-iot-central.md).

## <a name="data-transformation-at-ingress"></a>Giriş sırasında veri dönüştürme

Giriş sırasında cihaz verilerini dönüştürmek için iki seçenek vardır:

- **IoT Edge**: verileri IoT Central uygulamanıza göndermeden önce aşağı akış cihazlarından verileri dönüştürmek için bir IoT Edge modülü kullanın.

- **IoT Central cihaz Köprüsü**: [IoT Central cihaz Köprüsü](howto-build-iotc-device-bridge.md) , Sigfox, parçacık ve nesnelerin ağı gibi diğer ıot cihaz bulutlarını IoT Central 'e bağlar. Cihaz Köprüsü, verileri iletmek için bir Azure işlevi kullanır ve cihazı, cihaz verilerini dönüştürmek için özelleştirebilirsiniz.

### <a name="use-iot-edge-to-transform-device-data"></a>Cihaz verilerini dönüştürmek için IoT Edge kullanma

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="IoT Edge kullanarak giriş sırasında veri dönüştürme" border="false":::

Bu senaryoda, bir IoT Edge modülü verileri IoT Central uygulamanıza iletmeden önce aşağı akış cihazlarından dönüştürür. Yüksek düzeyde, bu senaryoyu yapılandırma adımları şunlardır:

1. **IoT Edge cihazı ayarlama**: bir IoT Edge cihazını ağ geçidi olarak yükleyip sağlayın ve ağ geçidini IoT Central uygulamanıza bağlayın.

1. **Aşağı akış cihazını IoT Edge cihaza bağlayın:** Aşağı akış cihazlarını IoT Edge cihaza bağlayın ve IoT Central uygulamanıza sağlayın.

1. **IoT Edge cihaz verilerini Dönüştür:** Verileri dönüştürmek için bir IoT Edge modülü oluşturun. Modül, dönüştürülmüş cihaz verilerini IoT Central uygulamanıza ileten IoT Edge ağ geçidi cihazına dağıtın.

1. **Doğrula**: bir aşağı akış cihazdan ağ geçidine veri gönderme ve dönüştürülen cihaz verilerinin IoT Central uygulamanıza ulaştığını doğrulama.

Aşağıdaki bölümlerde açıklanan örnekte, aşağı akış cihazı, IoT Edge ağ geçidi cihazına CSV verilerini aşağıdaki biçimde gönderir:

```csv
"<temperature >, <pressure>, <humidity>"
```

IoT Central gönderilmeden önce verileri aşağıdaki JSON biçimine dönüştürmek için IoT Edge modülünü kullanmak istiyorsunuz:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

Aşağıdaki adımlarda, bu senaryonun nasıl ayarlanacağı ve yapılandırılacağı gösterilmektedir:

### <a name="build-the-custom-module"></a>Özel modülü oluşturma

Bu senaryoda IoT Edge cihaz, verileri aşağı akış cihazından dönüştüren özel bir modül çalıştırır. IoT Edge cihazı dağıtmadan ve yapılandırmadan önce şunları yapmanız gerekir:

- Özel modülü oluşturun.
- Özel modülü bir kapsayıcı kayıt defterine ekleyin.

IoT Edge Runtime, Azure Container Registry veya Docker Hub gibi bir kapsayıcı kayıt defterinden özel modüller indirir. [Azure Cloud Shell](../../cloud-shell/overview.md) bir kapsayıcı kayıt defteri oluşturmak, modülü derlemek ve modülü kayıt defterine yüklemek için ihtiyacınız olan tüm araçlara sahiptir:

Bir kapsayıcı kayıt defteri oluşturmak için:

1. [Azure Cloud Shell](https://shell.azure.com/) açın ve Azure aboneliğinizde oturum açın.

1. Azure Container Registry oluşturmak için aşağıdaki komutları çalıştırın:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Ve değerlerini bir yere getirin `username` ve `password` bunları daha sonra kullanın.

[Azure Cloud Shell](https://shell.azure.com/)özel modülü oluşturmak için:

1. [Azure Cloud Shell](https://shell.azure.com/)uygun bir klasöre gidin.
1. Modül kaynak kodunu içeren GitHub deposunu kopyalamak için aşağıdaki komutu çalıştırın:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Özel modülü derlemek için Azure Cloud Shell aşağıdaki komutları çalıştırın:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Önceki komutların çalışması birkaç dakika sürebilir.

### <a name="set-up-an-iot-edge-device"></a>IoT Edge cihazı ayarlama

Bu senaryo, verileri herhangi bir aşağı akış cihazından dönüştürmek için bir IoT Edge ağ geçidi cihazı kullanır. Bu bölümde, IoT Central uygulamanızda ağ geçidi ve aşağı akış cihazları için IoT Central cihaz şablonlarının nasıl oluşturulacağı açıklanmaktadır. IoT Edge cihazlar, modüllerini yapılandırmak için bir dağıtım bildirimi kullanır.

Bu senaryo, aşağı akış cihazına yönelik bir cihaz şablonu oluşturmak için basit bir termostat cihaz modeli kullanır:

1. [Termostat cihazının cihaz modelini](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) yerel makinenize indirin.

1. IoT Central uygulamanızda oturum açın ve **cihaz şablonları** sayfasına gidin.

1. **+ Yeni**' yi seçin, **IoT cihazı**' nı seçin ve **İleri**' yi seçin.

1. Şablon adı olarak *termostat* girin ve Ileri ' **yi seçin: gözden geçir**. Ardından **Oluştur**’u seçin.

1. **Modeli Içeri aktar** ' ı seçin ve daha önce indirdiğiniz dosyaya *thermostat-2.js* alın.

1. Yeni cihaz şablonunu yayımlamak için **Yayımla** ' yı seçin.

IoT Edge ağ geçidi cihazı için bir cihaz şablonu oluşturmak için:

1. Dağıtım bildiriminin bir kopyasını yerel geliştirme makinenize kaydedin: [moduledeployment.json](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Bildirim dosyasında *moduledeployment.js* yerel kopyanızı bir metin düzenleyicisinde açın.

1. Bölümünü bulun `registryCredentials` ve yer tutucuları, Azure Container Registry 'yi oluştururken bir notun yaptığınız değerlerle değiştirin. `address`Değer gibi görünür `<username>.azurecr.io` .

1. `settings`İçin bölümünü bulun `transformmodule` . `<acr or docker repo>` `address` Önceki adımda kullandığınız değerle değiştirin. Değişiklikleri kaydedin.

1. IoT Central uygulamanızda **cihaz şablonları** sayfasına gidin.

1. **+ Yeni**' yi seçin, **Azure IoT Edge**' ı seçin ve ardından **İleri**' yi seçin.

1. Cihaz şablonu adı olarak *IoT Edge ağ geçidi cihazı* girin. **Bu bir ağ geçidi cihazından** seçim yapın. Daha önce düzenlediğiniz dağıtım bildirimi dosyasına *moduledeployment.js* yüklemek Için, **Gözden** geçirme ' yi seçin.

1. Dağıtım bildirimi doğrulandığında, **İleri: gözden geçir**' i seçin ve ardından **Oluştur**' u seçin.

1. **Model** altında **ilişkiler**' i seçin. **+ Ilişki Ekle**' yi seçin. Ekran adı olarak *aşağı akış cihazını* girin ve hedef olarak **termostat** ' yi seçin. **Kaydet**’i seçin.

1. Cihaz şablonunu yayımlamak için **Yayımla** ' yı seçin.

Artık IoT Central uygulamanızda iki cihaz şablonunuz vardır. **IoT Edge ağ geçidi cihaz** şablonu ve aşağı akış cihazı olarak **termostat** şablonu.

Bir ağ geçidi cihazını IoT Central kaydetmek için:

1. IoT Central uygulamanızda, **cihazlar** sayfasına gidin.

1. **IoT Edge ağ geçidi cihazı** ' nı seçin ve **cihaz oluştur**' u seçin. Cihaz adı olarak *IoT Edge ağ geçidi cihazı* girin, *ağ geçidi-01* ' i cihaz kimliği olarak girin, **IoT Edge ağ geçidi cihazının** cihaz şablonu olarak seçildiğinden emin olun. **Oluştur**’u seçin.

1. Cihaz listesinde **IoT Edge ağ geçidi cihazına** ve sonra **Bağlan**' ı seçin.

1. **IoT Edge ağ geçidi cihazı** için **kimlik KAPSAMıNı**, **cihaz kimliğini** ve **birincil anahtar** değerlerini bir yere unutmayın. Bunları daha sonra kullanırsınız.

Bir aşağı akış cihazını IoT Central kaydetmek için:

1. IoT Central uygulamanızda, **cihazlar** sayfasına gidin.

1. **Termostat** ' yi seçin ve **cihaz oluştur**' u seçin. Cihaz adı olarak *termostat* girin, cihaz kimliği olarak *aşağı akış-01* girin, cihaz şablonu olarak **termostat** 'nin seçildiğinden emin olun. **Oluştur**’u seçin.

1. Cihaz listesinde, **termostat** ' yi seçin ve ardından **ağ geçidine Ekle**' yi seçin. **IoT Edge ağ geçidi cihaz** şablonunu ve **IoT Edge ağ geçidi cihaz** örneğini seçin. **Ekle**' yi seçin.

1. Cihaz listesinde, **termostat**' ye tıklayın ve ardından **Bağlan**' ı seçin.

1. **Termostat** cihazının **kimlik KAPSAMıNı**, **cihaz kimliğini** ve **birincil anahtar** değerlerini bir yere göz önünde yapın. Bunları daha sonra kullanırsınız.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Ağ geçidini ve aşağı akış cihazlarını dağıtma

Kolaylık olması için bu makalede, ağ geçidini ve aşağı akış cihazlarını çalıştırmak üzere Azure sanal makineleri kullanılmaktadır. İki Azure sanal makinesini oluşturmak için aşağıdaki **Azure 'A dağıt** düğmesini seçin ve aşağıdaki tablodaki bilgileri kullanarak **özel dağıtım** formunu doldurun:

| Alan | Değer |
| ----- | ----- |
| Kaynak grubu | `ingress-scenario` |
| DNS etiketi ön eki ağ geçidi | Bu makine için benzersiz bir DNS adı, örneğin `<your name>edgegateway` |
| DNS etiketi ön eki aşağı akış | Bu makine için benzersiz bir DNS adı, örneğin `<your name>downstream` |
| Kapsam Kimliği | Daha önce bir nota yaptığınız KIMLIK kapsamı |
| Cihaz KIMLIĞI IoT Edge Ağ Geçidi | `gateway-01` |
| Cihaz anahtarı IoT Edge Ağ Geçidi | Daha önce bir nota yaptığınız birincil anahtar değeri |
| Kimlik Doğrulama Türü | Parola |
| Yönetici parolası veya anahtarı | Her iki sanal makinede **AzureUser** hesabı için parola seçiminiz. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

**Gözden geçir + oluştur**' u ve ardından **Oluştur**' u seçin. Giriş **senaryosu** kaynak grubundaki sanal makinelerin oluşturulması birkaç dakika sürer.

IoT Edge cihazının düzgün çalışıp çalışmadığını denetlemek için:

1. IoT Central uygulamanızı açın. Ardından **cihazlar** sayfasındaki cihaz listesinden **IoT Edge ağ geçidi cihazına** gidin.

1. **Modüller** sekmesini seçin ve üç modülün durumunu denetleyin. IoT Edge çalışma zamanının sanal makinede başlatılması birkaç dakika sürer. Başlatıldığında, üç modülün durumu **çalışıyor** demektir. IoT Edge çalışma zamanı başlamazsa, bkz. [IoT Edge cihazınızda sorun giderme](../../iot-edge/troubleshoot.md).

IoT Edge cihazınızın bir ağ geçidi olarak çalışması için, bazı sertifikalara, kimliğini herhangi bir aşağı akış cihazına kanıtlamaları gerekir. Bu makalede tanıtım sertifikaları kullanılmaktadır. Bir üretim ortamında, sertifika yetkilinizdeki sertifikaları kullanın.

Tanıtım sertifikalarını oluşturmak ve ağ geçidi cihazınıza yüklemek için:

1. Ağ geçidi cihazı sanal makinenizde bağlantı kurmak ve oturum açmak için SSH kullanın. Bu sanal makine için DNS adını Azure portal bulabilirsiniz. Giriş **-senaryo** kaynak grubundaki **edgegateway** sanal makinesine gidin.

    > [!TIP]
    > Yerel makinenizden veya Azure Cloud Shell bağlanmak üzere SSH kullanabilmeniz için, hem sanal makinelerinizde hem de SSH erişimi için 22 numaralı bağlantı noktasını açmanız gerekebilir.

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

IoT Edge çalışma zamanı, değişiklikleriniz sonrasında başarıyla başlarsa, **$edgeAgent** ve **$EdgeHub** modüllerinin durumu **çalışıyor** olarak değişir. Bu durum değerlerini, IoT Central ağ geçidi cihazınızın **modüller** sayfasında görebilirsiniz.

Çalışma zamanı başlamazsa, *config. YAML* 'de yaptığınız değişiklikleri denetleyin ve [IoT Edge cihazınızda sorun giderme](../../iot-edge/troubleshoot.md)bölümüne bakın.

### <a name="connect-downstream-device-to-iot-edge-device"></a>Aşağı akış cihazını IoT Edge cihaza bağlama

Bir aşağı akış cihazını IoT Edge ağ geçidi cihazına bağlamak için:

1. Şirket yönündeki cihaz sanal makinenizde bağlantı kurmak ve oturum açmak için SSH kullanın. Bu sanal makine için DNS adını Azure portal bulabilirsiniz. Giriş **-senaryo** kaynak grubundaki **leafdevice** sanal makinesine gidin.

    > [!TIP]
    > Yerel makinenizden veya Azure Cloud Shell bağlanmak üzere SSH kullanabilmeniz için, hem sanal makinelerinizde hem de SSH erişimi için 22 numaralı bağlantı noktasını açmanız gerekebilir.

1. Örnek aşağı akış cihazının kaynak koduyla GitHub deposunu kopyalamak için aşağıdaki komutu çalıştırın:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Gerekli sertifikayı ağ geçidi cihazından kopyalamak için aşağıdaki `scp` komutları çalıştırın. Bu `scp` komut, `edgegateway` ağ geçidi sanal makinesini tanımlamak için konak adını kullanır. Parolanız istenir:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. *Leafdevice* klasörüne gidin ve gerekli paketleri yükler. Ardından, `build` `start` cihazı sağlamak ve ağ geçidine bağlamak için ve betiklerini çalıştırın:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Daha önce oluşturduğunuz aşağı akış cihazının cihaz KIMLIĞINI, kapsam KIMLIĞINI ve SAS anahtarını girin. Ana bilgisayar adı için girin `edgegateway` . Komutun çıktısı şöyle görünür:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Doğrulama

Senaryonun çalıştığını doğrulamak için, IoT Central içindeki **IoT Edge ağ geçidi cihazınıza** gidin:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Cihazlar sayfasında dönüştürülen verileri gösteren ekran görüntüsü.":::

- **Modüller**' i seçin. **$EdgeHub** ve **transformmodule** **$edgeAgent** üç IoT Edge modülünün çalıştığını doğrulayın.
- **Aşağı akış cihazlarını** seçin ve aşağı akış cihazının sağlandığını doğrulayın.
- **Ham verileri** seçin. **Modellenmemiş veriler** sütunundaki telemetri verileri şöyle görünür:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

IoT Edge cihaz verileri aşağı akış cihazından dönüştürtiğinden telemetri, IoT Central ağ geçidi aygıtıyla ilişkilendirilir. Telemetriyi görselleştirmek için telemetri türleri tanımlarıyla birlikte **IoT Edge Gateway cihaz** şablonunun yeni bir sürümünü oluşturun.

## <a name="data-transformation-at-egress"></a>Çıkış sırasında veri dönüştürme

Cihazlarınızı IoT Central, cihaz verilerini bir işlem altyapısına dışarı aktarabilir ve ardından cihaz yönetimi ve analiz için IoT Central geri gönderebilirsiniz. Örnek:

- Cihazlarınız IoT Central konuma veri gönderiyor.
- IoT Central verileri, hava durumu bilgileriyle konum verilerini geliştiren bir işlem altyapısına dışarı aktarır.
- İşlem altyapısı, gelişmiş verileri IoT Central 'e geri gönderir.

IoT Central ' dan içe aktarılmış verileri dönüştürmek için [IoT Central cihaz köprüsünü](https://github.com/Azure/iotc-device-bridge) işlem altyapısı olarak kullanabilirsiniz.

Verileri çıkış sırasında dönüştürmenin bir avantajı, cihazlarınızın doğrudan IoT Central bağlanmasına, bu da cihazların cihazlara veya cihaz özelliklerini güncelleştirmesine olanak sağlar. Ancak, bu yöntemle, aylık işlem biriminden daha fazla ileti kullanabilir ve Azure IoT Central kullanma maliyetini artırabilirsiniz.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Cihaz verilerini dönüştürmek için IoT Central cihaz köprüsünü kullanma

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="IoT Edge kullanarak çıkış üzerinde veri dönüştürme" border="false":::

Bu senaryoda, bir işlem altyapısı, IoT Central uygulamanıza geri göndermeden önce IoT Central dışarıya aktarılmış cihaz verilerini dönüştürür. Yüksek düzeyde, bu senaryoyu yapılandırma adımları şunlardır:

1. **İşlem altyapısını ayarlayın:** Veri dönüştürmesi için bir işlem altyapısı görevi görecek bir IoT Central cihaz Köprüsü oluşturun.

1. Cihaz **köprüsünde cihaz verilerini dönüştürme:** Veri dönüştürme kullanım durumu için cihaz Köprüsü işlev kodunu değiştirerek cihaz köprüsünde verileri dönüştürün.

1. **IoT Central veri akışını cihaz köprüsüne etkinleştirin:** Dönüştürme için IoT Central verileri cihaz köprüsüne dışarı aktarın. Ardından, dönüştürülen verileri IoT Central 'e geri iletin. Verilerin dışarı aktarılmasını oluştururken, yalnızca dönüştürülmemiş verileri dışarı aktarmak için ileti özelliği filtrelerini kullanın.

1. **Doğrulayın**: cihazınızı IoT Central uygulamasına bağlama ve hem ham cihaz verilerini hem de IoT Central dönüştürülmüş verileri kontrol edin.

<!-- To Do - doesn't the device send JSON data? -->
Aşağıdaki bölümlerde açıklanan örnekte, cihaz IoT Edge ağ geçidi cihazına CSV verilerini aşağıdaki biçimde gönderir:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Cihaz verilerini şu şekilde dönüştürmek için cihaz köprüsünü kullanın:

- Santigrad olan sıcaklık birimini Fahrenhayt olarak değiştirme.
- Latitude ve boylam değerleri için [Açık Hava durumu](https://openweathermap.org/) hizmetinden alınan hava durumu verileriyle cihaz verilerini zenginleştirir.

Ardından, cihaz Köprüsü dönüştürülen verileri aşağıdaki biçimde IoT Central gönderir:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Aşağıdaki adımlarda, bu senaryonun nasıl ayarlanacağı ve yapılandırılacağı gösterilmektedir:

### <a name="retrieve-your-iot-central-connection-settings"></a>IoT Central bağlantı ayarlarınızı alma

Bu senaryoyu ayarlamadan önce IoT Central uygulamanızdan bazı bağlantı ayarlarını almanız gerekir:

1. IoT Central uygulamanızda oturum açın.

1. **Yönetim > cihaz bağlantısı**' na gidin.

1. **Kimlik kapsamını** bir yere getirin. Bu değeri daha sonra kullanırsınız.

1. **SAS-IoT-cihazlar** kayıt grubunu seçin. Paylaşılan erişim imzası birincil anahtarını bir yere getirin. Bu değeri daha sonra kullanırsınız.

### <a name="set-up-a-compute-engine"></a>İşlem altyapısı ayarlama

Bu senaryo IoT Central cihaz Köprüsü ile aynı Azure Işlevleri dağıtımını kullanır. Cihaz köprüsünü dağıtmak için, aşağıdaki **Azure 'A dağıt** düğmesini seçin ve aşağıdaki tablodaki bilgileri kullanarak **özel dağıtım** formunu doldurun:

| Alan | Değer |
| ----- | ----- |
| Kaynak grubu | Adlı yeni bir kaynak grubu oluşturun `egress-scenario` |
| Region | Size en yakın bölgeyi seçin. |
| Kapsam Kimliği | Daha önce bir nota yaptığınız **kimlik kapsamını** kullanın. |
| IoT Central SAS anahtarı | **SAS-IoT-cihazlar** kayıt grubu için paylaşılan erişim imzası birincil anahtarını kullanın. Daha önce bu değere bir göz yaptınız. |

[ ![ Azure 'a dağıtın](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

**Gözden geçir + oluştur**' u ve ardından **Oluştur**' u seçin. **Çıkış senaryosu** kaynak grubunda Azure işlevinin ve ilgili kaynakların oluşturulması birkaç dakika sürer.

### <a name="transform-device-data-in-the-device-bridge"></a>Cihaz köprüsünde cihaz verilerini dönüştürme

Cihaz köprüsünü, aktarılmış cihaz verilerini dönüştürecek şekilde yapılandırmak için:

1. Açık Hava durumu hizmetinden bir uygulama API 'SI anahtarı edinin. Bir hesap, hizmetin sınırlı kullanımı ile ücretsizdir. Bir uygulama API anahtarı oluşturmak için, [Açık Hava durumu hizmeti portalında](https://openweathermap.org/) bir hesap oluşturun ve yönergeleri izleyin. Açık Hava durumu API anahtarınızı daha sonra kullanırsınız.

1. Azure portal, **Çıkış senaryosu** kaynak grubundaki işlev uygulaması ' a gidin.

1. Sol gezinti bölmesinde, **geliştirme araçları**' nda **App Service Düzenleyicisi (Önizleme)** öğesini seçin.

1. **App Service Düzenleyicisi** sayfasını açmak için **Git &rarr;** ' i seçin. Aşağıdaki değişiklikleri yapın:

    1. *Wwwroot/ıotcıntegration/index.js* dosyasını açın. Bu dosyadaki tüm kodu [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js)kodla değiştirin.

    1. Yeni *index.js*, `openWeatherAppId` daha önce edindiğiniz açık hava durumu API anahtarı ile değişken dosyasını güncelleştirin.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. IoT Central için işlev tarafından gönderilen verilere bir ileti özelliği ekleyin. IoT Central, dönüştürülen verilerin verilmesini engellemek için bu özelliği kullanır. Bu değişikliği yapmak için *Wwwroot/ıotcıntegration/lib/engine.js* dosyasını açın. Şu kodu bulun:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Önceki kod parçacığında koddan hemen sonra aşağıdaki kodu ekleyin:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Başvuru için [engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) dosyanın tamamlanan bir örneğini görüntüleyebilirsiniz.

1. **App Service Düzenleyicisi**, sol gezinti bölmesinde **konsol** ' ı seçin. Gerekli paketleri yüklemek için aşağıdaki komutları çalıştırın:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Bu komutun çalıştırılması birkaç dakika sürebilir.

1. **Azure Işlevine genel bakış** sayfasına dönün ve işlevi yeniden başlatın:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="İşlevi yeniden başlatın":::

1. Sol gezinti bölmesinde **işlevler** ' i seçin. Sonra **Iotcıntegration**' ı seçin. **Kod + test**' i seçin.

1. İşlev URL 'sini bir yere getirin, daha sonra bu değere ihtiyacınız vardır:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="İşlev URL 'sini al":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>IoT Central veri akışını cihaz köprüsüne etkinleştirme

Bu bölümde, Azure IoT Central uygulamasının nasıl ayarlanacağı açıklanmaktadır.

İlk olarak, [cihaz modeli](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) dosyasını yerel makinenize kaydedin.

IoT Central uygulamanıza bir cihaz şablonu eklemek için IoT Central uygulamanıza gidin ve şunları yapın:

1. IoT Central uygulamanızda oturum açın ve **cihaz şablonları** sayfasına gidin.

1. **+ Yeni**' yi seçin **, IoT cihazı**' nı seçin, ileri ' yi seçin **: Özelleştir**, *işlem modelini* şablon adı olarak girin. Şunu seçin: **İleri: Gözden Geçir**. Ardından **Oluştur**’u seçin.

1. **Modeli Içeri aktar** ' ı seçin ve daha önce indirdiğiniz dosyada *model.js* gidin.

1. Model alındıktan sonra, **işlem modeli** cihaz şablonunu yayımlamak için **Yayımla** ' yı seçin.

Verileri cihaz köprüüze göndermek üzere veri dışa aktarmayı ayarlamak için:

1. IoT Central uygulamanızda **veri dışarı aktarma**' yı seçin.

1. Cihaz köprüsüyle kullanılacak bir hedef oluşturmak için **+ yeni hedef** ' i seçin. Hedef *işlem işlevini* çağırın, **hedef türü** için **Web kancasını** seçin. Geri çağırma URL 'si için, işlev URL 'sinde daha önce bir değişiklik yaptığınız bir yere Yapıştır ' ı seçin. **Yetkilendirmeyi** **kimlik doğrulama yok** olarak bırakın.

1. Değişiklikleri kaydedin.

1. **+ Yeni dışarı aktar** ' ı seçin ve *işlem verme* adlı bir veri dışa aktarma işlemi oluşturun.

1. Yalnızca kullandığınız cihaz şablonuna ait cihaz verilerini dışarı aktarmak için bir filtre ekleyin. **+ Filtre**' yi seçin, **öğe cihaz şablonu**' nu seçin, işleç **eşittir**' i seçin ve yeni oluşturduğunuz **işlem modeli** cihaz şablonunu seçin.

1. Dönüştürülen ve dönüştürülbırakılmamış verileri birbirinden ayırt etmek için bir ileti filtresi ekleyin. Bu filtre, dönüştürülmüş değerlerin cihaz köprüsüne geri gönderilmesini engeller. **+ Message Özellik filtresi** ' ni seçin ve *hesaplanan* ad değerini girip işleç **yok**' u seçin. Dize, `computed` aygıt Köprüsü örnek kodunda bir anahtar sözcük olarak kullanılır.

1. Hedef için, daha önce oluşturduğunuz **işlem işlevi** hedefini seçin.

1. Değişiklikleri kaydedin. Bir dakika sonra, **dışa aktarma durumu** **sağlıklı** olarak gösterilir.

### <a name="verify"></a>Doğrulama

Senaryoyu test etmek için kullandığınız örnek cihaz Node.js yazılır. Yerel makinenizde Node.js ve NPM 'nin yüklü olduğundan emin olun. Bu önkoşulları yüklemek istemiyorsanız, bunları önceden yüklenmiş[Azure Cloud Shell](https://shell.azure.com/) kullanın.

Senaryoyu test eden örnek bir cihaz çalıştırmak için:

1. Örnek kodu içeren GitHub deposunu kopyalayın, aşağıdaki komutu çalıştırın:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Örnek cihazı IoT Central uygulamanıza bağlamak için *IoT-merkezi-işlem/cihaz/device.js* dosyasındaki bağlantı ayarlarını düzenleyin. Kapsam KIMLIĞI ve grup SAS anahtarını, daha önce bir notunuz yaptığınız değerlerle değiştirin:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Değişiklikleri kaydedin.

1. Gerekli paketleri yüklemek ve cihazı çalıştırmak için aşağıdaki komutları kullanın:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Bu komutun sonucu aşağıdaki çıktı gibi görünür:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. IoT Central uygulamanızda, **computeDevice** adlı cihaza gidin. **Ham veri** görünümünde, her beş saniyede bir görüntülenen iki farklı telemetri akışı vardır. Modellenmemiş verileri olan akış özgün telemetri, modellenen veri içeren akış ise işlevin dönüştürüledikleri veri olur:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Orijinal ve dönüştürülmüş ham verileri gösteren ekran görüntüsü.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu kılavuzdaki adımları izleyerek oluşturduğunuz Azure kaynaklarına artık ihtiyacınız yoksa [Azure Portal kaynak gruplarını](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups)silin.

Bu **kılavuzda kullandığınız iki** kaynak grubu giriş senaryosu ve **Çıkış senaryosudur**.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, hem giriş hem de çıkış aşamasında IoT Central cihaz verilerini dönüştürmek için farklı seçenekler hakkında bilgi edindiniz. Bu makalede, iki özel senaryo için izlenecek yollar yer almaktadır:

- Veriler IoT Central uygulamanıza gönderilmeden önce aşağı akış cihazlarındaki verileri dönüştürmek için bir IoT Edge modülü kullanın.
- IoT Central dışındaki verileri dönüştürmek için Azure Işlevlerini kullanın. Bu senaryoda, IoT Central dönüştürülecek bir Azure işlevine gelen verileri göndermek için bir veri dışarı aktarma işlemi kullanır. İşlevi dönüştürülen verileri IoT Central uygulamanıza geri gönderir.

Azure IoT Central uygulamanızın dışında cihaz verilerini nasıl dönüştürebileceğinizi öğrendiğinize göre, [IoT Central cihaz verilerini çözümlemek için](howto-create-analytics.md)analizler kullanmayı öğrenebilirsiniz.
