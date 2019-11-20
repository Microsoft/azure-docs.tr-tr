---
title: Azure Key Vault için Azure Event Grid olay şeması
description: Azure Event Grid Azure Key Vault olaylar için belirtilen özellikleri ve şemayı açıklar
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082862"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault için Azure Event Grid olay şeması (Önizleme)

Bu makalede, şu anda önizleme aşamasında olan [Azure Key Vault](../key-vault/index.yml)olaylar için özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md).

## <a name="available-event-types"></a>Kullanılabilir olay türleri

Bir Azure Key Vault hesabı aşağıdaki olay türlerini üretir:

| Olay tam adı | Olay görünen adı | Açıklama |
| ---------- | ----------- |---|
| Microsoft. Keykasası. Certificatenewversionoluşturuldu | Sertifika yeni sürümü oluşturuldu | Yeni bir sertifika veya yeni sertifika sürümü oluşturulduğunda tetiklenir. |
| Microsoft. Keykasası. Certificatenearsüre sonu | Süresi dolmak üzere olan sertifika | Sertifikanın güncel sürümü sona ermek üzereyken tetiklenir. (Varsayılan değer, sona erme tarihinden itibaren 30 gündür.) |
| Microsoft. Keykasası. Certificatedolmuştur | Sertifikanın Süresi Doldu | Sertifikanın süre dolduğunda tetiklenir. |
| Microsoft. Keykasası. Keynewversionoluşturuldu | Anahtar yeni sürüm oluşturuldu | Yeni bir anahtar veya yeni anahtar sürümü oluşturulduğunda tetiklenir. |
| Microsoft. Keykasası. Keyyaklaştığında süre sonu | Süresi dolmak üzere olan anahtar | Anahtarın geçerli sürümünün kullanım süreleri dolduğunda tetiklenir. (Varsayılan değer, sona erme tarihinden itibaren 30 gündür.) |
| Microsoft. Keykasası. Keyerdim | Anahtarın geçerliliği zaman aşımına uğradı | Anahtarın süre dolduğunda tetiklenir. |
| Microsoft. Keykasası. Secretnewversionoluşturuldu | Gizli yeni sürüm oluşturuldu | Yeni bir gizli dizi veya yeni bir gizli dizi sürümü oluşturulduğunda tetiklenir. |
| Microsoft. Keykasası. Secretyaklaştığında süre sonu | Süre sonu yakın gizli | Bir parolanın geçerli sürümü sona ermek üzereyken tetiklenir. (Varsayılan değer, sona erme tarihinden itibaren 30 gündür.) |
| Microsoft. Keykasası. SecretExpired | Gizli dizi zaman aşımına uğradı | Gizli dizi dolduğunda tetiklenir. |

## <a name="event-examples"></a>Olay örnekleri

Aşağıdaki örnek, **Microsoft. Keykasası. Secretnewversiontarafından oluşturulan**şemayı göstermektedir:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Olay özellikleri

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Açıklama |
| ---------- | ----------- |---|
| id | string | Bu olayı tetikleyen nesnenin KIMLIĞI |
| vaultName | string | Bu olayı tetikleyen nesnenin Anahtar Kasası adı |
| Nesne | string | Bu olayı tetikleyen nesnenin türü |
| ObjectName | string | Bu olayı tetikleyen nesnenin adı |
| version | string | Bu olayı tetikleyen nesnenin sürümü |
| NBF | number | Bu olayı tetikleyen nesnenin 1970-01-01T00:00:00Z 'den bu yana saniye cinsinden olmayan Tarih |
| Exp | number | Bu olayı tetikleyen nesnenin 1970-01-01T00:00:00Z 'den bu yana geçen sona erme tarihi |


## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. Event Grid nedir [?](overview.md).
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).
* Event Grid ile Key Vault tümleştirme hakkında daha fazla bilgi için bkz. [Azure Event Grid Ile izleme Key Vault (Önizleme)](../key-vault/event-grid-overview.md).
* Event Grid Key Vault tümleştirme hakkında bir öğretici için bkz. [Azure Event Grid (Önizleme) ile Anahtar Kasası bildirimleri alma ve yanıtlama](../key-vault/event-grid-tutorial.md).
* Key Vault ve Azure Otomasyonu hakkında ek rehberlik almak için, bkz.:
    - [Azure Key Vault nedir?](../key-vault/key-vault-overview.md)
    - [Azure Event Grid ile Key Vault izleme (Önizleme)](../key-vault/event-grid-overview.md)
    - [Azure Event Grid ile Anahtar Kasası bildirimlerini alma ve yanıtlama (Önizleme)](../key-vault/event-grid-tutorial.md)
    - [Azure Otomasyonu’na genel bakış](../automation/index.yml)
