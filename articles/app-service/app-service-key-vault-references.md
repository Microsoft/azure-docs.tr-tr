---
title: Key Vault başvuruları-Azure App Service | Microsoft Docs
description: Azure App Service ve Azure Işlevlerinde Azure Key Vault başvuruları için kavramsal başvuru ve Kurulum Kılavuzu
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 8f237e4c676a53f6df15940a196a998bee529f6b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817963"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>App Service ve Azure Işlevleri için Key Vault başvurularını kullanma

> [!NOTE] 
> Key Vault başvuruları Linux tüketim planlarında Şu anda kullanılamıyor.

Bu konu başlığı altında, herhangi bir kod değişikliğine gerek kalmadan App Service veya Azure Işlevleri uygulamanızda Azure Key Vault gizliliklerle nasıl çalışılacağı gösterilmektedir. [Azure Key Vault](../key-vault/key-vault-overview.md) , erişim ilkeleri ve denetim geçmişi üzerinde tam denetim ile merkezi gizli dizi yönetimi sağlayan bir hizmettir.

## <a name="granting-your-app-access-to-key-vault"></a>Uygulamanıza Key Vault erişim verme

Key Vault parolaları okumak için bir kasasının oluşturulmuş olması ve uygulamanıza erişim izni vermeniz gerekir.

1. [Key Vault hızlı](../key-vault/quick-create-cli.md)başlangıcı ' nı izleyerek bir Anahtar Kasası oluşturun.

1. Uygulamanız için [sistem tarafından atanan bir yönetilen kimlik](overview-managed-identity.md) oluşturun.

   > [!NOTE] 
   > Key Vault başvurular Şu anda yalnızca sistem tarafından atanan yönetilen kimlikleri destekler. Kullanıcı tarafından atanan kimlikler kullanılamaz.

1. Daha önce oluşturduğunuz uygulama kimliği için [Key Vault bir erişim ilkesi](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) oluşturun. Bu ilkede "Get" gizli anahtarını etkinleştirin. "Yetkilendirilmiş uygulama" veya `applicationId` ayarlarını, yönetilen bir kimlikle uyumlu olmadığından yapılandırmayın.

    > [!NOTE]
    > Key Vault başvurular Şu anda [ağ kısıtlamalarına](../key-vault/key-vault-overview-vnet-service-endpoints.md)sahip bir anahtar kasasında depolanan gizli dizileri çözemeyebilir.

## <a name="reference-syntax"></a>Başvuru sözdizimi

Key Vault bir başvuru, `{referenceString}` aşağıdaki seçeneklerden biriyle değiştirildiği `@Microsoft.KeyVault({referenceString})`formundadır:

> [!div class="mx-tdBreakAll"]
> | Başvuru dizesi                                                            | Açıklama                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_Secreturi_                                                       | **Secreturi** , bir sürüm gibi Key Vault bir parolanın tam veri düzlemi URI 'si olmalıdır, örneğin, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName =_vaultname_; SecretName =_secretname_; SecretVersion =_Secretversion_ | **Vaultname** Key Vault kaynağınızın adı olmalıdır. **Secretname** , hedef parolanın adı olmalıdır. **Secretversion** , kullanılacak gizli dizinin sürümü olmalıdır. |

Örneğin, sürüme sahip bir tüm başvuru aşağıdaki gibi görünür:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Kopyalamaktır

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Key Vault kaynak uygulama ayarları

Key Vault başvuruları, [uygulama ayarları](configure-common.md#configure-app-settings)için değer olarak kullanılabilir, böylece gizli dizileri site yapılandırması yerine Key Vault tutabilirsiniz. Uygulama ayarları, bekleyen olarak güvenli bir şekilde şifrelenir, ancak gizli yönetim özelliklerine ihtiyacınız varsa, Key Vault gitmeleri gerekir.

Bir uygulama ayarı için Key Vault başvurusu kullanmak için, başvuruyu ayarın değeri olarak ayarlayın. Uygulamanız gizli dizi anahtarı aracılığıyla gizli dizi başvurusu yapabilir. Kod değişikliği gerekli değildir.

> [!TIP]
> Key Vault başvurularını kullanan uygulama ayarlarının çoğu, her ortam için ayrı kasaların olması gerektiği için yuva ayarları olarak işaretlenmelidir.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager dağıtımı

Kaynak dağıtımlarını Azure Resource Manager şablonları aracılığıyla otomatikleştirmede, bu özelliğin çalışmasını sağlamak için bağımlılıklarınızı belirli bir sırada sıraya almanız gerekebilir. Bu durumda, site tanımında `siteConfig` özelliği kullanmak yerine uygulama ayarlarınızı kendi kaynakları olarak tanımlamanız gerekir. Bunun nedeni, sitenin, sistem tarafından atanan kimliğin birlikte oluşturulması ve erişim ilkesinde kullanılabilmesi için önce tanımlanması gerekir.

Bir işlev uygulaması için örnek bir psuedo şablonu, aşağıdaki gibi görünebilir:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Bu örnekte, kaynak denetimi dağıtımı uygulama ayarlarına bağlıdır. Uygulama ayarı güncelleştirmesi zaman uyumsuz olarak davrandığı için bu durum normalde güvenli olmayan bir davranıştır. Ancak `WEBSITE_ENABLE_SYNC_UPDATE_SITE` uygulama ayarını eklediğimiz için güncelleştirme zaman uyumludur. Bu, kaynak denetimi dağıtımının yalnızca uygulama ayarları tamamen güncelleştirildikten sonra başlayacağı anlamına gelir.

## <a name="troubleshooting-key-vault-references"></a>Key Vault başvuruları sorunlarını giderme

Bir başvuru düzgün çözümlenmezse, bunun yerine başvuru değeri kullanılacaktır. Bu, uygulama ayarları için, değeri `@Microsoft.KeyVault(...)` sözdizimine sahip olan bir ortam değişkeni oluşturulacak anlamına gelir. Bu, belirli bir yapının gizli dizisi beklediği için uygulamanın hata oluşturmasına neden olabilir.

En yaygın olarak, bunun nedeni [Key Vault erişim ilkesinin](#granting-your-app-access-to-key-vault)yanlış yapılandırılmasından kaynaklanır. Bununla birlikte, aynı zamanda bir gizli dizi yok veya başvurunun kendisi bir sözdizimi hatası olabilir.

Sözdizimi doğruysa, portaldaki geçerli çözüm durumunu denetleyerek hata nedenlerini görüntüleyebilirsiniz. Uygulama ayarları ' na gidin ve söz konusu başvuru için "Düzenle" seçeneğini belirleyin. Ayar yapılandırmasının altında hatalar da dahil olmak üzere durum bilgilerini görmeniz gerekir. Bunun yokluğu, başvuru sözdiziminin geçersiz olduğunu gösterir.

Ek bilgi edinmek için yerleşik algılayıcıları da kullanabilirsiniz.

### <a name="using-the-detector-for-app-service"></a>App Service için algılayıcısının kullanımı

1. Portalda uygulamanıza gidin.
2. **Tanıla ve sorunları çöz '** ü seçin.
3. **Kullanılabilirlik ve performans** ' ı seçin ve **Web uygulaması** ' nı seçin.
4. **Uygulama ayarları tanılamayı Key Vault** bulun ve **daha fazla bilgi**'ye tıklayın.


### <a name="using-the-detector-for-azure-functions"></a>Azure Işlevleri için algılayıcısının kullanımı

1. Portalda uygulamanıza gidin.
2. **Platform özellikleri** ' ne gidin.
3. **Tanıla ve sorunları çöz '** ü seçin.
4. **Kullanılabilirlik ve performans** ' ı seçin ve **işlev uygulaması çalışmıyor veya raporlama hataları** ' nı seçin.
5. **Uygulama ayarları tanılamayı Key Vault** ' ye tıklayın.
