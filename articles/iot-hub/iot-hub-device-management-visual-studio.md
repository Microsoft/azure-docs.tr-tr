---
title: Azure IoT cihaz yönetimi w/Visual Studio Cloud Explorer
description: Doğrudan Yöntemler ve Ikizi 'ın istenen özellikler yönetim seçeneklerine sahip Azure için Visual Studio IoT Hub cihaz yönetimi için Cloud Explorer 'ı kullanın.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953193"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Azure için Visual Studio IoT Hub cihaz yönetimi için Cloud Explorer 'ı kullanma

![Uçtan uca diyagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Bulut Gezgini](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) , Azure kaynaklarınızı görüntülemenize, özelliklerini Incelemenize ve Visual Studio içinden önemli geliştirici eylemleri gerçekleştirmenize olanak tanıyan yararlı bir Visual Studio uzantısıdır. Bu, çeşitli görevleri gerçekleştirmek için kullanabileceğiniz yönetim seçenekleriyle birlikte gelir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Yönetim seçeneği          | Görev                    |
|----------------------------|--------------------------------|
| Doğrudan yöntemler             | İleti göndermeyi başlatma veya durdurma ya da cihazı yeniden başlatma gibi bir cihaz oluşturun.                                        |
| Cihaz ikizi okuma           | Bir cihazın bildirilen durumunu alır. Örneğin, cihaz, LED 'in Şu anda yanıp söndüğünü bildiriyor.                                    |
| Cihaz ikizi Güncelleştir         | Bir cihazı, bir ışığı yeşil olarak ayarlama veya telemetri gönderme aralığını 30 dakikaya ayarlama gibi belirli durumlara yerleştirin.         |
| Bulut-cihaz iletilerini   | Bir cihaza bildirim gönderin. Örneğin, "Bugün sizi yağmur olasıdır. Bir şemsiye getirmeyi unutmayın. "              |

Bu seçenekleri kullanmayla ilgili farklılıklar ve yönergeler hakkında daha ayrıntılı bilgi için bkz. [cihazdan buluta iletişim Kılavuzu](iot-hub-devguide-d2c-guidance.md) ve [buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md).

Cihaz TWINS, meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere cihaz durum bilgilerini depolayan JSON belgelerdir. IoT Hub, kendisine bağlanan her cihaz için bir cihaz ikizi devam ettirir. Cihaz TWINS hakkında daha fazla bilgi için bkz. [cihaz ikgörülerle çalışmaya başlama](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Öğrenecekleriniz

Bu makalede, geliştirme bilgisayarınızda çeşitli yönetim seçenekleriyle Visual Studio için bulut Gezgini 'ni nasıl kullanacağınızı öğreneceksiniz.

## <a name="what-you-do"></a>Yapabilecekleriniz

Bu makalede, çeşitli yönetim seçenekleriyle Visual Studio için Cloud Explorer 'ı çalıştırın.

## <a name="what-you-need"></a>Ne gerekiyor

Aşağıdaki önkoşulların olması gerekir:

- Etkin bir Azure aboneliği.

- Aboneliğiniz altındaki bir Azure IoT Hub.

- Microsoft Visual Studio 2017 güncelleştirme 9 veya üzeri. Bu makalede [Visual studio 2017 veya Visual studio 2019](https://www.visualstudio.com/vs/)kullanılmaktadır.

- Azure Iş yükü ile varsayılan olarak seçilen Visual Studio Yükleyicisi bulut Gezgini bileşeni.

## <a name="update-cloud-explorer-to-latest-version"></a>Bulut Gezginini en son sürüme Güncelleştir

Visual Studio 2017 için Visual Studio Yükleyicisi bulut Gezgini bileşeni yalnızca cihazdan buluta ve buluttan cihaza iletileri izlemeyi destekler. Visual Studio 2017 ' i kullanmak için en son [bulut Gezginini](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)indirip yükleyin.

## <a name="sign-in-to-access-your-hub"></a>Hub 'ınıza erişmek için oturum açın

1. Visual Studio 'da bulut Gezgini 'ni açmak **için > ** **bulut Gezgini** ' ni seçin.

1. Aboneliklerinizi göstermek için hesap yönetimi simgesini seçin.

    ![Hesap yönetimi simgesi](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Azure 'da oturum açtıysanız hesaplarınız görüntülenir. Azure 'da ilk kez oturum açmak için **Hesap Ekle**' yi seçin.

1. Kullanmak istediğiniz Azure aboneliklerini seçin ve **Uygula**' yı seçin.

1. Aboneliğinizi genişlettikten sonra **IoT Hub 'ları**' nı genişletin.  Her hub 'ın altında bu hub için cihazlarınızı görebilirsiniz. Yönetim seçeneklerine erişmek için bir cihaza sağ tıklayın.

    ![Yönetim seçenekleri](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Doğrudan yöntemler

Doğrudan yöntemler kullanmak için aşağıdaki adımları uygulayın:

1. Cihazınıza sağ tıklayıp **Cihaz doğrudan yöntemini çağır**' ı seçin.

1. Yöntem adını ve yükünü **doğrudan çağırma yöntemine**girip **Tamam**' ı seçin.

    Sonuçlar **çıktıda**görüntülenir.

## <a name="update-device-twin"></a>Cihaz ikizi Güncelleştir

Bir cihaz ikizi düzenlemek için aşağıdaki adımları uygulayın:

1. Cihazınıza sağ tıklayın ve **cihaz Ikizi Düzenle**' yi seçin.

   Bir **Azure-IoT-Device-ikizi. JSON** dosyası, Device ikizi içeriğiyle açılır.

1. **Etiketlerin** veya özelliklerin bazı düzenlemeleri yapın **.** **Azure-IoT-Device-ikizi. JSON** dosyasında istenen alanlar.

1. Cihaz ikizi güncelleştirmek için **CTRL + S** tuşlarına basın.

   Sonuçlar **çıktıda**görüntülenir.

## <a name="send-cloud-to-device-messages"></a>Buluttan cihaza iletileri gönderme

IoT Hub cihazınıza bir ileti göndermek için şu adımları izleyin:

1. Cihazınıza sağ tıklayın ve **C2D Iletisi gönder**' i seçin.

1. **C2D Iletisi gönder** ' de iletiyi girin ve **Tamam**' ı seçin.

   Sonuçlar **çıktıda**görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

Visual Studio için Cloud Explorer 'ı çeşitli yönetim seçenekleriyle nasıl kullanacağınızı öğrendiniz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
