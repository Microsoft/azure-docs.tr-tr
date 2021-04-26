---
title: Şablonlarda dağıtım betikleri kullanma | Microsoft Docs
description: Azure Resource Manager şablonlarda dağıtım betikleri kullanın.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: c39b332e9ee62a8502d5e2fdf155819194a30e34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762200"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>ARM şablonlarında dağıtım betikleri kullanma

Azure Kaynak şablonlarında (ARM şablonları) Dağıtım betiklerini nasıl kullanacağınızı öğrenin. Adlı yeni bir kaynak türüyle `Microsoft.Resources/deploymentScripts` Kullanıcılar, şablon dağıtımlarında betikleri yürütebilir ve yürütme sonuçlarını gözden geçirebilir. Bu betikler, aşağıdaki gibi özel adımları gerçekleştirmek için kullanılabilir:

- bir dizine kullanıcı ekleme
- veri düzlemi işlemlerini gerçekleştirme, örneğin Blobları veya çekirdek veritabanını kopyalama
- lisans anahtarını arama ve doğrulama
- otomatik olarak imzalanan sertifika oluşturma
- Azure AD 'de nesne oluşturma
- Özel sistemden IP adresi bloklarını ara

Dağıtım betiğinin avantajları:

- Kod, kullanım ve hata ayıklama işlemlerini kolayca yapabilirsiniz. Dağıtım betikleri, en sevdiğiniz geliştirme ortamlarınızda geliştirebilirsiniz. Betikler, şablonlara veya dış betik dosyalarına gömülebilir.
- Betik dilini ve platformunu belirtebilirsiniz. Şu anda, Linux ortamındaki Azure PowerShell ve Azure CLı dağıtım betikleri desteklenir.
- Komut satırı bağımsız değişkenlerinin betiğe geçirilmesine izin ver.
- Betik çıkışlarını belirtebilir ve bunları dağıtıma geri geçirebilir.

Dağıtım betiği kaynağı yalnızca Azure Container Instance 'ın kullanılabildiği bölgelerde kullanılabilir.  Bkz. [Azure bölgelerindeki Azure Container Instances Için kaynak kullanılabilirliği](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Betik yürütme ve sorun giderme için bir depolama hesabı ve kapsayıcı örneği gereklidir. Mevcut bir depolama hesabını belirtme seçenekleriniz vardır; Aksi takdirde, kapsayıcı örneğiyle birlikte depolama hesabı betik hizmeti tarafından otomatik olarak oluşturulur. Dağıtım betiği yürütmesi bir terminal durumunda olduğunda, otomatik olarak oluşturulan iki kaynak genellikle betik hizmeti tarafından silinir. Kaynaklar silinene kadar kaynaklar için faturalandırılırsınız. Daha fazla bilgi için bkz. [Temizleme dağıtım betiği kaynakları](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> DeploymentScripts kaynak API sürümü 2020-10-01, [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md)destekler. Dağıtım betiği hizmeti, OBO 'yı kullanarak dağıtım betikleri çalıştırmak için Azure Container Instance, Azure depolama hesabı ve yönetilen kimlik için rol atamalarını içeren temel kaynakları oluşturmak üzere dağıtım sorumlusunun belirtecini kullanır. Daha eski API sürümünde, bu kaynakları oluşturmak için yönetilen kimlik kullanılır.
> Azure oturum açma için yeniden deneme mantığı artık sarmalayıcı betikte yerleşik olarak bulunur. Dağıtım betikleri çalıştırdığınız şablonda izin verirseniz. Dağıtım betiği hizmeti, yönetilen kimlik rolü ataması çoğaltılana kadar 10 dakikalık bir aralıkla oturum açmayı yeniden dener.

## <a name="configure-the-minimum-permissions"></a>Minimum izinleri yapılandırma

Dağıtım betiği API sürüm 2020-10-01 veya üzeri için dağıtım sorumlusu, dağıtım betiği kaynağı için gereken temel kaynakları (depolama hesabı ve Azure Container Instance) oluşturmak için kullanılır. Betiğin Azure 'da kimlik doğrulaması yapması ve Azure 'a özel eylemler gerçekleştirmesi gerekiyorsa, komut dosyasını Kullanıcı tarafından atanan bir yönetilen kimlikle yapmanızı öneririz. Yönetilen kimliğin komut dosyasındaki işlemi tamamlaması için gereken erişimi olması gerekir.

En düşük ayrıcalıklı izinleri yapılandırmak için şunlar gerekir:

- Dağıtım sorumlusuna aşağıdaki özelliklerle özel bir rol atayın:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Azure depolama ve Azure Container Instance kaynak sağlayıcıları kayıtlı değilse, ve de eklemeniz gerekir `Microsoft.Storage/register/action` `Microsoft.ContainerInstance/register/action` .

- Yönetilen bir kimlik kullanılıyorsa, dağıtım sorumlusu yönetilen kimlik kaynağına atanmış **yönetilen kimlik işleci** rolüne (yerleşik bir rol) ihtiyaç duyuyor.

## <a name="sample-templates"></a>Örnek şablonlar

Aşağıdaki JSON bir örnektir. Daha fazla bilgi için bkz. en son [şablon şeması](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Örnek, tanıtım amaçlıdır. Özellikler `scriptContent` ve `primaryScriptUri` bir şablonda birlikte bulunamaz.

> [!NOTE]
> _Scriptcontent_ , birden çok satırı olan bir betiği gösterir.  Azure portal ve Azure DevOps işlem hattı, birden çok satırlık bir dağıtım betiğini ayrıştıramıyor. PowerShell komutlarını (noktalı virgül veya _\\ r \\_ veya _\\ n_ kullanarak) tek bir satıra zincirleyebilir ya da `primaryScriptUri` özelliğini bir dış betik dosyası ile kullanabilirsiniz. Kullanılabilir çok sayıda ücretsiz JSON dizesi kaçış/unkaçış aracı vardır. Örneğin, [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Özellik değeri ayrıntıları:

- `identity`: Dağıtım betiği API 'SI sürüm 2020-10-01 veya üzeri için, betikte herhangi bir Azure 'a özgü eylem gerçekleştirmeniz gerekmedikçe Kullanıcı tarafından atanan yönetilen kimlik isteğe bağlıdır.  API sürümü 2019-10-01-önizleme için dağıtım betiği hizmeti tarafından betikleri yürütmek üzere kullandığı için yönetilen bir kimlik gerekir. Identity özelliği belirtildiğinde, komut dosyası hizmeti, `Connect-AzAccount -Identity` Kullanıcı betiği çağırmadan önce çağrılır. Şu anda yalnızca Kullanıcı tarafından atanan yönetilen kimlik desteklenir. Farklı bir kimlikle oturum açmak için, betikte [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) ' u çağırabilirsiniz.
- `kind`: Betiğin türünü belirtin. Şu anda, Azure PowerShell ve Azure CLı betikleri desteklenir. Değerler **AzurePowerShell** ve **azurecli**' dir.
- `forceUpdateTag`: Bu değerin, şablon dağıtımları arasında değiştirilmesi dağıtım betiğini yeniden yürütmeye zorlar. `newGuid()`Veya `utcNow()` işlevlerini kullanırsanız, her iki işlev yalnızca bir parametre için varsayılan değerde kullanılabilir. Daha fazla bilgi için bkz. [betiği birden çok kez çalıştırma](#run-script-more-than-once).
- `containerSettings`: Azure Container Instance 'ı özelleştirmek için ayarları belirtin. Dağıtım betiği yeni bir Azure Container örneği gerektirir. Mevcut bir Azure Container Instance belirtemezsiniz. Ancak, kullanarak kapsayıcı grubu adını özelleştirebilirsiniz `containerGroupName` . Belirtilmemişse, Grup adı otomatik olarak oluşturulur.
- `storageAccountSettings`: Mevcut bir depolama hesabını kullanmak için ayarları belirtin. `storageAccountName`Belirtilmemişse, otomatik olarak bir depolama hesabı oluşturulur. Bkz. [var olan bir depolama hesabını kullanma](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Kullanılacak modül sürümünü belirtin. [Desteklenen Azure PowerShell sürümlerinin](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)listesini görüntüleyin. [Desteklenen Azure CLI sürümlerinin](https://mcr.microsoft.com/v2/azure-cli/tags/list)listesini görüntüleyin.

  >[!IMPORTANT]
  > Dağıtım betiği, Microsoft Container Registry (MCR) ' deki kullanılabilir CLı görüntülerini kullanır. Dağıtım betiği için bir CLı görüntüsünü onaylamak üzere bir ay sürer. 30 gün içinde Yayınlanan CLı sürümlerini kullanmayın. Görüntülerin yayın tarihlerini bulmak için bkz. [Azure CLI sürüm notları](/cli/azure/release-notes-azure-cli). Desteklenmeyen bir sürüm kullanılırsa, hata iletisinde desteklenen sürümler listelenir.

- `arguments`: Parametre değerlerini belirtin. Değerler boşluklarla ayrılır.

  Dağıtım betikleri, [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) sistem çağrısını çağırarak bağımsız değişkenleri dizeler dizisine böler. Bağımsız değişkenler bir [komut özelliği](/rest/api/container-instances/containergroups/createorupdate#containerexec) olarak Azure Container Instance 'a geçirildiğinden ve komut özelliği bir dize dizisi olduğundan, bu adım gereklidir.

  Bağımsız değişkenler kaçış karakterleri içeriyorsa, karakterleri çift kaçış için [Jsonescaper](https://www.jsonescaper.com/) ' ı kullanın. Özgün atlanan dizeyi araca yapıştırın ve ardından **kaçış**' ı seçin.  Araç, Çift kaçan bir dize verir. Örneğin, önceki örnek şablonunda bağımsız değişken olur `-name \"John Dole\"` . Kaçan dize `-name \\\"John Dole\\\"` .

  Object türünde bir ARM şablon parametresini bir bağımsız değişken olarak geçirmek için, [String ()](./template-functions-string.md#string) işlevini kullanarak nesneyi bir dizeye dönüştürün ve sonra replace [()](./template-functions-string.md#replace) işlevini kullanarak herhangi bir öğesini değiştirin `\"` `\\\"` . Örnek:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Daha fazla bilgi için bkz. [örnek şablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Betiğe geçirilecek ortam değişkenlerini belirtin. Daha fazla bilgi için bkz. [dağıtım betikleri geliştirme](#develop-deployment-scripts).
- `scriptContent`: Betik içeriğini belirtin. Bir dış betik çalıştırmak için `primaryScriptUri` bunun yerine kullanın. Örnekler için bkz. [satır içi betiği kullanma](#use-inline-scripts) ve [dış betiği kullanma](#use-external-scripts).
- `primaryScriptUri`: Birincil dağıtım betiğinin desteklenen dosya uzantılarına sahip genel olarak erişilebilen bir URL belirtin. Daha fazla bilgi için bkz. [dış betikleri kullanma](#use-external-scripts).
- `supportingScriptUris`: Ya da ' de çağrılan dosyaları desteklemek için genel olarak erişilebilen bir URL dizisi belirtin `scriptContent` `primaryScriptUri` . Daha fazla bilgi için bkz. [dış betikleri kullanma](#use-external-scripts).
- `timeout`: [Iso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601)belirtilen izin verilen en fazla betik yürütme süresini belirtin. Varsayılan değer **P1D**' dir.
- `cleanupPreference`. Komut dosyası yürütmesi bir terminal durumunda olduğunda dağıtım kaynaklarını Temizleme tercihini belirtin. Varsayılan ayar **her zaman**, bu, Terminal durumuna (başarılı, başarısız, iptal edildi) rağmen kaynakları silmenin anlamına gelir. Daha fazla bilgi için bkz. [dağıtım betiği kaynaklarını Temizleme](#clean-up-deployment-script-resources).
- `retentionInterval`: Dağıtım betiği yürütmesi bir Terminal durumuna ulaştıktan sonra hizmetin dağıtım betiği kaynaklarını koruduğunu belirten aralığı belirtin. Bu süre sona erdiğinde dağıtım betiği kaynakları silinir. Süre, [ıso 8601 düzenine](https://en.wikipedia.org/wiki/ISO_8601)göre belirlenir. Saklama aralığı 1 ile 26 saat arasında (PT26H). Bu özellik `cleanupPreference` **onexpiration** olarak ayarlandığında kullanılır. Daha fazla bilgi için bkz. [dağıtım betiği kaynaklarını Temizleme](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Ek örnekler

- [Örnek 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): bir Anahtar Kasası oluşturun ve anahtar kasasına bir sertifika atamak için dağıtım betiği kullanın.
- [Örnek 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): abonelik düzeyinde bir kaynak grubu oluşturun, kaynak grubunda bir Anahtar Kasası oluşturun ve sonra anahtar kasasına bir sertifika atamak için dağıtım betiği kullanın.
- [Örnek 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun, kaynak grubu düzeyindeki kimliğe katkıda bulunan rolünü atayın, bir Anahtar Kasası oluşturun ve sonra anahtar kasasına bir sertifika atamak için dağıtım betiği kullanın.

## <a name="use-inline-scripts"></a>Satır içi betikleri kullan

Aşağıdaki şablonda, türüyle tanımlanmış bir kaynak vardır `Microsoft.Resources/deploymentScripts` . Vurgulanan bölüm, satır içi betiğidir.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Satır içi dağıtım betikleri çift tırnak içine alındığından, dağıtım betiklerinin içindeki dizelerin bir ters eğik çizgi (**&#92;**) kullanılarak veya tek tırnak içine alınmış olması gerekir. Ayrıca, önceki JSON örneğinde gösterildiği üzere dize değiştirme kullanmayı da düşünebilirsiniz.

Betik bir parametre alır ve parametre değerini çıktı. `DeploymentScriptOutputs` çıkışları depolamak için kullanılır. Çıktılar bölümünde, `value` satır depolanan değerlere nasıl erişegösterdiğini gösterir. `Write-Output` hata ayıklama amacıyla kullanılır. Çıkış dosyasına nasıl erişebileceğinizi öğrenmek için bkz. [Dağıtım betiklerini izleme ve sorun giderme](#monitor-and-troubleshoot-deployment-scripts). Özellik açıklamaları için bkz. [örnek şablonlar](#sample-templates).

Betiği çalıştırmak için **dene** ' yi seçerek Cloud Shell açın ve ardından aşağıdaki kodu kabuk bölmesine yapıştırın.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

Çıktı şuna benzer:

![Kaynak Yöneticisi şablonu dağıtım betiği Merhaba Dünya çıktısı](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Dış betikler kullanın

Satır içi betiklerin yanı sıra dış betik dosyalarını da kullanabilirsiniz. Yalnızca _ps1_ dosya uzantısına sahip birincil PowerShell betikleri desteklenir. CLı betikleri için, betikler geçerli Bash betikleri olduğu sürece, birincil betiklerin uzantıları (veya uzantısı olmadan) olabilir. Dış betik dosyalarını kullanmak için ile değiştirin `scriptContent` `primaryScriptUri` . Örnek:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Daha fazla bilgi için bkz. [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Dış betik dosyalarına erişilebilir olmalıdır. Azure depolama hesaplarında depolanan betik dosyalarınızın güvenliğini sağlamak için bir SAS belirteci oluşturun ve şablonun URI 'sine dahil edin. Dağıtım işleminin tamamlanmasına yetecek sürenin sona ereceği süre sonu süresini ayarlayın. Daha fazla bilgi için bkz. [SAS belirteci ile özel ARM şablonu dağıtma](./secure-template-with-sas-token.md).

Ya da dağıtım betiği tarafından başvurulan betiklerin bütünlüğünü sağlamaktan siz sorumlusunuz `primaryScriptUri` `supportingScriptUris` . Yalnızca güvendiğiniz betiklerin başvurusu.

## <a name="use-supporting-scripts"></a>Destekleyici betikleri kullanma

Karmaşık günlüklerlerini bir veya daha fazla destekleyici betik dosyasına ayırabilirsiniz. `supportingScriptUris`Özelliği, gerekirse destekleyici betik dosyalarına bir URI dizisi sağlamanıza olanak tanır:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Destekleyici betik dosyaları, hem satır içi betiklerden hem de birincil betik dosyalarından çağrılabilir. Destekleyici betik dosyalarının dosya uzantısında hiçbir kısıtlaması yoktur.

Destekleyici dosyalar çalışma zamanında öğesine kopyalanır `azscripts/azscriptinput` . Satır içi betiklerden ve birincil betik dosyalarından destekleyici dosyalara başvurmak için göreli yol kullanın.

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell betiğinin çıktılarla çalışma

Aşağıdaki şablonda değerlerin iki kaynak arasında nasıl geçirileceğini gösterilmektedir `deploymentScripts` :

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

İlk kaynakta, adlı bir değişken tanımlarsınız `$DeploymentScriptOutputs` ve bunu çıkış değerlerini depolamak için kullanabilirsiniz. Şablon içindeki başka bir kaynaktan çıkış değerine erişmek için şunu kullanın:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>CLı betiğinin çıktılarla çalışma

PowerShell dağıtım betiğinden farklı olarak, CLı/Bash desteği betik çıkışlarını depolamak için ortak bir değişken sunmaz, bunun yerine `AZ_SCRIPTS_OUTPUT_PATH` betik çıkışları dosyasının bulunduğu konumu depolayan bir ortam değişkeni vardır. Bir dağıtım betiği Kaynak Yöneticisi şablondan çalışıyorsa, bu ortam değişkeni bash kabuğu tarafından sizin için otomatik olarak ayarlanır. Değeri `AZ_SCRIPTS_OUTPUT_PATH` */mnt/azscripts/azscriptoutput/scriptoutputs.js'* dir.

Dağıtım betiği çıktılarının konuma kaydedilmesi gerekir `AZ_SCRIPTS_OUTPUT_PATH` ve çıktılar geçerli BIR JSON dize nesnesi olmalıdır. Dosyanın içeriğinin anahtar-değer çifti olarak kaydedilmesi gerekir. Örneğin, bir dize dizisi olarak depolanır `{ "MyResult": [ "foo", "bar"] }` .  Yalnızca dizi sonuçlarının depolanması, örneğin `[ "foo", "bar" ]` , geçersiz.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) , önceki örnekte kullanılır. Kapsayıcı görüntüleri ile birlikte gelir. Bkz. [geliştirme ortamını yapılandırma](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Var olan depolama hesabını kullan

Betik yürütme ve sorun giderme için bir depolama hesabı ve kapsayıcı örneği gereklidir. Mevcut bir depolama hesabını belirtme seçenekleriniz vardır; Aksi takdirde, kapsayıcı örneğiyle birlikte depolama hesabı betik hizmeti tarafından otomatik olarak oluşturulur. Var olan bir depolama hesabını kullanma gereksinimleri:

- Desteklenen depolama hesabı türleri şunlardır:

    | SKU             | Desteklenen tür     |
    |-----------------|--------------------|
    | Premium_LRS     | Dosya depolama        |
    | Premium_ZRS     | Dosya depolama        |
    | Standard_GRS    | Depolama, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Depolama, StorageV2 |
    | Standard_RAGRS  | Depolama, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Bu birleşimler dosya paylaşımlarını destekler. Daha fazla bilgi için bkz. [Azure dosya paylaşma](../../storage/files/storage-how-to-create-file-share.md) ve [depolama hesabı türleri](../../storage/common/storage-account-overview.md)oluşturma.

- Depolama hesabı güvenlik duvarı kuralları henüz desteklenmiyor. Daha fazla bilgi için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](../../storage/common/storage-network-security.md).
- Dağıtım sorumlusu, okuma, oluşturma, dosya paylaşımlarını silme dahil olmak üzere depolama hesabını yönetme izinlerine sahip olmalıdır.

Mevcut bir depolama hesabını belirtmek için aşağıdaki JSON öğesini öğesinin özellik öğesine ekleyin `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: depolama hesabının adını belirtin.
- `storageAccountKey`: depolama hesabı anahtarlarından birini belirtin. Anahtarı almak için [ListKeys ()](./template-functions-resource.md#listkeys) işlevini kullanabilirsiniz. Örnek:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Tüm tanım örnekleri için bkz. [örnek şablonlar](#sample-templates) `Microsoft.Resources/deploymentScripts` .

Mevcut bir depolama hesabı kullanıldığında, betik hizmeti benzersiz bir ada sahip bir dosya paylaşma oluşturur. Betik hizmetinin dosya paylaşımının nasıl temizleyeceğini öğrenmek için bkz. [dağıtım betiği kaynaklarını Temizleme](#clean-up-deployment-script-resources) .

## <a name="develop-deployment-scripts"></a>Dağıtım betikleri geliştirme

### <a name="handle-non-terminating-errors"></a>Sonlandırma olmayan hataları işle

Dağıtım betiğinizdeki değişkeni kullanarak, PowerShell 'in sonlandırmasız hatalara nasıl yanıt vereceğini denetleyebilirsiniz `$ErrorActionPreference` . Dağıtım betiğimizde değişken ayarlanmamışsa, komut dosyası hizmeti **devam et** varsayılan değerini kullanır.

Betik hizmeti, ayarlarına rağmen bir hatayla karşılaştığında, kaynak sağlama durumunu **başarısız** olarak ayarlar `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Ortam değişkenlerini kullanma

Dağıtım betiği bu ortam değişkenlerini kullanır:

|Ortam değişkeni|Varsayılan değer|Sistem ayrıldı|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptınput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLı: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|Üzerinde scriptoutputs.js|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|Üzerinde executionresult.js|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/Subscriptions/|N|

Kullanma hakkında daha fazla bilgi için `AZ_SCRIPTS_OUTPUT_PATH` bkz. [CLI betiğinin çıktılarla çalışma](#work-with-outputs-from-cli-script).

### <a name="pass-secured-strings-to-deployment-script"></a>Güvenli dizeleri dağıtım betiğine geçir

Kapsayıcı örneklerinizin ortam değişkenlerini (EnvironmentVariable) ayarlamak, kapsayıcı tarafından çalıştırılan uygulamanın veya betiğin dinamik yapılandırmasını sağlamanıza olanak tanır. Dağıtım betiği, güvenli olmayan ve güvenli olmayan ortam değişkenlerini Azure Container Instance ile aynı şekilde işler. Daha fazla bilgi için bkz. [kapsayıcı örneklerinde ortam değişkenlerini ayarlama](../../container-instances/container-instances-environment-variables.md#secure-values). Örnek için bkz. [örnek şablonlar](#sample-templates).

Ortam değişkenleri için izin verilen en büyük boyut 64 KB 'dir.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Dağıtım betiklerini izleme ve sorunlarını giderme

Betik hizmeti bir [depolama hesabı](../../storage/common/storage-account-overview.md) (mevcut bir depolama hesabı belirtmediğiniz müddetçe) ve betik yürütme için bir [kapsayıcı örneği](../../container-instances/container-instances-overview.md) oluşturur. Bu kaynaklar betik hizmeti tarafından otomatik olarak oluşturulduysa, her iki kaynak de `azscripts` kaynak adlarında sonekine sahiptir.

![Kaynak Yöneticisi şablonu dağıtım betiği kaynak adları](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Kullanıcı betiği, yürütme sonuçları ve STDOUT dosyası depolama hesabının dosya paylaşımlarında depolanır. Adlı bir klasör vardır `azscripts` . Klasöründe, giriş ve çıkış dosyaları için iki klasör daha vardır: `azscriptinput` ve `azscriptoutput` .

Çıkış klasörü, üzerinde bir _executionresult.js_ ve betik çıkış dosyası içerir. Betik yürütme hata iletisini _üzerindeexecutionresult.js_ görebilirsiniz. Çıkış dosyası yalnızca komut dosyası başarıyla yürütüldüğünde oluşturulur. Giriş klasörü bir sistem PowerShell betik dosyası ve kullanıcı dağıtımı komut dosyalarını içerir. Kullanıcı dağıtımı betik dosyasını düzeltilmiş bir kodla değiştirebilir ve dağıtım betiğini Azure Container Instance ' dan yeniden çalıştırabilirsiniz.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Dağıtım komut dosyası kaynağını dağıttıktan sonra, kaynak Azure portal kaynak grubunun altında listelenir. Aşağıdaki ekran görüntüsünde, bir dağıtım betiği kaynağının **genel bakış** sayfası gösterilmektedir:

![Kaynak Yöneticisi şablonu dağıtım betiği portalına genel bakış](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Genel Bakış sayfası kaynağı **sağlama durumu**, **depolama hesabı**, **kapsayıcı örneği** ve **Günlükler** gibi bazı önemli bilgileri görüntüler.

Sol taraftaki menüden dağıtım betiği içeriğini, Betiğe geçirilen bağımsız değişkenleri ve çıktıyı görüntüleyebilirsiniz. Dağıtım betiği için dağıtım betiği de dahil olmak üzere bir şablonu dışa aktarabilirsiniz.

### <a name="use-powershell"></a>PowerShell kullanma

Azure PowerShell kullanarak, Dağıtım betiklerini abonelik veya kaynak grubu kapsamında yönetebilirsiniz:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Dağıtım betiklerini alır veya listeler.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): bir dağıtım betiği yürütmenin günlüğünü alır.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): bir dağıtım betiğini ve ilişkili kaynaklarını kaldırır.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): bir dağıtım betiği yürütmenin günlüğünü diske kaydeder.

`Get-AzDeploymentScript`Çıktı şuna benzerdir:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Azure CLI kullanma

Azure CLı kullanarak, abonelik veya kaynak grubu kapsamındaki Dağıtım betiklerini yönetebilirsiniz:

- [az Deployment-betikler Delete](/cli/azure/deployment-scripts#az_deployment_scripts_delete): bir dağıtım betiğini silin.
- [az Deployment-Scripts List](/cli/azure/deployment-scripts#az_deployment_scripts_list): tüm Dağıtım betiklerini listeleyin.
- [az Deployment-Scripts Show](/cli/azure/deployment-scripts#az_deployment_scripts_show): dağıtım betiği alma.
- [az Deployment-betikler Show-log](/cli/azure/deployment-scripts#az_deployment_scripts_show_log): dağıtım betiği günlüklerini göster.

Liste komut çıktısı şuna benzerdir:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>REST API kullanma

Dağıtım betik kaynağı dağıtım bilgilerini kaynak grubu düzeyinde ve abonelik düzeyinde REST API kullanarak edinebilirsiniz:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

Aşağıdaki örnek [Armclient](https://github.com/projectkudu/ARMClient)kullanır:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

Çıktı şuna benzer olacaktır:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

Aşağıdaki REST API günlüğü döndürür:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Yalnızca dağıtım betiği kaynakları silinmeden önce işe yarar.

Portalda deploymentScripts kaynağını görmek için **gizli türleri göster**' i seçin:

![Kaynak Yöneticisi şablonu dağıtım betiği, gizli türleri göster, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Dağıtım betiği kaynaklarını temizle

Betik yürütme ve sorun giderme için bir depolama hesabı ve kapsayıcı örneği gereklidir. Mevcut bir depolama hesabını belirtme seçenekleriniz vardır, aksi takdirde bir kapsayıcı örneğiyle birlikte bir depolama hesabı otomatik olarak betik hizmeti tarafından oluşturulur. Dağıtım betiği yürütmesi bir terminal durumunda olduğunda, otomatik olarak oluşturulan iki kaynak betik hizmeti tarafından silinir. Kaynaklar silinene kadar kaynaklar için faturalandırılırsınız. Fiyat bilgileri için bkz. [Container Instances fiyatlandırması](https://azure.microsoft.com/pricing/details/container-instances/) ve [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

Bu kaynakların yaşam döngüsü, şablondaki aşağıdaki özelliklerle denetlenir:

- `cleanupPreference`: Betik yürütme bir terminal durumunda olduğunda temizle tercihi. Desteklenen değerler şunlardır:

  - **Her zaman**: betik yürütme bir Terminal durumuna ulaştıktan sonra otomatik olarak oluşturulan kaynakları silin. Mevcut bir depolama hesabı kullanılıyorsa, betik hizmeti depolama hesabında oluşturulan dosya paylaşımından siler. `deploymentScripts`Kaynaklar temizlendikten sonra kaynak hala mevcut olabileceğinden, betik hizmeti komut dosyası yürütme sonuçlarını (örneğin, stdout, çıktılar ve kaynak silinmeden önce dönüş değeri) devam ettirir.
  - **OnSuccess**: otomatik olarak oluşturulan kaynakları yalnızca betik yürütme başarılı olduğunda silin. Mevcut bir depolama hesabı kullanılıyorsa, betik hizmeti yalnızca betik yürütme başarılı olduğunda dosya paylaşımının kaldırılmasına neden olur. Hata ayıklama bilgilerini bulmak için kaynaklara erişmeye devam edebilirsiniz.
  - **Onexpiration**: otomatik olarak oluşturulan kaynakları yalnızca `retentionInterval` ayarın süresi dolduğunda silin. Mevcut bir depolama hesabı kullanılıyorsa, betik hizmeti dosya paylaşımından kaldırır, ancak depolama hesabını korur.

- `retentionInterval`: Bir betik kaynağının saklanacağı zaman aralığını ve sonra süresi dolacak ve silinecek süreyi belirtin.

> [!NOTE]
> Başka amaçlar için betik hizmeti tarafından oluşturulan depolama hesabı ve kapsayıcı örneği kullanılması önerilmez. İki kaynak, betik yaşam döngüsüne bağlı olarak kaldırılabilir.

Kapsayıcı örneği ve depolama hesabı öğesine göre silinir `cleanupPreference` . Ancak, komut dosyası başarısız olursa ve `cleanupPreference` **her zaman** olarak ayarlanmamışsa, dağıtım işlemi kapsayıcıyı bir saat için otomatik olarak çalışır halde tutar. Komut dosyası sorunlarını gidermek için bu saati kullanabilirsiniz. Başarılı dağıtımlar sonrasında kapsayıcıyı çalıştırmaya devam etmek istiyorsanız, betiğe bir uyku adımı ekleyin. Örneğin, betiğinizin sonuna [Başlangıç Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) ekleyin. Uyku adımını eklememeniz durumunda kapsayıcı bir Terminal durumuna ayarlanır ve henüz silinmemiş olsa bile erişilemez.

## <a name="run-script-more-than-once"></a>Betiği birden çok kez çalıştır

Dağıtım betiği yürütmesi bir ıdempotent işlemidir. `deploymentScripts`Kaynak özelliklerinden hiçbiri (satır içi betik dahil) değişmezse, şablonu yeniden dağıtırken komut dosyası yürütülmez. Dağıtım betiği hizmeti şablondaki kaynak adlarını aynı kaynak grubundaki mevcut kaynaklarla karşılaştırır. Aynı dağıtım betiğini birden çok kez yürütmek istiyorsanız iki seçenek vardır:

- Kaynağınızın adını değiştirin `deploymentScripts` . Örneğin, kaynak adı veya kaynak adının bir parçası olarak [UtcNow](./template-functions-date.md#utcnow) şablon işlevini kullanın. Kaynak adını değiştirmek yeni bir kaynak oluşturur `deploymentScripts` . Betik yürütme geçmişini tutmak iyi olur.

    > [!NOTE]
    > `utcNow`İşlev yalnızca bir parametre için varsayılan değerde kullanılabilir.

- Şablon özelliğinde farklı bir değer belirtin `forceUpdateTag` . Örneğin, `utcNow` değer olarak kullanın.

> [!NOTE]
> Idempotent olan Dağıtım betiklerini yazın. Bu, yanlışlıkla yeniden çalıştıklarında sistem değişikliklerine neden olmaz. Örneğin, dağıtım betiği bir Azure kaynağı oluşturmak için kullanılıyorsa, oluşturmadan önce kaynağın mevcut olmadığını doğrulayın, bu nedenle betik başarılı olur veya kaynağı tekrar oluşturmazsınız.

## <a name="configure-development-environment"></a>Geliştirme ortamını yapılandırma

Dağıtım betiği geliştirme ortamınız olarak önceden yapılandırılmış bir kapsayıcı görüntüsü kullanabilirsiniz. Daha fazla bilgi için bkz. [şablonlarda dağıtım betikleri için geliştirme ortamını yapılandırma](./deployment-script-template-configure-dev.md).

Betiği başarıyla test edildikten sonra, şablonlarınızı şablonlarda bir dağıtım betiği olarak kullanabilirsiniz.

## <a name="deployment-script-error-codes"></a>Dağıtım betiği hata kodları

| Hata kodu | Description |
|------------|-------------|
| DeploymentScriptInvalidOperation | Şablondaki dağıtım betiği kaynak tanımı geçersiz özellik adları içeriyor. |
| DeploymentScriptResourceConflict | Terminal dışı durumda olan bir dağıtım betiği kaynağı silinemez ve yürütme 1 saati aşmadı. Ya da aynı dağıtım betiğini aynı kaynak tanımlayıcısıyla (aynı abonelik, kaynak grubu adı ve kaynak adı), aynı anda farklı betik gövdesi içeriğiyle yeniden çalıştıramıyorum. |
| Deploymentscriptoperationbaşarısız oldu | Dağıtım betiği işlemi dahili olarak başarısız oldu. Microsoft desteği 'ne başvurun. |
| Deploymentscriptstorageaccountaccesskeynotbelirtti | Mevcut depolama hesabı için erişim anahtarı belirtilmedi.|
| Deploymentscriptcontainergroupcontainsınvalidcontainers | Dağıtım betiği hizmeti tarafından oluşturulan bir kapsayıcı grubu dışarıdan değiştirildi ve geçersiz kapsayıcılar eklendi. |
| Deploymentscriptcontainergroupınnonterminalstate | İki veya daha fazla dağıtım betiği kaynağı aynı kaynak grubunda aynı Azure Kapsayıcı örneği adını kullanır ve bunlardan biri henüz yürütmesini tamamlamadı. |
| Deploymentscriptstorageaccountınvalidkind | BlobBlobStorage veya BlobStorage türünün mevcut depolama hesabı dosya paylaşımlarını desteklemez ve kullanılamaz. |
| Deploymentscriptstorageaccountınvalidkindandsku | Mevcut depolama hesabı dosya paylaşımlarını desteklemez. Desteklenen depolama hesabı türlerinin bir listesi için bkz. [var olan depolama hesabını kullanma](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | Depolama hesabı yok veya bir dış işlem ya da araç tarafından silindi. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Belirtilen depolama hesabı bir hizmet uç noktası içeriyor. Hizmet uç noktası olan bir depolama hesabı desteklenmiyor. |
| Deploymentscriptstorageaccounınvalidaccesskey | Mevcut depolama hesabı için geçersiz erişim anahtarı belirtildi. |
| Deploymentscriptstorageaccounınvalidaccesskeyformat | Geçersiz depolama hesabı anahtar biçimi. Bkz. [depolama hesabı erişim anahtarlarını yönetme](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | Dağıtım betiği yürütme süresi, dağıtım betiği kaynak tanımında belirtilen zaman aşımı değerini aştı. |
| DeploymentScriptInvalidOutputs | Dağıtım betiği çıkışı geçerli bir JSON nesnesi değil. |
| Deploymentscriptcontainerınstancesserviceloginfailure | Kullanıcı tarafından atanan yönetilen kimlik, 1 dakikalık aralığa göre 10 denemeden sonra oturum açamadı. |
| DeploymentScriptContainerGroupNotFound | Dağıtım betiği hizmeti tarafından oluşturulan bir kapsayıcı grubu, dış bir araç veya işlem tarafından silindi. |
| DeploymentScriptDownloadFailure | Destekleyici bir betik indirilemedi. Bkz. [destekleyici betiği kullanma](#use-supporting-scripts).|
| DeploymentScriptError | Kullanıcı betiği bir hata oluşturdu. |
| Deploymentscriptbootstrapscriptexecutionbaşarısız oldu | Önyükleme betiği bir hata oluşturdu. Önyükleme betiği, dağıtım betiği yürütmeyi düzenleyen sistem betiğdir. |
| Deploymentscriptexecutionbaşarısız oldu | Dağıtım betiği yürütülürken bilinmeyen bir hata oluştu. |
| Deploymentscriptcontainerınstancesserviceunavailable | Azure Container Instance (acı) oluştururken, acı hizmeti kullanılamayan bir hata oluşturdu. |
| Deploymentscriptcontainergroupınnonterminalstate | Azure Container Instance (acı) oluştururken, başka bir dağıtım betiği aynı kapsamda aynı acı adını (aynı abonelik, kaynak grubu adı ve kaynak adı) kullanıyor. |
| Deploymentscriptcontainergroupnamegeçersiz | Belirtilen Azure Container Instance Name (acı), acı gereksinimlerini karşılamıyor. Bkz. [Azure Container Instances genel sorunları giderme](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede dağıtım betiklerini nasıl kullanacağınızı öğrendiniz. Bir dağıtım betiği öğreticisini gözden aktarmak için:

> [!div class="nextstepaction"]
> [Öğretici: Azure Resource Manager şablonlarda dağıtım betikleri kullanma](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Modül öğren: dağıtım betikleri kullanarak ARM şablonlarını genişletme](/learn/modules/extend-resource-manager-template-deployment-scripts/)
