---
title: Anahtarlar hakkında-Azure Key Vault
description: Anahtarlar için Azure Key Vault REST arabirimine ve geliştirici ayrıntılarına genel bakış.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: b9565095a40052a940d7a7b31f0fd3a27e0e75c2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815009"
---
# <a name="about-keys"></a>Anahtarlar hakkında

Azure Key Vault, şifreleme anahtarlarını depolamak ve yönetmek için iki tür kaynak sağlar. Kasaların yazılım korumalı ve HSM korumalı (donanım güvenlik modülü) anahtarlarını destekler. Yönetilen HSMs yalnızca HSM korumalı anahtarları destekler. 

|Kaynak türü|Anahtar koruma yöntemleri|Veri düzlemi uç noktası temel URL 'SI|
|--|--|--|
| **Kasalar** | Yazılım korumalı<br/><br/>ve<br/><br/>HSM korumalı (Premium SKU ile)</li></ul> | https://{kasa-adı}. kasa. Azure. net |
| **Yönetilen HSM 'ler** | HSM korumalı | https://{HSM-adı}. managedhsm. Azure. net |
||||

-  Kasaların kasalarına göre düşük maliyetli, kolay bir dağıtım, çok kiracılı, bölge açısından dayanıklı (kullanılabilir yerlerde), en yaygın bulut uygulaması senaryoları için uygun olan yüksek düzeyde kullanılabilir anahtar yönetimi çözümü sağlar.
- **Yönetilen hsms** tarafından yönetilen HSM, şifreleme anahtarlarınızı depolamak ve yönetmek için tek kiracılı, bölge dayanıklı (kullanılabilir), yüksek oranda kullanılabilir HSM 'ler sağlar. Yüksek değerli anahtarları işleyen uygulamalar ve kullanım senaryoları için en uygun. Ayrıca, en sıkı güvenlik, uyumluluk ve mevzuat gereksinimlerini karşılamanıza yardımcı olur. 

> [!NOTE]
> Kasalar, şifreleme anahtarlarının yanı sıra gizli dizileri, sertifikaları ve depolama hesabı anahtarlarını gibi çeşitli nesne türlerini depolamanıza ve yönetmenize de olanak tanır.

Key Vault içindeki şifreleme anahtarları JSON Web anahtarı [JWK] nesneleri olarak gösterilir. JavaScript Nesne Gösterimi (JSON) ve JavaScript nesne Imzalama ve şifreleme (Joo) belirtimleri şunlardır:

-   [JSON Web anahtarı (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web şifrelemesi (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web algoritmaları (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Imzası (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Temel JWK/JWA belirtimleri, Azure Key Vault ve yönetilen HSM uygulamalarına özgü anahtar türlerini etkinleştirmek için de genişletilir. 

HSM korumalı anahtarlar (HSM anahtarları olarak da adlandırılır) bir HSM 'de (donanım güvenlik modülü) işlenir ve her zaman HSM koruma sınırı kalır. 

- Kasaların paylaşılan HSM arka uç altyapısında HSM anahtarlarını korumak için **fıps 140-2 düzey 2** doğrulanan HSM 'leri kullanın. 
- Yönetilen HSM, anahtarlarınızı korumak için **fıps 140-2 düzey 3** doğrulanan HSM modülleri kullanır. Her HSM havuzu, kendi [güvenlik etki alanı](../managed-hsm/security-domain.md) ile yalıtılmış bir tek kiracılı örneğidir ve aynı donanım altyapısını paylaşan diğer tüm hsms 'lerden tam şifreleme yalıtımı sağlar.

Bu anahtarlar tek kiracılı HSM havuzları halinde korunur. Bir RSA, EC ve simetrik anahtarı, yumuşak biçimde veya desteklenen bir HSM cihazından dışarı aktararak içeri aktarabilirsiniz. Ayrıca HSM havuzlarında anahtarlar da oluşturabilirsiniz. HSM anahtarlarını [bYok (kendi anahtarını getir) belirtiminde](../keys/byok-specification.md)açıklanan yöntemi kullanarak içeri aktardığınızda, yönetilen HSM havuzlarına güvenli ulaşım anahtar malzemesini sunar. 

Coğrafi sınırlar hakkında daha fazla bilgi için bkz. [Microsoft Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Anahtar türleri ve koruma yöntemleri

Key Vault RSA ve EC anahtarlarını destekler. Yönetilen HSM, RSA, EC ve simetrik anahtarları destekler. 

### <a name="hsm-protected-keys"></a>HSM ile korunan anahtarlar

|Anahtar türü|Kasa (yalnızca Premium SKU)|Yönetilen HSM 'ler|
|--|--|--|
|**EC-HSM**: eliptik eğri tuşu | Desteklenir | Desteklenir|
|**RSA-HSM**: RSA anahtarı|Desteklenir|Desteklenir|
|**EKI HSM**: simetrik anahtar|Desteklenmez|Desteklenir|
|||

### <a name="software-protected-keys"></a>Yazılım korumalı anahtarlar

|Anahtar türü|Kasalar|Yönetilen HSM 'ler|
|--|--|--|
**RSA**: "yazılım KORUMALı" RSA anahtarı|Desteklenir|Desteklenmez
**EC**: "Yazılım korumalı" eliptik eğri anahtarı|Desteklenir|Desteklenmez
|||

### <a name="compliance"></a>Uyumluluk

|Anahtar türü ve hedefi|Uyumluluk|
|---|---|
|Kasalardaki yazılım korumalı anahtarlar (Premium & standart SKU 'Lar) | FIPS 140-2 düzey 1|
|Kasalardaki HSM korumalı anahtarlar (Premium SKU)| FIPS 140-2 Level 2|
|Yönetilen HSM 'de HSM korumalı anahtarlar|FIPS 140-2 düzey 3|
|||



Her anahtar türü, algoritmalar, işlemler, öznitelikler ve Etiketler hakkındaki ayrıntılar için bkz. [anahtar türleri, algoritmalar ve işlemler](about-keys-details.md) .

## <a name="next-steps"></a>Sonraki adımlar
- [Key Vault Hakkında](../general/overview.md)
- [Yönetilen HSM hakkında](../managed-hsm/overview.md)
- [Gizli diziler hakkında](../secrets/about-secrets.md)
- [Sertifikalar hakkında](../certificates/about-certificates.md)
- [Key Vault REST API genel bakış](../general/about-keys-secrets-certificates.md)
- [Kimlik doğrulama, istekler ve yanıtlar](../general/authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)
