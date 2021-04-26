---
title: AzCopy kullanarak Amazon S3 'ten Azure depolama 'ya veri kopyalama | Microsoft Docs
description: Amazon S3 ' dan Azure depolama 'ya veri kopyalamak için AzCopy kullanın. AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac73d0e57377a8922691ea06c8de3df5ef577680
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502445"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>AzCopy kullanarak Amazon S3 'ten Azure depolama 'ya veri kopyalama

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale, AzCopy kullanarak Amazon Web Services (AWS) S3 ' den Azure Blob depolama alanına nesneleri, dizinleri ve demetleri kopyalamanıza yardımcı olur.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Yetkilendirme kimlik bilgilerini nasıl sağlayacağınızı seçin

* Azure depolama ile yetkilendirmek için Azure Active Directory (AD) veya paylaşılan erişim Imzası (SAS) belirteci kullanın.

* AWS S3 ile yetkilendirmek için bir AWS erişim anahtarı ve gizli bir erişim anahtarı kullanın.

### <a name="authorize-with-azure-storage"></a>Azure depolama ile yetkilendirme

AzCopy 'i indirmek için [AzCopy kullanmaya başlama](storage-use-azcopy-v10.md) makalesine bakın ve depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayacağınız seçin.

> [!NOTE]
> Bu makaledeki örneklerde, komutunu kullanarak kimliğinizi doğruladığınızı varsayalım `AzCopy login` . AzCopy daha sonra blob depolamadaki verilere erişim yetkisi vermek için Azure AD hesabınızı kullanır.
>
> Blob verilerine erişim yetkisi vermek için bir SAS belirteci kullanıyorsanız, bu belirteci her AzCopy komutunda kaynak URL 'sine ekleyebilirsiniz.
>
> Örneğin: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>AWS S3 ile yetkilendirme

AWS erişim anahtarınızı ve gizli erişim anahtarınızı toplayın ve ardından bu ortam değişkenlerini ayarlayın:

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Nesneleri, dizinleri ve demetleri kopyalama

AzCopy, [URL API 'Den put bloğunu](/rest/api/storageservices/put-block-from-url) kullanır, bu nedenle veriler doğrudan AWS S3 ve depolama sunucuları arasında kopyalanır. Bu kopyalama işlemleri bilgisayarınızın ağ bant genişliğini kullanmaz.

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

 Bu örnekler, hiyerarşik bir ad alanı olan hesaplarla de çalışır. [Data Lake Storage çoklu protokol erişimi](../blobs/data-lake-storage-multi-protocol-access.md) , bu HESAPLARDA aynı URL sözdizimini () kullanmanıza olanak sağlar `blob.core.windows.net` . 

### <a name="copy-an-object"></a>Nesne kopyalama

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Örnek**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> Bu makaledeki örnekler AWS S3 demetleri için yol stili URL 'Leri kullanır (örneğin: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Ayrıca, sanal barındırılan stil URL 'Leri de kullanabilirsiniz (örneğin: `http://bucket.s3.amazonaws.com` ). 
>
> Demetlerin sanal barındırılması hakkında daha fazla bilgi edinmek için bkz. [demetlerin sanal barındırılması](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Bir dizini kopyalama

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Bu örnek, `--recursive` dosyaları tüm alt dizinlerde kopyalamak için bayrağını ekler.

### <a name="copy-the-contents-of-a-directory"></a>Bir dizinin içeriğini kopyalama

Joker karakter sembolünü (*) kullanarak, bir dizinin içeriğini içeren dizini kopyalayarak kopyalayabilirsiniz.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>Demeti kopyalama

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>Tüm bölgelerdeki tüm demetleri Kopyala

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Belirli bir S3 bölgesindeki tüm demetleri Kopyala

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>Nesne adlandırma kurallarındaki farkları işleme

AWS S3, demet adları için Azure Blob kapsayıcılarıyla karşılaştırıldığında farklı bir adlandırma kuralları kümesine sahiptir. [Buradan](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)hakkında bilgi edinebilirsiniz. Bir demet grubunu bir Azure depolama hesabına kopyalamayı seçerseniz, adlandırma farklılıkları nedeniyle kopyalama işlemi başarısız olabilir.

AzCopy, ortaya çıkabilecek en yaygın sorunların ikisini de gerçekleştirir; ardışık kısa çizgi içeren noktalar ve demetleri içeren demetler. AWS S3 demet adları nokta ve ardışık tireler içerebilir, ancak Azure 'da bir kapsayıcı olamaz. AzCopy, ardışık çizgilerden oluşan sayıları temsil eden bir sayı ile kısa çizgilerden ve ardışık çizgilerden oluşan noktaları değiştirir (örneğin: adlı bir demet `my----bucket` `my-4-bucket` . 

Ayrıca, dosya üzerinde AzCopy kopyaları olarak, adlandırma çakışmalarını denetler ve bunları çözmeye çalışır. Örneğin, ve adında demetler varsa `bucket-name` `bucket.name` AzCopy, `bucket.name` ilk ve sonra adlı bir demetini çözer `bucket-name` `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Nesne meta verilerinde farkları işleme

AWS S3 ve Azure, nesne anahtarlarının adlarındaki farklı karakter kümelerine izin veriyor. AWS S3 tarafından [burada](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)kullanılan karakterler hakkında bilgi edinebilirsiniz. Azure tarafında, blob nesne anahtarları [C# tanımlayıcıları](/dotnet/csharp/language-reference/)için adlandırma kurallarına uyar.

AzCopy komutunun bir parçası olarak `copy` , `s2s-handle-invalid-metadata` dosyanın meta verilerinin uyumsuz anahtar adlarını içerdiği dosyaları nasıl işlemek istediğinizi belirten isteğe bağlı bayrak için bir değer sağlayabilirsiniz. Aşağıdaki tabloda her bayrak değeri açıklanmaktadır.

| Bayrak değeri | Açıklama  |
|--------|-----------|
| **Excludeifgeçersiz** | (Varsayılan seçenek) Meta veriler aktarılan nesneye dahil değildir. AzCopy bir uyarı kaydeder. |
| **Failifgeçersiz** | Nesneler kopyalanmaz. AzCopy bir hatayı günlüğe kaydeder ve aktarım özetinde görüntülenen başarısız olan sayıma bu hatayı ekler.  |
| **Renameifgeçersiz**  | AzCopy geçersiz meta veri anahtarını çözer ve çözümlenen meta veri anahtar değer çiftini kullanarak nesneyi Azure 'a kopyalar. AzCopy 'ın nesne anahtarlarını yeniden adlandırma hakkında tam olarak hangi adımların alınacağını öğrenmek için aşağıdaki aşağıdaki [nesne anahtarlarını](#rename-logic) yeniden adlandırma bölümüne bakın. AzCopy anahtarı yeniden adlandıramaz, nesne kopyalanmaz. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>AzCopy nesne anahtarlarını yeniden adlandırmalar

AzCopy şu adımları gerçekleştirir:

1. Geçersiz karakterleri ' _ ' ile değiştirir.

2. Dizeyi `rename_` Yeni bir geçerli anahtarın başlangıcına ekler.

   Bu anahtar, özgün meta veri **değerini** kaydetmek için kullanılacaktır.

3. Dizeyi `rename_key_` Yeni bir geçerli anahtarın başlangıcına ekler.
   Bu anahtar, özgün meta veriler geçersiz **anahtarını** kaydetmek için kullanılacaktır.
   Meta veri anahtarı BLOB depolama hizmetindeki bir değer olarak korunduğu için Azure tarafında meta verileri kurtarmayı denemek üzere bu anahtarı kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde daha fazla örnek bulun:

- [Örnekler: Karşıya Yükle](storage-use-azcopy-blobs-upload.md)
- [Örnekler: Karşıdan Yükle](storage-use-azcopy-blobs-download.md)
- [Örnekler: Hesaplar arasında kopyala](storage-use-azcopy-blobs-copy.md)
- [Örnekler: Eşitle](storage-use-azcopy-blobs-synchronize.md)
- [Örnekler: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Örnekler: Azure dosyaları](storage-use-azcopy-files.md)
- [Öğretici: AzCopy kullanarak şirket içi verileri bulut depolamasına taşıma](storage-use-azcopy-migrate-on-premises-data.md)

Ayarları yapılandırmak, performansı iyileştirmek ve sorunları gidermek için şu makalelere bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)
- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)
