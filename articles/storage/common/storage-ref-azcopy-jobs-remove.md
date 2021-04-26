---
title: AzCopy işleri kaldır | Microsoft Docs
description: Bu makale AzCopy işleri Kaldır komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503380"
---
# <a name="azcopy-jobs-remove"></a>azcopy işleri kaldırma

Verilen iş KIMLIĞIYLE ilişkili tüm dosyaları kaldırın.

> [!NOTE] 
> Günlük ve plan dosyalarının kaydedildiği konumu özelleştirebilirsiniz. Daha fazla bilgi için [AzCopy env](storage-ref-azcopy-env.md) komutuna bakın.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="examples"></a>Örnekler

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Seçenekler

**--Yardım**                Remove için yardım.

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

**--Cap-Mbps float**      Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**--komut çıktısının çıkış türü** dize biçimi. Seçenekler şunlardır: Text, JSON. `text` varsayılan değerdir. (varsayılan `text` )

**--Güvenilen-Microsoft-Suffixes** dizesi Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.

## <a name="see-also"></a>Ayrıca bkz.

- [azcopy işleri](storage-ref-azcopy-jobs.md)
