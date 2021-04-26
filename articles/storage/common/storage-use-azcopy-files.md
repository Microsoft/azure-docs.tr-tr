---
title: AzCopy ile v10 arasındaki kullanarak Azure dosyalarına veri aktarma | Microsoft Docs
description: AzCopy ve dosya depolama ile veri aktarma. AzCopy, bir depolama hesabına blob veya dosya kopyalamak için bir komut satırı aracıdır. Azure dosyaları ile AzCopy kullanın.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 04bf84c5dc8a63929900f1bc95d7274074ef9d5a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498365"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy ve dosya depolama ile verileri aktarma 

AzCopy, bir depolama hesabından dosya kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale, Azure dosyaları ile çalışan örnek komutlar içerir.

Başlamadan önce AzCopy [ile çalışmaya başlama](storage-use-azcopy-v10.md) makalesini okuyun ve aracı hakkında bilgi edinin.

> [!TIP]
> Bu makaledeki örneklerde, tek tırnak (' ') ile yol bağımsız değişkenleri yer alınmalıdır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

## <a name="create-file-shares"></a>Dosya paylaşımları oluşturma

Bir dosya paylaşma oluşturmak için [AzCopy Make](storage-ref-azcopy-make.md) komutunu kullanabilirsiniz. Bu bölümdeki örnek adlı bir dosya paylaşma oluşturur `myfileshare` .

**Syntax**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Örnek**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Ayrıntılı başvuru belgeleri için bkz. [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Dosyaları karşıya yükleme

Yerel bilgisayarınızdan dosya ve Dizin yüklemek için [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanabilirsiniz.

Bu bölüm aşağıdaki örnekleri içerir:

> [!div class="checklist"]
> * Dosyayı karşıya yükleme
> * Bir dizini karşıya yükle
> * Bir dizinin içeriğini karşıya yükleme
> * Belirli bir dosyayı karşıya yükle

> [!TIP]
> İsteğe bağlı bayraklar kullanarak karşıya yükleme işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.
>
> |Senaryo|Bayrak|
> |---|---|
> |Erişim denetim listelerini (ACL 'Ler) dosyalarla birlikte kopyalayın.|**--Preserve-SMB-izinleri** = \[ doğru \| yanlış\]|
> |SMB Özellik bilgilerini dosyalarla birlikte kopyalayın.|**--Preserve-SMB-Info** = \[ doğru \| yanlış\]|
> 
> Tüm liste için bkz. [Seçenekler](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy, dosyanın MD5 karma kodunu otomatik olarak hesaplamaz ve depolamaz. AzCopy 'in bunu yapmak istiyorsanız, `--put-md5` her bir kopyala komutuna bayrak ekleyin. Bu şekilde, Dosya indirildiğinde AzCopy, indirilen veriler için bir MD5 karması hesaplar ve dosyanın özelliğinde depolanan MD5 karmasının `Content-md5` hesaplanan karmayla eşleştiğini doğrular.

### <a name="upload-a-file"></a>Dosyayı karşıya yükleme

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Dosya yolu veya dosya adında herhangi bir yerde joker karakter sembolünü (*) kullanarak da bir dosyayı karşıya yükleyebilirsiniz. Örneğin: `'C:\myDirectory\*.txt'` , veya `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Bir dizini karşıya yükle

Bu örnekte bir dizin (ve dizinin içindeki tüm dosyalar) bir dosya paylaşımına kopyalanmıştır. Sonuçta dosya paylaşımında aynı ada sahip bir dizin oluşturulur.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Dosya paylaşımındaki bir dizine kopyalamak için, komut dizeniz içindeki bu dizinin adını belirtmeniz yeterlidir.

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Dosya paylaşımında var olmayan bir dizinin adını belirtirseniz AzCopy, bu adla yeni bir dizin oluşturur.

### <a name="upload-the-contents-of-a-directory"></a>Bir dizinin içeriğini karşıya yükleme

Joker karakter sembolünü (*) kullanarak, içeren dizinin kendisini kopyalamadan bir dizinin içeriğini karşıya yükleyebilirsiniz.

**Syntax**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"
```

> [!NOTE]
> `--recursive`Tüm alt dizinlerde dosyaları karşıya yüklemek için bayrağı ekleyin.

### <a name="upload-specific-files"></a>Belirli dosyaları karşıya yükle

Tüm dosya adlarını, joker karakter (*) ile kısmi adları veya tarih ve saatleri kullanarak belirli dosyaları karşıya yükleyebilirsiniz.

#### <a name="specify-multiple-complete-file-names"></a>Birden çok dosya adı belirtin

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-path` . Tek bir dosya adını noktalı virgül () kullanarak ayırın `;` .

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'
```

Bu örnekte, AzCopy `C:\myDirectory\photos` dizini ve `C:\myDirectory\documents\myFile.txt` dosyayı aktarır. `--recursive`Dizindeki tüm dosyaları aktarma seçeneğini dahil etmeniz gerekir `C:\myDirectory\photos` .

Ayrıca, seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz `--exclude-path` . Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

#### <a name="use-wildcard-characters"></a>Joker karakter kullan

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-pattern` . Joker karakterleri içeren kısmi adlar belirtin. Adları noktalı virgül () kullanarak ayırın `;` .

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'
```

Ayrıca, seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz `--exclude-pattern` . Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

`--include-pattern`Ve `--exclude-pattern` seçenekleri, yalnızca dosya adları için geçerlidir, yola değildir.  Bir dizin ağacında var olan tüm metin dosyalarını kopyalamak istiyorsanız, tüm `--recursive` dizin ağacını almak için seçeneğini kullanın ve ardından öğesini kullanarak `--include-pattern` `*.txt` tüm metin dosyalarını alın.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Tarih ve saatten sonra değiştirilen dosyaları karşıya yükleme 

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-after` . ISO 8601 biçiminde bir tarih ve saat belirtin (örneğin: `2020-08-19T15:04:00Z` ). 

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'
```

Ayrıntılı başvuru için bkz. [AzCopy kopyası](storage-ref-azcopy-copy.md) başvuru belgeleri.

## <a name="download-files"></a>Dosyaları indirme

Dosya, dizin ve dosya paylaşımlarını yerel bilgisayarınıza indirmek için [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanabilirsiniz.

Bu bölüm aşağıdaki örnekleri içerir:

> [!div class="checklist"]
> * Dosya indirme
> * Bir dizin indirin
> * Bir dizinin içeriğini indirin
> * Belirli dosyaları indir

> [!TIP]
> İsteğe bağlı bayraklar kullanarak indirme işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.
>
> |Senaryo|Bayrak|
> |---|---|
> |Erişim denetim listelerini (ACL 'Ler) dosyalarla birlikte kopyalayın.|**--Preserve-SMB-izinleri** = \[ doğru \| yanlış\]|
> |SMB Özellik bilgilerini dosyalarla birlikte kopyalayın.|**--Preserve-SMB-Info** = \[ doğru \| yanlış\]|
> |Dosyaları otomatik olarak aç.|**--sıkıştırmayı aç**|
> 
> Tüm liste için bkz. [Seçenekler](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> `Content-md5`Bir dosyanın özellik değeri bir karma içeriyorsa, AzCopy indirilen veriler için BIR MD5 karması hesaplar ve dosyanın özelliğinde depolanan MD5 karmasının `Content-md5` hesaplanan karmayla eşleştiğini doğrular. Bu değerler eşleşmezse, `--check-md5=NoCheck` Copy komutuna veya öğesini ekleyerek bu davranışı geçersiz kılmadığınız müddetçe indirme işlemi başarısız olur `--check-md5=LogOnly` .

### <a name="download-a-file"></a>Dosya indirme

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Örnek**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'
```

### <a name="download-a-directory"></a>Bir dizin indirin

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Örnek**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive
```

Bu örnek `C:\myDirectory\myFileShareDirectory` , indirilen tüm dosyaları içeren adlı dizine neden olur.

### <a name="download-the-contents-of-a-directory"></a>Bir dizinin içeriğini indirin

Joker karakter simgesini (*) kullanarak dizinin kendisini kopyalamadan içeriğini indirebilirsiniz.

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Örnek**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'
```

> [!NOTE]
> `--recursive`Dosyaları tüm alt dizinlerde indirmek için bayrağı ekleyin.

### <a name="download-specific-files"></a>Belirli dosyaları indir

Tüm dosya adlarını, joker karakter (*) ile kısmi adları veya tarih ve saatleri kullanarak belirli dosyaları indirebilirsiniz.

#### <a name="specify-multiple-complete-file-names"></a>Birden çok dosya adı belirtin

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-path` . Tek bir dosya adını noktalı virgül () kullanarak ayırın `;` .

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Örnek**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

Bu örnekte, AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` dizini ve `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` dosyayı aktarır. `--recursive`Dizindeki tüm dosyaları aktarma seçeneğini ekleyin `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` .

Ayrıca, seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz `--exclude-path` . Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

#### <a name="use-wildcard-characters"></a>Joker karakter kullan

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-pattern` . Joker karakterleri içeren kısmi adlar belirtin. Adları noktalı virgül () kullanarak ayırın `;` .

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Örnek**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

Ayrıca, seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz `--exclude-pattern` . Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

`--include-pattern`Ve `--exclude-pattern` seçenekleri, yalnızca dosya adları için geçerlidir, yola değildir.  Bir dizin ağacında var olan tüm metin dosyalarını kopyalamak istiyorsanız, tüm `--recursive` dizin ağacını almak için seçeneğini kullanın ve ardından öğesini kullanarak `--include-pattern` `*.txt` tüm metin dosyalarını alın.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Tarih ve saatten sonra değiştirilen dosyaları indirme 

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-after` . ISO-8601 biçiminde bir tarih ve saat belirtin (örneğin: `2020-08-19T15:04:00Z` ). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Örnek**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'
```

Ayrıntılı başvuru için bkz. [AzCopy kopyası](storage-ref-azcopy-copy.md) başvuru belgeleri.

#### <a name="download-from-a-share-snapshot"></a>Bir paylaşma anlık görüntüsünden indir

Bir dosya veya dizinin belirli bir sürümünü, bir paylaşma anlık görüntüsünün **DateTime** değerine başvurarak indirebilirsiniz. Anlık görüntüler hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları için paylaşılan anlık görüntülere genel bakış](../files/storage-snapshots-files.md). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Örnek (dosya Indir)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Örnek (bir dizin Indir)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive
```

## <a name="copy-files-between-storage-accounts"></a>Depolama hesapları arasında dosyalar kopyalama

Daha fazla depolama hesabına dosya kopyalamak için AzCopy kullanabilirsiniz. Kopyalama işlemi zaman uyumludur. Başka bir deyişle komut sonuç döndürdüğünde tüm dosyalar kopyalanmış demektir.

AzCopy, [sunucudan sunucuya](/rest/api/storageservices/put-block-from-url) [API 'ler](/rest/api/storageservices/put-page-from-url)kullanır, bu nedenle veriler doğrudan depolama sunucuları arasında kopyalanır. Bu kopyalama işlemleri bilgisayarınızın ağ bant genişliğini kullanmaz. Ortam değişkeninin değerini ayarlayarak bu işlemlerin verimini artırabilirsiniz `AZCOPY_CONCURRENCY_VALUE` . Daha fazla bilgi için bkz. [eşzamanlılık artırma](storage-use-azcopy-optimize.md#increase-concurrency).

Ayrıca, bir dosyanın belirli sürümlerini bir paylaşma anlık görüntüsünün **DateTime** değerine başvurarak kopyalayabilirsiniz. Anlık görüntüler hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları için paylaşılan anlık görüntülere genel bakış](../files/storage-snapshots-files.md). 

Bu bölüm aşağıdaki örnekleri içerir:

> [!div class="checklist"]
> * Dosyayı başka bir depolama hesabına Kopyala
> * Bir dizini başka bir depolama hesabına kopyalama
> * Dosya paylaşımından başka bir depolama hesabına kopyalama
> * Tüm dosya paylaşımlarını, dizinlerini ve dosyaları başka bir depolama hesabına kopyalama

> [!TIP]
> İsteğe bağlı bayraklar kullanarak kopyalama işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.
>
> |Senaryo|Bayrak|
> |---|---|
> |Erişim denetim listelerini (ACL 'Ler) dosyalarla birlikte kopyalayın.|**--Preserve-SMB-izinleri** = \[ doğru \| yanlış\]|
> |SMB Özellik bilgilerini dosyalarla birlikte kopyalayın.|**--Preserve-SMB-Info** = \[ doğru \| yanlış\]|
> 
> Tüm liste için bkz. [Seçenekler](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Dosyayı başka bir depolama hesabına Kopyala

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

**Örnek (anlık görüntü paylaşma)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

### <a name="copy-a-directory-to-another-storage-account"></a>Bir dizini başka bir depolama hesabına kopyalama

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Örnek (anlık görüntü paylaşma)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Dosya paylaşımından başka bir depolama hesabına kopyalama

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D
```

**Örnek (anlık görüntü paylaşma)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Tüm dosya paylaşımlarını, dizinlerini ve dosyaları başka bir depolama hesabına kopyalama

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Örnek (anlık görüntü paylaşma)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

## <a name="synchronize-files"></a>Dosyaları eşitler

Bir dosya paylaşımının içeriğini başka bir dosya paylaşımıyla eşzamanlı hale getirebilirsiniz. Ayrıca, bir dosya paylaşımındaki dizinin içeriğini başka bir dosya paylaşımında bulunan bir dizinin içeriğiyle de eşzamanlı hale getirebilirsiniz. Eşitleme bir yoldur. Diğer bir deyişle, bu iki uç noktanın hangisinin kaynak olduğunu ve hedefin nerede olduğunu seçersiniz. Eşitleme, sunucuyu sunucu API 'Leri için de kullanır.

> [!NOTE]
> Şu anda, bu senaryo yalnızca hiyerarşik bir ad alanına sahip olmayan hesaplar için desteklenir. AzCopy 'in geçerli sürümü, Azure dosyaları ile BLOB depolama arasında eşitlenmez.

[Eşitleme](storage-ref-azcopy-sync.md) komutu dosya adlarını ve son değiştirilme zaman damgalarını karşılaştırır. `--delete-destination` `true` `prompt` Bu dosyalar artık kaynak dizinde yoksa, hedef dizindeki dosyaları silmek için veya değerine isteğe bağlı bayrağını ayarlayın.

`--delete-destination`Bayrağını olarak ayarlarsanız `true` AzCopy bir istem sağlamadan dosyaları siler. AzCopy bir dosyayı silmesinden önce bir istem görünmesini istiyorsanız, `--delete-destination` bayrağını olarak ayarlayın `prompt` .

> [!TIP]
> İsteğe bağlı bayraklar kullanarak eşitleme işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.
>
> |Senaryo|Bayrak|
> |---|---|
> |Erişim denetim listelerini (ACL 'Ler) dosyalarla birlikte kopyalayın.|**--Preserve-SMB-izinleri** = \[ doğru \| yanlış\]|
> |SMB Özellik bilgilerini dosyalarla birlikte kopyalayın.|**--Preserve-SMB-Info** = \[ doğru \| yanlış\]|
> |Dosyaları bir düzene göre hariç tutun.|**--exclude-Path**|
> |Eşitleme ile ilgili günlük girdilerinizi ne kadar ayrıntılı olarak istediğinizi belirtin.|**--günlük düzeyi** = \[ Uyarı \| hatası \| bilgisi \| yok\]|
> 
> Tüm liste için bkz. [Seçenekler](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Başka bir dosya paylaşımındaki değişikliklerle dosya paylaşma güncelleştirme

Bu komutta görünen ilk dosya paylaşımının kaynağı. İkincisi, hedefin bir biridir.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Örnek**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Başka bir dosya paylaşımındaki bir dizinde bulunan değişikliklerle Dizin güncelleştirme

Bu komutta görüntülenen ilk dizin kaynağıdır. İkincisi, hedefin bir biridir.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Örnek**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Bir dosya paylaşımının paylaşma anlık görüntüsünün içeriğiyle eşleşecek şekilde güncelleştirilmesi

Bu komutta görünen ilk dosya paylaşımının kaynağı. URI 'nin sonunda, dizeyi ve `&sharesnapshot=` ardından anlık görüntünün **Tarih saat** değerini ekleyin.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Örnek**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
``` 

Anlık görüntü paylaşma hakkında daha fazla bilgi için bkz. [Azure dosyaları için paylaşılan anlık görüntülere genel bakış](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerden herhangi birinde daha fazla örnek bulabilirsiniz:

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [Veri aktarma](storage-use-azcopy-v10.md#transfer-data)

Ayarları yapılandırmak, performansı iyileştirmek ve sorunları gidermek için şu makalelere bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)
- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)

