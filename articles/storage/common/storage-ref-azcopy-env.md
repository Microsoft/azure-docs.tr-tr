---
title: AzCopy env | Microsoft Docs
description: Bu makale AzCopy env komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 71a4c27b84a16a4acb37c196ccd8ee571c2b2468
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503499"
---
# <a name="azcopy-env"></a>azcopy ortamı

AzCopy 'in davranışını yapılandırabileceği ortam değişkenlerini gösterir. Ortam değişkenlerinin tüm listesi için bkz. [AzCopy ile v10 arasındaki yapılandırma ayarları (Azure depolama)](storage-ref-azcopy-configuration-settings.md).

## <a name="synopsis"></a>Özeti

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Bir ortam değişkenini komut satırını kullanarak ayarlarsanız, bu değişken komut satırı geçmişinizde okunabilir olacaktır. Komut satırı geçmişinizden kimlik bilgilerini içeren değişkenleri temizlemeyi göz önünde bulundurun. Değişkenlerin geçmişinizde görünmesini önlemek için, kullanıcıya kimlik bilgilerini istemek ve ortam değişkenini ayarlamak için bir komut dosyası kullanabilirsiniz.

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="options"></a>Seçenekler

|Seçenek|Açıklama|
|--|--|
|-h,--yardım|Env komutu için yardım içeriğini gösterir. |
|--hassas göster|Gizli/gizli ortam değişkenlerini gösterir.|

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps float|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|
|--Güvenilen-Microsoft-sonekler dizesi  | Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
