---
title: AzCopy işleri özgeçmişi | Microsoft Docs
description: Bu makale, AzCopy işleri özgeçmişi komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034153"
---
# <a name="azcopy-jobs-resume"></a>azcopy işleri sürdürme

Verilen iş KIMLIĞI ile mevcut işi sürdürür.

## <a name="synopsis"></a>Özeti

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="options"></a>Seçenekler

|Seçenek|Açıklama|
|--|--|
|--hedef-SAS dizesi|Verilen iş kimliği için hedefin hedef SAS 'ları.|
|--dizeyi Dışla|Filtre: iş sürdürülürken bu başarısız olan aktarım (ler) i hariç tut. Dosyalar '; ' ile ayrılmalıdır.|
|-h,--yardım|Özgeçmişi komutu için yardım içeriğini göster.|
|--Include dizesi|Filtre: iş sürdürülürken yalnızca bu başarısız aktarmalar dahil edin. Dosyalar '; ' ile ayrılmalıdır.|
|--Kaynak-SAS dizesi |verilen iş kimliği için kaynağın kaynak SAS 'ları.|

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps uint32|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy işleri](storage-ref-azcopy-jobs.md)
