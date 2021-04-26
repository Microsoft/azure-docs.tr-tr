---
title: Bekleyen verileri şifrelemek için kendi anahtarınızı yapılandırın Azure Service Bus
description: Bu makalede, Azure Service Bus veri kalanını şifrelemek için kendi anahtarınızı yapılandırma hakkında bilgi verilmektedir.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: de716b9f14191ba057c83a060104e64937c4192a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816017"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Azure portal kullanarak geri kalan Azure Service Bus verileri şifrelemek için müşteri tarafından yönetilen anahtarları yapılandırın
Azure Service Bus Premium, Azure Depolama Hizmeti Şifrelemesi (Azure SSE) ile bekleyen verilerin şifrelenmesini sağlar. Service Bus Premium, verileri depolamak için Azure depolama kullanır. Azure depolama ile depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Kendi anahtarınızı (Kendi Anahtarını Getir (BYOK) veya müşteri tarafından yönetilen anahtar olarak da bilinir) kullanırsanız, veriler Microsoft tarafından yönetilen anahtar kullanılarak şifrelenir, ancak ek olarak, Microsoft tarafından yönetilen anahtar, müşteri tarafından yönetilen anahtar kullanılarak şifrelenir. Bu özellik, Microsoft tarafından yönetilen anahtarları şifrelemek için kullanılan müşteri tarafından yönetilen anahtarlara erişimi oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal etmenize olanak sağlar. BYOK özelliğinin etkinleştirilmesi, ad alanınız üzerinde bir kerelik kurulum işlemidir.

Hizmet tarafı şifrelemesi için müşterinin yönettiği anahtar için bazı uyarılar vardır. 
- Bu özellik [Azure Service Bus Premium](service-bus-premium-messaging.md) katmanı tarafından desteklenir. Standart katman Service Bus ad alanları için etkinleştirilemez.
- Şifreleme yalnızca yeni veya boş ad alanları için etkinleştirilebilir. Ad alanı herhangi bir kuyruk veya konu içeriyorsa, şifreleme işlemi başarısız olur.

Anahtarlarınızı yönetmek ve anahtar kullanımınızı denetlemek için Azure Key Vault kullanabilirsiniz. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md)

Bu makalede, Azure portal kullanarak, müşteri tarafından yönetilen anahtarlarla bir anahtar kasasının nasıl yapılandırılacağı gösterilmektedir. Azure portal kullanarak bir Anahtar Kasası oluşturmayı öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault oluşturma](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Azure Service Bus ile müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasının iki gerekli özelliği yapılandırılmış olmasını gerektirir. Bunlar:  **geçici silme** ve **Temizleme**. Azure portal yeni bir Anahtar Kasası oluşturduğunuzda, bu özellikler varsayılan olarak etkinleştirilir. Ancak, var olan bir anahtar kasasında bu özellikleri etkinleştirmeniz gerekiyorsa, PowerShell veya Azure CLı kullanmanız gerekir.

## <a name="enable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları etkinleştir
Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Service Bus Premium ad alanına gidin.
2. Service Bus ad alanının **Ayarlar** sayfasında **şifreleme**' yi seçin.
3. Aşağıdaki görüntüde gösterildiği gibi **geri kalan müşteri tarafından yönetilen anahtar şifrelemesini** seçin.

    ![Müşteri tarafından yönetilen anahtarı etkinleştirme](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Anahtarlar içeren bir Anahtar Kasası ayarlama

Müşteri tarafından yönetilen anahtarları etkinleştirdikten sonra, müşteri tarafından yönetilen anahtarı Azure Service Bus ad alanıyla ilişkilendirmeniz gerekir. Service Bus yalnızca Azure Key Vault destekler. Önceki bölümde, **müşteri tarafından yönetilen anahtar seçeneğiyle şifrelemeyi** etkinleştirirseniz, anahtarın Azure Key Vault içine aktarılması gerekir. Ayrıca, anahtarlar için **yumuşak silme** ve anahtar Için de **Temizleme** yapılandırması olmalıdır. Bu ayarlar, [PowerShell](../key-vault/general/key-vault-recovery.md) veya [CLI](../key-vault/general/key-vault-recovery.md)kullanılarak yapılandırılabilir.

1. Yeni bir Anahtar Kasası oluşturmak için Azure Key Vault [hızlı](../key-vault/general/overview.md)başlangıcı ' nı izleyin. Varolan anahtarları içeri aktarma hakkında daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](../key-vault/general/about-keys-secrets-certificates.md).
1. Bir kasa oluştururken hem geçici silme hem de Temizleme korumasını açmak için [az keykasa Create](/cli/azure/keyvault#az_keyvault_create) komutunu kullanın.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Var olan bir kasaya Temizleme koruması eklemek için (zaten geçici silme etkindir), [az keykasa Update](/cli/azure/keyvault#az_keyvault_update) komutunu kullanın.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Aşağıdaki adımları izleyerek anahtarlar oluşturun:
    1. Yeni bir anahtar oluşturmak için **Ayarlar** altındaki **anahtarlar** menüsünden **Oluştur/içeri aktar** ' ı seçin.
        
        ![Oluştur/Içeri Aktar düğmesini seçin](./media/configure-customer-managed-key/select-generate-import.png)

    1. Oluşturma **seçeneklerini** belirleyin  ve anahtara bir ad verin.

        ![Bir anahtar oluşturma](./media/configure-customer-managed-key/create-key.png) 

    1. Şimdi, açılan listeden şifrelemek için Service Bus ad alanıyla ilişkilendirmek üzere bu anahtarı seçebilirsiniz. 

        ![Anahtar kasasından anahtar seçin](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Artıklık için en fazla 3 anahtar ekleyebilirsiniz. Anahtarların birinin süresi dolduğunda veya erişilebilir durumda değilse, diğer anahtarlar şifreleme için kullanılacaktır.
        
    1. Anahtarın ayrıntılarını girin ve **Seç**' e tıklayın. Bu, Microsoft tarafından yönetilen anahtar şifrelemesini anahtarınızla (müşteri tarafından yönetilen anahtar) etkinleştirir. 


    > [!IMPORTANT]
    > Coğrafi olağanüstü durum kurtarma ile birlikte müşteri tarafından yönetilen anahtar kullanmak istiyorsanız lütfen bu bölümü gözden geçirin. 
    >
    > Müşteri tarafından yönetilen bir anahtarla Microsoft tarafından yönetilen anahtar şifrelemesini etkinleştirmek için, belirtilen Azure Keykasasında Service Bus ' yönetilen kimliği için bir [erişim ilkesi](../key-vault/general/security-features.md) ayarlanır. Bu, Azure Keykasasına Azure Service Bus ad alanından denetimli erişim sağlar.
    >
    > Bunun nedeni:
    > 
    >   * Service Bus ad alanı için [coğrafi olağanüstü durum kurtarma](service-bus-geo-dr.md) zaten etkinse ve müşterinin yönettiği anahtarı etkinleştirmek istiyorsanız, 
    >     * Eşleştirmeyi kes
    >     * Anahtar kasasında hem birincil hem de ikincil ad alanları için yönetilen kimliğin [erişim Ilkesini ayarlayın](../key-vault/general/assign-access-policy-portal.md) .
    >     * Birincil ad alanında şifrelemeyi ayarlayın.
    >     * Birincil ve ikincil ad alanlarını yeniden eşleştirin.
    > 
    >   * Müşteri tarafından yönetilen anahtarın zaten ayarlandığı bir Service Bus ad alanında coğrafi DR 'yi etkinleştirmek istiyorsanız-
    >     * İkincil ad alanı için yönetilen kimlik için [erişim ilkesini](../key-vault/general/assign-access-policy-portal.md) anahtar kasasında ayarlayın.
    >     * Birincil ve ikincil ad alanlarını eşleştirin.


## <a name="rotate-your-encryption-keys"></a>Şifreleme anahtarlarınızı döndürün

Anahtarı anahtar kasasında Azure Anahtar Kasası döndürme mekanizmasını kullanarak döndürebilirsiniz. Etkinleştirme ve sona erme tarihleri, anahtar döndürmeyi otomatik hale getirmek için de ayarlanabilir. Service Bus hizmet yeni anahtar sürümlerini algılar ve otomatik olarak kullanmaya başlar.

## <a name="revoke-access-to-keys"></a>Anahtarlara erişimi iptal et

Şifreleme anahtarlarına erişimin iptal edilmemesi Service Bus verileri temizleyemezsiniz. Ancak, verilere Service Bus ad alanından erişilemez. Şifreleme anahtarını erişim ilkesi veya anahtarı silerek iptal edebilirsiniz. Erişim ilkeleri hakkında daha fazla bilgi edinin ve anahtar kasasının güvenliğini [güvenli bir şekilde bir anahtar kasasına erişin](../key-vault/general/security-features.md).

Şifreleme anahtarı iptal edildiğinde, şifrelenen ad alanındaki Service Bus hizmeti çalışamaz hale gelir. Anahtara erişim etkinleştirilirse veya silinen anahtar geri yüklenirse, şifrelenmiş Service Bus ad alanındaki verilere erişebilmek için Service Bus hizmet anahtarı seçer.

## <a name="caching-of-keys"></a>Anahtarların önbelleğe alınması
Service Bus örnek, listelenen şifreleme anahtarlarını her 5 dakikada bir yoklar. Bu, 5 dakika sonra olan sonraki yoklamaya kadar önbelleğe alır ve bunları kullanır. En az bir anahtar kullanılabildiği sürece kuyruklar ve konular erişilebilir olur. Yokladığı sırada listelenen tüm anahtarlara erişilemezse, tüm kuyruklar ve konular kullanılamaz hale gelir. 

Daha fazla ayrıntı aşağıdadır: 

- Her 5 dakikada bir Service Bus hizmeti, ad alanının kaydında listelenen tüm müşteri tarafından yönetilen anahtarları yoklar:
    - Bir anahtar döndürülürse, kayıt yeni anahtarla güncellenir.
    - Bir anahtar iptal edildiyse, anahtar kayıttan kaldırılır.
    - Tüm anahtarlar iptal edildiyse, ad alanının şifreleme durumu **iptal** olarak ayarlanır. Service Bus ad alanından verilere erişilemez. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Şifrelemeyi etkinleştirmek için Kaynak Yöneticisi şablonu kullanma
Bu bölümde **Azure Resource Manager şablonlar** kullanılarak aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir. 

1. **Yönetilen hizmet kimliğiyle** **Premium** Service Bus ad alanı oluşturun.
2. **Anahtar Kasası** oluşturun ve anahtar kasasına hizmet kimliği erişimi verin. 
3. Service Bus ad alanını Anahtar Kasası bilgileriyle güncelleştirin (anahtar/değer). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Yönetilen hizmet kimliğiyle Premium Service Bus ad alanı oluşturma
Bu bölümde, bir Azure Resource Manager şablonu ve PowerShell kullanarak yönetilen hizmet kimliğiyle bir Azure Service Bus ad alanı oluşturma gösterilmektedir. 

1. Yönetilen hizmet kimliğiyle Service Bus Premium katman ad alanı oluşturmak için bir Azure Resource Manager şablonu oluşturun. Dosyayı şu şekilde adlandırın: **CreateServiceBusPremiumNamespace.js**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. **ÜzerindeCreateServiceBusPremiumNamespaceParams.js** adlı bir şablon parametre dosyası oluşturun. 

    > [!NOTE]
    > Aşağıdaki değerleri değiştirin: 
    > - `<ServiceBusNamespaceName>` -Service Bus ad alanının adı
    > - `<Location>` -Service Bus ad alanınız konumu

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Bir Premium Service Bus ad alanı oluşturmak üzere şablonu dağıtmak için aşağıdaki PowerShell komutunu çalıştırın. Ardından, daha sonra kullanmak üzere Service Bus ad alanının KIMLIĞINI alın. `{MyRG}`Komutu çalıştırmadan önce kaynak grubunun adıyla değiştirin.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Anahtar kasasına Service Bus ad alanı kimliği erişimi verme

1. **Temizleme koruması** ve **geçici silme** etkin olan bir Anahtar Kasası oluşturmak için aşağıdaki komutu çalıştırın. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    VEYA
    
    **Mevcut bir anahtar kasasını** güncelleştirmek için aşağıdaki komutu çalıştırın. Komutu çalıştırmadan önce kaynak grubu ve Anahtar Kasası adları için değerler belirtin. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Service Bus ad alanındaki yönetilen kimliğin anahtar kasasındaki anahtar değere erişebilmesi için Anahtar Kasası erişim ilkesini ayarlayın. Önceki bölümde Service Bus ad alanının KIMLIĞINI kullanın. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Anahtar kasasından müşteri tarafından yönetilen anahtarla Service Bus ad alanındaki verileri şifreleme
Şu ana kadar şu adımları tamamladınız: 

1. Yönetilen kimliğe sahip bir Premium ad alanı oluşturuldu.
2. Anahtar Kasası oluşturun ve anahtar kasasında yönetilen kimlik erişimi izni verilir. 

Bu adımda, Service Bus ad alanını Anahtar Kasası bilgileriyle güncelleirsiniz. 

1. Aşağıdaki içerikle **UpdateServiceBusNamespaceWithEncryption.js** ADLı bir JSON dosyası oluşturun: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Şablon parametre dosyası oluşturma: **üzerindeUpdateServiceBusNamespaceWithEncryptionParams.js**.

    > [!NOTE]
    > Aşağıdaki değerleri değiştirin: 
    > - `<ServiceBusNamespaceName>` -Service Bus ad alanının adı
    > - `<Location>` -Service Bus ad alanınız konumu
    > - `<KeyVaultName>` -Anahtar kasanızın adı
    > - `<KeyName>` -Anahtar kasasındaki anahtarın adı  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Kaynak Yöneticisi şablonunu dağıtmak için aşağıdaki PowerShell komutunu çalıştırın. `{MyRG}`Komutu çalıştırmadan önce kaynak grubunuzun adıyla değiştirin. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:
- [Service Bus genel bakış](service-bus-messaging-overview.md)
- [Key Vault genel bakış](../key-vault/general/overview.md)