---
title: Azure Dağıtım Yöneticisi şablonlar ile kullanma
description: Azure kaynaklarını dağıtmak için Azure Deployment Manager ile Resource Manager şablonlarını kullanın.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0ce37a744cdf22beddcef433fca1a64252e1673d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149900"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Öğretici: Azure Dağıtım Yöneticisi sistem durumu denetimi kullanma (Genel Önizleme)

[Azure dağıtım Yöneticisi](./deployment-manager-overview.md)'de sistem durumu denetimini tümleştirmeyi öğrenin. Bu öğretici, [Kaynak Yöneticisi şablonları Ile Azure dağıtım Yöneticisi kullanma](./deployment-manager-tutorial.md) öğreticisine dayanır. Bu öğreticiye devam etmeden önce bu öğreticiyi tamamlamalısınız.

[Azure dağıtım Yöneticisi kullanma](./deployment-manager-tutorial.md)bölümünde kullanılan dağıtım şablonunda, Kaynak Yöneticisi şablonlarla bir bekleme adımı kullandınız. Bu öğreticide, bekleme adımını bir sistem durumu denetimi adımıyla değiştirirsiniz.

> [!IMPORTANT]
> Aboneliğiniz yeni Azure özelliklerini test etmek üzere işaretlenmişse, Azure Dağıtım Yöneticisi 'yi yalnızca Canary bölgelerine dağıtmak için kullanabilirsiniz. 

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Durum denetimi hizmeti simülatörü oluşturma
> * Piyasaya çıkma şablonunu gözden geçirin
> * Topolojiyi dağıtma
> * Dağıtımı sağlıksız durumuyla dağıt
> * Dağıtım dağıtımını doğrulama
> * Dağıtımı sağlıklı durumla dağıtma
> * Dağıtım dağıtımını doğrulama
> * Kaynakları temizleme

Ek kaynaklar:

* [Azure Dağıtım Yöneticisi REST API başvurusu](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Azure dağıtım Yöneticisi örneği](https://github.com/Azure-Samples/adm-quickstart).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Azure Dağıtım Yöneticisi kaynak yöneticisi şablonlarla kullanın](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Yapıtları yükler

[Şablonları ve yapıtları](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) indirin ve bunu yapmadıysanız yerel olarak ayıklayın. Ve sonra [yapıtları hazırlama](./deployment-manager-tutorial.md#prepare-the-artifacts)sırasında bulunan PowerShell betiğini çalıştırın. Betik bir kaynak grubu oluşturur, bir depolama kapsayıcısı oluşturur, bir blob kapsayıcısı oluşturur, indirilen dosyaları karşıya yükler ve ardından bir SAS belirteci oluşturur.

SAS belirteci ile URL 'nin bir kopyasını oluşturun. İki parametre dosyasındaki bir alanı, topoloji parametreleri dosyasını ve dağıtım parametreleri dosyasını doldurmak için bu URL gereklidir.

CreateADMServiceTopology. Parameters. json dosyasını açın ve **ProjectName** ve **Artifactsourcesaslocation**değerlerini güncelleştirin.

Createadmpiyasaya çıkma. Parameters. json dosyasını açın ve **ProjectName** ve **Artifactsourcesaslocation**değerlerini güncelleştirin.

## <a name="create-a-health-check-service-simulator"></a>Durum denetimi hizmeti simülatörü oluşturma

Üretimde, genellikle bir veya daha fazla izleme sağlayıcısı kullanırsınız. Sistem durumu tümleştirmesini mümkün olduğunca kolay hale getirmek için, Microsoft, en önemli hizmet durumu izleme şirketleriyle birlikte çalışarak dağıtımlarınızla durum denetimlerini tümleştirmek üzere basit bir kopyalama/yapıştırma çözümü sağlar. Bu şirketlerin listesi için bkz. [sistem durumu izleme sağlayıcıları](./deployment-manager-health-check.md#health-monitoring-providers). Bu öğreticinin amacı doğrultusunda, bir sistem durumu izleme hizmetinin benzetimini yapmak için bir [Azure işlevi](/azure/azure-functions/) oluşturursunuz. Bu işlev bir durum kodu alır ve aynı kodu döndürür. Azure Dağıtım Yöneticisi şablonunuz dağıtım ile devam etmek için durum kodunu kullanır.

Azure Işlevini dağıtmak için aşağıdaki iki dosya kullanılır. Öğreticiye gitmek için bu dosyaları indirmeniz gerekmez.

* [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)konumunda bulunan bir kaynak yöneticisi şablonu. Bu şablonu, bir Azure Işlevi oluşturmak için dağıtırsınız.
* Azure Işlevi kaynak kodunun bir ZIP dosyası [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip). Çağrılan bu zip Kaynak Yöneticisi şablonu tarafından çağırılır.

Azure işlevini dağıtmak için **dene** ' yi seçerek Azure Cloud Shell 'i açın ve ardından aşağıdaki betiği kabuk penceresine yapıştırın.  Kodu yapıştırmak için kabuk penceresine sağ tıklayıp **Yapıştır**' ı seçin.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Azure işlevini doğrulamak ve test etmek için:

1. [Azure portalı](https://portal.azure.com) açın.
1. Kaynak grubunu açın.  Varsayılan ad, **RG** eklenmiş proje adıdır.
1. Kaynak grubundan App Service ' i seçin.  App Service 'in varsayılan adı, **WebApp** eklenen proje adıdır.
1. **İşlevler**' i genişletin ve ardından **HttpTrigger1**' ı seçin.

    ![Azure Dağıtım Yöneticisi sistem durumu denetimi Azure Işlevi](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. **&lt;/> işlev URL 'Sini al**' ı seçin.
1. URL 'YI panoya kopyalamak için **Kopyala** ' yı seçin.  URL şuna benzer:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    URL 'deki `{healthStatus}` bir durum kodu ile değiştirin. Bu öğreticide, sağlıksız senaryoyu test etmek için **sağlıksız** kullanın ve sağlıklı senaryoyu test etmek için **sağlıklı** ya da **uyarıyı** kullanın. Biri sağlıksız durum ve diğeri sağlıklı durumda olan iki URL oluşturun. Örnekler için:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Bu öğreticinin tamamlanması için her iki URL 'ye de ihtiyacınız vardır.

1. Sistem durumu izleme simülatörünü test etmek için, son adımda oluşturduğunuz URL 'Leri açın.  Sağlıksız durum sonuçları şuna benzer olacaktır:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Piyasaya çıkma şablonunu gözden geçirin

Bu bölümün amacı, dağıtım şablonunda bir sistem durumu denetimi adımının nasıl ekleneceğini gösterir.

1. [Azure dağıtım Yöneticisi 'de Kaynak Yöneticisi şablonlarla](./deployment-manager-tutorial.md)oluşturduğunuz **createadmpiyasaya dağıtımı. JSON** dosyasını açın. Bu JSON dosyası, indirmenin bir parçasıdır.  [Ön koşullara](#prerequisites) bakın.
1. İki parametre daha ekleyin:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Bekleme adımı kaynak tanımını bir sistem durumu denetimi adımı kaynak tanımıyla değiştirin:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Tanım temelinde, dağıtım sistem durumu *sağlıklı* veya *Uyarı*olursa devam eder.

1. Yeni tanımlanan sistem durumu denetimi adımını dahil etmek için, piyasaya çıkma tanımının **bağımlılığını** güncelleştirin:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. **StepGroups** durumunu, sistem durumu denetimi adımını içerecek şekilde güncelleştirin. **Healthcheckstep** , **StepGroup2**'in **postdeploymentsteps** içinde çağırılır. **stepGroup3** ve **stepGroup4** yalnızca sağlıklı durum *sağlıklı* veya *Uyarı*olduğunda dağıtılır.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    **StepGroup3** bölümünü, yeniden güncelleştirildikten önce ve sonra karşılaştırırsanız, bu bölüm artık **stepGroup2**'e bağımlıdır.  Bu, **stepGroup3** ve sonraki adım grupları sistem durumu izlemenin sonuçlarına bağlı olduğunda gereklidir.

    Aşağıdaki ekran görüntüsünde, değiştirilen bölgeler ve sistem durumu denetimi adımının nasıl kullanıldığı gösterilmektedir:

    ![Azure Dağıtım Yöneticisi sistem durumu denetimi şablonu](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Topolojiyi dağıtma

Topolojiyi dağıtmak için aşağıdaki PowerShell betiğini çalıştırın. [Azure Dağıtım Yöneticisi kaynak yöneticisi şablonlarıyla](./deployment-manager-tutorial.md)kullandığınız **createadmservicetopology. JSON** ve **Createadmservicetopology. Parameters. JSON** aynı olmalıdır.

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Azure portalı kullanarak hizmet topolojisinin ve temel kaynakların başarıyla oluşturulduğunu doğrulayın:

![Azure Deployment Manager öğreticisi dağıtılan hizmet topolojisi kaynakları](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Kaynakları görmek için **Gizli türleri göster** seçeneği belirlenmelidir.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Dağıtımı sağlıksız durumuyla dağıtma

[Durum denetimi hizmeti simülatörü oluşturma](#create-a-health-check-service-simulator)bölümünde oluşturduğunuz sağlıksız durum URL 'sini kullanın. Düzeltilmiş **Createadmservicetopology. JSON** ve [Kaynak Yöneticisi şablonlarla Azure dağıtım Yöneticisi kullanırken](./deployment-manager-tutorial.md)kullandığınız **Createadmservicetopology. Parameters. JSON** ' a ihtiyacınız vardır.

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` zaman uyumsuz bir çağrıdır. Başarı iletisi yalnızca dağıtımın başarıyla başladığını gösterir. Dağıtımı doğrulamak için `Get-AZDeploymentManagerRollout`kullanın.  Sonraki yordama bakın.

Aşağıdaki PowerShell betiğini kullanarak dağıtım ilerlemesini denetlemek için:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Aşağıdaki örnek çıktı, sağlıksız durum nedeniyle dağıtımın başarısız olduğunu gösterir:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Dağıtım tamamlandıktan sonra, Batı ABD için oluşturulmuş bir ek kaynak grubu görürsünüz.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Dağıtımı sağlıklı durumuyla dağıtma

Dağıtımı sağlıklı durum URL 'SI ile yeniden dağıtmak için bu bölümü tekrarlayın.  Dağıtım tamamlandıktan sonra, Doğu ABD için bir kaynak grubunun oluşturulduğunu daha görürsünüz.

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalı](https://portal.azure.com) açın.
2. Piyasaya çıkarma dağıtımı tarafından oluşturulan yeni kaynak gruplarındaki yeni oluşturulan web uygulamalarına gidin.
3. Web uygulamasını bir web tarayıcıda açın. Index.html dosyasında konumu ve sürümü doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. Bu öğreticide oluşturulan kaynak gruplarını daraltmak için **Ada göre filtrele** alanını kullanın. 3-4 adet olacaktır:

    * **&lt;projectName > RG**: Dağıtım Yöneticisi kaynaklarını içerir.
    * **&lt;projectName > ServiceWUSrg**: ServiceWUS tarafından tanımlanan kaynakları içerir.
    * **&lt;projectName > ServiceEUSrg**: ServiceEUS tarafından tanımlanan kaynakları içerir.
    * Kullanıcı tanımlı yönetilen kimlik için kaynak grubu.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.
5. Bu öğretici tarafından oluşturulan diğer kaynak gruplarını silmek için son iki adımı tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Dağıtım Yöneticisi sistem durumu denetimi özelliğini kullanmayı öğrendiniz. Daha fazla bilgi edinmek için bkz. [Azure Resource Manager belgeleri](/azure/azure-resource-manager/).
