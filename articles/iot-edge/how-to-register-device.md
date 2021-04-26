---
title: Yeni bir cihaz kaydetme-Azure IoT Edge | Microsoft Docs
description: Simetrik anahtarlar veya X. 509.440 sertifikaları ile el ile sağlama için IoT Hub içinde tek bir IoT Edge cihazı kaydetme
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481880"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>IoT Hub bir IoT Edge cihazı kaydetme

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Bu makalede, IoT Hub yeni bir IoT Edge cihazı kaydetme adımları sağlanmaktadır.

Bir IoT Hub 'ına bağlanan her cihazda, buluttan cihaza veya cihazdan buluta iletişimleri izlemek için kullanılan bir cihaz KIMLIĞI vardır. Bir cihazı, IoT Hub ana bilgisayar adı, cihaz KIMLIĞI ve cihazın IoT Hub kimlik doğrulaması için kullandığı bilgileri içeren bağlantı bilgileriyle yapılandırırsınız.

Bu makaledeki adımlarda, tek bir cihazı IoT Hub 'ına bağladığınızda el ile sağlama adlı bir süreç gösterilmektedir. El ile sağlama için, IoT Edge cihazların kimliğini doğrulamak için iki seçeneğiniz vardır:

* **Simetrik anahtar**: IoT Hub yeni bir cihaz kimliği oluşturduğunuzda, hizmet iki anahtar oluşturur. Bir cihazdan anahtardan birini yerleştirebilirsiniz ve kimlik doğrulanırken IoT Hub anahtarı gösterir.

  Bu kimlik doğrulama yöntemi kullanmaya başlamak için daha hızlıdır, ancak güvenli değildir.

* **X. 509.440 otomatik imzalı**: iki X. 509.440 kimlik sertifikası oluşturup cihaza yerleştirebilirsiniz. IoT Hub yeni bir cihaz kimliği oluşturduğunuzda, her iki sertifika için de parmak izleri sağlarsınız. Cihaz IoT Hub kimlik doğrulaması yaparken, bir sertifika gösterir ve IoT Hub sertifikanın parmak izine uygun olduğunu doğrular.

  Bu kimlik doğrulama yöntemi daha güvenlidir ve üretim senaryoları için önerilir.

Bu makalede her iki kimlik doğrulama yöntemi ele alınmaktadır.

Ayarlanacak ve her birini el ile sağlamak istemediğiniz birçok cihazınız varsa, IoT Edge IoT Hub cihaz sağlama hizmeti ile nasıl çalıştığını öğrenmek için aşağıdaki makalelerden birini kullanın:

* [X. 509.440 sertifikalarını kullanarak IoT Edge cihazları oluşturma ve sağlama](how-to-auto-provision-x509-certs.md)
* [TPM ile IoT Edge cihazları oluşturma ve sağlama](how-to-auto-provision-simulated-device-linux.md)
* [Simetrik anahtarlar kullanarak IoT Edge cihazları oluşturma ve sağlama](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Önkoşullar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure aboneliğinizde ücretsiz veya standart bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure aboneliğinizde ücretsiz veya standart bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure aboneliğinizde ücretsiz veya standart bir [IoT Hub 'ı](../iot-hub/iot-hub-create-using-cli.md) .
* Ortamınızdaki [Azure CLI](/cli/azure/install-azure-cli) . Azure CLı sürümünüz en azından 2.0.70 veya daha yeni olmalıdır. Doğrulamak için `az --version` kullanın. Bu sürüm, az uzantı komutlarını destekler ve Knack komut çerçevesini kullanıma sunar.

---

## <a name="option-1-register-with-symmetric-keys"></a>Seçenek 1: simetrik anahtarlarla kaydetme

IoT Hub yeni bir IoT Edge cihazı kaydetmek ve tercihinize bağlı olarak bağlantı dizesini almak için birkaç araç kullanabilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal IoT Hub 'ınızda, IoT Edge cihazlar kenar etkin olmayan IoT cihazlarından ayrı olarak oluşturulur ve yönetilir.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

1. Sol bölmede, menüden **IoT Edge** ' i seçin ve ardından **IoT Edge cihaz ekle**' yi seçin.

   ![Azure portal IoT Edge bir cihaz ekleyin](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. **Cihaz oluştur** sayfasında, aşağıdaki bilgileri sağlayın:

   * Açıklayıcı bir cihaz KIMLIĞI oluşturun.
   * Kimlik doğrulama türü olarak **simetrik anahtar** ' ı seçin.
   * Kimlik doğrulama anahtarlarını otomatik oluşturmak ve yeni cihazı hub 'ınıza bağlamak için varsayılan ayarları kullanın.

1. **Kaydet**’i seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>IoT Hub 'ınıza erişmek için oturum açın

IoT Hub 'ınız ile işlemler gerçekleştirmek için Visual Studio Code için Azure IoT uzantılarını kullanabilirsiniz. Bu işlemlerin çalışması için Azure hesabınızda oturum açmanız ve hub 'ınızı seçmeniz gerekir.

1. Visual Studio Code ' de **Gezgin** görünümünü açın.
1. Gezgin 'in alt kısmındaki **Azure IoT Hub** bölümünü genişletin.

   ![Azure IoT Hub cihazları bölümünü Genişlet](./media/how-to-register-device/azure-iot-hub-devices.png)

1. **Azure IoT Hub** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç nokta görmüyorsanız, üzerine tıklayın veya üstbilginin üzerine gelin.
1. **IoT Hub Seç ' i** seçin.
1. Azure hesabınızda oturum açmadıysanız, bunu yapmak için istemleri izleyin.
1. Azure aboneliğinizi seçin.
1. IoT Hub 'ınızı seçin.

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code yeni bir cihaz kaydetme

1. Visual Studio Code Gezgini ' nde **Azure IoT Hub** bölümünü genişletin.
1. **Azure IoT Hub** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç nokta görmüyorsanız, üzerine tıklayın veya üstbilginin üzerine gelin.
1. **IoT Edge cihaz oluştur**' u seçin.
1. Açılan metin kutusunda, cihazınıza bir KIMLIK verin.

Çıkış ekranında, komutun sonucunu görürsünüz. Belirttiğiniz **DeviceID** 'yi ve fiziksel cihazınızı IoT Hub 'ınıza bağlamak Için kullanabileceğiniz **ConnectionString** öğesini içeren cihaz bilgileri yazdırılır.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT Hub 'ınızda yeni bir cihaz kimliği oluşturmak için [az IoT Hub Device-Identity Create](/cli/azure/iot/hub/device-identity) komutunu kullanın. Örnek:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Bu komut üç parametre içerir:

* `--device-id` ya da `-d` : IoT Hub 'ınız dahilinde benzersiz olan açıklayıcı bir ad sağlayın.
* `--hub-name` ya da `-n` : IoT Hub 'ınızın adını sağlayın.
* `--edge-enabled` veya `--ee` : cihazın IoT Edge bir cihaz olduğunu bildirin.

   ![az IoT Hub cihazı-kimlik oluşturma çıkışı](./media/how-to-register-device/create-edge-device-cli.png)

---

Artık IoT Hub kayıtlı bir cihazınız olduğuna göre, IoT Edge çalışma zamanının yüklenmesi ve sağlanması için kullandığınız bağlantı dizesini alın. [Kayıtlı cihazları görüntülemek ve bağlantı dizelerini almak](#view-registered-devices-and-retrieve-connection-strings)için bu makalenin devamındaki adımları izleyin.

## <a name="option-2-register-with-x509-certificates"></a>Seçenek 2: X. 509.440 sertifikalarına kaydolma

X. 509.440 sertifikaları ile el ile sağlama, IoT Edge sürüm 1.0.10 veya daha yenisini gerektirir.

X. 509.952 sertifikası kimlik doğrulaması için, her cihazın kimlik doğrulama bilgileri, cihaz kimlik sertifikalarınızın alındığı *parmak izleri* biçiminde sağlanır. Bu parmak izlerinin cihaz kaydı sırasında IoT Hub, hizmetin bağlandığı sırada cihazı tanıyabilmesi için bu parmak izleri verilmiştir.

### <a name="create-certificates-and-thumbprints"></a>Sertifika ve parmak izleri oluşturma

X. 509.440 sertifikalarıyla IoT Edge bir cihaz sağladığınızda, *cihaz kimlik sertifikası* olarak adlandırılan öğeleri kullanırsınız. Bu sertifika yalnızca IoT Edge bir cihaz sağlamak ve cihazın Azure IoT Hub kimlik doğrulamasını yapmak için kullanılır. Bu, diğer sertifikaları imzalayameyen bir yaprak sertifikadır. Cihaz kimliği sertifikası, IoT Edge cihazın, doğrulama için modüller veya aşağı akış cihazlarına sunduğu sertifika yetkilisi (CA) sertifikalarından ayrıdır. CA sertifikalarının IoT Edge cihazlarda nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [Azure IoT Edge sertifikaları nasıl kullandığını anlayın](iot-edge-certs.md).

X. 509.952 ile el ile sağlama için aşağıdaki dosyalara ihtiyacınız vardır:

* . Cer veya. ped biçimlerinde eşleşen özel anahtar sertifikaları olan iki cihaz kimliği sertifikası.

  IoT Edge çalışma zamanına bir sertifika/anahtar dosyası kümesi verilmiştir. Cihaz kimlik sertifikaları oluştururken, sertifika ortak adı 'nı (CN) cihazın IoT Hub 'ınızda olmasını istediğiniz cihaz KIMLIĞIYLE ayarlayın.

* Her iki cihaz kimlik sertifikasından alınan parmak izleri.

  Parmak izi değerleri, SHA-1 karmaları için 40 onaltılı karakterler veya SHA-256 karmaları için 64 onaltılık karakterdir. Her iki parmak izi de IoT Hub için cihaz kaydı sırasında sağlanır.

Kullanılabilir sertifikanız yoksa, [IoT Edge cihaz özelliklerini test etmek için tanıtım sertifikaları oluşturabilirsiniz](how-to-create-test-certificates.md). Sertifika oluşturma betikleri ayarlamak, bir kök CA sertifikası oluşturmak ve ardından iki IoT Edge cihaz kimlik sertifikası oluşturmak için bu makaledeki yönergeleri izleyin.

Bir sertifikadan parmak izini almanın bir yolu aşağıdaki OpenSSL komutuna sahiptir:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Yeni bir cihaz Kaydet

IoT Hub yeni bir IoT Edge cihazı kaydetmek ve sertifika parmak izlerini karşıya yüklemek için çeşitli araçları kullanabilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal IoT Hub 'ınızda, IoT Edge cihazlar kenar etkin olmayan IoT cihazlarından ayrı olarak oluşturulur ve yönetilir.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

1. Sol bölmede, menüden **IoT Edge** ' i seçin ve ardından **IoT Edge cihaz ekle**' yi seçin.

   ![Azure portal IoT Edge bir cihaz ekleyin](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. **Cihaz oluştur** sayfasında, aşağıdaki bilgileri sağlayın:

   * Açıklayıcı bir cihaz KIMLIĞI oluşturun. Sonraki bölümde kullanacağınız gibi, bu cihaz KIMLIĞINI bir yere unutmayın.
   * Kimlik doğrulama türü olarak **X. 509.440 otomatik olarak imzalanan** ' ı seçin.
   * Birincil ve ikincil kimlik sertifikası parmak izlerini sağlayın. Parmak izi değerleri, SHA-1 karmaları için 40 onaltılı karakterler veya SHA-256 karmaları için 64 onaltılık karakterdir.

1. **Kaydet**’i seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Şu anda Visual Studio Code için Azure IoT uzantısı, X. 509.440 sertifikalarıyla cihaz kaydını desteklemez.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT Hub 'ınızda yeni bir cihaz kimliği oluşturmak için [az IoT Hub Device-Identity Create](/cli/azure/iot/hub/device-identity) komutunu kullanın. Örnek:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Bu komut çeşitli parametreleri içerir:

* `--device-id` ya da `-d` : IoT Hub 'ınız için benzersiz olan açıklayıcı bir ad sağlayın. Sonraki bölümde kullanacağınız gibi, bu cihaz KIMLIĞINI bir yere unutmayın.
* `hub-name` ya da `-n` : IoT Hub 'ınızın adını sağlayın.
* `--edge-enabled` veya `--ee` : cihazın IoT Edge bir cihaz olduğunu bildirin.
* `--auth-method` veya `--am` : cihazın kullanacağı Yetkilendirme türünü bildirin. Bu durumda, X. 509.440 sertifikası parmak izlerini kullanıyoruz.
* `--primary-thumbprint` ya da `--ptp` : birincil anahtar olarak kullanılacak bir X. 509.440 sertifika parmak izi sağlayın.
* `--secondary-thumbprint` ya da `--stp` : ikincil anahtar olarak kullanılacak bir X. 509.440 sertifika parmak izi sağlayın.

---

Artık IoT Hub kayıtlı bir cihazınız olduğuna göre, cihazınızda IoT Edge çalışma zamanını yüklemeye ve sağlamaya hazırsınız demektir. X. 509.440 sertifikalarıyla kimlik doğrulayan IoT Edge cihazlar bağlantı dizelerini kullanmaz, bu nedenle sonraki adıma devam edebilirsiniz:

* [Linux için Azure IoT Edge yükleme veya kaldırma](how-to-install-iot-edge.md)
* [Windows için Azure IoT Edge yükleme veya kaldırma](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Kayıtlı cihazları görüntüleme ve bağlantı dizelerini alma

Simetrik anahtar kimlik doğrulaması kullanan cihazların, IoT Edge çalışma zamanının yüklenmesi ve sağlanması için bağlantı dizelerine ihtiyacı vardır.

X. 509.440 sertifikası kimlik doğrulaması kullanan cihazlarda bağlantı dizelerine gerek yoktur. Bunun yerine, bu cihazların IoT Hub 'ı adı, cihaz adı ve sertifika dosyaları, IoT Edge çalışma zamanının yüklenmesini ve sağlanmasını tamamlayacak şekilde gereklidir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

IoT Hub 'ınıza bağlanan tüm Edge özellikli cihazlar **IoT Edge** sayfasında listelenir.

![IoT Hub 'ınızdaki tüm IoT Edge cihazları görüntülemek için Azure portal kullanın](./media/how-to-register-device/portal-view-devices.png)

Cihazınızı ayarlamaya hazırsanız, fiziksel cihazınızı IoT Hub 'ındaki kimliğiyle bağlayan bağlantı dizesine ihtiyacınız vardır.

Simetrik Anahtarlarla kimlik doğrulayan cihazların, bağlantı dizeleri portalda kopyalamak için kullanılabilir.

1. Portaldaki **IoT Edge** sayfasında, IoT Edge cihazları LISTESINDEN cihaz kimliği ' ne tıklayın.
2. **Birincil bağlantı dizesinin** veya **İkincil bağlantı dizesinin** değerini kopyalayın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code olan IoT Edge cihazları görüntüleme

IoT Hub 'ınıza bağlanan tüm cihazlar, Visual Studio Code Gezgini 'nin **Azure IoT Hub** bölümünde listelenmiştir. IoT Edge cihazlar, farklı bir simgeyle uç olmayan cihazlardan ayırt edilemez ve **$edgeAgent** ve **$edgeHub** modüllerinin her bir IoT Edge cihazına dağıtılmasıdır.

![IoT Hub 'ınızdaki tüm IoT Edge cihazları görüntülemek için VS Code kullanma](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Bağlantı dizesini Visual Studio Code alma

Cihazınızı ayarlamaya hazırsanız, fiziksel cihazınızı IoT Hub 'ındaki kimliğiyle bağlayan bağlantı dizesine ihtiyacınız vardır.

1. **Azure IoT Hub** bölümünde cihazınızın kimliğine sağ tıklayın.
1. **Cihaz bağlantı dizesini Kopyala**' yı seçin.

   Bağlantı dizesi panonuza kopyalanır.

Ayrıca, çıkış penceresindeki bağlantı dizesi dahil tüm cihaz bilgilerini görmek için sağ tıklama menüsünden **cihaz bilgilerini al** ' ı da seçebilirsiniz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLı ile IoT Edge cihazları görüntüleme

IoT Hub 'ınızdaki tüm cihazları görüntülemek için [az IoT Hub Device-Identity List](/cli/azure/iot/hub/device-identity) komutunu kullanın. Örnek:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge cihaz olarak kaydedilmiş tüm cihazlarda özellik **özellikleri olur. ıotedge** **doğru** olarak ayarlanır.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLı ile bağlantı dizesini alma

Cihazınızı ayarlamaya hazırsanız, fiziksel cihazınızı IoT Hub 'ındaki kimliğiyle bağlayan bağlantı dizesine ihtiyacınız vardır. Tek bir cihaza yönelik bağlantı dizesini döndürmek için [az IoT Hub cihazı-Identity Connection-String Show](/cli/azure/iot/hub/device-identity/connection-string) komutunu kullanın:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>`connection-string show`Komut, kullanım dışı bırakılan komutu değiştirerek Azure IoT uzantısının 0.9.8 sürümünde kullanıma sunulmuştur `show-connection-string` . Bu komutu çalıştırırken bir hata alırsanız, uzantı sürümünüzün 0.9.8 veya sonraki bir sürüme güncelleştirildiğinden emin olun. Daha fazla bilgi ve en son güncelleştirmeler için bkz. [Azure CLI için Microsoft Azure IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension).

`device-id`Parametresinin değeri büyük/küçük harfe duyarlıdır.

Bir cihazda kullanılacak bağlantı dizesini kopyalarken, bağlantı dizesinin etrafında tırnak işaretleri eklemeyin.

---

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Hub kayıtlı bir cihazınız olduğuna göre, cihazınızda IoT Edge çalışma zamanını yüklemeye ve sağlamaya hazırsınız demektir.

* [Linux için Azure IoT Edge yükleme veya kaldırma](how-to-install-iot-edge.md)
* [Windows için Azure IoT Edge yükleme veya kaldırma](how-to-install-iot-edge-windows-on-windows.md)