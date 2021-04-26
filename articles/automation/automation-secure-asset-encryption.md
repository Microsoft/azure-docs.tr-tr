---
title: Azure Otomasyonu'nda güvenli varlıkların şifrelenmesi
description: Bu makalede, Otomasyon hesabını şifrelemeyi kullanmak üzere yapılandırmaya yönelik kavramlar sunulmaktadır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: b46cf3a742158a3347b43a1e9bc6d62d0b2160d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773700"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Azure Otomasyonu'nda güvenli varlıkların şifrelenmesi

Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, birden çok şifreleme düzeyi kullanılarak Azure Otomasyonu 'nda korunur. Şifreleme için kullanılan en üst düzey anahtara bağlı olarak, şifreleme için iki model vardır:

- Microsoft tarafından yönetilen anahtarları kullanma
- Yönettiğiniz anahtarları kullanma

## <a name="microsoft-managed-keys"></a>Microsoft tarafından yönetilen anahtarlar

Varsayılan olarak, Azure Otomasyonu hesabınız Microsoft tarafından yönetilen anahtarları kullanır.

Her bir güvenli varlık şifrelenir ve her Otomasyon hesabı için oluşturulan benzersiz bir anahtar (veri şifreleme anahtarı) kullanılarak Azure Otomasyonu 'nda depolanır. Bu anahtarların kendisi, bir hesap şifreleme anahtarı (AEK) olarak adlandırılan her bir hesap için oluşturulan başka bir benzersiz anahtar kullanılarak Azure Otomasyonu 'nda şifrelenir ve depolanır. Bu hesap şifreleme anahtarları Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenmiş ve Azure Otomasyonu 'nda depolanıyor. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Key Vault ile yönettiğiniz anahtarlar (Önizleme)

Otomasyon hesabınız için güvenli varlıkların şifrelemesini kendi anahtarlarınızla yönetebilirsiniz. Otomasyon hesabı düzeyinde müşteri tarafından yönetilen bir anahtar belirttiğinizde, bu anahtar Otomasyon hesabı için hesap şifreleme anahtarına erişimi korumak ve denetlemek için kullanılır. Bu sırayla tüm güvenli varlıkları şifrelemek ve şifresini çözmek için kullanılır. Müşteri tarafından yönetilen anahtarlar, erişim denetimlerini oluşturma, döndürme, devre dışı bırakma ve iptal etme için daha fazla esneklik sunar. Ayrıca, güvenli varlıklarınızı korumak için kullanılan şifreleme anahtarlarını da denetleyebilirsiniz.

Müşteri tarafından yönetilen anahtarları depolamak için Azure Key Vault kullanın. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz.  Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Otomasyon hesabı için müşteri tarafından yönetilen anahtarların kullanımı

Otomasyon hesabı için müşteri tarafından yönetilen anahtarlarla şifrelemeyi kullandığınızda Azure Otomasyonu, hesap şifreleme anahtarını ilişkili anahtar kasasındaki müşteri tarafından yönetilen anahtarla sarar. Müşteri tarafından yönetilen anahtarların etkinleştirilmesi performansı etkilemez ve hesap, herhangi bir gecikme olmadan hemen yeni anahtarla şifrelenir.

Yeni bir Otomasyon hesabı her zaman Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Hesap oluşturulduğu sırada müşteri tarafından yönetilen anahtarları etkinleştirmek mümkün değildir. Müşteri tarafından yönetilen anahtarlar Azure Key Vault depolanır ve Anahtar Kasası, Otomasyon hesabıyla ilişkili yönetilen kimliğe anahtar izinleri veren erişim ilkeleriyle sağlanmalıdır. Yönetilen kimlik yalnızca depolama hesabı oluşturulduktan sonra kullanılabilir.

Azure Otomasyonu güvenli varlık şifrelemesi için kullanılan anahtarı değiştirirken, müşteri tarafından yönetilen anahtarları etkinleştirerek veya devre dışı bırakarak, anahtar sürümünü güncelleştirerek veya farklı bir anahtar belirttiğinizde, hesap şifreleme anahtarının şifrelenmesi değişir ancak Azure Otomasyonu hesabınızdaki güvenli varlıkların yeniden şifrelenmesi gerekmez.

> [!NOTE] 
> Müşteri tarafından yönetilen anahtarları etkinleştirmek için API sürüm 2020-01-13 ile Azure Otomasyonu REST API çağrıları yapmanız gerekir-Önizleme

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Azure Otomasyonu 'nda müşteri tarafından yönetilen anahtarları kullanma önkoşulları

Bir Otomasyon hesabı için müşteri tarafından yönetilen anahtarları etkinleştirmeden önce, aşağıdaki önkoşulların karşılandığından emin olmanız gerekir:

 - Müşteri tarafından yönetilen anahtar, bir Azure Key Vault depolanır. 
 - Anahtar kasasında hem **geçici silme** hem de **Temizleme** özelliklerini etkinleştirin. Bu özellikler, yanlışlıkla silme durumunda anahtarların kurtarılmasına izin vermek için gereklidir.
 - Yalnızca RSA anahtarları Azure Otomasyonu şifrelemesi ile desteklenir. Anahtarlar hakkında daha fazla bilgi için bkz. [Azure Key Vault anahtarlar, gizli diziler ve sertifikalar hakkında](../key-vault/general/about-keys-secrets-certificates.md).
- Otomasyon hesabı ve Anahtar Kasası farklı aboneliklerde olabilir, ancak aynı Azure Active Directory kiracısında olması gerekir.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Otomasyon hesabına bir kimlik atama

Müşteri tarafından yönetilen anahtarları bir Otomasyon hesabıyla birlikte kullanmak için Otomasyon hesabınızın, müşteri tarafından yönetilen anahtarları depolayan anahtar kasasında kimlik doğrulaması yapması gerekir. Azure Otomasyonu, Azure Key Vault hesabın kimliğini doğrulamak için sistem tarafından atanmış Yönetilen kimlikler kullanır. Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md)

Aşağıdaki REST API çağrısını kullanarak, bir sistem tarafından atanmış yönetilen kimliği Otomasyon hesabına yapılandırın:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

İstek gövdesi:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Otomasyon hesabı için sistem tarafından atanan kimlik şuna benzer bir yanıtta döndürülür:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>Key Vault erişim ilkesinin yapılandırması

Otomasyon hesabına yönetilen bir kimlik atandıktan sonra, müşteri tarafından yönetilen anahtarları depolayan anahtar kasasının erişimini yapılandırırsınız. Azure Otomasyonu, müşteri tarafından yönetilen anahtarlar üzerinde **Get**, **Recover**, **wrapKey**, **UnwrapKey** gerektirir.

Bu tür bir erişim ilkesi aşağıdaki REST API çağrısı kullanılarak ayarlanabilir:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

İstek gövdesi:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Otomasyon hesabı için yönetilen kimliğin yanıtından sırasıyla **Identity. Tenantıd** ve **Identity. PrincipalId** değerleri ile birlikte **tenantıd** ve **ObjectID** alanları sağlanmalıdır.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Otomasyon hesabının yapılandırmasını, müşteri tarafından yönetilen anahtarı kullanacak şekilde değiştirin

Son olarak, aşağıdaki REST API çağrısını kullanarak Otomasyon hesabınızı Microsoft tarafından yönetilen anahtarlardan müşteri tarafından yönetilen anahtarlara geçirebilirsiniz:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

İstek gövdesi:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Örnek yanıt

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Müşteri tarafından yönetilen bir anahtarın dönüşü

Azure Key Vault, müşteri tarafından yönetilen bir anahtarı uyumluluk ilkelerinize göre döndürebilirsiniz. Anahtar döndürüldüğünde, Otomasyon hesabını yeni anahtar URI 'sini kullanacak şekilde güncelleştirmeniz gerekir.

Anahtarın döndürülmesi Otomasyon hesabındaki güvenli varlıkların yeniden şifrelenmesini tetiklemez. Başka bir eylem gerekli değildir.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Müşteri tarafından yönetilen anahtara erişimin iptali

Müşteri tarafından yönetilen anahtarlara erişimi iptal etmek için PowerShell veya Azure CLı kullanın. Daha fazla bilgi için bkz. PowerShell veya [Azure Key Vault clı](/cli/azure/keyvault) [Azure Key Vault](/powershell/module/az.keyvault/) . Erişimi iptal etmek, Azure Otomasyonu tarafından şifreleme anahtarına erişilemediğinden, Otomasyon hesabındaki tüm güvenli varlıklara erişimi etkin bir şekilde engeller.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Key Vault anlamak için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md).
- Sertifikalarla çalışmak için bkz. [Azure Otomasyonu 'nda sertifikaları yönetme](shared-resources/certificates.md).
- Kimlik bilgilerini işlemek için bkz. [Azure Otomasyonu 'nda kimlik bilgilerini yönetme](shared-resources/credentials.md).
- Otomasyon değişkenlerini kullanmak için [Azure Otomasyonu 'nda değişkenleri yönetin](shared-resources/variables.md).
- Bağlantılarla çalışırken yardım için bkz. [Azure Otomasyonu 'nda bağlantıları yönetme](automation-connections.md).
