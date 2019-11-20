---
title: Microsoft Azure Depolama için Python ile istemci tarafı şifreleme | Microsoft Docs
description: Python için Azure Storage Istemci kitaplığı, Azure depolama uygulamalarınız için en yüksek güvenlik için istemci tarafı şifrelemeyi destekler.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cd8ba51b960703fa25371d874ed2bb50e7df2fde
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360047"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Microsoft Azure Depolama için Python ile istemci tarafı şifreleme
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Genel Bakış
[Python Için Azure Storage Istemci kitaplığı](https://pypi.python.org/pypi/azure-storage) , Azure depolama 'ya yüklemeden önce istemci uygulamalar içindeki verilerin şifrelenmesini ve istemciye indirilirken verilerin şifrelerini çözmeyi destekler.

> [!NOTE]
> Azure Storage Python kitaplığı önizlemededir.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Zarf tekniği aracılığıyla şifreleme ve şifre çözme
Şifreleme ve şifre çözme işlemleri Envelope tekniğini izler.

### <a name="encryption-via-the-envelope-technique"></a>Zarf tekniği aracılığıyla şifreleme
Zarf tekniği aracılığıyla şifreleme aşağıdaki şekilde çalışacaktır:

1. Azure Storage istemci kitaplığı, tek seferlik kullanılan bir simetrik anahtar olan bir içerik şifreleme anahtarı (CEK) oluşturur.
2. Kullanıcı verileri bu CEK kullanılarak şifrelenir.
3. CEK daha sonra anahtar şifreleme anahtarı (KEK) kullanılarak sarmalanır (şifrelenir). KEK, bir anahtar tanımlayıcısı tarafından tanımlanır ve yerel olarak yönetilen bir asimetrik anahtar çifti veya simetrik anahtar olabilir.
   Depolama istemci kitaplığı, KEK 'e hiçbir şekilde erişemez. Kitaplık, KEK tarafından sunulan anahtar sarmalama algoritmasını çağırır. Kullanıcılar, istenirse anahtar sarmalama/sarmalama için özel sağlayıcılar kullanmayı seçebilir.
4. Şifrelenmiş veriler daha sonra Azure Storage Service 'e yüklenir. Kaydırılan anahtar, bazı ek şifreleme meta verileri ile birlikte meta veri olarak depolanır (bir blob üzerinde) veya şifreli verilerle (kuyruk iletileri ve tablo varlıkları) birlikte bulunur.

### <a name="decryption-via-the-envelope-technique"></a>Zarf tekniği aracılığıyla şifre çözme
Zarf tekniği aracılığıyla şifre çözme aşağıdaki şekilde çalışmaktadır:

1. İstemci kitaplığı, kullanıcının anahtar şifreleme anahtarını (KEK) yerel olarak yönetiyor olduğunu varsayar. Kullanıcının şifreleme için kullanılan belirli anahtarı bilmeleri gerekmez. Bunun yerine, anahtarlar için farklı anahtar tanımlayıcıları çözen bir anahtar çözümleyici ayarlanabilir ve kullanılabilir.
2. İstemci kitaplığı, şifrelenmiş verileri, hizmette depolanan tüm şifreleme malzemeleriyle birlikte indirir.
3. Sarmalanan içerik şifreleme anahtarı (CEK) anahtar şifreleme anahtarı (KEK) kullanılarak sarmalanmamış (şifresi çözülür). Burada, istemci kitaplığının KEK erişimi yok. Yalnızca özel sağlayıcının sarmalama algoritmasını çağırır.
4. İçerik şifreleme anahtarı (CEK), daha sonra şifrelenmiş Kullanıcı verilerinin şifresini çözmek için kullanılır.

## <a name="encryption-mechanism"></a>Şifreleme mekanizması
Depolama istemci kitaplığı, Kullanıcı verilerini şifrelemek için [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kullanır. Özellikle, AES ile [Şifre blok zincirleme (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modu. Her bir hizmet biraz farklı çalışır, bu nedenle bunlardan her birini burada ele alınacaktır.

### <a name="blobs"></a>Bloblar
İstemci kitaplığı şu anda yalnızca tüm Blobların şifrelenmesini destekliyor. Özellikle, kullanıcılar **Create*** yöntemlerini kullanırken şifreleme desteklenir. İndirmeler için hem tamamlanma hem de Aralık İndirmeleri desteklenir ve hem karşıya yükleme hem de indirme için paralelleştirme kullanılabilir.

Şifreleme sırasında, istemci kitaplığı, 16 baytlık rastgele bir başlatma vektörü (IV), 32 baytlık bir rastgele içerik şifreleme anahtarı (CEK) ile birlikte oluşturulur ve bu bilgileri kullanarak blob verilerinin zarf şifrelemesini gerçekleştirir. Sarmalanan CEK ve bazı ek şifreleme meta verileri, hizmet üzerindeki şifreli blob ile birlikte blob meta verileri olarak depolanır.

> [!WARNING]
> Blob için kendi meta verilerinizi düzenliyorsanız veya karşıya yüklüyorsanız, bu meta verilerin korunduğundan emin olmanız gerekir. Bu meta veriler olmadan yeni meta veri yüklerseniz, Sarmalanan CEK, IV ve diğer meta veriler kaybolur ve BLOB içeriği hiçbir şekilde yeniden getirilemez.
> 
> 

Şifrelenmiş bir Blobun indirilmesi, **Get*** kullanışlı yöntemler kullanılarak tüm Blobun içeriğini almayı içerir. Sarmalanmış CEK, şifresi çözülmüş verileri kullanıcılara döndürmek için sarmalanmış ve IV (Bu durumda blob meta verileri olarak saklanır) ile birlikte kullanılır.

Şifrelenmiş Blobun rastgele bir aralığı indirme (Aralık parametrelerine geçilen bir**Get*** yöntemi), istenen aralığın şifresini başarıyla çözmek için kullanılabilecek daha az miktarda ek veri alabilmek için kullanıcılar tarafından verilen aralığı ayarlamayı içerir .

Blok Blobları ve sayfa Blobları yalnızca bu şema kullanılarak şifrelenebilir/çözülür. Şu anda, ekleme bloblarını şifrelemek için destek yoktur.

### <a name="queues"></a>Sıralar
Sıra iletileri herhangi bir biçimde olduğundan, istemci kitaplığı, ileti metninde başlatma vektörü (IV) ve şifreli içerik şifreleme anahtarını (CEK) içeren özel bir biçimi tanımlar.

Şifreleme sırasında, istemci kitaplığı rastgele bir IV/32 bayt üretir ve bu bilgileri kullanarak kuyruk ileti metninin zarf şifrelemesini gerçekleştirir. Sarmalanan CEK ve bazı ek şifreleme meta verileri daha sonra şifreli kuyruk iletisine eklenir. Bu değiştirilmiş ileti (aşağıda gösterilmiştir) hizmette depolanır.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
3. Sarmalanan CEK ve bazı ek şifreleme meta verileri daha sonra iki ek ayrılmış özellik olarak depolanır. İlk ayrılmış özelliği (\_ClientEncryptionMetadata1), IV, sürüm ve Sarmalanan anahtarla ilgili bilgileri tutan bir String özelliğidir. İkinci ayrılmış özelliği (\_ClientEncryptionMetadata2), şifrelenen özelliklerle ilgili bilgileri tutan bir ikili özelliktir. Bu ikinci özelliğindeki bilgilerin (\_ClientEncryptionMetadata2) kendisi şifrelenir.
4. Şifreleme için gerekli olan bu ek ayrılmış özellikler nedeniyle, kullanıcılar artık 252 yerine yalnızca 250 özel özelliklerine sahip olabilir. Varlığın toplam boyutu 1 MB 'tan az olmalıdır.

   Yalnızca dize özelliklerinin şifrelendiğini unutmayın. Diğer özellik türleri şifrelenirse, bunların dizelere dönüştürülmesi gerekir. Şifrelenmiş dizeler, hizmet üzerinde ikili özellikler olarak depolanır ve şifre çözme işleminden sonra dizelere (tür EdmType. STRING olan EntityProperties 'e değil) geri dönüştürülür.

   Şifreleme İlkesi yanı sıra, tablolar için kullanıcıların şifrelenmiş özelliklerini belirtmeniz gerekir. Bu özellik, bu özellikler Type değeri EdmType. STRING olarak ayarlanmış ve şifreleme doğru olarak ayarlanmış veya tableservice nesnesi üzerinde encryption_resolver_function olarak ayarlanan TableEntity nesnelerinde depolanarak yapılabilir. Şifreleme çözümleyici, Bölüm anahtarını, satır anahtarını ve özellik adını alan ve bu özelliğin şifrelenip şifrelenmeyeceğini belirten bir Boole değeri döndüren bir işlevdir. Şifreleme sırasında istemci kitaplığı, bir özellik için kablo yazılırken şifrelenmesi gerekip gerekmediğine karar vermek için bu bilgileri kullanır. Temsilci özellikleri nasıl şifrelenir etrafında mantıksal olasılığı için de sağlar. (X, örneğin, daha sonra özellik A şifrelemek; Aksi takdirde özellik A ve b şifreleme) Varlıkları okurken veya sorgularken bu bilgilerin sağlanması gerekmediğini unutmayın.

### <a name="batch-operations"></a>Toplu Işlemler
Tek bir şifreleme ilkesi, toplu işlemdeki tüm satırlara uygulanır. İstemci kitaplığı, toplu işte her satır için yeni bir rastgele IV ve Random CEK oluşturacaktır. Kullanıcılar ayrıca bu davranışı şifreleme Çözümleyicisi 'nde tanımlayarak toplu işteki her işlem için farklı özellikleri şifrelemeyi seçebilir.
Bir toplu işlem, tableservice Batch () yöntemiyle bir bağlam Yöneticisi olarak oluşturulduysa, tableservice 'in şifreleme ilkesi toplu işe otomatik olarak uygulanır. Oluşturucu çağırarak bir toplu iş açıkça oluşturulduysa, şifreleme ilkesinin bir parametre olarak geçirilmesi ve toplu işin ömrü boyunca değiştirilmemiş olarak bırakılması gerekir.
Toplu işin şifreleme ilkesi kullanılarak toplu işe eklenen varlıkların şifrelendiğine ve (varlıklar, tableservice 'in şifreleme ilkesini kullanarak toplu işi yürütme sırasında şifrelenmez).

### <a name="queries"></a>Sorgular
> [!NOTE]
> Varlıklar şifrelendiğinden, şifrelenen bir özelliği filtreleyen sorguları çalıştıramazsınız.  Deneme yaparsanız, hizmet şifrelenmiş verileri şifrelenmemiş verilerle karşılaştırmaya çalıştığı için sonuçlar yanlış olacaktır.
> 
> 
> Sorgu işlemleri gerçekleştirmek için, sonuç kümesindeki tüm anahtarları çözebilecek bir anahtar çözümleyici belirtmeniz gerekir. Sorgu sonucunda içerilen bir varlık bir sağlayıcıya çözümlenemiyorsa, istemci kitaplığı bir hata oluşturur. Sunucu tarafı projeksiyonları gerçekleştiren sorgular için, istemci kitaplığı, varsayılan olarak seçilen sütunlara özel şifreleme meta verileri\_özelliklerini ( \_ClientEncryptionMetadata1 ve ClientEncryptionMetadata2) ekler.
> 
> [!IMPORTANT]
> İstemci tarafı şifrelemeyi kullanırken bu önemli noktalara dikkat edin:
> 
> * Şifrelenmiş bir bloba okurken veya yazarken, tüm blob karşıya yükleme komutlarını ve Aralık/tam blob indirme komutlarını kullanın. Put bloğu, put bloğu listesi, sayfa yazma veya sayfa temizleme gibi protokol işlemlerini kullanarak şifrelenmiş bir blob 'a yazma yapmaktan kaçının; Aksi takdirde, şifreli blobu bozuyor ve okunamaz hale getirebilirsiniz.
> * Tablolar için, benzer bir kısıtlama vardır. Şifrelenmiş özellikleri, şifreleme meta verilerini güncelleştirmeden güncelleştirmemeye dikkat edin.
> * Şifrelenmiş blob üzerinde meta verileri ayarlarsanız, verilerin ayarlanmamasından dolayı, şifre çözme için gereken şifrelemeyle ilgili meta verilerin üzerine yazabilirsiniz. Bu, anlık görüntüler için de geçerlidir; şifrelenmiş bir Blobun anlık görüntüsünü oluştururken meta verileri belirtmekten kaçının. Meta verilerin ayarlanması gerekiyorsa, geçerli şifreleme meta verilerini almak için önce **get_blob_metadata** yöntemini çağırdığınızdan emin olun ve meta veriler ayarlanmakta iken eşzamanlı yazmaları önleyin.
> * Yalnızca şifrelenmiş verilerle çalışması gereken kullanıcılar için hizmet nesnesinde **require_encryption** bayrağını etkinleştirin. Daha fazla bilgi için aşağıya bakın.

Depolama istemci kitaplığı, aşağıdaki arabirimi uygulamak için belirtilen KEK ve anahtar çözümleyici 'yi bekler. Python KEK yönetimi için [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) desteği beklemede ve tamamlandığında bu kitaplıkla tümleştirilecektir.

## <a name="client-api--interface"></a>İstemci API 'SI/arabirimi
Bir depolama hizmeti nesnesi (ör. blockblobservice) oluşturulduktan sonra, Kullanıcı bir şifreleme ilkesi oluşturan alanlara değerler atayabilir: key_encryption_key, key_resolver_function ve require_encryption. Kullanıcılar yalnızca bir KEK, yalnızca çözümleyici veya her ikisini birden sağlayabilir. key_encryption_key, anahtar tanımlayıcısı kullanılarak tanımlanan ve sarmalama/sarmalama mantığını sağlayan temel anahtar türüdür. key_resolver_function, şifre çözme işlemi sırasında bir anahtarı çözümlemek için kullanılır. Anahtar tanımlayıcı verilen geçerli bir KEK döndürür. Bu, kullanıcılara birden çok konumda yönetilen birden çok anahtar arasında seçim yapma olanağı sağlar.

KEK, verileri başarıyla şifrelemek için aşağıdaki yöntemleri gerçekleştirmelidir:

* wrap_key(cek): Kullanıcının tercih ettiği algoritmayı kullanarak belirtilen CEK (bayt) sayısını kaydırır. Sarmalanan anahtarı döndürür.
* get_key_wrap_algorithm(): Anahtarları kaydırmak için kullanılan algoritmayı döndürür.
* get_kid(): Bu KEK için dize anahtar kimliğini döndürür.
  KEK, verilerin şifresini başarıyla çözmek için aşağıdaki yöntemleri uygulamalıdır:
* unwrap_key (cek, algoritma): Belirtilen CEK dize tarafından belirtilen algoritmayı kullanarak sarmalanmamış biçimini döndürür.
* get_kid(): Bu KEK için bir dize anahtar kimliği döndürür.

Anahtar çözümleyici, en az bir anahtar kimliği verilen bir yöntemi, yukarıdaki arabirimi uygulayan karşılık gelen KEK döndürür. Yalnızca bu yöntem, hizmet nesnesi üzerindeki key_resolver_function özelliğine atanmalıdır.

* Şifreleme için, anahtar her zaman kullanılır ve bir anahtarın yokluğu bir hataya neden olur.
* Şifre çözme için:

  * Anahtarı almak için belirtilmişse anahtar çözümleyici çağrılır. Çözümleyici belirtilmişse ancak anahtar tanımlayıcısı için bir eşleme yoksa bir hata oluşur.
  * Çözümleyici belirtilmemişse ancak anahtar belirtilmişse, tanımlayıcı gerekli anahtar tanımlayıcısıyla eşleşiyorsa anahtar kullanılır. Tanımlayıcı eşleşmiyorsa bir hata oluşur.

    Azure. Storage. Samples içindeki şifreleme örnekleri, Bloblar, kuyruklar ve tablolar için daha ayrıntılı bir uçtan uca senaryoyu gösterir.
      KEK ve Key Resolver 'ın örnek uygulamaları sırasıyla anahtar sarmalayıcı ve KeyResolver olarak örnek dosyalarda verilmiştir.

### <a name="requireencryption-mode"></a>RequireEncryption modu
Kullanıcılar isteğe bağlı olarak, tüm karşıya yüklemeler ve indirmelerin şifrelenmesi gereken bir işlem modunu etkinleştirebilir. Bu modda, şifreleme ilkesi olmadan veya hizmette şifrelenmeyen verileri karşıdan yükleme girişimleri istemcide başarısız olur. Hizmet nesnesindeki **require_encryption** bayrağı bu davranışı denetler.

### <a name="blob-service-encryption"></a>Blob hizmeti şifrelemesi
Blockblobservice nesnesindeki şifreleme ilkesi alanlarını ayarlayın. Diğer her şey, dahili olarak istemci kitaplığı tarafından işlenir.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Kuyruk hizmeti şifreleme
QueueService nesnesindeki şifreleme ilkesi alanlarını ayarlayın. Diğer her şey, dahili olarak istemci kitaplığı tarafından işlenir.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Tablo hizmeti şifrelemesi
Bir şifreleme ilkesi oluşturmaya ve istek seçeneklerinde ayarlamaya ek olarak, **tableservice**üzerinde bir **encryption_resolver_function** belirtmeniz ya da EntityProperty üzerindeki Encrypt özniteliğini ayarlamanız gerekir.

### <a name="using-the-resolver"></a>Çözümleyici 'yi kullanma

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Öznitelikleri kullanma
Yukarıda belirtildiği gibi, bir özellik bir EntityProperty nesnesinde depolanarak ve şifreleme alanı ayarlanarak şifreleme için işaretlenebilir.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Şifreleme ve performans
Depolama verilerinizi şifrelerken ek performans yükü ile sonuçlandığına göz önünde unutmayın. İçerik anahtarı ve IV oluşturulmalıdır, içeriğin kendisi şifrelenmeli ve ek meta verilerin biçimlendirilmesi ve karşıya yüklenmesi gerekir. Bu ek yük, Şifrelenmekte olan veri miktarına bağlı olarak farklılık gösterecektir. Müşterilerin geliştirme sırasında uygulamalarının performansını her zaman test etmenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar
* [Java Pypı paketi Için Azure depolama Istemci kitaplığı](https://pypi.python.org/pypi/azure-storage) 'nı indirin
* [GitHub 'Dan Python kaynak kodu Için Azure Storage Istemci kitaplığı](https://github.com/Azure/azure-storage-python) 'nı indirin
