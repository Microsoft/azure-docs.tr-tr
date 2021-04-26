---
title: Azure yay bulutu için CI/CD
description: Azure yay bulutu için CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 331ef39facb9f7cf8f069f2a238be325f53de2d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878791"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure yay bulutu için CI/CD

Sürekli tümleştirme ve sürekli teslim araçları, var olan uygulamalara en az çaba ve riske sahip güncelleştirmeleri hızlı bir şekilde dağıtmanızı sağlar. Azure DevOps, bu anahtar işleri düzenlemenize ve denetlemenize yardımcı olur. Şu anda Azure yay bulutu belirli bir Azure DevOps eklentisi sunmaz.  Ancak, [Azure CLI görevi](/azure/devops/pipelines/tasks/deploy/azure-cli)kullanarak Spring Cloud uygulamalarınızı DevOps ile tümleştirebilirsiniz.

Bu makalede Azure DevOps ile tümleştirme için Azure Spring Cloud ile Azure CLı görevinin nasıl kullanılacağı gösterilmektedir.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager hizmet bağlantısı oluşturma

Azure DevOps projenize Azure Resource Manager bir hizmet bağlantısı oluşturmayı öğrenmek için [Bu makaleyi](/azure/devops/pipelines/library/connect-to-azure) okuyun. Azure Spring Cloud Service örneğiniz için kullandığınız aboneliğin aynısını seçtiğinizden emin olun.

## <a name="azure-cli-task-templates"></a>Azure CLı görev şablonları
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Yapıtları dağıt

Bir serisini kullanarak projelerinizi derleyebilir ve dağıtabilirsiniz `tasks` . Bu kod parçacığı değişkenleri, uygulamayı derlemek için bir .NET Core görevini ve *. zip* dosyasını dağıtmak Için BIR Azure CLI görevini tanımlar.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Yapıtları dağıt

Bir serisini kullanarak projelerinizi derleyebilir ve dağıtabilirsiniz `tasks` . Bu kod parçacığı ilk olarak, uygulamayı derlemek için bir Maven görevi tanımlar ve ardından Azure Spring Cloud Azure CLı uzantısını kullanarak JAR dosyasını dağıtan ikinci bir görevdir.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Kaynaktan dağıt

Ayrı bir derleme adımı olmadan doğrudan Azure 'a dağıtım yapılabilir.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: ilk Azure Spring Cloud uygulamanızı dağıtın](spring-cloud-quickstart.md)