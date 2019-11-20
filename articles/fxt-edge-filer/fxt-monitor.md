---
title: Azure FXT Edge Filni izleme
description: Azure FXT Edge Filer karma depolama önbelleği için donanım durumunu izleme
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254877"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Azure FXT Edge Filer donanım durumunu izleme

Azure FXT Edge Filer karma depolama önbelleği sisteminde, yöneticilerin donanımın nasıl çalıştığını anlamalarına yardımcı olmak için kasaya yerleşik birden çok durum ışığı vardır.

## <a name="system-health-status"></a>Sistem durumu

Önbellek işlemlerini daha yüksek bir düzeyde izlemek için, [Denetim Masası Pano kılavuzunda](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html) açıklanan şekilde, yazılım Denetim Masası **Pano** sayfasını kullanın

## <a name="hardware-status-leds"></a>Donanım durumu LED 'Leri

Bu bölümde, Azure FXT Edge Filer donanımında yerleşik olarak bulunan çeşitli durum ışıkları açıklanmaktadır.

### <a name="hard-drive-status-leds"></a>Sabit sürücü durumu LED 'Leri

![sabit sürücü ön, yatay, belirtme çizgisi etiketleri 2 (sol üst köşe), 1 (sol alt köşe) ve 3 (sağ taraf)](media/fxt-monitor/fxt-drive-callouts.png)

Her sürücü taşıyıcısı iki durum LED 'e sahiptir: etkinlik göstergesi (1) ve bir durum göstergesi (2). 

* Etkinlik LED (1) sürücü kullanımda olduğunda ışıklar.  
* Durum ışığı (2), aşağıdaki tablodaki kodları kullanarak sürücünün koşulunu gösterir.

| Sürücü durumu LED durumu              | Açıklama  |
|-------------------------------------|----------------------------------------------------------|
| Saniyede dakikada bir kez yanıp sönyeşil      | Sürücüyü belirleme *veya* <br> Sürücü kaldırma için hazırlanıyor  |
| Kapalı (açılmamış)                         | Sistem başlatmayı tamamlamadı *veya* <br>Sürücü kaldırılmak üzere hazırlanıyor |
| Yanıp sönmeye yeşil, bir ve kapalı       | Sürücü hatası tahmin edildi   |
| Saniyede yanıp sönyılda dört kez | Sürücü başarısız oldu   |
| Solid yeşil                         | Sürücü çevrimiçi |

Sürücünün (3) sağ tarafı, sürücünün kapasitesi ve diğer bilgilerle etiketlidir.

Sürücü numaraları, sürücüler arasındaki alana yazdırılır. Azure FXT Edge Filsi içinde, 0. sürücü, sol üst sürücüdür ve 1. Sürücü doğrudan onun altında. Numaralandırma bu düzende devam eder. 

![FXT kasadaki bir sabit sürücü bölmesinin fotoğrafı, sürücü numaralarını ve kapasite etiketlerini gösterir](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Sol Denetim bölmesi

Sol ön denetim masasında çeşitli durum göstergeleri (1) ve büyük bir sistem durumu göstergesi (2) bulunur. 

![Sol tarafta 1 etiketleme durumu göstergesi ve sağ taraftaki büyük sistem durumu göstergesi ışığını 2 etiketleyerek sol durum bölmesi](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Denetim Masası durum göstergeleri 

Sol taraftaki durum göstergeleri, bu sistemde bir hata varsa Solid bir TBU ışığı göster. Aşağıdaki tabloda hatalara yönelik olası nedenler ve çözümler açıklanmaktadır. 

Bu çözümleri denemeden sonra hala hata yaşıyorsanız yardım için [desteğe başvurun](fxt-support-ticket.md) . 

| Simge | Açıklama | Hata koşulu | Olası çözümler |
|----------------|---------------|--------------------|----------------------|
| ![sürücü simgesi](media/fxt-monitor/fxt-hd-icon.jpg) | Sürücü durumu | Sürücü hatası | Sürücüde hata olup olmadığını öğrenmek için sistem olay günlüğünü denetleyin veya <br>Uygun çevrimiçi tanılama testini çalıştırın; sistemi yeniden başlatın ve katıştırılmış tanılamayı (ePSA) çalıştırın veya <br>Sürücüler bir RAID dizisinde yapılandırılmışsa, sistemi yeniden başlatın ve konak bağdaştırıcısı yapılandırma yardımcı programı programını girin |
|![sıcaklık simgesi](media/fxt-monitor/fxt-temp-icon.jpg) | Sıcaklık durumu | Isı hatası-Örneğin, bir fan başarısız oldu veya çevresel sıcaklık aralığın dışında | Aşağıdaki adreslenebilir koşulları denetleyin: <br>Soğutma fanı eksik veya başarısız oldu <br>Sistemin kapağı, AIR shkabd, bellek modülü boş veya arka doldurucu ayracı kaldırıldı <br>Çevresel sıcaklık çok yüksek <br>Dış uçak akışı engellemeli |
|![Elektrik simgesi](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrik durumu | Elektrik hatası-Örneğin, Aralık dışı, başarısız PSU veya başarısız bir voltaj Düzenleyicisi olan voltaj |  Belirli bir sorun için sistem olay günlüğünü veya sistem iletilerini denetleyin. Bir PSU sorunu varsa, PSU durumunu yeniden denetleyin ve gerekirse PSU 'yı yeniden takın. | 
|![Bellek simgesi](media/fxt-monitor/fxt-memory-icon.jpg) | Bellek durumu | Bellek hatası | Hatalı belleğin konumu için sistem olay günlüğünü veya sistem iletilerini denetleyin; bellek modülünü yeniden takın. |
|![PCIe simgesi](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe durumu | PCIe kartı hatası | Sistemi yeniden başlatın; PCIe kart sürücülerini güncelleştirin; kartı yeniden yükleme |


### <a name="system-health-status-indicator"></a>Sistem durumu göstergesi

Sol denetim bölmesinin sağ tarafındaki büyük aydınlatmalı düğme, sistem durumunun genel durumunu gösterir ve ayrıca sistem KIMLIĞI modunda bir birim Bulucu ışığı olarak kullanılır.

Sistem durumu ve KIMLIK düğmesine basarak sistem KIMLIĞI modu ve sistem durumu modu arasında geçiş yapın.

|Sistem durumu durumu | Koşul |
|-------------------------------------------|-----------------------------------------------|
| Düz mavi | Normal işlem: Sistem açık, normal çalışan ve sistem KIMLIĞI modu etkin değil. <br/>Sistem KIMLIĞI moduna geçmek istiyorsanız sistem durumu ve KIMLIĞI düğmesine basın. |
| Yanıp sönen mavi | Sistem KIMLIĞI modu etkin. Sistem durumu moduna geçmek istiyorsanız sistem durumu ve sistem KIMLIĞI düğmesine basın. |
| Solid, | Sistem, başarısız olarak güvenli modda. Sorun devam ederse, [Microsoft Müşteri Hizmetleri ve destek 'e başvurun](fxt-support-ticket.md). |
| Yanıp sönen bir bu | Sistem hatası. Belirli hata iletileri için sistem olay günlüğünü denetleyin. Sistem üretici yazılımı ve sistem bileşenlerini izleyen aracılar tarafından oluşturulan olay ve hata iletileri hakkında daha fazla bilgi için qrl.dell.com adresindeki hata kodu arama sayfasına bakın. |


