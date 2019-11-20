---
title: PowerShell ve şablon ile kaynakları dağıtma
description: Azure 'a kaynak dağıtmak için Azure Resource Manager ve Azure PowerShell kullanın. Kaynaklar, bir Resource Manager şablonunda tanımlanır.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: e726d1154fca1dbcce244783bf987bea6610cf98
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150712"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma

Kaynaklarınızı Azure 'a dağıtmak için Kaynak Yöneticisi şablonlarla Azure PowerShell nasıl kullanacağınızı öğrenin. Azure çözümlerinizi dağıtma ve yönetme kavramları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](resource-group-overview.md).

## <a name="deployment-scope"></a>Dağıtım kapsamı

Dağıtımınızı bir Azure aboneliğine ya da bir abonelik içindeki bir kaynak grubuna hedefleyebilirsiniz. Çoğu durumda, dağıtımı bir kaynak grubuna hedefleyebilirsiniz. Abonelik genelinde ilke ve rol atamaları uygulamak için abonelik dağıtımlarını kullanın. Ayrıca, abonelik dağıtımlarını bir kaynak grubu oluşturmak ve kaynakları dağıtmak için de kullanabilirsiniz. Dağıtımın kapsamına bağlı olarak, farklı komutlar kullanırsınız.

Bir **kaynak grubuna**dağıtmak Için, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanın:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Bir **aboneliğe**dağıtmak Için, [New-azdeployment](/powershell/module/az.resources/new-azdeployment)kullanın:

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Abonelik düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](deploy-to-subscription.md).

Şu anda yönetim grubu dağıtımları yalnızca REST API aracılığıyla desteklenir. Yönetim grubu düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [Yönetim grubu düzeyinde kaynak oluşturma](deploy-to-management-group.md).

Bu makaledeki örnekler, kaynak grubu dağıtımlarını kullanır.

## <a name="prerequisites"></a>Önkoşullar

Dağıtılacak bir şablonunuz olması gerekir. Henüz bir hesabınız yoksa Azure hızlı başlangıç şablonları deposundan bir [örnek şablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) indirip kaydedin. Bu makalede kullanılan yerel dosya adı **c:\MyTemplates\azuredeploy.JSON**' dir.

Şablonları dağıtmak için Azure Cloud Shell kullanmıyorsanız, Azure PowerShell yüklemeniz ve Azure 'a bağlanmanız gerekir:

- **Azure PowerShell cmdlet 'lerini yerel bilgisayarınıza yükler.** Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](/powershell/azure/get-started-azureps).
- **[Connect-Azaccount](/powershell/module/az.accounts/connect-azaccount)kullanarak Azure 'a bağlanın**. Birden çok Azure aboneliğiniz varsa, [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)' i de çalıştırmanız gerekebilir. Daha fazla bilgi için bkz. [birden çok Azure aboneliği kullanma](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Yerel şablon dağıt

Aşağıdaki örnek, bir kaynak grubu oluşturur ve yerel makinenizden bir şablon dağıtır. Kaynak grubunun adı yalnızca alfasayısal karakterler, noktalar, alt çizgiler, kısa çizgiler ve parantezler içerebilir. En fazla 90 karakter olabilir. Nokta ile bitemez.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Dağıtımın tamamlanması birkaç dakika sürebilir.

## <a name="deploy-remote-template"></a>Uzak şablonu dağıtma

Kaynak Yöneticisi şablonlarını yerel makinenizde depolamak yerine, bunları bir dış konumda depolamayı tercih edebilirsiniz. Şablonları bir kaynak denetimi deposunda (GitHub gibi) saklayabilirsiniz. Ya da, bunları kuruluşunuzda paylaşılan erişim için bir Azure depolama hesabında saklayabilirsiniz.

Dış şablon dağıtmak için **Templateuri** parametresini kullanın. Örnek şablonu GitHub 'dan dağıtmak için örnekteki URI 'yi kullanın.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Önceki örnekte, şablon için genel olarak erişilebilir bir URI gerekir ve bu, şablonunuz önemli verileri içermemelidir. Gizli veriler (yönetici parolası gibi) belirtmeniz gerekiyorsa, bu değeri güvenli bir parametre olarak geçirin. Ancak, şablonunuzun herkese açık bir şekilde erişilebilir olmasını istemiyorsanız, bunu özel bir depolama kapsayıcısında depolayarak koruyabilirsiniz. Paylaşılan erişim imzası (SAS) belirteci gerektiren bir şablonu dağıtma hakkında daha fazla bilgi için bkz. [özel şablonu SAS belirteci Ile dağıtma](resource-manager-powershell-sas-token.md). Öğreticiye gitmek için bkz. [öğretici: Kaynak Yöneticisi Şablon dağıtımı Azure Key Vault tümleştirme](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Azure Cloud Shell 'den dağıtma

Şablonunuzu dağıtmak için [Azure Cloud Shell](https://shell.azure.com) kullanabilirsiniz. Dış şablon dağıtmak için şablonun URI 'sini sağlayın. Yerel bir şablon dağıtmak için, önce şablonunuzu Cloud Shell depolama hesabına yüklemeniz gerekir. Dosyaları kabuğa yüklemek için kabuk penceresinden **dosyaları karşıya yükle/indir** menü simgesini seçin.

Cloud Shell 'i açmak için [https://shell.azure.com](https://shell.azure.com)gidin veya aşağıdaki kod bölümünden **dene-bunu** seçin:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Kodu kabuğa yapıştırmak için, kabuğun içine sağ tıklayıp **Yapıştır**' ı seçin.

## <a name="pass-parameter-values"></a>Parametre değerlerini geçir

Parametre değerlerini geçirmek için satır içi parametreleri ya da bir parametre dosyasını kullanabilirsiniz.

### <a name="inline-parameters"></a>Satır içi parametreler

Satır içi parametreleri geçirmek için, `New-AzResourceGroupDeployment` komutuyla parametrenin adlarını sağlayın. Örneğin, bir şablona bir dize ve dizi geçirmek için şunu kullanın:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Ayrıca dosyanın içeriğini alabilir ve bu içeriği satır içi bir parametre olarak sağlayabilirsiniz.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Bir dosyadan parametre değeri alma, yapılandırma değerleri sağlamanız gerektiğinde faydalıdır. Örneğin, [bir Linux sanal makinesi için Cloud-init değerleri](../virtual-machines/linux/using-cloud-init.md)sağlayabilirsiniz.

Nesneler dizisini geçirmeniz gerekiyorsa, PowerShell 'de karma tablolar oluşturun ve bunları bir diziye ekleyin. Dağıtım sırasında bu diziyi bir parametre olarak geçirin.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parametre dosyaları

Komut dosyanıza satır içi değer olarak parametre geçirmek yerine, parametre değerlerini içeren bir JSON dosyası kullanmayı daha kolay bulabilirsiniz. Parametre dosyası yerel bir dosya veya erişilebilir bir URI 'ye sahip bir dış dosya olabilir.

Parametre dosyası hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi parametre dosyası oluşturma](resource-manager-parameter-files.md).

Yerel bir parametre dosyasını geçirmek için **Templateparameterfile** parametresini kullanın:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Dış parametre dosyasını geçirmek için **Templateparameteruri** parametresini kullanın:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Test şablonu dağıtımları

Şablon ve parametre değerlerinizi hiçbir kaynağı gerçekten dağıtmadan test etmek için, [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)' ı kullanın. 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Herhangi bir hata algılanmazsa, komut yanıt olmadan sonlanır. Bir hata algılanırsa, komut bir hata mesajı döndürür. Örneğin, depolama hesabı SKU 'SU için yanlış bir değer geçirilerek aşağıdaki hata döndürülür:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Şablonunuzda bir sözdizimi hatası varsa, komut, şablonu ayrıştıramadığını belirten bir hata döndürür. İleti, ayrıştırma hatasının satır numarasını ve konumunu gösterir.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Sonraki adımlar

- Hata aldığınızda başarılı bir dağıtıma geri dönmek için, [başarılı bir dağıtımda hata durumunda geri alma](rollback-on-error.md)konusuna bakın.
- Kaynak grubunda var olan, ancak şablonda tanımlanmamış kaynakların nasıl işleneceğini belirtmek için bkz. [Azure Resource Manager Dağıtım modları](deployment-modes.md).
- Şablonunuzda parametrelerin nasıl tanımlanacağını anlamak için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).
- SAS belirteci gerektiren bir şablonu dağıtma hakkında daha fazla bilgi için bkz. [özel şablonu SAS belirteci Ile dağıtma](resource-manager-powershell-sas-token.md).
