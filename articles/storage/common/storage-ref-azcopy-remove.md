---
title: AzCopy kaldır | Microsoft Docs
description: Bu makale AzCopy Remove komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033993"
---
# <a name="azcopy-remove"></a>azcopy kaldırma

Blob 'ları veya dosyaları bir Azure Storage hesabından silin.

## <a name="synopsis"></a>Özeti

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="examples"></a>Örnekler

SAS içeren tek bir blobu kaldırma:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Bir sanal dizinin tamamını SAS ile kaldırın:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Yalnızca bir sanal dizin içindeki en üstteki Blobları Kaldır ancak alt dizinlerini kaldırın:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Bir sanal dizindeki Blobların bir alt kümesini kaldırın (örneğin: yalnızca jpg ve PDF dosyaları ya da blob adı "exactName" ise):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Sanal bir dizinin tamamını kaldırın, ancak belirli Blobları kapsamdan hariç tutun (örneğin: her blob, Foo ile başlayan veya çubukla bitiyor):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Belirli blob 'ları ve sanal dizinleri, göreli yollarını (URL kodlamalı DEĞIL) bir dosyaya koyarak kaldırın:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Hiyerarşik ad alanı olan bir BLOB depolama hesabından tek bir dosyayı kaldırın (dahil etme/hariç tutma desteklenmiyor).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Hiyerarşik ad alanı olan bir BLOB depolama hesabı olan tek bir dizini kaldırma (dahil etme/hariç tutma desteklenmez):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Seçenekler

**--exclude-Path dizesi**      Bu yolları kaldırırken hariç tutun. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler. Örneğin: myFolder; myFolder/subDirName/File. PDF.

**--exclude-model** dize, adın model listesiyle eşleştiği dosyaları hariç tutar. Örneğin: *. jpg;* . PDF; exactName

**-h,--** kaldırma için yardım yardımı

**--Include-Path** dize yalnızca kaldırılırken Bu yolları içerir. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler. Örneğin: myFolder; myFolder/subDirName/File. PDF

**--Include-model** dize yalnızca adı, model listesiyle eşleşen dosyaları içerir. Örneğin: *. jpg;* . PDF; exactName

**--dosya listesi** dizesi, silinecek dosya ve dizinlerin listesini içeren bir dosyanın konumunu tanımlar. Göreli yollar satır sonları ile sınırlandırılmalıdır ve yolların URL kodlamalı olmaması gerekir.

**--günlük düzeyi** dize günlük dosyası için günlük ayrıntı düzeyini tanımlar. Kullanılabilir düzeyler şunlardır: BILGI (tüm istekler/yanıtlar), uyarı (yavaş yanıtlar), hata (yalnızca başarısız istekler) ve HIÇBIRI (çıktı günlüğü yok). (varsayılan ' BILGI ') (varsayılan "BILGI")

**--özyinelemeli**                Dizinler arasında eşitleme yaparken alt dizinlere yinelemeli olarak bakın.

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps uint32|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
