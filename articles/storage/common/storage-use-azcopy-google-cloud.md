---
title: AzCopy ile Google bulut depolamadan Azure depolama 'ya kopyalama | Microsoft Docs
description: Azure depolama 'ya Google Cloud Storage 'dan veri kopyalamak için AzCopy kullanın. AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498348"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>AzCopy (Önizleme) kullanarak Google bulut depolamadaki verileri Azure depolama 'ya kopyalama

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale, AzCopy kullanarak Google bulut depolamadan Azure Blob depolamaya nesneleri, dizinleri ve demetleri kopyalamanıza yardımcı olur.

> [!IMPORTANT]
> Google Cloud Storage 'dan Azure Storage 'a veri kopyalama Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Yetkilendirme kimlik bilgilerini nasıl sağlayacağınızı seçin

* Azure depolama ile yetkilendirmek için Azure Active Directory (AD) veya paylaşılan erişim Imzası (SAS) belirteci kullanın.

* Google Cloud Storage 'ı yetkilendirmek için bir hizmet hesabı anahtarı kullanın.

### <a name="authorize-with-azure-storage"></a>Azure depolama ile yetkilendirme

AzCopy ['i indirmek Için AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md) makalesini inceleyin ve depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayabileceğiniz yolları hakkında bilgi edinin.

> [!NOTE] 
> Bu makaledeki örneklerde Azure Active Directory (Azure AD) kullanarak yetkilendirme kimlik bilgileri sağladığınızı varsayalım.
>
> Blob verilerine erişim yetkisi vermek için bir SAS belirteci kullanıyorsanız, bu belirteci her AzCopy komutunda kaynak URL 'sine ekleyebilirsiniz. Örneğin: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Google Cloud Storage ile yetkilendirme

Google Cloud Storage 'ı yetkilendirmek için bir hizmet hesabı anahtarı kullanacaksınız. Hizmet hesabı anahtarı oluşturma hakkında daha fazla bilgi için bkz. [hizmet hesabı anahtarları oluşturma ve yönetme](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Bir hizmet anahtarı elde ettikten sonra, `GOOGLE_APPLICATION_CREDENTIALS` ortam değişkenini hizmet hesabı anahtar dosyasına mutlak yol olarak ayarlayın:

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Nesneleri, dizinleri ve demetleri kopyalama

AzCopy, [URL API 'Den put bloğunu](/rest/api/storageservices/put-block-from-url) kullanır, bu nedenle veriler doğrudan Google bulut depolama ve depolama sunucuları arasında kopyalanır. Bu kopyalama işlemleri bilgisayarınızın ağ bant genişliğini kullanmaz.

> [!TIP]
> Bu bölümdeki örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

 Bu örnekler, hiyerarşik bir ad alanı olan hesaplarla de çalışır. [Data Lake Storage çoklu protokol erişimi](../blobs/data-lake-storage-multi-protocol-access.md) , bu HESAPLARDA aynı URL sözdizimini () kullanmanıza olanak sağlar `blob.core.windows.net` . 

### <a name="copy-an-object"></a>Nesne kopyalama

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Örnek**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Bir dizini kopyalama

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Bu örnek, `--recursive` dosyaları tüm alt dizinlerde kopyalamak için bayrağını ekler.

### <a name="copy-the-contents-of-a-directory"></a>Bir dizinin içeriğini kopyalama

Joker karakter sembolünü (*) kullanarak, bir dizinin içeriğini içeren dizini kopyalayarak kopyalayabilirsiniz.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Bulut depolama demetini kopyalama

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Google Cloud projesindeki tüm demetleri Kopyala 

İlk olarak, `GOOGLE_CLOUD_PROJECT` Google Cloud projesinin proje kimliği ' ni ayarlayın.

`blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Google Cloud projesindeki demetlerin bir alt kümesini kopyalama 

İlk olarak, `GOOGLE_CLOUD_PROJECT` Google Cloud projesinin proje kimliği ' ni ayarlayın.

Demet adında bir joker karakter simgesi (*) kullanarak demetlerin bir alt kümesini kopyalayın. `blob.core.windows.net`Hiyerarşik bir ad alanı olan hesaplar için aynı URL söz dizimini () kullanın.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Örnek**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Demet adlandırma kurallarında farkları işleme

Google Cloud Storage 'ın, Azure Blob kapsayıcılarıyla karşılaştırıldığında demet adları için farklı adlandırma kuralları kümesi vardır. [Buradan](https://cloud.google.com/storage/docs/naming-buckets)hakkında bilgi edinebilirsiniz. Bir demet grubunu bir Azure depolama hesabına kopyalamayı seçerseniz, adlandırma farklılıkları nedeniyle kopyalama işlemi başarısız olabilir.

AzCopy, ortaya çıkabilecek en yaygın sorunların üçünü de gerçekleştirir; nokta, ardışık çizgiler ve alt çizgi içeren demetleri içeren demetler. Google Cloud Storage demet adları nokta ve ardışık tireler içerebilir, ancak Azure 'da bir kapsayıcı olamaz. AzCopy, ardışık çizgilerden oluşan sayıları temsil eden bir sayı ile kısa çizgilerden ve ardışık çizgilerden oluşan noktaları değiştirir (örneğin: adlı bir demet `my----bucket` `my-4-bucket` . Demet adının bir alt çizgi () varsa `_` AzCopy alt çizgiyi bir tire ile değiştirir (örneğin: adlı bir demet, `my_bucket` olur `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Nesne adlandırma kurallarındaki farkları işleme

Google Cloud Storage 'ın, Azure bloblarına kıyasla, nesne adları için farklı adlandırma kuralları kümesi vardır. [Buradan](https://cloud.google.com/storage/docs/naming-objects)hakkında bilgi edinebilirsiniz.

Azure depolama, nesne adlarının (veya sanal dizin yolundaki herhangi bir segmentin) sondaki noktalarla sonlanmasına (örneğin) izin vermez `my-bucket...` . Kopyalama işlemi gerçekleştirildiğinde sondaki noktalar atılır. 

## <a name="handle-differences-in-object-metadata"></a>Nesne meta verilerinde farkları işleme

Google Cloud Storage ve Azure, nesne anahtarlarının adlarındaki farklı karakter kümelerine izin veriyor. Google Cloud Storage 'daki meta veriler hakkında [buradan](https://cloud.google.com/storage/docs/metadata)bilgi edinebilirsiniz. Azure tarafında, blob nesne anahtarları [C# tanımlayıcıları](/dotnet/csharp/language-reference/)için adlandırma kurallarına uyar.

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
- [Örnekler: Amazon S3 demetleri](storage-use-azcopy-s3.md)
- [Örnekler: Azure dosyaları](storage-use-azcopy-files.md)
- [Öğretici: AzCopy kullanarak şirket içi verileri bulut depolamasına taşıma](storage-use-azcopy-migrate-on-premises-data.md)

Ayarları yapılandırmak, performansı iyileştirmek ve sorunları gidermek için şu makalelere bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)
- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)