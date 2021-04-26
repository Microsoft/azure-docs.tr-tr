---
title: Öğretici-kaynaklar & özel eylemler
description: Bu öğreticide, Azure özel sağlayıcısı ile Azure yönetilen uygulamasının nasıl oluşturulacağı açıklanmaktadır.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 228d1a8bcd97daebae873de4c69cbec238e0c995
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312544"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Öğretici: özel eylemler ve kaynaklarla yönetilen uygulama oluşturma

Bu öğreticide, özel eylemler ve kaynaklarla kendi yönetilen uygulamanızı oluşturursunuz. Yönetilen uygulama sayfada özel bir eylem `Overview` , içinde ayrı bir menü öğesi olarak görüntülenecek özel bir kaynak türü `Table of Content` ve özel kaynak sayfasında özel bir bağlam eylemi içerir.

Bu öğretici aşağıdaki adımları içerir:

> [!div class="checklist"]
> * Yönetilen uygulama örneği oluşturmak için Kullanıcı arabirimi tanım dosyası yaz
> * [Azure özel sağlayıcısı](../custom-providers/overview.md), Azure depolama hesabı ve Azure işlevi ile dağıtım şablonu yazma
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
* [Azure özel sağlayıcı](../custom-providers/overview.md) özellikleri.

## <a name="user-interface-definition"></a>Kullanıcı arabirimi tanımı

Bu öğreticide, yönetilen bir uygulama oluşturursunuz ve yönetilen kaynak grubu özel sağlayıcı örneği, depolama hesabı ve işlev içerecektir. Bu örnekte kullanılan Azure Işlevi, Eylemler ve kaynaklar için özel sağlayıcı işlemlerini işleyen bir API uygular. Azure depolama hesabı, özel sağlayıcı kaynaklarınız için temel depolama alanı olarak kullanılır.

Yönetilen uygulama örneği oluşturmak için Kullanıcı arabirimi tanımı, `funcname` ve `storagename` giriş öğeleri içerir. Depolama hesabı adı ve işlev adı genel olarak benzersiz olmalıdır. Varsayılan olarak, işlev dosyaları [örnek işlev paketinden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)dağıtılır, ancak *üzerindecreateUiDefinition.js* bir paket bağlantısı için bir giriş öğesi ekleyerek bunu değiştirebilirsiniz:

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

ve *üzerindecreateUiDefinition.js* çıkış:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Örnek *üzerindeki tümcreateUiDefinition.js* , [Başvuru: Kullanıcı arabirimi öğeleri yapıtlarına başvurabilir](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Özel sağlayıcı içeren şablon

Özel sağlayıcı ile yönetilen bir uygulama örneği oluşturmak için, **genel** adına sahip özel sağlayıcı kaynağı tanımlamanız ve **üzerindemainTemplate.js** **Microsoft. customproviders/resourceproviders** yazmanız gerekir. Bu kaynakta, hizmetinize yönelik kaynak türlerini ve eylemleri tanımlarsınız. Azure Işlevi dağıtmak için ve Azure depolama hesabı örnekleri, türü kaynakları `Microsoft.Web/sites` ve `Microsoft.Storage/storageAccounts` sırasıyla tanımlar.

Bu öğreticide, `users` özel bir kaynak bağlamında gerçekleştirilecek bir kaynak türü, `ping` özel eylem ve `users/contextAction` özel eylem oluşturacaksınız `users` . Her kaynak türü ve eylemi için, [createUiDefinition.jsüzerinde](#user-interface-definition)belirtilen ada sahip işleve işaret eden bir uç nokta sağlar. Kaynak türleri ve eylemler için **yönlendirme türünü** belirtin `Proxy,Cache` `Proxy` :

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

Örnek *üzerinde tümmainTemplate.js* , [Başvuru: dağıtım şablonu yapıtında](reference-main-template-artifact.md)bulunabilir.

## <a name="view-definition-artifact"></a>Tanım yapıtını görüntüleme

Yönetilen uygulamanızda özel eylemleri ve özel kaynakları içeren Kullanıcı arabirimini tanımlamak için, yapıt **üzerindeviewDefinition.js** yazmanız gerekir. Görünüm tanımı yapıtı hakkında daha fazla bilgi için bkz. [Azure yönetilen uygulamalar 'da tanım yapıtı görüntüleme](concepts-view-definition.md).

Bu öğreticide şunları tanımlarsınız:
* Temel metin girişi ile özel bir eylemi temsil eden araç çubuğu düğmesi içeren bir *genel bakış* sayfası `TestAction` .
* Özel bir kaynak türünü temsil eden bir *Kullanıcılar* sayfası `users` .
* `users/contextAction` *Kullanıcılar* sayfasında, özel kaynak türünde bir bağlamda gerçekleştirilecek özel bir kaynak eylemi `users` .

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

Örnek *üzerindeki tümviewDefinition.js* , [Başvuru: View definition yapay](reference-view-definition-artifact.md)öğesinde bulunabilir.

## <a name="managed-application-definition"></a>Yönetilen uygulama tanımı

Aşağıdaki yönetilen uygulama yapılarını zip arşivine paketleyin ve depolama birimine yükleyin:

* Üzerinde createUiDefinition.js
* Üzerinde mainTemplate.js
* Üzerinde viewDefinition.js

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

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal, **tüm hizmetler**' i seçin. Kaynak listesinde **yönetilen uygulamalar merkezi**' ni yazın ve seçin.
2. **Yönetilen uygulamalar merkezi**'Nde **Hizmet kataloğu uygulama tanımı** ' nı seçin ve **Ekle**' ye tıklayın. 
    
    ![Hizmet kataloğu Ekle](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Hizmet kataloğu tanımı oluşturmak için değerleri girin:

    * Hizmet kataloğu tanımı, **görünen ad** ve *Açıklama*(isteğe bağlı) için benzersiz bir **ad** sağlayın.
    * Uygulama tanımının oluşturulacağı **aboneliği**, **kaynak grubunu** ve **konumu** seçin. ZIP paketi için kullanılan aynı kaynak grubunu kullanabilir veya yeni bir kaynak grubu oluşturabilirsiniz.
    * Bir **paket dosyası URI 'si** için, önceki adımda oluşturduğunuz zip dosyasının yolunu belirtin.

    ![Değer sağla](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Kimlik doğrulama ve kilit düzeyi bölümüne geldiğinizde, **Yetkilendirme Ekle**' yi seçin.

    ![Yetkilendirme Ekle](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Kaynakları yönetmek için bir Azure Active Directory grubu seçin ve **Tamam**' ı seçin.

   ![Yetkilendirme grubu Ekle](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Tüm değerleri sağladıysanız **Oluştur**' u seçin.

   ![Yönetilen uygulama tanımı oluştur](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Yönetilen uygulama örneği

Yönetilen uygulama tanımı dağıtıldığında aşağıdaki betiği çalıştırın veya yönetilen uygulama örneğinizi özel sağlayıcıyla dağıtmak için Azure portal bölümündeki adımları uygulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal, **tüm hizmetler**' i seçin. Kaynak listesinde **yönetilen uygulamalar merkezi**' ni yazın ve seçin.
2. **Yönetilen uygulamalar merkezi**'Nde **Hizmet kataloğu uygulamaları** ' nı seçin ve **Ekle**' ye tıklayın. 

    ![Yönetilen uygulama ekle](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. **Hizmet kataloğu uygulamaları** sayfasında, arama kutusuna hizmet kataloğu tanımı görünen adı yazın. Önceki adımda oluşturulan tanımı seçin ve **Oluştur**' a tıklayın.

    ![Hizmet kataloğu seçme](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Hizmet kataloğu tanımından yönetilen bir uygulama örneği oluşturmak için değerler girin:

    * Uygulama örneğinin oluşturulacağı **aboneliği**, **kaynak grubunu** ve **konumu** seçin.
    * Benzersiz bir Azure Işlevi adı ve Azure depolama hesabı adı sağlayın.

    ![Uygulama ayarları](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Doğrulama geçtiğinde, yönetilen bir uygulamanın örneğini dağıtmak için **Tamam** ' ı tıklatın. 
    
    ![Yönetilen uygulama dağıtma](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Özel eylemler ve kaynaklar

Hizmet kataloğu uygulama örneği dağıtıldıktan sonra, iki yeni kaynak grubunuz vardır. İlk kaynak grubu, `applicationGroup` yönetilen uygulamanın bir örneğini içerir, ikinci kaynak grubu, `managedResourceGroup` yönetilen uygulama için **özel sağlayıcı** dahil kaynakları barındırır.

![Uygulama kaynak grupları](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Yönetilen uygulama örneğine gidebilir ve "genel bakış" sayfasında **özel eylem** gerçekleştirebilir, "kullanıcılar" sayfasında **Kullanıcılar** özel kaynağı oluşturabilir ve özel kaynak üzerinde **özel bağlam eylemi** çalıştırabilirsiniz.

* "Genel bakış" sayfasına gidin ve "ping eylemi" düğmesine tıklayın:

![Özel eylem gerçekleştir](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* "Kullanıcılar" sayfasına gidin ve "Ekle" düğmesine tıklayın. Kaynak oluşturmak ve formu göndermek için giriş girişleri sağlayın:

![Ekran görüntüsü, kullanıcılardan Ekle düğmesinin seçili olduğunu gösterir.](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* "Kullanıcılar" sayfasına gidin, bir "kullanıcılar" kaynağı seçin ve "özel bağlam eylemi" ni tıklatın:

![Ekran görüntüsü özel bağlam eylemini gösterir.](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Yardım aranıyor

Azure yönetilen uygulamalar hakkında sorularınız varsa, Azure tarafından yönetilen-uygulama etiketi ile Azure-Managed-App veya [Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-managed-applications.html) ile [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managed-app) isteyebilirsiniz. Benzer bir soru zaten istendi ve yanıtlamış olabilir, bu nedenle göndermeden önce kontrol edin. Daha hızlı yanıt için lütfen ilgili etiketleri kullanın. 

## <a name="next-steps"></a>Sonraki adımlar

Yönetilen uygulamanızı Azure Market'te yayımlamak için bkz. [Market'teki Azure tarafından yönetilen uygulamalar](../../marketplace/create-new-azure-apps-offer.md).

[Azure özel sağlayıcılar](../custom-providers/overview.md)hakkında daha fazla bilgi edinin.
