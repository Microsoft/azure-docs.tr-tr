---
title: AzCopy işleri temizle | Microsoft Docs
description: Bu makale, AzCopy işleri temizleme komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 01bb7e37965abacac8105689bcb5ae52c548d647
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503431"
---
# <a name="azcopy-jobs-clean"></a>azcopy işleri temizleme

Tüm işler için tüm günlük ve plan dosyalarını kaldır

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="examples"></a>Örnekler

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Seçenekler

**--Yardım**                Temizleme için yardım.

**--WITH-Status** dizesi yalnızca bu durumdaki işleri kaldırır, kullanılabilir değerler: `Canceled` ,,, `Completed` `Failed` `InProgress` , `All` (varsayılan `All` )

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

**--Cap-Mbps float**      Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**--komut çıktısının çıkış türü** dize biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer ' text ' değeridir. (varsayılan "metin")

**--Güvenilen-Microsoft-Suffixes** dizesi Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.

## <a name="see-also"></a>Ayrıca bkz.

- [azcopy işleri](storage-ref-azcopy-jobs.md)
