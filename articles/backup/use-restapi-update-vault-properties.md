---
title: Kurtarma Hizmetleri Kasası yapılandırmasını REST API güncelleştirme
description: Bu makalede, REST API kullanarak kasasının yapılandırmasını güncelleştirmeyi öğrenin.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91567834"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>REST API kullanarak Azure kurtarma hizmetleri Kasası yapılandırmasını güncelleştirme

Bu makalede, Azure kurtarma hizmetleri kasasında REST API kullanarak yedeklemeyle ilgili yapılandırmaların nasıl güncelleştirileceğini açıklanmaktadır.

## <a name="soft-delete-state"></a>Geçici silme durumu

Korunan bir öğenin yedeklerini silmek, izlenmesi gereken önemli bir işlemdir. Yanlışlıkla silinmelere karşı korunmak için, Azure kurtarma hizmetleri kasasının geçici silme yeteneği vardır. Bu özellik, gerekirse silinen yedeklemeleri, silinme sonrasında bir süre içinde geri yüklemenize olanak sağlar.

Ancak bu özelliğin gerekmediği senaryolar vardır. Azure kurtarma hizmetleri Kasası, içinde yedekleme öğeleri varsa, hatta geçici olarak silinenler silinemez. Bu, kasanın hemen silinmesi gerektiğinde bir sorun oluşturabilir. Örneğin: dağıtım işlemleri genellikle aynı iş akışındaki oluşturulan kaynakları temizler. Dağıtım bir kasa oluşturabilir, bir öğe için yedeklemeleri yapılandırabilir, test geri yükleme yapabilir ve sonra yedekleme öğelerini ve kasasını silmeye devam edebilir. Kasa silme işlemi başarısız olursa, tüm dağıtım başarısız olabilir. Geçici silme işleminin devre dışı bırakılması, hemen silmeyi güvence altına almanın tek yoludur.

Bu nedenle, senaryoya bağlı olarak belirli bir kasa için geçici silme devre dışı bırakılıp başlatılmayacağını dikkatle seçmeniz gerekir. Daha fazla bilgi için bkz. [geçici silme makalesi](backup-azure-security-feature-cloud.md).

### <a name="fetch-soft-delete-state-using-rest-api"></a>REST API kullanarak geçici silme durumunu getirme

Varsayılan olarak, yeni oluşturulan tüm kurtarma hizmetleri kasasında geçici silme durumu etkinleştirilir. Bir kasadaki geçici silme durumunu getirmek/güncelleştirmek için, yedekleme kasasının config ile ilgili [REST API belgesini](/rest/api/backup/backupresourcevaultconfigs) kullanın

Bir kasadaki geçici silme işleminin geçerli durumunu getirmek için aşağıdaki *alma* işlemini kullanın

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Get URI 'si, `{subscriptionId}` `{vaultName}` , `{vaultresourceGroupName}` parametreleri. Bu örnekte, `{vaultName}` "Testkasası" ve `{vaultresourceGroupName}` "testVaultRG" dir. URI 'de tüm gerekli parametreler verildiğinden, ayrı bir istek gövdesi gerekmez.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Yanıtlar

' GET ' işlemi için başarılı yanıt aşağıda gösterilmiştir:

|Ad  |Tür  |Description  |
|---------|---------|---------|
|200 TAMAM     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | Tamam        |

##### <a name="example-response"></a>Örnek yanıt

' GET ' isteği gönderildikten sonra, 200 (başarılı) yanıtı döndürülür.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>REST API kullanarak geçici silme durumunu güncelleştirme

REST API kullanarak kurtarma hizmetleri kasasının geçici silme durumunu güncelleştirmek için aşağıdaki *PUT* işlemini kullanın

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Put URI 'si, `{subscriptionId}` `{vaultName}` , `{vaultresourceGroupName}` parametreleri. Bu örnekte, `{vaultName}` "Testkasası" ve `{vaultresourceGroupName}` "testVaultRG" dir. URI 'yi yukarıdaki değerlerle değiştirirseniz URI şöyle görünecektir.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>İstek gövdesini oluşturma

Aşağıdaki ortak tanımlar bir istek gövdesi oluşturmak için kullanılır

Daha fazla ayrıntı için [REST API belgelerine](/rest/api/backup/backupresourcevaultconfigs/update#request-body) bakın

|Name  |Gerekli  |Tür  |Description  |
|---------|---------|---------|---------|
|Özelliği     |         |   Dize      |  İsteğe bağlı eTag       |
|location     |  true       |Dize         |   Kaynak konumu      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Kasanın özellikleri       |
|etiketler     |         | Nesne        |     Kaynak etiketleri    |

#### <a name="example-request-body"></a>Örnek istek gövdesi

Aşağıdaki örnek, geçici silme durumunu ' Disabled ' olarak güncelleştirmek için kullanılır.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>Düzeltme Eki işlemi için yanıtlar

' PATCH ' işlemi için başarılı yanıt aşağıda gösterilmiştir:

|Ad  |Tür  |Description  |
|---------|---------|---------|
|200 TAMAM     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | Tamam        |

##### <a name="example-response-for-the-patch-operation"></a>Düzeltme Eki işlemi için örnek yanıt

' PATCH ' isteği gönderildikten sonra, bir 200 (başarılı) yanıtı döndürülür.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Bu kasada bir Azure VM yedeklemesi için bir yedekleme Ilkesi oluşturun](backup-azure-arm-userestapi-createorupdatepolicy.md).

Azure REST API 'Leri hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

- [Azure kurtarma hizmetleri sağlayıcısı REST API](/rest/api/recoveryservices/)
- [Azure REST API’yi kullanmaya başlayın](/rest/api/azure/)
