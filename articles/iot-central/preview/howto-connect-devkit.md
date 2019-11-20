---
title: DevKit cihazını Azure IoT Central uygulamanıza bağlama | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, IoT Tak ve Kullan kullanarak bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlamayı öğrenin.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 178f518ed7ab24fc0d3678f77ae75933a7163b58
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930140"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application-preview-features"></a>Bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlama (Önizleme özellikleri)

Bu makalede bir Mxyonga IoT DevKit (DevKit) cihazının bir Azure IoT Central uygulamasına nasıl bağlanacağı gösterilmektedir. Cihaz, IoT Central bağlantısını yapılandırmak için DevKit cihazı için sertifikalı IoT Tak ve Kullan modelini kullanır.

Bu nasıl yapılır makalesinde şunları yapabilirsiniz:

- IoT Central uygulamanızdan bağlantı ayrıntılarını alın.
- Cihazı hazırlayın ve IoT Central uygulamanıza bağlayın.
- IoT Central cihaz telemetri ve özelliklerini görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

- Bir [devkit cihazı](https://aka.ms/iot-devkit-purchase).
- **Önizleme uygulaması** şablonundan oluşturulan bir IoT Central uygulaması. [Iot Tak ve kullan uygulaması oluşturma](./quick-deploy-iot-central.md)bölümündeki adımları izleyebilirsiniz.

## <a name="get-device-connection-details"></a>Cihaz bağlantısı ayrıntılarını al

Azure IoT Central uygulamanızda, **Yönetim** sekmesini seçin ve **cihaz bağlantısı**' nı seçin. **Kapsam kimliği** ve **birincil anahtarı** ( **anahtarları görüntüle** bağlantısında) bir yere göz önünde yapın. Ve **otomatik onaylama** özelliğinin etkinleştirildiğinden emin olun.

![Cihaz grubu bağlantı ayrıntıları](media/howto-connect-devkit/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Cihazı hazırlama

1. DevKit cihazı için [önceden oluşturulmuş en son Azure IoT Central Tak ve kullan bellenimini](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) GitHub 'dan indirin.

1. DevKit cihazını bir USB kablosu kullanarak geliştirme makinenize bağlayın. Windows 'da, DevKit cihazında depolama ile eşleştirilmiş bir sürücüde dosya Gezgini penceresi açılır. Örneğin, sürücü **AZ3166 (D:)** olarak adlandırılabilir.

1. **İotc_devkit. bin** dosyasını sürücü penceresine sürükleyin. Kopyalama tamamlandığında, cihaz yeni üretici yazılımıyla yeniden başlatılır.

    > [!NOTE]
    > Ekranda **Wi-Fi**gibi hatalar görürseniz, bunun nedeni devkit 'In henüz WiFi 'ye bağlı olmaması olabilir.

1. DevKit üzerinde, **düğme b**tuşunu basılı tutarak **sıfırlama** düğmesini gönderin ve serbest bırakın ve sonra **B düğmesini**bırakın. Cihaz artık erişim noktası modunda. Onaylamak için, ekranda "IoT DevKit-AP" ve yapılandırma portalının IP adresi görüntülenir.

1. Bilgisayarınızda veya tabletinizde, cihazın ekranında gösterilen WiFi ağ adına bağlanın. WiFi ağı **az** ve ardından MAC adresi ile başlar. Bu ağa bağlandığınızda Internet erişiminiz yok demektir. Bu durum beklenir ve bu ağa yalnızca cihazı yapılandırırken kısa bir süre boyunca bağlanırsınız.

1. Web tarayıcınızı açın ve [http://192.168.0.1/](http://192.168.0.1/)gidin. Aşağıdaki Web sayfası görüntülenir:

    ![Yapılandırma Kullanıcı arabirimi](media/howto-connect-devkit/config-ui.png)

    Web sayfasında, şunu girin:

    - WiFi ağınızın adı (SSID).
    - WiFi ağı parolanız.
    - Bağlantı ayrıntıları: kendiniz seçebileceğiniz **CIHAZ kimliği** ve **Kapsam KIMLIĞI** ve **Grup SAS birincil anahtarı** daha önce bir yere göz ınızı yapmış olursunuz.

    > [!NOTE]
    > Şu anda IoT DevKit yalnızca 2,4 GHz Wi-Fi ' a bağlanabilir, donanım kısıtlamaları nedeniyle 5 GHz desteklenmez.

1. **Cihazı Yapılandır**' ı seçin, devkit cihazı yeniden başlatılır ve uygulamayı çalıştırır:

    ![Yeniden başlatma Kullanıcı arabirimi](media/howto-connect-devkit/reboot-ui.png)

    DevKit ekranında uygulamanın çalıştığı bir onay görüntülenir:

    ![DevKit çalışıyor](media/howto-connect-devkit/devkit-running.png)

DevKit öncelikle IoT Central uygulamasına yeni bir cihaz kaydeder ve sonra veri göndermeye başlar.

## <a name="view-the-telemetry"></a>Telemetriyi görüntüleme

Bu adımda, Azure IoT Central uygulamanızda Telemetriyi görüntüleyebilirsiniz.

IoT Central uygulamanızda, **cihazlar** sekmesini seçin, eklediğiniz cihazı seçin. **Genel bakış** sekmesinde, devkit cihazdan Telemetriyi görebilirsiniz:

![IoT Central cihaza genel bakış](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Kodu gözden geçirin

Kodu gözden geçirmek veya değiştirmek ve derlemek için, [Mxyongaıot DevKit örnek kodu GitHub deposuna](https://github.com/MXCHIP/IoTDevKit/tree/master/pnp)gidin.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir DevKit cihazını Azure IoT Central uygulamanıza bağlamayı öğrendiğinize göre, önerilen sonraki adım, kendi IoT cihazınız için [özel cihaz şablonu ayarlamayı](./howto-set-up-template.md) öğrenirsiniz.
