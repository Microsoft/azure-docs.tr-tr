---
title: SAS belirteci ile güvenli bir şekilde şablon dağıtın
description: Bir SAS belirteci ile korunan Azure Resource Manager şablonuyla Azure 'a kaynak dağıtın. Azure PowerShell ve Azure CLı gösterir.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 8138563211cae12cc38f0e737be49b32204dc243
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150328"
---
# <a name="deploy-private-resource-manager-template-with-sas-token"></a>SAS belirteci ile özel Resource Manager şablonu dağıtma

Şablonunuz bir depolama hesabında bulunuyorsa, genel kullanıma sunulmamak için şablona erişimi kısıtlayabilirsiniz. Şablon için bir paylaşılan erişim imzası (SAS) belirteci oluşturarak ve dağıtım sırasında bu belirteci sağlayarak güvenli bir şablona erişirsiniz. Bu makalede, bir SAS belirteci ile şablon dağıtmak için Azure PowerShell veya Azure CLı 'nın nasıl kullanılacağı açıklanmaktadır.

## <a name="create-storage-account-with-secured-container"></a>Güvenli kapsayıcı ile depolama hesabı oluşturma

Aşağıdaki betik, ortak erişim kapatılmış bir depolama hesabı ve kapsayıcısı oluşturur.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Şablonu depolama hesabına yükle

Şimdi, şablonunuzu depolama hesabına yüklemeye hazırsınız demektir. Kullanmak istediğiniz şablonun yolunu belirtin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Dağıtım sırasında SAS belirteci sağla

Bir depolama hesabında özel bir şablon dağıtmak için bir SAS belirteci oluşturun ve şablon için URI 'sine dahil edin. Dağıtım işleminin tamamlanmasına yetecek sürenin sona ereceği süre sonu süresini ayarlayın.

> [!IMPORTANT]
> Şablonu içeren Blobun yalnızca hesap sahibi tarafından erişilebilir. Ancak, blob için bir SAS belirteci oluşturduğunuzda, blob bu URI 'ye sahip herkes tarafından erişilebilir. Başka bir Kullanıcı URI 'yi kesirse, bu kullanıcı şablona erişebilir. SAS belirteci, şablonlarınıza erişimi sınırlayan iyi bir yoldur, ancak parolalar gibi hassas verileri doğrudan şablonda içermemelidir.
>

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Bağlı şablonlar içeren bir SAS belirteci kullanmanın bir örneği için bkz. [Azure Resource Manager ile bağlantılı şablonlar kullanma](resource-group-linked-templates.md).


## <a name="next-steps"></a>Sonraki adımlar
* Şablonları dağıtmaya giriş için bkz. [Kaynak Yöneticisi şablonlarıyla kaynak dağıtma ve Azure PowerShell](resource-group-template-deploy.md).
* Şablon dağıtan bir örnek betik için, bkz. [Kaynak Yöneticisi şablonu betiği dağıtma](resource-manager-samples-powershell-deploy.md)
* Şablonda parametreleri tanımlamak için bkz. [yazma şablonları](resource-group-authoring-templates.md#parameters).
