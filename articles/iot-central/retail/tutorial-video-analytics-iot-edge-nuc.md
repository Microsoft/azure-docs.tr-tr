---
title: Öğretici-Azure IoT Central 'da bir video analizi IoT Edge örneği oluşturma (Intel NUC)
description: Bu öğreticide, video analizi-nesne ve hareket algılama uygulama şablonuyla kullanılacak bir video analizi IoT Edge örneği oluşturma işleminin nasıl yapılacağı gösterilmektedir.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831935"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Öğretici: video analizi için IoT Edge örneği oluşturma (Intel NUC)

Azure IoT Edge, bulut zekasını dağıtarak ve çalıştırarak yerel olarak sunan tam olarak yönetilen bir hizmettir:

* Özel mantık
* Azure hizmetleri
* Yapay zeka

IoT Edge, bu hizmetler doğrudan platformlar arası IoT cihazlarında çalışır. böylece IoT çözümünüzü bulutta veya çevrimdışı ortamda güvenli bir şekilde çalıştırmanızı sağlar.

Bu öğreticide, IoT Edge çalışma zamanının bir Intel NUC cihazında nasıl yükleneceği ve yapılandırılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * IoT Edge güncelleştirin ve yapılandırın
> * IoT Edge ağ geçidini kurma
> * Yerel bir ONVıF-uyumlu kamerayı Intel NUC cihazınıza bağlama

## <a name="prerequisites"></a>Önkoşullar

* Başlamadan önce, Azure IoT Central (YOLO v3) veya [azure IoT Central (OpenVINO &trade; ) öğreticisinde bir video analizi oluşturmanız](tutorial-video-analytics-create-app-openvino.md)için önceki [bir canlı video analizi uygulaması oluşturun](./tutorial-video-analytics-create-app-yolo-v3.md) .
* Linux çalıştıran Intel NUC gibi bir cihaz, Docker kapsayıcılarını çalıştırabilecek ve video analizlerini çalıştırmak için yeterli işleme gücüne sahip.
* [IoT Edge çalışma zamanı cihazda yüklendi](../../iot-edge/how-to-install-iot-edge.md) ve çalışıyor.
* Windows makinenizden IoT Edge cihazına bağlanabiliyor, [Putty SSH istemcisine](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) veya eşdeğer bir yardımcı programa ihtiyacınız vardır.
* Ayrıca bir Azure aboneliğine de ihtiyacınız vardır. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)ücretsiz olarak bir tane oluşturabilirsiniz.

## <a name="configure-the-iot-edge-device"></a>IoT Edge cihazını yapılandırma

Intel NUC makinenizde yüklü IoT Edge çalışma zamanı yoksa, bkz. [Azure IoT Edge çalışma zamanını, detem tabanlı Linux sistemleri yönergelerine yükleme](../../iot-edge/how-to-install-iot-edge.md) .

IoT Edge çalışma zamanını güncelleştirmek için:

1. IoT Edge cihaza bağlanmak için PuTTY yardımcı programını kullanın.

1. IoT Edge çalışma zamanının sürümünü güncelleştirmek ve denetlemek için aşağıdaki komutları çalıştırın. Yazma sırasında sürüm 1.0.9 ' dir:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Dağıtımın gerekli izinlerle kullandığı klasörleri oluşturmak için aşağıdaki komutları kullanın:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Yapılandırma dosyası *state.js* IoT Edge cihazınızda */Data/Storage* klasörüne eklemek için:

1. Yerel makinenizde *LVA-yapılandırma* klasöründeki dosya *state.js* açmak için bir metin düzenleyicisi kullanın.

1. `system`Ve `iotCentral > properties` yer tutucuları, ağ geçidi cihazınızı tanımlayan dize değerleriyle güncelleştirin. Bu değerleri daha sonra IoT Central uygulama panosu ' nda görüntüleyebilirsiniz.

1. `iotCentral > appKeys`Yer tutucuları, önceki öğreticideki *scratchpad.txt* dosyasında bir Note yaptığınız değerlerle güncelleştirin. Değişiklikleri kaydedin.

1. `scp`Yeni düzenlediğiniz dosyadaki *state.js* IoT Edge cihazınızda */Data/Storage* klasörüne kopyalamak Için bir komut isteminde Putty yardımcı programını kullanın. Bu örnek `192.168.0.144` , örnek BIR IP adresi olarak kullanır, IoT Edge CIHAZıNıZıN IP adresiyle değiştirin:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

IoT Central uygulamanıza kaydolmak ve bağlanmak için IoT Edge yapılandırın:

1. IoT Edge cihaza bağlanmak için PuTTY yardımcı programını kullanın.

1. `nano`IoT Edge config. YAML dosyasını açmak için gibi bir metin düzenleyicisi kullanın.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML dosyaları girintileme için sekmeleri kullanamaz, bunun yerine iki boşluk kullanın. Üst düzey öğelerin başında boşluk bulunamaz.

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

1. `{scope_id}`Önceki öğreticideki *scratchpad.txt* dosyasında bir Note oluşturduğunuz **kimlik kapsamıyla** değiştirin.

1. `{registration_id}`Önceki öğreticide oluşturduğunuz cihaz *ağ geçidi-001* ile değiştirin.

1. `{symmetric_key}` **Ağ geçidi-001** cihazının **birincil anahtarıyla** değiştirin, önceki öğreticideki *scratchpad.txt* dosyasında bir değişiklik yaptınız.

1. IoT Edge Daemon 'u yeniden başlatmak için aşağıdaki komutu çalıştırın:

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge modüllerinin durumunu denetlemek için şu komutu çalıştırın:

    ```bash
    iotedge list
    ```

    Performans komutundan alınan çıktıda beş çalışan modül gösterilmektedir. Ayrıca, IoT Central uygulamanızda çalışan modüllerin durumunu görüntüleyebilirsiniz.

    > [!TIP]
    > Durumu denetlemek için bu komutu yeniden çalıştırabilirsiniz. Tüm modüllerin çalışmaya başlamasını beklemeniz gerekebilir.

IoT Edge modüller doğru başlatılmazsa, bkz. [IoT Edge cihazınızda sorun giderme](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Kameranızdan RTSP akışını toplayın

IoT Edge cihazınıza bağlı olan kameraların RTSP akış URL 'Lerini belirler, örneğin:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> IoT Edge bilgisayarda VLC gibi bir medya oynatıcı kullanarak kamera akışını görüntülemeyi deneyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı tamamladıysanız, oluşturduğunuz tüm kaynakları aşağıdaki şekilde kaldırabilirsiniz:

1. IoT Central uygulamasında, **Yönetim** bölümünde **uygulamanızın** sayfasına gidin. Ardından **Sil**’i seçin.
1. Azure portal, **LVA-RG** kaynak grubunu silin.
1. Yerel makinenizde **amp-Viewer** Docker kapsayıcısını durdurun.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Edge çalışma zamanını ve LVA modüllerini Intel NUC ağ geçidi cihazına dağıttık.

Kameraları yönetmek için sonraki öğreticiye uyun:

> [!div class="nextstepaction"]
> [Video analizlerini izleme ve yönetme-nesne ve hareket algılama uygulaması](./tutorial-video-analytics-manage.md)