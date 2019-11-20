---
title: Azure IoT Gezginini yüklemeyi ve kullanmayı | Microsoft Docs
description: Azure IoT gezgin aracını yükleyip IoT Hub 'ma bağlı IoT Tak ve Kullan önizleme cihazlarıyla etkileşim kurmak için bu aracı kullanın.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3b5e9a70f9eecbf187a6748073de009653061dc0
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679865"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT Gezginini yükleyip kullanma

Azure IoT Explorer, IoT Tak ve Kullan önizleme cihazlarınızı etkileşimli ve test etmeye yönelik bir grafik aracıdır. Aracı yerel makinenize yükledikten sonra bir cihaza bağlanmak için kullanabilirsiniz. Aracı, cihazın gönderdiği Telemetriyi görüntülemek, cihaz özellikleriyle çalışmak ve komutları çağırmak için kullanabilirsiniz.

Bu makalede nasıl yapılacağı gösterilmektedir:

- Azure IoT gezgin aracını yükleyip yapılandırın.
- Cihazlarınızla etkileşim kurmak ve bunları test etmek için aracını kullanın.

## <a name="prerequisites"></a>Önkoşullar

Azure IoT gezgin aracını kullanmak için şunlar gerekir:

- Azure IoT Hub 'ı. Azure aboneliğinize Azure [CLI kullanarak](../iot-hub/iot-hub-create-using-cli.md)IoT Hub 'ı oluşturma gibi bir IoT Hub 'ı eklemenin birçok yolu vardır. Azure IoT gezgin aracını çalıştırmak için IoT Hub bağlantı dizesine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- IoT Hub 'ınıza kayıtlı bir cihaz. Bir cihazı kaydetmek için aşağıdaki Azure CLı komutunu kullanabilirsiniz. @No__t_0 ve `{YourDeviceID}` yer tutucuları değerlerinizle değiştirdiğinizden emin olun:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Azure IoT Explorer 'ı yükler

[Azure IoT Explorer sürümleri](https://github.com/Azure/azure-iot-explorer/releases) ' ne gidin ve en son sürümün varlık listesini genişletin. Uygulamanın en son sürümünü indirip yükleyin.

## <a name="use-azure-iot-explorer"></a>Azure IoT Gezginini Kullanma

Bir cihaz için kendi cihazınızı bağlanabilir veya örnek sanal cihazlarımızın birini kullanabilirsiniz. Sanal cihaz örneğini çalıştırmak için [Bu yönergeleri](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) izleyin.

### <a name="connect-to-your-hub"></a>Hub 'ınıza bağlanın

Azure IoT Gezginini ilk kez çalıştırdığınızda IoT Hub 'ınızın bağlantı dizesi istenir. Bağlantı dizesini ekledikten sonra **Bağlan**' ı seçin. Bağlantı dizesini güncelleştirerek, aracın ayarlarını başka bir IoT Hub 'ına geçiş yapmak için kullanabilirsiniz.

IoT Tak ve Kullan cihazının model tanımı, genel depoda, bir şirket deposunda veya bağlı cihazınızda saklanır. Varsayılan olarak araç, model tanımınızı ortak model deposunda ve bağlı cihazınızda arar. Kaynak ekleyebilir ve kaldırabilir veya **Ayarlar**' da kaynakların önceliğini yapılandırabilirsiniz:

Kaynak eklemek için:

1. **Ayarlar**' a gidin.
1. **Yeni** ' yi seçin ve kaynağınızı seçin.
1. Şirket modeli deponuzu ekliyorsanız bağlantı dizesini belirtin.

Bir kaynağı kaldırmak için:

1. **Ayarlar**' a gidin.
1. Kaldırmak istediğiniz kaynağı bulun.
1. **X** seçimini kaldırmak için seçin. Ortak arabirim tanımları bu depodan geldiği için ortak model deposunu kaldıramazsınız.

Kaynak önceliklerini değiştirin:

Model tanımı kaynaklarından birini, listedeki farklı bir derecelendirmeye sürükleyip bırakabilirsiniz. Bir çakışma varsa, daha yüksek derecelendirici içeren tanım kaynakları, kaynakları daha düşük derecelendirimiyle geçersiz kılar.

### <a name="view-devices"></a>Cihazları görüntüleme

Araç IoT Hub 'ınıza bağlandıktan sonra, IoT Hub 'ınıza kayıtlı cihaz kimliklerini listeleyen **cihazlar** listesi sayfasını görüntüler. Daha fazla bilgi görmek için listedeki herhangi bir girdiyi genişletebilirsiniz.

**Cihazlar** listesi sayfasında şunları yapabilirsiniz:

- Hub 'ınıza yeni bir cihaz kaydetmek için **Ekle** ' yi seçin. Ardından bir cihaz KIMLIĞI girin. Otomatik olarak kimlik doğrulama anahtarları oluşturmak ve hub 'ınız ile bağlantıyı etkinleştirmek için varsayılan ayarları kullanın.
- Bir cihaz seçin ve ardından bir cihaz kimliğini silmek için **Sil** ' i seçin. Doğru cihaz kimliğini sildiğinizden emin olmak için bu eylemi tamamlamadan önce cihaz ayrıntılarını gözden geçirin.
- @No__t_0 ve `interfaceID` göre sorgulama. Cihazlarınızı sorgulamak için `capabilityID` ya da `interfaceID` bir parametre olarak ekleyin.

## <a name="interact-with-a-device"></a>Cihazla etkileşim kurma

**CIHAZ** **kimliği** sütununda, kayıtlı cihazın ayrıntı sayfasını görüntülemek için bir değer seçin. Her bir cihaz için iki bölüm vardır: **Device** ve **Digital ikizi**.

### <a name="device"></a>Cihaz

Bu bölüm **cihaz kimliği**, **cihaz ikizi**, **telemetri**, **doğrudan yöntem** ve **buluttan cihaza ileti** sekmelerini içerir.

- [Cihaz kimlik bilgilerini](../iot-hub/iot-hub-devguide-identity-registry.md) **cihaz kimliği** sekmesinden görüntüleyebilir ve güncelleştirebilirsiniz.
- Device [ikizi](../iot-hub/iot-hub-devguide-device-twins.md) bilgilerine **Device ikizi** sekmesinden erişebilirsiniz.
- Bir cihaz bağlıysa ve etkin bir şekilde veri [gönderiyorsanız telemetri '](../iot-hub/iot-hub-devguide-messages-read-builtin.md) i **telemetri** sekmesinden görüntüleyebilirsiniz.
- Doğrudan **Yöntem** sekmesinde cihazda [doğrudan yöntem](../iot-hub/iot-hub-devguide-direct-methods.md) çağırabilirsiniz.
- Buluttan cihaza **iletiler** sekmesinden [buluta cihaz iletisi](../iot-hub/iot-hub-devguide-messages-c2d.md) gönderebilirsiniz.

### <a name="digital-twin"></a>Dijital ikizi

Aracı bir görüntüleme dijital ikizi örneğine yönelik olarak kullanabilirsiniz. IoT Tak ve Kullan cihazında cihaz yetenek modeliyle ilişkili tüm arabirimler aracın bu bölümünde görüntülenir. Karşılık gelen [ıot Tak ve Kullan temel](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)öğelerini genişletmek için bir arabirim seçin.

### <a name="interface"></a>Arayüz

**Arabirim** sayfasında, arabirimin JSON tanımını görüntüleyebilirsiniz.

#### <a name="properties"></a>Özellikler

**Yazılabilir olmayan özellikler** sayfasındaki bir arabirimde tanımlanmış salt okuma özelliklerini görüntüleyebilirsiniz. **Yazılabilir** Özellikler sayfasındaki bir arabirimde tanımlanan yazılabilir özellikleri güncelleştirebilirsiniz:

1. **Yazılabilir Özellikler** sayfasına gidin.
1. Güncelleştirmek istediğiniz özelliğe tıklayın.
1. Özellik için yeni bir değer girin.
1. Cihaza gönderilecek yükün önizlemesini görüntüleyin.
1. Değişikliği gönder.

Bir değişiklik gönderdikten sonra, güncelleştirme durumunu takip edebilirsiniz: **eşitleniyor**, **başarılı**veya **hata**. Eşitleme tamamlandığında, **bildirilen özellik** sütununda, özelliğinin yeni değerini görürsünüz. Eşitleme tamamlanmadan önce diğer sayfalara gittiğinizde, güncelleştirme tamamlandığında araç sizi size bildirir. Ayrıca, bildirim geçmişini görmek için aracın bildirim merkezini de kullanabilirsiniz.

#### <a name="commands"></a>Komutlar

Bir cihaza komut göndermek için, **Komutlar** sayfasına gidin:

1. Komut listesinde, tetiklemek istediğiniz komutu genişletin.
1. Komut için gerekli değerleri girin.
1. Cihaza gönderilecek yükün önizlemesini görüntüleyin.
1. Komutunu gönder.

#### <a name="telemetry"></a>Telemetri

Seçili arabirime yönelik Telemetriyi görüntülemek için **telemetri** sayfasına gidin.

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesinde IoT Tak ve Kullan cihazlarınızla etkileşim kurmak için Azure IoT Gezginini yüklemeyi ve kullanmayı öğrendiniz. Önerilen bir sonraki adım [Azure CLI uzantısını yüklemeyi ve kullanmayı](./howto-install-pnp-cli.md)öğreneceksiniz.
