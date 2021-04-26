---
title: AzCopy kaldır | Microsoft Docs
description: Bu makale AzCopy Remove komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd221215d6be3c14ce1200e8bd374a97cb7608a0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503023"
---
# <a name="azcopy-remove"></a>azcopy kaldırma

Blob 'ları veya dosyaları bir Azure Storage hesabından silin.

## <a name="synopsis"></a>Özeti

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="examples"></a>Örnekler

SAS belirteci kullanarak tek bir blobu kaldırma:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Bir sanal dizinin tamamını bir SAS belirteci kullanarak kaldırın:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Yalnızca bir sanal dizinin içindeki Blobları kaldırın, ancak bu alt dizinlerin içindeki alt dizinleri veya Blobları kaldırmayın:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Bir sanal dizindeki Blobların bir alt kümesini kaldırın (örneğin: yalnızca jpg ve PDF dosyalarını kaldır veya blob adı ise `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Tüm sanal dizini kaldırın, ancak belirli Blobları kapsamdan hariç tutun (örneğin: her blob ile foo veya bitişi ile başlar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Belirli blob 'ları ve sanal dizinleri, göreli yollarını (URL kodlamalı DEĞIL) bir dosyaya koyarak kaldırın:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Hiyerarşik ad alanı olan bir BLOB depolama hesabından tek bir dosyayı kaldırma (dahil etme/hariç tutma desteklenmiyor):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Hiyerarşik ad alanı olan bir BLOB depolama hesabından tek bir dizini kaldırın (dahil etme/hariç tutma desteklenmez):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Seçenekler

**--Delete-Snapshot** String varsayılan olarak, blob 'un anlık görüntüleri varsa silme işlemi başarısız olur. `include`Kök blobu ve tüm anlık görüntülerini kaldırmak için belirtin; alternatif olarak `only` , yalnızca anlık görüntüleri kaldırmak ve kök blobu tutmak için belirtin.

**--exclude-Path** dizesi kaldırılırken Bu yolları hariç tutar. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler. Örnek: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-model** dize, adın model listesiyle eşleştiği dosyaları hariç tutar. Örneğin: `*.jpg` ;; `*.pdf``exactName`

**--**   -------salt-   Bir Azure dosyaları dosyası veya klasörü silinirken, var olan nesnenin salt okunurdur özniteliği ayarlanmış olsa bile silme işlemini çalışmaya zorlayın.

**--**   kaldırma yardımı yardımı.

**--Include-Path** dize yalnızca kaldırılırken Bu yolları içerir. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler. Örnek: `myFolder;myFolder/subDirName/file.pdf`

**--Include-model** dize yalnızca adı, model listesiyle eşleşen dosyaları içerir. Örneğin: *`.jpg` ;* `.pdf` ;`exactName`

**--dosya listesi** dizesi, silinecek dosya ve dizinlerin listesini içeren bir dosyanın konumunu tanımlar. Göreli yollar satır sonları ile sınırlandırılmalıdır ve yolların URL kodlamalı olmaması gerekir. 

**--** ------------------------ Kaynağın tek bir Blobun işaret etmelidir ve bu bayrağı kullanarak dosyada belirtilen tüm sürüm kimlikleri yalnızca kaynak Blobun ait olmalıdır. Verilen Blobun belirtilen sürüm kimlikleri Azure depolama alanından silinecek. 

**--günlük düzeyi** dize günlük dosyası için günlük ayrıntı düzeyini tanımlar. Kullanılabilir düzeyler şunlardır: `INFO` (tüm istekler/yanıtlar), `WARNING` (yavaş yanıtlar), `ERROR` (yalnızca başarısız istekler) ve `NONE` (çıktı günlüğü yok). (varsayılan `INFO` ) (varsayılan `INFO` )

**--özyinelemeli**    Dizinler arasında eşitleme yaparken alt dizinlere yinelemeli olarak bakın.

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps float|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|
|--Güvenilen-Microsoft-sonekler dizesi   |Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
