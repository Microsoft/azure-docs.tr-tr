---
title: Azure Izleyici çalışma kitabı tabanlı Öngörüler sorunlarını giderme
description: Azure Izleyici çalışma kitabı tabanlı Öngörüler için Azure Key Vault, Azure CosmosDB, Azure depolama ve Redsıs gibi hizmetler için sorun giderme kılavuzu sağlar.
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: a823b5e2ae31e62ff6d876f0b3468953c0ed9c35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587151"
---
# <a name="troubleshooting-workbook-based-insights"></a>Çalışma kitabı tabanlı Öngörüler sorunlarını giderme

Bu makale, Azure Izleyici çalışma kitabı tabanlı Öngörüler kullanırken karşılaşabileceğiniz bazı yaygın sorunların tanılanması ve sorun gidermede size yardımcı olur.


## <a name="why-can-i-only-see-200-resources"></a>Neden yalnızca 200 kaynaklarını görebilirim?

Seçilen kaynakların sayısı, seçilen aboneliklerin sayısından bağımsız olarak 200 sınırına sahiptir.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Panoda son sabitlenmiş bir kutucuğa tıkladığımda ne olur?

* Kutucukta herhangi bir yere tıkladığınızda, sizi kutucuğun sabitlendiği sekmeye götürür. Örneğin, "genel bakış" sekmesine bir grafik sabitlemeyi yaparsanız, panoda bu kutucuğa tıkladığınızda bu varsayılan görünümü açılır. ancak, kendi kayıtlı kopyaınızdan bir grafiği sabitleyebilir, bu, kaydettiğiniz kopyanın görünümünü açar.
* Başlığın sol üst kısmındaki filtre simgesi "kutucuk ayarlarını yapılandır" sekmesini açar.
* Sağ üst köşedeki elips simgesi size "başlık verilerini özelleştirme", "özelleştirme", "Yenile" ve "Panodan kaldır" seçeneklerini verecektir.

## <a name="what-happens-when-i-save-a-workbook"></a>Çalışma kitabını kaydettiğimde ne olur?

* Bir çalışma kitabını kaydettiğinizde, düzenlemelerinizle çalışma kitabının yeni bir kopyasını oluşturmanıza ve başlığı değiştirmenize olanak sağlar. Kaydetme, çalışma kitabının üzerine yazmaz, geçerli çalışma kitabı her zaman varsayılan görünüm olacaktır.
* **Kaydedilmemiş** bir çalışma kitabı yalnızca varsayılan görünümüdür.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Portalda tüm aboneliklerimi neden görmüyorum?

Portal, yalnızca Portal başlatıldığında seçili abonelikler için verileri gösterir. Hangi aboneliklerin seçili olduğunu değiştirmek için sağ üst kısımdaki ' a gidin ve filtre simgesi olan not defterine tıklayın. Bu seçenek **Dizin + abonelikler** sekmesini gösterecektir.

![Dizin + abonelik](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Zaman aralığı nedir?

Zaman aralığı, belirli bir zaman çerçevesinde verileri gösterir. Örneğin, zaman aralığı 24 saat ise, son 24 saat içindeki verileri gösterir.

## <a name="what-is-time-granularity-time-grain"></a>Zaman ayrıntı düzeyi nedir (zaman dilimi)

Zaman ayrıntı düzeyi iki veri noktası arasındaki zaman farkındır. Örneğin, zaman dilimi 1 saniye olarak ayarlandıysa, her saniye ölçüm toplanır.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Çalışma kitaplarının herhangi bir bölümünü bir panoya sabitledikten sonra zaman ayrıntı düzeyi nedir

Varsayılan zaman ayrıntı düzeyi otomatik olarak ayarlanmıştır, şu anda şu anda değiştirilemez.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Panodaki çalışma kitabı adımının TimeSpan/Time aralığını değiştirmek Nasıl yaparım?

Varsayılan olarak, pano kutucuğlarınızdaki TimeSpan/Time aralığı 24 saat olarak ayarlanır, bunu değiştirmek için sağ üst köşedeki üç noktaya tıklayın, **kutucuk verilerini Özelleştir**' i seçin, "başlık düzeyinde Pano zaman ayarlarını geçersiz kıl" kutusunu işaretleyin ve ardından açılan menüyü kullanarak bir zaman aralığı seçin.  

![Kutucuğun sağ köşesindeki üç noktayı seçin ve bu verileri Özelleştir ' i seçin](./media/storage-insights-overview/fqa-data-settings.png)

![Kutucuğu Yapılandır ayarlarını, TimeSpan/Time aralığını değiştirmek için TimeSpan açılan listesini seçin](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Çalışma kitabının başlığını veya bir panoya sabitlediğim çalışma kitabı adımını değiştirme Nasıl yaparım?

Bir panoya sabitlenmiş çalışma kitabının veya çalışma kitabı adımının başlığı, çalışma kitabındaki aynı adı korur. Başlığı değiştirmek için çalışma kitabının kendi kopyasını kaydetmeniz gerekir. Ardından, Kaydet 'e tıklamadan önce çalışma kitabını adlandırın.

![Çalışma kitabının bir kopyasını kaydetmek ve adını değiştirmek için üst kısımdaki Kaydet ' i seçin](./media/storage-insights-overview/fqa-change-workbook-name.png)

Kaydettiğiniz çalışma kitabınızdaki bir adımın adını değiştirmek için, adım altında Düzenle ' yi seçin ve ardından Ayarlar ' ın altındaki dişli ' ı seçin.

![Çalışma kitabı adımının altındaki Düzenle ](./media/storage-insights-overview/fqa-edit.png)
 ![ ' yi seçerek, adım adını değiştirebilmek için ayarların altındaki dişli ' ı seçin.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Sonraki adımlar

Çalışma kitaplarının destekleyecek şekilde tasarlanan senaryolar hakkında daha fazla bilgi edinin, var olan raporların nasıl yazıldığını ve özelleştirildiğini ve [Azure izleyici çalışma kitaplarını kullanarak etkileşimli raporlar oluşturma](../visualize/workbooks-overview.md)konusunu gözden geçirin.
