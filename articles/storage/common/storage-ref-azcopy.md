---
title: AzCopy | Microsoft Docs
description: Bu makale AzCopy komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038081"
---
# <a name="azcopy"></a>azcopy

AzCopy, Azure depolama 'ya ve dışına veri taşınan bir komut satırı aracıdır.

## <a name="synopsis"></a>Özeti

Komutların genel biçimi: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Sorunları bildirmek veya araç hakkında daha fazla bilgi edinmek için bkz. [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="options"></a>Seçenekler

**--Cap-Mbps uint32**   Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**-h,--yardım** AzCopy için yardım
      
**--Çıkış türü**  Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer ' text ' değeridir. (varsayılan "metin")

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy tezgahtır](storage-ref-azcopy-bench.md)
- [AzCopy kopyası](storage-ref-azcopy-copy.md)
- [AzCopy belgesi](storage-ref-azcopy-doc.md)
- [AzCopy env](storage-ref-azcopy-env.md)
- [AzCopy işleri](storage-ref-azcopy-jobs.md)
- [AzCopy işleri temizle](storage-ref-azcopy-jobs-clean.md)
- [AzCopy işleri listesi](storage-ref-azcopy-jobs-list.md)
- [AzCopy işleri kaldır](storage-ref-azcopy-jobs-remove.md)
- [AzCopy işleri özgeçmişi](storage-ref-azcopy-jobs-resume.md)
- [AzCopy işlerinin gösterilmesi](storage-ref-azcopy-jobs-show.md)
- [AzCopy listesi](storage-ref-azcopy-list.md)
- [AzCopy oturum açma](storage-ref-azcopy-login.md)
- [AzCopy oturumu kapatma](storage-ref-azcopy-logout.md)
- [AzCopy oluştur](storage-ref-azcopy-make.md)
- [AzCopy kaldır](storage-ref-azcopy-remove.md)
- [AzCopy eşitleme](storage-ref-azcopy-sync.md)
