---
title: AzCopy ile v10 arasındaki kullanarak Azure Blob depolamaya dosya yükleme | Microsoft Docs
description: Bu makale, Azure Blob depolama alanına dosya yüklemeye yardımcı olan AzCopy örnek komutlarının bir koleksiyonunu içerir.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502921"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>AzCopy kullanarak Azure Blob depolamaya dosya yükleme

AzCopy ile v10 arasındaki komut satırı yardımcı programını kullanarak blob depolamaya dosya ve Dizin yükleyebilirsiniz. 

Blob 'ları indirme, blob depolamayla eşitleme veya hesaplar arasında blobları kopyalama gibi diğer görev türlerine yönelik örnekleri görmek için, bu makalenin [sonraki adımları](#next-steps) bölümünde sunulan bağlantılara bakın.

## <a name="get-started"></a>başlarken

AzCopy ['i indirmek Için AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md) makalesini inceleyin ve depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayabileceğiniz yolları hakkında bilgi edinin.

> [!NOTE] 
> Bu makaledeki örneklerde Azure Active Directory (Azure AD) kullanarak yetkilendirme kimlik bilgileri sağladığınızı varsayalım.
>
> Blob verilerine erişim yetkisi vermek için bir SAS belirteci kullanıyorsanız, bu belirteci her AzCopy komutunda kaynak URL 'sine ekleyebilirsiniz. Örneğin: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı oluşturmak için [AzCopy Make](storage-ref-azcopy-make.md) komutunu kullanabilirsiniz.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

Bu bir tablo örneğidir:

**Syntax**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Örnek**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Ayrıntılı başvuru belgeleri için bkz. [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Dosyayı karşıya yükleme

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak bir dosyayı karşıya yükleyin.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

Dosya yolu veya dosya adında herhangi bir yerde joker karakter sembolünü (*) kullanarak da bir dosyayı karşıya yükleyebilirsiniz. Örneğin: `'C:\myDirectory\*.txt'` , veya `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Bir dizini karşıya yükle

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak bir dizini karşıya yükleyin. 

Bu örnekte bir dizin (ve bu dizindeki tüm dosyalar) bir blob kapsayıcısına kopyalanır. Sonuç, kapsayıcıda aynı ada sahip bir dizindir.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

Kapsayıcı içindeki bir dizine kopyalamak için, komut dizinizdeki bu dizinin adını belirtmeniz yeterlidir.

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Kapsayıcıda mevcut olmayan bir dizinin adını belirtirseniz AzCopy, bu adla yeni bir dizin oluşturur.

## <a name="upload-directory-contents"></a>Dizin içeriğini karşıya yükle

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak bir dizinin içeriğini karşıya yükleyin. İçeriği içeren dizinin kendisini kopyalamadan karşıya yüklemek için joker karakter sembolünü (*) kullanın.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

`--recursive`Tüm alt dizinlere dosya yükleme bayrağını ekleyin.

## <a name="upload-specific-files"></a>Belirli dosyaları karşıya yükle

Tüm dosya adlarını, joker karakter (*) ile kısmi adları veya tarih ve saatleri kullanarak belirli dosyaları karşıya yükleyebilirsiniz.

> [!TIP]
> Bu örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

### <a name="specify-multiple-complete-file-names"></a>Birden çok dosya adı belirtin

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-path` . Tek bir dosya adını noktalı virgül () kullanarak ayırın `;` .

**Syntax** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

Bu örnekte, AzCopy `C:\myDirectory\photos` dizini ve `C:\myDirectory\documents\myFile.txt` dosyayı aktarır. `--recursive`Dizindeki tüm dosyaları aktarma seçeneğini ekleyin `C:\myDirectory\photos` .

Ayrıca, seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz `--exclude-path` . Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

### <a name="use-wildcard-characters"></a>Joker karakter kullan

Seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-pattern` . Joker karakterleri içeren kısmi adlar belirtin. Bir semicolin () kullanarak adları ayırın `;` . 

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

Ayrıca, seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz `--exclude-pattern` . Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

`--include-pattern`Ve `--exclude-pattern` seçenekleri, yalnızca dosya adları için geçerlidir, yola değildir.  Bir dizin ağacında var olan tüm metin dosyalarını kopyalamak istiyorsanız, tüm `–recursive` dizin ağacını almak için seçeneğini kullanın ve ardından öğesini kullanarak `–include-pattern` `*.txt` tüm metin dosyalarını alın.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Tarih ve saatten önce veya sonra değiştirilen dosyaları karşıya yükleme 

Veya seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın `--include-before` `--include-after` . ISO-8601 biçiminde bir tarih ve saat belirtin (örneğin: `2020-08-19T15:04:00Z` ). 

Aşağıdaki örnekler, belirtilen tarihte veya sonra değiştirilen dosyaları karşıya yükler.

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Örnek**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Örnek (hiyerarşik ad alanı)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Ayrıntılı başvuru için bkz. [AzCopy kopyası](storage-ref-azcopy-copy.md) başvuru belgeleri.

## <a name="upload-with-index-tags"></a>Dizin etiketleriyle karşıya yükle

Hedef bloba bir dosyayı karşıya yükleyebilir ve [BLOB dizin etiketleri (Önizleme)](../blobs/storage-manage-find-blobs.md) ekleyebilirsiniz.  

Azure AD yetkilendirmesi kullanıyorsanız, güvenlik sorumlusuna [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolü atanmalıdır veya `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Kaynak sağlayıcısı işlemine](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) özel bir Azure rolü aracılığıyla izin verilmelidir. Paylaşılan erişim Imzası (SAS) belirteci kullanıyorsanız, bu belirtecin SAS izni aracılığıyla Blobun etiketlerine erişim sağlaması gerekir `t` .

Etiketler eklemek için, `--blob-tags` URL kodlamalı anahtar-değer çiftiyle birlikte seçeneğini kullanın. Örneğin, anahtar `my tag` ve bir değer eklemek için `my tag value` , `--blob-tags='my%20tag=my%20tag%20value'` hedef parametreye eklersiniz. 

Bir ampersan () kullanarak birden çok dizin etiketini ayırın `&` .  Örneğin, bir anahtar `my second tag` ve değer eklemek istiyorsanız `my second tag value` , tüm seçenek dizesi olur `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Aşağıdaki örneklerde seçeneğinin nasıl kullanılacağı gösterilmektedir `--blob-tags` .

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Karşıya dosya yükleme**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Bir dizini karşıya yükle**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Dizin içeriğini karşıya yükle**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Kaynak için bir dizin belirtirseniz, hedefe kopyalanmış olan tüm Bloblar komutta belirttiğiniz etiketlere sahip olur.

## <a name="upload-with-optional-flags"></a>İsteğe bağlı bayraklarla karşıya yükle

İsteğe bağlı bayraklar kullanarak karşıya yükleme işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.

|Senaryo|Bayrak|
|---|---|
|Dosyaları Append Ekleme Blobları veya Sayfa Blobları olarak karşıya yükleyin.|**--BLOB-tür** = \[ BlockBlob \| pageblob \| appendblob\]|
|Belirli bir erişim katmanına (arşiv katmanı gibi) yükleyin.|**--Block-blob-Tier** = \[ Hiçbiri \| sık erişimli olmayan \| \| Arşiv\]|

Tüm liste için bkz. [Seçenekler](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde daha fazla örnek bulun:

- [Örnekler: Karşıdan Yükle](storage-use-azcopy-blobs-download.md)
- [Örnekler: Hesaplar arasında kopyala](storage-use-azcopy-blobs-copy.md)
- [Örnekler: Eşitle](storage-use-azcopy-blobs-synchronize.md)
- [Örnekler: Amazon S3 demetleri](storage-use-azcopy-s3.md)
- [Örnekler: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Örnekler: Azure dosyaları](storage-use-azcopy-files.md)
- [Öğretici: AzCopy kullanarak şirket içi verileri bulut depolamasına taşıma](storage-use-azcopy-migrate-on-premises-data.md)

Ayarları yapılandırmak, performansı iyileştirmek ve sorunları gidermek için şu makalelere bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)
- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)