---
title: AzCopy eşitleme | Microsoft Docs
description: Bu makale AzCopy Sync komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9bfe88c34c78d18f2f5aefb8ae6946b9786030ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023506"
---
# <a name="azcopy-sync"></a>azcopy eşitleme

Kaynak konumunu hedef konuma çoğaltır.

## <a name="synopsis"></a>Özeti

Son değiştirme zamanları karşılaştırma için kullanılır. Hedefteki son değiştirilme zamanı daha yeni ise dosya atlanır.

Desteklenen çiftler şunlardır:

- Yerel < > Azure Blobu (SAS veya OAuth kimlik doğrulaması kullanılabilir)
- Azure Blob <-> Azure Blob (kaynak bir SAS içermeli ya da genel olarak erişilebilir olmalıdır; hedef için SAS veya OAuth kimlik doğrulaması kullanılabilir)
- Azure dosya <-> Azure dosyası (kaynak bir SAS içermeli veya genel olarak erişilebilir olmalıdır; Hedef için SAS kimlik doğrulaması kullanılmalıdır)

Eşitle komutu kopyalama komutundan farklı yollarla farklılık gösterir:

1. Varsayılan olarak, özyinelemeli bayrak true olur ve tüm alt dizinleri eşitler. Eşitleme yalnızca özyinelemeli bayrak false ise, en üst düzey dosyaları bir dizin içinde kopyalar.
2. Sanal dizinler arasında eşitleme yaparken, bir sanal dizinle aynı ada sahip bir blob varsa yola bir sondaki eğik çizgi ekleyin (örneklere bakın).
3. ' DeleteDestination ' bayrağı true ya da Prompt olarak ayarlandıysa, eşitleme, hedefteki dosyaları ve Blobları kaynakta mevcut olmayan şekilde siler.

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Gelişmiş

Bir dosya uzantısı belirtmezseniz, AzCopy dosya uzantısına veya içeriğe (uzantı belirtilmediyse) göre yerel diskten karşıya yüklenirken dosyaların içerik türünü otomatik olarak algılar.

Yerleşik arama tablosu küçüktür, ancak UNIX üzerinde, bu adlardan biri veya birkaçı altında varsa, yerel sistemin MIME. Types dosyaları tarafından Genişletilebilir:

- /etc/Mime.exe
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows 'da MIME türleri kayıt defterinden ayıklanır.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Örnekler

Tek bir dosyayı eşitleyin:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Yukarıdaki gibi, ancak bu kez dosya içeriğinin MD5 karmasını de hesaplar ve BLOB 'un Content-MD5 özelliği olarak kaydeder:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Tüm dizini alt dizinleriyle birlikte eşitleyin (özyinelemeli, varsayılan olarak açık olduğunu unutmayın):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

veya

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Yalnızca bir dizinin içindeki en üstteki dosyaları eşitleyin, ancak alt dizinlerini değil:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Dizindeki dosyaların bir alt kümesini eşitleyin (örneğin: yalnızca jpg ve PDF dosyaları Için veya dosya adı "exactName" ise):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Bir dizinin tamamını eşitleyin, ancak belirli dosyaları kapsamdan dışlayın (örneğin: Foo ile başlayan her dosya ve çubukla biter):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Tek bir blobu eşitleme:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Sync a virtual directory:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Aynı ada sahip bir sanal dizini blob ile eşitleyin (belirsizliği ortadan kaldırmak için yola eğik çizgi ekleyin):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Bir Azure dosya dizinini eşitleme (blob ile aynı söz dizimi):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Dahil etme/hariç tutma bayrakları birlikte kullanılırsa, yalnızca içerme desenleriyle eşleşen dosyalar aranabilir, ancak dışlama desenleriyle eşleşen dosyalar her zaman yok sayılır.

## <a name="options"></a>Seçenekler

**--blok-boyut-MB** float, Azure depolama 'ya yükleme yaparken veya Azure Storage 'dan indirerek bu blok boyutunu (MIB 'de belirtilir) kullanır. Varsayılan değer, dosya boyutuna göre otomatik olarak hesaplanır. Ondalık kesirlere izin verilir (örneğin: 0,25).

**--Check-MD5** dizesi, indirme sırasında ne kadar MD5 karmalarının doğrulanması gerektiğini belirtir. Bu seçenek yalnızca indirme sırasında kullanılabilir. Kullanılabilir değerler şunlardır: NoCheck, LogOnly, Failiffarklı, Failiffarklıentormissing. (varsayılan ' Failiffarklı '). (varsayılan "Failiffarklı")

**--Delete-Destination** dizesi, kaynakta mevcut olmayan hedefe ait ek dosyaların silinip oluşturulmayacağını tanımlar. True, false veya Prompt olarak ayarlanabilir. Sor olarak ayarlandıysa, kullanıcıya dosyaları ve Blobları silmeye başlamadan önce bir soru sorulur. (varsayılan ' false '). (varsayılan "false")

**--exclude-Attributes** dizesi (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları hariç tutar. Örneğin: A; Malar Sağ

**--exclude-model** dize, adın model listesiyle eşleştiği dosyaları hariç tutar. Örneğin: *. jpg;* . PDF; exactName

**-h,--** eşitleme için yardım yardımı

**--Include-Attributes** dizesi (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları içerir. Örneğin: A; Malar Sağ

**--Include-model** dize yalnızca adı, model listesiyle eşleşen dosyaları içerir. Örneğin: *. jpg;* . PDF; exactName

**--günlük düzeyi** dize günlük dosyası, kullanılabilir düzeyler: bilgi (tüm istekler ve yanıtlar), uyarı (yavaş yanıtlar), hata (yalnızca başarısız istekler) ve hiçbiri (çıktı günlüğü yok) için günlük ayrıntı düzeylerini tanımlar. (varsayılan BILGI). (varsayılan "BILGI")

**--PUT-MD5**                     Her bir dosyanın MD5 karmasını oluşturun ve karmayı hedef Blobun veya dosyanın Content-MD5 özelliği olarak kaydedin. (Varsayılan olarak, karma oluşturulmaz.) Yalnızca karşıya yükleme sırasında kullanılabilir.

**--özyinelemeli**                   Varsayılan olarak, dizinler arasında eşitleme yaparken alt dizinlere yinelemeli olarak bakın. (varsayılan doğru). (varsayılan doğru)

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps uint32|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
