---
title: Azure Kinect DK bellenimini güncelleştirme
description: Azure Kinect bilgi üretim bellenimi aracını kullanarak Azure Kinect DK cihaz bellenimini güncelleştirmeyi öğrenin.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, bellenim, güncelleştirme, kurtarma
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94356235"
---
# <a name="update-azure-kinect-dk-firmware"></a>Azure Kinect DK bellenimini güncelleştirme

Bu belge, Azure Kinect DK 'de cihaz bellenimini güncelleştirme hakkında rehberlik sağlar.

Azure Kinect DK, bellenimi otomatik olarak güncelleştirmez. Bellenimi, kullanılabilir en son sürüme el ile güncelleştirmek için [Azure Kinect üretici yazılımı aracını](azure-kinect-firmware-tool.md) kullanabilirsiniz.

## <a name="prepare-for-firmware-update"></a>Bellenim güncelleştirmesi için hazırlanma

1. [SDK 'Yı indirin](sensor-sdk-download.md).
2. SDK’yı yükleyin.
3. SDK yüklemesi konumunda (SDK yüklemesi konumu) \araçları \ ' de şunu bulmanız gerekir:

    - AzureKinectFirmwareTool.exe
    - Bellenim klasöründe *AzureKinectDK_Fw_1.5.926614. bin* gibi bir bellenim. bin dosyası.

4. Cihazınızı ana bilgisayara bağlayın ve aynı zamanda güç tasarrufu yapın.

> [!IMPORTANT]
> Üretici yazılımı güncelleştirmesi sırasında USB ve güç kaynağını bağlı tutun. Güncelleştirme sırasında herhangi bir bağlantının kaldırılması, bellenimi bozulmuş bir duruma döndürebilir.

## <a name="update-device-firmware"></a>Cihaz üretici yazılımını güncelleştirme

1. (SDK yüklemesi konumu) \araçları \ klasöründe bir komut istemi açın.
2. Azure Kinect üretici yazılımı aracını kullanarak üretici yazılımını güncelleştirme

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Örnek:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Üretici yazılımı güncelleştirmesi bitene kadar bekleyin. Görüntü boyutuna bağlı olarak birkaç dakika sürebilir.

### <a name="verify-device-firmware-version"></a>Cihaz üretici yazılımı sürümünü doğrulama

1. Üretici yazılımının güncelleştirildiğini doğrulayın.

    `AzureKinectFirmwareTool.exe -q`

2. Aşağıdaki örneği görüntüleyin.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Yukarıdaki çıktıyı görürseniz, belleniminizi güncellenmiştir.

4. Bellenim güncelleştirmesinden sonra, tüm algılayıcıları beklendiği gibi çalıştığını doğrulamak için [Azure Kinect görüntüleyicisini](azure-kinect-viewer.md) çalıştırabilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme

Üretici yazılımı güncelleştirmeleri çeşitli nedenlerle başarısız olabilir. Bir bellenim güncelleştirmesi başarısız olursa, aşağıdaki risk azaltma adımlarını deneyin:

1. Üretici yazılımı güncelleştirme komutunu ikinci kez çalıştırmayı deneyin.

2. Üretici yazılımı sürümünü sorgulayarak cihazın hala bağlı olduğunu onaylayın.        AzureKinectFirmareTool.exe

3. Diğer tümü başarısız olursa, fabrika bellenimine geri dönmek için [Kurtarma](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) adımlarını izleyin ve yeniden deneyin.

Ek sorunlar için bkz. [Microsoft destek sayfaları](./index.yml)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Azure Kinect üretici yazılımı aracı](azure-kinect-firmware-tool.md)