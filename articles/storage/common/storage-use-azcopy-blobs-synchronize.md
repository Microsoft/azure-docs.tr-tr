---
title: AzCopy ile v10 arasındaki kullanarak Azure Blob depolama ile eşitlemeyi | Microsoft Docs
description: Bu makale, Azure Blob depolamayla eşitlemenize yardımcı olan AzCopy örnek komutlarının bir koleksiyonunu içerir.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502938"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>AzCopy kullanarak Azure Blob depolama ile eşitlemeyi

AzCopy ile v10 arasındaki komut satırı yardımcı programını kullanarak yerel depolamayı Azure Blob depolama ile eşitlenebilir. 

Bir yerel dosya sisteminin içeriğini bir blob kapsayıcısı ile eşzamanlı hale getirebilirsiniz. Kapsayıcıları ve sanal dizinleri birbiriyle de eşzamanlı hale getirebilirsiniz. Eşitleme bir yoldur. Diğer bir deyişle, bu iki uç noktanın hangisinin kaynak olduğunu ve hedefin nerede olduğunu seçersiniz. Eşitleme, sunucuyu sunucu API 'Leri için de kullanır. Bu bölümde sunulan örnekler, hiyerarşik bir ad alanı olan hesaplarla de çalışır. 

> [!NOTE]
> AzCopy 'in geçerli sürümü diğer kaynak ve hedefler arasında eşitlenmez (örneğin: dosya depolama veya Amazon Web Services (AWS) S3 demetleri).

Dosyaları karşıya yükleme, Blobları indirme veya hesaplar arasında blobları kopyalama gibi diğer görev türlerine yönelik örnekleri görmek için, bu makalenin [sonraki adımlarda](#next-steps) sunulan bağlantılara bakın.

## <a name="get-started"></a>başlarken

AzCopy ['i indirmek Için AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md) makalesini inceleyin ve depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayabileceğiniz yolları hakkında bilgi edinin.

> [!NOTE] 
> Bu makaledeki örneklerde Azure Active Directory (Azure AD) kullanarak yetkilendirme kimlik bilgileri sağladığınızı varsayalım.
>
> Blob verilerine erişim yetkisi vermek için bir SAS belirteci kullanıyorsanız, bu belirteci her AzCopy komutunda kaynak URL 'sine ekleyebilirsiniz. Örneğin: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Yönergeler

- [Eşitleme](storage-ref-azcopy-sync.md) komutu dosya adlarını ve son değiştirilme zaman damgalarını karşılaştırır. `--delete-destination` `true` `prompt` Bu dosyalar artık kaynak dizinde yoksa, hedef dizindeki dosyaları silmek için veya değerine isteğe bağlı bayrağını ayarlayın.

- `--delete-destination`Bayrağını olarak ayarlarsanız `true` AzCopy bir istem sağlamadan dosyaları siler. AzCopy bir dosyayı silmesinden önce bir istem görünmesini istiyorsanız, `--delete-destination` bayrağını olarak ayarlayın `prompt` .

- `--delete-destination`Bayrağını veya olarak ayarlamayı planlıyorsanız `prompt` `false` , [Eşitle](storage-ref-azcopy-sync.md) komutu yerine [Copy](storage-ref-azcopy-copy.md) komutunu kullanmayı düşünün ve `--overwrite` parametresini olarak ayarlayın `ifSourceNewer` . [Kopyalama](storage-ref-azcopy-copy.md) komutu daha az bellek tüketir ve bir kopyalama işleminin, dosyaları taşımadan önce kaynağı veya hedefi dizinlemek zorunda olmadığından daha az bir faturalama maliyeti doğurur. 

- Yanlışlıkla silinmeleri engellemek için, bayrağını kullanmadan önce [geçici silme](../blobs/soft-delete-blob-overview.md) özelliğini etkinleştirdiğinizden emin olun `--delete-destination=prompt|true` .

- Eşitleme komutunu çalıştırdığınız makinenin doğru bir sistem saati olması gerekir, çünkü son değiştirilme zamanları bir dosyanın aktarılması gerekip gerekmediğini belirlemede kritik öneme sahiptir. Sisteminizde önemli saat eğriliği varsa, bir eşitleme komutu çalıştırmayı planladığınız zaman hedefteki dosyaları çok yakın bir şekilde değiştirmekten kaçının.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Yerel dosya sistemindeki değişikliklerle bir kapsayıcıyı güncelleştirme

Bu durumda kapsayıcı hedef, yerel dosya sistemi de kaynaktır. 

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Örnek**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Yerel dosya sistemini kapsayıcıda yapılan değişikliklerle güncelleştirin

Bu durumda, yerel dosya sistemi hedefdir ve kapsayıcı kaynağıdır.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Örnek**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Başka bir kapsayıcıdaki değişikliklerle bir kapsayıcıyı güncelleştirme

Bu komutta görüntülenen ilk kapsayıcı kaynağıdır. İkincisi, hedefin bir biridir.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Örnek**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Başka bir kapsayıcıdaki dizinde bulunan değişikliklerle bir dizin güncelleştirme

Bu komutta görüntülenen ilk dizin kaynağıdır. İkincisi, hedefin bir biridir.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Örnek**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>İsteğe bağlı bayraklarla eşitler

İsteğe bağlı bayraklar kullanarak eşitleme işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.

|Senaryo|Bayrak|
|---|---|
|İndirme sırasında ne kadar kesin MD5 karmalarının doğrulanması gerektiğini belirtin.|**--Check-MD5** = \[ NoCheck \| LogOnly \| failiffarklı \| failiffarklıentormissing yok\]|
|Dosyaları bir düzene göre hariç tutun.|**--exclude-Path**|
|Eşitleme ile ilgili günlük girdilerinizi ne kadar ayrıntılı olarak istediğinizi belirtin.|**--günlük düzeyi** = \[ Uyarı \| hatası \| bilgisi \| yok\]|

Bayrakların tüm listesi için bkz. [Seçenekler](storage-ref-azcopy-sync.md#options).

> [!NOTE]
> `--recursive`Bayrak varsayılan olarak olarak ayarlanır `true` . `--exclude-pattern`Ve `--include-pattern` bayrakları, dosya yolunun diğer bölümlerine değil yalnızca dosya adları için geçerlidir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde daha fazla örnek bulun:

- [Örnekler: Karşıya Yükle](storage-use-azcopy-blobs-upload.md)
- [Örnekler: Karşıdan Yükle](storage-use-azcopy-blobs-download.md)
- [Örnekler: Hesaplar arasında kopyala](storage-use-azcopy-blobs-copy.md)
- [Örnekler: Amazon S3 demetleri](storage-use-azcopy-s3.md)
- [Örnekler: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Örnekler: Azure dosyaları](storage-use-azcopy-files.md)
- [Öğretici: AzCopy kullanarak şirket içi verileri bulut depolamasına taşıma](storage-use-azcopy-migrate-on-premises-data.md)

Ayarları yapılandırmak, performansı iyileştirmek ve sorunları gidermek için şu makalelere bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)
- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)

