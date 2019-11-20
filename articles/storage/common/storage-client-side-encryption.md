---
title: Microsoft Azure Depolama için .NET ile istemci tarafı şifreleme | Microsoft Docs
description: .NET için Azure Storage Istemci kitaplığı, istemci tarafı şifrelemeyi ve Azure depolama uygulamalarınızın en yüksek güvenliği için Azure Key Vault ile tümleştirmeyi destekler.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 921ea148c12a23ece47688a26743e1195caf52f4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003794"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Microsoft Azure Depolama için istemci tarafı şifreleme ve Azure Key Vault
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Genel Bakış
[.Net Için Azure Storage Istemci kitaplığı](/dotnet/api/overview/azure/storage/client) , Azure depolama 'ya yüklemeden önce istemci uygulamalardaki verileri şifrelemeyi ve istemciye indirme sırasında verilerin şifresini çözmesini destekler. Kitaplık Ayrıca depolama hesabı anahtar yönetimi için [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) tümleştirmeyi destekler.

İstemci tarafı şifreleme ve Azure Key Vault kullanarak blob 'ları şifreleme sürecinde size yol gösteren adım adım bir öğretici için, bkz. [Azure Key Vault kullanarak Microsoft Azure depolama bloblarını şifreleme ve şifre çözme](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Java ile istemci tarafı şifreleme için bkz. [Microsoft Azure depolama Için Java Ile Istemci tarafı şifreleme](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Zarf tekniği aracılığıyla şifreleme ve şifre çözme
Şifreleme ve şifre çözme işlemleri Envelope tekniğini izler.

### <a name="encryption-via-the-envelope-technique"></a>Zarf tekniği aracılığıyla şifreleme
Zarf tekniği aracılığıyla şifreleme aşağıdaki şekilde çalışacaktır:

1. Azure Storage istemci kitaplığı, tek seferlik kullanılan bir simetrik anahtar olan bir içerik şifreleme anahtarı (CEK) oluşturur.
2. Kullanıcı verileri bu CEK kullanılarak şifrelenir.
3. CEK daha sonra anahtar şifreleme anahtarı (KEK) kullanılarak sarmalanır (şifrelenir). KEK bir anahtar tanımlayıcısı tarafından tanımlanır ve asimetrik bir anahtar çifti veya simetrik anahtar olabilir ve yerel olarak yönetilebilir veya Azure Anahtar Kasası 'nda depolanabilir.
   
    Depolama istemci kitaplığı, KEK 'e hiçbir şekilde erişemez. Kitaplık, Key Vault tarafından sunulan anahtar sarmalama algoritmasını çağırır. Kullanıcılar, istenirse anahtar sarmalama/sarmalama için özel sağlayıcılar kullanmayı seçebilir.

4. Şifrelenmiş veriler daha sonra Azure Storage Service 'e yüklenir. Kaydırılan anahtar, bazı ek şifreleme meta verileri ile birlikte meta veri olarak depolanır (bir blob üzerinde) veya şifreli verilerle (kuyruk iletileri ve tablo varlıkları) birlikte bulunur.

### <a name="decryption-via-the-envelope-technique"></a>Zarf tekniği aracılığıyla şifre çözme
Zarf tekniği aracılığıyla şifre çözme aşağıdaki şekilde çalışmaktadır:

1. İstemci kitaplığı, kullanıcının yerel olarak veya Azure Anahtar Kasası 'nda anahtar şifreleme anahtarını (KEK) yönetdüğünü varsayar. Kullanıcının şifreleme için kullanılan belirli anahtarı bilmeleri gerekmez. Bunun yerine, anahtarlar için farklı anahtar tanımlayıcılarını çözen bir anahtar çözümleyici ayarlanabilir ve kullanılabilir.
2. İstemci kitaplığı, şifrelenmiş verileri, hizmette depolanan tüm şifreleme malzemeleriyle birlikte indirir.
3. Sarmalanan içerik şifreleme anahtarı (CEK) anahtar şifreleme anahtarı (KEK) kullanılarak sarmalanmamış (şifresi çözülür). Burada, istemci kitaplığının KEK erişimi yok. Yalnızca özel veya Key Vault sağlayıcının sarmalama algoritmasını çağırır.
4. İçerik şifreleme anahtarı (CEK), daha sonra şifrelenmiş Kullanıcı verilerinin şifresini çözmek için kullanılır.

## <a name="encryption-mechanism"></a>Şifreleme mekanizması
Depolama istemci kitaplığı, Kullanıcı verilerini şifrelemek için [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kullanır. Özellikle, AES ile [Şifre blok zincirleme (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modu. Her bir hizmet biraz farklı çalışır, bu nedenle bunlardan her birini burada ele alınacaktır.

### <a name="blobs"></a>Bloblar
İstemci kitaplığı şu anda yalnızca tüm Blobların şifrelenmesini destekliyor. Özellikle, kullanıcılar **Uploadfrom** yöntemlerini veya **OpenWrite** metodunu kullanırken şifreleme desteklenir. İndirmeler için hem tamamlanma hem de Aralık İndirmeleri desteklenir.

Şifreleme sırasında, istemci kitaplığı, 16 baytlık rastgele bir başlatma vektörü (IV), 32 baytlık bir rastgele içerik şifreleme anahtarı (CEK) ile birlikte oluşturulur ve bu bilgileri kullanarak blob verilerinin zarf şifrelemesini gerçekleştirir. Sarmalanan CEK ve bazı ek şifreleme meta verileri, hizmet üzerindeki şifreli blob ile birlikte blob meta verileri olarak depolanır.

> [!WARNING]
> Blob için kendi meta verilerinizi düzenliyorsanız veya karşıya yüklüyorsanız, bu meta verilerin korunduğundan emin olmanız gerekir. Bu meta veriler olmadan yeni meta veri yüklerseniz, Sarmalanan CEK, IV ve diğer meta veriler kaybedilir ve BLOB içeriği hiçbir şekilde yeniden getirilemez.
> 
> 

Şifrelenmiş bir blob 'u indirmek,**bloenine akış** kolaylığı yöntemlerine **downloadto**/kullanarak tüm Blobun içeriğini almayı içerir. Sarmalanmış CEK, şifresi çözülmüş verileri kullanıcılara döndürmek için sarmalanmış ve IV (Bu durumda blob meta verileri olarak saklanır) ile birlikte kullanılır.

Şifrelenmiş Blobun rastgele bir aralığın (**downloadrange** yöntemleri) indirilmesi, istenen aralığın şifresini başarıyla çözmek için kullanılabilecek az miktarda ek veri alabilmek için kullanıcılar tarafından sunulan aralığı ayarlamayı içerir.

Tüm blob türleri (blok Blobları, sayfa Blobları ve ekleme Blobları) Bu şema kullanılarak şifrelenebilir/şifresi çözülür.

### <a name="queues"></a>Sıralar
Sıra iletileri herhangi bir biçimde olduğundan, istemci kitaplığı, ileti metninde başlatma vektörü (IV) ve şifreli içerik şifreleme anahtarını (CEK) içeren özel bir biçimi tanımlar.

Şifreleme sırasında, istemci kitaplığı rastgele bir IV/32 bayt üretir ve bu bilgileri kullanarak kuyruk ileti metninin zarf şifrelemesini gerçekleştirir. Sarmalanan CEK ve bazı ek şifreleme meta verileri daha sonra şifreli kuyruk iletisine eklenir. Bu değiştirilmiş ileti (aşağıda gösterilmiştir) hizmette depolanır.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Şifre çözme sırasında, Sarmalanan anahtar kuyruk iletisinden ayıklanır ve sarmalanmamış. IV Ayrıca kuyruk iletisinden çıkarılır ve sıra ileti verilerinin şifresini çözmek için sarmalanmamış anahtarla birlikte kullanılır. Şifreleme meta verilerinin küçük olduğunu unutmayın (500 bayt altında), bu nedenle bir kuyruk iletisi için 64 KB sınırına doğru sayılır, etki alanı yönetilebilir olmalıdır.

### <a name="tables"></a>Tablolar
İstemci kitaplığı, ekleme ve değiştirme işlemleri için varlık özelliklerinin şifrelenmesini destekler.

> [!NOTE]
> Birleştirme şu anda desteklenmiyor. Bir özellik alt kümesi daha önce farklı bir anahtarla şifrelenmiş olabilecek olduğundan, yalnızca birleştirme yeni özellikleri ve meta verilerini güncelleştirme veri kaybına yol açar. Özellik başına yeni bir anahtar kullanarak, ikisi için de performansla ilgili nedenlerden dolayı uygun değil veya hizmetten önceden var olan bir varlığa okumak için ek hizmet çağrıları yapma ya da birleştirme gerektirir.
> 
> 

Tablo veri şifrelemesi aşağıdaki gibi kullanılabilir:  

1. Kullanıcılar şifrelenecek özellikleri belirtir.
2. İstemci kitaplığı, her varlık için 32 baytlık rastgele bir içerik şifreleme anahtarı (CEK) ile 16 baytlık rastgele bir başlatma vektörü (IV) oluşturur ve özellik başına yeni bir IV türeterek tek tek özelliklerde zarf şifrelemesi gerçekleştirir. Şifrelenmiş Özellik ikili veri olarak depolanır.
3. Sarmalanan CEK ve bazı ek şifreleme meta verileri daha sonra iki ek ayrılmış özellik olarak depolanır. İlk ayrılmış Özellik (_ClientEncryptionMetadata1), IV, sürüm ve Sarmalanan anahtarla ilgili bilgileri tutan bir String özelliğidir. İkinci ayrılmış Özellik (_ClientEncryptionMetadata2), şifrelenen özelliklerle ilgili bilgileri tutan bir ikili özelliktir. Bu ikinci özelliğindeki bilgilerin (_ClientEncryptionMetadata2) kendisi şifrelenir.
4. Şifreleme için gerekli olan bu ek ayrılmış özellikler nedeniyle, kullanıcılar artık 252 yerine yalnızca 250 özel özelliklerine sahip olabilir. Varlığın toplam boyutu 1 MB 'tan az olmalıdır.

Yalnızca dize özelliklerinin şifrelendiğini unutmayın. Diğer özellik türleri şifrelenirse, bunların dizelere dönüştürülmesi gerekir. Şifrelenmiş dizeleri hizmette ikili özellikleri olarak depolanır ve şifre çözme sonra geri dizelere dönüştürülür.

Şifreleme İlkesi yanı sıra, tablolar için kullanıcıların şifrelenmiş özelliklerini belirtmeniz gerekir. Bu, ya da (TableEntity türetilen POCO varlık için) bir [EncryptProperty] özniteliği ya da bir şifreleme çözümleyici istek seçenekleri belirterek yapılabilir. Bir şifreleme çözümleyici bölüm anahtarını, satır anahtarını ve özellik adını alır ve bu özellik şifrelenmesi gerekip gerekmediğini belirten bir Boole değeri döndüren bir temsilcidir. Şifreleme sırasında istemci kitaplığı, bir özellik için kablo yazılırken şifrelenmesi gerekip gerekmediğine karar vermek için bu bilgileri kullanır. Temsilci özellikleri nasıl şifrelenir etrafında mantıksal olasılığı için de sağlar. (X, örneğin, daha sonra özellik A şifrelemek; Aksi takdirde özellik A ve b şifreleme) Varlıkları okurken veya sorgularken bu bilgilerin sağlanması gerekmediğini unutmayın.

### <a name="batch-operations"></a>Toplu Işlemler
Batch işlemlerinde, bu toplu işlemdeki tüm satırlarda aynı KEK kullanılacaktır çünkü istemci kitaplığı, toplu işlem başına yalnızca bir seçenek nesnesine (ve bu nedenle bir ilke/KEK) izin verir. Ancak, istemci Kitaplığı toplu işte her satır için yeni bir rastgele IV ve rastgele CEK oluşturacaktır. Kullanıcılar ayrıca bu davranışı şifreleme Çözümleyicisi 'nde tanımlayarak toplu işteki her işlem için farklı özellikleri şifrelemeyi seçebilir.

### <a name="queries"></a>Sorgular
> [!NOTE]
> Varlıklar şifrelendiğinden, şifrelenen bir özelliği filtreleyen sorguları çalıştıramazsınız.  Deneme yaparsanız, hizmet şifrelenmiş verileri şifrelenmemiş verilerle karşılaştırmaya çalıştığı için sonuçlar yanlış olacaktır.
> 
> 
> Sorgu işlemleri gerçekleştirmek için, sonuç kümesindeki tüm anahtarları çözebilecek bir anahtar çözümleyici belirtmeniz gerekir. Sorgu sonucunda içerilen bir varlık bir sağlayıcıya çözümlenemiyorsa, istemci kitaplığı bir hata oluşturur. Sunucu tarafı projeksiyonları gerçekleştiren sorgular için, istemci kitaplığı, varsayılan olarak seçilen sütunlara özel şifreleme meta verileri özelliklerini (_ClientEncryptionMetadata1 ve _ClientEncryptionMetadata2) ekler.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Anahtar Kasası, bulut uygulamaları ve hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Kullanıcılar, Azure Key Vault kullanarak anahtarları ve gizli dizileri (kimlik doğrulama anahtarları, depolama hesabı anahtarları, veri şifreleme anahtarları,) şifreleyebilir. PFX dosyaları ve parolalar), donanım güvenlik modülleri (HSM 'ler) tarafından korunan anahtarları kullanarak. Daha fazla bilgi için bkz. [Azure Key Vault nedir?](../../key-vault/key-vault-overview.md).

Depolama istemci kitaplığı, anahtarları yönetmek için Azure genelinde ortak bir çerçeve sağlamak üzere Key Vault çekirdek kitaplığını kullanır. Kullanıcılar ayrıca Key Vault uzantıları kitaplığını kullanmanın ek avantajını de alır. Uzantılar kitaplığı basit ve sorunsuz simetrik/RSA yerel ve bulut anahtar sağlayıcılarının yanı sıra toplama ve önbelleğe alma ile ilgili yararlı işlevler sağlar.

### <a name="interface-and-dependencies"></a>Arabirim ve bağımlılıklar
Üç Key Vault paketi vardır:

* Microsoft. Azure. Keykasası. Core, Ikey ve ıkeyresolver içerir. Bağımlılıkları olmayan küçük bir pakettir. .NET için depolama istemci kitaplığı, bunu bir bağımlılık olarak tanımlar.
* Microsoft. Azure. Keykasasında Key Vault REST istemcisi bulunur.
* Microsoft. Azure. Keykasası. Extensions, şifreleme algoritmalarının ve bir RSAKey ve bir SymmetricKey uygulamalarının bulunduğu uzantı kodunu içerir. Çekirdek ve Anahtar Kasası ad alanlarına bağlıdır ve bir toplama Çözümleyicisi (kullanıcılar birden çok anahtar sağlayıcı kullanmak istediğinizde) ve bir önbelleğe alma anahtar çözümleyici tanımlamak için işlevsellik sağlar. Depolama istemci kitaplığı bu pakete doğrudan bağlı olmasa da, kullanıcılar anahtarlarını depolamak için Azure Key Vault kullanmak veya yerel ve bulut şifreleme sağlayıcılarını kullanmak için Key Vault uzantıları kullanmak istediklerinde, bu pakete ihtiyacı vardır.

Key Vault, yüksek değerli ana anahtarlar için tasarlanmıştır ve Key Vault başına azaltma limitleri bu şekilde göz önünde bulundurularak tasarlanır. Key Vault ile istemci tarafı şifreleme gerçekleştirirken tercih edilen model, Key Vault gizli dizi olarak depolanan ve yerel olarak önbelleğe alınan simetrik ana anahtarları kullanmaktır. Kullanıcıların aşağıdakileri yapması gerekir:

1. Çevrimdışı bir gizli dizi oluşturun ve Key Vault yükleyin.
2. Şifreleme için geçerli gizli sürümü çözümlemek ve bu bilgileri yerel olarak önbelleğe almak için bir parametre olarak gizli dizi temel tanımlayıcısını kullanın. Önbelleğe alma için CachingKeyResolver kullanın; Kullanıcıların kendi önbelleğe alma mantığını uygulaması beklenmez.
3. Şifreleme ilkesi oluştururken bir giriş olarak önbelleğe alma Çözümleyicisi 'ni kullanın.

Key Vault kullanımı ile ilgili daha fazla bilgiyi [şifreleme kodu örneklerinde](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)bulabilirsiniz.

## <a name="best-practices"></a>En iyi uygulamalar
Şifreleme desteği yalnızca .NET için depolama istemci kitaplığı 'nda kullanılabilir. Windows Phone ve Windows Çalışma Zamanı Şu anda şifrelemeyi desteklemiyor.

> [!IMPORTANT]
> İstemci tarafı şifrelemeyi kullanırken bu önemli noktalara dikkat edin:
> 
> * Şifrelenmiş bir bloba okurken veya yazarken, tüm blob karşıya yükleme komutlarını ve Aralık/tam blob indirme komutlarını kullanın. Put bloğu, put bloğu listesi, yazma sayfaları, temizleme sayfaları veya ekleme bloğu gibi protokol işlemlerini kullanarak şifrelenmiş bir bloba yazmaktan kaçının; Aksi takdirde, şifreli blobu bozuyor ve okunamaz hale getirebilirsiniz.
> * Tablolar için, benzer bir kısıtlama vardır. Şifrelenmiş özellikleri, şifreleme meta verilerini güncelleştirmeden güncelleştirmemeye dikkat edin.
> * Şifrelenmiş blob üzerinde meta verileri ayarlarsanız, verilerin ayarlanmamasından dolayı, şifre çözme için gereken şifrelemeyle ilgili meta verilerin üzerine yazabilirsiniz. Bu, anlık görüntüler için de geçerlidir; şifrelenmiş bir Blobun anlık görüntüsünü oluştururken meta verileri belirtmekten kaçının. Meta verilerin ayarlanması gerekiyorsa, geçerli şifreleme meta verilerini almak için önce **Fetchattributes** yöntemini çağırdığınızdan emin olun ve meta veriler ayarlanırken eş zamanlı yazmaları önleyin.
> * Yalnızca şifrelenmiş verilerle çalışması gereken kullanıcılar için varsayılan istek seçeneklerinde **Requireencryptıon** özelliğini etkinleştirin. Daha fazla bilgi için aşağıya bakın.
> 
> 

## <a name="client-api--interface"></a>İstemci API 'SI/arabirimi
Bir EncryptionPolicy nesnesi oluştururken, kullanıcılar yalnızca bir anahtar (Ikey uygulayan), yalnızca bir çözümleyici (ıkeyresolver uygulayan) veya her ikisini de sağlayabilir. Ikey, anahtar tanımlayıcısı kullanılarak tanımlanan ve sarmalama/sarmalama mantığını sağlayan temel anahtar türüdür. Ikeyresolver, şifre çözme işlemi sırasında bir anahtarı çözümlemek için kullanılır. Anahtar tanımlayıcı verilen bir Ikey döndüren bir ResolveKey yöntemi tanımlar. Bu, kullanıcılara birden çok konumda yönetilen birden çok anahtar arasında seçim yapma olanağı sağlar.

* Şifreleme için, anahtar her zaman kullanılır ve bir anahtarın yokluğu bir hataya neden olur.
* Şifre çözme için:
  * Anahtarı almak için belirtilmişse anahtar çözümleyici çağrılır. Çözümleyici belirtilmişse ancak anahtar tanımlayıcısı için bir eşleme yoksa bir hata oluşur.
  * Çözümleyici belirtilmemişse ancak anahtar belirtilmişse, tanımlayıcı gerekli anahtar tanımlayıcısıyla eşleşiyorsa anahtar kullanılır. Tanımlayıcı eşleşmiyorsa bir hata oluşur.

Bu makaledeki kod örnekleri, bir şifreleme ilkesi ayarlamayı ve şifreli verilerle çalışmayı gösterir, ancak Azure Key Vault çalışmayı gösterir. GitHub 'daki [şifreleme örnekleri](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) , Key Vault tümleştirmeyle birlikte Bloblar, kuyruklar ve tablolar için daha ayrıntılı bir uçtan uca senaryo gösterir.

### <a name="requireencryption-mode"></a>RequireEncryption modu
Kullanıcılar isteğe bağlı olarak, tüm karşıya yüklemeler ve indirmelerin şifrelenmesi gereken bir işlem modunu etkinleştirebilir. Bu modda, şifreleme ilkesi olmadan veya hizmette şifrelenmeyen verileri karşıdan yükleme girişimleri istemcide başarısız olur. İstek seçenekleri nesnesinin **RequireEncryption** özelliği bu davranışı denetler. Uygulamanız Azure depolama alanında depolanan tüm nesneleri şifreleyeceğini, hizmet istemci nesnesi için varsayılan istek seçeneklerinde **RequireEncryption** özelliğini ayarlayabilirsiniz. Örneğin, istemci nesnesi aracılığıyla gerçekleştirilen tüm blob işlemleri için şifrelemeyi zorunlu kılmak üzere **Cloudblobclient. DefaultRequestOptions. RequireEncryption** öğesini **true** olarak ayarlayın.


### <a name="blob-service-encryption"></a>Blob hizmeti şifrelemesi
**Blobencryptionpolicy** nesnesi oluşturun ve bunu istek SEÇENEKLERINDE (API başına veya **defaultrequestoptions**kullanarak istemci düzeyinde) ayarlayın. Diğer her şey, dahili olarak istemci kitaplığı tarafından işlenir.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Kuyruk hizmeti şifreleme
Bir **Queueencryptionpolicy** nesnesi oluşturun ve bunu istek SEÇENEKLERINDE (API başına veya **defaultrequestoptions**kullanarak istemci düzeyinde) ayarlayın. Diğer her şey, dahili olarak istemci kitaplığı tarafından işlenir.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Tablo hizmeti şifrelemesi
Bir şifreleme ilkesi oluşturup istek seçeneklerinde ayarlamaya ek olarak, **Tablerequestoptions**Içinde bir **encryptionresolver** belirtmeniz veya varlıkta [encryptproperty] özniteliğini ayarlamanız gerekir.

#### <a name="using-the-resolver"></a>Çözümleyici 'yi kullanma

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Öznitelikleri kullanma
Yukarıda belirtildiği gibi, varlık TableEntity uygularsa, Özellikler **Encryptionresolver**'ı belirtmek yerine [EncryptProperty] özniteliğiyle birlikte kullanılabilir.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Şifreleme ve performans
Depolama verilerinizi şifrelerken ek performans yükü ile sonuçlandığına göz önünde unutmayın. İçerik anahtarı ve IV oluşturulmalıdır, içeriğin kendisi şifrelenmelidir ve ek meta veriler biçimlendirilmelidir ve karşıya yüklenmelidir. Bu ek yük, Şifrelenmekte olan veri miktarına bağlı olarak farklılık gösterecektir. Müşterilerin geliştirme sırasında uygulamalarının performansını her zaman test etmenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar
* [Öğretici: Azure Key Vault kullanarak Microsoft Azure Depolama Blobları şifreleme ve şifre çözme](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* [.Net NuGet paketi Için Azure Storage Istemci kitaplığı](https://www.nuget.org/packages/WindowsAzure.Storage) 'nı indirin
* NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)ve [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) paketlerini Azure Key Vault indirin  
* [Azure Key Vault belgelerini](../../key-vault/key-vault-overview.md) ziyaret edin
