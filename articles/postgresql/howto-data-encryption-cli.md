---
title: Veri şifreleme-Azure CLı-PostgreSQL için Azure veritabanı-tek sunucu
description: Azure CLı kullanarak PostgreSQL için Azure veritabanı için veri şifrelemeyi ayarlama ve yönetme hakkında bilgi edinin.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240622"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı için veri şifreleme tek sunucu

Azure CLı kullanarak PostgreSQL için Azure veritabanı tek sunucu için veri şifrelemeyi ayarlama ve yönetme hakkında bilgi edinin.

## <a name="prerequisites-for-azure-cli"></a>Azure CLı önkoşulları

* Bu abonelikte bir Azure aboneliğiniz olması ve bir yönetici olmanız gerekir.
* Bir anahtar kasası ve müşterinin yönettiği anahtar için kullanılacak bir anahtar oluşturun. Ayrıca, anahtar kasasında Temizleme korumasını ve geçici silmeyi etkinleştirin.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* Oluşturulan Azure Key Vault, PostgreSQL için Azure veritabanı 'nın tek sunucu veri şifrelemesi için kullanılacak anahtarı oluşturun.

   ```azurecli-interactive
   az keyvault key create --name <key_name> -p software --vault-name <vault_name>
   ```

* Mevcut bir anahtar kasasını kullanabilmeniz için, müşteri tarafından yönetilen anahtar olarak kullanmak için aşağıdaki özelliklere sahip olmalıdır:
  * [Geçici silme](../key-vault/general/soft-delete-overview.md)

      ```azurecli-interactive
      az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
      ```

  * [Korumalı Temizleme](../key-vault/general/soft-delete-overview.md#purge-protection)

      ```azurecli-interactive
      az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
      ```

* Müşteri tarafından yönetilen anahtar olarak kullanılabilmesi için anahtarın aşağıdaki özniteliklere sahip olması gerekir:
  * Son kullanma tarihi yok
  * Devre dışı bırakılmadı
  * Get, **Wrap** ve **geri saral** işlemlerini gerçekleştirin 

## <a name="set-the-right-permissions-for-key-operations"></a>Anahtar işlemleri için doğru izinleri ayarla

1. PostgreSQL için Azure veritabanı tek sunuculu yönetilen kimliği almanın iki yolu vardır.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Managed Identity ile PostgreSQL için yeni bir Azure veritabanı sunucusu oluşturun.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Mevcut bir PostgreSQL için Azure veritabanı sunucusunu yönetilen bir kimlik almak üzere güncelleştirin.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. **Ana** Için PostgreSQL tek sunucu sunucusunun adı olan **anahtar izinlerini** (**Get**, **Wrap**, **Unwrap**) ayarlayın.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>PostgreSQL için Azure veritabanı için veri şifrelemeyi ayarlama tek sunucu

1. Azure Key Vault oluşturulan anahtarı kullanarak PostgreSQL için Azure veritabanı tek sunucu için veri şifrelemeyi etkinleştirin.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Anahtar URL 'si:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Geri yükleme veya çoğaltma sunucuları için veri şifrelemeyi kullanma

PostgreSQL için Azure veritabanı tek sunucu, Key Vault ' de depolanan bir müşterinin yönetilen anahtarıyla şifrelendikten sonra, sunucunun yeni oluşturulan kopyası da şifrelenir. Bu yeni kopyayı yerel veya coğrafi geri yükleme işlemi aracılığıyla ya da bir çoğaltma (yerel/bölge) işlemi aracılığıyla yapabilirsiniz. Bu nedenle, şifrelenmiş bir PostgreSQL tek sunuculu sunucu için, şifrelenmiş geri yüklenmiş bir sunucu oluşturmak için aşağıdaki adımları kullanabilirsiniz.

### <a name="creating-a-restoredreplica-server"></a>Geri yüklenen/çoğaltılan sunucu oluşturma

* [Geri yükleme sunucusu oluşturma](howto-restore-server-cli.md)
* [Okuma çoğaltması sunucusu oluşturma](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Sunucu geri yüklendikten sonra, geri yüklenen sunucu için veri şifrelemeyi yeniden doğrula

*   Çoğaltma sunucusu için kimlik ata
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Geri yüklenen/çoğaltma sunucusu için kullanılması gereken mevcut anahtarı al

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Geri yüklenen/çoğaltma sunucusu için yeni kimliğin ilkesini ayarlama

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Geri yüklenen/çoğaltılan sunucuyu şifreleme anahtarıyla yeniden doğrulama

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>PostgreSQL için Azure veritabanı tek sunucu için kullanılan anahtar için ek özellik

### <a name="get-the-key-used"></a>Kullanılan anahtarı al

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Anahtar URL 'si: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Kullanılan anahtarı listeleyin

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Kullanılan anahtarı bırakın

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Veri şifrelemeyi etkinleştirmek için Azure Resource Manager şablonu kullanma

Azure portal dışında, yeni ve mevcut sunucu için Azure Resource Manager şablonlarını kullanarak PostgreSQL için Azure veritabanı 'nda veri şifrelemeyi de etkinleştirebilirsiniz.

### <a name="for-a-new-server"></a>Yeni bir sunucu için

Sunucuda veri şifrelemesi etkinken sunucu sağlamak için önceden oluşturulmuş Azure Resource Manager şablonlarından birini kullanın: [veri şifreleme Ile örnek](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Bu Azure Resource Manager şablonu, PostgreSQL için Azure veritabanı tek sunucu oluşturur ve sunucuda veri şifrelemeyi etkinleştirmek için **anahtar** **Kasası** ve parametre olarak geçirilen anahtarı kullanır.

### <a name="for-an-existing-server"></a>Var olan bir sunucu için
Ayrıca, mevcut PostgreSQL için Azure veritabanı tek sunucuları üzerinde veri şifrelemeyi etkinleştirmek üzere Azure Resource Manager şablonları kullanabilirsiniz.

* Daha önce bir özellik altında kopyaladığınız Azure Key Vault anahtarının kaynak KIMLIĞINI `Uri` Özellikler nesnesinde geçirin.

* API sürümü olarak *2020-01-01-Preview* kullanın.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

 Veri şifreleme hakkında daha fazla bilgi edinmek için bkz. [PostgreSQL Için Azure veritabanı istemci tarafından yönetilen anahtarla tek sunuculu veri şifreleme](concepts-data-encryption-postgresql.md).
