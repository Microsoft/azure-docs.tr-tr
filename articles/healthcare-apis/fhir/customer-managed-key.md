---
title: FHıR için Azure API 'SI için müşteri tarafından yönetilen anahtarları yapılandırma
description: Azure API 'de, Cosmos DB aracılığıyla Azure API 'de desteklenen kendi anahtar özelliğini getirin
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: daa71a6df0ad412823736b3ee094cfd3945af492
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220858"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Bekleyen müşteri tarafından yönetilen anahtarları yapılandırma

FHıR hesabı için yeni bir Azure API 'SI oluşturduğunuzda verileriniz varsayılan olarak Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Şimdi, seçtiğiniz ve yönettiğiniz kendi anahtarınızı kullanarak veriler için ikinci bir şifreleme katmanı ekleyebilirsiniz.

Azure 'da bu, genellikle müşterinin Azure Key Vault bir şifreleme anahtarı kullanılarak gerçekleştirilir. Azure SQL, Azure depolama ve Cosmos DB bu özelliği bugün sağlayan bazı örneklerdir. FHıR için Azure API Cosmos DB bu destekten yararlanır. Bir hesap oluşturduğunuzda, bir Azure Key Vault anahtar URI 'SI belirtme seçeneğine sahip olursunuz. Bu anahtar, DB hesabı sağlandığında Cosmos DB geçirilecek. Bir FHıR isteği yapıldığında, Cosmos DB anahtarınızı getirir ve verileri şifrelemek/şifresini çözmek için kullanır. Başlamak için aşağıdaki bağlantılara başvurabilirsiniz:

- [Azure aboneliğiniz için Azure Cosmos DB kaynak sağlayıcısını kaydetme](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Azure Key Vault örneğinizi yapılandırma](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Azure Key Vault örneğine bir erişim ilkesi ekleme](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [Azure Key Vault bir anahtar oluşturun](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Azure portalını kullanma

Azure portal için Azure API 'nizi oluştururken, "ek ayarlar" sekmesinde "veritabanı ayarları" altında "veri şifreleme" yapılandırma seçeneğini görebilirsiniz. Hizmet tarafından yönetilen anahtar seçeneği varsayılan olarak seçilir. 

KeyPicker adresinden anahtarınızı seçebilirsiniz:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

İsterseniz, "müşteri tarafından yönetilen anahtar" seçeneğini belirleyerek Azure Key Vault anahtarınızı burada belirtebilirsiniz. Anahtar URI 'sini buraya girebilirsiniz:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="FHıR için Azure API oluşturma":::

Mevcut FHıR hesaplarında, anahtar şifreleme seçimini (hizmet veya müşteri tarafından yönetilen anahtar) aşağıda gösterildiği gibi "veritabanı" dikey penceresinde görüntüleyebilirsiniz. Yapılandırma seçeneği seçildikten sonra değiştirilemez. Ancak anahtarınızı değiştirebilir ve güncelleştirebilirsiniz.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Veritabanı":::

Ayrıca, belirtilen anahtarın yeni bir sürümünü oluşturabilirsiniz, bundan sonra verilerinizin hizmet kesintisi olmadan yeni sürümle şifrelenmesini sağlayabilirsiniz. Verilere erişimi kaldırmak için anahtara erişimi de kaldırabilirsiniz. Anahtar devre dışı bırakıldığında, sorgular bir hataya neden olur. Anahtar yeniden etkinleştirilmişse sorgular yeniden başarılı olur.




## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

Azure Key Vault anahtar URI 'SI ile aşağıdaki PowerShell komutunu çalıştırarak CMK 'yi PowerShell kullanarak yapılandırabilirsiniz:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma

PowerShell yönteminde olduğu gibi, Azure Key Vault anahtar URI 'nizi parametre altına geçirerek `key-vault-key-uri` ve AŞAĞıDAKI CLI komutunu çalıştırarak CMK 'yi yapılandırabilirsiniz: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Azure Key Vault anahtar URI 'SI ile CMK 'yi, **Özellikler** nesnesindeki **Keyvaultkeyuri** özelliği altına geçirerek yapılandırabilirsiniz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Ve şablonu aşağıdaki PowerShell betiğine dağıtabilirsiniz:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, müşteri tarafından yönetilen anahtarların Azure portal, PowerShell, CLı ve Kaynak Yöneticisi şablonu kullanarak geri kalanında nasıl yapılandırılacağını öğrendiniz. Karşılaşabileceğiniz diğer sorular için Azure Cosmos DB SSS bölümüne bakabilirsiniz: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: CMK 'yi kurma](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)