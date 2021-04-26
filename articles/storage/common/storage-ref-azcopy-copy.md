---
title: AzCopy kopyası | Microsoft Docs
description: Bu makale, AzCopy kopyalama komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503567"
---
# <a name="azcopy-copy"></a>azcopy kopya

Kaynak verileri bir hedef konuma kopyalar.

## <a name="synopsis"></a>Özeti

Kaynak verileri bir hedef konuma kopyalar. Desteklenen yönergeler şunlardır:

  - Yerel < > Azure Blobu (SAS veya OAuth kimlik doğrulaması)
  - Yerel < > Azure dosyaları (Share/Directory SAS kimlik doğrulaması)
  - Yerel < > Azure Data Lake Storage Gen 2 (SAS, OAuth veya paylaşılan anahtar kimlik doğrulaması)
  - Azure Blob (SAS veya genel)-Azure Blob > (SAS veya OAuth kimlik doğrulaması)
  - Azure Blob (SAS veya genel)-> Azure dosyaları (SAS)
  - Azure dosyaları (SAS)-Azure dosyaları > (SAS)
  - Azure dosyaları (SAS)-> Azure Blob (SAS veya OAuth kimlik doğrulaması)
  - Amazon Web Services (AWS) S3 (erişim anahtarı)-> Azure Blok Blobu (SAS veya OAuth kimlik doğrulaması)
  - Google Cloud Storage (hizmet hesabı anahtarı)-> Azure Blok Blobu (SAS veya OAuth kimlik doğrulaması) [Önizleme]

Daha fazla bilgi için bu makalenin örnekler bölümüne bakın.

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [Öğretici: AzCopy ile şirket içi verileri bulut depolamaya geçirme](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="advanced"></a>Gelişmiş

AzCopy, dosyaları yerel diskten karşıya yüklediğinizde dosyaların içerik türünü otomatik olarak algılar. AzCopy, içerik türünü dosya uzantısına veya içeriğe göre algılar (uzantı belirtilmediyse).

Yerleşik arama tablosu küçüktür, ancak UNIX üzerinde, `mime.types` Bu adların bir veya daha fazlası altında kullanılabilir olmaları durumunda yerel sistemin dosya (ler) i tarafından Genişletilebilir:

- /etc/Mime.exe
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows 'da MIME türleri kayıt defterinden ayıklanır. Bu özellik bir bayrağın yardımıyla kapatılabilir. Daha fazla bilgi için bu makalenin bayrak bölümüne bakın.

Bir ortam değişkenini komut satırını kullanarak ayarlarsanız, bu değişken komut satırı geçmişinizde okunabilir olacaktır. Komut satırı geçmişinizden kimlik bilgilerini içeren değişkenleri temizlemeyi göz önünde bulundurun. Değişkenlerin geçmişinizde görünmesini önlemek için, kullanıcıya kimlik bilgilerini istemek ve ortam değişkenini ayarlamak için bir komut dosyası kullanabilirsiniz.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Örnekler

OAuth kimlik doğrulaması kullanarak tek bir dosyayı karşıya yükleyin. AzCopy oturumu Henüz açmadıysanız, `azcopy login` aşağıdaki komutu çalıştırmadan önce komutunu çalıştırın.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Yukarıdaki gibi, ancak bu kez dosya içeriğinin MD5 karmasını de hesaplar ve BLOB 'un Content-MD5 özelliği olarak kaydeder:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

SAS belirteci kullanarak tek bir dosyayı karşıya yükle:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS belirteci ve boru kullanarak tek bir dosyayı karşıya yükleme (yalnızca blok blob 'lar):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Bir SAS belirteci kullanarak tüm dizini karşıya yükleyin:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

veya

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Bir SAS belirteci ve joker karakter (*) kullanarak bir dosya kümesini karşıya yükleyin:
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Bir SAS belirteci ve joker karakter (*) kullanarak dosyaları ve dizinleri karşıya yükleyin:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Dosyaları ve dizinleri Azure Storage hesabına yükleyin ve BLOB üzerinde sorgu dizesi kodlamalı Etiketler ayarlayın. 

- {Key = "bla bla", Val = "foo"} ve {Key = "bla bla 2", Val = "Bar"} etiketlerini ayarlamak için aşağıdaki sözdizimini kullanın: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Anahtarlar ve değerler URL kodlamalı ve anahtar-değer çiftleri bir ve işareti (' & ') ile ayrılır

- Bloblarda Etiketler ayarlanırken, SAS 'de hizmetin yetkilendirme hatası geri vermeksizin ek izinler (yani, Etiketler için 't) vardır.

OAuth kimlik doğrulaması kullanarak tek bir dosyayı indirin. AzCopy oturumu Henüz açmadıysanız, `azcopy login` aşağıdaki komutu çalıştırmadan önce komutunu çalıştırın.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

SAS belirteci kullanarak tek bir dosyayı indirme:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Bir SAS belirteci kullanarak tek bir dosyayı indirin ve sonra çıktıyı bir dosyaya (yalnızca blok Blobları) boru edin:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Bir SAS belirteci kullanarak tüm dizini indirin:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

URL 'lerde joker karakter (*) kullanmayla ilgili bir yer.

Bir URL 'de joker karakter kullanmanın yalnızca iki desteklenen yolu vardır. 

- Bir URL 'nin son eğik çizgiden (/) hemen sonra birini kullanabilirsiniz. Bu joker karakter kullanımı, bir dizindeki tüm dosyaları bir alt dizine yerleştirmeksizin doğrudan hedefe kopyalar. 

- Ayrıca, URL yalnızca bir kapsayıcıya başvurduğu ve bir blob 'a değil, kapsayıcının adında bir joker karakter kullanabilirsiniz. Bu yaklaşımı, kapsayıcıların bir alt kümesinden dosyaları almak için kullanabilirsiniz. 

Bir dizinin içeriğini içeren dizinin kendisini kopyalamadan indirin.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Bir depolama hesabının tamamını indirin.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Kapsayıcı adında bir joker karakter simgesi (*) kullanarak bir depolama hesabı içindeki kapsayıcıların bir alt kümesini indirin.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Bir SAS belirteci kullanarak tek bir blobu başka bir bloba kopyalama.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS belirteci ve kimlik doğrulama belirteci kullanarak tek bir blobu başka bir bloba kopyalama. Kaynak hesap URL 'sinin sonunda bir SAS belirteci kullanmanız gerekir, ancak komutunu kullanarak AzCopy içinde oturum açarsanız hedef hesap bir tane gerektirmez `azcopy login` . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

SAS belirteci kullanarak bir blob sanal dizinini diğerine kopyalayın:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Bir SAS belirteci kullanarak tüm blob kapsayıcılarını, dizinleri ve bloblarını depolama hesabından diğerine kopyalayın:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Bir erişim anahtarı ve SAS belirteci kullanarak Amazon Web Services (AWS) S3 ' den blob depolamaya tek bir nesne kopyalama. İlk olarak, ortam değişkenini `AWS_ACCESS_KEY_ID` ve `AWS_SECRET_ACCESS_KEY` AWS S3 kaynağı için ayarlayın.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Bir erişim anahtarı ve bir SAS belirteci kullanarak AWS S3 'ten bir dizinin tamamını blob depolamaya kopyalayın. İlk olarak, ortam değişkenini `AWS_ACCESS_KEY_ID` ve `AWS_SECRET_ACCESS_KEY` AWS S3 kaynağı için ayarlayın.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html[Folder] yer tutucusunu daha iyi anlamak için bölümüne bakın.

Bir erişim anahtarı ve bir SAS belirteci kullanarak tüm demetleri Amazon Web Services (AWS) ' den blob depolamaya kopyalayın. İlk olarak, ortam değişkenini `AWS_ACCESS_KEY_ID` ve `AWS_SECRET_ACCESS_KEY` AWS S3 kaynağı için ayarlayın.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Bir erişim anahtarı ve bir SAS belirteci kullanarak, tüm demetleri bir Amazon Web Services (AWS) bölgesinden blob depolamaya kopyalayın. İlk olarak, ortam değişkenini `AWS_ACCESS_KEY_ID` ve `AWS_SECRET_ACCESS_KEY` AWS S3 kaynağı için ayarlayın.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Demet adında bir joker karakter simgesi (*) kullanarak demetlerin bir alt kümesini kopyalayın. Önceki örneklerde olduğu gibi, bir erişim anahtarına ve SAS belirtecine ihtiyacınız olacaktır. Ortam değişkenini `AWS_ACCESS_KEY_ID` ve `AWS_SECRET_ACCESS_KEY` AWS S3 kaynağı için ayarladığınızdan emin olun.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Dosya ve dizinleri Azure depolama hesabına aktarın ve BLOB üzerinde verilen sorgu dizesi kodlamalı etiketleri ayarlayın. 

- {Key = "bla bla", Val = "foo"} ve {Key = "bla bla 2", Val = "Bar"} etiketlerini ayarlamak için aşağıdaki sözdizimini kullanın: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Anahtarlar ve değerler URL kodlamalı ve anahtar-değer çiftleri bir ve işareti (' & ') ile ayrılır
    
- Bloblarda Etiketler ayarlanırken, SAS 'de hizmetin yetkilendirme hatası geri vermeksizin ek izinler (yani, Etiketler için 't) vardır.

Bir hizmet hesabı anahtarı ve bir SAS belirteci kullanarak Google bulut depolamadan blob depolamaya tek bir nesne kopyalayın. İlk olarak, Google Cloud Storage kaynağı için GOOGLE_APPLICATION_CREDENTIALS ortam değişkenini ayarlayın.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Bir hizmet hesabı anahtarı ve bir SAS belirteci kullanarak bir dizinin tamamını Google Cloud Storage 'dan blob depolamaya kopyalayın. İlk olarak, Google Cloud Storage kaynağı için GOOGLE_APPLICATION_CREDENTIALS ortam değişkenini ayarlayın.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Bir hizmet hesabı anahtarı ve bir SAS belirteci kullanarak, bir demet tamamını Google bulut depolamadan blob depolamaya kopyalayın. İlk olarak, Google Cloud Storage kaynağı için GOOGLE_APPLICATION_CREDENTIALS ortam değişkenini ayarlayın.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Tüm demetleri, bir hizmet hesabı anahtarı ve bir SAS belirteci kullanarak Google bulut depolamadan blob depolamaya kopyalayın. İlk olarak, GCS kaynağı için GOOGLE_APPLICATION_CREDENTIALS ortam değişkenlerini ve GOOGLE_CLOUD_PROJECT =<proje kimliği> ayarlayın

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Bir hizmet hesabı anahtarı ve hedef için bir SAS belirteci kullanarak, bir arama alt kümesini Google Cloud Storage 'dan bir joker karakter simgesi (*) kullanarak kopyalayın. İlk olarak, GOOGLE_APPLICATION_CREDENTIALS ortam değişkenlerini ve GOOGLE_CLOUD_PROJECT =<proje kimliği> Google bulut depolama kaynağı için ayarlayın.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Seçenekler

**--yedekleme** AzCopy 'in, dosya sistemi izinlerinden bağımsız olarak tüm dosyaları görüp okumasına ve tüm izinleri geri yüklemelerine izin vermek için, yüklemeler için Windows ' SeBackupPrivilege ' veya indirme işlemleri için SeRestorePrivilege ' ı etkinleştirir. AzCopy çalıştıran hesabın bu izinlere zaten sahip olması gerekir (örneğin, yönetici haklarına sahip veya grubun üyesi olan `Backup Operators` ). Bu bayrak, hesabın zaten sahip olduğu ayrıcalıkları etkinleştirir.

**--BLOB-Etiketler** dize, Depolama hesabınızdaki verileri kategorilere ayırmak için Bloblarda Etiketler kümesi.

**--BLOB türü** dize, hedefteki blob türünü tanımlar. Bu, Blobları karşıya yüklemek için ve hesaplar arasında kopyalama sırasında kullanılır (varsayılan `Detect` ). Geçerli değerler,,, `Detect` `BlockBlob` ve içerir `PageBlob` `AppendBlob` . Hesaplar arasında kopyalama yaparken değeri, `Detect` AzCopy 'in hedef Blobun türünü belirlemekte kaynak Blobun türünü kullanmasına neden olur. Bir dosya karşıya yüklenirken, dosyanın `Detect` dosya uzantısına bağlı olarak BIR VHD veya vhdx dosyası olup olmadığını belirler. Dosya bir VHD veya VHDX dosyası ile karşılaşırsanız AzCopy dosyayı bir Sayfa Blobu olarak değerlendirir. (varsayılan "Algıla")

**--Block-blob-katman** dizesi bu blob katmanını kullanarak Blok Blobu Azure depolama 'ya yükler. (varsayılan "none")

**--blok-boyut-MB** float, Azure depolama 'ya yükleme yaparken ve Azure depolama 'dan indirerek bu blok boyutunu (MIB 'de belirtilir) kullanır. Varsayılan değer, dosya boyutuna göre otomatik olarak hesaplanır. Ondalık kesirlere izin verilir (örneğin: 0,25).

**--Cache-Control** dize Cache-Control üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Çek uzunluğu** Aktarımdan sonra hedefteki bir dosyanın uzunluğunu denetleyin. Kaynak ve hedef arasında uyuşmazlık varsa, aktarım başarısız olarak işaretlenir. (varsayılan değer `true` )

**--Check-MD5** dizesi, indirme sırasında ne kadar MD5 karmalarının doğrulanması gerektiğini belirtir. Yalnızca indirme sırasında kullanılabilir. Kullanılabilir seçenekler: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (varsayılan `FailIfDifferent` ) (varsayılan "Failiffarklı")

**--Content-Disposition** dize, Content-Disposition üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Content-** Encoding dize, Content-Encoding üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Content-** Language string içerik-Language üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Content-Type** dizesi dosyanın Içerik türünü belirtir. Tahmin yok-MIME türü anlamına gelir. İndirilmek üzere döndürüldü.

**--sıkıştırmayı aç** İçerik kodlaması sıkıştırılmış olduklarını gösteriyorsa, indirme sırasında dosyaları otomatik olarak aç. Desteklenen içerik kodlama değerleri `gzip` ve ' dir `deflate` . Veya için dosya uzantıları `.gz` / `.gzip` `.zz` gerekli değildir, ancak varsa kaldırılacaktır.

**--exclude-Attributes** dizesi (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları dışlar. Örneğin: A; Malar Sağ

**--exclude-blob türü** dize isteğe bağlı olarak, `BlockBlob` /  `PageBlob` /  `AppendBlob` bloblardan veya hesaptan blob kopyalanırken dışlanacak blob () türünü belirtir. Bu bayrağın kullanılması, Azure olmayan hizmetten hizmete veri kopyalamak için geçerli değildir. Birden fazla BLOB ile ayrılmalıdır `;` . 

**--exclude-yol** dizesi kopyalama sırasında bu yolları hariç tutar. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler (örneğin: `myFolder;myFolder/subDirName/file.pdf` ). Hesap çapraz geçiş ile birlikte kullanıldığında, yollar kapsayıcı adını içermez.

**--exclude-model** dize, kopyalama sırasında bu dosyaları hariç tutar. Bu seçenek joker karakterler (*) destekler.

**--takip-symlinks**  Yerel dosya sisteminden karşıya yüklerken sembolik bağlantıları izleyin.

**--** -------salt- Windows veya Azure dosyalarındaki mevcut bir dosyanın üzerine yazarken, var olan dosyanın salt okunurdur özniteliği ayarlanmış olsa bile, üzerine yazmaya zorlayın.

**--** ---arası Isteğe bağlı olarak kaynak hedef birleşimini belirtir. Örneğin: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--**  kopyalama için yardım yardım.

**--Include-After** dizesi yalnızca verilen tarih/saat içinde veya daha sonra değiştirilen dosyaları içerir. Değer ıSO8601 biçiminde olmalıdır. Hiçbir saat dilimi belirtilmemişse, değer AzCopy çalıştıran makinenin yerel saat diliminde olduğu varsayılır. Örneğin, `2020-08-19T15:04:00Z` BIR UTC saati veya `2020-08-19` yerel saat dilimindeki gece yarısı (00:00) için. AzCopy 10,5 itibariyle, bu bayrak yalnızca dosyalar için geçerlidir, bu nedenle bu bayrak veya ile kullanılırken klasör özellikleri kopyalanmaz `--preserve-smb-info` `--preserve-smb-permissions` .

 **--Include-Before** dize yalnızca belirtilen tarih/saat veya üzerinde değiştirilen dosyaları içermelidir. Değer ıSO8601 biçiminde olmalıdır. Hiçbir saat dilimi belirtilmemişse, değer AzCopy çalıştıran makinenin yerel saat diliminde olduğu varsayılır. Örneğin `2020-08-19T15:04:00Z` UTC saati veya `2020-08-19` gece yarısı (00:00) için yerel saat diliminde. AzCopy 10,7 itibariyle, bu bayrak yalnızca dosyalar için geçerlidir, bu nedenle bu bayrak veya ile kullanılırken klasör özellikleri kopyalanmaz `--preserve-smb-info` `--preserve-smb-permissions` .

**--Include-Attributes** dizesi (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları içerir. Örneğin: A; Malar Sağ

**--include-path** dizesi kopyalama sırasında yalnızca bu yolları içerir. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler (örneğin: `myFolder;myFolder/subDirName/file.pdf` ).

**--Include-model** dize, kopyalama sırasında yalnızca bu dosyaları içerir. Bu seçenek joker karakterler (*) destekler. Kullanarak dosyaları ayırın `;` .

**--** ------------------------ Kaynağın tek bir Blobun işaret etmelidir ve bu bayrağı kullanarak dosyada belirtilen tüm sürüm kimlikleri yalnızca kaynak Blobun ait olmalıdır. AzCopy, belirtilen sürümleri belirtilen hedef klasöre indirecek. Daha fazla bilgi için bkz. [bir blob 'un önceki sürümlerini indirme](./storage-use-azcopy-v10.md#transfer-data).

**--günlük düzeyi** dize günlük dosyası, kullanılabilir düzeyler: bilgi (tüm istekler/yanıtlar), uyarı (yavaş yanıtlar), hata (yalnızca başarısız istekler) ve hiçbiri (çıktı günlüğü yok) için günlük ayrıntı düzeylerini tanımlar. (varsayılan `INFO` ). 

**--meta veri** dizesi bu anahtar-değer çiftleri ile meta veri olarak Azure depolama 'Ya karşıya yükleyin.

**--No-tahmin-MIME türü**  AzCopy 'in, dosyanın uzantısına veya içeriğine göre içerik türünü algılamasını önler.

**--** bu bayrak true olarak ayarlandıysa, dizenin üzerine yazma, hedefteki çakışan dosya ve Blobların üzerine yazar. (varsayılan `true` ) Olası değerler şunlardır,,, `true` `false` `prompt` ve `ifSourceNewer` . Klasörleri destekleyen hedefler için, çakışan klasör düzeyi özelliklerinin üzerine yazılır `true` veya istem için olumlu bir yanıt sağlandıysa bu bayrak olur. (varsayılan "true")

**--Page-blob katmanı** dizesi bu blob katmanını kullanarak sayfa blobunu Azure depolama 'ya yükler. (varsayılan `None` ). (varsayılan "none")

**--Preserve-son değiştirilme zamanı**  Yalnızca hedef dosya sistemi olduğunda kullanılabilir.

**--Preserve-Owner**    Yalnızca indirmelerde ve yalnızca kullanıldığında bir etkiye sahiptir `--preserve-smb-permissions` . True ise (varsayılan), dosya sahibi ve grup indirmelerde korunur. False olarak ayarlanırsa, `--preserve-smb-permissions` ACL 'leri korur, ancak sahip ve grup AzCopy çalıştıran kullanıcıya göre yapılır (varsayılan doğru)

**--Preserve-SMB-Info**   Varsayılan olarak false. SMB kullanan kaynaklar (Windows ve Azure dosyaları) arasında SMB Özellik bilgilerini (son yazma saati, oluşturma saati, öznitelik bitleri) korur. Yalnızca Azure dosyaları tarafından desteklenen öznitelik bitleri aktarılır; diğerleri yok sayılır. Bu bayrak, salt dosya filtresi belirtilmediği takdirde (örneğin, include-model) hem dosya hem de klasörler için geçerlidir. Klasörler için aktarılan bilgiler, klasörler için hiçbir zaman korunmamış son yazma zamanı dışında dosyalarla aynıdır.

**--Preserve-SMB-izinleri**   Varsayılan olarak false. , Aware kaynakları (Windows ve Azure dosyaları) arasında SMB ACL 'Lerini korur. İndirmeler için, `--backup` Yeni sahibin AzCopy komutunu çalıştıran kullanıcı olmaması durumunda izinleri geri yükleme bayrağına de ihtiyacınız olacaktır. Bu bayrak, salt dosya filtresi belirtilmediği takdirde (örneğin,) hem dosya hem de klasörler için geçerlidir `include-pattern` .

**--PUT-MD5**    Her bir dosyanın MD5 karmasını oluşturun ve karmayı hedef Blobun veya dosyanın Content-MD5 özelliği olarak kaydedin. (Varsayılan olarak, karma oluşturulmaz.) Yalnızca karşıya yükleme sırasında kullanılabilir.

**--özyinelemeli**    Yerel dosya sisteminden karşıya yüklerken alt dizinlere yinelemeli olarak bakın.

**--S2S-Algıla-kaynak-değiştirildi**   Kaynak dosyanın/Blobun okuma sırasında değiştiğini algıla. (Bu parametre yalnızca hizmetten hizmete kopyalara uygulanır, çünkü karşılık gelen denetim karşıya yükleme ve indirme işlemleri için kalıcı olarak etkinleştirilmiştir.)

**--S2S-Handle-geçersiz-Metadata** dize, geçersiz meta veri anahtarlarının nasıl işleneceğini belirtir. Kullanılabilir seçenekler: Excludeifgeçersiz, Failifgeçersiz, Renameifgeçersiz. (varsayılan `ExcludeIfInvalid` ). (varsayılan "Excludeifgeçersiz")

**--S2S-Preserve-Access-Tier**   Hizmetten hizmete kopyalama sırasında erişim katmanını koruyun. Hedef depolama hesabının erişim katmanını ayarlamayı desteklediğinden emin olmak için [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](../blobs/storage-blob-storage-tiers.md) ' na bakın. Erişim katmanını ayarlamanın desteklenme durumlarında, erişim katmanını kopyalamayı atlamak için s2sPreserveAccessTier = false kullanın. (varsayılan `true` ).  (varsayılan "true")

**--S2S-Preserve-Özellikler**   Hizmetten hizmete kopyalama sırasında tam özellikleri koruyun. AWS S3 ve Azure dosya için tek dosya kaynağı için liste işlemi, nesnelerin ve dosyaların tam özelliklerini döndürmez. Tam özellikleri korumak için AzCopy, nesne veya dosya başına bir ek istek göndermelidir. (varsayılan doğru)

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

**--Cap-Mbps float**   Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**--komut çıktısının çıkış türü** dize biçimi. Seçenekler şunlardır: Text, JSON. `text` varsayılan değerdir. (varsayılan "metin")

**--Güvenilen-Microsoft-Suffixes** dizesi Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer: `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
