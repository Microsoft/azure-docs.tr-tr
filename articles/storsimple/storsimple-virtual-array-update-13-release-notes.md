---
title: Microsoft Azure StorSimple Virtual Array Update 1,3 sürüm notları | Microsoft Docs
description: Güncelleştirme 1,3 çalıştıran Azure StorSimple Sanal dizisine yönelik kritik açık sorunlar ve çözümleri açıklar.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389528"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Sanal dizisi güncelleştirme 1,3 sürüm notları

Aşağıdaki sürüm notları, Microsoft Azure StorSimple Sanal dizi güncelleştirmelerine yönelik kritik açık sorunları ve çözümlenen sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir. Çözüm gerektiren kritik sorunlar bulundukça eklenir. StorSimple Sanal dizinizi dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Güncelleştirme 1,3, yazılım sürümü 10.0.10319.0 'e karşılık gelir.

> [!IMPORTANT]
> - Güncelleştirme 1,3, kritik bir güncelleştirmedir. En kısa sürede yüklemenizi kesinlikle öneririz.
> - Güncelleştirme 1,3 ' i yalnızca güncelleştirme 1,2 çalıştıran cihazlara yükleyebilirsiniz.
> - Güncelleştirmeler kesintiye uğratan ve cihazınızı yeniden başlatacak. G/ç devam ediyorsa, cihaz kapalı kalma süresini doğurur. Bu güncelleştirmeyi uygulamak için kullanılan paketlere ilişkin ayrıntılı yönergeler için [güncelleştirme 1,3](#download-update-13)' ye gidin.

## <a name="whats-new-in-update-13"></a>Güncelleştirme 1,3 ' deki yenilikler

Bu güncelleştirme aşağıdaki geliştirmeleri içerir: KB4540725

- Aktarım Katmanı Güvenliği (TLS) 1,2 zorunlu bir güncelleştirmedir ve yüklenmesi gerekir. Bu sürümden itibaren TLS 1,2, tüm Azure portal iletişimleri için standart protokol haline gelir.
  
   Aşağıdaki uyarıyı görürseniz, devam etmeden önce cihazdaki yazılımı güncelleştirmeniz gerekir:

   Bir veya daha fazla StorSimple cihazı eski bir yazılım sürümünü çalıştırıyor. TLS 1,2 için kullanılabilen en son güncelleştirme, zorunlu bir güncelleştirmedir ve bu cihazlara hemen yüklenmesi gerekir. TLS 1,2 tüm Azure portal iletişimi için kullanılır ve bu güncelleştirme olmadan cihaz StorSimple hizmetiyle iletişim kuramaz.

- Çöp toplama hata düzeltmeleri, cihaz ve depolama hesabı iki uzak bölgede olduğunda çöp toplama döngüsünün performansını geliştirir.
- Blob zaman aşımları nedeniyle yedekleme hatalarının düzeltilmesi.
- İşletim sistemi/. NET Framework güvenlik yamaları güncelleştirildi:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): Mart 2020 SSU (hizmet yığını güncelleştirmesi)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): Temmuz 2020 toplaması
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): temmuz 2020 .NET Framework güncelleştirmesi

## <a name="download-update-13"></a>Güncelleştirme 1,3 ' yı İndirin

Bu güncelleştirmeyi indirmek için [Microsoft Update Katalog](https://www.catalog.update.microsoft.com/Home.aspx) sunucusuna gıdın ve KB4575898 paketini indirin. Bu paket aşağıdaki paketleri içerir. Paketleri şu sırayla yükler:

1. **KB4540725**, 2020 Mart ' e kadar 2012 R2 Için toplu Windows güncelleştirmeleri içerir. Bu toplamada nelerin dahil olduğu hakkında daha fazla bilgi için [Mart aylık güvenlik toplaması](https://support.microsoft.com/help/4540725)' na gidin.
1. 2020 Temmuz 2012 R2 için toplu Windows güncelleştirmelerini içeren **KB4565541**. Bu toplamada nelerin dahil olduğu hakkında daha fazla bilgi için, [aylık güvenlik toplaması](https://support.microsoft.com/help/4565541)' na gidin.
1. 2020 Temmuz ' ye kadar olan cumulative.NET Framework güncelleştirmelerini içeren **KB4565622**. Bu toplamada nelerin dahil olduğu hakkında daha fazla bilgi için [KB4565622](https://support.microsoft.com/help/4565622)adresine gidin.<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. Cihaz yazılım güncelleştirmelerini içeren **KB3011067**.

KB4575898 indirin ve [Yerel Web Kullanıcı arabirimi aracılığıyla güncelleştirmeyi uygulamak](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)için bu yönergeleri izleyin.

## <a name="known-issues-in-update-13"></a>Güncelleştirme 1,3 ' de bilinen sorunlar
Güncelleştirme 1,3 ' de yeni bir sorun yayınlanmadı. Sürüm tarafından belirtilen tüm sorunlar önceki sürümlerden devredilir. Önceki sürümlerden dahil edilen bilinen sorunların özetini görmek için [güncelleştirme 1,2 ' de bilinen sorunlar](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)bölümüne gidin.

## <a name="next-steps"></a>Sonraki adımlar
KB4575898 indirin ve [Yerel Web Kullanıcı arabirimi aracılığıyla güncelleştirmeyi uygulayın](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Başvurular
Daha eski bir sürüm notuna mi bakıyorsunuz? Şuraya gidin:

- [StorSimple Sanal dizisi güncelleştirme 1,2 sürüm notları](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Sanal dizisi güncelleştirme 1,0 sürüm notları](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Sanal dizisi güncelleştirme 0,6 sürüm notları](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Sanal dizisi güncelleştirme 0,5 sürüm notları](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Sanal dizisi güncelleştirme 0,4 sürüm notları](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Sanal dizisi güncelleştirme 0,3 sürüm notları](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Sanal dizisi güncelleştirme 0,1 ve 0,2 sürüm notları](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Sanal dizisi genel kullanılabilirlik sürüm notları](./storsimple-virtual-array-update-06-release-notes.md)