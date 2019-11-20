---
title: AzCopy işleri göster | Microsoft Docs
description: Bu makale, AzCopy işleri göster komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034129"
---
# <a name="azcopy-jobs-show"></a>azcopy işleri gösterme

Verilen iş KIMLIĞI için ayrıntılı bilgileri gösterir.

## <a name="synopsis"></a>Özeti

Yalnızca iş KIMLIĞI bayrak olmadan sağlanırsa, işin ilerleme Özeti döndürülür.

Bu komutu çalıştırdığınızda görüntülenen bayt sayısı ve tamamlanma yüzdesi, yalnızca işte tamamlanan dosyaları yansıtır. Kısmen tamamlanmış dosyaları yansıtmaz.

`with-status` bayrağı ayarlandıysa, söz konusu değere sahip iş içindeki aktarımların listesi görüntülenir.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="options"></a>Seçenekler

|Seçenek|Açıklama|
|--|--|
|-h,--yardım|Göster komutu için yardım içeriğini gösterir.|
|--durum dizesi|Yalnızca bu duruma sahip iş aktarımlarını Listele, kullanılabilir değerler: başlatıldı, başarılı, başarısız oldu|

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps uint32|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy işleri](storage-ref-azcopy-jobs.md)
