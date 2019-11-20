---
title: Yeni bir Azure IoT Edge cihaz kaydetme | Microsoft Docs
description: Yeni bir IoT Edge cihazı kaydetmek ve bağlantı dizesini almak için Azure CLı için IoT uzantısı 'nı kullanın
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 65fb55a16e358624dceb42a268b035f826576f63
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966261"
---
# <a name="register-an-azure-iot-edge-device"></a>Azure IoT Edge cihazı kaydetme

IoT cihazlarınızı Azure IoT Edge ile kullanabilmeniz için, bunları IoT Hub 'ınıza kaydetmeniz gerekir. Bir cihaz kaydedildikten sonra, cihazınızı IoT Edge iş yükleri için ayarlamak üzere bir bağlantı dizesi alabilirsiniz.

Aşağıdaki araçlardan birini kullanarak kayıt seçeneğiniz vardır:

* [Azure Portal](https://portal.azure.com) , Azure kaynaklarını oluşturmak, görüntülemek ve yönetmek için grafik kullanıcı arabirimi sağlar.
* [Visual Studio Code](https://code.visualstudio.com/) , kaynak kodu düzenleyicidir. Azure IoT uzantıları, IoT çözümlerini geliştirmekte olduğunuz araçlardan IoT kaynaklarını yönetmeyi kolaylaştırır.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) , Azure kaynaklarını yönetmeye yönelik bir komut satırı aracıdır. Yeniden kullanılabilir komutları, görevleri otomatikleştirme için yararlıdır.

## <a name="register-in-the-azure-portal"></a>Azure portal kaydetme

Azure portal tüm kayıt görevlerini gerçekleştirebilirsiniz.

### <a name="prerequisites-for-the-azure-portal"></a>Azure portal önkoşulları

Azure aboneliğinizde ücretsiz veya standart bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure portal IoT Edge cihaz oluşturma

Azure portal IoT Hub, IoT Edge cihazlar kenar etkin olmayan ıOT cihazlarından ayrı olarak oluşturulur ve yönetilir.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.
2. Sol bölmede, menüden **IoT Edge** ' yi seçin.
3. **IoT Edge cihaz ekle**' yi seçin.
4. Açıklayıcı bir cihaz KIMLIĞI sağlayın. Kimlik doğrulama anahtarlarını otomatik oluşturmak ve yeni cihazı hub 'ınıza bağlamak için varsayılan ayarları kullanın.
5. **Kaydet**’i seçin.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure portal IoT Edge cihazları görüntüleme

IoT Hub 'ınıza bağlanan tüm Edge özellikli cihazlar **IoT Edge** sayfasında listelenir.

![IoT Hub 'ınızdaki tüm IoT Edge cihazları görüntüleme](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Azure portal bağlantı dizesini alma

Cihazınızı ayarlamaya hazırsanız, fiziksel cihazınızı IoT Hub 'ındaki kimliğiyle bağlayan bağlantı dizesine ihtiyacınız vardır.

1. Portaldaki **IoT Edge** sayfasında, IoT Edge cihazları LISTESINDEN cihaz kimliği ' ne tıklayın.
2. **Bağlantı dizesinin (birincil anahtar)** ya da **bağlantı dizesinin (ikincil anahtar)** değerini kopyalayın.

## <a name="register-with-visual-studio-code"></a>Visual Studio Code Kaydet

VS Code birçok işlemi gerçekleştirmenin birden çok yolu vardır. Bu makalede gezgin kullanılmaktadır, ancak adımları çalıştırmak için komut paletini de kullanabilirsiniz.

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code önkoşulları

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>IoT Hub 'ınıza erişmek için oturum açın

IoT Hub işlemleri gerçekleştirmek için Azure IoT uzantılarını Visual Studio Code kullanabilirsiniz. Bu işlemlerin çalışması için Azure hesabınızda oturum açmanız ve IoT Hub seçmeniz gerekir.

1. Visual Studio Code ' de **Gezgin** görünümünü açın.
1. Gezgin 'in alt kısmındaki **Azure IoT Hub** bölümünü genişletin.

   ![Azure IoT Hub cihazları bölümünü Genişlet](./media/how-to-register-device/azure-iot-hub-devices.png)

1. **Azure IoT Hub** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç nokta görmüyorsanız, üzerine tıklayın veya üstbilginin üzerine gelin.
1. **IoT Hub Seç ' i**seçin.
1. Azure hesabınızda oturum açmadıysanız, bunu yapmak için istemleri izleyin.
1. Azure aboneliğinizi seçin.
1. IoT Hub 'ınızı seçin.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Visual Studio Code ile IoT Edge cihaz oluşturma

1. VS Code Gezgini ' nde, **Azure IoT Hub cihazları** bölümünü genişletin.
1. **Azure IoT Hub Devices** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç nokta görmüyorsanız, üzerine tıklayın veya üstbilginin üzerine gelin.
1. **IoT Edge cihaz oluştur**' u seçin.
1. Açılan metin kutusunda, cihazınıza bir KIMLIK verin.

Çıkış ekranında, komutun sonucunu görürsünüz. Belirttiğiniz **DeviceID** 'yi ve fiziksel cihazınızı IoT Hub 'ınıza bağlamak Için kullanabileceğiniz **ConnectionString** öğesini içeren cihaz bilgileri yazdırılır.

Çıkış ekranında, komutun sonucunu görürsünüz. Belirttiğiniz **DeviceID** 'yi ve fiziksel cihazınızı IoT Hub 'ınıza bağlamak Için kullanabileceğiniz **ConnectionString** öğesini içeren cihaz bilgileri yazdırılır.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code olan IoT Edge cihazları görüntüleme

IoT Hub 'ınıza bağlanan tüm cihazlar, Visual Studio Code Gezgini 'nin **Azure IoT Hub** bölümünde listelenmiştir. IoT Edge cihazlar, farklı bir simgeyle uç olmayan cihazlardan ayırt edilemez ve **$edgeAgent** ve **$edgeHub** modüllerinin her bir IoT Edge cihazına dağıtılmasıdır.

![IoT Hub 'ınızdaki tüm IoT Edge cihazları görüntüleme](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Bağlantı dizesini Visual Studio Code alma

Cihazınızı ayarlamaya hazırsanız, fiziksel cihazınızı IoT Hub 'ındaki kimliğiyle bağlayan bağlantı dizesine ihtiyacınız vardır.

1. **Azure IoT Hub** bölümünde cihazınızın kimliğine sağ tıklayın.
1. **Cihaz bağlantı dizesini Kopyala**' yı seçin.

   Bağlantı dizesi panonuza kopyalanır.

Ayrıca, çıkış penceresindeki bağlantı dizesi dahil tüm cihaz bilgilerini görmek için sağ tıklama menüsünden **cihaz bilgilerini al** ' ı da seçebilirsiniz.

## <a name="register-with-the-azure-cli"></a>Azure CLı ile kaydolun

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) , IoT Edge gibi Azure kaynaklarını yönetmeye yönelik açık kaynaklı bir platformlar arası komut satırı aracıdır. Azure IoT Hub kaynaklarını, cihaz sağlama hizmeti örneklerini ve bağlı hub 'ları kutudan dışarı yönetmenizi sağlar. Yeni IoT uzantısı, Azure CLı 'yi cihaz yönetimi ve tam IoT Edge özelliği gibi özelliklerle zenginleştirir.

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLı önkoşulları

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-using-cli.md) .
* Ortamınızdaki [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) . Azure CLı sürümünüz en azından 2.0.24 veya üzeri olmalıdır. Doğrulamak için `az --version` kullanın. Bu sürüm, az uzantı komutlarını destekler ve Knack komut çerçevesini kullanıma sunar.
* [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLı ile IoT Edge cihaz oluşturma

IoT Hub 'ınızda yeni bir cihaz kimliği oluşturmak için [az IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) komutunu kullanın. Örnek:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Bu komut üç parametre içerir:

* **cihaz kimliği**: IoT Hub 'ınız için benzersiz olan açıklayıcı bir ad sağlayın.
* **hub-adı**: IoT Hub 'ınızın adını sağlayın.
* **kenar etkin**: Bu parametre, cihazın IoT Edge birlikte kullanılacağını bildirir.

   ![az IoT Hub cihazı-kimlik oluşturma çıkışı](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLı ile IoT Edge cihazları görüntüleme

IoT Hub 'ınızdaki tüm cihazları görüntülemek için [az IoT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) komutunu kullanın. Örnek:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge cihaz olarak kaydedilmiş tüm cihazlarda özellik **özellikleri olur. ıotedge** **doğru**olarak ayarlanır.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLı ile bağlantı dizesini alma

Cihazınızı ayarlamaya hazırsanız, fiziksel cihazınızı IoT Hub 'ındaki kimliğiyle bağlayan bağlantı dizesine ihtiyacınız vardır. Tek bir cihaza yönelik bağlantı dizesini döndürmek için [az IoT Hub cihazı-Identity Show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) komutunu kullanın:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` parametresinin değeri büyük/küçük harfe duyarlıdır. Bağlantı dizesinin etrafında tırnak işaretlerini kopyalamayın.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Hub 'ınıza kayıtlı bir cihaz kimliğiniz olduğuna göre, cihazlarınıza IoT Edge çalışma zamanını yüklemeye hazırsınız demektir. Çalışma zamanını cihazın işletim sistemine göre yükler:

* [Windows 'a Azure IoT Edge yüklemesi](how-to-install-iot-edge-windows.md)
* [Linux üzerinde Azure IoT Edge çalışma zamanını yükler](how-to-install-iot-edge-linux.md)
