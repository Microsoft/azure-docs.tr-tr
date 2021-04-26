---
title: Azure Percept DK 'yi USB bağlantısı üzerinden güncelleştirme
description: Azure Percept DK 'yi USB bağlantısı üzerinden güncelleştirme hakkında bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887759"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Azure Percept DK 'yi USB bağlantısı üzerinden güncelleştirme

-Air (OTA) güncelleştirmelerinin kullanılması, Dev Kit 'in işletim sistemini ve bellenimini güncel tutmanın en iyi yöntemidir; ancak, güncelleştirme (veya "yanıp sönen"), bir USB bağlantısı üzerinden Dev Kit 'in gerekli olduğu senaryolar vardır:

- Bağlantı veya diğer teknik sorunlar nedeniyle OTA güncelleştirmesi mümkün değil
- Cihazın fabrika durumuna geri sıfırlanması gerekir

Bu kılavuzda, bir USB bağlantısı üzerinden Dev Kit 'in işletim sistemini ve bellenimini başarıyla güncelleştirme hakkında gösterilmektedir.

> [!WARNING]
> Geliştirme kitinizi USB üzerinden güncellemek, cihazdaki AI modelleri ve kapsayıcıları dahil tüm mevcut verileri siler.
>
> Sırayla tüm yönergeleri izleyin. Adımları atlamak Dev Kit 'i kullanılamaz duruma getirebilir.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK
- Wi-Fi özelliğine sahip bir Windows, Linux veya OS X tabanlı ana bilgisayar ve kullanılabilir USB-C veya USB-A bağlantı noktası
- USB-C-USB-kablosu (isteğe bağlı, ayrı olarak satılır)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md) SıRASıNDA oluşturulan SSH oturum açma bilgileri

## <a name="download-software-tools-and-update-files"></a>Yazılım araçlarını ve güncelleştirme dosyalarını indirin

1. [NXP UUU aracı](https://github.com/NXPmicro/mfgtools/releases). **En son sürüm** uuu.exe dosyasını (Windows için) veya uuu dosyasını (Linux Için) **varlıklar** sekmesi altında indirin.

1. [7-zip](https://www.7-zip.org/). Bu yazılım, Ham görüntü dosyasının XZ sıkıştırılmış dosyasından ayıklanması için kullanılacaktır. Uygun. exe dosyasını indirin ve yükleyin.

1. [Güncelleştirme dosyalarını indirin](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Üç derleme yapıtlarının tümünün mevcut olduğundan emin olun:
    - Azure-Percept-DK-*&lt; sürüm numarası &gt;*. RAW. XZ
    - Fast-HAB-fw. RAW
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

1. Ana bilgisayarda komut satırı aracılığıyla kolayca erişim sağlayan bir konumda bir klasör/dizin oluşturun.

1. UıUU aracını (**uuu.exe** veya **uuu**) yeni klasöre kopyalayın.

1. Azure- **Percept-dk-*&lt; sürüm numarası &gt;*. RAW. XZ** üzerine sağ tıklayıp, burada **7-zip** Ayıkla ' yı seçerek **Azure-Percept-dk-*&lt; &gt; sürüm numarası*. RAW** dosyasını sıkıştırılmış dosyadan ayıklayın &gt; .

1. Ayıklanan **Azure-Percept-dk-*&lt; sürüm numarası &gt;*. RAW** dosyasını, **Fast-HAB-fw. RAW**' ı ve **emmc_full.txt** , uuu aracını içeren klasöre taşıyın.

## <a name="update-your-device"></a>Cihazınızı güncelleştirme

1. [Geliştirme setinizdeki SSH](./how-to-ssh-into-percept-dk.md).

1. Sonra, bir Windows komut istemi (**Start**  >  **cmd**) veya Linux terminali açın ve güncelleştirme dosyalarının ve uuu aracının depolandığı klasöre gidin. Bilgisayarınızı bir düzleştirilebilir cihaz alacak şekilde hazırlamak için komut istemine veya terminale aşağıdaki komutu girin:

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Azure Percept Vision cihazının, taşıyıcı panonun USB-C bağlantı noktasından bağlantısını kesin.

1. Sağlanan USB-C kablosunu taşıyıcı panonun USB-C bağlantı noktasına ve ana bilgisayarın USB-C bağlantı noktasına bağlayın. Bilgisayarınızda yalnızca bir USB-A bağlantı noktası varsa, taşıyıcı panosuna ve ana bilgisayara USB-C ' y i USB-bir kablo bağlayın (ayrı olarak satılır).

1. SSH istemcisi isteminde aşağıdaki komutları girin:

    1. Cihazı USB güncelleştirme moduna ayarla:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Cihazı yeniden başlatın. Güncelleştirme yüklemesi başlatılır.

        ```bash
        sudo reboot -f
        ```

1. Diğer komut istemine veya terminale geri gidin. Güncelleştirme tamamlandığında şunları içeren bir ileti görürsünüz ```Success 1    Failure 0``` :

    > [!NOTE]
    > Güncelleştirme sonrasında cihazınız fabrika ayarlarına sıfırlanır ve Wi-Fi bağlantınız ve SSH oturum açma bilgilerinizi kaybedersiniz.

1. Güncelleştirme tamamlandıktan sonra, taşıyıcı panosunu kapatın. USB kablosunu BILGISAYARDAN çıkarın.  

## <a name="next-steps"></a>Sonraki adımlar

Cihazınızı yeniden yapılandırmak için [Azure PERCEPT dk kurulum deneyimiyle](./quickstart-percept-dk-set-up.md) çalışın.