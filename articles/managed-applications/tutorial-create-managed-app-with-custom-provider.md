---
title: Azure yönetilen uygulama özel eylemlerle & kaynakları
description: Bu öğreticide, Azure özel sağlayıcısı ile Azure yönetilen uygulamasının nasıl oluşturulacağı açıklanmaktadır.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: f70bb768dae3de80f85ffc49558b9ef51d2fce49
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529210"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Öğretici: özel eylemler ve kaynaklarla yönetilen uygulama oluşturma

Bu öğreticide, özel eylemler ve kaynaklarla kendi yönetilen uygulamanızı oluşturursunuz. Yönetilen uygulama `Overview` sayfasında özel bir eylem içerir, özel kaynak türü, `Table of Content` ayrı bir menü öğesi olarak görüntülenir ve özel kaynak sayfasında özel bir bağlam eylemi olur.

Bu öğretici aşağıdaki adımları içerir:

> [!div class="checklist"]
> * Yönetilen uygulama örneği oluşturmak için Kullanıcı arabirimi tanım dosyası yaz
> * Azure özel sağlayıcısı, Azure depolama hesabı ve Azure Işlevi ile dağıtım şablonu yazma
> * Özel eylemler ve kaynaklarla görünüm tanımı yapıtı yazma
> * Yönetilen uygulama tanımını dağıtma
> * Yönetilen uygulamanın bir örneğini dağıtma
> * Özel eylemler gerçekleştirme ve özel kaynaklar oluşturma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için şunları bilmeniz gerekir:

* [Yönetilen uygulama tanımı oluşturma ve yayımlama](publish-service-catalog-app.md).
* [Azure Portal aracılığıyla Service Catalog uygulaması dağıtma](deploy-service-catalog-quickstart.md).
* [Yönetilen uygulamanız için Azure Portal Kullanıcı arabirimi oluşturma](create-uidefinition-overview.md).
* [Tanım yapıtı yeteneklerini görüntüleyin](concepts-view-definition.md) .
* [Azure özel sağlayıcı](custom-providers-overview.md) özellikleri.

## <a name="user-interface-definition"></a>Kullanıcı arabirimi tanımı

Bu öğreticide, yönetilen bir uygulama oluşturursunuz ve yönetilen kaynak grubu özel sağlayıcı örneği, depolama hesabı ve işlev içerecektir. Bu örnekte kullanılan Azure Işlevi, Eylemler ve kaynaklar için özel sağlayıcı işlemlerini işleyen bir API uygular. Azure depolama hesabı, özel sağlayıcı kaynaklarınız için temel depolama alanı olarak kullanılır.

Yönetilen uygulama örneği oluşturmak için Kullanıcı arabirimi tanımı `funcname` ve `storagename` giriş öğelerini içerir. Depolama hesabı adı ve işlev adı genel olarak benzersiz olmalıdır. Varsayılan olarak, işlev dosyaları [örnek işlev paketinden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)dağıtılır, ancak *createuıdefinition. JSON*içindeki bir paket bağlantısı için bir giriş öğesi ekleyerek bunu değiştirebilirsiniz:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

*Createuıdefinition. JSON*içinde çıktı:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Tüm *Createuıdefinition. JSON* örneği, [Başvuru: Kullanıcı arabirimi öğeleri yapıtlarına başvurabilir](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Özel sağlayıcı içeren şablon

Özel sağlayıcıyla yönetilen bir uygulama örneği oluşturmak için, **genel** adına sahip özel sağlayıcı kaynağı tanımlamanız ve **maintemplate. JSON**' da **Microsoft. customproviders/resourceproviders** yazmanız gerekir. Bu kaynakta, hizmetinize yönelik kaynak türlerini ve eylemleri tanımlarsınız. Azure Işlevi ve Azure depolama hesabı örnekleri dağıtmak için sırasıyla `Microsoft.Web/sites` ve `Microsoft.Storage/storageAccounts` türündeki kaynakları tanımlayın.

Bu öğreticide, bir `users` kaynak türü, `ping` özel eylem ve `users` özel bir kaynak bağlamında gerçekleştirilecek özel eylem `users/contextAction` oluşturacaksınız. Her kaynak türü ve eylemi için [Createuıdefinition. JSON](#user-interface-definition)içinde belirtilen ada sahip işleve işaret eden bir uç nokta sağlayın. Kaynak türleri için `Proxy,Cache` olarak **Routingtype** ve eylemler için `Proxy` belirtin:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Tüm *Maintemplate. JSON* örneği, [Başvuru: dağıtım şablonu yapıtında](reference-main-template-artifact.md)bulunabilir.

## <a name="view-definition-artifact"></a>Tanım yapıtını görüntüleme

Yönetilen uygulamanızda özel eylemleri ve özel kaynakları içeren Kullanıcı arabirimini tanımlamak için **ViewDefinition. JSON** yapıtı yazmak gerekir. Görünüm tanımı yapıtı hakkında daha fazla bilgi için bkz. [Azure yönetilen uygulamalar 'da tanım yapıtı görüntüleme](concepts-view-definition.md).

Bu öğreticide şunları tanımlarsınız:
* Temel metin girişi ile `TestAction` özel bir eylemi temsil eden araç çubuğu düğmesi içeren bir *genel bakış* sayfası.
* Özel bir kaynak türünü temsil eden bir *Kullanıcılar* sayfası `users`.
* *Kullanıcılar* sayfasında, `users` türünde özel kaynak bağlamında gerçekleştirilecek özel bir kaynak eylemi `users/contextAction`.

Aşağıdaki örnek, "genel bakış" sayfası için görünüm yapılandırmasını gösterir:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Aşağıdaki örnekte, "kullanıcılar" kaynakları sayfa yapılandırması özel kaynak eylemiyle birlikte verilmiştir:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Tüm *ViewDefinition. JSON* örneği, [Başvuru: View definition yapay](reference-view-definition-artifact.md)öğesinde bulunabilir.

## <a name="managed-application-definition"></a>Yönetilen uygulama tanımı

Aşağıdaki yönetilen uygulama yapılarını zip arşivine paketleyin ve depolama birimine yükleyin:

* Createuıdefinition. JSON
* mainTemplate. JSON
* viewDefinition. JSON

Tüm dosyalar kök düzeyinde olmalıdır. Yapıtları olan paket, GitHub Blobu veya Azure depolama hesabı blobu gibi herhangi bir depolama alanında depolanabilir. Uygulama paketini depolama hesabına yüklemek için bir komut dosyası aşağıda verilmiştir: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Aşağıdaki Azure CLı betiğini çalıştırın veya hizmet kataloğu yönetilen uygulama tanımını dağıtmak için Azure portal içindeki adımları izleyin:

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Azure portalda **Tüm hizmetler**’i seçin. Kaynak listesinde **yönetilen uygulamalar merkezi**' ni yazın ve seçin.
2. **Yönetilen uygulamalar merkezi**'Nde **Hizmet kataloğu uygulama tanımı** ' nı seçin ve **Ekle**' ye tıklayın. 
    
    ![Hizmet kataloğu Ekle](./media/managed-application-with-custom-providers/service-catalog-managed-application.png)

3. Hizmet kataloğu tanımı oluşturmak için değerleri girin:

    * Hizmet kataloğu tanımı, **görünen ad** ve *Açıklama*(isteğe bağlı) için benzersiz bir **ad** sağlayın.
    * Uygulama tanımının oluşturulacağı **aboneliği**, **kaynak grubunu**ve **konumu** seçin. ZIP paketi için kullanılan aynı kaynak grubunu kullanabilir veya yeni bir kaynak grubu oluşturabilirsiniz.
    * Bir **paket dosyası URI 'si**için, önceki adımda oluşturduğunuz zip dosyasının yolunu belirtin.

    ![Değer sağla](./media/managed-application-with-custom-providers/add-service-catalog-managed-application.png)

4. Kimlik doğrulama ve kilit düzeyi bölümüne geldiğinizde, **Yetkilendirme Ekle**' yi seçin.

    ![Yetkilendirme Ekle](./media/managed-application-with-custom-providers/add-authorization.png)

5. Kaynakları yönetmek için bir Azure Active Directory grubu seçin ve **Tamam**' ı seçin.

   ![Yetkilendirme grubu Ekle](./media/managed-application-with-custom-providers/add-auth-group.png)

6. Tüm değerleri sağladıysanız **Oluştur**' u seçin.

   ![Yönetilen uygulama tanımı oluştur](./media/managed-application-with-custom-providers/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Yönetilen uygulama örneği

Yönetilen uygulama tanımı dağıtıldığında aşağıdaki betiği çalıştırın veya yönetilen uygulama örneğinizi özel sağlayıcıyla dağıtmak için Azure portal bölümündeki adımları uygulayın:

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Azure portalda **Tüm hizmetler**’i seçin. Kaynak listesinde **yönetilen uygulamalar merkezi**' ni yazın ve seçin.
2. **Yönetilen uygulamalar merkezi**'Nde **Hizmet kataloğu uygulamaları** ' nı seçin ve **Ekle**' ye tıklayın. 

    ![Yönetilen uygulama ekle](./media/managed-application-with-custom-providers/add-managed-application.png)

3. **Hizmet kataloğu uygulamaları** sayfasında, arama kutusuna hizmet kataloğu tanımı görünen adı yazın. Önceki adımda oluşturulan tanımı seçin ve **Oluştur**' a tıklayın.

    ![Hizmet kataloğu seçme](./media/managed-application-with-custom-providers/select-service-catalog-definition.png)

4. Hizmet kataloğu tanımından yönetilen bir uygulama örneği oluşturmak için değerler girin:

    * Uygulama örneğinin oluşturulacağı **aboneliği**, **kaynak grubunu**ve **konumu** seçin.
    * Benzersiz bir Azure Işlevi adı ve Azure depolama hesabı adı sağlayın.

    ![Uygulama ayarları](./media/managed-application-with-custom-providers/application-settings.png)

5. Doğrulama geçtiğinde, yönetilen bir uygulamanın örneğini dağıtmak için **Tamam** ' ı tıklatın. 
    
    ![Yönetilen uygulama dağıtma](./media/managed-application-with-custom-providers/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Özel eylemler ve kaynaklar

Hizmet kataloğu uygulama örneği dağıtıldıktan sonra, iki yeni kaynak grubunuz vardır. İlk kaynak grubu `applicationGroup` yönetilen uygulamanın bir örneğini içerir. ikinci kaynak grubu, **özel sağlayıcı**dahil olmak üzere, yönetilen uygulama için kaynakları barındırır `managedResourceGroup`.

![Uygulama kaynak grupları](./media/managed-application-with-custom-providers/application-resource-groups.png)

Yönetilen uygulama örneğine gidebilir ve "genel bakış" sayfasında **özel eylem** gerçekleştirebilir, "kullanıcılar" sayfasında **Kullanıcılar** özel kaynağı oluşturabilir ve özel kaynak üzerinde **özel bağlam eylemi** çalıştırabilirsiniz.

* "Genel bakış" sayfasına gidin ve "ping eylemi" düğmesine tıklayın:

![Özel eylem gerçekleştir](./media/managed-application-with-custom-providers/perform-custom-action.png)

* "Kullanıcılar" sayfasına gidin ve "Ekle" düğmesine tıklayın. Kaynak oluşturmak ve formu göndermek için giriş girişleri sağlayın:

![Özel kaynak oluştur](./media/managed-application-with-custom-providers/create-custom-resource.png)

* "Kullanıcılar" sayfasına gidin, bir "kullanıcılar" kaynağı seçin ve "özel bağlam eylemi" ni tıklatın:

![Özel kaynak oluştur](./media/managed-application-with-custom-providers/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Yardım aranıyor

Azure yönetilen uygulamalar hakkında sorularınız varsa [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps)yapmayı deneyin. Benzer bir soru zaten istendi ve yanıtlamış olabilir, bu nedenle göndermeden önce kontrol edin. Hızlı bir yanıt almak için etiketi `azure-managedapps` ekleyin!

## <a name="next-steps"></a>Sonraki adımlar

Yönetilen uygulamanızı Azure Market'te yayımlamak için bkz. [Market'teki Azure tarafından yönetilen uygulamalar](publish-marketplace-app.md).
