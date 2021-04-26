---
title: Key Vault başvurularını kullanma
description: Azure App Service ve Azure Işlevlerini Azure Key Vault başvurularını kullanacak şekilde ayarlamayı öğrenin. Key Vault gizli dizileri uygulama kodunuz için kullanılabilir hale getirin.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 1e6f46b205790d81a3e76d2aafbcf7e13dbb5afd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815225"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>App Service ve Azure Işlevleri için Key Vault başvurularını kullanma

Bu konu başlığı altında, herhangi bir kod değişikliğine gerek kalmadan App Service veya Azure Işlevleri uygulamanızda Azure Key Vault gizliliklerle nasıl çalışılacağı gösterilmektedir. [Azure Key Vault](../key-vault/general/overview.md) , erişim ilkeleri ve denetim geçmişi üzerinde tam denetim ile merkezi gizli dizi yönetimi sağlayan bir hizmettir.

## <a name="granting-your-app-access-to-key-vault"></a>Uygulamanıza Key Vault erişim verme

Key Vault parolaları okumak için bir kasasının oluşturulmuş olması ve uygulamanıza erişim izni vermeniz gerekir.

1. [Key Vault hızlı](../key-vault/secrets/quick-create-cli.md)başlangıcı ' nı izleyerek bir Anahtar Kasası oluşturun.

1. Uygulamanız için [sistem tarafından atanan bir yönetilen kimlik](overview-managed-identity.md) oluşturun.

   > [!NOTE] 
   > Key Vault başvurular Şu anda yalnızca sistem tarafından atanan yönetilen kimlikleri destekler. Kullanıcı tarafından atanan kimlikler kullanılamaz.

1. Daha önce oluşturduğunuz uygulama kimliği için [Key Vault bir erişim ilkesi](../key-vault/general/security-features.md#privileged-access) oluşturun. Bu ilkede "Get" gizli anahtarını etkinleştirin. "Yetkilendirilmiş uygulama" veya `applicationId` ayarları, yönetilen bir kimlikle uyumlu olmadığından yapılandırmayın.

### <a name="access-network-restricted-vaults"></a>Ağ kısıtlamalı kasaları erişim

> [!NOTE]
> Uygulama bir [App Service ortamı](./environment/intro.md)barındırıdıkça, Linux tabanlı uygulamalar şu anda ağ kısıtlı anahtar kasasından gizli dizileri çözemeyebilir.

Kasanızda [ağ kısıtlamalarına](../key-vault/general/overview-vnet-service-endpoints.md)göre yapılandırıldıysa, uygulamanın ağ erişimi olduğundan da emin olmanız gerekir.

1. [App Service ağ özellikleri](./networking-features.md) ve [Azure işlevleri ağ seçenekleri](../azure-functions/functions-networking-options.md)' nde açıklandığı gibi, uygulamanın yapılandırılmış giden ağ özelliklerine sahip olduğundan emin olun.

2. Kasasının, uygulamanızın erişebileceği ağ veya alt ağ için yapılandırma hesaplarının olduğundan emin olun.

> [!IMPORTANT]
> Sanal ağ tümleştirmesiyle bir kasaya erişim, [belirtilen sürüm olmadan gizli anahtar güncelleştirmeleri için](#rotation)Şu anda uyumsuz.

## <a name="reference-syntax"></a>Başvuru sözdizimi

Key Vault bir başvuru, `@Microsoft.KeyVault({referenceString})` `{referenceString}` aşağıdaki seçeneklerden biri tarafından değiştirildiği biçimdedir:

> [!div class="mx-tdBreakAll"]
> | Başvuru dizesi                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_Secreturi_                                                       | **Secreturi** , isteğe bağlı olarak bir sürüm, örneğin, veya gibi Key Vault bir parolanın tam veri düzlemi URI 'si olmalıdır. `https://myvault.vault.azure.net/secrets/mysecret/``https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName =_vaultname_; SecretName =_secretname_; SecretVersion =_Secretversion_ | **Vaultname** gereklidir ve Key Vault kaynağınızın adı olmalıdır. **Secretname** gereklidir ve hedef gizli anahtar adı olmalıdır. **Secretversion** isteğe bağlıdır, ancak varsa, kullanılacak gizli dizinin sürümünü belirtir. |

Örneğin, bir bütün başvuru aşağıdaki gibi görünür:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Alternatif olarak:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Döndürme

> [!IMPORTANT]
> [Sanal ağ tümleştirmesiyle bir kasaya erişim](#access-network-restricted-vaults) , belirtilen sürüm olmadan gizli anahtar güncelleştirmeleri için şu anda uyumsuz.

Başvuru içinde bir sürüm belirtilmemişse, uygulama Key Vault mevcut olan en son sürümü kullanır. Bir döndürme olayı gibi daha yeni sürümler kullanılabilir olduğunda, uygulama otomatik olarak güncelleştirilecek ve bir gün içinde en son sürümü kullanmaya başlayacaktır. Uygulamada yapılan tüm yapılandırma değişiklikleri, tüm başvurulan parolaların en son sürümlerine anında güncelleştirme yapılmasına neden olur.

## <a name="source-application-settings-from-key-vault"></a>Key Vault kaynak uygulama ayarları

Key Vault başvuruları, [uygulama ayarları](configure-common.md#configure-app-settings)için değer olarak kullanılabilir, böylece gizli dizileri site yapılandırması yerine Key Vault tutabilirsiniz. Uygulama ayarları, bekleyen olarak güvenli bir şekilde şifrelenir, ancak gizli yönetim özelliklerine ihtiyacınız varsa, Key Vault gitmeleri gerekir.

Bir uygulama ayarı için Key Vault başvurusu kullanmak için, başvuruyu ayarın değeri olarak ayarlayın. Uygulamanız gizli dizi anahtarı aracılığıyla gizli dizi başvurusu yapabilir. Kod değişikliği gerekli değildir.

> [!TIP]
> Key Vault başvurularını kullanan uygulama ayarlarının çoğu, her ortam için ayrı kasaların olması gerektiği için yuva ayarları olarak işaretlenmelidir.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager dağıtımı

Kaynak dağıtımlarını Azure Resource Manager şablonları aracılığıyla otomatikleştirmede, bu özelliğin çalışmasını sağlamak için bağımlılıklarınızı belirli bir sırada sıraya almanız gerekebilir. , Site tanımında bir özellik kullanmak yerine uygulama ayarlarınızı kendi kaynakları olarak tanımlamanız gerekecektir `siteConfig` . Bunun nedeni, sitenin, sistem tarafından atanan kimliğin birlikte oluşturulması ve erişim ilkesinde kullanılabilmesi için önce tanımlanması gerekir.

Bir işlev uygulaması için örnek sözde şablon, aşağıdaki gibi görünebilir:

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
> Bu örnekte, kaynak denetimi dağıtımı uygulama ayarlarına bağlıdır. Uygulama ayarı güncelleştirmesi zaman uyumsuz olarak davrandığı için bu durum normalde güvenli olmayan bir davranıştır. Ancak, uygulama ayarını dahil ettiğimiz `WEBSITE_ENABLE_SYNC_UPDATE_SITE` için güncelleştirme zaman uyumludur. Bu, kaynak denetimi dağıtımının yalnızca uygulama ayarları tamamen güncelleştirildikten sonra başlayacağı anlamına gelir.

## <a name="troubleshooting-key-vault-references"></a>Key Vault başvuruları sorunlarını giderme

Bir başvuru düzgün çözümlenmezse, bunun yerine başvuru değeri kullanılacaktır. Bu, uygulama ayarları için değeri sözdizimine sahip bir ortam değişkeni oluşturulacak anlamına gelir `@Microsoft.KeyVault(...)` . Bu, belirli bir yapının gizli dizisi beklediği için uygulamanın hata oluşturmasına neden olabilir.

En yaygın olarak, bunun nedeni [Key Vault erişim ilkesinin](#granting-your-app-access-to-key-vault)yanlış yapılandırılmasından kaynaklanır. Bununla birlikte, aynı zamanda bir gizli dizi yok veya başvurunun kendisi bir sözdizimi hatası olabilir.

Sözdizimi doğruysa, portaldaki geçerli çözüm durumunu denetleyerek hata nedenlerini görüntüleyebilirsiniz. Uygulama ayarları ' na gidin ve söz konusu başvuru için "Düzenle" seçeneğini belirleyin. Ayar yapılandırmasının altında hatalar da dahil olmak üzere durum bilgilerini görmeniz gerekir. Bunun yokluğu, başvuru sözdiziminin geçersiz olduğunu gösterir.

Ek bilgi edinmek için yerleşik algılayıcıları da kullanabilirsiniz.

### <a name="using-the-detector-for-app-service"></a>App Service için algılayıcısının kullanımı

1. Portalda uygulamanıza gidin.
2. **Sorunları tanılama ve çözme**’yi seçin.
3. **Kullanılabilirlik ve performans** ' ı seçin ve **Web uygulaması** ' nı seçin.
4. **Uygulama ayarları tanılamayı Key Vault** bulun ve **daha fazla bilgi**'ye tıklayın.


### <a name="using-the-detector-for-azure-functions"></a>Azure Işlevleri için algılayıcısının kullanımı

1. Portalda uygulamanıza gidin.
2. **Platform özellikleri** ' ne gidin.
3. **Sorunları tanılama ve çözme**’yi seçin.
4. **Kullanılabilirlik ve performans** ' ı seçin ve **işlev uygulaması çalışmıyor veya raporlama hataları** ' nı seçin.
5. **Uygulama ayarları tanılamayı Key Vault** ' ye tıklayın.
