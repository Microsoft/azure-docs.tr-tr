---
title: Özel Azure Resource Manager rolü oluşturma ve hizmet sorumlusu 'na atama-Azure
description: Bu makalede, Azure CLı kullanarak IoT Edge üzerinde canlı video analizine yönelik özel Azure Resource Manager rol oluşturma ve hizmet sorumlusuna atama hakkında yönergeler sunulmaktadır.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 6c33f6703522fc0b28237e22c16c96587467df40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788520"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Özel Azure Resource Manager rolü oluşturma ve hizmet sorumlusuna atama

IoT Edge Module örneğindeki canlı video analizinin düzgün çalışması için etkin bir Azure Media Services hesabı gerekir. IoT Edge modülü ve Azure Media Service hesabı üzerindeki canlı video analizi arasındaki ilişki, bir dizi modül ikizi özelliği aracılığıyla oluşturulur. Bu ikizi özelliklerinden biri, modül örneğinin Media Services hesabında gerekli işlemleri tetiklemesine ve tetiklemesine olanak tanıyan bir [hizmet sorumlusundan](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) biridir. Uç cihazdan olası kötüye kullanımı ve/veya yanlışlıkla veri pozlamasını en aza indirmek için, bu hizmet sorumlusu en az ayrıcalığa sahip olmalıdır.

Bu makalede, bir hizmet sorumlusu oluşturmak için kullanılan Azure Cloud Shell olan özel Azure Resource Manager rol oluşturma adımları gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar  

Bu makaleye yönelik önkoşullar aşağıdaki gibidir:

* Sahip aboneliği olan Azure aboneliği.
* Bir uygulama oluşturma ve bir role hizmet sorumlusu atama ayrıcalıklarına sahip bir Azure Active Directory.

Hesabınızın yeterli izinlere sahip olup olmadığını denetlemenin en kolay yolu portalı kullanmaktır. Bkz. [Gerekli izinleri denetleme](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Genel Bakış  

Özel bir rol oluşturma ve bunu bir hizmet sorumlusu ile bağlama adımlarını aşağıdaki sırayla öğreneceksiniz:

1. Henüz bir medya hizmeti hesabı oluşturun.
1. Hizmet sorumlusu oluşturun.
1. Sınırlı ayrıcalıklara sahip özel bir Azure Resource Manager rolü oluşturun.
1. Oluşturulan özel rolü kullanarak hizmet sorumlusu ayrıcalıklarını kısıtla.
1. Hizmet sorumlusunu başarılı bir şekilde kısıtlayabiliyoruz olup olmadığını görmek için basit bir test çalıştırın.
1. IoT Edge dağıtım bildirimlerinde kullanılacak parametreleri yakalayın.

### <a name="create-a-media-services-account"></a>Media Services hesabı oluşturma  

Medya hizmeti hesabınız yoksa, bir tane oluşturmak için aşağıdaki adımları kullanın.

1. [Cloud Shell](https://shell.azure.com/)gidin.
1. Kabuk penceresinin sol tarafındaki açılan menüdeki ortamınız olarak "Bash" ı seçin

    ![Ekran başlıklarından, kabuk penceresinden Bash seçili gösterilmektedir.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Aşağıdaki komut şablonunu kullanarak Azure aboneliğinizi varsayılan hesap olarak ayarlayın:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Bir [kaynak grubu](/cli/azure/group#az_group_create) ve [depolama hesabı](/cli/azure/storage/account#az_storage_account_create)oluşturun.
1. Şimdi Cloud Shell ' de aşağıdaki komut şablonunu kullanarak bir Azure Media Service hesabı oluşturun:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Hizmet sorumlusu oluşturma  

Şimdi yeni bir hizmet sorumlusu oluşturacak ve bunu medya hizmeti hesabınıza bağlayacağız.

Herhangi bir kimlik doğrulama parametresi olmadan, hizmet sorumlusu için rastgele bir parolayla parola tabanlı kimlik doğrulaması kullanılır. Cloud Shell ' de, aşağıdaki komut şablonunu kullanın:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Bu komut şöyle bir yanıt üretir:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. Parola kimlik doğrulamasıyla bir hizmet sorumlusu çıkışı, bu örnekte "AadSecret" parametresi olan parola anahtarını içerir. 

    Bu değeri kopyalamadığınızdan emin olun; bu değer alınamaz. Parolayı unutursanız [hizmet sorumlusu kimlik bilgilerini sıfırlayın](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. AppID ve kiracı anahtarı çıktıda sırasıyla "Aadclitıd" ve "Aadtenantıd" olarak görünür. Hizmet sorumlusu kimlik doğrulamasında kullanılır. Değerlerini kaydedin, ancak [az ad SP listesi](/cli/azure/ad/sp#az_ad_sp_list)ile herhangi bir noktada elde edilebilir.

### <a name="create-a-custom-role-definition"></a>Özel bir rol tanımı oluştur  

Özel bir rol oluşturmak için aşağıdaki adımları izlemeniz gerekir:

1. Yerel sisteminizde bir rol tanımı JSON dosyası oluşturun ve aşağıdaki metni dosyaya kaydedin. 
    1. < Yoursubscriptionıd> Azure abonelik KIMLIĞINIZLE değiştirin
    1. Bu rol için izin verilen tek eylemler şunlardır:
        * listContainerSas – varlık içeriğini karşıya yüklemek ve indirmek için, modülün depolama kapsayıcısı URL 'Lerini paylaşılan erişim imzaları (SAS) ile listelemelerine yardımcı olur.
        * Varlık yazma – modülün herhangi bir varlık oluşturmasına veya güncelleştirmesine yardımcı olur
        * listEdgePolicies – Edge cihazına uygulanan ilkeleri listeler  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Oluşturulduktan sonra, abonelikte yeni rol tanımını oluşturmak için aşağıdaki komut şablonunu çalıştırın:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Komutun başarılı bir şekilde yürütülmesi sırasında aşağıdaki çıktıyı görürsünüz:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Rol ataması oluştur  

Rol ataması eklemek için, az önce oluşturduğunuz özel rolü atamak istediğiniz hizmet sorumlusu ObjectID 'ye ihtiyacınız olur.

ObjectID 'yi almak için Cloud Shell ' de aşağıdaki komutu kullanın:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` , [hizmet sorumlusu oluşturma](#create-service-principal) adımının çıktısından alınabilir.

Yukarıdaki komut, hizmet sorumlusu ObjectID 'yi yazdıracak. 

```
“objectId” : “<yourObjectId>”,
```

Özel rolü hizmet sorumlusu ile bağlamak için [az role atama Create komut şablonu komutunu](/cli/azure/role/assignment#az_role_assignment_create) kullanın:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametreler:

|Parametreler|Description| 
|---|---|
|--rol |Özel rol adı veya KIMLIĞI. Bizim örneğimizde: "LVAEdge kullanıcısı".|
|--atanan e-nesne kimliği|Kullanacağınız hizmet sorumlusunun nesne KIMLIĞI.|

Sonuç şöyle görünür:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Rol atamasının gerçekleşti olduğunu onaylayın

Hizmet sorumlusunun yeni oluşturduğumuz özel rolle bağlantılı olduğunu onaylamak için aşağıdaki komutu çalıştırın:

```
az role assignment list  --assignee < objectId>
```

Sonuç şöyle görünmelidir:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
"RoleDefinitionName" öğesini arayın ve değerinin "LVAEdge kullanıcısı" olarak ayarlandığını görün. 

Bu, Özel Kullanıcı rolünü uygulamamız için kullanılan hizmet sorumlusu ile bağladığımızdan onaylanır.

### <a name="test-the-service-principal-access-control"></a>Hizmet sorumlusu erişim denetimini test etme

1. Hizmet sorumlusunu kullanarak oturum açın. Bunun için, [hizmet sorumlusu oluşturma](#create-service-principal) adımının çıktısından alabileceğiniz uygun erişim belirtecini sağlamak üzere Azure Active Directory için 3 bilgi parçasına ihtiyacımız olacak:
    1. Aadclientıd 
    1. AadSecret
    1. Aadtenantıd
1. Şimdi, aşağıdaki komut şablonunu kullanarak oturum açmayı denemeye izin verir:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Şimdi, oturum açmanın başarısız olduğundan emin olmak için bir kaynak grubu oluşturmaya çalışan "LVAEdge kullanıcısı" rolüyle hizmet sorumlusu ile sınırlı olup olmadığını görelim. Cloud Shell ' de aşağıdaki komutu çalıştırın:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Bu komut başarısız olmalıdır ve şöyle görünür:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Sonraki adımlar  

Bu makaledeki aşağıdaki değerleri aklınızda edin. IoT Edge modülündeki canlı video analizinin ikizi özelliklerini yapılandırmanız için bu değerler gerekecektir, bkz. [module IKIZI JSON şeması](module-twin-configuration-schema.md).

| Bu makaledeki değişken|IoT Edge üzerinde canlı video analizi için ikizi Özellik adı|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|Aadtenantıd |  Aadtenantıd|
|Aadclientıd    |Aadserviceprincıpalappıd|
