---
title: AzCopy eşitleme | Microsoft Docs
description: Bu makale AzCopy Sync komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 83d10a7a6e9eb14379d32cc88800a2c443feac60
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503057"
---
# <a name="azcopy-sync"></a>azcopy eşitleme

Kaynak konumunu hedef konuma çoğaltır. Bu makale, AzCopy Sync komutuna yönelik ayrıntılı bir başvuru sağlar. Kaynak ve hedef konumlar arasında blob 'ları eşitleme hakkında daha fazla bilgi edinmek için bkz. [AzCopy ile v10 arasındaki kullanarak Azure Blob depolama Ile eşitleme](storage-use-azcopy-blobs-synchronize.md). Azure dosyaları için bkz. [Synchronize Files](storage-use-azcopy-files.md#synchronize-files).

## <a name="synopsis"></a>Özeti

Son değiştirme zamanları karşılaştırma için kullanılır. Hedefteki son değiştirilme zamanı daha yeni ise dosya atlanır.

Desteklenen çiftler şunlardır:

- Yerel < > Azure Blobu (SAS veya OAuth kimlik doğrulaması kullanılabilir)
- Azure Blob <-> Azure Blob (kaynak bir SAS içermeli ya da genel olarak erişilebilir olmalıdır; hedef için SAS veya OAuth kimlik doğrulaması kullanılabilir)
- Azure dosya <-> Azure dosyası (kaynak bir SAS içermeli veya genel olarak erişilebilir olmalıdır; Hedef için SAS kimlik doğrulaması kullanılmalıdır)

Eşitle komutu kopyalama komutundan farklı yollarla farklılık gösterir:

1. Varsayılan olarak, özyinelemeli bayrak true olur ve tüm alt dizinleri eşitler. Eşitleme yalnızca özyinelemeli bayrak false ise, en üst düzey dosyaları bir dizin içinde kopyalar.
2. Sanal dizinler arasında eşitleme yaparken, bir sanal dizinle aynı ada sahip bir blob varsa yola bir sondaki eğik çizgi ekleyin (örneklere bakın).
3. `deleteDestination`Bayrak true veya Prompt olarak ayarlandıysa, eşitleme, hedefte mevcut olmayan, hedefteki dosyaları ve Blobları siler.

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [Öğretici: AzCopy ile şirket içi verileri bulut depolamaya geçirme](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

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

Yukarıdaki gibi aynıdır, ancak dosya içeriğinin MD5 karmasını de hesaplar ve ardından bu MD5 karmasını Blobun Content-MD5 özelliği olarak kaydedin. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Alt dizinleri de içeren bir dizinin tamamını eşitleyin (özyinelemeli öğesinin varsayılan olarak olduğunu unutmayın):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

veya

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Yalnızca bir dizinin içindeki dosyaları ve alt dizinleri veya alt dizinlerin içindeki dosyaları eşitleyin:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Dizindeki dosyaların bir alt kümesini eşitleyin (örneğin: yalnızca jpg ve PDF dosyaları ya da dosya adı ise `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Bir dizinin tamamını eşitleyin, ancak belirli dosyaları kapsamdan dışlayın (örneğin: Foo ile başlayan her dosya ve çubukla biter):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Tek bir blobu eşitleme:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sanal bir dizini Eşitle:

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

**--blok-boyut-MB** float, Azure depolama 'ya yükleme yaparken veya Azure Storage 'dan indirerek bu blok boyutunu (MIB 'de belirtilir) kullanır. Varsayılan değer, dosya boyutuna göre otomatik olarak hesaplanır. Ondalık kesirlere izin verilir (örneğin: `0.25` ).

**--Check-MD5** dizesi, indirme sırasında ne kadar MD5 karmalarının doğrulanması gerektiğini belirtir. Bu seçenek yalnızca indirme sırasında kullanılabilir. Kullanılabilir değerler şunlardır: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (varsayılan `FailIfDifferent` ). (varsayılan `FailIfDifferent` )

**--Delete-Destination** dizesi, kaynakta mevcut olmayan hedefe ait ek dosyaların silinip oluşturulmayacağını tanımlar. , Veya olarak ayarlanabilir `true` `false` `prompt` . Olarak ayarlanırsa `prompt` , kullanıcıya dosyaları ve Blobları silinmek üzere zamanlamadan önce bir soru sorulur. (varsayılan `false` ). (varsayılan `false` )

**--exclude-Attributes** dizesi (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları dışlar. Örnek: `A;S;R`

**--exclude-Path** dize, kaynağı hedefe göre karşılaştırırken bu yolları hariç tutar. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler (örneğin: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-model** dize, adın model listesiyle eşleştiği dosyaları hariç tutar. Örnek: `*.jpg;*.pdf;exactName`

**--**    eşitleme için yardım yardım.

**--Include-Attributes** dizesi (yalnızca Windows), öznitelikleri öznitelik listesiyle eşleşen dosyaları içerir. Örnek: `A;S;R`

**--Include-model** dize yalnızca adı, model listesiyle eşleşen dosyaları içerir. Örnek: `*.jpg;*.pdf;exactName`

**--günlük düzeyi** dize, günlük dosyası, kullanılabilir düzeyler: `INFO` (tüm istekler ve yanıtlar), `WARNING` (yavaş yanıtlar), `ERROR` (yalnızca başarısız istekler) ve `NONE` (çıktı günlüğü yok) için günlük ayrıntı düzeyini tanımlar. (varsayılan `INFO` ). 

**--Preserve-SMB-Info**   Varsayılan olarak false. SMB kullanan kaynaklar (Windows ve Azure dosyaları) arasında SMB Özellik bilgilerini (son yazma saati, oluşturma saati, öznitelik bitleri) korur. Bu bayrak, salt dosya filtresi belirtilmediği takdirde (örneğin, include-model) hem dosya hem de klasörler için geçerlidir. Klasörler için aktarılan bilgiler, klasörler için korunmayan son yazma zamanı dışında dosyalar için de aynıdır.

**--Preserve-SMB-izinleri**   Varsayılan olarak false. , Aware kaynakları (Windows ve Azure dosyaları) arasında SMB ACL 'Lerini korur. Bu bayrak, salt dosya filtresi belirtilmediği takdirde (örneğin,) hem dosya hem de klasörler için geçerlidir `include-pattern` .

**--PUT-MD5**     Her bir dosyanın MD5 karmasını oluşturun ve karmayı hedef Blobun veya dosyanın Content-MD5 özelliği olarak kaydedin. (Varsayılan olarak, karma oluşturulmaz.) Yalnızca karşıya yükleme sırasında kullanılabilir.

**--özyinelemeli** `True` Varsayılan olarak, dizinler arasında eşitleme yaparken alt dizinlere yinelemeli olarak bakın.     (varsayılan `True` ). 

**--S2S-Preserve-Access-Tier**  Hizmetten hizmete kopyalama sırasında erişim katmanını koruyun. Hedef depolama hesabının erişim katmanını ayarlamayı desteklediğinden emin olmak için [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](../blobs/storage-blob-storage-tiers.md) ' na bakın. Erişim katmanını ayarlamanın desteklenme durumlarında, lütfen s2sPreserveAccessTier = false kullanarak erişim katmanını kopyalamayı atlayın. (varsayılan `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps uint32|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|
|--Güvenilen-Microsoft-sonekler dizesi   |Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
