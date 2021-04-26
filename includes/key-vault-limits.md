---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753316"
---
Azure Key Vault hizmet iki kaynak türünü destekler: kasa ve yönetilen HSM 'ler. Aşağıdaki iki bölüm, her birinin hizmet sınırlarını sırasıyla anlatmaktadır.

### <a name="resource-type-vault"></a>Kaynak türü: kasa

Bu bölüm, kaynak türü için hizmet sınırlarını açıklar `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Anahtar işlemleri (bölge başına en fazla 10 saniye içinde izin verilen işlem sayısı<sup>1</sup>):

|Anahtar türü|HSM anahtarı<br>Anahtar oluştur|HSM anahtarı<br>Diğer tüm işlemler|Yazılım anahtarı<br>Anahtar oluştur|Yazılım anahtarı<br>Diğer tüm işlemler|
|:---|---:|---:|---:|---:|
|RSA 2.048 bit|5|1.000|10|2.000|
|RSA 3.072 bit|5|250|10|500|
|RSA 4.096 bit|5|125|10|250|
|ECC P-256|5|1.000|10|2.000|
|ECC P-384|5|1.000|10|2.000|
|ECC P-521|5|1.000|10|2.000|
|ECC SECP256K1|5|1.000|10|2.000|
||||||

> [!NOTE]
> Önceki tabloda, RSA 2.048-bit yazılım anahtarları için, 2.000/10 saniyeye kadar işlem yapılmasına izin verileceğini görüyoruz. RSA 2.048 bit HSM anahtarları için, her 10 saniye başına 1.000 al işlem yapılmasına izin verilir.
>
> Daraltma eşikleri ağırlıklı olur ve zorlamasının toplam üzerinde olması gerekir. Örneğin, önceki tabloda gösterildiği gibi, RSA HSM anahtarları üzerinde al işlemleri gerçekleştirdiğinizde, 2.048-bit anahtarlara kıyasla 4.096 bitlik anahtarların kullanılması sekiz kat daha pahalıdır. Bunun nedeni 1000/125 = 8 ' dir.
>
> Belirli bir 10 saniyelik aralıkta, bir Azure Key Vault istemci, azaltma HTTP durum kodu ile karşılaşmadan önce aşağıdaki işlemlerden *yalnızca birini* gerçekleştirebilir `429` :
> - 2.000 RSA 2.048-bit yazılım-anahtar al işlemler
> - 1.000 RSA 2.048-bit HSM-anahtar al işlemleri
> - 125 RSA 4.096-bit HSM-anahtar al işlemleri
> - 124 RSA 4.096-bit HSM-anahtar al işlem ve 8 RSA 2.048-bit HSM-anahtar al işlem

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Gizli dizileri, yönetilen depolama hesabı anahtarları ve kasa işlemleri:

| İşlem türü | Bölge başına en fazla 10 saniye içinde izin verilen işlem sayısı<sup>1</sup> |
| --- | --- |
| Tüm işlemler |2.000 |

Bu sınırlar aşıldığında azaltmayı işleme hakkında daha fazla bilgi için bkz. [Azure Key Vault azaltma Kılavuzu](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> tüm işlem türleri için abonelik genelinde sınır, Anahtar Kasası sınırı başına beş kat olur. Örneğin, HSM-abonelik başına diğer işlemler, abonelik başına 10 saniye içinde 5.000 işlem ile sınırlıdır.

#### <a name="backup-keys-secrets-certificates"></a>Yedekleme anahtarları, gizlilikler, sertifikalar

Gizli dizi, anahtar veya sertifika gibi bir Anahtar Kasası nesnesini yedeklerken, yedekleme işlemi nesneyi şifreli bir blob olarak indirir. Bu Blobun Azure dışından şifresi çözülemiyor. Bu bloba kullanılabilir verileri almak için, blobu aynı Azure aboneliği ve Azure coğrafya içindeki bir anahtar kasasında geri yüklemeniz gerekir

| İşlem türü | İzin verilen maksimum Anahtar Kasası nesne sürümleri |
| --- | --- |
| Tek tek anahtar, gizli dizi, certfiicate |500 |

> [!NOTE]
> Yukarıdaki sınırdan daha fazla sürümü olan bir anahtar, gizli dizi ya da sertifika nesnesi yedeklemeye çalışılması bir hataya neden olur. Bir anahtar, gizli dizi ya da sertifikanın önceki sürümlerini silmek mümkün değildir. 

#### <a name="azure-private-link-integration"></a>Azure özel bağlantı tümleştirmesi

> [!NOTE]
> Abonelik başına etkin olan özel uç noktaları olan anahtar kasalarının sayısı, ayarlanabilir bir kısıtlamadır. Aşağıda gösterilen sınır varsayılan sınırdır. Hizmetiniz için bir sınır artışı istemek istiyorsanız lütfen bir destek isteği oluşturun ve bu durum, büyük/küçük harf temelinde değerlendirilir.

| Kaynak | Sınır |
| -------- | -----:|
| Anahtar Kasası başına özel uç noktalar | 64 |
| Abonelik başına özel uç noktalara sahip anahtar kasaları | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Kaynak türü: yönetilen HSM (Önizleme)

Bu bölüm, kaynak türü için hizmet sınırlarını açıklar `managed HSM` .

#### <a name="object-limits"></a>Nesne sınırları

|Öğe|Sınırlar|
|----|------:|
Her bölge için abonelik başına HSM örneği sayısı|1 (Önizleme sırasında)
HSM havuzu başına anahtar sayısı|5000
Anahtar başına sürüm sayısı|100
HSM başına özel rol tanımı sayısı|50
HSM kapsamındaki rol atamalarının sayısı|50
Her bir anahtar kapsamındaki rol atama sayısı|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Yönetim işlemleri için işlem sınırları (HSM örneği başına saniye başına işlem sayısı)
|İşlem |Saniye başına işlem sayısı|
|----|------:|
Tüm RBAC işlemleri<br/>(rol tanımları ve rol atamaları için tüm CRUD işlemlerini içerir)|5
Tam HSM yedekleme/geri yükleme<br/>(HSM örneği başına yalnızca bir eşzamanlı yedekleme veya geri yükleme işlemi desteklenir)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Şifreleme işlemleri için işlem sınırları (HSM örneği başına saniye başına işlem sayısı)

- Her yönetilen HSM örneği 3 yük dengeli HSM bölümü oluşturur. Üretilen iş sınırları, her bölüm için ayrılan temel donanım kapasitesinin bir işlevidir. Aşağıdaki tablolarda en az bir bölüm kullanılabilir olan en fazla aktarım hızı gösterilmektedir. 3 bölüm varsa, gerçek verimlilik 3x daha yüksek olabilir.
- Belirtilen üretilen iş sınırları, en yüksek aktarım hızını elde etmek için tek bir anahtarın kullanıldığını varsayın. Örneğin, tek bir RSA-2048 anahtarı kullanılırsa, en yüksek verimlilik 1100 imza işlemi olacaktır. Saniyede 1 işlem ile 1100 farklı anahtar kullanırsanız, aynı işleme elde edemeyecektir.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA anahtar işlemleri (HSM örneği başına saniye başına işlem sayısı)

|İşlem|2048 bit|3072 bit|4096 bit|
|--|--:|--:|--:|
Anahtar oluştur|1| 1| 1
Anahtarı sil (geçici silme)|10|10|10 
Anahtarı temizle|10|10|10 
Yedekleme anahtarı|10|10|10 
Anahtarı geri yükle|10|10|10 
Anahtar bilgilerini al|1100|1100|1100
Şifreleme|10000|10000|6000
Şifre Çözme|1100|360|160
Ilacağını|10000|10000|6000
Unwrap|1100|360|160
İşaret|1100|360|160
Doğrulama|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC anahtar işlemleri (HSM örneği başına saniye başına işlem sayısı)

Bu tabloda her bir eğri türü için saniye başına işlem sayısı açıklanmaktadır.

|İşlem|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Anahtar oluştur| 1| 1| 1| 1
Anahtarı sil (geçici silme)|10|10|10|10
Anahtarı temizle|10|10|10|10
Yedekleme anahtarı|10|10|10|10
Anahtarı geri yükle|10|10|10|10
Anahtar bilgilerini al|1100|1100|1100|1100
İşaret|260|260|165|56
Doğrulama|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES anahtar işlemleri (HSM örneği başına saniye başına işlem sayısı)
- Şifreleme ve şifre çözme işlemleri 4KB 'lik bir paket boyutunu varsayar.
- Şifreleme/şifre çözme için üretilen iş sınırları AES-CBC ve AES-GCM algoritmaları için geçerlidir.
- Sarmalama/sarmalama için üretilen iş sınırları AES-KW algoritması için geçerlidir.

|İşlem|128 bit|192 bit|256 bit|
|--|--:|--:|--:|
Anahtar oluştur|1| 1| 1
Anahtarı sil (geçici silme)|10|10|10
Anahtarı temizle|10|10|10
Yedekleme anahtarı|10|10|10
Anahtarı geri yükle|10|10|10
Anahtar bilgilerini al|1100|1100|1100
Şifreleme|8000|8000 |8000 
Şifre Çözme|8000|8000|8000
Ilacağını|9000|9000|9000
Unwrap|9000|9000|9000

