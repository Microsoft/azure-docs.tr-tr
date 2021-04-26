---
title: Azure kanıtlama için sanallaştırma tabanlı güvenlik (VBS) protokolü
description: VBS kanıtlama protokolü
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507922"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Güvenilir Platform Modülü (TPM) ve sanallaştırma tabanlı güvenlik (VBS) şifreleme protokolü 

Güçlü bir güvenlik garantisi sağlamak için kanıtlama Microsoft Azure, bir güven zincirinin, hiper yönetici ve güvenli çekirdek başlatma ile bir güven köküne (TPM) karşı korunmasını sağlar. Bu Azure kanıtlama elde etmek için güvenli kuşve güvende güven kurmadan önce makinenin önyükleme durumunu atlamanız gerekir. İşletim sistemi, hiper yönetici ve güvenli çekirdek ikilileri, doğru resmi Microsoft yetkilileri tarafından imzalanmalıdır ve güvenli bir şekilde yapılandırılmalıdır. Güvenilir Platform Modülü (TPM) ve hiper yöneticinin sistem durumu arasında güven ilişkisi olduktan sonra, ölçülen önyükleme günlüğünde sunulan sanallaştırma tabanlı güvenlik (VBS) Şifreleçlerine güvenebilir ve bu anahtarla güveni bağlayan ve güvenlik düzeyi ve önyükleme kanıtlama özellikleri gibi başka talepler içeren bir kanıtlama raporu ile bir anahtar çiftinin oluşturulduğunu doğrulayabiliriz. 

VBS enclaven, güvenlik temelini doğrulamaya yönelik ölçümü sağlamak için TPM gerektirir. VBS enclaven, protokolünde istek nesnesine ek olarak TPM uç noktası tarafından test edilmiştir. 

## <a name="protocol-messages"></a>Protokol iletileri

### <a name="init-message"></a>Init iletisi

#### <a name="direction"></a>Yön

İstemci-Azure kanıtlama >

#### <a name="payload"></a>Te

```
{ 
  "type": "aikcert" 
} 
```

"Type" (ASCII String): istenen kanıtlama türünü temsil eder. Şu anda yalnızca "aıkcert" desteklenir.

### <a name="challenge-message"></a>Sınama iletisi

#### <a name="direction"></a>Yön

Azure kanıtlama-> Istemcisi

#### <a name="payload"></a>Te

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**Challenge** (BASE64URL (sekizli)): hizmet tarafından verilen rastgele değer.

**service_context** (BASE64URL (sekizli)): hizmet tarafından oluşturulan opak bağlam.


### <a name="request-message"></a>İstek iletisi

#### <a name="direction"></a>Yön

İstemci-Azure kanıtlama > 

#### <a name="payload"></a>Te

```
{
  "request": "<JWS>"
}
```

**istek** (JWS): Istek bir JSON Web imzası (JWS) yapısından oluşur. JWS korumalı üst bilgi ve JWS yükü aşağıda gösterilmiştir. Her JWS yapısında olduğu gibi, son değer aşağıdakilerden oluşur:

BASE64URL (UTF8 (JWS korumalı üst bilgi)) | | '.' ||

BASE64URL (JWS yükü) | | '.' ||

BASE64URL (JWS Imzası)

##### <a name="jws-protected-header"></a>JWS korumalı üst bilgi

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS yükü

JWS yükü Basic veya VBS türünde olabilir. Temel, kanıtlama kanıtlaması VBS verileri içermediği zaman kullanılır. 

Yalnızca TPM örneği: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + vbs kuşatma örneği: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (stringoruri): bağlı olan taraf tanımlayıcısı. Hizmet tarafından makine KIMLIĞI talebi hesaplamasında kullanılır

**rp_data** (BASE64URL (sekizli)): bağlı olan taraf tarafından geçen donuk veri. Bu, genellikle raporun yeniliği güvence altına almak için bir kerelik olarak bağlı olan taraf tarafından kullanılır

**sınama** (BASE64URL (sekizli)): hizmet tarafından verilen rastgele değer

**tpm_att_data**: TPM ile ilgili kanıtlama verileri

- **srtm_boot_log (BASE64URL (sekizli))**: işlev Tbsi_Get_TCG_Log_Ex, günlük türü = TBS_TCGLOG_SRTM_BOOT tarafından alınan SRTM önyükleme günlükleri

- **srtm_resume_log (BASE64URL (sekizli))**: SRTM, günlük türü = TBS_TCGLOG_SRTM_RESUME olan işlev Tbsi_Get_TCG_Log_Ex tarafından alınan günlüğü sürdürür

- **drtm_boot_log (BASE64URL (sekizli))**: işlev Tbsi_Get_TCG_Log_Ex tarafından alınan drtm önyükleme günlükleri, günlük türü = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (sekizli))**: drtm, günlük türü = TBS_TCGLOG_DRTM_RESUME olan işlev Tbsi_Get_TCG_Log_Ex tarafından alınan günlüğü sürdürür

- **aik_cert (BASE64URL (sekizli))**: NCryptGetProperty işlevi tarafından döndürülen X. 509.440 sertifikası özelliği = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: AIK 'Nin JSON Web anahtarı (JWK) nesnesi olarak temsil ettiği genel bölümü (RFC 7517)

- **current_claim (BASE64URL (sekizli))**: dwclaimtype = NCRYPT_CLAIM_PLATFORM ve Parameter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK tüm PTE 'leri içerecek şekilde ayarlanan NCryptCreateClaim işlevi tarafından döndürülen geçerli PCR durumu için kanıtlama talebi. Hizmet tarafından gönderilen sınama bu talebin hesaplamasında de kullanılmalıdır

- **boot_claim (BASE64URL (sekizli))**: dwclaimtype = NCRYPT_CLAIM_PLATFORM ve Parameter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK tüm PTE 'leri içerecek şekilde ayarlanan NCryptCreateClaim işlevi tarafından döndürülen PCR durumu için kanıtlama talebi

**vsm_report**   (BASE64URL (sekizli)): işlev enclavegetattestationreport tarafından döndürülen vbs kuşatma kanıtlama raporu. EnclaveData parametresi, report_signed değerinin SHA-512 karması olmalıdır (açılış ve kapanış ayraçları dahil). Karma işlev girişi UTF8 (report_signed)

**attest_key**: bir JSON Web anahtarı (JWK) nesnesi olarak temsil edilen şifreleme anahtarının genel bölümü (RFC 7517)

**custom_claims**: hizmete gönderilen ve ilke tarafından değerlendirilebilen özel şifreleme talepleri dizisi. Talep

- **ad (dize)**: talebin adı. Bu ad, kanıtlama hizmeti tarafından belirlenen bir URL 'ye eklenir (çakışmaları önlemek için) ve birleştirilmiş dize ilkede kullanılabilecek talebin türü olur

- **değer (dize)**: talebin değeri

- **value_type (dize)**: talep değerinin veri türü

**service_context** (BASE64URL (sekizli)): hizmet tarafından oluşturulan opak bağlam.

### <a name="report-message"></a>Rapor iletisi

#### <a name="direction"></a>Yön

Azure kanıtlama-> Istemcisi

#### <a name="payload"></a>Te

```
{
  "report": "<JWT>"
}
```

**Report** (JWT): JSON Web token (JWT) biçiminde kanıtlama raporu (RFC 7519).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kanıtlama iş akışı](workflow.md)
