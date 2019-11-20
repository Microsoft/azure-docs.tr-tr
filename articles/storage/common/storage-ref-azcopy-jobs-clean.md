---
title: AzCopy işleri temizle | Microsoft Docs
description: Bu makale, AzCopy işleri temizleme komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033715"
---
# <a name="azcopy-jobs-clean"></a>azcopy işleri temizleme

Tüm işler için tüm günlük ve plan dosyalarını kaldır

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="examples"></a>Örnekler

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Seçenekler

**-h,--yardım**                Temizleme için yardım.

**--WITH-Status** dizesi yalnızca bu durumdaki işleri kaldırır, kullanılabilir değerler: iptal edildi, tamamlandı, başarısız, sürüyor, All (varsayılan "tümü")

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

**--Cap-Mbps uint32**      Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**--komut çıktısının çıkış türü** dize biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer ' text ' değeridir. (varsayılan "metin")

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy işleri](storage-ref-azcopy-jobs.md)
