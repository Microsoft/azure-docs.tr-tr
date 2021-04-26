---
title: Azure DevOps kullanarak işlev uygulama kodunu sürekli Güncelleştir
description: Azure Işlevleri 'ni hedefleyen bir Azure DevOps işlem hattı ayarlamayı öğrenin.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a99e313a0c3fe9093137d4acaa64e789ef5e10e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762218"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Azure DevOps kullanarak sürekli teslim

[Azure Pipelines](/azure/devops/pipelines/)kullanarak işlevinizi bir Azure işlevleri uygulamasına otomatik olarak dağıtabilirsiniz.

İşlem hattınızı tanımlamaya yönelik iki seçeneğiniz vardır:

- **YAML dosyası**: BIR YAML dosyası ardışık düzeni tanımlar. Dosya bir yapı adımları bölümüne ve bir sürüm bölümüne sahip olabilir. YAML dosyası uygulamayla aynı depoda olmalıdır.
- **Şablon**: Şablonlar, uygulamanızı oluşturan veya dağıtan, kullanıma hazırlanmış görevlerdir.

## <a name="yaml-based-pipeline"></a>YAML tabanlı işlem hattı

YAML tabanlı bir işlem hattı oluşturmak için, önce uygulamanızı derleyin ve ardından uygulamayı dağıtın.

### <a name="build-your-app"></a>Uygulamanızı derleme

Uygulamanızı nasıl derleyebilirsiniz Azure Pipelines uygulamanızın programlama diline bağlıdır. Her dilin bir dağıtım yapıtı oluşturan belirli derleme adımları vardır. Bir dağıtım yapıtı, işlev uygulamanızı Azure 'da dağıtmak için kullanılır.

# <a name="c"></a>[,\#](#tab/csharp)

.NET uygulaması derlemek üzere bir YAML dosyası oluşturmak için aşağıdaki örneği kullanabilirsiniz:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Bir JavaScript uygulaması derlemek için bir YAML dosyası oluşturmak üzere aşağıdaki örneği kullanabilirsiniz:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Belirli bir Python sürümü için uygulama derlemek üzere bir YAML dosyası oluşturmak üzere aşağıdaki örneklerden birini kullanabilirsiniz. Python yalnızca Linux üzerinde çalışan işlev uygulamaları için desteklenir.

**Sürüm 3,7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Sürüm 3,6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Bir PowerShell uygulamasını paketlemek üzere bir YAML dosyası oluşturmak için aşağıdaki örneği kullanabilirsiniz. PowerShell yalnızca Windows Azure Işlevleri için desteklenir.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Uygulamanızı dağıtın

Barındırma işletim sistemine bağlı olarak, YAML dosyanıza aşağıdaki YAML örneklerinden birini eklemeniz gerekir.

#### <a name="windows-function-app"></a>Windows işlev uygulaması

Bir Windows işlev uygulamasını dağıtmak için aşağıdaki kod parçacığını kullanabilirsiniz:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux işlev uygulaması

Bir Linux işlev uygulamasını dağıtmak için aşağıdaki kod parçacığını kullanabilirsiniz:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Şablon tabanlı işlem hattı

Azure DevOps 'daki şablonlar, bir uygulamayı oluşturan veya dağıtan önceden tanımlanmış görev gruplarıdır.

### <a name="build-your-app"></a>Uygulamanızı derleme

Uygulamanızı nasıl derleyebilirsiniz Azure Pipelines uygulamanızın programlama diline bağlıdır. Her dilin bir dağıtım yapıtı oluşturan belirli derleme adımları vardır. Azure 'da işlev uygulamanızı güncelleştirmek için bir dağıtım yapıtı kullanılır.

Yerleşik yapı şablonlarını kullanmak için yeni bir derleme işlem hattı oluşturduğunuzda, tasarımcı şablonlarını kullanarak bir işlem hattı oluşturmak için **Klasik düzenleyiciyi kullan** ' ı seçin.

![Azure Pipelines klasik düzenleyiciyi seçin](media/functions-how-to-azure-devops/classic-editor.png)

Kodunuzun kaynağını yapılandırdıktan sonra, Azure Işlevleri derleme şablonları ' nı arayın. Uygulama dilinizle eşleşen şablonu seçin.

![Azure Işlevleri derleme şablonu seçin](media/functions-how-to-azure-devops/build-templates.png)

Bazı durumlarda, yapı yapıtları belirli bir klasör yapısına sahiptir. **Yolları arşivlemek için kök klasör adını** seçin onay kutusunu seçmeniz gerekebilir.

![Kök klasör adını sonuna eklenecek seçenek](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript uygulamaları

JavaScript uygulamanızın Windows yerel modüllerine bağımlılığı varsa, aracı havuzu sürümünü **BARıNDıRıLAN VS2017** olarak güncelleştirmeniz gerekir.

![Aracı havuzu sürümünü Güncelleştir](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Uygulamanızı dağıtın

Yeni bir yayın işlem hattı oluşturduğunuzda, Azure Işlevleri yayın şablonu ' nu arayın.

![Azure Işlevleri sürüm şablonunu arayın](media/functions-how-to-azure-devops/release-template.png)

Dağıtım yuvasına dağıtım, yayın şablonunda desteklenmez.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Azure CLı kullanarak derleme işlem hattı oluşturma

Azure 'da bir derleme işlem hattı oluşturmak için `az functionapp devops-pipeline create` [komutunu](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create)kullanın. Derleme işlem hattı, deponuzda yapılan tüm kod değişikliklerini derlemek ve serbest bırakmak için oluşturulur. Bu komut, derleme ve yayın işlem hattını tanımlayan yeni bir YAML dosyası oluşturur ve sonra deponuzda kaydeder. Bu komutun önkoşulları, kodunuzun konumuna bağlıdır.

- Kodunuz GitHub 'da ise:

    - Aboneliğiniz için **yazma** izinleriniz olmalıdır.

    - Azure DevOps 'da proje yöneticisi olmanız gerekir.

    - Yeterli izinlere sahip bir GitHub kişisel erişim belirteci (PAT) oluşturmak için izinleriniz olmalıdır. Daha fazla bilgi için bkz [. GITHUB Pat izin gereksinimleri.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Otomatik olarak oluşturulmuş YAML dosyasını çalıştırabilmeniz için GitHub deponuzdaki Ana dala yürütmek için izinleriniz olmalıdır.

- Kodunuz Azure Repos:

    - Aboneliğiniz için **yazma** izinleriniz olmalıdır.

    - Azure DevOps 'da proje yöneticisi olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure işlevlerine genel bakış](functions-overview.md)konusunu gözden geçirin.
- [Azure DevOps 'a genel bakış](/azure/devops/pipelines/)konusunu inceleyin.