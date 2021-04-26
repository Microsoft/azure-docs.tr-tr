---
title: Azure Percept DK-AIR (OTA) üzerinde güncelleştirme
description: Azure Percept DK 'de hava (OTA) güncelleştirmelerini nasıl alacağınızı öğrenin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063126"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Azure Percept DK-AIR (OTA) üzerinde güncelleştirme

IoT Hub için cihaz güncelleştirmesiyle Azure Percept DK 'nin (OTA) Azure DK 'nin taşıyıcı panosunun işletim sistemini ve bellenimini güncelleştirme hakkında bilgi edinmek için bu kılavuzu izleyin.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md): Dev Kit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve geliştirme setinizi IoT Hub bağladınız
- [IoT Hub için cihaz güncelleştirmesi başarıyla yapılandırıldı](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Güncelleştirme dosyanızı ve bildirim dosyanızı içeri aktarın

> [!NOTE]
> Güncelleştirmeyi zaten içeri aktardıysanız, doğrudan **bir cihaz güncelleştirme grubu oluşturmaya** geçebilirsiniz.

1. [Azure Percept cihazınız için uygun bildirim dosyasını (. JSON) ve güncelleştirme dosyasını (. SWU) indirin](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Azure Percept cihazınız için kullandığınız Azure IoT Hub gidin. Sol taraftaki menü panelinde, **otomatik cihaz yönetimi** altında **cihaz güncelleştirmeleri** ' ni seçin.

1. Ekranın üst kısmında birkaç sekme görürsünüz. **Güncelleştirmeler** sekmesini seçin.

1. **Dağıtmaya hazırlanma** üst bilgisinin altında **+ yeni güncelleştirme al** ' ı seçin.

1. Bildirim dosyasını **Içeri aktar** ' ın altındaki kutulara tıklayın ve bildirim dosyanızı (. JSON) ve güncelleştirme dosyasını (. SWU) seçmek Için **dosyaları Güncelleştir** ' i seçin.

1. **Bir depolama kapsayıcısı seçin** altında klasör simgesini veya metin kutusunu seçin ve uygun depolama hesabını seçin. Zaten bir depolama kapsayıcısı oluşturduysanız, onu yeniden kullanabilirsiniz. Aksi takdirde, OTA güncelleştirmeleri için yeni bir depolama kapsayıcısı oluşturmak üzere **+ kapsayıcı** ' yı seçin. Kullanmak istediğiniz kapsayıcıyı seçin ve **Seç**' e tıklayın.

1. İçeri aktarma işlemini başlatmak için **Gönder** ' i seçin. Görüntü boyutu nedeniyle gönderim işlemi 5 dakikaya kadar sürebilir.

    > [!NOTE]
    > Seçili depolama kapsayıcısına erişmek için bir çapraz kaynak Isteği (CORS) kuralı eklemeniz istenebilir. **Kural Ekle '** yi seçin ve devam etmek için yeniden deneyin.

1. İçeri aktarma işlemi başladığında, **cihaz güncelleştirmeleri** sayfasının **içeri aktarma geçmişi** sekmesine yönlendirilirsiniz. İçeri aktarma işlemi tamamlandığında ilerlemeyi izlemek için **Yenile** ' ye tıklayın. Güncelleştirme boyutuna bağlı olarak, bu işlem birkaç dakika veya daha uzun sürebilir (yoğun saatlerde içeri aktarma hizmeti 1 saate kadar sürebilir).

1. **Durum** sütunu içeri aktarmanın başarılı olduğunu gösteriyorsa, **dağıtmaya hazırlanma** sekmesini seçin ve **Yenile**' ye tıklayın. Artık içeri aktarılan güncelleştirmenizi listede görmeniz gerekir.

## <a name="create-a-device-update-group"></a>Cihaz güncelleştirme grubu oluştur

IoT Hub cihaz güncelleştirmesi, belirli Azure Percept DKs gruplarındaki bir güncelleştirmeyi hedefetmenize olanak tanır. Bir grup oluşturmak için, Azure IoT Hub 'deki hedef cihaz kümesine bir etiket eklemeniz gerekir.

> [!NOTE]
> Zaten bir grup oluşturduysanız, bir sonraki bölüme atlayabilirsiniz.

Grup etiketi gereksinimleri:

- Etiketinize, ayrılmış bir değer olan "kategorilere ayrılmamış" hariç herhangi bir değer ekleyebilirsiniz.
- Etiket değeri 255 karakteri aşamaz.
- Etiket değeri yalnızca şu özel karakterleri içerebilir: ".", "-", "_", "~".
- Etiket ve grup adları büyük/küçük harfe duyarlıdır.
- Bir cihazda yalnızca bir etiket olabilir. Cihaza eklenen sonraki Etiketler önceki etiketi geçersiz kılar.
- Bir cihaz yalnızca bir gruba ait olabilir.

1. Cihazınıza bir etiket ekleyin:

    1. Sol gezinti bölmesindeki **IoT Edge** , Azure Percept dk 'nizi bulun ve **cihaz ikizi** sayfasına gidin.

    1. Aşağıda gösterildiği gibi IoT Hub Tag değeri için yeni bir **cihaz güncelleştirmesi** ekleyin ( ```<CustomTagValue>``` etiket değeri/adı, örneğin, AzurePerceptGroup1). Device ikizi [JSON belge etiketleri](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)hakkında daha fazla bilgi edinin.

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. **Kaydet** ' e tıklayın ve tüm biçimlendirme sorunlarını çözün.

1. Mevcut bir Azure IoT Hub etiketi seçerek bir grup oluşturun:

    1. Azure IoT Hub sayfanıza geri gidin.

    1. Sol taraftaki menü panelinde **otomatik cihaz yönetimi** altında **cihaz güncelleştirmeleri** ' ni seçin.

    1. **Gruplar** sekmesini seçin. Bu sayfada cihaz güncelleştirmesine bağlı Gruplandırılmamış cihazların sayısı görüntülenir.

    1. Yeni bir grup oluşturmak için **+ Ekle** ' yi seçin.

    1. Listeden bir IoT Hub etiketi seçin ve **Gönder**' e tıklayın.

    1. Grup oluşturulduktan sonra, güncelleştirme uyumluluğu grafiği ve gruplar listesi güncelleşmeyecektir. Grafik, farklı uyumluluk durumlarında cihaz sayısını gösterir: **en son güncelleştirmede**, **yeni güncelleştirmeler var**, **devam etmekte olan güncelleştirmeler** ve **henüz gruplandırılmadı**.

## <a name="deploy-an-update"></a>Güncelleştirme dağıtma

1. Yeni oluşturulan grubunuzu, **kullanılabilir güncelleştirmeler** altında listelenmiş yeni bir güncelleştirmeyle görmeniz gerekir (bir kez yenilemeniz gerekebilir). Güncelleştirmeyi seçin.

1. Hedef cihaz grubu olarak doğru cihaz grubunun seçili olduğunu onaylayın. Dağıtımınız için bir **Başlangıç tarihi** ve **Başlangıç saati** seçip **dağıtım oluştur**' a tıklayın.

    > [!CAUTION]
    > Başlangıç zamanının geçmişte ayarlanması dağıtımı hemen tetikler.

1. Uyumluluk grafiğini kontrol edin. Güncelleştirmenin devam etmekte olduğunu görmeniz gerekir.

1. Güncelleştirmeniz tamamlandıktan sonra, uyumluluk grafiğiniz yeni güncelleştirme durumunuzu yansıtır.

1. **Cihaz güncelleştirmeleri** sayfasının en üstündeki **dağıtımlar** sekmesini seçin.

1. Dağıtım ayrıntılarını görüntülemek için dağıtımınızı seçin. **Durum** **başarılı** olana kadar **Yenile** ' ye tıklamanız gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

Geliştirme Seti artık başarıyla güncelleştirildi. Geliştirme setinizdeki geliştirme ve işleme devam edebilirsiniz.