---
title: Öğretici-Azure IoT Central Azure IoT Edge bir cihaz ekleme | Microsoft Docs
description: Öğretici-bir operatör olarak Azure IoT Central uygulamanıza Azure IoT Edge bir cihaz ekleyin
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724889"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Öğretici: Azure IoT Central uygulamanıza Azure IoT Edge bir cihaz ekleme

*Bu makale işleçler, çözüm oluşturucular ve cihaz geliştiricileri için geçerlidir.*

Bu öğreticide, Azure IoT Central uygulamanıza bir Azure IoT Edge cihazının nasıl yapılandırılacağı ve ekleneceği gösterilmektedir. Öğretici, bir IoT Edge cihazının benzetimini yapmak için IoT Edge özellikli bir Linux sanal makinesi (VM) kullanır. IoT Edge cihaz, sanal çevresel telemetri üreten bir modül kullanır. Telemetri IoT Central uygulamanızdaki bir panoda görüntülenir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * IoT Edge cihaz için cihaz şablonu oluşturma
> * IoT Central IoT Edge cihaz oluşturma
> * Bir Linux sanal makinesine sanal bir IoT Edge cihazı dağıtma

## <a name="prerequisites"></a>Önkoşullar

Özel uygulama **> özel uygulama** şablonunu kullanarak bir IoT Central uygulaması oluşturmak için [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md) hızlı başlangıcı ' nı doldurun.

Bu öğreticideki adımları tamamlayabilmeniz için etkin bir Azure aboneliğine ihtiyacınız vardır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

IoT Edge bildirim dosyasını GitHub 'dan indirin. Aşağıdaki bağlantıya sağ tıklayın ve sonra **Bağlantıyı farklı kaydet**' i seçin: [EnvironmentalSensorManifest.js](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Cihaz şablonu oluştur

Bu bölümde, bir IoT Edge cihaz için IoT Central cihaz şablonu oluşturacaksınız. Başlamak için bir IoT Edge bildirimi içeri aktarır ve sonra telemetri tanımları ve görünümleri eklemek için şablonu değiştirirsiniz:

### <a name="import-manifest-to-create-template"></a>Şablon oluşturmak için bildirimi içeri aktar

IoT Edge bildiriminden bir cihaz şablonu oluşturmak için:

1. IoT Central uygulamanızda **cihaz şablonları** ' na gidin ve **+ Yeni**' yi seçin.

1. **Şablon türü seç** sayfasında **Azure IoT Edge** kutucuğunu seçin. Ardından **İleri: Özelleştir**' i seçin.

1. **Azure IoT Edge dağıtım bildirimini karşıya yükle** sayfasında, cihaz şablonu adı olarak *çevresel algılayıcı Edge cihazını* girin. Ardından, daha önce indirdiğinizEnvironmentalSensorManifest.jskarşıya yüklemek için, **Gözden** geçirme **'** yi seçin. Sonra Ileri ' yi seçin **: İnceleme**.

1. **İnceleme** sayfasında **Oluştur**' u seçin.

1. Bildirimde tanımlanan iki özelliği görüntülemek için **SimulatedTemperatureSensor** modülündeki **Yönet** arabirimini seçin:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="IoT Edge bildiriminden oluşturulan cihaz şablonu":::

> [!TIP]
> Bu dağıtım bildirimi, bağlantı için herhangi bir kimlik bilgisi gerektirmeyen Azure Container Registry deposundan modül görüntülerini çeker. Modül görüntülerini özel bir depodan kullanmak istiyorsanız, bildirimde kapsayıcı kayıt defteri kimlik bilgilerini ayarlayın.

### <a name="add-telemetry-to-manifest"></a>Bildirime telemetri Ekle

IoT Edge bildirimi, bir modülün gönderdiği Telemetriyi tanımlamaz. Telemetri tanımlarını IoT Central cihaz şablonuna eklersiniz. **SimulatedTemperatureSensor** modülü aşağıdaki JSON gibi görünen telemetri iletilerini gönderir:

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

Telemetri tanımlarını cihaz şablonuna eklemek için:

1. **Çevresel algılayıcı Edge cihaz** şablonunda **Yönet** arabirimini seçin.

1. **+ Özellik Ekle**' yi seçin. **Görüntü adı** olarak *makine* girin ve **yetenek türünün** **telemetri** olduğundan emin olun.

1. Şema türü olarak **nesne** ' yi seçin ve ardından **Tanımla**' yı seçin. Nesne tanımı sayfasında, **Double** türünden öznitelik olarak *sıcaklık* ve *basınç* ekleyin ve ardından **Uygula**' yı seçin.

1. **+ Özellik Ekle**' yi seçin. **Görüntü adı** olarak *ortam* girin ve **yetenek türünün** **telemetri** olduğundan emin olun.

1. Şema türü olarak **nesne** ' yi seçin ve ardından **Tanımla**' yı seçin. Nesne tanımı sayfasında, **Double** türü öznitelikleri olarak *sıcaklık* ve *nem* ekleyin ve ardından **Uygula**' yı seçin.

1. **+ Özellik Ekle**' yi seçin. **Görünen ad** olarak *TimeCreated* yazın ve **yetenek türünün** **telemetri** olduğundan emin olun.

1. Şema türü olarak **DateTime** öğesini seçin.

1. Şablonu güncelleştirmek için **Kaydet** ' i seçin.

**Manage** Interface artık **Machine**, **Ambient** ve **TimeCreated** telemetri türlerini içerir:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Makine ve ortam telemetri türleri ile arabirim":::

### <a name="add-views-to-template"></a>Şablona görünümler ekleme

Cihaz şablonunda, bir işlecin IoT Edge cihazdan Telemetriyi görmesini sağlayan bir görünüm yoktur. Cihaz şablonuna bir görünüm eklemek için:

1. **Ortam algılayıcı uç cihaz** şablonunda **Görünümler** ' i seçin.

1. **Yeni bir görünüm eklemek Için seçin** sayfasında, cihaz kutucuğunu **görselleştirmeyi** seçin.

1. Görünüm adını *IoT Edge cihaz telemetrisini görüntülemek* için değiştirin.

1. **Çevresel** ve **makine** telemetri türlerini seçin. Ardından **kutucuk Ekle**' yi seçin.

1. **Görünüm IoT Edge cihaz telemetrisi** görünümünü kaydetmek için **Kaydet** ' i seçin.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Telemetri görünümü ile cihaz şablonu":::

### <a name="publish-the-template"></a>Şablonu yayımlama

**Ortam algılayıcı Edge cihaz** şablonunu kullanan bir cihaz ekleyebilmeniz için önce şablonu yayımlamanız gerekir.

**Ortam algılayıcı Edge cihaz** şablonuna gidin ve **Yayımla**' yı seçin. **Bu cihaz şablonunu uygulama panelinde Yayımla** ' ya tıklayın, şablonu yayımlamak için **Yayımla** ' yı seçin:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Cihaz şablonunu yayımlama":::

## <a name="add-iot-edge-device"></a>IoT Edge cihaz ekle

Artık **çevresel algılayıcı Edge cihaz** şablonunu yayımladınız, IoT Central uygulamanıza bir cihaz ekleyebilirsiniz:

1. IoT Central uygulamanızda, **cihazlar** sayfasına gidin ve kullanılabilir şablonlar listesinden **çevresel algılayıcı kenar cihazı** ' nı seçin.

1. Şablondan yeni bir cihaz eklemek için **+ Yeni** ' yi seçin. **Yeni cihaz oluştur** sayfasında **Oluştur**' u seçin.

Artık **kayıtlı** durumda olan yeni bir cihazınız var:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Yeni, kayıtlı cihaz":::

### <a name="get-the-device-credentials"></a>Cihaz kimlik bilgilerini al

IoT Edge cihazını Bu öğreticide daha sonra dağıtırken, cihazın IoT Central uygulamanıza bağlanmasına izin veren kimlik bilgileri gerekir. Cihaz kimlik bilgilerini al:

1. **Cihaz** sayfasında, oluşturduğunuz aygıtı seçin.

1. **Bağlan**’ı seçin.

1. **Cihaz bağlantısı** sayfasında, **kimlik KAPSAMı**, **cihaz kimliği** ve **birincil anahtarı** bir yere unutmayın. Bu değerleri daha sonra kullanırsınız.

1. **Kapat**’ı seçin.

Artık, bir IoT Edge cihazının bağlanmasını sağlamak için IoT Central uygulamanızı yapılandırmayı tamamladınız.

## <a name="deploy-an-iot-edge-device"></a>IoT Edge cihazı dağıtma

Bu öğreticide, bir IoT Edge cihazının benzetimini yapmak için Azure 'da oluşturulan Azure IoT Edge özellikli bir Linux VM kullanırsınız. Azure aboneliğinizde IoT Edge özellikli VM oluşturmak için şunu tıklatın:

[![iotedge-vm-deploy için Azure Düğmesini dağıtma](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

**Özel dağıtım** sayfasında:

1. Azure aboneliğinizi seçin.

1. *Orta kenar-RG* adlı yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin.

1. Size yakın bir bölge seçin.

1. *Contoso-orta-uç* gibi benzersiz bir **DNS etiketi öneki** ekleyin.

1. Sanal makine için bir Yönetici Kullanıcı adı seçin.

1. Bağlantı dizesi olarak *Temp* girin. Daha sonra, cihazı DPS kullanarak bağlanacak şekilde yapılandırırsınız.

1. VM boyutu, Ubuntu sürümü ve konum için varsayılan değerleri kabul edin.

1. Kimlik doğrulama türü olarak **parola** ' yı seçin.

1. VM için bir parola girin.

1. Ardından **gözden geçir + oluştur**' u seçin.

1. Seçimlerinizi gözden geçirin ve ardından **Oluştur**' u seçin:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="IoT Edge VM oluşturma":::

Dağıtımın tamamlanabilmesi birkaç dakika sürer. Dağıtım tamamlandığında, Azure portal **Merkezi-Edge-RG** kaynak grubuna gidin.

### <a name="configure-the-iot-edge-vm"></a>IoT Edge VM 'yi yapılandırma

Sanal makinede IoT Edge yapılandırmak için, IoT Central uygulamanıza kaydolmak ve bağlanmak üzere DPS 'yi kullanın:

1. **Contoso-Edge-RG** kaynak grubunda, sanal makine örneğini seçin.

1. **Destek + sorun giderme** bölümünde **seri konsol**' yi seçin. Önyükleme tanılamayı yapılandırmanız istenirse, portaldaki yönergeleri izleyin.

1. İstemi görmek için **ENTER** tuşuna basın `login:` . Oturum açmak için Kullanıcı adınızı ve parolanızı girin.

1. IoT Edge çalışma zamanı sürümünü denetlemek için aşağıdaki komutu çalıştırın. Yazma sırasında sürüm 1.0.9.1 ' dir:

    ```bash
    sudo iotedge --version
    ```

1. `nano`IoT Edge config. YAML dosyasını açmak için düzenleyiciyi kullanın:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Görene kadar aşağı kaydırın `# Manual provisioning configuration` . Aşağıdaki kod parçacığında gösterildiği gibi sonraki üç satırı Açıklama:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Görene kadar aşağı kaydırın `# DPS symmetric key provisioning configuration` . Aşağıdaki kod parçacığında gösterildiği gibi sonraki sekiz satırın açıklamasını kaldırın:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > Önünde boşluk olmadığından emin olun `provisioning:`

1. `{scope_id}`Daha önce bir notunuz yaptığınız **kimlik kapsamıyla** değiştirin.

1. `{registration_id}`Daha önce bir nota yaptığınız **cihaz kimliğiyle** değiştirin.

1. `{symmetric_key}`Daha önce bir notunuz yaptığınız **birincil anahtarla** değiştirin.

1. Değişiklikleri kaydedin (**CTRL-O**) ve Exit (**CTRL-X**) `nano` .

1. IoT Edge Daemon 'u yeniden başlatmak için aşağıdaki komutu çalıştırın:

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge modüllerinin durumunu denetlemek için şu komutu çalıştırın:

    ```bash
    iotedge list
    ```

    Aşağıdaki örnek çıktıda çalışan modüller gösterilmektedir:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Tüm modüllerin çalışmaya başlamasını beklemeniz gerekebilir.

## <a name="view-the-telemetry"></a>Telemetri verilerini görüntüleme

Sanal IoT Edge cihaz artık VM 'de çalışıyor. IoT Central uygulamanızda cihaz durumu artık **cihazlar** sayfasında **sağlanıyor** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="IoT Edge cihaz sağlandı":::

Cihaz Telemetriyi **görüntüleme IoT Edge cihaz telemetrisi** sayfasında görebilirsiniz:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Cihaz telemetrisi":::

**Modüller** sayfası, cihazdaki IoT Edge modüllerinin durumunu gösterir:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Cihaz modülü durumu":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

IoT Edge VM ile çalışmaya devam etmeyi planlıyorsanız, bu öğreticide kullandığınız kaynakları tutabilir ve yeniden kullanabilirsiniz. Aksi takdirde, ek ücretleri önlemek için bu öğreticide oluşturduğunuz kaynakları silebilirsiniz:

* IoT Edge sanal makinesini ve ilişkili kaynaklarını silmek için Azure portal **contoso-Edge-RG** kaynak grubunu silin.
* IoT Central uygulamasını silmek için uygulamanın **Yönetim** bölümünde **uygulama** sayfanıza gidin ve **Sil**' i seçin.

Bir çözüm geliştiricisi veya işletmeni olarak, artık IoT Central IoT Edge cihazları ile nasıl çalışabileceğinizi öğrendiğinize göre, önerilen bir sonraki adım şunları yapmanız gerekir:

> [!div class="nextstepaction"]
> [Cihaz telemetrisini analiz etmek için cihaz gruplarını kullanma](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisi olarak, artık IoT Central IoT Edge cihazlarıyla nasıl çalışabileceğinizi öğrendiğinize göre, önerilen bir sonraki adım okunmalıdır:

> [!div class="nextstepaction"]
> [IoT Edge modülleri geliştirme](../../iot-edge/tutorial-develop-for-linux.md)