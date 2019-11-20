---
title: Sertifika oluşturmayı izleme ve yönetme
description: Key Vault ile sertifika oluşturma işlemini oluşturmaya, izlemeye ve bunlarla etkileşime yönelik bir dizi seçeneği gösteren senaryolar.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 9f88af7027f6c907b5b55eb9aac545d98e2fbb7a
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880841"
---
# <a name="monitor-and-manage-certificate-creation"></a>Sertifika oluşturmayı izleme ve yönetme
Uygulama hedefi: Azure

Aşağıdaki 

Bu makalede özetlenen senaryolar/işlemler şunlardır:

- Desteklenen bir veren ile KV sertifikası isteme
- Bekleyen isteği Al-istek durumu "sürüyor"
- Bekleyen isteği Al-istek durumu "tam"
- Bekleyen isteği al-bekleyen istek durumu "iptal edildi" veya "başarısız"
- Bekleyen isteği al-bekleyen istek durumu "silindi" veya "üzerine yazıldı"
- Bekleyen istek var olduğunda oluştur (veya Içeri aktar)-durum "sürüyor" dır
- İstek veren (DigiCert) ile bekleyen istek oluşturulduğunda Birleştir
- Bekleyen istek durumu "sürüyor" iken bir iptal iste
- Bekleyen bir istek nesnesini Sil
- Bir KV sertifikasını el ile oluşturma
- Bekleyen bir istek oluşturulduğunda Birleştir-el ile sertifika oluşturma

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Desteklenen bir veren ile KV sertifikası isteme 

|Yöntem|İstek URI'si|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Aşağıdaki örneklerde, "mydigicert" adlı bir nesne, zaten bir örnek kasasında, bir sertifika veren sağlayıcısı ile DigiCert olarak kullanılabilir. Sertifika veren, bir Certificateıssuer kaynağı olarak Azure Key Vault (KV) ile temsil edilen bir varlıktır. Bir KV sertifikasının kaynağı hakkında bilgi sağlamak için kullanılır; verenin adı, sağlayıcı, kimlik bilgileri ve diğer yönetim ayrıntıları.

### <a name="request"></a>İstek

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Yanıt

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Bekleyen isteği Al-istek durumu "sürüyor"

|Yöntem|İstek URI'si|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>İstek
AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Sorguda *request_id* belirtilmişse, bir filtre gibi davranır. Sorgudaki *request_id* ve bekleyen nesne farklıysa, 404 HTTP durum kodu döndürülür.

### <a name="response"></a>Yanıt

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Bekleyen isteği Al-istek durumu "tam"

### <a name="request"></a>İstek

|Yöntem|İstek URI'si|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Yanıt

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Bekleyen isteği al-bekleyen istek durumu "iptal edildi" veya "başarısız"

### <a name="request"></a>İstek

|Yöntem|İstek URI'si|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Yanıt

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Hata *kodu* değeri, "sertifika veren hatası" veya "istek reddedildi" veya sırasıyla Kullanıcı hatası temelinde olabilir.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Bekleyen isteği al-bekleyen istek durumu "silindi" veya "üzerine yazıldı"
Bekleyen bir nesne, durumu "sürüyor" olmadığında bir oluşturma/içeri aktarma işlemi tarafından silinebilir veya üzerine yazılabilir.

|Yöntem|İstek URI'si|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>İstek
AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

AL`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Yanıt

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Bekleyen istek var olduğunda oluştur (veya Içeri aktar)-durum "sürüyor" dır
Bekleyen bir nesne dört olası duruma sahiptir; "sürüyor", "iptal edildi", "başarısız" veya "tamamlandı".

Bekleyen bir isteğin durumu "sürüyor" olduğunda, oluşturma (ve içeri aktarma) işlemleri http durum kodu 409 (çakışma) ile başarısız olur.

Bir çakışmayı onarmak için:

- Sertifika el ile oluşturulduysa, bekleyen nesnede bir birleştirme veya silme yaparak KV sertifikasını tamamlayabilirsiniz.

- Sertifika bir veren ile oluşturulduysa, sertifika tamamlanana kadar bekleyebilir, başarısız olur veya iptal edilebilir. Alternatif olarak, bekleyen nesneyi silebilirsiniz.

> [!NOTE]
> Bekleyen bir nesnenin silinmesi, sağlayıcıya yönelik x509 sertifika isteğini iptal edebilir veya iptal edemeyebilir.

|Yöntem|İstek URI'si|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>İstek

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Yanıt

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Bir veren ile bekleyen istek oluşturulduğunda Birleştir
Bekleyen bir nesne veren ile oluşturulduğunda ancak durumu "sürüyor" olduğunda birleştirmeye izin verilmez. 

X509 sertifikası oluşturma isteği başarısız olursa veya bazı nedenlerle iptal ederse ve bir x509 sertifikası bant dışı yollarla alınamazsa, KV sertifikasını tamamlamaya yönelik bir birleştirme işlemi yapılabilir.

|Yöntem|İstek URI'si|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>İstek

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Yanıt

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Bekleyen istek durumu "sürüyor" iken bir iptal iste
İptal etme yalnızca istenebilir. İstek iptal edilmiş olabilir veya iptal edilemez. Bir istek "InProgress" değilse, 400 (Hatalı Istek) http durumu döndürülür.

|Yöntem|İstek URI'si|
|------------|-----------------|
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>İstek
DÜZELTMESI`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

DÜZELTMESI`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Yanıt

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Bekleyen bir istek nesnesini Sil

> [!NOTE]
> Bekleyen nesnenin silinmesi, sağlayıcıya yönelik x509 sertifika isteğini iptal edebilir veya iptal edemeyebilir.

|Yöntem|İstek URI'si|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>İstek
SILMELI`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

SILMELI`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Yanıt

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Bir KV sertifikasını el ile oluşturma
El ile oluşturma işlemi aracılığıyla tercih ettiğiniz bir CA ile verilen bir sertifika oluşturabilirsiniz. Verenin adını "bilinmiyor" olarak ayarlayın veya veren alanını belirtmeyin.

|Yöntem|İstek URI'si|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>İstek

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Yanıt

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Bekleyen bir istek oluşturulduğunda Birleştir-el ile sertifika oluşturma

|Yöntem|İstek URI'si|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>İstek

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Öğe adı|Gerekli|Type|Version|Açıklama|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Evet|array|\<sürüm > tanıtma|X509 sertifika zinciri temel 64 dize dizisi olarak.|

### <a name="response"></a>Yanıt

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```

## <a name="see-also"></a>Ayrıca Bkz.
- [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md)
