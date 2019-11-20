---
title: Azure Key Vault anahtarlar, gizli diziler ve sertifikalar hakkında-Azure Key Vault
description: Anahtarlar, gizli diziler ve sertifikalar için Azure Key Vault REST arabirimine ve geliştirici ayrıntılarına genel bakış.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3024d77c02f623f8b8dc1a8956e692c208c8c9e5
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799407"
---
# <a name="about-keys-secrets-and-certificates"></a>Anahtarlar, gizli diziler ve sertifikalar hakkında

Azure Key Vault, Microsoft Azure uygulamaların ve kullanıcıların çeşitli türlerdeki gizli/anahtar verileri depolamasını ve kullanmasını sağlar:

- Şifreleme anahtarları: birden çok anahtar türünü ve algoritmaları destekler ve yüksek değerli anahtarlar için donanım güvenlik modüllerinin (HSM) kullanılmasını mümkün. 
- Gizlilikler: parolalar ve veritabanı bağlantı dizeleri gibi güvenli parolaların depolanmasını sağlar.
- Sertifikalar: anahtarların ve parolaların üzerine inşa edilen sertifikaları destekler ve otomatik yenileme özelliği ekler.
- Azure depolama: bir Azure depolama hesabının anahtarlarını sizin için yönetebilir. Dahili olarak, Key Vault anahtarları Azure Storage hesabıyla listeleyebilir (eşitleyebilir) ve anahtarları düzenli olarak yeniden oluşturabilir (döndürün). 

Key Vault hakkında daha fazla genel bilgi için bkz. [Azure Key Vault nedir?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Aşağıdaki bölümler Key Vault hizmetinin uygulanması için geçerli olan genel bilgileri sunmaktadır.

### <a name="supporting-standards"></a>Destek standartları

JavaScript Nesne Gösterimi (JSON) ve JavaScript nesne Imzalama ve şifreleme (Joo) belirtimleri önemli arka plan bilgileri.  

-   [JSON Web anahtarı (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON Web şifrelemesi (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON Web algoritmaları (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON Web Imzası (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Veri türleri

Anahtarlar, şifreleme ve imzalama için ilgili veri türleri için JOTE belirtimlerine bakın.  

-   **algoritma** -anahtar işlemi için desteklenen bir algoritma, ÖRNEĞIN, RSA1_5  
-   **şifreli-değer** -şifre metin Sekizlilileri, Base64URL kullanarak kodlanmış  
-   **Özet-değer** -Base64URL kullanılarak kodlanan karma algoritmanın çıkışı  
-   **anahtar türü** -desteklenen anahtar türlerinden biri, örneğin RSA (Rivest-Shamir-Adliman).  
-   **düz metin-değer** -düz metin sekizlik, Base64URL kullanılarak kodlanır  
-   **imza-değer** -Base64URL kullanılarak kodlanan imza algoritmasının çıkışı  
-   **base64URL** -a BASE64URL [RFC4648] kodlamalı ikili değer  
-   **Boolean** -true veya false  
-   **Kimlik** -Azure Active Directory bir KIMLIK (AAD).  
-   **Intdate** -belirtilen UTC tarih/saatine kadar 1970-01-01T0:0: 0z UTC 'den saniye sayısını temsil eden bir JSON ondalık değeri. Genel ve UTC 'de tarih/saat ile ilgili ayrıntılar için bkz. RFC3339.  

### <a name="objects-identifiers-and-versioning"></a>Nesneler, tanımlayıcılar ve sürüm oluşturma

Key Vault depolanan nesneler, bir nesnenin yeni bir örneği oluşturulduğunda sürümlüdür. Her sürüme benzersiz bir tanımlayıcı ve URL atanır. Bir nesne ilk oluşturulduğunda, benzersiz bir sürüm tanımlayıcısı verilir ve nesnenin geçerli sürümü olarak işaretlenir. Aynı nesne adına sahip yeni bir örnek oluşturmak, yeni nesneye benzersiz bir sürüm tanımlayıcısı sağlar ve bunun geçerli sürüm olmasına neden olur.  

Key Vault nesneler, geçerli tanımlayıcı veya sürüme özgü tanımlayıcı kullanılarak çözülebilir. Örneğin, `MasterKey`adında bir anahtar verildiğinde, geçerli tanımlayıcıyla işlem gerçekleştirmek sistemin kullanılabilir en son sürümü kullanmasına neden olur. Sürüme özgü tanımlayıcıyla işlem gerçekleştirmek sistemin nesnenin o belirli sürümünü kullanmasına neden olur.  

Nesneler bir URL kullanarak Key Vault içinde benzersiz şekilde tanımlanır. Coğrafi konumdan bağımsız olarak sistemdeki iki nesne aynı URL 'ye sahip değildir. Bir nesnenin tüm URL 'SI, nesne tanımlayıcısı olarak adlandırılır. URL, Key Vault, nesne türü, Kullanıcı tarafından sağlanmış nesne adı ve bir nesne sürümü tanımlayan bir önekden oluşur. Nesne adı büyük/küçük harfe duyarlıdır ve sabittir. Nesne sürümünü içermeyen tanımlayıcılar temel tanımlayıcılar olarak adlandırılır.  

Daha fazla bilgi için bkz. [kimlik doğrulaması, istekler ve yanıtlar](authentication-requests-and-responses.md)

Bir nesne tanımlayıcısı aşağıdaki genel biçime sahiptir:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Nerede:  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault hizmetindeki bir anahtar kasasının adı.<br /><br /> Key Vault adlar Kullanıcı tarafından seçilir ve genel olarak benzersizdir.<br /><br /> Key Vault ad, yalnızca 0-9, a-z, A-Z ve-içeren bir 3-24 karakter dizesi olmalıdır.|  
|`object-type`|Nesnenin türü, "anahtarlar" veya "gizlilikler" olabilir.|  
|`object-name`|`object-name`, için Kullanıcı tarafından sağlanmış bir addır ve bir Key Vault içinde benzersiz olmalıdır. Ad, yalnızca 0-9, a-z, A-Z ve-içeren bir 1-127 karakter dizesi olmalıdır.|  
|`object-version`|`object-version`, isteğe bağlı olarak kullanılan * o bir nesnenin benzersiz bir sürümünü ele alan sistem tarafından 32 oluşturulmuş bir karakter dizesi tanımlayıcısıdır.|  

## <a name="key-vault-keys"></a>Key Vault anahtarları

### <a name="keys-and-key-types"></a>Anahtarlar ve anahtar türleri

Key Vault içindeki şifreleme anahtarları JSON Web anahtarı [JWK] nesneleri olarak gösterilir. Temel JWK/JWA belirtimleri de Key Vault uygulamasına özgü anahtar türlerini etkinleştirmek üzere genişletilir. Örneğin, HSM satıcıya özgü paketleme kullanarak anahtarları içeri aktarmak, yalnızca Key Vault HSM 'lerde kullanılabilecek anahtarların güvenli bir şekilde aktarılmasına izin verebilir.  

- **"Geçici" anahtarlar**: yazılımda Key Vault tarafından işlenen, ancak bir HSM 'de olan bir sistem anahtarı kullanılarak Rest 'te şifrelenen bir anahtar. İstemciler mevcut bir RSA veya EC (Eliptik Eğri) anahtarını içeri aktarabilir veya Key Vault oluşturmak isteyebilir.
- **"Hard" anahtarları**: bir HSM 'de işlenen anahtar (donanım güvenlik modülü). Bu anahtarlar Key Vault HSM güvenlik dünyasının birinde korunur (yalıtımı sürdürmek için coğrafya başına bir güvenlik dünyası vardır). İstemciler, bir RSA veya EC anahtarını, geçici biçimde veya uyumlu bir HSM cihazından dışarı aktararak içeri aktarabilir. İstemciler bir anahtar oluşturmak için Key Vault da isteyebilir. Bu anahtar türü, HSM anahtar malzemesini taşımak için key_hsm özniteliğini JWK alma 'ya ekler.

     Coğrafi sınırlar hakkında daha fazla bilgi için bkz. [Microsoft Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault yalnızca RSA ve eliptik eğri tuşlarını destekler. 

-   **EC**: "Soft" eliptik eğri anahtarı.
-   **EC-HSM**: "Hard" eliptik eğri anahtarı.
-   **RSA**: "Soft" RSA anahtarı.
-   **RSA-HSM**: "Hard" RSA anahtarı.

Key Vault, 2048, 3072 ve 4096 boyutlarının RSA anahtarlarını destekler. Key Vault, P-256, P-384, P-521 ve P-256K (SECP256K1) eliptik eğri anahtar türlerini destekler.

### <a name="cryptographic-protection"></a>Şifreleme koruması

Key Vault kullandığı şifreleme modülleri, HSM veya yazılımın FIPS (Federal bilgi Işleme standartları) tarafından doğrulanıp onaylanmayacağı. FIPS modunda çalıştırmak için özel bir şey yapmanız gerekmez. HSM korumalı olarak **oluşturulan** veya **içeri AKTARıLAN** anahtarlar, FIPS 140-2 düzey 2 olarak doğrulanan bir HSM içinde işlenir. Yazılım korumalı olarak **oluşturulan** veya **IÇERI aktarılan** anahtarlar FIPS 140-2 düzey 1 ' e doğrulanan şifreleme modülleri içinde işlenir. Daha fazla bilgi için bkz. [anahtarlar ve anahtar türleri](#keys-and-key-types).

###  <a name="ec-algorithms"></a>EC algoritmaları
 Aşağıdaki algoritma tanımlayıcıları Key Vault içindeki EC ve EC-HSM anahtarları ile desteklenir. 

#### <a name="curve-types"></a>Eğri türleri

-   **P-256** - [DSS FIPS Yayın 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)' de tanımlanan nıst eğrisi p-256.
-   **P-256k** -sn eğrisi SECP256K1, tanımlanan, [2: önerilen eliptik eğri etki alanı parametreleri](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - [DSS FIPS Yayın 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)' de tanımlanan nıst eğrisi p-384.
-   **P-521** - [DSS FIPS Yayın 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)' de tanımlanan nıst eğrisi p-521.

#### <a name="signverify"></a>IMZALA/DOĞRULA

-   SHA-256 özetleri için **ES256** -ECDSA ve P-256 eğrisi ile oluşturulan anahtarlar. Bu algoritma [RFC7518](https://tools.ietf.org/html/rfc7518)adresinde açıklanmaktadır.
-   SHA-256 özetleri için **ES256K** -ECDSA ve P-256k eğrisi ile oluşturulan anahtarlar. Bu algoritma bekleyen standartmadır.
-   SHA-384 özetleri için **ES384** -ECDSA ve P-384 eğrisi ile oluşturulan anahtarlar. Bu algoritma [RFC7518](https://tools.ietf.org/html/rfc7518)adresinde açıklanmaktadır.
-   SHA-512 özetleri için **ES512** -ECDSA ve P-521 eğrisi ile oluşturulan anahtarlar. Bu algoritma [RFC7518](https://tools.ietf.org/html/rfc7518)adresinde açıklanmaktadır.

###  <a name="rsa-algorithms"></a>RSA algoritmaları  
 Aşağıdaki algoritma tanımlayıcıları Key Vault içindeki RSA ve RSA-HSM anahtarları ile desteklenir.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ŞIFRELEME/ŞIFRE ÇÖZME

-   **RSA1_5** -rsaes-PKCS1-V1_5 [RFC3447] anahtar şifreleme  
-   **Rsa-oaep** -bir. 2.1 bölümünde RFC 3447 tarafından belirtilen varsayılan parametrelerle En Iyi asimetrik şifreleme dolgusu (OAEP) [RFC3447] kullanılarak rsaes. Bu varsayılan parametreler, SHA-1 ' in bir karma işlevini ve MGF1 'in bir SHA-1 ile bir maske oluşturma işlevini kullanıyor.  

#### <a name="signverify"></a>IMZALA/DOĞRULA

-   **RS256** -RSASSA-PKCS-V1_5, SHA-256 kullanılarak. Uygulamanın sağladığı Özet değeri SHA-256 kullanılarak hesaplanmalıdır ve 32 bayt uzunluğunda olmalıdır.  
-   **RS384** -RSASSA-PKCS-V1_5, SHA-384 kullanılarak. Uygulamanın sağladığı Özet değeri SHA-384 kullanılarak hesaplanmalıdır ve 48 bayt uzunluğunda olmalıdır.  
-   **RS512** -RSASSA-PKCS-V1_5, SHA-512 kullanılarak. Uygulamanın sağladığı Özet değeri SHA-512 kullanılarak hesaplanmalıdır ve 64 bayt uzunluğunda olmalıdır.  
-   **Rsnull** : belirli TLS senaryolarını etkinleştirmek için özel kullanım örneği olan [RFC2437].  

###  <a name="key-operations"></a>Anahtar işlemleri

Key Vault, anahtar nesnelerinde aşağıdaki işlemleri destekler:  

-   **Oluştur**: bir istemcinin Key Vault bir anahtar oluşturmasına izin verir. Anahtarın değeri Key Vault tarafından oluşturulur ve depolanır ve istemciye serbest bırakılır. Asimetrik anahtarlar Key Vault oluşturulabilir.  
-   **Içeri aktarma**: bir istemcinin mevcut bir anahtarı Key Vault içeri aktarmasını sağlar. Asimetrik anahtarlar, bir JWK yapısı içinde çok sayıda farklı paketleme yöntemi kullanılarak Key Vault içeri aktarılabilir. 
-   **Güncelleştirme**: daha önce Key Vault içinde depolanan bir anahtarla ilişkili meta verileri (anahtar öznitelikleri) değiştirmek için yeterli izinlere sahip bir istemciye izin verir.  
-   **Sil**: Key Vault ' dan bir anahtarı silmek için yeterli izinlere sahip bir istemciye izin verir.  
-   **Liste**: bir istemcinin belirli bir Key Vault tüm anahtarları listeetmesine izin verir.  
-   **Sürümleri Listele**: bir istemcinin belirli bir Key Vault verilen bir anahtarın tüm sürümlerini listeetmesine izin verir.  
-   **Get**: bir istemcinin, bir Key Vault verilen bir anahtarın ortak parçalarını almasına izin verir.  
-   **Yedekleme**: bir anahtarı korumalı bir biçimde dışa aktarır.  
-   **Geri yükle**: daha önce yedeklenen bir anahtarı içeri aktarır.  

Daha fazla bilgi için [Key Vault REST API başvuru Içindeki anahtar işlemleri](/rest/api/keyvault)konusuna bakın.  

Key Vault bir anahtar oluşturulduktan sonra anahtar kullanılarak aşağıdaki şifreleme işlemleri gerçekleştirilebilir:  

-   **İmzala ve Doğrula**: kesinlikle, bu işlem "imza karması" veya "doğrulama karması" olduğundan Key Vault imza oluşturma işleminin bir parçası olarak içeriğin karmasını desteklemez. Uygulamalar yerel olarak imzalanacak verileri karma hale almalıdır, sonra Key Vault karmayı imzalamasını ister. İmzalı karmaların doğrulanması, [Genel] anahtar malzemesine erişimi olmayan uygulamalar için kolaylık olarak desteklenir. En iyi uygulama performansı için, işlemlerin yerel olarak gerçekleştirildiğini doğrulayın.  
-   **Anahtar şifreleme/sarmalama**: Key Vault depolanan bir anahtar, genellikle bir simetrik içerik şifreleme anahtarı (cek) ile başka bir anahtarı korumak için kullanılabilir. Key Vault anahtarı asimetrik olduğunda, anahtar şifreleme kullanılır. Örneğin, RSA-OAEP ve WRAPKEY/UNWRAPKEY işlemleri ŞIFRELEME/ŞIFRE çözme ile eşdeğerdir. Key Vault anahtar simetrik olduğunda, anahtar kaydırma kullanılır. Örneğin, AES-KW. WRAPKEY işlemi, [public] anahtar malzemesine erişimi olmayan uygulamalar için kolaylık olarak desteklenir. En iyi uygulama performansı için WRAPKEY işlemleri yerel olarak gerçekleştirilmelidir.  
-   **Şifreleme ve şifre çözme**: Key Vault depolanan bir anahtar, tek bir veri bloğunu şifrelemek veya şifresini çözmek için kullanılabilir. Bloğun boyutu anahtar türüne ve seçili şifreleme algoritmasına göre belirlenir. Şifreleme işlemi, [Genel] anahtar malzemesine erişimi olmayan uygulamalar için kolaylık sağlaması için sağlanır. En iyi uygulama performansı için, şifreleme işlemleri yerel olarak gerçekleştirilmelidir.  

Asimetrik anahtarları kullanarak WRAPKEY/UNWRAPKEY gereksiz görünebilir (işlem, ŞIFRELEME/ŞIFRE çözme ile eşdeğerdir), farklı işlemlerin kullanılması önemlidir. Ayrım, bu işlemlere yönelik anlam ve yetkilendirme ayrımı sağlar ve hizmet tarafından diğer anahtar türleri desteklenirse tutarlılığa sahiptir.  

Key Vault dışarı aktarma işlemlerini desteklemez. Sistemde bir anahtar sağlandıktan sonra, bu ayıklanamaz veya anahtar malzemeleri değiştirilemez. Ancak, Key Vault kullanıcıları, oluşturulduktan sonra gibi diğer kullanım durumları için kendi anahtarını gerektirebilir. Bu durumda, anahtarı korumalı bir biçimde dışa/dışarı aktarmak için yedekleme ve GERI yükleme işlemlerini kullanabilirler. Yedekleme işlemi tarafından oluşturulan anahtarlar Key Vault dışında kullanılamaz. Alternatif olarak, IÇERI aktarma işlemi birden çok Key Vault örneğine karşı kullanılabilir.  

Kullanıcılar, JWK nesnesinin key_ops özelliğini kullanarak her anahtar temelinde Key Vault desteklediği şifreleme işlemlerini kısıtlayabilir.  

JWK nesneleri hakkında daha fazla bilgi için bkz. [JSON Web anahtarı (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Anahtar öznitelikleri

Anahtar malzemeye ek olarak, aşağıdaki öznitelikler de belirtilebilir. Bir JSON Isteğinde, belirtilen öznitelik olmadığından bile ' {' '} ' öznitelikler anahtar sözcüğü ve ayraçları gereklidir.  

- *etkin*: Boolean, isteğe bağlı, varsayılan değer **true**'dur. Anahtarın etkinleştirilip etkinleştirilmeyeceğini ve şifreleme işlemleri için kullanılıp kullanılamayacağını belirtir. *Enabled* özniteliği, *NBF* ve *Exp*ile birlikte kullanılır. *NBF* ve *Exp*arasında bir işlem olduğunda, yalnızca *etkin* değeri **true**olarak ayarlandıysa izin verilir. [Belirli koşullarda](#date-time-controlled-operations)belirli işlem türleri dışında, *NBF* / *Exp* penceresinin dışındaki işlemlere otomatik olarak izin verilmez.
- *NBF*: intdate, isteðe baðlý, default artık. *NBF* (before) özniteliği, [belirli koşullarda](#date-time-controlled-operations)belirli işlem türleri dışında, anahtarın ŞIFRELEME işlemleri için kullanılması gereken süreyi tanımlar. *NBF* özniteliğinin işlenmesi, geçerli tarih/saatin, *NBF* özniteliğinde listelenen son tarih/saat değerinden önce veya buna eşit olması gerekir. Key Vault saat eğimlerini hesaba çıkarak, genellikle birkaç dakikadan daha uzun bir süre boyunca daha fazla bilgi verebilir. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR.  
- *Exp*: intdate, isteðe baðlý, Default, "sonsuza kadar". *Exp* (sona erme saati) özniteliği, [belirli koşullarda](#date-time-controlled-operations)belirli işlem türleri dışında, anahtarın ŞIFRELEME işlemi için kullanılması gereken süre sonu zamanını belirler. *Exp* özniteliğinin işlenmesi, geçerli tarih/saatin, *Exp* özniteliğinde listelenen süre sonu tarih/saatinden önce olması gerekir. Key Vault, saat eğimlerini hesaba çıkarak, genellikle birkaç dakikadan daha kısa bir süre boyunca daha fazla bilgi sağlayabilir. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR.  

Anahtar öznitelikleri içeren herhangi bir yanıtta yer alan ek salt okuma öznitelikleri vardır:  

- *oluşturma*: Int32 Tarih, isteğe bağlı. *Oluşturulan* öznitelik, anahtarın bu sürümünün ne zaman oluşturulduğunu gösterir. Bu özniteliğin eklenmesinden önce oluşturulan anahtarlar için değer null. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR.  
- *güncelleştirildi*: Int32 tarihi, isteğe bağlı. *Updated* özniteliği, anahtarın bu sürümünün ne zaman güncelleştirildiğini gösterir. Bu özniteliğin eklenmesiyle önce en son güncellenen anahtarlar için değer null. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR.  

IntDate ve diğer veri türleri hakkında daha fazla bilgi için bkz. [veri türleri](#data-types)  

#### <a name="date-time-controlled-operations"></a>Tarih-saat denetimli işlemler

Henüz geçerli olmayan ve süresi dolmayan anahtarlar, *nbf* / *Exp* penceresi dışında, **şifre çözme**, **sarmalama**ve **doğrulama** işlemleri için (403 döndürmez, yasak) çalışacaktır. Henüz geçerli olmayan durumu kullanmaya yönelik korvaale, bir anahtarın üretim kullanılmadan önce test edilmeye izin vermektir. Kullanım tarihi geçen durum kullanımı için, anahtar geçerli olduğunda oluşturulan verilerdeki kurtarma işlemlerine izin vermektir. Ayrıca, Key Vault ilkeleri kullanarak bir anahtara erişimi devre dışı bırakabilir ya da *etkin* anahtar özniteliğini **yanlış**olarak güncelleyerek.

Veri türleri hakkında daha fazla bilgi için bkz. [veri türleri](#data-types).

Diğer olası öznitelikler hakkında daha fazla bilgi için bkz. [JSON Web anahtarı (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Anahtar etiketleri

Etiketler biçiminde uygulamaya özgü ek meta verileri belirtebilirsiniz. Key Vault, her birinin 256 karakter adı ve 256 karakter değeri olabilen en fazla 15 etiketi destekler.  

>[!Note]
>Bu nesne türü (anahtarlar, gizlilikler veya sertifikalar) için *liste* veya *Al* izni varsa, Etiketler bir çağıran tarafından okunabilir.

###  <a name="key-access-control"></a>Anahtar erişim denetimi

Key Vault tarafından yönetilen anahtarlar için erişim denetimi, anahtarların kapsayıcısı olarak davranan bir Key Vault düzeyinde sağlanır. Anahtarlar için erişim denetimi ilkesi, aynı Key Vault gizli dizi için erişim denetimi ilkesinden farklıdır. Kullanıcılar anahtarları tutmak için bir veya daha fazla kasa oluşturabilir ve senaryonun uygun segmentlerini ve anahtarların yönetimini sürdürmek için gereklidir. Anahtarlar için erişim denetimi, gizli dizileri için erişim denetiminden bağımsızdır.  

Bir kasadaki anahtarlar erişim denetimi girişinde, Kullanıcı başına/hizmet sorumlusu temelinde aşağıdaki izinler verilebilir. Bu izinler, anahtar nesnesinde izin verilen işlemleri yakından yansıtır.  Anahtar kasasındaki bir hizmet sorumlusuna erişim verilmesi, kerelik bir işlemdir ve tüm Azure abonelikleri için aynı kalacaktır. Bunu istediğiniz sayıda sertifika dağıtmak için kullanabilirsiniz. 

- Anahtar yönetimi işlemlerine yönelik izinler
  - *Al*: bir anahtarın ortak bölümünü ve özniteliklerini okuyun
  - *liste*: anahtar kasasında depolanan bir anahtarın anahtarlarını veya sürümlerini listeleyin
  - *güncelleştirme*: bir anahtarın özniteliklerini güncelleştirme
  - *Oluştur*: yeni anahtar oluştur
  - *içeri aktarma*: anahtar kasasına anahtar aktarma
  - *Sil*: anahtar nesnesini Sil
  - *kurtar*: silinen bir anahtarı kurtar
  - *yedekleme*: anahtar kasasında anahtar yedekleme
  - *geri yükleme*: Yedeklenen anahtarı bir anahtar kasasına geri yükleme

- Şifreleme işlemlerine yönelik izinler
  - *şifre çözme*: bir bayt dizisinin korumasını kaldırmak için anahtarı kullanın
  - *şifreleme*: rastgele bir bayt dizisini korumak için anahtarı kullanın
  - *unwrapKey*: Sarmalanan simetrik anahtarların korumasını kaldırmak için anahtarı kullanın
  - *wrapKey*: bir simetrik anahtarı korumak için anahtarı kullanın
  - *Doğrula*: özetleri 'yi doğrulamak için anahtarı kullanın  
  - *imzala*: anahtarı kullanarak özetleri 'yi imzalayın
    
- Ayrıcalıklı işlemler için izinler
  - *Temizle*: silinen bir anahtarı temizle (kalıcı olarak sil)

Anahtarlarla çalışma hakkında daha fazla bilgi için bkz. [Key Vault REST API başvurusu Içindeki anahtar işlemleri](/rest/api/keyvault). İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Key Vault gizli dizileri 

### <a name="working-with-secrets"></a>Gizli dizilerle çalışma

Geliştirici perspektifinden Key Vault API 'Leri, gizli değerleri dizeler olarak kabul eder ve döndürür. Dahili olarak Key Vault, gizli dizileri, her biri en fazla 25k bayt boyutunda olan sekizlik (8 bit bayt) dizileri olarak depolar ve yönetir. Key Vault hizmeti gizli dizileri için semantik bir sağlamaz. Yalnızca verileri kabul eder, şifreler, kaydeder ve gizli bir tanımlayıcı ("kimlik") döndürür. Tanımlayıcı, daha sonraki bir zamanda gizli dizi almak için kullanılabilir.  

Son derece hassas veriler için istemciler, veriler için ek koruma katmanları düşünmelidir. Key Vault depolama öncesinde ayrı bir koruma anahtarı kullanarak verileri şifrelemek bir örnektir.  

Key Vault, gizlilikler için bir contentType alanını da destekler. İstemciler, alındığı sırada gizli verilerin yorumlanmasına yardımcı olmak için bir gizli dizi içerik türünü belirtebilir. Bu alanın uzunluk üst sınırı 255 karakterdir. Önceden tanımlı değer yok. Önerilen kullanım, gizli verileri yorumlamak için bir ipucu olarak kullanılır. Örneğin, bir uygulama parolaları ve sertifikaları gizli dizi olarak saklayabilir, ardından bu alanı ayırt etmek için kullanabilirsiniz. Önceden tanımlanmış değer yok.  

### <a name="secret-attributes"></a>Gizli dizi öznitelikleri

Gizli verilerin yanı sıra, aşağıdaki öznitelikler de belirtilebilir:  

- *Exp*: intdate, isteðe baðlý, Default, **sonsuza**kadar. *Exp* (sona erme saati) özniteliği, [belirli durumlar](#date-time-controlled-operations)dışında, gizli verilerin alınmayabileceği veya sonrasında sona erme süresini belirler. Bu alan yalnızca, Anahtar Kasası Hizmeti kullanıcılarına belirli bir parolanın kullanılamayacağını bildiren **bilgilendirme** amaçlıdır. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR.   
- *NBF*: intdate, isteðe baðlý, Default **artık**. *NBF* (before) özniteliği, [belirli durumlar](#date-time-controlled-operations)dışında gizli verilerin alınamadığı süreyi tanımlar. Bu alan yalnızca **bilgilendirme** amaçlıdır. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR. 
- *etkin*: Boolean, isteğe bağlı, varsayılan değer **true**'dur. Bu öznitelik, gizli verilerin alınıp alınamayacağını belirtir. Enabled özniteliği *, NBF ve* *Exp*arasında bir işlem olduğunda, *NBF* *ve exp* ile birlikte kullanıldığında, yalnızca Enabled **değeri true**olarak ayarlandığında izin verilir. [Belirli durumlar](#date-time-controlled-operations)dışında, *NBF* ve *Exp* penceresinin dışındaki işlemlere otomatik olarak izin verilmez.  

Gizli öznitelikler içeren herhangi bir yanıta dahil olan ek salt okuma öznitelikleri vardır:  

- *oluşturma*: Int32 Tarih, isteğe bağlı. Oluşturulan öznitelik, bu gizli anahtar sürümünün ne zaman oluşturulduğunu gösterir. Bu değer, bu özniteliğin eklenmesinden önce oluşturulan gizli diziler için null. Değeri, bir IntDate değeri içeren bir sayı olmalıdır.  
- *güncelleştirildi*: Int32 tarihi, isteğe bağlı. Updated özniteliği, gizli anahtar sürümünün ne zaman güncelleştirildiğini gösterir. Bu değer, bu özniteliğin eklenmesinden önce son güncellenen gizli diziler için null. Değeri, bir IntDate değeri içeren bir sayı olmalıdır.

#### <a name="date-time-controlled-operations"></a>Tarih-saat denetimli işlemler

Bir gizli anahtar **Al** işlemi, *NBF* / *Exp* penceresi dışında, henüz geçerli olmayan ve süresi dolmayan gizli diziler için çalışacaktır. Gizli olmayan bir gizli dizi için bir gizli dizi **Al** işlemi çağırmak, test amacıyla kullanılabilir. Bir zaman aşımına uğradı (**alma**), kurtarma işlemleri için kullanılabilir.

Veri türleri hakkında daha fazla bilgi için bkz. [veri türleri](#data-types).  

### <a name="secret-access-control"></a>Parola erişim denetimi

Key Vault yönetilen gizli dizileri için Access Control, bu gizli dizileri içeren Key Vault düzeyinde sağlanır. Gizli dizileri için erişim denetimi ilkesi, aynı Key Vault anahtarlar için erişim denetimi ilkesinden farklıdır. Kullanıcılar, gizli dizileri tutmak için bir veya daha fazla kasa oluşturabilir ve senaryoya uygun bölümlemeye ve gizli dizi yönetimine devam etmek için gereklidir.   

Aşağıdaki izinler, her bir kasada, bir kasadaki gizli dizi erişim denetimi girdisinde kullanılabilir ve gizli bir nesne üzerinde izin verilen işlemleri yakından yansıtır:  

- Gizli dizi yönetimi işlemlerine yönelik izinler
  - *Al*: gizli dizi oku  
  - *liste*: Key Vault depolanan bir gizli dizinin gizli dizilerini veya sürümlerini listeleyin  
  - *Ayarla*: gizli dizi oluştur  
  - *Sil*: gizli dizi silme  
  - *kurtar*: silinen gizli dizi kurtarma
  - *yedekleme*: anahtar kasasında gizli dizi yedekleme
  - *geri yükleme*: yedeklenen gizli anahtarı bir anahtar kasasına geri yükleme

- Ayrıcalıklı işlemler için izinler
  - *Temizle*: silinen bir parolayı temizle (kalıcı olarak sil)

Gizli dizileri ile çalışma hakkında daha fazla bilgi için, [Key Vault REST API başvurusunda gizli işlemler](/rest/api/keyvault)bölümüne bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Gizli Etiketler  
Etiketler biçiminde uygulamaya özgü ek meta verileri belirtebilirsiniz. Key Vault, her birinin 256 karakter adı ve 256 karakter değeri olabilen en fazla 15 etiketi destekler.  

>[!Note]
>Bu nesne türü (anahtarlar, gizlilikler veya sertifikalar) için *liste* veya *Al* izni varsa, Etiketler bir çağıran tarafından okunabilir.

## <a name="key-vault-certificates"></a>Key Vault sertifikaları

Key Vault sertifikaları, x509 sertifikalarınızın ve aşağıdaki davranışların yönetimi için destek sağlar:  

-   Bir sertifika sahibinin Key Vault oluşturma işlemiyle veya var olan bir sertifikayı içeri aktarmasından sertifika oluşturmasına izin verir. Hem otomatik olarak imzalanan hem de sertifika yetkilisi tarafından oluşturulan sertifikaları içerir.
-   Bir Key Vault sertifika sahibinin, özel anahtar malzemesiyle etkileşime girmeden x509 sertifikalarının güvenli depolanmasını ve yönetimini uygulamasına olanak tanır.  
-   Bir sertifika sahibinin, bir sertifikanın yaşam döngüsünü yönetmesi için Key Vault yönlendiren bir ilke oluşturmasına izin verir.  
-   Sertifika sahiplerinin, sertifika süresinin dolma ve yenilemenin yaşam döngüsü olayları hakkında bildirim için iletişim bilgilerini sağlamasına izin verir.  
-   Seçili verenler ile otomatik yenilemeyi destekler-Key Vault iş ortağı x509 sertifika sağlayıcıları/sertifika yetkilileri.

>[!Note]
>İş ortağı olmayan sağlayıcılara/yetkililere da izin verilir, ancak otomatik yenileme özelliğini desteklemez.

### <a name="composition-of-a-certificate"></a>Sertifika oluşturma

Bir Key Vault sertifikası oluşturulduğunda, aynı ada sahip bir adreslenebilir anahtar ve gizli dizi da oluşturulur. Key Vault anahtarı, anahtar işlemlerine izin verir ve Key Vault gizli dizi, sertifika değerinin gizli olarak alınmasına izin verir. Bir Key Vault sertifikası ortak x509 sertifika meta verilerini de içerir.  

Sertifikaların tanımlayıcısı ve sürümü anahtarlar ve gizli dizilerle benzerdir. Adreslenebilir bir anahtarın ve bir parolanın Key Vault sertifikası sürümüyle oluşturulmuş bir gizli sürümü Key Vault sertifika yanıtında bulunabilir.
 
![Sertifikalar karmaşık nesnelerdir](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Dışarı aktarılabilir veya dışarı aktarılabilir olmayan anahtar

Bir Key Vault sertifikası oluşturulduğunda, bu, özel anahtarla birlikte adreslenebilir gizli kümesinden PFX veya ped biçiminde alınabilir. Sertifikayı oluşturmak için kullanılan ilke, anahtarın dışarı aktarılabilir olduğunu göstermelidir. İlke dışa aktarılabilir olduğunu gösteriyorsa, özel anahtar, gizli dizi olarak alındığında değerin bir parçası değildir.  

Adreslenebilir anahtar, dışarı aktarılabilir KV sertifikalarıyla daha da alakalı olur. Adreslenebilir kV anahtarının işlemleri, KV sertifikasını oluşturmak için kullanılan kV sertifika ilkesinin *KeyUsage uzantısında* alanından eşleştirilir.  

İki tür anahtar, sertifikalarla birlikte desteklenir – *RSA* veya *RSA HSM* . Dışarı aktarılabilir yalnızca RSA ile kullanılabilir, RSA HSM tarafından desteklenmez.  

### <a name="certificate-attributes-and-tags"></a>Sertifika öznitelikleri ve Etiketler

Bir Key Vault sertifikası, sertifika meta verilerine, adreslenebilir bir anahtara ve adreslenebilir gizliliğe ek olarak öznitelikler ve Etiketler de içerir.  

#### <a name="attributes"></a>Öznitelikler

Sertifika öznitelikleri, bir adreslenebilir anahtar ve KV sertifikası oluşturulduğunda oluşturulan gizli anahtar özniteliklerine yansıtılır.  

Bir Key Vault sertifikası aşağıdaki özniteliklere sahiptir:  

-   *etkin*: Boolean, isteğe bağlı, varsayılan değer **true**'dur. , Sertifika verilerinin anahtar olarak gizli veya çalıştırılabilir olarak alınıp alınamayacağını göstermek için belirtilebilir. Ayrıca *, NBF ve* *Exp*arasında bir işlem gerçekleştiğinde, *NBF* ve *Exp* ile birlikte de kullanılır ve yalnızca etkin değeri true olarak ayarlandıysa izin verilir. *NBF* ve *Exp* penceresinin dışındaki işlemlere otomatik olarak izin verilmez.  

Yanıtta bulunan ek salt okuma öznitelikleri vardır:

-   *oluşturma*: intdate: sertifikanın bu sürümünün ne zaman oluşturulduğunu gösterir.  
-   *güncelleştirildi*: intdate: sertifikanın bu sürümünün ne zaman güncelleştirildiğini gösterir.  
-   *Exp*: intdate: x509 sertifikasının süre sonu tarihinin değerini içerir.  
-   *NBF*: intdate: x509 sertifikası tarihinin değerini içerir.  

> [!Note] 
> Bir Key Vault sertifikasının süresi dolarsa, adreslenebilir anahtar ve gizli dizi çalışamaz duruma gelir.  

#### <a name="tags"></a>Etiketler

 İstemci, anahtarlar ve gizli dizileri içindeki etiketlere benzer şekilde anahtar değer çiftlerinin belirtilen sözlüğünü belirtti.  

 > [!Note]
> Bu nesne türü (anahtarlar, gizlilikler veya sertifikalar) için *liste* veya *Al* izni varsa, Etiketler bir çağıran tarafından okunabilir.

### <a name="certificate-policy"></a>Sertifika ilkesi

Sertifika ilkesi, bir Key Vault sertifikasının yaşam döngüsünü oluşturma ve yönetme hakkında bilgiler içerir. Özel anahtara sahip bir sertifika anahtar kasasına aktarıldığında, x509 sertifikası okunarak varsayılan bir ilke oluşturulur.  

Sıfırdan bir Key Vault sertifikası oluşturulduğunda, bir ilkenin sağlanması gerekir. İlke, bu Key Vault sertifikası sürümünün veya sonraki Key Vault sertifika sürümünün nasıl oluşturulacağını belirtir. Bir ilke kurulduktan sonra, gelecekteki sürümler için art arda oluşturma işlemlerinde bu gerekli değildir. Bir Key Vault sertifikasının tüm sürümleri için bir ilkenin yalnızca bir örneği bulunur.  

Yüksek düzeyde, bir sertifika ilkesi aşağıdaki bilgileri içerir:  

-   X509 sertifika özellikleri: bir x509 sertifika isteği oluşturmak için kullanılan konu adını, konu diğer adlarını ve diğer özellikleri Içerir.  
-   Anahtar özellikleri: anahtar türü, anahtar uzunluğu, dışarı aktarılabilir ve anahtar alanlarını yeniden kullanır. Bu alanlar anahtar kasasının nasıl oluşturulacağını gösteren anahtar kasasına yönlendirir.  
-   Gizli özellikler: sertifikayı gizli olarak almak için, gizli değeri oluşturmak üzere adreslenebilir gizli dizi içerik türü gibi gizli özellikler içerir.  
-   Ömür Işlemleri: KV sertifikası için ömür eylemleri içerir. Her ömür eylemi şunları içerir:  

     - Tetikleyici: süre sonu veya yaşam süresi yüzdesi olarak belirtilen günler üzerinden belirtildi  

     - Eylem: eylem türü belirtme – *Emailcontacts* veya *autoRenew*  

-   Veren: x509 sertifikaları vermek için kullanılacak sertifika verenle ilgili parametreler.  
-   İlke öznitelikleri: ilkeyle ilişkili öznitelikleri içerir  

#### <a name="x509-to-key-vault-usage-mapping"></a>Key Vault kullanım eşlemesine x509

Aşağıdaki tablo, x509 anahtar kullanımı ilkesinin Key Vault sertifikası oluşturma kapsamında oluşturulan bir anahtarın etkin anahtar işlemlerine eşlenmesini temsil eder.

|**X509 anahtar kullanım bayrakları**|**Key Vault anahtar Ops**|**Varsayılan davranış**|
|----------|--------|--------|
|Veri şifreleme|şifreleme, şifre çözme| Yok |
|Yalnızca çözülemez|çözülemiyor| Yok  |
|DigitalSignature|imzala, Doğrula| Sertifika oluşturma sırasında kullanım belirtimi olmadan varsayılan Key Vault | 
|Yalnızca şifreleme|şifrele| Yok |
|KeyCertSign|imzala, Doğrula|Yok|
|KeyEncipherment|wrapKey, unwrapKey| Sertifika oluşturma sırasında kullanım belirtimi olmadan varsayılan Key Vault | 
|Kabullenme|imzala, Doğrula| Yok |
|crlsign|imzala, Doğrula| Yok |

### <a name="certificate-issuer"></a>Sertifikayı veren

Key Vault bir sertifika nesnesi, x509 sertifikalarını sıralamak için seçilen bir sertifika veren sağlayıcısıyla iletişim kurmak için kullanılan bir yapılandırma barındırır.  

-   SSL sertifikaları için aşağıdaki sertifika veren sağlayıcılarıyla iş ortakları Key Vault

|**Sağlayıcı adı**|**Konumlar**|
|----------|--------|
|DigiCert|Genel bulut ve Azure Kamu 'daki tüm anahtar kasası hizmeti konumlarında desteklenir|
|GlobalSign|Genel bulut ve Azure Kamu 'daki tüm anahtar kasası hizmeti konumlarında desteklenir|

Bir sertifika verenin bir Key Vault oluşturulabilmesi için, önkoşul 1 ve 2 ' nin aşağıdaki adımlardan başarıyla gerçekleştirilmesi gerekir.  

1. Sertifika yetkilisi (CA) sağlayıcılarına ekleme  

    -   Kuruluş yöneticisinin şirketlerinin şirket içinde olması gerekir (örn. Contoso) en az bir CA sağlayıcısıyla.  

2. Yönetici, SSL sertifikalarını kaydetmek (ve yenilemek) için Key Vault isteyen kimlik bilgilerini oluşturur  

    -   Anahtar kasasında sağlayıcının veren nesnesini oluşturmak için kullanılacak yapılandırmayı sağlar  

Sertifika portalından veren nesneleri oluşturma hakkında daha fazla bilgi için [Key Vault sertifikaları bloguna](https://aka.ms/kvcertsblog) bakın  

Key Vault, farklı veren sağlayıcı yapılandırmasına sahip birden çok veren nesne oluşturulmasına izin verir. Bir veren nesnesi oluşturulduktan sonra, adının bir veya birden çok sertifika ilkesiyle başvurusu yapılabilir. Veren nesnesine başvurmak, sertifika oluşturma ve yenileme sırasında CA sağlayıcısından x509 sertifikası istenirken sertifikayı veren nesnesinde belirtilen yapılandırmayı kullanmak Key Vault söyler.  

Verenin nesneleri kasada oluşturulur ve yalnızca aynı kasadaki KV sertifikalarıyla birlikte kullanılabilir.  

### <a name="certificate-contacts"></a>Sertifika kişileri

Sertifika kişileri, sertifika ömrü olayları tarafından tetiklenen bildirimleri göndermek için iletişim bilgilerini içerir. Kişi bilgileri, anahtar kasasındaki tüm sertifikalar tarafından paylaşılır. Anahtar kasasındaki tüm sertifikalar için bir olay için belirtilen tüm kişilere bir bildirim gönderilir.  

Bir sertifikanın ilkesi otomatik olarak yenilemeye ayarlandıysa, aşağıdaki olaylara bir bildirim gönderilir.  

- Sertifika yenilemeden önce
- Sertifika yenileme sonrasında, sertifikanın başarıyla yenilendiğini veya bir hata olup olmadığını ve sertifikanın el ile yenilenmesini gerektirme.  

  El ile yenilenmek üzere ayarlanmış bir sertifika ilkesi (yalnızca e-posta) olduğunda, sertifikayı yenileme zamanı olduğunda bir bildirim gönderilir.  

### <a name="certificate-access-control"></a>Sertifika Access Control

 Sertifikalar için erişim denetimi Key Vault tarafından yönetilir ve bu sertifikaları içeren Key Vault tarafından sağlanır. Sertifikalar için erişim denetimi ilkesi, aynı Key Vault anahtarlar ve gizli diziler için erişim denetimi ilkelerinden farklıdır. Kullanıcılar, senaryoya uygun bir veya daha fazla kasa oluşturarak sertifikaların uygun segmentlerini ve yönetimini korumanıza olanak sağlayabilir.  

 Aşağıdaki izinler, bir anahtar kasasındaki gizli dizi erişim denetimi girişinde, her bir sorumlu temelinde kullanılabilir ve gizli bir nesne üzerinde izin verilen işlemleri yakından yansıtır:  

- Sertifika yönetimi işlemlerine yönelik izinler
  - *Get*: geçerli sertifika sürümünü veya bir sertifika sürümünü alın 
  - *liste*: geçerli sertifikaları veya bir sertifikanın sürümlerini listeleyin  
  - *güncelleştirme*: bir sertifikayı güncelleştirme
  - *oluşturma*: Key Vault sertifikası oluşturma
  - *içeri aktarma*: sertifika malzemesini bir Key Vault sertifikasına aktarma
  - *Sil*: bir sertifikayı, ilkesini ve tüm sürümlerini silme  
  - *kurtar*: silinen bir sertifikayı kurtar
  - *yedekleme*: bir sertifikayı anahtar kasasında yedekleme
  - *geri yükleme*: yedeklenen sertifikayı bir anahtar kasasına geri yükleme
  - *managecontacts*: Key Vault sertifikası kişilerini yönetme  
  - *manageverenler*: Key Vault sertifika yetkililerini/verenler yönetme
  - *getısers*: bir sertifikanın yetkililerini/verenler al
  - *listissuers*: bir sertifikanın yetkililerini/verenler listeleyin  
  - *setısers*: Key Vault sertifikanın yetkililerini/verenler oluşturma veya güncelleştirme  
  - *deleteverenler*: Key Vault sertifikasının yetkililerini/verenler silme  
 
- Ayrıcalıklı işlemler için izinler
  - *Temizle*: silinen bir sertifikayı temizle (kalıcı olarak sil)

Daha fazla bilgi için [Key Vault REST API başvurusu Içindeki sertifika işlemlerine](/rest/api/keyvault)bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Azure depolama hesabı anahtar yönetimi

Key Vault, Azure depolama hesabı anahtarlarını yönetebilir:

- Dahili olarak, Key Vault anahtarları Azure Storage hesabıyla listeleyebilir (eşitleyebilir). 
- Anahtarları düzenli aralıklarla yeniden üretir Key Vault (döndürür).
- Anahtar değerleri, çağırana yanıt olarak hiçbir şekilde döndürülmez.
- Key Vault hem depolama hesaplarının hem de klasik depolama hesaplarının anahtarlarını yönetir.

Daha fazla bilgi için bkz. [Azure Key Vault depolama hesabı anahtarları](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Depolama hesabı erişim denetimi

Bir kullanıcı veya uygulama sorumlusu yönetilen bir depolama hesabında işlem gerçekleştirmek üzere yetkilendirirken aşağıdaki izinler kullanılabilir:  

- Yönetilen depolama hesabı ve SaS tanım işlemlerine yönelik izinler
  - *Get*: bir depolama hesabı hakkında bilgi alır 
  - *liste*: Key Vault tarafından yönetilen depolama hesaplarını listeleyin
  - *güncelleştirme*: depolama hesabını güncelleştirme
  - *Sil*: depolama hesabını silme  
  - *kurtar*: silinen bir depolama hesabını kurtar
  - *yedekleme*: bir depolama hesabını yedekleme
  - *geri yükle*: yedeklenen bir depolama hesabını bir Key Vault geri yükleme
  - *Ayarla*: depolama hesabı oluşturun veya güncelleştirin
  - *RegenerateKey*: bir depolama hesabı için belirtilen anahtar değerini yeniden oluştur
  - *getsas*: bir depolama HESABı için SAS tanımı hakkında bilgi alın
  - *listsas*: depolama hesabı IÇIN depolama SAS tanımlarını listeleyin
  - *deletesas*: bir depolama hesabından SAS tanımını silme
  - *setsas*: depolama hesabı IÇIN yeni SAS tanımı/öznitelikleri oluşturma veya güncelleştirme

- Ayrıcalıklı işlemler için izinler
  - *Temizle*: yönetilen bir depolama hesabını temizle (kalıcı olarak sil)

Daha fazla bilgi için [Key Vault REST API başvurusu Içindeki depolama hesabı işlemlerine](/rest/api/keyvault)bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Ayrıca Bkz.

- [Kimlik doğrulama, istekler ve yanıtlar](authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](/azure/key-vault/key-vault-developers-guide)
