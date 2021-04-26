---
title: Azure Key Vault anahtarlar, gizlilikler ve sertifikalara genel bakış
description: Anahtarlar, gizli diziler ve sertifikalar için Azure Key Vault REST arabirimine ve geliştirici ayrıntılarına genel bakış.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: e3eb77d15c288c93298da6dd79a76565e5d67f96
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749951"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault anahtarlar, gizlilikler ve sertifikalara genel bakış

Azure Key Vault, Microsoft Azure uygulamaların ve kullanıcıların çeşitli türlerdeki gizli/anahtar verileri depolamasına ve kullanmasına olanak sağlar. Key Vault kaynak sağlayıcısı iki kaynak türünü destekler: kasa ve yönetilen HSM 'ler.

## <a name="dns-suffixes-for-base-url"></a>Taban URL 'SI için DNS sonekleri
 Aşağıdaki tabloda, çeşitli bulut ortamlarında kasa ve yönetilen HSM havuzları için veri düzlemi uç noktası tarafından kullanılan temel URL DNS son eki gösterilmektedir.

Bulut ortamı | Kasa için DNS son eki | Yönetilen HSM 'ler için DNS son eki
---|---|---
Azure bulutu | . vault.azure.net | . managedhsm.azure.net
Azure Çin bulutu | . vault.azure.cn | Desteklenmez
Azure ABD Kamu | . vault.usgovcloudapi.net | Desteklenmez
Azure Almanya bulutu | . vault.microsoftazure.de | Desteklenmez
|||


## <a name="object-types"></a>Nesne türleri
 Aşağıdaki tabloda, taban URL 'sindeki nesne türleri ve bunların son ekleri gösterilmektedir.

Nesne türü|URL son eki|Kasalar|Yönetilen HSM havuzları
--|--|--|--
**Şifreleme anahtarları**||
HSM ile korunan anahtarlar|/Keys|Desteklenir|Desteklenir
Yazılım korumalı anahtarlar|/Keys|Desteklenir|Desteklenmez
**Diğer nesne türleri**||
Gizli Diziler|/gizlilikler|Desteklenir|Desteklenmez
Sertifikalar|/Certificates|Desteklenir|Desteklenmez
Depolama hesabı anahtarları|/Storage|Desteklenir|Desteklenmez
|||
- **Şifreleme anahtarları**: birden çok anahtar türünü ve algoritmaları destekler ve yazılım KORUMALı ve HSM korumalı anahtarların kullanımını sunar. Daha fazla bilgi için bkz. [anahtarlar hakkında](../keys/about-keys.md).
- **Gizlilikler**: parolalar ve veritabanı bağlantı dizeleri gibi güvenli parolaların depolanmasını sağlar. Daha fazla bilgi için bkz. [gizlilikler hakkında](../secrets/about-secrets.md).
- **Sertifikalar**: anahtarların ve parolaların üzerine inşa edilen sertifikaları destekler ve otomatik yenileme özelliği ekler. Daha fazla bilgi için bkz. [Sertifikalar hakkında](../certificates/about-certificates.md).
- **Azure depolama hesabı anahtarları**: sizin Için bir Azure depolama hesabı anahtarlarını yönetebilir. Dahili olarak, Key Vault anahtarları Azure Storage hesabıyla listeleyebilir (eşitleyebilir) ve anahtarları düzenli olarak yeniden oluşturabilir (döndürün). Daha fazla bilgi için bkz. [Key Vault depolama hesabı anahtarlarını yönetme](../secrets/overview-storage-keys.md).

Key Vault hakkında daha fazla genel bilgi için bkz. [Azure Key Vault hakkında](overview.md). Yönetilen HSM havuzları hakkında daha fazla bilgi için bkz. [Azure Key Vault Managed HSM nedir?](../managed-hsm/overview.md)


## <a name="data-types"></a>Veri türleri

Anahtarlar, şifreleme ve imzalama için ilgili veri türleri için JOTE belirtimlerine bakın.  

-   **algoritma** -anahtar işlemi için desteklenen bir algoritma; örneğin, RSA1_5  
-   **şifreli-değer** -şifre metin Sekizlilileri, Base64URL kullanarak kodlanmış  
-   **Özet-değer** -Base64URL kullanılarak kodlanan karma algoritmanın çıkışı  
-   **anahtar türü** -desteklenen anahtar türlerinden biri, örneğin RSA (Rivest-Shamir-Adliman).  
-   **düz metin-değer** -düz metin sekizlik, Base64URL kullanılarak kodlanır  
-   **imza-değer** -Base64URL kullanılarak kodlanan imza algoritmasının çıkışı  
-   **base64URL** -a BASE64URL [RFC4648] kodlamalı ikili değer  
-   **Boolean** -true veya false  
-   **Kimlik** -Azure Active Directory bir KIMLIK (AAD).  
-   **Intdate** -belirtilen UTC tarih/saatine kadar 1970-01-01T0:0: 0z UTC 'den saniye sayısını temsil eden bir JSON ondalık değeri. Genel ve UTC 'de tarih/saat ile ilgili ayrıntılar için bkz. RFC3339.  

## <a name="objects-identifiers-and-versioning"></a>Nesneler, tanımlayıcılar ve sürüm oluşturma

Key Vault depolanan nesneler, bir nesnenin yeni bir örneği oluşturulduğunda sürümlüdür. Her sürüme benzersiz bir tanımlayıcı ve URL atanır. Bir nesne ilk oluşturulduğunda, benzersiz bir sürüm tanımlayıcısı verilir ve nesnenin geçerli sürümü olarak işaretlenir. Aynı nesne adına sahip yeni bir örnek oluşturmak, yeni nesneye benzersiz bir sürüm tanımlayıcısı sağlar ve bunun geçerli sürüm olmasına neden olur.  

Key Vault nesneler, bir sürüm belirtilerek veya nesnenin geçerli sürümündeki işlemler için sürüm dışarıda bırakarak çözülebilir. Örneğin, adı olan bir anahtar verildiğinde `MasterKey` , bir sürüm belirtmeden işlemleri gerçekleştirmek sistemin kullanılabilir en son sürümü kullanmasına neden olur. Sürüme özgü tanımlayıcıyla işlem gerçekleştirmek sistemin nesnenin o belirli sürümünü kullanmasına neden olur.  

### <a name="vault-name-and-object-name"></a>Kasa adı ve nesne adı
Nesneler bir URL kullanarak Key Vault içinde benzersiz şekilde tanımlanır. Coğrafi konumdan bağımsız olarak sistemdeki iki nesne aynı URL 'ye sahip değildir. Bir nesnenin tüm URL 'SI, nesne tanımlayıcısı olarak adlandırılır. URL, Key Vault, nesne türü, Kullanıcı tarafından sağlanmış nesne adı ve bir nesne sürümü tanımlayan bir önekden oluşur. Nesne adı büyük/küçük harfe duyarlıdır ve sabittir. Nesne sürümünü içermeyen tanımlayıcılar temel tanımlayıcılar olarak adlandırılır.  

Daha fazla bilgi için bkz. [kimlik doğrulaması, istekler ve yanıtlar](authentication-requests-and-responses.md)

Bir nesne tanımlayıcısının aşağıdaki genel biçimi vardır (kapsayıcı türüne göre):  

- **Kasa için**: `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **YÖNETILEN HSM havuzları için**: `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Her kapsayıcı türü tarafından desteklenen nesne türleri için bkz. [nesne türü desteği](#object-types) .

Konum:  

| Öğe | Açıklama |  
|-|-|  
|`vault-name` veya `hsm-name`|Microsoft Azure Key Vault hizmetindeki bir kasanın veya yönetilen bir HSM havuzunun adı.<br /><br />Kasa adları ve yönetilen HSM havuzu adları Kullanıcı tarafından seçilir ve genel olarak benzersizdir.<br /><br />Kasa adı ve yönetilen HSM havuzu adı, yalnızca 0-9, a-z, A-Z ve-içeren bir 3-24 karakter dizesi olmalıdır.|  
|`object-type`|Nesnenin türü, "anahtarlar", "gizlilikler" veya ' Certificates '.|  
|`object-name`|`object-name`, İçin Kullanıcı tarafından sağlanmış bir addır ve bir Key Vault içinde benzersiz olmalıdır. Ad, bir harfle başlayan ve yalnızca 0-9, a-z, A-Z ve-içeren bir 1-127 karakter dizesi olmalıdır.|  
|`object-version`|, `object-version` İsteğe bağlı olarak bir nesnenin benzersiz bir sürümünü ele almak için kullanılan, sistem tarafından oluşturulan 32 karakter dizesi tanımlayıcısıdır.|  

## <a name="next-steps"></a>Sonraki adımlar

- [Anahtarlar hakkında](../keys/about-keys.md)
- [Gizli diziler hakkında](../secrets/about-secrets.md)
- [Sertifikalar hakkında](../certificates/about-certificates.md)
- [Kimlik doğrulama, istekler ve yanıtlar](../general/authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)
