---
title: AzCopy ile v10 arasındaki kullanarak Azure Blob depolama alanına veya buradan veri aktarma | Microsoft Docs
description: Bu makale, kapsayıcılar oluşturmanıza, dosyaları kopyalamaya ve yerel dosya sistemleri ile kapsayıcılar arasında dizin eşitlemenize yardımcı olan AzCopy örnek komutlarının bir koleksiyonunu içerir.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f5aafbb22ecbff416d90aa5b98eb027c33872b35
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048550"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy ve BLOB Storage ile veri aktarma

AzCopy, depolama hesaplarına veri kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale, blob depolamayla çalışan örnek komutlar içerir.

## <a name="get-started"></a>Başlarken

AzCopy ['i indirmek Için AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md) makalesini inceleyin ve depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayabileceğiniz yolları hakkında bilgi edinin.

> [!NOTE]
> Bu makaledeki örneklerde, kimlik doğrulamasının `AzCopy login` komutunu kullanarak doğruladığınızı varsayalım. AzCopy daha sonra blob depolamadaki verilere erişim yetkisi vermek için Azure AD hesabınızı kullanır.
>
> Blob verilerine erişim yetkisi vermek için bir SAS belirteci kullanıyorsanız, bu belirteci her AzCopy komutunda kaynak URL 'sine ekleyebilirsiniz.
>
> Örneğin: `'https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>'`.

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd. exe) dışında tüm komut kabukleriyle tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd. exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

Bir kapsayıcı oluşturmak için [AzCopy Make](storage-ref-azcopy-make.md) komutunu kullanabilirsiniz. Bu bölümdeki örneklerde `mycontainer`adlı bir kapsayıcı oluşturulur.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Örnek** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Ayrıntılı başvuru belgeleri için bkz. [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Dosyaları karşıya yükleme

Yerel bilgisayarınızdan dosya ve Dizin yüklemek için [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanabilirsiniz.

Bu bölüm aşağıdaki örnekleri içerir:

> [!div class="checklist"]
> * Dosyayı karşıya yükleme
> * Bir dizini karşıya yükle
> * Bir dizinin içeriğini karşıya yükleme 
> * Belirli dosyaları karşıya yükle

Ayrıntılı başvuru belgeleri için bkz. [AzCopy kopyası](storage-ref-azcopy-copy.md).

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd. exe) dışında tüm komut kabukleriyle tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd. exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

### <a name="upload-a-file"></a>Dosyayı karşıya yükleme

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Örnek** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Dosya yolu veya dosya adında herhangi bir yerde joker karakter sembolünü (*) kullanarak da bir dosyayı karşıya yükleyebilirsiniz. Örneğin: `'C:\myDirectory\*.txt'`veya `C:\my*\*.txt`.

> [!NOTE]
> AzCopy varsayılan olarak blok bloblarına veri yükler. Dosyaları ekleme Blobları olarak yüklemek için veya sayfa Blobları `--blob-type=[BlockBlob|PageBlob|AppendBlob]`bayrağını kullanır.

### <a name="upload-a-directory"></a>Bir dizini karşıya yükle

Bu örnekte bir dizin (ve bu dizindeki tüm dosyalar) bir blob kapsayıcısına kopyalanır. Sonuç, kapsayıcıda aynı ada sahip bir dizindir.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Örnek** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Kapsayıcı içindeki bir dizine kopyalamak için, komut dizinizdeki bu dizinin adını belirtmeniz yeterlidir.

|    |     |
|--------|-----------|
| **Örnek** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Kapsayıcıda varolmayan bir dizinin adını belirtirseniz AzCopy, bu adla yeni bir dizin oluşturur.

### <a name="upload-the-contents-of-a-directory"></a>Bir dizinin içeriğini karşıya yükleme

Joker karakter sembolünü (*) kullanarak, içeren dizinin kendisini kopyalamadan bir dizinin içeriğini karşıya yükleyebilirsiniz.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Örnek** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Tüm alt dizinlerde dosyaları karşıya yüklemek için `--recursive` bayrağını ekleyin.

### <a name="upload-specific-files"></a>Belirli dosyaları karşıya yükle

Tüm dosya adlarını belirtebilir veya joker karakterler (*) ile kısmi adlar kullanabilirsiniz.

#### <a name="specify-multiple-complete-file-names"></a>Birden çok dosya adı belirtin

`--include-path` seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın. Tek bir dosya adını noktalı virgül (`;`) kullanarak ayırın.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Örnek** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Bu örnekte, AzCopy `C:\myDirectory\photos` dizinini ve `C:\myDirectory\documents\myFile.txt` dosyasını aktarır. `C:\myDirectory\photos` dizinindeki tüm dosyaları aktarmak için `--recursive` seçeneğini dahil etmeniz gerekir.

Ayrıca, `--exclude-path` seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz. Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

#### <a name="use-wildcard-characters"></a>Joker karakter kullan

`--include-pattern` seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın. Joker karakterleri içeren kısmi adlar belirtin. Bir semicolin (`;`) kullanarak adları ayırın. 

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Örnek** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Ayrıca, `--exclude-pattern` seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz. Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

`--include-pattern` ve `--exclude-pattern` seçenekleri yalnızca dosya adlarıyla uygulanır, yola değildir.  Bir dizin ağacında var olan tüm metin dosyalarını kopyalamak istiyorsanız, tüm dizin ağacını almak için `–recursive` seçeneğini kullanın ve ardından `–include-pattern` ve tüm metin dosyalarını almak için `*.txt` belirtin.

## <a name="download-files"></a>Dosyaları indirme

Blob, dizin ve kapsayıcıları yerel bilgisayarınıza indirmek için [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanabilirsiniz.

Bu bölüm aşağıdaki örnekleri içerir:

> [!div class="checklist"]
> * Dosya indirme
> * Bir dizin indirin
> * Bir dizinin içeriğini indirin
> * Belirli dosyaları indir

> [!NOTE]
> Bir Blobun `Content-md5` Özellik değeri bir karma içeriyorsa, AzCopy indirilen veriler için bir MD5 karması hesaplar ve BLOB 'un `Content-md5` özelliğinde depolanan MD5 karmasının hesaplanan karmayla eşleştiğini doğrular. Bu değerler eşleşmezse, Copy komutuna `--check-md5=NoCheck` veya `--check-md5=LogOnly` ekleyerek bu davranışı geçersiz kılmadığınız müddetçe indirme işlemi başarısız olur.

Ayrıntılı başvuru belgeleri için bkz. [AzCopy kopyası](storage-ref-azcopy-copy.md).

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd. exe) dışında tüm komut kabukleriyle tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd. exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

### <a name="download-a-file"></a>Dosya indirme

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Örnek** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Bir dizin indirin

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Örnek** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory 'C:\myDirectory'  --recursive` |

Bu örnek, indirilen tüm dosyaları içeren `C:\myDirectory\myBlobDirectory` adlı bir dizine neden olur.

### <a name="download-the-contents-of-a-directory"></a>Bir dizinin içeriğini indirin

Joker karakter sembolünü (*) kullanarak, bir dizinin içeriğini içeren dizini kopyalamadan indirebilirsiniz.

> [!NOTE]
> Şu anda, bu senaryo yalnızca hiyerarşik bir ad alanına sahip olmayan hesaplar için desteklenir.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Örnek** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Tüm alt dizinlerde dosyaları indirmek için `--recursive` bayrağını ekleyin.

### <a name="download-specific-files"></a>Belirli dosyaları indir

Tüm dosya adlarını belirtebilir veya joker karakterler (*) ile kısmi adlar kullanabilirsiniz.

#### <a name="specify-multiple-complete-file-names"></a>Birden çok dosya adı belirtin

`--include-path` seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın. Tek dosya adlarını bir semicolin (`;`) kullanarak ayırın.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Örnek** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Bu örnekte, AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` dizinini ve `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` dosyasını aktarır. `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` dizinindeki tüm dosyaları aktarmak için `--recursive` seçeneğini dahil etmeniz gerekir.

Ayrıca, `--exclude-path` seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz. Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

#### <a name="use-wildcard-characters"></a>Joker karakter kullan

`--include-pattern` seçeneğiyle [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın. Joker karakterleri içeren kısmi adlar belirtin. Bir semicolin (`;`) kullanarak adları ayırın.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Örnek** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Örnek** (hiyerarşik ad alanı) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Ayrıca, `--exclude-pattern` seçeneğini kullanarak dosyaları dışarıda bırakabilirsiniz. Daha fazla bilgi edinmek için bkz. [AzCopy kopyalama](storage-ref-azcopy-copy.md) başvuru belgeleri.

`--include-pattern` ve `--exclude-pattern` seçenekleri yalnızca dosya adlarıyla uygulanır, yola değildir.  Bir dizin ağacında var olan tüm metin dosyalarını kopyalamak istiyorsanız, tüm dizin ağacını almak için `–recursive` seçeneğini kullanın ve ardından `–include-pattern` ve tüm metin dosyalarını almak için `*.txt` belirtin.

## <a name="copy-blobs-between-storage-accounts"></a>Depolama hesapları arasında blobları kopyalama

Diğer depolama hesaplarına Blobları kopyalamak için AzCopy kullanabilirsiniz. Kopyalama işlemi, komutun döndürdüğü zaman zaman uyumludur, yani tüm dosyalar kopyalanmış olduğunu gösterir. 

AzCopy, [sunucudan sunucuya](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API 'ler](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)kullanır, bu nedenle veriler doğrudan depolama sunucuları arasında kopyalanır. Bu kopyalama işlemleri bilgisayarınızın ağ bant genişliğini kullanmaz. `AZCOPY_CONCURRENCY_VALUE` ortam değişkeninin değerini ayarlayarak bu işlemlerin verimini artırabilirsiniz. Daha fazla bilgi için bkz. [aktarım hızını iyileştirme](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Bu senaryoda, geçerli sürümde aşağıdaki sınırlamalar bulunur.
>
> - Yalnızca hiyerarşik bir ad alanı olmayan hesaplar desteklenir.
> - Her kaynak URL 'ye bir SAS belirteci eklemeniz gerekir. Azure Active Directory (AD) kullanarak yetkilendirme kimlik bilgilerini sağlarsanız, SAS belirtecini yalnızca hedef URL 'den atlayabilirsiniz.
>-  Premium Blok Blob depolama hesapları, erişim katmanlarını desteklemez. `s2s-preserve-access-tier` `false` olarak ayarlayarak kopyalama işleminden bir Blobun erişim katmanını atlayın (örneğin: `--s2s-preserve-access-tier=false`).

Bu bölüm aşağıdaki örnekleri içerir:

> [!div class="checklist"]
> * Bir blobu başka bir depolama hesabına kopyalama
> * Bir dizini başka bir depolama hesabına kopyalama
> * Kapsayıcıyı başka bir depolama hesabına kopyalama
> * Tüm kapsayıcıları, dizinleri ve dosyaları başka bir depolama hesabına Kopyala

Ayrıntılı başvuru belgeleri için bkz. [AzCopy kopyası](storage-ref-azcopy-copy.md).

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd. exe) dışında tüm komut kabukleriyle tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd. exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

### <a name="copy-a-blob-to-another-storage-account"></a>Bir blobu başka bir depolama hesabına kopyalama

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Örnek** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Bir dizini başka bir depolama hesabına kopyalama

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Örnek** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kapsayıcıyı başka bir depolama hesabına kopyalama

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Örnek** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Tüm kapsayıcıları, dizinleri ve Blobları başka bir depolama hesabına Kopyala

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Örnek** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Dosyaları eşitler

Bir yerel dosya sisteminin içeriğini bir blob kapsayıcısı ile eşzamanlı hale getirebilirsiniz. Kapsayıcıları ve sanal dizinleri birbiriyle de eşzamanlı hale getirebilirsiniz. Eşitleme tek yönlü. Diğer bir deyişle, bu iki uç noktanın hangisinin kaynak olduğunu ve hedefin nerede olduğunu seçersiniz. Eşitleme, sunucuyu sunucu API 'Leri için de kullanır.

> [!NOTE]
> Şu anda, bu senaryo yalnızca hiyerarşik bir ad alanına sahip olmayan hesaplar için desteklenir. AzCopy 'in geçerli sürümü diğer kaynak ve hedefler arasında eşitlenmez (örneğin: dosya depolama veya Amazon Web Services (AWS) S3 demetleri).

[Eşitleme](storage-ref-azcopy-sync.md) komutu dosya adlarını ve son değiştirilme zaman damgalarını karşılaştırır. Bu dosyalar artık kaynak dizinde yoksa, hedef dizindeki dosyaları silmek için `--delete-destination` isteğe bağlı bayrağını `true` veya `prompt` değerine ayarlayın.

`--delete-destination` bayrağını `true` AzCopy olarak ayarlarsanız, bir istem sağlamadan dosyaları siler. AzCopy bir dosyayı silmesinden önce bir istem görüntülenmesini istiyorsanız `--delete-destination` bayrağını `prompt`olarak ayarlayın.

> [!NOTE]
> Yanlışlıkla silinmeleri engellemek için `--delete-destination=prompt|true` bayrağını kullanmadan önce [geçici silme](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) özelliğini etkinleştirdiğinizden emin olun.

Ayrıntılı başvuru belgeleri için bkz. [AzCopy eşitleme](storage-ref-azcopy-sync.md).

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd. exe) dışında tüm komut kabukleriyle tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd. exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Yerel dosya sistemindeki değişikliklerle bir kapsayıcıyı güncelleştirme

Bu durumda, kapsayıcı hedef ve yerel dosya sistemi kaynağıdır.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Örnek** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Bir kapsayıcıda değişikliklerle yerel bir dosya sistemini güncelleştirme

Bu durumda, yerel dosya sistemi hedefdir ve kapsayıcı kaynağıdır.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Örnek** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |
|

### <a name="update-a-container-with-changes-in-another-container"></a>Başka bir kapsayıcıdaki değişikliklerle bir kapsayıcıyı güncelleştirme

Bu komutta görüntülenen ilk kapsayıcı kaynağıdır. İkincisi, hedefin bir biridir.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Örnek** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Başka bir dosya paylaşımındaki bir dizinde bulunan değişikliklerle Dizin güncelleştirme

Bu komutta görüntülenen ilk dizin kaynağıdır. İkincisi, hedefin bir biridir.

|    |     |
|--------|-----------|
| **Söz dizimi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Örnek** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerden herhangi birinde daha fazla örnek bulabilirsiniz:

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)

- [Öğretici: AzCopy kullanarak şirket içi verileri bulut depolamaya geçirme](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)

- [AzCopy ve Amazon S3 demetleri ile veri aktarma](storage-use-azcopy-s3.md)

- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)
