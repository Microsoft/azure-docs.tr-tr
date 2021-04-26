---
title: Microsoft Azure StorSimple Virtual Array Update 1,2 sürüm notları | Microsoft Docs
description: Güncelleştirme 1,2 çalıştıran StorSimple Sanal dizisine yönelik kritik açık sorunlar ve çözümleri açıklar.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960213"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Sanal dizisi güncelleştirme 1,2 sürüm notları

Aşağıdaki sürüm notları, Microsoft Azure StorSimple Sanal dizi güncelleştirmelerine yönelik kritik açık sorunları ve çözümlenen sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir. Çözüm gerektiren kritik sorunlar bulundukça eklenir. StorSimple Sanal dizinizi dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Güncelleştirme 1,2, yazılım sürümü **10.0.10311.0**'e karşılık gelir.

> [!IMPORTANT]
> - Güncelleştirmeler kesintiye uğratan ve cihazınızı yeniden başlatacak. G/ç devam ediyorsa, cihaz kapalı kalma süresini doğurur. Bu güncelleştirmeyi uygulamak için kullanılan paketlere ilişkin ayrıntılı yönergeler için [güncelleştirme 1,2](#download-update-12)' ye gidin.
> - Güncelleştirme 1,2, yalnızca cihazınız güncelleştirme 1,0 veya 1,1 çalıştırıyorsa Azure portal aracılığıyla kullanılabilir.

## <a name="whats-new-in-update-12"></a>Güncelleştirme 1,2 ' deki yenilikler

Bu güncelleştirme aşağıdaki hata düzeltmelerini içerir:

- Silinen dosyaları işlerken iyileştirilmiş dayanıklılık.
- Yedekleme, geri yükleme, bulut okuma ve otomatik Space geri kazanma 'da azaltılmış hatalara karşı, kod başlangıç yolundaki özel durumların işlenmesi geliştirildi.

## <a name="download-update-12"></a>Güncelleştirme 1,2 ' yı İndirin

Bu güncelleştirmeyi indirmek için [Microsoft Update Katalog](https://www.catalog.update.microsoft.com/Home.aspx) sunucusuna gıdın ve KB4502035 paketini indirin. Bu paket aşağıdaki paketleri içerir:

 - 2019 Nisan 2012 R2 için toplu Windows güncelleştirmelerini içeren **KB4493446** . Bu toplamasına dahil olanlar hakkında daha fazla bilgi için [Nisan aylık güvenlik toplaması](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)' na gidin.
 - Microsoft Update tek başına bir paket dosyası WindowsTH-KB3011067-x64 olan **KB3011067** . Bu dosya, cihaz yazılımını güncelleştirmek için kullanılır.

KB4502035 indirin ve [Yerel Web Kullanıcı arabirimi aracılığıyla güncelleştirmeyi uygulamak](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)için bu yönergeleri izleyin.

## <a name="issues-fixed-in-update-12"></a>Güncelleştirme 1,2 ' de düzeltilen sorunlar

Aşağıdaki tabloda, bu sürümde düzeltilen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun |
| --- | --- | --- |
| 1 |Silme| Yazılımın önceki sürümlerinde, Dosyalar silindiğinde bile cihazın kullanımı değişmediyse bir sorun oluştu. Bu sorun bu sürümde düzeltilmiştir. Kod yolu, silinen dosyalar işlenirken daha dayanıklı yapıldı.|
| 2 |Özel durum işleme| Yazılımın önceki sürümlerinde, sistem yeniden başlatıldıktan sonra yedeklemelerdeki hatalara neden olabilecek, geri yükleme, buluttan okuma ve otomatik Space geri kazanma ile ilgili bir sorun oluştu. Bu sürüm, başlangıç yolunda özel durumların nasıl işlendiği gibi değişiklikler içerir.|

## <a name="known-issues-in-update-12"></a>Güncelleştirme 1,2 ' de bilinen sorunlar

Güncelleştirme 1,2 ' de yeni bir sorun yayınlanmadı. Sürüm tarafından belirtilen tüm sorunlar önceki sürümlerden devredilir. Önceki sürümlerden dahil edilen bilinen sorunların özetini görmek için [güncelleştirme 1,1 ' de bilinen sorunlar](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)bölümüne gidin.

## <a name="next-steps"></a>Sonraki adımlar

KB4502035 indirin ve [Yerel Web Kullanıcı arabirimi aracılığıyla güncelleştirmeyi uygulayın](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Başvurular

Daha eski bir sürüm notuna mi bakıyorsunuz? Şuraya gidin:
* [StorSimple Sanal dizisi güncelleştirme 1,1 sürüm notları](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 1,0 sürüm notları](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,6 sürüm notları](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,5 sürüm notları](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,4 sürüm notları](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,3 sürüm notları](storsimple-ova-update-03-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,1 ve 0,2 sürüm notları](storsimple-ova-update-01-release-notes.md)
* [StorSimple Sanal dizisi genel kullanılabilirlik sürüm notları](./storsimple-virtual-array-update-06-release-notes.md)