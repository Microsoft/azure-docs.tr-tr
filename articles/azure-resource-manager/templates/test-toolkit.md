---
title: ARM şablonu test araç seti
description: Şablonunuzda Azure Resource Manager şablonu (ARM şablonu) test araç setinin nasıl çalıştırılacağını açıklar. Araç seti, önerilen uygulamalar uyguladıysanız görmenizi sağlar.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e5ad0b6dca7718166517b52148fbc6dd49f38869
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674021"
---
# <a name="use-arm-template-test-toolkit"></a>ARM şablonu test araç setini kullanma

[Azure Resource Manager şablonu (ARM şablonu) test araç seti](https://aka.ms/arm-ttk) , şablonunuzun önerilen uygulamalar kullanıp kullanmadığını denetler. Şablonunuz Önerilen uygulamalarla uyumlu olmadığında, önerilen değişikliklerle ilgili uyarıların bir listesini döndürür. Test araç setini kullanarak, şablon geliştirmede yaygın sorunların nasıl önleneceğini öğrenebilirsiniz.

Test araç seti, [varsayılan testlerin bir kümesini](test-cases.md)sağlar. Bu sınamalar önerilerdir ancak gereksinimlerdir. Hangi testlerin hedeflerinizle ilgili olduğuna karar verebilir ve hangi testlerin çalıştırılacağını özelleştirebilirsiniz.

Bu makalede, test araç setinin nasıl çalıştırılacağı ve testlerin nasıl ekleneceği ve kaldırılacağı açıklanır. Varsayılan testlerin açıklamaları için bkz. [araç seti test çalışmaları](test-cases.md).

Araç seti, PowerShell veya CLı içindeki bir komuttan çalıştırılabilen bir PowerShell betikleri kümesidir.

## <a name="install-on-windows"></a>Windows’ta yükleme

1. Henüz PowerShell yoksa, [Windows 'A PowerShell 'i yükleyebilirsiniz](/powershell/scripting/install/installing-powershell-core-on-windows).

1. Test araç seti için [en son. zip dosyasını indirin](https://aka.ms/arm-ttk-latest) ve ayıklayın.

1. PowerShell 'i başlatın.

1. Test araç takımını ayıkladığınız klasöre gidin. Bu klasör içinde **ARM-TTK** klasörü ' ne gidin.

1. [Yürütme ilkeniz](/powershell/module/microsoft.powershell.core/about/about_execution_policies) betikleri Internet 'ten engelliyorsa, betik dosyalarının engellemesini kaldırmanız gerekir. **ARM-TTK** klasöründe olduğunuzdan emin olun.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Modülü içeri aktarın.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Testleri çalıştırmak için aşağıdaki komutu kullanın:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Linux'ta yükleme

1. Henüz PowerShell yoksa, [Linux 'Ta PowerShell 'i yükleyebilirsiniz](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Test araç seti için [en son. zip dosyasını indirin](https://aka.ms/arm-ttk-latest) ve ayıklayın.

1. PowerShell 'i başlatın.

   ```bash
   pwsh
   ```

1. Test araç takımını ayıkladığınız klasöre gidin. Bu klasör içinde **ARM-TTK** klasörü ' ne gidin.

1. [Yürütme ilkeniz](/powershell/module/microsoft.powershell.core/about/about_execution_policies) betikleri Internet 'ten engelliyorsa, betik dosyalarının engellemesini kaldırmanız gerekir. **ARM-TTK** klasöründe olduğunuzdan emin olun.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Modülü içeri aktarın.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Testleri çalıştırmak için aşağıdaki komutu kullanın:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>macOS’ta yükleme

1. Zaten PowerShell 'e sahip değilseniz, [macOS 'Ta PowerShell 'i yükleyebilirsiniz](/powershell/scripting/install/installing-powershell-core-on-macos).

1. `coreutils` yükleme:

   ```bash
   brew install coreutils
   ```

1. Test araç seti için [en son. zip dosyasını indirin](https://aka.ms/arm-ttk-latest) ve ayıklayın.

1. PowerShell 'i başlatın.

   ```bash
   pwsh
   ```

1. Test araç takımını ayıkladığınız klasöre gidin. Bu klasör içinde **ARM-TTK** klasörü ' ne gidin.

1. [Yürütme ilkeniz](/powershell/module/microsoft.powershell.core/about/about_execution_policies) betikleri Internet 'ten engelliyorsa, betik dosyalarının engellemesini kaldırmanız gerekir. **ARM-TTK** klasöründe olduğunuzdan emin olun.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Modülü içeri aktarın.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Testleri çalıştırmak için aşağıdaki komutu kullanın:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Sonuç biçimi

Pass, **yeşil** ve **[+]** ile önceden ortaya çıkacak testler görüntülenir.

Başarısız olan testler **kırmızı renkte** görüntülenir ve **[-]** ile önceden başlatılacaktır.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="test sonuçlarını görüntüle":::

Metin sonuçları şunlardır:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Test parametreleri

**-TemplatePath** parametresini sağladığınızda araç seti, azuredeploy.json veya maintemplate.jsadlı bir şablon için bu klasöre bakar. Önce bu şablonu sınar ve sonra klasördeki ve alt klasörlerindeki diğer tüm şablonları sınar. Diğer şablonlar, bağlantılı şablonlar olarak test edilir. Yolunuz [CreateUiDefinition.js](../managed-applications/create-uidefinition-overview.md)adlı bir dosya IÇERIYORSA, UI tanımıyla ilgili olan testleri çalıştırır.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Bu klasördeki bir dosyayı test etmek için **-File** parametresini ekleyin. Ancak, klasörde hala azuredeploy.jsveya maintemplate.jsadında bir ana şablon olması gerekir.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Varsayılan olarak, tüm testler çalıştırılır. Çalıştırmak üzere ayrı testler belirtmek için **-Test** parametresini kullanın. Testin adını belirtin. Adlar için bkz. [araç seti Için test çalışmaları](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Testleri özelleştirme

ARM şablonları için araç seti, **\arm-ttk\testcases\deploymentTemplate** klasöründeki tüm testleri çalıştırır. Bir testi kalıcı olarak kaldırmak istiyorsanız, bu dosyayı klasöründen silin.

[Createuıdefinition](../managed-applications/create-uidefinition-overview.md) dosyaları için, **\arm-ttk\testcases\CreateUiDefinition** klasöründeki tüm testleri çalıştırır.

Kendi testinizi eklemek için, adlandırma kuralına sahip bir dosya oluşturun: **Your-Custom-Test-Name.test.ps1**.

Test, şablonu bir nesne parametresi veya dize parametresi olarak alabilir. Genellikle, bir veya diğerini kullanırsınız, ancak her ikisini de kullanabilirsiniz.

Şablonun bir bölümünü almanız ve özellikleri boyunca yinelemek istediğinizde, Object parametresini kullanın. Object parametresini kullanan bir test aşağıdaki biçimdedir:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Şablon nesnesi aşağıdaki özelliklere sahiptir:

* $schema
* contentVersion
* parameters
* değişkenlerinin
* kaynaklar
* çıkışı

Örneğin, ile parametrelerin koleksiyonunu alabilirsiniz `$TemplateObject.parameters` .

Tüm şablonda bir dize işlemi yapmanız gerektiğinde dize parametresini kullanın. String parametresini kullanan bir test aşağıdaki biçimdedir:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Örneğin, belirli bir sözdiziminin kullanıldığını görmek için dize parametresinin normal bir ifadesini çalıştırabilirsiniz.

Testi uygulama hakkında daha fazla bilgi edinmek için bu klasördeki diğer testlere bakın.

## <a name="integrate-with-azure-pipelines"></a>Azure Pipelines ile tümleştirin

Test araç takımını Azure ardışık düzenine ekleyebilirsiniz. Bir işlem hattı ile, şablon her güncelleştirildiği zaman testi çalıştırabilir veya dağıtım işleminizin bir parçası olarak çalıştırabilirsiniz.

Test araç takımını ardışık düzene eklemenin en kolay yolu, üçüncü taraf uzantılardır. Aşağıdaki iki uzantı mevcuttur:

* [ARM TTK testlerini Çalıştır](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM şablonu Sınayıcısı](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Ya da kendi görevlerinizi uygulayabilirsiniz. Aşağıdaki örnek, test araç setinin nasıl indirileceği gösterilmektedir.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

Sonraki örnek, testlerin nasıl çalıştırılacağını gösterir.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- Varsayılan testler hakkında bilgi edinmek için bkz. [ARM şablonu test araç seti Için varsayılan test çalışmaları](test-cases.md).
- Test araç setini kullanarak kapsanan bir Microsoft Learn modülü için bkz. [değişiklik önizleme ve Azure kaynaklarını doğrulama, durum ve ARM şablonu test araç seti](/learn/modules/arm-template-test/).
