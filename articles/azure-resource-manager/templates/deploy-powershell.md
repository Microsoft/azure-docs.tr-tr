---
title: PowerShell ve şablon ile kaynakları dağıtma
description: Azure 'a kaynak dağıtmak için Azure Resource Manager ve Azure PowerShell kullanın. Kaynaklar bir Kaynak Yöneticisi şablonunda veya bir Bıcep dosyasında tanımlanır.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 11a293ca58fc6acf3bd99bb0169d817dae11fb94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543814"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>ARM şablonları ve Azure PowerShell kaynak dağıtma

Bu makalede, kaynaklarınızı Azure 'a dağıtmak için Azure Resource Manager şablonları (ARM şablonları) veya Bıcep dosyaları ile Azure PowerShell nasıl kullanılacağı açıklanmaktadır. Azure çözümlerinizi dağıtma ve yönetme kavramlarını bilmiyorsanız, bkz. [şablon dağıtımına genel bakış](overview.md) veya [bıcep genel bakış](bicep-overview.md).

Bicep dosyalarını dağıtmak için, [Azure PowerShell sürüm 5.6.0 veya sonraki bir sürümü](/powershell/azure/install-az-ps)gerekir.

## <a name="prerequisites"></a>Önkoşullar

Dağıtılacak bir şablonunuz olması gerekir. Henüz bir hesabınız yoksa Azure hızlı başlangıç şablonları deposundan bir [örnek şablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) indirip kaydedin. Bu makalede kullanılan yerel dosya adı _C:\MyTemplates\azuredeploy.js_.

Azure PowerShell yüklemeniz ve Azure 'a bağlanmanız gerekir:

- **Azure PowerShell cmdlet 'lerini yerel bilgisayarınıza yükler.** Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](/powershell/azure/get-started-azureps).
- **[Connect-Azaccount](/powershell/module/az.accounts/connect-azaccount)kullanarak Azure 'a bağlanın**. Birden çok Azure aboneliğiniz varsa, [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)' i de çalıştırmanız gerekebilir. Daha fazla bilgi için bkz. [birden çok Azure aboneliği kullanma](/powershell/azure/manage-subscriptions-azureps).

PowerShell yüklü değilse, Azure Cloud Shell kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Cloud Shell ARM şablonları dağıtma](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Dağıtım kapsamı

Dağıtımınızı bir kaynak grubuna, aboneliğe, yönetim grubuna veya kiracıya hedefleyebilirsiniz. Dağıtımın kapsamına bağlı olarak, farklı komutlar kullanırsınız.

- Bir **kaynak grubuna** dağıtmak Için, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanın:

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template-or-bicep>
  ```

- Bir **aboneliğe** dağıtmak için cmdlet 'in diğer adı olan [New-azsubscriptiondeployment](/powershell/module/az.resources/new-azdeployment) ' ı kullanın `New-AzDeployment` :

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Abonelik düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](deploy-to-subscription.md).

- Bir **yönetim grubuna** dağıtmak Için, [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)' ı kullanın.

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Yönetim grubu düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [Yönetim grubu düzeyinde kaynak oluşturma](deploy-to-management-group.md).

- Bir **kiracıya** dağıtmak Için, [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)kullanın.

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Kiracı düzeyinde dağıtımlar hakkında daha fazla bilgi için bkz. [kiracı düzeyinde kaynak oluşturma](deploy-to-tenant.md).

Her kapsam için, şablonu dağıtan kullanıcının kaynak oluşturmak için gerekli izinlere sahip olması gerekir.

## <a name="deployment-name"></a>Dağıtım adı

ARM şablonunu dağıttığınızda dağıtıma bir ad verebilirsiniz. Bu ad dağıtım geçmişinden dağıtım almanıza yardımcı olabilir. Dağıtım için bir ad sağlamazsanız, şablon dosyasının adı kullanılır. Örneğin, adlı bir şablonu dağıtırsanız `azuredeploy.json` ve dağıtım adı belirtmezseniz, dağıtım adlandırılır `azuredeploy` .

Bir dağıtımı her çalıştırdığınızda, kaynak grubunun dağıtım geçmişine dağıtım adı ile bir giriş eklenir. Başka bir dağıtım çalıştırırsanız ve aynı adı verirseniz, önceki girdi geçerli dağıtımla değiştirilmiştir. Dağıtım geçmişinde benzersiz girişleri sürdürmek istiyorsanız, her dağıtıma benzersiz bir ad verin.

Benzersiz bir ad oluşturmak için rastgele bir sayı atayabilirsiniz.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Veya bir tarih değeri ekleyin.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Aynı dağıtım adına sahip aynı kaynak grubuna eşzamanlı dağıtımlar çalıştırırsanız, yalnızca son dağıtım tamamlanmıştır. Tamamlanmayan aynı ada sahip dağıtımlar son dağıtımla değiştirilmiştir. Örneğin, adlı bir depolama hesabı dağıtan adlı bir dağıtım çalıştırırsanız `newStorage` `storage1` ve aynı zamanda adlı bir depolama hesabı dağıtan adlı bir dağıtımı çalıştırırsanız `newStorage` `storage2` , yalnızca bir depolama hesabı dağıtırsınız. Elde edilen depolama hesabının adı `storage2` .

Ancak, adlı bir depolama hesabı dağıtan adlı bir dağıtımı çalıştırırsanız `newStorage` `storage1` ve bu işlemi tamamladıktan hemen sonra adlı bir depolama hesabı dağıtan adlı başka bir dağıtım çalıştırırsanız `newStorage` `storage2` , iki depolama hesabınız olur. Biri olarak adlandırılır `storage1` ve diğeri olarak adlandırılır `storage2` . Ancak, dağıtım geçmişinde yalnızca bir girişiniz vardır.

Her dağıtım için benzersiz bir ad belirttiğinizde, bunları çakışma olmadan eşzamanlı olarak çalıştırabilirsiniz. Adlı bir depolama hesabı dağıtan adlı bir dağıtım çalıştırırsanız `newStorage1` `storage1` ve aynı zamanda adında bir depolama hesabı dağıtan adlı başka bir dağıtımı çalıştırırsanız `newStorage2` `storage2` , dağıtım geçmişinde iki depolama hesabınız ve iki giriş vardır.

Eşzamanlı dağıtımlar ile çakışmalardan kaçınmak ve dağıtım geçmişinde benzersiz girişler sağlamak için her dağıtıma benzersiz bir ad verin.

## <a name="deploy-local-template-or-bicep-file"></a>Yerel şablon veya Bıcep dosyası dağıtma

Yerel makinenizden veya dışarıdan depolanan bir şablonu dağıtabilirsiniz. Bu bölümde yerel bir şablon dağıtımı açıklanmaktadır.

Mevcut olmayan bir kaynak grubuna dağıtıyorsanız, kaynak grubunu oluşturun. Kaynak grubunun adı yalnızca alfasayısal karakterler, noktalar, alt çizgiler, kısa çizgiler ve parantezler içerebilir. En fazla 90 karakter olabilir. Ad bir noktayla bitemez.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Yerel bir şablon veya Bıcep dosyası dağıtmak için `-TemplateFile` dağıtım komutundaki parametresini kullanın. Aşağıdaki örnek ayrıca, şablondan gelen bir parametre değerinin nasıl ayarlanacağını gösterir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep>
```

Dağıtımın tamamlanması birkaç dakika sürebilir.

## <a name="deploy-remote-template"></a>Uzak şablonu dağıtma

> [!NOTE]
> Şu anda Azure PowerShell uzak bicep dosyalarının dağıtılmasını desteklemez. Bicep [CLI](./bicep-install.md#development-environment) kullanarak bıcep dosyasını bir JSON şablonuna derleyin ve ardından json dosyasını uzak konuma yükleyin.

ARM şablonlarını yerel makinenizde depolamak yerine, bunları bir dış konumda depolamayı tercih edebilirsiniz. Şablonları bir kaynak denetimi deposunda (GitHub gibi) saklayabilirsiniz. İsterseniz kuruluşunuzda paylaşılan erişim sağlamak için bir Azure depolama hesabı kullanabilirsiniz.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Mevcut olmayan bir kaynak grubuna dağıtıyorsanız, kaynak grubunu oluşturun. Kaynak grubunun adı yalnızca alfasayısal karakterler, noktalar, alt çizgiler, kısa çizgiler ve parantezler içerebilir. En fazla 90 karakter olabilir. Ad bir noktayla bitemez.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Dış şablon kullanmak için `-TemplateUri` parametresini kullanın.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name remoteTemplateDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Önceki örnekte, şablon için genel olarak erişilebilir bir URI gerekir ve bu, şablonunuz önemli verileri içermemelidir. Gizli veriler (yönetici parolası gibi) belirtmeniz gerekiyorsa, bu değeri güvenli bir parametre olarak geçirin. Bununla birlikte, şablona erişimi yönetmek istiyorsanız, [şablon](#deploy-template-spec)özelliklerini kullanmayı göz önünde bulundurun.

Bir depolama hesabında depolanan göreli yol ile uzak bağlantılı şablonlar dağıtmak için, `QueryString` SAS belirtecini belirtmek için kullanın:

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

Daha fazla bilgi için bkz. [bağlantılı şablonlar için göreli yol kullanma](./linked-templates.md#linked-template).

## <a name="deploy-template-spec"></a>Şablon belirtimini dağıt

> [!NOTE]
> Şu anda, Azure PowerShell Bıcep dosyaları sağlayarak şablon özelliklerinin oluşturulmasını desteklemez. Ancak, bir şablon belirtimini dağıtmak için [Microsoft. resources/templatespec](/azure/templates/microsoft.resources/templatespecs) kaynağı Ile bir Bıcep dosyası oluşturabilirsiniz. İşte bir [örnek](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).
Yerel veya uzak şablon dağıtmak yerine, bir [şablon belirtimi](template-specs.md)oluşturabilirsiniz. Şablon belirtimi, Azure aboneliğinizdeki bir ARM şablonu içeren bir kaynaktır. Şablonu kuruluşunuzdaki kullanıcılarla güvenli bir şekilde paylaşmayı kolaylaştırır. Şablon belirtimine erişim sağlamak için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanırsınız. Bu özellik şu anda önizleme aşamasındadır.

Aşağıdaki örneklerde, bir şablon belirtiminin nasıl oluşturulacağı ve dağıtılacağı gösterilmektedir.

İlk olarak, ARM şablonunu sağlayarak şablon belirtimini oluşturun.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Ardından, şablon belirtiminin KIMLIĞINI alın ve dağıtın.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Daha fazla bilgi için bkz. [Azure Resource Manager şablonu özellikleri (Önizleme)](template-specs.md).

## <a name="preview-changes"></a>Değişiklikleri Önizle

Şablonunuzu dağıtmadan önce, şablonun ortamınızda yapacağız değişiklikleri önizleyebilirsiniz. Şablonun beklediğiniz değişiklikleri yaptığını doğrulamak için [ne yapılır işlemini](template-deploy-what-if.md) kullanın. Ayrıca, şablonu hatalara yönelik olarak doğrular.

## <a name="pass-parameter-values"></a>Parametre değerlerini geçir

Parametre değerlerini geçirmek için satır içi parametreleri ya da bir parametre dosyasını kullanabilirsiniz.

### <a name="inline-parameters"></a>Satır içi parametreler

Satır içi parametreleri geçirmek için, komutuyla parametrenin adlarını sağlayın `New-AzResourceGroupDeployment` . Örneğin, bir şablona bir dize ve dizi geçirmek için şunu kullanın:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Ayrıca dosyanın içeriğini alabilir ve bu içeriği satır içi bir parametre olarak sağlayabilirsiniz.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Bir dosyadan parametre değeri alma, yapılandırma değerleri sağlamanız gerektiğinde faydalıdır. Örneğin, [bir Linux sanal makinesi için Cloud-init değerleri](../../virtual-machines/linux/using-cloud-init.md)sağlayabilirsiniz.

Nesneler dizisini geçirmeniz gerekiyorsa, PowerShell 'de karma tablolar oluşturun ve bunları bir diziye ekleyin. Dağıtım sırasında bu diziyi bir parametre olarak geçirin.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parametre dosyaları

Parametreleri betiğinize satır içi değerler olarak geçirmek yerine parametre değerlerini içeren bir JSON dosyası kullanmak daha kolayınıza gelebilir. Parametre dosyası yerel bir dosya veya erişilebilir bir URI 'ye sahip bir dış dosya olabilir. Hem ARM şablonu hem de bicep dosyası JSON parametre dosyalarını kullanır.

Parametre dosyası hakkında daha fazla bilgi için bkz. [Resource Manager parametre dosyası oluşturma](parameter-files.md).

Yerel bir parametre dosyasını geçirmek için `TemplateParameterFile` parametresini kullanın:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Dış parametre dosyasını geçirmek için `TemplateParameterUri` parametresini kullanın:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Sonraki adımlar

- Hata aldığınızda başarılı bir dağıtıma geri dönmek için, [başarılı bir dağıtımda hata durumunda geri alma](rollback-on-error.md)konusuna bakın.
- Kaynak grubunda var olan, ancak şablonda tanımlanmamış kaynakların nasıl işleneceğini belirtmek için bkz. [Azure Resource Manager Dağıtım modları](deployment-modes.md).
- Şablonunuzda parametrelerin nasıl tanımlanacağını anlamak için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
- SAS belirteci gerektiren bir şablonu dağıtma hakkında daha fazla bilgi için bkz. [özel ARM ŞABLONUNU SAS belirteci Ile dağıtma](secure-template-with-sas-token.md).
