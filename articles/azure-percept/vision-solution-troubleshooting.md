---
title: Azure Percept Vision ve Vision modülleriyle ilgili sorunları giderme
description: Vision AI prototipleme deneyimlerinde bulunan daha yaygın sorunlardan bazıları için sorun giderme ipuçları alın
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074701"
---
# <a name="vision-solution-troubleshooting"></a>Vision çözüm sorunlarını giderme

Azure Percept Studio 'da kod içermeyen Vision çözümlerinin sorunlarını giderme hakkında bilgi için aşağıdaki kılavuza bakın.

## <a name="delete-a-vision-project"></a>Bir Vision projesini silme

1. https://www.customvision.ai/projects öğesine gidin.

1. Silmek istediğiniz projenin üzerine gelin ve projeyi silmek için çöp kutusu simgesine tıklayın.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Silme simgesi vurgulanmış Özel Görüntü İşleme Projeler sayfası.":::

## <a name="check-which-modules-are-on-a-device"></a>Bir cihazda hangi modüllerin olduğunu denetleme

1. [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) gidin.

1. **IoT Hub** simgesine tıklayın.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="IoT Hub simgesi vurgulanmış şekilde Azure portal giriş sayfası." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Hedef cihazınızın bağlı olduğu IoT Hub seçin.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT Hub 'Ları listesi.":::

1. **IoT Edge** ' yi seçin ve cihaz **kimliği** sekmesinin altındaki cihazınıza tıklayın.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge giriş sayfası.":::

1. Cihaz modülleriniz **modüller** sekmesinin altında listelenecektir.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Modüller sekmesi içeriğini gösteren seçili cihaz için IoT Edge sayfası." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Cihazı silme

1. [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) gidin.

1. **IoT Hub** simgesine tıklayın.

1. Hedef cihazınızın bağlı olduğu IoT Hub seçin.

1. **IoT Edge** ' yi seçin ve hedef cihaz kimliğinizin yanındaki kutuyu işaretleyin. Cihazınızı silmek için çöp kutusu simgesine tıklayın.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="IoT Edge giriş sayfasında vurgulanmış simgesi Sil.":::

## <a name="eye-module-troubleshooting-tips"></a>Göz modülü sorun giderme ipuçları

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Azureeyemodule çalışma zamanı durumunu denetleyin

**Webstreammodule** ile ilgili bir sorun varsa, ınkapsayan Vision modelini işleyen **azureeyemodule**'un çalıştığından emin olun. Çalışma zamanı durumunu denetlemek için [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) gidin ve **tüm kaynaklar**  ->  **\<your IoT hub>**  ->  **IoT Edge** gidin  ->  **\<your device ID>** . Tüm yüklü modüllerin çalışma zamanı durumunu görmek için **modüller** sekmesine tıklayın.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Cihaz modülü çalışma zamanı durumu ekranı." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

**Azureeyemodule** çalışma zamanı durumu **çalışıyor** olarak listelenmiyorsa, **modülleri ayarla**  ->  **azureeyemodule**' e tıklayın. **Modül ayarları** sayfasında, **istenen durumu** **çalışıyor** olarak ayarlayın ve **Güncelleştir**' e tıklayın.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Modül ayarı yapılandırma ekranı.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>TelemetryIntervalNeuralNetworkMs Güncelleştir

Aşağıdaki Count kısıtlama hatasıyla karşılaşırsanız, azureeyemodule Module ikizi ayarlarındaki TelemetryIntervalNeuralNetworkMs değerinin güncellenmesi gerekir.

|Hata İletisi|
|------|
|' Xxxxxxxxx ' ıothub üzerindeki toplam ileti sayısı ayrılan kotayı aştı. İzin verilen en fazla ileti sayısı: ' 8000 ', geçerli ileti sayısı: ' XXXX '. Gönderme ve alma işlemleri, sonraki UTC gününe kadar bu hub için engellenir. Kotayı artırmak için bu hub için birimleri artırmayı göz önünde bulundurun.|

TelemetryIntervalNeuralNetworkMs, sinir ağından iletilerin (milisaniye olarak) ne sıklıkla gönderileceğini belirler. Azure abonelikleriniz, abonelik katmanınıza bağlı olarak günde sınırlı sayıda ileti alır. Çok fazla ileti gönderilmesi nedeniyle kendinizi kilitlediyseniz, daha yüksek bir sayıya yükseltin. 12000 (her 12 saniyede bir kez) size, ücretsiz abonelik için 8000 ileti sınırı altında olan her gün yaklaşık bir yaklaşık 7200 ileti verecektir.

TelemetryIntervalNeuralNetworkMs değerini güncelleştirmek için şu adımları izleyin:

1. [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) oturum açın ve **tüm kaynakları** açın.

1. **Tüm kaynaklar** sayfasında, kurulum deneyimi sırasında devkit 'e sağlanan IoT Hub adına tıklayın.

1. IoT Hub sayfasının sol tarafında **otomatik cihaz yönetimi** altında **IoT Edge** ' e tıklayın. IoT Edge cihazlar sayfasında, devkit 'in cihaz KIMLIĞINI bulun. IoT Edge cihaz sayfasını açmak için devkit 'in cihaz KIMLIĞINE tıklayın.

1. **Modüller** sekmesinin altındaki **azureeyemodule** öğesini seçin.

1. Azureeyemodule sayfasında **modül kimliği ikizi**' nı açın.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Modül sayfasının ekran görüntüsü." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Aşağı kaydırarak **Özellikler**' e gidin. "Running" ve "Logging" özelliklerinin Şu anda etkin olmadığına unutmayın.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Modül ikizi özelliklerinin ekran görüntüsü." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. **TelemetryIntervalNeuralNetworkMs** değerini istediğiniz şekilde güncelleştirin ve **Kaydet** simgesine tıklayın.

## <a name="view-device-rtsp-video-stream"></a>Cihaz RTSP video akışını görüntüle

Cihazınızın RTSP video akışını [Azure Percept Studio](./how-to-view-video-stream.md) veya [VLC medya yürütücüsü](https://www.videolan.org/vlc/index.html)' nde görüntüleyin.

VLC Media Player 'da RTSP akışını açmak için **medya**  ->  **açık ağ akışı**  ->  **RTSP://[aygıt IP adresi]/Sonuç** sayfasına gidin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Percept DK sorunlarını giderme hakkında daha fazla bilgi için bkz. [genel sorun giderme kılavuzu](./troubleshoot-dev-kit.md) .