---
title: Kimlik bilgilerini Azure Key Vault’ta depolama
description: Azure Data Factory, çalışma zamanında otomatik olarak alabileceği bir Azure Anahtar Kasası 'nda kullanılan veri depoları için kimlik bilgilerini nasıl depolayacağınızı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 00c28a9ede01a74f4ae64109d277276050047461
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653773"
---
# <a name="store-credential-in-azure-key-vault"></a>Kimlik bilgilerini Azure Key Vault içinde depola

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Veri depoları için kimlik bilgilerini saklayabilir ve bir [Azure Key Vault](../key-vault/general/overview.md). Azure Data Factory, veri deposu/işlem kullanan bir etkinliği yürütürken kimlik bilgilerini alır.

Şu anda, özel etkinlik dışındaki tüm etkinlik türleri bu özelliği destekler. Özellikle bağlayıcı yapılandırması için, Ayrıntılar için [her bağlayıcı konusunun](copy-activity-overview.md#supported-data-stores-and-formats) "bağlı hizmet özellikleri" bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu özellik, Data Factory tarafından yönetilen kimliği kullanır. [Data Factory Için yönetilen kimliğin](data-factory-service-identity.md) nasıl çalıştığını öğrenin ve Data Factory 'nizin ilişkili bir tane olduğundan emin olun.

## <a name="steps"></a>Adımlar

Azure Key Vault depolanan bir kimlik bilgisine başvurmak için şunları yapmanız gerekir:

1. Fabrikalarınızla birlikte üretilen "yönetilen kimlik nesnesi KIMLIĞI" değerini kopyalayarak **Veri Fabrikası tarafından yönetilen kimliği alın** . ADF yazma Kullanıcı arabirimini kullanıyorsanız, yönetilen kimlik nesnesi KIMLIĞI Azure Key Vault bağlı hizmet oluşturma penceresinde gösterilir; Ayrıca, Azure portal [Veri Fabrikası tarafından yönetilen kimliği alma](data-factory-service-identity.md#retrieve-managed-identity)bölümüne başvurabilirsiniz.
2. **Azure Key Vault yönetilen kimlik erişimine izin verin.** Anahtar kasasında erişim ilkelerine > erişim Ilkesi Ekle >, bu yönetilen kimliği gizli izinler açılan menüsünde **Al** izni vermek için arayın. Bu belirlenen fabrikasının anahtar kasasında gizli erişimine izin verir.
3. **Azure Key Vault işaret eden bir bağlı hizmet oluşturun.** [Azure Key Vault bağlı hizmeti](#azure-key-vault-linked-service)' ne bakın.
4. **İçinde, Anahtar Kasası 'nda depolanan karşılık gelen gizli dizi ile bağlantılı veri deposu bağlı hizmeti oluşturun.** [Anahtar Kasası 'nda depolanan başvuru gizli](#reference-secret-stored-in-key-vault)dizisi bölümüne bakın.

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault bağlantılı hizmeti

Azure Key Vault bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **AzureKeyVault** olarak ayarlanmalıdır. | Yes |
| baseUrl | Azure Key Vault URL 'sini belirtin. | Yes |

**Yazma Kullanıcı arabirimini kullanma:**

**Bağlantı**  ->  **bağlı hizmetleri**  ->  **Yeni**' yi seçin. Yeni bağlı hizmet ' de, "Azure Key Vault" araması yapın ve seçin:

![Arama Azure Key Vault](media/store-credentials-in-key-vault/search-akv.png)

Kimlik bilgilerinizin depolandığı sağlanmış Azure Key Vault seçin. AKV bağlantınızın geçerli olduğundan emin olmak için **bağlantıyı test** edebilirsiniz. 

![Azure Key Vault'u yapılandırma](media/store-credentials-in-key-vault/configure-akv.png)

**JSON örneği:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Anahtar kasasında depolanan gizli diziye başvurma

Aşağıdaki özellikler, bağlantılı hizmette bir Anahtar Kasası gizliliğine başvuran bir alanı yapılandırdığınızda desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Alanın Type özelliği: **AzureKeyVaultSecret** olarak ayarlanmalıdır. | Yes |
| secretName | Azure Key Vault ' deki gizli dizi adı. | Yes |
| Gizlidizisürümü | Azure Key Vault ' deki gizli dizi sürümü.<br/>Belirtilmemişse, her zaman gizli dizinin en son sürümünü kullanır.<br/>Belirtilmişse, belirtilen sürüme de sahiptir.| No |
| mağaza | Kimlik bilgisini depolamak için kullandığınız Azure Key Vault bağlı bir hizmete başvurur. | Yes |

**Yazma Kullanıcı arabirimini kullanma:**

Veri deponuza/hesaplamanıza bağlantı oluştururken gizli alanlar için **Azure Key Vault** seçin. Sağlanan Azure Key Vault bağlı hizmetini seçin ve **gizli dizi adını** sağlayın. İsteğe bağlı olarak gizli bir sürüm da sağlayabilirsiniz. 

>[!TIP]
>Bağlı hizmette SQL Server, BLOB depolama, vb. gibi bağlantı dizesini kullanan bağlayıcılar için, yalnızca gizli alan gibi bir parola (örneğin, AKV) depolamayı ya da bağlantı dizesinin tamamını AKV 'de depolamayı seçebilirsiniz. Kullanıcı arabiriminde her iki seçeneği de bulabilirsiniz.

![Azure Key Vault gizli anahtarını yapılandırma](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON örneği: ("parola" bölümüne bakın)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
