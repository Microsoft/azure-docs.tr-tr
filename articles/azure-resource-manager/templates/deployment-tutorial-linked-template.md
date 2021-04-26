---
title: Öğretici-bağlantılı şablon dağıtma
description: Bağlı bir şablonu dağıtmayı öğrenin
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589283"
---
# <a name="tutorial-deploy-a-linked-template"></a>Öğretici: bağlı bir şablon dağıtma

[Önceki öğreticilerde](./deployment-tutorial-local-template.md), yerel bilgisayarınızda depolanan bir şablonu dağıtmayı öğrendiniz. Karmaşık çözümleri dağıtmak için bir şablonu birçok şablona bölebilir ve bu şablonları bir ana şablon aracılığıyla dağıtabilirsiniz. Bu öğreticide, bağlantılı bir şablona başvuruyu içeren bir ana şablon dağıtmayı öğreneceksiniz. Ana şablon dağıtıldığında, bağlantılı şablonun dağıtımını tetikler. Ayrıca, SAS belirtecini kullanarak şablonları nasıl depolayacağınızı ve güvenlik altına alabileceğinizi öğreneceksiniz. Yaklaşık **12 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

Önceki öğreticiyi tamamlamanızı öneririz, ancak bu gerekli değildir.

## <a name="review-template"></a>Şablonu gözden geçir

Önceki öğreticilerde, bir depolama hesabı, App Service planı ve Web uygulaması oluşturan bir şablon dağıtırsınız. Kullanılan şablon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Bağlantılı şablon oluşturma

Depolama hesabı kaynağını bağlı bir şablona ayırabilirsiniz:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Aşağıdaki şablon ana şablondur. Vurgulanan `Microsoft.Resources/deployments` nesne, bağlantılı bir şablonun nasıl çağrılacağını gösterir. Bağlantılı şablon yerel bir dosya veya yalnızca yerel ağınızda bulunan bir dosya olarak depolanamaz. Bağlı şablon için HTTP veya HTTPS içeren bir URI değeri sağlayabilir ya da bir uzak bağlantılı şablonu üst şablona göre bir konuma dağıtmak için _relativePath_ özelliğini kullanabilirsiniz. Bir seçenek, hem ana şablonu hem de bağlantılı şablonu bir depolama hesabına yerleştirmemektedir.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Bağlı şablonu depola

Ana şablon ve bağlantılı şablon her ikisi de GitHub 'da depolanır:

Aşağıdaki PowerShell betiği bir depolama hesabı oluşturur, bir kapsayıcı oluşturur ve iki şablonu bir GitHub deposundan kapsayıcıya kopyalar. Bu iki şablon şunlardır:

- Ana şablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Bağlantılı şablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Cloud Shell açmak için **dene** ' yi seçin, PowerShell betiğini kopyalamak için **Kopyala** ' yı seçin ve komut dosyasını yapıştırmak için kabuk bölmesine sağ tıklayın:

> [!IMPORTANT]
> Depolama hesabı adları 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakam ve küçük harf kullanılmalıdır. Ad benzersiz olmalıdır. Şablonda, depolama hesabı adı, **Mağaza** eklenmiş proje adıdır ve proje adı 3 ila 11 karakter arasında olmalıdır. Bu nedenle, proje adı depolama hesabı adı gereksinimlerini karşılamalıdır ve 11 ' den az karakter içermelidir.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Şablon dağıtma

Bir depolama hesabına şablon dağıtmak için bir SAS belirteci oluşturun ve _-QueryString_ parametresine sağlayın. Dağıtım işleminin tamamlanmasına yetecek sürenin sona ereceği süre sonu süresini ayarlayın. Şablonları içeren bloblara yalnızca hesap sahibi tarafından erişilebilir. Ancak, bir blob için SAS belirteci oluşturduğunuzda, bu SAS belirtecine sahip herkes tarafından blob 'a erişilebilir. Başka bir Kullanıcı URI ve SAS belirtecini karşılar, bu kullanıcı şablona erişebilir. SAS belirteci, şablonlarınıza erişimi sınırlayan iyi bir yoldur, ancak parolalar gibi hassas verileri doğrudan şablonda içermemelidir.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> Aşağıdaki Azure CLı kodunda, `date` parametre `-d` MacOS içinde geçersiz bir bağımsız değişkendir. Bu nedenle, macOS kullanıcıları, ' yi kullanmanız gereken macOS 'ta terminalde geçerli saate 2 saat eklemek için `-v+2H` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bağlı bir şablonu dağıtmayı öğrendiniz. Sonraki öğreticide, bir şablonu dağıtmak için DevOps işlem hattı oluşturmayı öğreneceksiniz.

> [!div class="nextstepaction"]
> [İşlem hattı oluşturma](./deployment-tutorial-pipeline.md)