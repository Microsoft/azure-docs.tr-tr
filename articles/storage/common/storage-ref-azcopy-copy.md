---
title: AzCopy kopyası | Microsoft Docs
description: Bu makale, AzCopy kopyalama komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0c1b09fbc425a80fe1f8d075c5a83455167073c3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74029992"
---
# <a name="azcopy-copy"></a>azcopy kopya

Kaynak verileri bir hedef konuma kopyalar.

## <a name="synopsis"></a>Özeti

Kaynak verileri bir hedef konuma kopyalar. Desteklenen yönergeler şunlardır:

  - Yerel < > Azure Blobu (SAS veya OAuth kimlik doğrulaması)
  - Yerel < > Azure dosyaları (Share/Directory SAS kimlik doğrulaması)
  - Yerel <-> ADLS Gen 2 (SAS, OAuth veya SharedKey kimlik doğrulaması)
  - Azure Blob (SAS veya genel)-Azure Blob > (SAS veya OAuth kimlik doğrulaması)
  - Azure Blob (SAS veya genel)-> Azure dosyaları (SAS)
  - Azure dosyaları (SAS)-Azure dosyaları > (SAS)
  - Azure dosyaları (SAS)-> Azure Blob (SAS veya OAuth kimlik doğrulaması)
  - AWS S3 (erişim anahtarı)-> Azure Blok Blobu (SAS veya OAuth kimlik doğrulaması)

Daha fazla bilgi için lütfen örneklere bakın.

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Gelişmiş

AzCopy, dosya uzantısına veya içeriğe (uzantı belirtilmediyse) göre yerel diskten karşıya yüklenirken dosyaların içerik türünü otomatik olarak algılar.

Yerleşik arama tablosu küçüktür, ancak UNIX üzerinde, bu adlardan biri veya birkaçı altında varsa, yerel sistemin MIME. Types dosyaları tarafından Genişletilebilir:

- /etc/Mime.exe
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows 'da MIME türleri kayıt defterinden ayıklanır. Bu özellik bir bayrağın yardımıyla kapatılabilir. Lütfen bayrak bölümüne bakın.

Bir ortam değişkenini komut satırını kullanarak ayarlarsanız, bu değişken komut satırı geçmişinizde okunabilir olacaktır. Komut satırı geçmişinizden kimlik bilgilerini içeren değişkenleri temizlemeyi göz önünde bulundurun. Değişkenlerin geçmişinizde görünmesini önlemek için, kullanıcıya kimlik bilgilerini istemek ve ortam değişkenini ayarlamak için bir komut dosyası kullanabilirsiniz.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Örnekler

OAuth kimlik doğrulaması kullanarak tek bir dosyayı karşıya yükleyin. AzCopy 'a henüz oturum açmadıysanız, lütfen aşağıdaki komutu çalıştırmadan önce AzCopy Login komutunu çalıştırın.

- AzCopy CP "/Path/to/dosya.txt" "https://[hesap]. blob. Core. Windows. net/[Container]/[yol/to/blobu]"

Yukarıdaki gibi, ancak bu kez dosya içeriğinin MD5 karmasını de hesaplar ve BLOB 'un Content-MD5 özelliği olarak kaydeder:

- AzCopy CP "/Path/to/dosya.txt" "https://[hesap]. blob. Core. Windows. net/[Container]/[yol/to/blob]"--put-MD5

SAS belirteci kullanarak tek bir dosyayı karşıya yükle:

- AzCopy CP "/Path/to/DosyaYolu" "https://[hesap]. blob. Core. Windows. net/[Container]/[yol/to/blobu]? [SAS] "

SAS belirteci ve boru kullanarak tek bir dosyayı karşıya yükleme (yalnızca blok blob 'lar):
  
- Cat "/Path/to/dosya.txt" | AzCopy CP "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/blob]? [SAS] "

Bir SAS belirteci kullanarak tüm dizini karşıya yükleyin:
  
- AzCopy CP "/Path/to/dir" "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] "--özyinelemeli = true

veya

- AzCopy CP "/Path/to/dir" "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] "--özyinelemeli = true--put-MD5

Bir SAS belirteci ve joker karakter (*) kullanarak bir dosya kümesini karşıya yükleyin:

- AzCopy CP "/path/*foo/* Bar/*. pdf" "https://[hesap]. blob. Core. Windows. net/[Container]/[yol/to/Directory]? [SAS] "

Bir SAS belirteci ve joker karakter (*) kullanarak dosyaları ve dizinleri karşıya yükleyin:

- AzCopy CP "/path/*foo/* Bar *" "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] "--özyinelemeli = true

OAuth kimlik doğrulaması kullanarak tek bir dosyayı indirin. AzCopy 'a henüz oturum açmadıysanız, lütfen aşağıdaki komutu çalıştırmadan önce AzCopy Login komutunu çalıştırın.

- AzCopy CP "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/blob]" "/Path/to/dosya.txt"

SAS belirteci kullanarak tek bir dosyayı indirme:

- AzCopy CP "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/blob]? [SAS] ""/Path/to/dosya.txt "

Bir SAS belirteci kullanarak tek bir dosyayı indirin ve sonra çıktıyı bir dosyaya (yalnızca blok Blobları) boru edin:
  
- AzCopy CP "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/blob]? [SAS] ">"/Path/to/dosya.txt "

Bir SAS belirteci kullanarak tüm dizini indirin:
  
- AzCopy CP "https://[hesap]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] ""/Path/to/dir "--özyinelemeli = true

URL 'lerde joker karakter (*) kullanmayla ilgili bir yer.

Bir URL 'de joker karakter kullanmanın yalnızca iki desteklenen yolu vardır. 

- Bir URL 'nin son eğik çizgiden (/) hemen sonra birini kullanabilirsiniz. Bu, bir dizindeki tüm dosyaları bir alt dizine yerleştirmeksizin doğrudan hedefe kopyalar.

- URL yalnızca bir kapsayıcıya başvurduğu ve bir blob 'a değil, bir kapsayıcının adında de kullanabilirsiniz. Bu yaklaşımı, kapsayıcıların bir alt kümesinden dosyaları almak için kullanabilirsiniz.

Bir dizinin içeriğini içeren dizinin kendisini kopyalamadan indirin.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[Path/to/Folder]/*? [SAS] ""/Path/to/dir "

Bir depolama hesabının tamamını indirin.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/" "/Path/to/dir"--özyinelemeli

Kapsayıcı adında bir joker karakter simgesi (*) kullanarak bir depolama hesabı içindeki kapsayıcıların bir alt kümesini indirin.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[kapsayıcı * adı]" "/Path/to/dir"--özyinelemeli

Bir SAS belirteci kullanarak tek bir blobu başka bir bloba kopyalama.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[Path/to/blob]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[yol/to/blob]? [SAS] "

SAS belirtecini ve OAuth belirtecini kullanarak tek bir blobu başka bir bloba kopyalama. Kaynak hesap URL 'sinin sonunda bir SAS belirteci kullanmanız gerekir, ancak AzCopy Login komutunu kullanarak AzCopy oturumu açarsanız hedef hesaba bir tane ihtiyaç kalmaz. 

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[Path/to/blob]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[Path/to/blob] "

SAS belirteci kullanarak bir blob sanal dizinini diğerine kopyalayın:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] "--özyinelemeli = true

Bir SAS belirteci kullanarak tüm blob kapsayıcılarını, dizinleri ve bloblarını depolama hesabından diğerine kopyalayın:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net? [SAS] "" https://[destaccount]. blob. Core. Windows. net? [SAS] "--özyinelemeli = true

Bir erişim anahtarı ve SAS belirteci kullanarak Amazon Web Services (AWS) S3 ' den blob depolamaya tek bir nesne kopyalama. İlk olarak, AWS S3 kaynağı için AWS_ACCESS_KEY_ID ve AWS_SECRET_ACCESS_KEY ortam değişkenini ayarlayın.
  
- AzCopy CP "https://s3.amazonaws.com/[Bucket]/[nesne]" "https://[destaccount]. blob. Core. Windows. net/[Container]/[Path/to/blob]? [SAS] "

Bir erişim anahtarı ve bir SAS belirteci kullanarak AWS S3 'ten bir dizinin tamamını blob depolamaya kopyalayın. İlk olarak, AWS S3 kaynağı için AWS_ACCESS_KEY_ID ve AWS_SECRET_ACCESS_KEY ortam değişkenini ayarlayın.

- AzCopy CP "https://s3.amazonaws.com/[Bucket]/[klasör]" "https://[destaccount]. blob. Core. Windows. net/[Container]/[Path/to/Directory]? [SAS] "--özyinelemeli = true

[Folder] yer tutucusunu daha iyi anlamak için lütfen https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html başvurun.

Bir erişim anahtarı ve bir SAS belirteci kullanarak tüm demetleri Amazon Web Services (AWS) ' den blob depolamaya kopyalayın. İlk olarak, AWS S3 kaynağı için AWS_ACCESS_KEY_ID ve AWS_SECRET_ACCESS_KEY ortam değişkenini ayarlayın.

- AzCopy CP "https://s3.amazonaws.com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--özyinelemeli = true

Bir erişim anahtarı ve bir SAS belirteci kullanarak, tüm demetleri bir Amazon Web Services (AWS) bölgesinden blob depolamaya kopyalayın. İlk olarak, AWS S3 kaynağı için AWS_ACCESS_KEY_ID ve AWS_SECRET_ACCESS_KEY ortam değişkenini ayarlayın.

- AzCopy CP "https://s3-[Bölge]. amazonaws. com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--özyinelemeli = true

Demet adında bir joker karakter simgesi (*) kullanarak demetlerin bir alt kümesini kopyalayın. Önceki örneklerde olduğu gibi, bir erişim anahtarına ve SAS belirtecine ihtiyacınız olacaktır. AWS S3 kaynağı için AWS_ACCESS_KEY_ID ve AWS_SECRET_ACCESS_KEY ortam değişkenini ayarladığınızdan emin olun.

- AzCopy CP "https://s3.amazonaws.com/[Bucket * ad]/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--özyinelemeli = true

## <a name="options"></a>Seçenekler

**--BLOB türü** dize, hedefteki blob türünü tanımlar. Bu, Blobları karşıya yüklemek için ve hesaplar arasında kopyalama sırasında kullanılır (varsayılan ' Algıla '). Geçerli değerler ' Detect ', ' BlockBlob ', ' PageBlob ' ve ' AppendBlob ' değerleridir. Hesaplar arasında kopyalama yaparken, ' Algıla ' değeri AzCopy 'in hedef Blobun türünü belirlemek için kaynak Blobun türünü kullanmasına neden olur. Bir dosya karşıya yüklenirken, ' Algıla ' dosyanın dosya uzantısına bağlı olarak bir VHD veya VHDX dosyası olup olmadığını belirler. Dosya bir VHD veya VHDX dosyası ile karşılaşırsanız AzCopy dosyayı bir Sayfa Blobu olarak değerlendirir. (varsayılan "Algıla")

**--Block-blob-katman** dizesi bu blob katmanını kullanarak Blok Blobu Azure depolama 'ya yükler. (varsayılan "none")

**--blok-boyut-MB** float, Azure depolama 'ya yükleme yaparken ve Azure depolama 'dan indirerek bu blok boyutunu (MIB 'de belirtilir) kullanır. Varsayılan değer, dosya boyutuna göre otomatik olarak hesaplanır. Ondalık kesirlere izin verilir (örneğin: 0,25).

**--Cache-Control** dize Cache-Control üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Çek uzunluğu**                         Aktarımdan sonra hedefteki bir dosyanın uzunluğunu denetleyin. Kaynak ve hedef arasında uyuşmazlık varsa, aktarım başarısız olarak işaretlenir. (varsayılan doğru)

**--Check-MD5** dizesi, indirme sırasında ne kadar MD5 karmalarının doğrulanması gerektiğini belirtir. Yalnızca indirme sırasında kullanılabilir. Kullanılabilir seçenekler: NoCheck, LogOnly, Failiffarklı, Failiffarklıentormissing. (varsayılan "Failiffarklı")

**--Content-Disposition** dize, Content-Disposition üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Content-** Encoding dize, Content-Encoding üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Content-** Language string içerik-Language üst bilgisini ayarlar. İndirilmek üzere döndürüldü.

**--Content-Type** dizesi dosyanın Içerik türünü belirtir. Tahmin yok-MIME türü anlamına gelir. İndirilmek üzere döndürüldü.

**--sıkıştırmayı aç**                           İçerik kodlaması sıkıştırılmış olduklarını gösteriyorsa, indirme sırasında dosyaları otomatik olarak aç. Desteklenen içerik kodlama değerleri ' gzip ' ve ' söndür ' değerleridir. '. Gz '/'. gzip ' veya '. ZZ ' dosya uzantıları gerekli değildir, ancak varsa kaldırılacaktır.

**--exclude-Attributes** dizesi (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları hariç tutar. Örneğin: A; Malar Sağ

**--exclude-blob türü** dize Isteğe bağlı olarak, bloblardan veya hesaptan blob kopyalanırken hariç tutulacak blob türünü (Blockblob/Pageblob/appendblob) belirler. Bu bayrağın kullanılması, verileri Azure olmayan hizmetten hizmetten kopyalamak için geçerli değildir. Birden fazla BLOB '; ' ile ayrılmalıdır.

**--exclude-yol** dizesi kopyalama sırasında bu yolları hariç tutar. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler (örneğin: myFolder; myFolder/subDirName/File. PDF). Hesap çapraz geçiş ile birlikte kullanıldığında, yollar kapsayıcı adını içermez.

**--exclude-model** dize, kopyalama sırasında bu dosyaları hariç tutar. Bu seçenek joker karakterler (*) destekler

**--takip-symlinks**                      Yerel dosya sisteminden karşıya yüklerken sembolik bağlantıları izleyin.

**--** ---arası Isteğe bağlı olarak kaynak hedef birleşimini belirtir. Örneğin: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--** kopyalama için yardım yardımı

**--Include-Attributes** dize (yalnızca Windows) öznitelikleri öznitelik listesiyle eşleşen dosyaları içerir. Örneğin: A; Malar Sağ

**--include-path** dizesi kopyalama sırasında yalnızca bu yolları içerir. Bu seçenek joker karakterleri (*) desteklemez. Göreli yol önekini denetler (örneğin: myFolder; myFolder/subDirName/File. PDF).

**--Include-model** dize, kopyalama sırasında yalnızca bu dosyaları içerir. Bu seçenek joker karakterler (*) destekler. '; ' Kullanarak dosyaları ayırın.

**--günlük düzeyi** dize günlük dosyası, kullanılabilir düzeyler: bilgi (tüm istekler/yanıtlar), uyarı (yavaş yanıtlar), hata (yalnızca başarısız istekler) ve hiçbiri (çıktı günlüğü yok) için günlük ayrıntı düzeylerini tanımlar. (varsayılan "BILGI")

**--meta veri** dizesi bu anahtar-değer çiftleri ile meta veri olarak Azure depolama 'Ya karşıya yükleyin.

**--No-tahmin-MIME türü**                   AzCopy 'in, dosyanın uzantısına veya içeriğine göre içerik türünü algılamasını önler.

**--** bu bayrak true olarak ayarlandıysa, dizenin üzerine yazma, hedefteki çakışan dosya ve Blobların üzerine yazar. Olası değerler ' true ', ' false ' ve ' Prompt ' değerleridir. (varsayılan "true")

**--Page-blob katmanı** dizesi bu blob katmanını kullanarak sayfa blobunu Azure depolama 'ya yükler. (varsayılan "none")

**--Preserve-son değiştirilme zamanı**          Yalnızca hedef dosya sistemi olduğunda kullanılabilir.

**--PUT-MD5**                             Her bir dosyanın MD5 karmasını oluşturun ve karmayı hedef Blobun veya dosyanın Content-MD5 özelliği olarak kaydedin. (Varsayılan olarak, karma oluşturulmaz.) Yalnızca karşıya yükleme sırasında kullanılabilir.

**--özyinelemeli**                            Yerel dosya sisteminden karşıya yüklerken alt dizinlere yinelemeli olarak bakın.

**--S2S-Algıla-kaynak-değiştirildi**           Numaralandırdıktan sonra kaynağın değiştirilip değiştirilmediğini denetleyin.

**--S2S-Handle-geçersiz-Metadata** dize, geçersiz meta veri anahtarlarının nasıl işleneceğini belirtir. Kullanılabilir seçenekler: Excludeifgeçersiz, Failifgeçersiz, Renameifgeçersiz. (varsayılan "Excludeifgeçersiz")

**--S2S-Preserve-Access-Tier**             Hizmetten hizmete kopyalama sırasında erişim katmanını koruyun. Hedef depolama hesabının erişim katmanını ayarlamayı desteklediğinden emin olmak için lütfen [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanlarını](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) inceleyin. Erişim katmanını ayarlamanın desteklenme durumlarında, lütfen s2sPreserveAccessTier = false kullanarak erişim katmanını kopyalamayı atlayın. (varsayılan doğru)

**--S2S-Preserve-Özellikler**              Hizmetten hizmete kopyalama sırasında tam özellikleri koruyun. AWS S3 ve Azure dosya için tek dosya kaynağı için liste işlemi, nesnelerin ve dosyaların tam özelliklerini döndürmez. Tam özellikleri korumak için AzCopy, nesne veya dosya başına bir ek istek göndermelidir. (varsayılan doğru)

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

**--Cap-Mbps uint32**      Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**--komut çıktısının çıkış türü** dize biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer ' text ' değeridir. (varsayılan "metin")

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
