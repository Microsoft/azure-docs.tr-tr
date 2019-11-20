---
title: Kimlik bilgilerini Azure Key Vault’ta depolama
description: Azure Data Factory, çalışma zamanında otomatik olarak alabileceği bir Azure Anahtar Kasası 'nda kullanılan veri depoları için kimlik bilgilerini nasıl depolayacağınızı öğrenin.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 1e2458fa125ee4d223ef46001534fb7ae72b805e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684014"
---
# <a name="store-credential-in-azure-key-vault"></a>Kimlik bilgilerini Azure Key Vault içinde depola

Veri depoları için kimlik bilgilerini saklayabilir ve bir [Azure Key Vault](../key-vault/key-vault-overview.md). Azure Data Factory, veri deposu/işlem kullanan bir etkinliği yürütürken kimlik bilgilerini alır.

Şu anda, özel etkinlik dışındaki tüm etkinlik türleri bu özelliği destekler. Özellikle bağlayıcı yapılandırması için, Ayrıntılar için [her bağlayıcı konusunun](copy-activity-overview.md#supported-data-stores-and-formats) "bağlı hizmet özellikleri" bölümüne bakın.

## <a name="prerequisites"></a>Ön koşullar

Bu özellik, Data Factory tarafından yönetilen kimliği kullanır. [Data Factory Için yönetilen kimliğin](data-factory-service-identity.md) nasıl çalıştığını öğrenin ve Data Factory 'nizin ilişkili bir tane olduğundan emin olun.

## <a name="steps"></a>Adımlar

Azure Key Vault depolanan bir kimlik bilgisine başvurmak için şunları yapmanız gerekir:

1. Fabrikalarınızla birlikte oluşturulan "yönetilen kimlik uygulama KIMLIĞI" değerini kopyalayarak **Veri Fabrikası tarafından yönetilen kimliği alın** . ADF yazma Kullanıcı arabirimini kullanıyorsanız, yönetilen kimlik uygulama KIMLIĞI Azure Key Vault bağlı hizmet oluşturma penceresinde gösterilir; Ayrıca, Azure portal [Veri Fabrikası tarafından yönetilen kimliği alma](data-factory-service-identity.md#retrieve-managed-identity)bölümüne başvurabilirsiniz.
2. **Azure Key Vault yönetilen kimlik erişimine izin verin.** Anahtar Kasanızda > erişim ilkeleri-> Yeni > Ekle-Bu yönetilen kimlik uygulama KIMLIĞI ' ni, gizli izinler açılan menüsünde **Al** izni vermek için arayın. Bu belirlenen fabrikasının anahtar kasasında gizli erişimine izin verir.
3. **Azure Key Vault işaret eden bir bağlı hizmet oluşturun.** [Azure Key Vault bağlı hizmeti](#azure-key-vault-linked-service)' ne bakın.
4. **İçinde, Anahtar Kasası 'nda depolanan karşılık gelen gizli dizi ile bağlantılı veri deposu bağlı hizmeti oluşturun.** [Anahtar Kasası 'nda depolanan başvuru gizli](#reference-secret-stored-in-key-vault)dizisi bölümüne bakın.

## <a name="azure-key-vault-linked-service"></a>Bağlı hizmet Azure Key Vault

Azure Key Vault bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **AzureKeyVault**olarak ayarlanmalıdır. | Evet |
| BaseUrl | Azure Key Vault URL 'sini belirtin. | Evet |

**Yazma Kullanıcı arabirimini kullanma:**

**Bağlı hizmetler** -> **Bağlantılar** ' a tıklayın ->  **+ Yeni** -> "Azure Key Vault" araması yapın:

![AKV ara](media/store-credentials-in-key-vault/search-akv.png)

Kimlik bilgilerinizin depolandığı sağlanmış Azure Key Vault seçin. AKV bağlantınızın geçerli olduğundan emin olmak için **bağlantıyı test** edebilirsiniz. 

![AKV 'yi yapılandırma](media/store-credentials-in-key-vault/configure-akv.png)

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

## <a name="reference-secret-stored-in-key-vault"></a>Anahtar Kasası 'nda depolanan başvuru gizli dizisi

Aşağıdaki özellikler, bağlantılı hizmette bir Anahtar Kasası gizliliğine başvuran bir alanı yapılandırdığınızda desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Alanın Type özelliği: **AzureKeyVaultSecret**olarak ayarlanmalıdır. | Evet |
| secretName | Azure Key Vault ' deki gizli dizi adı. | Evet |
| Gizlidizisürümü | Azure Key Vault ' deki gizli dizi sürümü.<br/>Belirtilmemişse, her zaman gizli dizinin en son sürümünü kullanır.<br/>Belirtilmişse, belirtilen sürüme de sahiptir.| Hayır |
| saklayabilir | Kimlik bilgisini depolamak için kullandığınız Azure Key Vault bağlı bir hizmete başvurur. | Evet |

**Yazma Kullanıcı arabirimini kullanma:**

Veri deponuza/hesaplamanıza bağlantı oluştururken gizli alanlar için **Azure Key Vault** seçin. Sağlanan Azure Key Vault bağlı hizmetini seçin ve **gizli dizi adını**sağlayın. İsteğe bağlı olarak gizli bir sürüm da sağlayabilirsiniz. 

>[!TIP]
>Bağlı hizmette SQL Server, BLOB depolama, vb. gibi bağlantı dizesini kullanan bağlayıcılar için, yalnızca gizli alan gibi bir parola (örneğin, AKV) depolamayı ya da bağlantı dizesinin tamamını AKV 'de depolamayı seçebilirsiniz. Kullanıcı arabiriminde her iki seçeneği de bulabilirsiniz.

![AKV gizli anahtarını yapılandırma](media/store-credentials-in-key-vault/configure-akv-secret.png)

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
