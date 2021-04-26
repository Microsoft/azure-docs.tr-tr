---
title: AzCopy ile v10 arasındaki ile Azure depolama hesapları arasında blobları kopyalama | Microsoft Docs
description: Bu makale, depolama hesapları arasında Blobları kopyalamanızı sağlayan AzCopy örnek komutlarının bir koleksiyonunu içerir.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: bfdc91ac8f4ce618052cc78e76b27e8bdeabeb77
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502989"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>AzCopy kullanarak Blobları Azure depolama hesapları arasında kopyalama

AzCopy ile v10 arasındaki komut satırı yardımcı programını kullanarak blob, dizin ve kapsayıcıları depolama hesapları arasında kopyalayabilirsiniz. 

Dosya yükleme, blob indirme ve BLOB depolama ile eşitleme gibi diğer görev türlerine yönelik örnekleri görmek için, bu makalenin [sonraki adımlarda](#next-steps) sunulan bağlantılara bakın.

AzCopy, [sunucudan sunucuya](/rest/api/storageservices/put-block-from-url) [API 'ler](/rest/api/storageservices/put-page-from-url)kullanır, bu nedenle veriler doğrudan depolama sunucuları arasında kopyalanır. Bu kopyalama işlemleri bilgisayarınızın ağ bant genişliğini kullanmaz.

AzCopy 'i indirmek ve depolama hizmetine yetkilendirme kimlik bilgilerini sağlamanın yolları hakkında bilgi edinmek için bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Yönergeler

AzCopy komutlarınıza aşağıdaki yönergeleri uygulayın. 

- İstemciniz hem kaynak hem de hedef depolama hesaplarına ağ erişimine sahip olmalıdır. Her depolama hesabı için ağ ayarlarını yapılandırma hakkında bilgi edinmek için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

- Her kaynak URL 'ye bir SAS belirteci ekleyin. 

  Azure Active Directory (Azure AD) kullanarak yetkilendirme kimlik bilgilerini sağlarsanız, SAS belirtecini yalnızca hedef URL 'den atlayabilirsiniz. Hedef hesabınızda uygun rolleri ayarladığınızdan emin olun. Bkz. [1. seçenek: Use Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  Bu makaledeki örneklerde, Azure AD 'yi kullanarak kimliğinizin kimliğini doğruladığınızı varsayıyoruz, bu nedenle örneklerin hedef URL 'den SAS belirteçlerini yok.

-  Bir Premium Blok Blob depolama hesabına kopyalarsanız, öğesini olarak ayarlayarak kopyalama işleminden bir Blobun erişim katmanını atlayın `s2s-preserve-access-tier` `false` (örneğin: `--s2s-preserve-access-tier=false` ). Premium Blok Blob depolama hesapları, erişim katmanlarını desteklemez. 

- Hiyerarşik ad alanı olan bir hesaba veya ' a kopyalarsanız, `blob.core.windows.net` `dfs.core.windows.net` URL sözdizimi yerine kullanın. [Data Lake Storage çoklu protokol erişimi](../blobs/data-lake-storage-multi-protocol-access.md) , kullanmanıza olanak sağlar ve hesap `blob.core.windows.net` kopyalama senaryolarında hesap için desteklenen tek sözdizimidir. 

- Ortam değişkeninin değerini ayarlayarak kopyalama işlemlerinin aktarım hızını artırabilirsiniz `AZCOPY_CONCURRENCY_VALUE` . Daha fazla bilgi için bkz. [eşzamanlılık artırma](storage-use-azcopy-optimize.md#increase-concurrency). 

- Kaynak Blobların dizin etiketleri varsa ve bu etiketleri sürdürmek istiyorsanız, bunları hedef bloblara yeniden uygulamanız gerekir. Dizin etiketlerinin nasıl ayarlanacağı hakkında daha fazla bilgi için, bu makalenin [Dizin etiketleriyle blob 'ları başka bir depolama hesabına kopyalama](#copy-between-accounts-and-add-index-tags) bölümüne bakın.

## <a name="copy-a-blob"></a>Blob kopyalama

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak bir blobu başka bir depolama hesabına kopyalayın. 

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

Kopyalama işlemi zaman uyumludur. Başka bir deyişle komut sonuç döndürdüğünde tüm dosyalar kopyalanmış demektir. 

## <a name="copy-a-directory"></a>Bir dizini kopyalama

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak bir dizini başka bir depolama hesabına kopyalayın. 

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

Kopyalama işlemi zaman uyumludur. Başka bir deyişle komut sonuç döndürdüğünde tüm dosyalar kopyalanmış demektir.

## <a name="copy-a-container"></a>Kapsayıcıyı kopyalama

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak bir kapsayıcıyı başka bir depolama hesabına kopyalayın.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

Kopyalama işlemi zaman uyumludur. Başka bir deyişle komut sonuç döndürdüğünde tüm dosyalar kopyalanmış demektir.

## <a name="copy-containers-directories-and-blobs"></a>Kapsayıcıları, dizinleri ve blobları kopyalama

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanarak tüm kapsayıcıları, dizinleri ve Blobları başka bir depolama hesabına kopyalayın.

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**Örnek**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

Kopyalama işlemi zaman uyumludur. Başka bir deyişle komut sonuç döndürdüğünde tüm dosyalar kopyalanmış demektir.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Blob 'ları kopyalama ve dizin etiketleri ekleme

Blobları başka bir depolama hesabına kopyalayın ve hedef blob 'a [BLOB dizin etiketleri (Önizleme)](../blobs/storage-manage-find-blobs.md) ekleyin.

Azure AD yetkilendirmesi kullanıyorsanız, güvenlik sorumlusuna [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolü atanmalıdır veya `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Kaynak sağlayıcısı işlemine](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) özel bir Azure rolü aracılığıyla izin verilmelidir. Paylaşılan erişim Imzası (SAS) belirteci kullanıyorsanız, bu belirtecin SAS izni aracılığıyla Blobun etiketlerine erişim sağlaması gerekir `t` .

Etiketler eklemek için, `--blob-tags` URL kodlamalı anahtar-değer çiftiyle birlikte seçeneğini kullanın. 

Örneğin, anahtar `my tag` ve bir değer eklemek için `my tag value` , `--blob-tags='my%20tag=my%20tag%20value'` hedef parametreye eklersiniz. 

Bir ampersan () kullanarak birden çok dizin etiketini ayırın `&` .  Örneğin, bir anahtar `my second tag` ve değer eklemek istiyorsanız `my second tag value` , tüm seçenek dizesi olur `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Aşağıdaki örneklerde seçeneğinin nasıl kullanılacağı gösterilmektedir `--blob-tags` .

> [!TIP]
> Bu örnekler, yol bağımsız değişkenlerini tek tırnak (' ') ile çevreler. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

**Blob örneği**

```azcopy

`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Dizin örneği**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

 **Kapsayıcı örneği**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Hesap örneği**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

Kopyalama işlemi zaman uyumludur. Başka bir deyişle komut sonuç döndürdüğünde tüm dosyalar kopyalanmış demektir.

> [!NOTE]
> Kaynak için bir dizin, kapsayıcı veya hesap belirtirseniz, hedefe kopyalanmış tüm Bloblar komutta belirttiğiniz etiketlere sahip olur.

## <a name="copy-with-optional-flags"></a>İsteğe bağlı bayraklarla Kopyala

İsteğe bağlı bayraklar kullanarak kopyalama işleminizi ince ayar yapabilirsiniz. İşte birkaç örnek.

|Senaryo|Bayrak|
|---|---|
|Blob 'ları blok, sayfa veya ekleme Blobları olarak kopyalayın.|**--BLOB-tür** = \[ BlockBlob \| pageblob \| appendblob\]|
|Belirli bir erişim katmanına (Arşiv katmanı gibi) kopyalayın.|**--Block-blob-Tier** = \[ Hiçbiri \| sık erişimli olmayan \| \| Arşiv\]|
|Dosyaları otomatik olarak aç.|**--sıkıştırmayı aç** = \[ gzip \| söndür\]|

Tüm liste için bkz. [Seçenekler](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde daha fazla örnek bulun:

- [Örnekler: Karşıya Yükle](storage-use-azcopy-blobs-upload.md)
- [Örnekler: Karşıdan Yükle](storage-use-azcopy-blobs-download.md)
- [Örnekler: Eşitle](storage-use-azcopy-blobs-synchronize.md)
- [Örnekler: Amazon S3 demetleri](storage-use-azcopy-s3.md)
- [Örnekler: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Örnekler: Azure dosyaları](storage-use-azcopy-files.md)
- [Öğretici: AzCopy kullanarak şirket içi verileri bulut depolamasına taşıma](storage-use-azcopy-migrate-on-premises-data.md)

Ayarları yapılandırmak, performansı iyileştirmek ve sorunları gidermek için şu makalelere bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)
- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)