---
title: Azure Event Hubs verilerini bekleyen bir şekilde şifrelemek için kendi anahtarınızı yapılandırın
description: Bu makalede, Azure Event Hubs Data Rest 'i şifrelemek için kendi anahtarınızı yapılandırma hakkında bilgi verilmektedir.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 33587812121051d93aa8b939c3df70530ba65c5e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812453"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Azure Event Hubs verilerini Rest 'te şifrelemek için müşteri tarafından yönetilen anahtarları Azure portal kullanarak yapılandırın
Azure Event Hubs, Azure Depolama Hizmeti Şifrelemesi (Azure SSE) ile bekleyen verilerin şifrelenmesini sağlar. Event Hubs hizmeti, verileri depolamak için Azure Storage 'ı kullanır. Azure depolama ile depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Kendi anahtarınızı (Kendi Anahtarını Getir (BYOK) veya müşteri tarafından yönetilen anahtar olarak da bilinir) kullanırsanız, veriler Microsoft tarafından yönetilen anahtar kullanılarak şifrelenir, ancak ek olarak, Microsoft tarafından yönetilen anahtar, müşteri tarafından yönetilen anahtar kullanılarak şifrelenir. Bu özellik, Microsoft tarafından yönetilen anahtarları şifrelemek için kullanılan müşteri tarafından yönetilen anahtarlara erişimi oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal etmenize olanak sağlar. BYOK özelliğinin etkinleştirilmesi, ad alanınız üzerinde bir kerelik kurulum işlemidir.

> [!NOTE]
> - BYOK özelliği [Event Hubs adanmış tek kiracılı](event-hubs-dedicated-overview.md) kümeler tarafından desteklenir. Standart Event Hubs ad alanları için etkinleştirilemez.
> - Şifreleme yalnızca yeni veya boş ad alanları için etkinleştirilebilir. Ad alanı olay hub 'ları içeriyorsa, şifreleme işlemi başarısız olur.

Anahtarlarınızı yönetmek ve anahtar kullanımınızı denetlemek için Azure Key Vault kullanabilirsiniz. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md)

Bu makalede, Azure portal kullanarak, müşteri tarafından yönetilen anahtarlarla bir anahtar kasasının nasıl yapılandırılacağı gösterilmektedir. Azure portal kullanarak bir Anahtar Kasası oluşturmayı öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault oluşturma](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Azure Event Hubs ile müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasının iki gerekli özelliği yapılandırılmış olmasını gerektirir. Bunlar:  **geçici silme** ve **Temizleme**. Azure portal yeni bir Anahtar Kasası oluşturduğunuzda, bu özellikler varsayılan olarak etkinleştirilir. Ancak, var olan bir anahtar kasasında bu özellikleri etkinleştirmeniz gerekiyorsa, PowerShell veya Azure CLı kullanmanız gerekir.

## <a name="enable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları etkinleştir
Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Event Hubs Ayrılmış kümenize gidin.
1. BYOK ' u etkinleştirmek istediğiniz ad alanını seçin.
1. Event Hubs ad alanının **Ayarlar** sayfasında **şifreleme**' yi seçin. 
1. Aşağıdaki görüntüde gösterildiği gibi **geri kalan müşteri tarafından yönetilen anahtar şifrelemesini** seçin. 

    ![Müşteri tarafından yönetilen anahtarı etkinleştirme](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Anahtarlar içeren bir Anahtar Kasası ayarlama
Müşteri tarafından yönetilen anahtarları etkinleştirdikten sonra, müşteri tarafından yönetilen anahtarı Azure Event Hubs ad alanınız ile ilişkilendirmeniz gerekir. Event Hubs yalnızca Azure Key Vault destekler. Önceki bölümde, **müşteri tarafından yönetilen anahtar seçeneğiyle şifrelemeyi** etkinleştirirseniz, anahtarın Azure Key Vault içine aktarılması gerekir. Ayrıca, anahtarlar için **yumuşak silme** ve anahtar Için de **Temizleme** yapılandırması olmalıdır. Bu ayarlar, [PowerShell](../key-vault/general/key-vault-recovery.md) veya [CLI](../key-vault/general/key-vault-recovery.md)kullanılarak yapılandırılabilir.

1. Yeni bir Anahtar Kasası oluşturmak için Azure Key Vault [hızlı](../key-vault/general/overview.md)başlangıcı ' nı izleyin. Varolan anahtarları içeri aktarma hakkında daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](../key-vault/general/about-keys-secrets-certificates.md).
1. Bir kasa oluştururken hem geçici silme hem de Temizleme korumasını açmak için [az keykasa Create](/cli/azure/keyvault#az_keyvault_create) komutunu kullanın.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Var olan bir kasaya Temizleme koruması eklemek için (zaten geçici silme etkindir), [az keykasa Update](/cli/azure/keyvault#az_keyvault_update) komutunu kullanın.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Aşağıdaki adımları izleyerek anahtarlar oluşturun:
    1. Yeni bir anahtar oluşturmak için **Ayarlar** altındaki **anahtarlar** menüsünden **Oluştur/içeri aktar** ' ı seçin.
        
        ![Oluştur/Içeri Aktar düğmesini seçin](./media/configure-customer-managed-key/select-generate-import.png)
    1. Oluşturma **seçeneklerini** belirleyin  ve anahtara bir ad verin.

        ![Bir anahtar oluşturma](./media/configure-customer-managed-key/create-key.png) 
    1. Şimdi, açılan listeden şifrelemek için Event Hubs ad alanıyla ilişkilendirmek üzere bu anahtarı seçebilirsiniz. 

        ![Anahtar kasasından anahtar seçin](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Anahtarın ayrıntılarını girin ve **Seç**' e tıklayın. Bu, Microsoft tarafından yönetilen anahtar şifrelemesini anahtarınızla (müşteri tarafından yönetilen anahtar) etkinleştirir. 


## <a name="rotate-your-encryption-keys"></a>Şifreleme anahtarlarınızı döndürün
Anahtarı anahtar kasasında Azure Anahtar Kasası döndürme mekanizmasını kullanarak döndürebilirsiniz. Etkinleştirme ve sona erme tarihleri, anahtar döndürmeyi otomatik hale getirmek için de ayarlanabilir. Event Hubs hizmet yeni anahtar sürümlerini algılar ve otomatik olarak kullanmaya başlar.

## <a name="revoke-access-to-keys"></a>Anahtarlara erişimi iptal et
Şifreleme anahtarlarına erişimin iptal edilmemesi Event Hubs verileri temizleyemezsiniz. Ancak, verilere Event Hubs ad alanından erişilemez. Şifreleme anahtarını erişim ilkesi veya anahtarı silerek iptal edebilirsiniz. Erişim ilkeleri hakkında daha fazla bilgi edinin ve anahtar kasasının güvenliğini [güvenli bir şekilde bir anahtar kasasına erişin](../key-vault/general/security-features.md).

Şifreleme anahtarı iptal edildiğinde, şifrelenen ad alanındaki Event Hubs hizmeti çalışamaz hale gelir. Anahtara erişim etkinleştirilirse veya silme anahtarı geri yüklenirse, şifrelenmiş Event Hubs ad alanındaki verilere erişebilmek için Event Hubs hizmet anahtarı seçer.

## <a name="set-up-diagnostic-logs"></a>Tanılama günlükleri ayarlama 
BYOK etkinleştirilmiş ad alanları için tanılama günlüklerini ayarlamak, işlemler hakkında gerekli bilgileri sağlar. Bu Günlükler etkinleştirilebilir ve daha sonra bir olay hub 'ına bağlanabilir veya Log Analytics aracılığıyla analiz edilebilir ya da özelleştirilmiş analizler gerçekleştirmek için depolama alanına akışı yapılabilir. Tanılama günlükleri hakkında daha fazla bilgi edinmek için bkz. [Azure tanılama günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Kullanıcı günlüklerini etkinleştir
Müşteri tarafından yönetilen anahtarlar için günlükleri etkinleştirmek üzere bu adımları izleyin.

1. Azure portal, BYOK etkinleştirilmiş olan ad alanına gidin.
1. Izleme altında **Tanılama ayarları** ' **nı** seçin.

    ![Tanılama ayarlarını seçin](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. **+ Tanılama ayarı Ekle**' yi seçin. 

    ![Tanılama ayarı Ekle 'yi seçin](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Bir **ad** girin ve günlüklerin akışını istediğiniz yeri seçin.
1. **Customermanagedkeyuserlogs** ve **Kaydet**' i seçin. Bu eylem, ad alanı üzerinde BYOK için günlüklere izin vermez.

    ![Müşteri tarafından yönetilen anahtar Kullanıcı günlükleri seçeneğini belirleyin](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Günlük şeması 
Tüm Günlükler JavaScript Nesne Gösterimi (JSON) biçiminde depolanır. Her girdinin aşağıdaki tabloda açıklanan biçimi kullanan dize alanları vardır. 

| Ad | Açıklama |
| ---- | ----------- | 
| Silinecek | Başarısız olan görevin açıklaması. |
| ActivityId | İzleme için kullanılan iç KIMLIK. |
| category | Görevin sınıflandırmasını tanımlar. Örneğin, anahtar kasanızın anahtarı devre dışı bırakılmışsa, bir bilgi kategorisi olur veya bir anahtarın sarmalanmamış olması durumunda hataya neden olabilir. |
| resourceId | Azure Resource Manager kaynak KIMLIĞI |
| keyVault | Anahtar kasasının tam adı. |
| anahtar | Event Hubs ad alanını şifrelemek için kullanılan anahtar adı. |
| sürüm | Kullanılan anahtarın sürümü. |
| operation | Anahtar kasasındaki anahtarda gerçekleştirilen işlem. Örneğin, anahtarı devre dışı bırakma/etkinleştirme, sarmalama veya kaydırmayı kaldırma |
| kod | İşlemle ilişkili kod. Örnek: hata kodu, 404, anahtarın bulunamadığı anlamına gelir. |
| message | İşlemle ilişkili herhangi bir hata iletisi |

Müşteri tarafından yönetilen anahtar için günlüğe bir örnek aşağıda verilmiştir:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Şifrelemeyi etkinleştirmek için Kaynak Yöneticisi şablonu kullanma
Bu bölümde **Azure Resource Manager şablonlar** kullanılarak aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir. 

1. Yönetilen hizmet kimliğiyle bir **Event Hubs ad alanı** oluşturun.
2. **Anahtar Kasası** oluşturun ve anahtar kasasına hizmet kimliği erişimi verin. 
3. Event Hubs ad alanını Anahtar Kasası bilgileriyle güncelleştirin (anahtar/değer). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Yönetilen hizmet kimliğiyle bir Event Hubs kümesi ve ad alanı oluşturma
Bu bölümde, bir Azure Resource Manager şablonu ve PowerShell kullanarak yönetilen hizmet kimliğiyle Azure Event Hubs ad alanı oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir. 

1. Yönetilen hizmet kimliğiyle bir Event Hubs ad alanı oluşturmak için Azure Resource Manager şablonu oluşturun. Dosyayı şu şekilde adlandırın: **CreateEventHubClusterAndNamespace.js**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. **ÜzerindeCreateEventHubClusterAndNamespaceParams.js** adlı bir şablon parametre dosyası oluşturun. 

    > [!NOTE]
    > Aşağıdaki değerleri değiştirin: 
    > - `<EventHubsClusterName>` -Event Hubs Kümenizin adı    
    > - `<EventHubsNamespaceName>` -Event Hubs ad alanının adı
    > - `<Location>` -Event Hubs ad alanınız konumu

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Event Hubs bir ad alanı oluşturmak üzere şablonu dağıtmak için aşağıdaki PowerShell komutunu çalıştırın. Ardından, daha sonra kullanmak üzere Event Hubs ad alanının KIMLIĞINI alın. `{MyRG}`Komutu çalıştırmadan önce kaynak grubunun adıyla değiştirin.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Anahtar kasasına Event Hubs ad alanı kimliği erişimi verme

1. **Temizleme koruması** ve **geçici silme** etkin olan bir Anahtar Kasası oluşturmak için aşağıdaki komutu çalıştırın. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    VEYA    
    
    **Mevcut bir anahtar kasasını** güncelleştirmek için aşağıdaki komutu çalıştırın. Komutu çalıştırmadan önce kaynak grubu ve Anahtar Kasası adları için değerler belirtin. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Event Hubs ad alanındaki yönetilen kimliğin anahtar kasasındaki anahtar değere erişebilmesi için Anahtar Kasası erişim ilkesini ayarlayın. Önceki bölümde Event Hubs ad alanının KIMLIĞINI kullanın. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Anahtar kasasından müşteri tarafından yönetilen anahtarla Event Hubs ad alanındaki verileri şifreleme
Şu ana kadar şu adımları tamamladınız: 

1. Yönetilen kimliğe sahip bir Premium ad alanı oluşturuldu.
2. Anahtar Kasası oluşturun ve anahtar kasasında yönetilen kimlik erişimi izni verilir. 

Bu adımda, Event Hubs ad alanını Anahtar Kasası bilgileriyle güncelleirsiniz. 

1. Aşağıdaki içerikle **CreateEventHubClusterAndNamespace.js** ADLı bir JSON dosyası oluşturun: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Şablon parametre dosyası oluşturma: **üzerindeUpdateEventHubClusterAndNamespaceParams.js**. 

    > [!NOTE]
    > Aşağıdaki değerleri değiştirin: 
    > - `<EventHubsClusterName>` -Event Hubs Kümenizin adı.        
    > - `<EventHubsNamespaceName>` -Event Hubs ad alanının adı
    > - `<Location>` -Event Hubs ad alanınız konumu
    > - `<KeyVaultName>` -Anahtar kasanızın adı
    > - `<KeyName>` -Anahtar kasasındaki anahtarın adı

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Sorun giderme
En iyi uygulama olarak, önceki bölümde gösterildiği gibi günlükleri her zaman etkinleştirin. BYOK şifrelemesi etkinleştirildiğinde etkinlikleri izlemeye yardımcı olur. Ayrıca sorunların kapsamını belirlemek için de yardımcı olur.

Aşağıda, BYOK şifrelemesi etkinleştirildiğinde aranacak ortak hatalar kodları verilmiştir.

| Eylem | Hata kodu | Verilerin sonuç durumu |
| ------ | ---------- | ----------------------- | 
| Anahtar kasasından kaydırmayı/kaydırmayı kaldırma iznini kaldır | 403 |    Erişilemez |
| Bir AAD sorumlusunun sarmalama/sarmalama izni verilen AAD rolü üyeliğini kaldırma | 403 |  Erişilemez |
| Anahtar kasasından bir şifreleme anahtarı silme | 404 | Erişilemez |
| Anahtar kasasını silme | 404 | Erişilebilir değil (gerekli bir ayar olan, geçici silme özelliğinin etkin olduğunu varsayar.) |
| Şifreleme anahtarındaki süre sonu süresini, zaten süresi dolduğu için değiştirme | 403 |   Erişilemez  |
| Bu anahtar şifreleme anahtarı etkin olmayan NBF (daha önce değil) değiştiriliyor | 403 | Erişilemez  |
| Anahtar Kasası güvenlik duvarı için **MSFT hizmetlerine Izin ver** seçeneğini belirleme veya başka bir şekilde şifreleme anahtarına sahip anahtar kasasına ağ erişimini engelleme | 403 | Erişilemez |
| Anahtar kasasını farklı bir kiracıya taşıma | 404 | Erişilemez |  
| Aralıklı ağ sorunu veya DNS/AAD/MSI kesintisi |  | Önbelleğe alınmış veri şifreleme anahtarı kullanılarak erişilebilir |

> [!IMPORTANT]
> BYOK şifrelemesini kullanan bir ad alanında coğrafi DR 'yi etkinleştirmek için, eşleştirme için ikincil ad alanı adanmış bir kümede olmalıdır ve üzerinde sistem tarafından atanmış bir yönetilen kimlik etkin olmalıdır. Daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:
- [Event Hubs genel bakış](event-hubs-about.md)
- [Key Vault genel bakış](../key-vault/general/overview.md)