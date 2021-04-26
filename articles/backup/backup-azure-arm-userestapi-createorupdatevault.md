---
title: REST API kullanarak kurtarma hizmetleri kasaları oluşturma
description: Bu makalede, REST API kullanarak Azure VM yedeklemesi 'nin yedekleme ve geri yükleme işlemlerini yönetmeyi öğrenin.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: a37808548ec58977b7d6af16c75b94b7b5efe446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002945"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>REST API kullanarak Azure kurtarma hizmetleri Kasası oluşturma

REST API kullanarak bir Azure kurtarma hizmetleri Kasası oluşturma adımları [kasa oluşturma REST API](/rest/api/recoveryservices/vaults/createorupdate) belgelerinde özetlenmiştir. "Batı ABD" içinde "Testkasası" adlı bir kasa oluşturmak için bu belgeyi bir başvuru olarak kullanalım.

Bir Azure kurtarma hizmetleri Kasası oluşturmak veya güncelleştirmek için aşağıdaki *PUT* işlemini kullanın.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>İstek oluştur

*PUT* isteğini oluşturmak için `{subscription-id}` parametresi gereklidir. Birden çok aboneliğiniz varsa bkz. [birden çok abonelikle çalışma](/cli/azure/manage-azure-subscriptions-azure-cli). `{resourceGroupName}` `{vaultName}` Kaynaklarınızın yanı sıra, kaynaklarınız için bir ve tanımlarsınız `api-version` . Bu makalede, kullanılır `api-version=2016-06-01` .

Aşağıdaki üstbilgiler gereklidir:

| İstek üst bilgisi   | Açıklama |
|------------------|-----------------|
| *Content-Type:*  | Gereklidir. `application/json` olarak ayarlayın. |
| *Yetkilendirme:* | Gereklidir. Geçerli bir `Bearer` [erişim belirtecine](/rest/api/azure/#authorization-code-grant-interactive-clients) ayarlayın. |

İsteğin nasıl oluşturulacağı hakkında daha fazla bilgi için, bkz. [bir REST API isteği/yanıtı bileşenleri](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>İstek gövdesini oluşturma

Aşağıdaki ortak tanımlar bir istek gövdesi oluşturmak için kullanılır:

|Name  |Gerekli  |Tür  |Description  |
|---------|---------|---------|---------|
|Özelliği     |         |   Dize      |  İsteğe bağlı eTag       |
|location     |  true       |Dize         |   Kaynak konumu      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Kasanın özellikleri       |
|isteyin     |         |  [İsteyin](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Her Azure kaynağı için benzersiz sistem tanımlayıcısını tanımlar     |
|etiketler     |         | Nesne        |     Kaynak etiketleri    |

Kasa adı ve kaynak grubu adının PUT URI 'sinde sağlandığını unutmayın. İstek gövdesi konumu tanımlar.

## <a name="example-request-body"></a>Örnek istek gövdesi

Aşağıdaki örnek gövde, "Batı ABD" içinde bir kasa oluşturmak için kullanılır. Konumu belirtin. SKU her zaman "standart" dır.

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Yanıtlar

Bir kurtarma hizmetleri Kasası oluşturma veya güncelleştirme işlemi için iki başarılı yanıt vardır:

|Ad  |Tür  |Description  |
|---------|---------|---------|
|200 TAMAM     |   [Kasa](/rest/api/recoveryservices/vaults/createorupdate#vault)      | Tamam        |
|201 oluşturuldu     | [Kasa](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Oluşturulan      |

REST API yanıtları hakkında daha fazla bilgi için bkz. [Yanıt Iletisini işleme](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Örnek yanıt

Önceki örnek istek gövdesinden sıkıştırılmış *201 tarafından oluşturulan* bir yanıt, bir *kimlik* atandığını ve *provisioningstate* *başarılı* olduğunu gösterir:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Bu kasada bir Azure VM yedeklemesi için bir yedekleme Ilkesi oluşturun](backup-azure-arm-userestapi-createorupdatepolicy.md).

Azure REST API 'Leri hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

- [Azure kurtarma hizmetleri sağlayıcısı REST API](/rest/api/recoveryservices/)
- [Azure REST API’yi kullanmaya başlayın](/rest/api/azure/)
