---
title: include dosyası
description: include dosyası
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763435"
---
## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services dağıtma ve yapılandırma

Çözüm, IoT Edge ağ geçidi cihazı tarafından yapılan nesne algılama video kliplerini depolamak için bir Azure Media Services hesabı kullanır.

Media Services hesabı oluşturduğunuzda:

- Hesap adı, Azure aboneliği, konum, kaynak grubu ve depolama hesabı sağlamanız gerekir. Media Services hesabını oluştururken varsayılan ayarları kullanarak yeni bir depolama hesabı oluşturun.

- Konumun **Doğu ABD** bölgesini seçin.

- Media Services ve depolama hesapları için **Doğu ABD** bölgesinde *LVA-RG* adlı yeni bir kaynak grubu oluşturun. Öğreticileri tamamladığınızda, *LVA-RG* kaynak grubunu silerek tüm kaynakları kolayca kaldırabilirsiniz.

[Media Services hesabı Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService)oluşturun.

> [!TIP]
> Bu öğreticiler, tüm örneklerde **Doğu ABD** bölgesini kullanır. İsterseniz en yakın bölgenizi kullanabilirsiniz.

*scratchpad.txt* dosyasında **Media Services** hesabınızın adını bir yere göz önüne alın.

Dağıtım tamamlandığında, bir Cloud Shell açın ve medya hizmeti hesabınızın **kaynak kimliğini** almak için aşağıdaki komutu çalıştırın:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Kaynak KIMLIĞINI almak için Cloud Shell kullanın":::

*scratchpad.txt* DOSYASıNDAKI **kaynak kimliğini** bir yere getirin, bu değeri daha sonra IoT Edge modülünü yapılandırdığınızda kullanırsınız.

Sonra, Media Services kaynağınız için bir Azure Active Directory hizmet sorumlusu yapılandırın. **API erişimi** ' ni ve ardından **hizmet sorumlusu kimlik doğrulamasını** seçin. Media Services kaynağınız ile aynı ada sahip yeni bir Azure Active Directory uygulaması oluşturun ve bir açıklama *IoT Edge erişime* sahip bir gizli dizi oluşturun.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Azure A D uygulamasını Azure Media Services için yapılandırma":::

Gizli dizi oluşturulduğunda, **hizmet sorumlusu uygulamanızı bağlamak için kimlik bilgilerinizi kopyalama** bölümüne gidin. Ardından **JSON**' ı seçin. Tüm kimlik bilgisi bilgilerini buradan bir go içinde kopyalayabilirsiniz. *scratchpad.txt* dosyasında bu bilgileri bir yere, daha sonra IoT Edge cihazını yapılandırdığınızda kullanacağınızı görürsünüz.

> [!WARNING]
> Bu, gizli dizi görüntüleme ve kaydetme şansınız olur. Kaybederseniz, başka bir gizli dizi oluşturmanız gerekir.

## <a name="create-the-azure-iot-central-application"></a>Azure IoT Central uygulamasını oluşturma

Bu bölümde, bir şablondan yeni bir Azure IoT Central uygulaması oluşturacaksınız. Bu uygulamayı, tüm bir çözüm oluşturmak için öğretici serisi boyunca kullanırsınız.

Yeni bir Azure IoT Central uygulaması oluşturmak için:

1. [Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesine gidin.

1. Azure aboneliğinize erişmek için kullandığınız kimlik bilgileriyle oturum açın.

1. Yeni bir Azure IoT Central uygulaması oluşturmaya başlamak için **Yapı** sayfasında **Yeni uygulama** ' yı seçin.

1. **Perakende**' ı seçin. Perakende sayfasında çeşitli perakende uygulama şablonları görüntülenir.

Yeni bir video analizi uygulaması oluşturmak için:

1. **Video analizi-nesne ve hareket algılama** uygulama şablonunu seçin. Bu şablon, öğreticide kullanılan cihazların cihaz şablonlarını içerir. Şablon, operatörlerin, kameraları izleme ve yönetme gibi görevleri gerçekleştirmek için kullanabileceği örnek panolar içerir.

1. İsteğe bağlı olarak, kolay bir **uygulama adı** seçin. Bu uygulama, Northwind Traders adlı kurgusal bir perakende mağazayı temel alır. Öğretici, *Northwind Traders video analizi* **uygulama adını** kullanır.

    > [!NOTE]
    > Kolay bir **uygulama adı** kullanırsanız, uygulama **URL 'si** için yine de benzersiz bir değer kullanmanız gerekir.

1. Azure aboneliğiniz varsa, **konum** olarak **Dizin**, **Azure aboneliğiniz** ve **Birleşik Devletler** seçin. Aboneliğiniz yoksa, **7 günlük ücretsiz denemeyi** etkinleştirebilir ve gerekli iletişim bilgilerini tamamlayabilirsiniz. Bu öğretici üç cihaz (iki kamera ve bir IoT Edge cihaz kullanır. bu nedenle, ücretsiz deneme sürümünü kullanmıyorsanız kullanım için faturalandırılırsınız.

    Dizinler, abonelikler ve konumlar hakkında daha fazla bilgi için bkz. [uygulama oluşturma hızlı](../articles/iot-central/core/quick-deploy-iot-central.md)başlangıcı.

1. **Oluştur**’u seçin.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Azure IoT Central Uygulama Oluştur sayfası":::

### <a name="retrieve-the-configuration-data"></a>Yapılandırma verilerini alma

Bu öğreticinin ilerleyen kısımlarında IoT Edge ağ geçidini yapılandırdığınızda IoT Central uygulamadan bazı yapılandırma verileri gerekir. IoT Edge cihaz, uygulamasına kaydolmak ve uygulamaya bağlanmak için bu bilgiye ihtiyaç duyuyor.

**Yönetim** bölümünde, **uygulamanızı** seçin ve *scratchpad.txt* DOSYADAKI uygulama **URL** 'sini ve **uygulama kimliğini** bir yere göz önünde oluşturun:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Ekran görüntüsü, Application U R L ve Application g vurgulanarak bir video analizi sayfasının yönetim bölmesini gösterir.":::

**API belirteçlerini** seçin ve **Işleç** rolü Için **lvaedgetoken** adlı yeni bir belirteç oluşturun:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Belirteç oluştur":::

*scratchpad.txt* dosyasındaki belirteci daha sonra bir yere unutmayın. İletişim kutusu kapatıldıktan sonra belirteci yeniden görüntüleyemezsiniz.

**Yönetim** bölümünde **cihaz bağlantısı**' nı seçin ve ardından **SAS-IoT-cihazlar**' ı seçin.

*scratchpad.txt* dosyasındaki cihazların **birincil anahtarını** bir yere unutmayın. IoT Edge cihazı yapılandırırken bu *birincil grup paylaşılan erişim imzası belirtecini* daha sonra kullanırsınız.
