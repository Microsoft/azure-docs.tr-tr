---
title: Sürekli tümleştirme ve teslim için otomatik yayımlama
description: Sürekli tümleştirme ve teslim için otomatik olarak yayımlama hakkında bilgi edinin.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 5730b0c7e522f7496f578ffebf716957fcaa56b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788944"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Sürekli tümleştirme ve teslim için otomatik yayımlama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Genel Bakış

Sürekli tümleştirme, kod tabanınızda yapılan her değişikliği otomatik olarak test etme uygulamasıdır. Sürekli teslim, mümkün olduğunca erken sürekli tümleştirme sırasında gerçekleşen testi izler ve değişiklikleri bir hazırlama veya üretim sistemine gönderir.

Azure Data Factory, sürekli tümleştirme ve sürekli teslim (CI/CD), Data Factory işlem hatlarını geliştirme, test ve üretim gibi bir ortamdan diğerine taşıma anlamına gelir. Data Factory, işlem hatları, veri kümeleri ve veri akışları gibi çeşitli Data Factory varlıklarınızın yapılandırmasını depolamak için [Azure Resource Manager şablonları (ARM şablonları)](../azure-resource-manager/templates/overview.md) kullanır.

Bir veri fabrikasını başka bir ortama yükseltmek için önerilen iki yöntem vardır:

- Data Factory [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)tümleştirmesi kullanılarak otomatik dağıtım.
- Azure Resource Manager ile Data Factory Kullanıcı deneyimi tümleştirmesi kullanarak ARM şablonunu el ile karşıya yükleme.

Daha fazla bilgi için bkz. [Azure Data Factory sürekli tümleştirme ve teslim](continuous-integration-deployment.md).

Bu makalede, sürekli dağıtım geliştirmeleri ve CI/CD için otomatik yayımlama özelliği ele alınmaktadır.

## <a name="continuous-deployment-improvements"></a>Sürekli dağıtım geliştirmeleri

Otomatik yayımlama özelliği, Data Factory kullanıcı deneyiminin **Tümünü doğrula** ve **dışarı aktarma ARM şablonu** özelliklerini alır ve genel kullanıma açık bir NPM paketiyle mantıksal tüketilebilir hale gelir [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Bu nedenle, Data Factory Kullanıcı arabirimine gitmek ve bir düğmeyi el ile seçmek yerine bu eylemleri programlı bir şekilde tetikleyebilirsiniz. Bu özellik, CI/CD işlem hatlarınızı bir truer sürekli tümleştirme deneyimi sağlayacak.

### <a name="current-cicd-flow"></a>Geçerli CI/CD akışı

1. Her kullanıcı özel dallarında değişiklikler yapar.
1. Ana öğesine gönderim yapılamaz. Kullanıcıların değişiklik yapması için bir çekme isteği oluşturması gerekir.
1. Kullanıcılar Data Factory Kullanıcı arabirimini yükledikten sonra değişiklikleri Data Factory dağıtmak ve Yayımla dalında ARM şablonlarını oluşturmak için **Yayımla** ' yı seçin.
1. DevOps yayın işlem hattı yeni bir sürüm oluşturmak ve yayınlama dalına her yeni değişiklik gönderildiğinde ARM şablonunu dağıtmak üzere yapılandırılır.

![Geçerli CI/CD akışını gösteren diyagram.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>El ile adım

Geçerli CI/CD akışında, Kullanıcı deneyimi ARM şablonunu oluşturmak için aracı olur. Sonuç olarak, bir kullanıcının Data Factory Kullanıcı ARABIRIMINE gitmesi ve el ile **Yayımla** ' yı seçerek ARM şablonu oluşturmayı başlatabilir ve Yayımla dalına bırakmalısınız.

### <a name="the-new-cicd-flow"></a>Yeni CI/CD akışı

1. Her kullanıcı özel dallarında değişiklikler yapar.
1. Ana öğesine gönderim yapılamaz. Kullanıcıların değişiklik yapması için bir çekme isteği oluşturması gerekir.
1. Azure DevOps işlem hattı derlemesi, ana öğe için her yeni bir işleme yapıldığında tetiklenir. Doğrulama başarılı olursa kaynakları doğrular ve yapı olarak bir ARM şablonu oluşturur.
1. DevOps yayın işlem hattı yeni bir sürüm oluşturmak ve yeni bir derleme kullanılabilir olduğunda ARM şablonunu dağıtmak üzere yapılandırılır.

![Yeni CI/CD akışını gösteren diyagram.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Ne değişti?

- Artık DevOps derleme işlem hattı kullanan bir yapı sürecimiz vardır.
- Derleme işlem hattı ADFUtilities NPM paketini kullanır, bu, tüm kaynakları doğrulayacak ve ARM şablonları oluşturacaktır. Bu şablonlar tek ve bağlantılı olabilir.
- Derleme işlem hattı, Data Factory kaynaklarını doğrulamadan ve Data Factory UI (**Yayımla** düğmesi) yerine ARM şablonu oluşturmaya sorumludur.
- DevOps yayın tanımı artık git yapıtı yerine bu yeni derleme işlem hattını kullanacaktır.

> [!NOTE]
> Dalı olan mevcut mekanizmayı kullanmaya devam edebilir `adf_publish` veya yeni akışı kullanabilirsiniz. Her ikisi de desteklenir.

## <a name="package-overview"></a>Pakete genel bakış

Pakette Şu anda iki komut mevcuttur:

- ARM şablonunu dışarı aktarma
- Doğrulama

### <a name="export-arm-template"></a>ARM şablonunu dışarı aktarma

`npm run start export <rootFolder> <factoryId> [outputFolder]`Belirli bir klasörün kaynaklarını kullanarak ARM şablonunu dışarı aktarmak için ' i çalıştırın. Bu komut, ARM şablonunu oluşturmadan önce bir doğrulama denetimi de çalıştırır. Aşağıda bir örnek verilmiştir:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` , Data Factory kaynaklarının nerede bulunduğunu temsil eden zorunlu bir alandır.
- `FactoryId` , biçimdeki Data Factory kaynak KIMLIĞINI temsil eden zorunlu bir alandır `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` , oluşturulan ARM şablonunu kaydetmek için göreli yolu belirten isteğe bağlı bir parametredir.
 
> [!NOTE]
> Oluşturulan ARM şablonu, fabrika 'nin canlı sürümünde yayımlanmaz. Dağıtım bir CI/CD işlem hattı kullanılarak yapılmalıdır.
 
### <a name="validate"></a>Doğrulama

`npm run start validate <rootFolder> <factoryId>`Belirli bir klasörün tüm kaynaklarını doğrulamak için ' i çalıştırın. Aşağıda bir örnek verilmiştir:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` , Data Factory kaynaklarının nerede bulunduğunu temsil eden zorunlu bir alandır.
- `FactoryId` , biçimdeki Data Factory kaynak KIMLIĞINI temsil eden zorunlu bir alandır `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Azure işlem hattı oluşturma

NPM paketleri çeşitli yollarla tüketilirken, birincil avantajlardan biri [Azure Işlem hattı](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)aracılığıyla tüketilecektir. İşbirliği dalınızdaki her bir birleştirmede, önce kodun tümünü doğrulayan ve sonra ARM şablonunu bir yayın işlem hattı tarafından tüketilen bir [Yapı yapıtına](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) dışarı aktaran bir işlem hattı tetiklenebilir. Geçerli CI/CD işleminden farklı olarak, *yayın işlem hattınızı mevcut `adf_publish` dal yerine bu yapıta işaret* edecek.

Kullanmaya başlamak için bu adımları izleyin:

1.  Bir Azure DevOps projesi açın ve işlem **hatları**' na gidin. **Yeni İşlem Hattı**’nı seçin.

    ![Yeni işlem hattı düğmesini gösteren ekran görüntüsü.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Ardışık düzen YAML betiğinizi kaydetmek istediğiniz depoyu seçin. Bunu, Data Factory kaynaklarınızın aynı deposundaki bir yapı klasörüne kaydetmenizi öneririz. Aşağıdaki örnekte gösterildiği gibi, paket adını içeren depodaki dosyada *package.js* olduğundan emin olun:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  **Başlatıcı işlem hattı** seçin. YAML dosyasını, aşağıdaki örnekte gösterildiği gibi karşıya yüklediyseniz veya birleştirdiyseniz, doğrudan bu dosyaya da işaret edebilir ve düzenleyebilirsiniz.

    ![Başlatıcı işlem hattını gösteren ekran görüntüsü.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  YAML kodunuzu girin. YAML dosyasını başlangıç noktası olarak kullanmanızı öneririz.
1.  Kaydet ve Çalıştır. YAML 'yi kullandıysanız, ana dal her güncelleştirilmesinde tetiklenir.

## <a name="next-steps"></a>Sonraki adımlar

Data Factory 'de sürekli tümleştirme ve teslim hakkında daha fazla bilgi edinin:

- [Azure Data Factory 'de sürekli tümleştirme ve teslim](continuous-integration-deployment.md).
