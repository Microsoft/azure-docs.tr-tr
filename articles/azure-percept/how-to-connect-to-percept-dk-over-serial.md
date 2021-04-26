---
title: Seri üzerinden Azure Percept DK 'ye bağlanma
description: PuTTY ve USB-TTL seri kablosuyla Azure Percept DK ile bir seri bağlantı ayarlamayı öğrenin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663818"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Seri üzerinden Azure Percept DK 'ye bağlanma

[Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)aracılığıyla Azure Percept dk ile bir seri bağlantı kurmak için aşağıdaki adımları izleyin.

> [!WARNING]
> Devkit 'i, Extreme hata durumları dışında (örneğin, cihazınızı bricked) seri üzerinden **bağlamayı denemeyin** . Seri kablosunu bağlamak için taşıyıcı Pano kasasının ayrılması çok zordur ve Wi-Fi anten kablolarını bozacaktır.

## <a name="prerequisites"></a>Önkoşullar

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Konak bilgisayar
- Azure Percept DK
- [USB 'den TTL 'ye seri kablo](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB 'den TTL 'ye seri kablo.":::

## <a name="initiate-the-serial-connection"></a>Seri bağlantıyı başlatma

1. Taşıyıcı panonuz bir 80/20 demiryolu 'e bağlıysa, onaltılık anahtarı (devkit karşılama kartına dahil) kullanarak bu dosyayı demiryolu 'ten kaldırın.

1. Taşıyıcı Pano kasasının altındaki screws 'yi kaldırın ve ana kartı ayıklayın.

    > [!WARNING]
    > Ana kartın kaldırılması Wi-Fi anten kablolarını bozacaktır. Cihazınızı kurtarmak için son çare olmadığı müddetçe seri bağlantısına devam **etme** .

1. Heatsink 'ı kaldırın.

1. Atlatıcı panosunu GıO PIN 'lerden kaldırın.

    > [!TIP]
    > Anahtar panosunun kaldırılmadan önce yönünü aklınızda edin. Örneğin, bir ok çizin veya bir etiketi, başvuruya ait bir başvuruya doğru işaret eden atlatıcı panosuna ekleyin. Atlatıcı panosu anahtarlanır ve taşıyıcı panonuzu yeniden eklendiğinde yanlışlıkla geriye doğru bağlanmış olabilir.

1. [USB-TTL seri kablosunu](https://www.adafruit.com/product/954) aşağıda gösterildiği gibi ana karttaki GIO PIN lerine bağlayın. Lütfen kırmızı tel bağlanmadığını unutmayın.

    - Siyah kabloyu (iğne) 6 pin 'e bağlayın.
    - Beyaz kablosunu (RX) 8 pin 'e bağlayın.
    - Yeşil kabloyu (TX) 10 pin 'e bağlayın.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Taşıyıcı Pano seri PIN bağlantıları.":::

1. Devkit 'i kapatıp seri kablonun USB tarafını bilgisayarınıza bağlayın.

1. Windows 'ta **Başlat**  ->  **Windows Update ayarlar**' a gidin,  ->  **isteğe bağlı güncelleştirmeler**  ->  **sürücü güncelleştirmelerini** görüntüleyin. Listeden bir seri USB güncelleştirmesine bakın, yanındaki kutuyu işaretleyin ve **indir ve yükle**' ye tıklayın.  

1. Sonra, Windows Device Manager açın (Device Manager **başlatın**  ->  ). **Bağlantı noktaları** ' na gidin ve **Özellikler**' i açmak için **USB** ' ye tıklayın. Cihazınızın bağlı olduğu COM bağlantı noktasını aklınızda edin.

1. **Bağlantı noktası ayarları** sekmesine tıklayın. **Saniye başına bitlerin** 115200 olarak ayarlandığından emin olun.

1. PuTTY’yi açın. Aşağıdaki girin ve seri aracılığıyla devkit 'e bağlanmak için **Aç** ' a tıklayın:

    1. Seri çizgisi: COM [bağlantı noktası #]
    1. Hız: 115200
    1. Bağlantı türü: seri

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Seri parametreleri seçili olan PuTTY oturum penceresi.":::

## <a name="next-steps"></a>Sonraki adımlar

[USB Ile TTL seri kablosuyla](https://www.adafruit.com/product/954)seri hale getirilmemiş bir cihazı güncelleştirmek için lütfen standart olmayan durumlar için USB güncelleştirme kılavuzuna bakın.

[comment]: # (Kullanılabilir olduğunda USB güncelleştirme kılavuzuna bağlantı ekleyin.)