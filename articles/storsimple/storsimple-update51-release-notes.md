---
title: StorSimple 8000 serisi güncelleştirme 5,1 sürüm notları
description: StorSimple 8000 serisi güncelleştirme 5,1 için yeni özellikleri, sorunları ve geçici çözümleri açıklar.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657578"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 serisi güncelleştirme 5,1 sürüm notları

## <a name="overview"></a>Genel Bakış

Aşağıdaki sürüm notları, yeni özellikleri anlatmaktadır ve StorSimple 8000 serisi güncelleştirme 5,1 için kritik açık sorunları belirler. Ayrıca, bu sürüme dahil olan StorSimple yazılım güncelleştirmelerinin bir listesini içerir.

Güncelleştirme 5,1, güncelleştirme 5 çalıştıran tüm StorSimple cihazlarına uygulanabilir. 5 ' ten daha düşük bir sürüm kullanıyorsanız, önce güncelleştirme 5 ' i uygulayın, sonra 5,1 uygulayın. Güncelleştirme 5,1 ile ilişkili cihaz sürümü 6.3.9600.17885.

Güncelleştirmeyi StorSimple çözümünüze dağıtmadan önce sürüm notlarında yer alan bilgileri gözden geçirin.

> [!IMPORTANT]
>
> * Güncelleştirme 5,1, zorunlu bir güncelleştirmedir ve hemen yüklenmesi gerekir. Daha fazla bilgi için bkz. [güncelleştirme 5,1 nasıl uygulanır](storsimple-8000-install-update-51.md).
> * Güncelleştirme 5,1 yalnızca güvenlik güncelleştirmelerine sahiptir. Bu güncelleştirmenin yüklenmesi yaklaşık 30 dakika sürer. Cihazınızın çalışmasını sağlamak için güncelleştirme 5,1 ' i uygulamanızı kesinlikle öneririz.
> * Güncelleştirmelerin aşamalı olarak piyasaya sürültiğimiz için yeni sürümler için güncelleştirmeleri hemen göremeyebilirsiniz. Birkaç gün bekleyin ve sonra bu güncelleştirmeler yakında kullanılabilir hale gelecağından güncelleştirmeleri yeniden tarayın.

## <a name="whats-new-in-update-51"></a>Güncelleştirme 5,1 ' deki yenilikler

Güncelleştirme 5,1 ' de aşağıdaki temel geliştirmeler ve hata düzeltmeleri yapılmıştır:

* **Tls 1,2** -bu StorSimple güncelleştirmesi tüm istemcilerde TLS 1,2 ' i zorlayacaktır. Bu, tüm StorSimple 8000 serisi cihazlar için zorunlu bir güncelleştirmedir.

   Aşağıdaki uyarıyı görürseniz, devam etmeden önce cihazdaki yazılımı güncelleştirmeniz gerekir:

   Bir veya daha fazla StorSimple cihazı eski bir yazılım sürümünü çalıştırıyor. TLS 1,2 için kullanılabilen en son güncelleştirme, zorunlu bir güncelleştirmedir ve bu cihazlara hemen yüklenmesi gerekir. TLS 1,2 tüm Azure portal iletişimi için kullanılır ve bu güncelleştirme olmadan cihaz StorSimple hizmetiyle iletişim kuramaz.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Önceki sürümlerden güncelleştirme 5,1 ' de bilinen sorunlar

Güncelleştirme 5,1 ' de bilinen yeni bir sorun yoktur. Güncelleştirme 5,1 ' ye önceki sürümlerden aktarılan sorunların listesi için [güncelleştirme 3 sürüm notları](storsimple-update3-release-notes.md#known-issues-in-update-3)' na gidin.

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Güncelleştirme 5,1 ' de StorSimple Cloud Appliance güncelleştirmeler

Bu güncelleştirme StorSimple Cloud Appliance uygulanamıyor (sanal cihaz olarak da bilinir). Yeni bulut gereçlerinin güncelleştirme 5,1 görüntüsü kullanılarak oluşturulması gerekir. StorSimple Cloud Appliance oluşturma hakkında daha fazla bilgi için, [StorSimple Cloud Appliance Dağıt ve Yönet](storsimple-8000-cloud-appliance-u2.md)' e gidin.

## <a name="next-step"></a>Sonraki adım

StorSimple cihazınıza [güncelleştirme 5,1](storsimple-8000-install-update-51.md) ' i nasıl yükleyeceğinizi öğrenin.
