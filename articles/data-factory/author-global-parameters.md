---
title: Genel parametreler
description: Azure Data Factory ortamlarınızın her biri için genel parametreleri ayarlayın
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 03/15/2021
ms.openlocfilehash: 3110ce8cb97379fd4690903ec769cc1dfc7f1326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492772"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure Data Factory genel parametreler

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Genel parametreler, herhangi bir ifadede bir işlem hattı tarafından tüketilen bir veri fabrikası genelinde sabitler. Aynı parametre adlarına ve değerlerine sahip birden çok işlem hattına sahip olduğunuzda faydalıdır. Bir veri fabrikasını sürekli tümleştirme ve dağıtım işlemi (CI/CD) kullanarak yükseltirken, bu parametreleri her bir ortamda geçersiz kılabilirsiniz. 

## <a name="creating-global-parameters"></a>Genel parametreler oluşturma

Genel parametre oluşturmak için, **Yönet** bölümündeki *genel parametreler* sekmesine gidin. **Yeni** ' yi seçerek oluşturma yan-NAV ' ı açın.

![Genel parametreler oluşturmak için seçtiğiniz yeni düğmeyi vurgulayan ekran görüntüsü.](media/author-global-parameters/create-global-parameter-1.png)

Yan gezinte bir ad girin, bir veri türü seçin ve parametresinin değerini belirtin.

![Yeni genel parametreye ilişkin adı, veri türünü ve değeri nereye ekleneceğini gösteren ekran görüntüsü.](media/author-global-parameters/create-global-parameter-2.png)

Genel bir parametre oluşturulduktan sonra parametrenin adına tıklayarak düzenleyebilirsiniz. Aynı anda birden çok parametreyi değiştirmek için **Tümünü Düzenle**' yi seçin.

![Genel parametreler oluştur](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>İşlem hattında genel parametreleri kullanma

Genel parametreler, herhangi bir [ardışık düzen ifadesinde](control-flow-expression-language-functions.md)kullanılabilir. Bir işlem hattı veri kümesi veya veri akışı gibi başka bir kaynağa başvuruyorsa, genel parametre değerini bu kaynak parametreleri aracılığıyla geçirebilirsiniz. Genel parametrelere olarak başvurulur `pipeline().globalParameters.<parameterName>` .

![Genel parametreleri kullanma](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD 'deki genel parametreler

Sürekli tümleştirme ve dağıtım çözümünüzde genel parametreleri tümleştirmenin iki yolu vardır:

* ARM şablonuna genel parametreleri ekleyin
* PowerShell betiği aracılığıyla genel parametreleri dağıtma

Çoğu kullanım durumu için, ARM şablonuna genel parametreleri eklemeniz önerilir. Bu, yerel olarak [CI/CD belgesi](continuous-integration-deployment.md)içinde özetlenen çözümle tümleştirilir. Genel parametreler, genellikle ortamdan ortama geçiş yaparken varsayılan olarak ARM şablon parametresi olarak eklenecektir. **Yönetim** hub 'ından ARM şablonunda genel parametrelerin dahil edilmesini sağlayabilirsiniz.

> [!NOTE]
> **ARM şablonu yapılandırmasına dahil etme** yalnızca "git modunda" bulunabilir. Şu anda "canlı modda" veya "Data Factory" modunda devre dışı bırakılmıştır. 

> [!WARNING]
>Parametre adında '-' kullanılamaz. Bir hata kodu "{" Code ":" BadRequest "," Message ":" ErrorCode = ınvalidtemplate, ErrorMessage = > ifadesi ' Pipeline (). globalParameters. myParam-dbtest-URL ' geçerli değil:.....} ". Ancak, parametre adında ' _ ' kullanabilirsiniz.

![ARM şablonuna Ekle](media/author-global-parameters/include-arm-template.png)

ARM şablonuna genel parametreler eklemek, müşteri tarafından yönetilen anahtar veya diğer ortamlarda git yapılandırması gibi diğer fabrika düzeyindeki ayarları geçersiz kılacak bir fabrika düzeyi ayarı ekler. Bu ayarları, UıAT veya PROD gibi yükseltilmiş bir ortamda etkinleştirdiyseniz, genel parametreleri aşağıda vurgulanan adımlarda bir PowerShell betiği aracılığıyla dağıtmanız daha iyidir.

### <a name="deploying-using-powershell"></a>PowerShell kullanarak dağıtma

Aşağıdaki adımlar, PowerShell aracılığıyla genel parametrelerin nasıl dağıtılacağını özetler. Hedef fabrikanızın, müşteri tarafından yönetilen anahtar gibi bir fabrika düzeyi ayarı varsa, bu faydalıdır.

Genel parametrelerle bir fabrika yayımladığınızda veya bir ARM şablonunu dışarı aktardığınızda, *your-factory-name_GlobalParameters.jsüzerinde* adlı bir dosya Ile *globalparameters* adlı bir klasör oluşturulur. Bu dosya, yayımlanan fabrikada her genel parametre türünü ve değerini içeren bir JSON nesnesidir.

![Genel parametreler yayımlanıyor](media/author-global-parameters/global-parameters-adf-publish.png)

TEST veya ÜRETIM gibi yeni bir ortama dağıtım yapıyorsanız, bu genel parametreler dosyasının bir kopyasını oluşturmanız ve ortama özgü uygun değerlerin üzerine yazılması önerilir. Özgün genel parametre dosyasını yeniden yayımladığınızda, üzerine yazılır, ancak diğer ortamın kopyası dokunmaz.

Örneğin, ' ADF-DEV ' adlı bir fabrikanızı ve ' dev ' değeri ile ' Environment ' adlı, dize türünde bir genel parametre varsa, *üzerindeADF-DEV_GlobalParameters.js* adlı bir dosya yayımladığınızda oluşturulur. ' ADF_TEST ' adlı bir test fabrikasına dağıtım yapıyorsanız, JSON dosyasının bir kopyasını oluşturun (örneğin, ADF-TEST_GlobalParameters.jsadlı) ve parametre değerlerini ortama özgü değerlerle değiştirin. ' Environment ' parametresinin ' test ' değeri artık olabilir. 

![Genel parametreleri dağıtma](media/author-global-parameters/powershell-task.png)

Genel parametreleri ek ortamlara yükseltmek için aşağıdaki PowerShell betiğini kullanın. ARM Şablon dağıtımı önce Azure PowerShell DevOps görevi ekleyin. DevOps görevinde yeni parametreler dosyasının konumunu, hedef kaynak grubunu ve hedef veri fabrikasını belirtmeniz gerekir.

> [!NOTE]
> PowerShell kullanarak genel parametreleri dağıtmak için, az Module 'ün en az sürüm 4.4.0 kullanmanız gerekir.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Data Factory [sürekli tümleştirme ve dağıtım süreci](continuous-integration-deployment.md) hakkında bilgi edinin
* [Denetim akışı ifade dilini](control-flow-expression-language-functions.md) kullanmayı öğrenin
