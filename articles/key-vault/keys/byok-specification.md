---
title: Kendi anahtar belirtimini getirin-Azure Key Vault | Microsoft Docs
description: Bu belge, kendi anahtar belirtimini getir bölümünde açıklanmıştır.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581032"
---
# <a name="bring-your-own-key-specification"></a>Kendi anahtarını getir belirtimi

Bu belgede, müşterilerin Şirket içi HSM 'leri Key Vault ' e kadar olan HSM korumalı anahtarları içeri aktarmaya yönelik özellikler açıklanmaktadır.

## <a name="scenario"></a>Senaryo

Key Vault müşteri, Azure dışındaki şirket içi HSM 'den bir anahtarı HSM yedekleme Azure Key Vault içine güvenli bir şekilde aktarmak istiyor. Key Vault dışında oluşturulan bir anahtarı içeri aktarma işlemi genellikle Kendi Anahtarını Getir (BYOK) olarak adlandırılır.

Gereksinimler şunlardır:
* Aktarılacak anahtar, düz metin biçiminde bir HSM dışında hiçbir şekilde yok.
* HSM dışında, aktarılacak anahtar, her zaman Azure Key Vault HSM 'de tutulan bir anahtarla korunur

## <a name="terminology"></a>Terminoloji

|Anahtar adı|Anahtar türü|Kaynak|Description|
|---|---|---|---|
|Anahtar değişim anahtarı (KEK)|RSA|Azure Key Vault HSM|Azure Key Vault içinde oluşturulan bir HSM tarafından desteklenen bir RSA anahtar çifti
Anahtar sarmalama|AES|Satıcı HSM|-Pred üzerinde HSM tarafından oluşturulan bir [kısa ömürlü] AES anahtarı
Hedef anahtar|RSA, EC, AES (yalnızca yönetilen HSM)|Satıcı HSM|Azure Key Vault HSM 'ye aktarılacak anahtar

**Anahtar değişim anahtarı**: müşterinin bYok anahtarının içeri aktarılacağı anahtar KASASıNDA oluşturduğu HSM ile desteklenen bir anahtar. Bu KEK aşağıdaki özelliklere sahip olmalıdır:

* Bu bir RSA-HSM anahtarıdır (4096-bit veya 3072-bit veya 2048-bit)
* YALNıZCA BYOK sırasında kullanılmasına izin veren sabit key_ops (yalnızca ' içeri aktar ') olur.
* Hedef anahtarın içeri aktarılacağı kasada aynı kasada olması gerekir

## <a name="user-steps"></a>Kullanıcı adımları

Bir anahtar aktarımı gerçekleştirmek için, bir Kullanıcı aşağıdaki adımları gerçekleştirir:

1. KEK oluştur.
2. KEK ortak anahtarını alın.
3. HSM satıcısından sunulan BYOK aracını kullanma-KEK hedef HSM 'ye aktarın ve KEK tarafından korunan hedef anahtarı dışarı aktarır.
4. Korunan hedef anahtarı Azure Key Vault içeri aktarın.

Müşteriler adım 3 ' ü tamamlayarak HSM satıcısı tarafından sunulan BYOK aracını ve belgelerini kullanır. Anahtar aktarım Blobu (". bYok" dosyası) oluşturur.


## <a name="hsm-constraints"></a>HSM kısıtlamaları

Mevcut HSM, yönettikleri anahtar üzerinde kısıtlamalar uygulayabilir, şunlar da dahildir:
* HSM 'nin anahtar kaydırması tabanlı dışarı aktarmaya izin verecek şekilde yapılandırılması gerekebilir
* Denetlenen dışarı aktarmaya izin vermek için HSM 'nin CKA_EXTRACTABLE hedef anahtarın işaretlenmiş olması gerekebilir
* Bazı durumlarda, KEK ve sarmalama anahtarının CKA_TRUSTED olarak işaretlenmesi gerekebilir. Bu, HSM 'deki anahtarları kaydırmak için kullanılmasına izin verir.

Kaynak HSM yapılandırması, genellikle bu belirtim kapsamının dışında. Microsoft, HSM satıcısının her türlü yapılandırma adımını dahil etmek için BYOK aracına eşlik eden belgeler oluşturmasını bekler.

> [!NOTE]
> Aşağıda açıklanan 1, 2 ve 4. adımlar Azure PowerShell ve Azure portalı gibi diğer arabirimler kullanılarak gerçekleştirilebilir. Ayrıca, Key Vault SDK 'daki eşdeğer işlevler kullanılarak programlı bir şekilde gerçekleştirilebilir.

### <a name="step-1-generate-kek"></a>1. Adım: KEK oluşturma

Anahtar işlemleri içeri aktarmaya ayarlanmış KEK oluşturmak için **az keykasa Key Create** komutunu kullanın. Aşağıdaki komuttan döndürülen ' KID ' anahtar tanımlayıcısını aklınızda edin.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>2. Adım: KEK ortak anahtarını alma

KEK öğesinin ortak anahtar bölümünü indirin ve ped dosyasına depolayın.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>3. Adım: HSM satıcısı tarafından sunulan BYOK Aracı kullanılarak anahtar aktarım blobu oluşturma

Müşteri, anahtar aktarım Blobu (". bYok" dosyası olarak depolanır) oluşturmak için HSM satıcısı tarafından sağlanmış BYOK aracını kullanır. KEK ortak anahtarı (. pek dosyası olarak) Bu araca ait girdilerden biri olacaktır.

#### <a name="key-transfer-blob"></a>Anahtar aktarımı blobu
Uzun dönem, Microsoft, bu mekanizma tek bir blob oluşturduğundan ve daha da önemlisi, ara AES anahtarının iki HSM tarafından işlenmesi ve kısa ömürlü olması nedeniyle, hedef anahtarı Azure Key Vault aktarmak için PKCS # 11 CKM_RSA_AES_KEY_WRAP mekanizmasını kullanmak istiyor. Bu mekanizma, bazı HSM 'lerde Şu anda kullanılamaz, ancak bir AES anahtarı kullanarak hedef anahtarın CKM_AES_KEY_WRAP_PAD koruma kombinasyonu ve AES anahtarını CKM_RSA_PKCS_OAEP ile koruma, denk bir blob üretir.

Hedef anahtar düz metin, anahtar türüne bağlıdır: 
* Bir RSA anahtarı için, PKCS # 8 ' de Sarmalanan özel Key ASN. 1 DER Encoding [RFC3447] [RFC5208] 
* EC anahtarı için, PKCS # 8 ' de Sarmalanan özel Key ASN. 1 DER Encoding [RFC5915] [RFC5208]
* Sekizli anahtar için, anahtarın ham baytları

Düz metin anahtarının baytları CKM_RSA_AES_KEY_WRAP mekanizması kullanılarak dönüştürülür:
* Bir kısa ömürlü AES anahtarı, SHA1 ile RSA-OAEP kullanılarak sarmalama RSA anahtarıyla şifrelenir.
* Kodlanmış düz metin anahtarı, doldurma ile AES anahtar kaydırması kullanılarak AES anahtarı kullanılarak şifrelenir.
* Şifrelenmiş AES anahtarı ve şifrelenmiş düz metin anahtarı, son şifreli blob blobu oluşturmak için birleştirilir.

Aktarım blob 'u, doğru şifre çözme için gereken meta verileri hizmete sunmaya yönelik bir araç olarak JSON Web şifrelemesi Compact serileştirme (RFC7516) kullanır.

CKM_RSA_AES_KEY_WRAP_PAD kullanılıyorsa, aktarım blobunun JSON serileştirilmesi şöyle olacaktır:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* KID = KEK öğesinin anahtar tanımlayıcısı. Key Vault anahtarlar için şöyle görünür: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = algoritması. 
* dir = doğrudan modu, yani, CKM_RSA_AES_KEY_WRAP doğru bir gösterimi olan doğrudan şifreli biçimi korumak için başvurulan çocuk kullanılır
* Generator = BYOK aracının adını ve sürümünü ve kaynak HSM üreticisini ve modelini gösteren bir bilgilendirme alanı. Bu bilgiler, sorun giderme ve destek açısından kullanılmak üzere tasarlanmıştır.

JSON blobu, ' Add-AzKeyVaultKey ' (PSH) veya ' az keykasa anahtar içe aktarma ' (CLı) komutları kullanılırken Azure PowerShell/CLı istemcilerinin doğru şekilde davranmasını sağlamak için ". bYok" uzantısına sahip bir dosyada depolanır.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>4. Adım: HSM anahtarını içeri aktarmak için anahtar aktarım blobu yükleme

Müşteri, anahtar aktarım Blobu (". bYok" dosyası) bir çevrimiçi iş istasyonuna aktarır ve sonra bu blobu Key Vault yeni bir HSM ile desteklenen anahtar olarak içeri aktarmak için **az keykasa anahtarı içeri aktarma** komutu çalıştırır. 

Bir RSA anahtarını içeri aktarmak için şu komutu kullanın:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Bir EC anahtarını içeri aktarmak için anahtar türü ve eğri adı belirtmeniz gerekir.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


Yukarıdaki komut yürütüldüğünde, REST API isteği aşağıdaki gibi göndermeye neden olur:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Bir RSA anahtarı içeri aktarılırken istek gövdesi:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Bir EC anahtarı içeri aktarılırken gövde iste:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

"key_hsm" değeri, Base64 biçiminde KeyTransferPackage-ContosoFirstHSMkey. bYok kodlandığı tüm içeridir.

## <a name="references"></a>Başvurular
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)

## <a name="next-steps"></a>Sonraki adımlar
* Adım adım BYOK yönergeleri: [HSM korumalı anahtarları Key Vault Içeri aktarma (BYOK)](hsm-protected-keys-byok.md)

