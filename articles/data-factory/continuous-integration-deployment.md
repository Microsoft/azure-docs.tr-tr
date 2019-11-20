---
title: Azure Data Factory sürekli tümleştirme ve teslim
description: Data Factory işlem hatlarını bir ortamdan (geliştirme, test, üretim) diğerine taşımak için sürekli tümleştirme ve teslimi nasıl kullanacağınızı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680023"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory 'de sürekli tümleştirme ve teslim (CI/CD)

## <a name="overview"></a>Genel Bakış

Sürekli tümleştirme, her bir değişikliğin kod tabanınızda otomatik olarak ve mümkün olduğunca erken test edilmesine yönelik bir uygulamadır. Sürekli teslim, sürekli tümleştirme sırasında gerçekleşen testi izler ve bir hazırlama veya üretim sistemine değişiklikleri gönderir.

Azure Data Factory, sürekli tümleştirme & teslimi, Data Factory işlem hatlarını bir ortamdan (geliştirme, test, üretim) diğerine taşıma anlamına gelir. Sürekli tümleştirme & teslimi yapmak için Data Factory UX tümleştirmesini Azure Resource Manager şablonlarıyla kullanabilirsiniz. Data Factory UX **ARM şablon** açılan listesinden bir kaynak yöneticisi şablonu oluşturabilir. **ARM şablonunu dışarı aktar**' ı seçtiğinizde, portal veri fabrikası için Kaynak Yöneticisi şablonu ve tüm bağlantı dizelerinizi ve diğer parametreleri içeren bir yapılandırma dosyası oluşturur. Ardından, her ortam için bir yapılandırma dosyası oluşturmanız gerekir (geliştirme, test, üretim). Ana Kaynak Yöneticisi Şablon dosyası tüm ortamlar için aynı kalır.

Bu özelliği dokuz dakikalık bir giriş ve tanıtım için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Sürekli tümleştirme yaşam döngüsü

Aşağıda, Azure Repos git ile yapılandırılmış bir Azure Data Factory 'de sürekli tümleştirme ve teslim yaşam döngüsüne örnek bir genel bakış sunulmaktadır. Git deposunu yapılandırma hakkında daha fazla bilgi için bkz. [Azure Data Factory kaynak denetimi](source-control.md).

1.  Bir geliştirme Veri Fabrikası, tüm geliştiricilerin işlem hatları ve veri kümeleri gibi Data Factory kaynakları yazma iznine sahip olduğu Azure Repos git ile oluşturulur ve yapılandırılır.

1.  Geliştiriciler Özellik dalında değişiklikler yaparken, işlem hattı çalıştırmalarıyla ilgili en son değişikliklerle hata ayıklaması yapar. İşlem hattı çalıştırmasında hata ayıklama hakkında daha fazla bilgi için, bkz. [Azure Data Factory yinelemeli geliştirme ve hata ayıklama](iterative-development-debugging.md).

1.  Geliştiriciler yaptıkları değişikliklerle karşılandıktan sonra, yaptıkları değişikliklerin eşler tarafından gözden geçirilmesini sağlamak için özellik dalından ana veya işbirliği dalına bir çekme isteği oluşturur.

1.  Çekme isteği onaylandıktan ve değişiklikler ana dalda birleştirildikten sonra, geliştirme fabrikasında yayımlayabilirler.

1.  Takım değişiklikleri test fabrikasına ve ardından üretim fabrikasına dağıtmaya hazırsa, Kaynak Yöneticisi şablonunu ana daldan dışarı aktarırlar.

1.  Dışarıya aktarılmış Kaynak Yöneticisi şablonu, test fabrikasına ve üretim fabrikasına farklı parametre dosyaları ile dağıtılır.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Her ortam için bir Kaynak Yöneticisi şablonu oluşturma

**ARM şablon** açılan menüsünde, geliştirme ortamında veri fabrikanızın Kaynak Yöneticisi şablonunu dışarı aktarmak için **ARM şablonunu dışarı aktar** ' ı seçin.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Test ve üretim verileri fabrikalarınız içinde **ARM şablonunu Içeri aktar**' ı seçin. Bu eylem, dışarı aktarılan şablonu içeri aktarabileceğiniz Azure portal sizi yönlendirir. Kaynak Yöneticisi Şablon düzenleyicisini açmak için **düzenleyicide kendi şablonunuzu oluşturun öğesini** seçin.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

**Dosya Yükle** ' ye tıklayın ve oluşturulan kaynak yöneticisi şablonunu seçin.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Ayarlar bölmesinde, bağlı hizmet kimlik bilgileri gibi yapılandırma değerlerini girin. İşiniz bittiğinde Kaynak Yöneticisi şablonunu dağıtmak için **satın al** ' a tıklayın.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Bağlantı dizeleri

Bağlantı dizelerini yapılandırma hakkında bilgiler, her bağlayıcının makalesinde bulunabilir. Örneğin, Azure SQL veritabanı için, bkz. [Azure Data Factory kullanarak Azure SQL veritabanına veri kopyalama](connector-azure-sql-database.md). Bir bağlantı dizesini doğrulamak için Data Factory UX içindeki kaynak için kod görünümünü açabilirsiniz. Kod görünümünde, bağlantı dizesinin parola veya hesap anahtarı kısmı kaldırılır. Kod görünümünü açmak için aşağıdaki ekran görüntüsünde vurgulanan simgeyi seçin.

![Bağlantı dizesini görmek için kod görünümünü açın](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Azure Pipelines sürümleriyle sürekli tümleştirmeyi otomatikleştirin

Aşağıda, bir veri fabrikasının birden çok ortama dağıtımını otomatikleştiren Azure Pipelines bir sürümü ayarlamaya yönelik bir kılavuz verilmiştir.

![Azure Pipelines ile sürekli tümleştirme diyagramı](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Gereksinimler

-    [Azure Resource Manager hizmeti uç noktası](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)kullanılarak Team Foundation Server veya Azure Repos bağlantılı bir Azure aboneliği.

-   Azure Repos git tümleştirmesiyle yapılandırılan Data Factory.

-   Her ortam için gizli dizileri içeren bir [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) .

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines yayını ayarlama

1.  [Azure DevOps kullanıcı deneyiminde](https://dev.azure.com/)Data Factory ile yapılandırılmış projeyi açın.

1.  Sayfanın sol tarafında, işlem **hatları** ' na tıklayın ve ardından **yayınlar**' ı seçin.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **Yeni** işlem hattı ' nı seçin veya mevcut işlem hatlarınız, **Yeni**ve ardından **Yeni yayın işlem hattı**.

1.  **Boş iş** şablonunu seçin.

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **Aşama adı** alanına ortamınızın adını girin.

1.  **Yapıt Ekle**' yi seçin ve Data Factory yapılandırılan depoyu seçin. En son varsayılan sürüme sahip varsayılan dal olarak `adf_publish` seçin.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager Dağıtım görevi ekleyin:

    a.  Aşama görünümünde, **aşama görevlerini görüntüle** bağlantısına tıklayın.

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Yeni bir görev oluşturun. **Azure Kaynak grubu dağıtımı**' nı arayın ve **Ekle**' ye tıklayın.

    c.  Dağıtım görevinde, hedef Data Factory için abonelik, kaynak grubu ve konum ' u seçin ve gerekirse kimlik bilgilerini sağlayın.

    d.  Eylem açılan menüsünde **kaynak grubu oluştur veya Güncelleştir**' i seçin.

    e.  Seç **...** **şablon** alanında. [Her ortam için Resource Manager şablonu oluşturma](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)' daki **ARM şablonunu içeri aktar** adımı aracılığıyla Azure Resource Manager şablonu oluşturma ' yı inceleyin. `adf_publish` dalının `<FactoryName>` klasörde bu dosyayı arayın.

    f.  Seç **...** **şablon parametreleri alanında.** parametre dosyasını seçin. Bir kopya oluşturup oluşturamayacağını veya *Armtemplateparametersforfactory. JSON*varsayılan dosyasını kullandığınızı bağlı olarak doğru dosyayı seçin.

    g.  Seç **...** **şablon parametrelerini geçersiz kıl** alanının yanında, hedef Data Factory için bilgileri girin. Anahtar kasasından gelen kimlik bilgileri için, çift tırnak arasında gizli dizi adını girin. Örneğin, gizli dizi adı `cred1`, değeri için `"$(cred1)"`girin.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. **Artımlı** Dağıtım modunu seçin.

    > [!WARNING]
    > Dağıtım modunu **tümüyle** Seç ' i seçerseniz, hedef kaynak grubundaki tüm kaynaklar da dahil olmak üzere, Kaynak Yöneticisi şablonunda tanımlanmamış olan kaynaklar silinebilir.

1.  Yayın ardışık düzenini kaydedin.

1. Bir yayını tetiklemek için **yayın oluştur** ' a tıklayın.

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault parolaları al

Azure Resource Manager şablonunda geçiş yapmak için gizli dizileri varsa, Azure Pipelines sürümü ile Azure Key Vault kullanmanızı öneririz.

Gizli dizileri ele almanın iki yolu vardır:

1.  Gizli dizileri parametreler dosyasına ekleyin. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Yayımla dalına yüklenen parametreler dosyasının bir kopyasını oluşturun ve anahtar kasasından almak istediğiniz parametrelerin değerlerini aşağıdaki biçimde ayarlayın:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Bu yöntemi kullandığınızda, gizli anahtar, anahtar kasasından otomatik olarak çekilir.

    -   Parametre dosyasının da yayımlama dalında olması gerekir.

1.  Önceki bölümde açıklanan Azure Resource Manager dağıtımından önce bir [Azure Key Vault görevi](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) ekleyin:

    -   **Görevler** sekmesini seçin, yeni bir görev oluşturun, **Azure Key Vault** arayın ve ekleyin.

    -   Key Vault görevinde, anahtar kasasını oluşturduğunuz aboneliği seçin, gerekirse kimlik bilgilerini sağlayın ve ardından anahtar kasasını seçin.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines aracısına izin verme

Doğru izinler yoksa Azure Key Vault görevi bir erişim reddedildi hatasıyla başarısız olabilir. Yayın için günlükleri indirin ve Azure Pipelines aracısına izinler vermek için `.ps1` dosyasını komutuyla bulun. Komutu doğrudan çalıştırabilirsiniz veya birincil KIMLIĞI dosyadan kopyalayabilir ve Azure portal erişim ilkesini el ile ekleyebilirsiniz. **Get** ve **list** , gereken en düşük izinlerdir.

### <a name="update-active-triggers"></a>Etkin Tetikleyicileri Güncelleştir

Etkin Tetikleyicileri güncelleştirmeye çalışırsanız dağıtım başarısız olabilir. Etkin Tetikleyicileri güncelleştirmek için, bunları el ile durdurmanız ve dağıtımdan sonra başlatmanız gerekir. Bunu bir Azure PowerShell görevi aracılığıyla yapabilirsiniz.

1.  Sürümün Görevler sekmesinde bir **Azure PowerShell** görevi ekleyin.

1.  Bağlantı türü olarak **Azure Resource Manager** seçin ve aboneliğinizi seçin.

1.  Komut dosyası türü olarak **satır Içi betik** ' ı seçin ve kodunuzu girin. Aşağıdaki örnek Tetikleyicileri sonlandırır:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Dağıtımdan sonra Tetikleyicileri yeniden başlatmak için benzer adımları (`Start-AzDataFactoryV2Trigger` işleviyle birlikte) izleyebilirsiniz.

> [!IMPORTANT]
> Sürekli tümleştirme ve dağıtım senaryolarında, farklı ortamlarda Integration Runtime türü aynı olmalıdır. Örneğin, geliştirme ortamında *Şirket Içinde barındırılan* bir INTEGRATION RUNTIME (IR) varsa, aynı IR, test ve üretim gibi diğer ortamlarda da *kendi kendine barındırılan* türde olmalıdır. Benzer şekilde, tümleştirme çalışma zamanlarını birden çok aşamada paylaşıyorsanız, tümleştirme çalışma zamanlarını, geliştirme, test ve üretim gibi tüm ortamlarda *bağlanmış* bir şekilde yapılandırmanız gerekir.

#### <a name="sample-prepostdeployment-script"></a>Örnek ön/postdeployment betiği

Aşağıda, dağıtımdan önce Tetikleyicileri durdurmak ve daha sonra Tetikleyicileri yeniden başlatmak için örnek bir komut dosyası verilmiştir. Betik Ayrıca kaldırılan kaynakları silmek için kod içerir. Azure PowerShell 'ın en son sürümünü yüklemek için bkz. [PowerShellGet Ile Windows 'a Azure PowerShell yüklemesi](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla özel parametreleri kullanma

GIT modundaysanız, şablonda parametreli olan özellikleri ve sabit kodlanmış özellikleri ayarlamak için Kaynak Yöneticisi şablonunuzda varsayılan özellikleri geçersiz kılabilirsiniz. Bu senaryolarda varsayılan parameterleştirme şablonunu geçersiz kılmak isteyebilirsiniz:

* Otomatik CI/CD kullanıyorsunuz ve Kaynak Yöneticisi dağıtımı sırasında bazı özellikleri değiştirmek istiyorsunuz, ancak özellikler varsayılan olarak parametreleştirimez.
* Fabrikanızın izin verilen en fazla sayıda parametreye (256) sahip olduğu için varsayılan Kaynak Yöneticisi şablonunun geçersiz olması çok büyük.

Bu koşullar altında, varsayılan parameterleştirme şablonunu geçersiz kılmak için, deponun kök klasöründe *ARM-Template-Parameters-Definition. JSON* adlı bir dosya oluşturun. Dosya adı tam olarak eşleşmelidir. Data Factory, bu dosyayı, yalnızca işbirliği dalından değil, Azure Data Factory portalında yaptığınız herhangi bir daldan okumaya çalışır. Bir özel daldan dosya oluşturabilir veya düzenleyebilirsiniz, burada, Kullanıcı arabirimindeki **ARM şablonunu dışarı aktar** ' ı kullanarak yaptığınız değişiklikleri test edebilirsiniz. Daha sonra, dosyayı işbirliği dalında birleştirebilirsiniz. Dosya bulunamazsa, varsayılan şablon kullanılır.


### <a name="syntax-of-a-custom-parameters-file"></a>Özel parametre dosyasının sözdizimi

Özel parametreler dosyasını yazarken kullanabileceğiniz bazı yönergeler aşağıda verilmiştir. Dosya her varlık türü için bir bölümden oluşur: tetikleyici, işlem hattı, bağlı hizmet, veri kümesi, tümleştirme çalışma zamanı vb.
* İlgili varlık türünün altında özellik yolunu girin.
* '\*' ' olarak bir özellik adı belirlediğinizde, altındaki tüm özellikleri parametreleştirmek istediğinizi (özyinelemeli değil, yalnızca ilk düzeye doğru değil) belirtmeniz gerektiğini belirtirsiniz. Bunun için özel durumlar da sağlayabilirsiniz.
* Bir özelliğin değerini bir dize olarak belirlediğinizde, özelliği parametreleştirmek istediğinizi belirtirsiniz.  `<action>:<name>:<stype>`biçimini kullanın.
   *  `<action>` şu karakterlerden biri olabilir:
      * `=` , geçerli değeri parametresi için varsayılan değer olarak tutacağı anlamına gelir.
      * `-` , parametresi için varsayılan değeri saklama anlamına gelir.
      * `|` , bağlantı dizeleri veya anahtarlar için Azure Key Vault parolalar için özel bir durumdur.
   * `<name>` , parametrenin adıdır. Boşsa, özelliğin adını alır. Değer bir `-` karakteriyle başlıyorsa, ad kısaltılmıştır. Örneğin, `AzureStorage1_properties_typeProperties_connectionString` `AzureStorage1_connectionString`kısaltıldı.
   * `<stype>` parametrenin türüdür.  `<stype>` boş ise, varsayılan tür `string`olur. Desteklenen değerler: `string`, `bool`, `number`, `object`ve `securestring`.
* Tanım dosyasında bir dizi belirttiğinizde, şablondaki eşleşen özelliğin bir dizi olduğunu belirteirsiniz. Data Factory, dizinin Integration Runtime nesnesinde belirtilen tanımı kullanarak dizideki tüm nesneler arasında yinelenir. İkinci nesne, bir dize, her yineleme için parametresinin adı olarak kullanılan özelliğin adı olur.
* Kaynak örneğine özgü bir tanım olması mümkün değildir. Herhangi bir tanım, bu türdeki tüm kaynaklar için geçerlidir.
* Varsayılan olarak, Key Vault gizli dizileri gibi tüm güvenli dizeler ve bağlantı dizeleri, anahtarlar ve belirteçler gibi güvenli dizeler parametrelenir.
 
### <a name="sample-parameterization-template"></a>Örnek Parametreleştirme şablonu

Aşağıda, bir Parametreleştirme şablonunun nasıl görünebileceklerini bir örnek verilmiştir:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Aşağıda, yukarıdaki şablonun nasıl oluşturulduğu ve kaynak türüne göre bölündüğü hakkında bir açıklama verilmiştir.

#### <a name="pipelines"></a>İşlem hatları
    
* Activities/typeProperties/Waittimeınseconds yolundaki herhangi bir özellik parametreleştiriledir. `waitTimeInSeconds` adlı bir işlem hattındaki herhangi bir etkinlik (örneğin, `Wait` etkinliği), varsayılan bir ada sahip bir sayı olarak parametrelenir. Ancak Kaynak Yöneticisi şablonunda varsayılan bir değere sahip olmaz. Kaynak Yöneticisi dağıtımı sırasında zorunlu bir giriş olacaktır.
* Benzer şekilde, `headers` adlı bir Özellik (örneğin, bir `Web` etkinliğinde), tür `object` (JObject) ile parametrelenir. Kaynak fabrikası ile aynı değer olan varsayılan bir değere sahiptir.

#### <a name="integrationruntimes"></a>Tümleştirme çalışma zamanları

* `typeProperties` yolu altındaki tüm özellikler, kendi varsayılan değerleriyle parametreleştirilenir. Örneğin, **ıntegrationçalışma zamanları** türü Özellikler altında iki özellik vardır: `computeProperties` ve `ssisProperties`. Her iki özellik türü de ilgili varsayılan değerleri ve türleri (nesne) ile oluşturulur.

#### <a name="triggers"></a>Tetikleyiciler

* `typeProperties`altında iki özellik parametrelenir. Birincisi, varsayılan bir değere sahip ve`string`türünde olan `maxConcurrency`. `<entityName>_properties_typeProperties_maxConcurrency`varsayılan parametre adına sahiptir.
* `recurrence` özelliği de parametreleştirilenir. Bu düzeyin altında, bu düzeydeki tüm özellikler, varsayılan değerler ve parametre adlarıyla dize olarak parametreleştirime olarak belirtilir. Bir özel durum, sayı türü olarak parametreleştirilen ve `<entityName>_properties_typeProperties_recurrence_triggerSuffix`parametre adı ile düzeltilen `interval` özelliğidir. Benzer şekilde, `freq` özelliği bir dizedir ve bir dize olarak parametrelenir. Ancak, `freq` özelliği varsayılan değer olmadan parametrelenir. Ad kısaltılmıştır ve Sonya düzeltildi. Örneğin, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Bağlı hizmetler benzersizdir. Bağlı hizmetler ve veri kümelerinin çok sayıda türü olduğundan, türe özgü özelleştirme sağlayabilirsiniz. Bu örnekte, `AzureDataLakeStore`türündeki tüm bağlı hizmetler, belirli bir şablon uygulanır ve diğerleri için (\*aracılığıyla) farklı bir şablon uygulanır.
* `connectionString` özelliği bir `securestring` değeri olarak parametrelendirilecektir, varsayılan bir değere sahip olmaz ve bu, `connectionString`ile Sonekli bir kısaltılmış parametre adına sahip olur.
* Özellik `secretAccessKey` `AzureKeyVaultSecret` (örneğin, `AmazonS3` bağlantılı bir hizmette) olur. Otomatik olarak Azure Key Vault gizli dizi olarak parametrelenir ve yapılandırılan anahtar kasasından alınır. Ayrıca, anahtar kasasının kendisini parametreleştirebilirsiniz.

#### <a name="datasets"></a>Veri kümeleri

* Veri kümeleri için türe özgü özelleştirme kullanılabilir olsa da, yapılandırma açıkça \*düzeyinde bir yapılandırmaya sahip olmadan sağlanabilmelidir. Yukarıdaki örnekte, `typeProperties` altındaki tüm veri kümesi özellikleri parametrelenir.

### <a name="default-parameterization-template"></a>Varsayılan parameterleştirme şablonu

Geçerli varsayılan parameterleştirme şablonu aşağıda verilmiştir. Yalnızca bir veya birkaç parametre eklemeniz gerekiyorsa, var olan parameterleştirme yapısını kaybetmemeniz durumunda bu doğrudan düzenlenmeniz yararlı olabilir.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Varsayılan parameterleştirme şablonuna tek bir değer eklemenin bir örneği aşağıda verilmiştir. Yalnızca bir Databricks bağlı hizmeti için parametreler dosyasına mevcut bir Databricks etkileşimli küme KIMLIĞI eklemek istiyoruz. Aşağıdaki dosya, `Microsoft.DataFactory/factories/linkedServices`Özellikler alanında yer alan `existingClusterId` hariç, yukarıdaki dosyayla aynıdır.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Bağlı Kaynak Yöneticisi şablonları

Veri fabrikalarınız için sürekli tümleştirme ve dağıtım (CI/CD) ayarladıysanız, fabrikanızın daha Büyük büyüdüğü için Azure Resource Manager şablon limitleriyle karşılaşabilirsiniz. Sınır örneği bir Kaynak Yöneticisi şablonundaki en fazla kaynak sayısıdır. Büyük fabrikalara uyum sağlamak için, bir fabrika için tam Kaynak Yöneticisi şablonu oluşturmaya, Data Factory artık bağlantılı Kaynak Yöneticisi şablonları oluşturuyor. Bu özellik sayesinde, tüm fabrika yükü birçok dosyaya bölünür ve bu sayede sınırlara devam edersiniz.

Git yapılandırdıysanız, bağlantılı şablonlar oluşturulur ve `linkedTemplates`adlı yeni bir klasör altındaki `adf_publish` dalındaki tam Kaynak Yöneticisi şablonlarıyla birlikte kaydedilir.

![Bağlı Kaynak Yöneticisi şablonları klasörü](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Bağlantılı Kaynak Yöneticisi şablonları genellikle ana şablona ve ana şablona bağlı bir alt şablonlar kümesine sahiptir. Üst şablon `ArmTemplate_master.json`olarak adlandırılır ve alt şablonlar `ArmTemplate_0.json`, `ArmTemplate_1.json`vb. ile adlandırılır. Tam Kaynak Yöneticisi şablonu yerine bağlantılı şablonlar kullanmak için, CI/CD görevinizi `ArmTemplateForFactory.json` (tam Kaynak Yöneticisi şablonu) yerine `ArmTemplate_master.json` işaret etmek üzere güncelleştirin. Kaynak Yöneticisi ayrıca, dağıtım sırasında Azure tarafından erişilebilmeleri için bağlantılı şablonları bir depolama hesabına yüklemenizi gerektirir. Daha fazla bilgi için bkz. [VSTS Ile bağlantılı ARM şablonlarını dağıtma](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Dağıtım görevinden önce ve sonra, CI/CD işlem hattınızda Data Factory betikleri eklemeyi unutmayın.

Git yapılandırılmamışsa, bağlantılı şablonlara **ARM şablonu dışarı aktarma** hareketini kullanarak erişilebilir.

## <a name="hot-fix-production-branch"></a>Etkin düzeltme üretim Dalı

Bir üretime fabrika dağıtımı yaptıysanız ve hemen düzeltilmesi gereken bir hata olduğunu fark ederseniz, ancak geçerli işbirliği dalını dağıtamazsınız, bir sık düzeltme dağıtmanız gerekebilir. Bu yaklaşım, hızlı çözüm Mühendisliği veya QFE olarak bilinir. 

1.  Azure DevOps 'da üretime dağıtılan sürüme gidin ve dağıtılan son yürütmeyi bulun.

2.  Tamamlama iletisinden işbirliği dalının kayıt KIMLIĞINI alın.

3.  Bu işlemeden yeni bir etkin düzeltme dalı oluşturun.

4.  Azure Data Factory UX 'e gidin ve bu dala geçiş yapın.

5.  Azure Data Factory UX kullanarak hatayı düzeltemedi. Değişikliklerinizi test edin.

6.  Düzeltme doğrulandıktan sonra, etkin düzeltme Kaynak Yöneticisi şablonunu almak için **ARM şablonunu dışarı aktar** ' a tıklayın.

7.  Bu derlemeyi adf_publish dalına el ile iade edin.

8.  Yayın işlem hattınızı adf_publish iadelerine göre otomatik olarak tetiklemek üzere yapılandırdıysanız, yeni bir sürüm otomatik olarak başlatılır. Aksi takdirde, bir yayını el ile sıraya alın.

9.  Test ve üretim fabrikasına sık düzeltme yayını dağıtın. Bu sürüm, önceki üretim yükünü ve 5. adımda yapılan çözümü içerir.

10. Daha sonraki sürümlerin aynı hata içinde çalıştırılmaması için, sık düzeltme ile geliştirme dalına değişiklikler ekleyin.

## <a name="best-practices-for-cicd"></a>CI/CD için en iyi yöntemler

Data Factory 'niz ile git tümleştirmesi kullanıyorsanız ve değişikliklerinizi geliştirmeden teste ve daha sonra üretime taşıyan bir CI/CD işlem hattına sahipseniz, aşağıdaki en iyi yöntemleri öneririz:

-   **Git tümleştirmesi**. Yalnızca geliştirme veri fabrikanızı git tümleştirmesi ile yapılandırmanız gerekir. Test ve üretimde yapılan değişiklikler CI/CD aracılığıyla dağıtılır ve git tümleştirmesi gerekmez.

-   **Data Factory CI/CD betiği**. CI/CD 'deki Kaynak Yöneticisi dağıtım adımından önce, Tetikleyiciler ve temizleme işlemleri gibi belirli görevler gereklidir. Dağıtımdan önce ve sonra PowerShell betikleri kullanmanızı öneririz. Daha fazla bilgi için bkz. [etkin Tetikleyicileri güncelleştirme](#update-active-triggers). 

-   **Tümleştirme çalışma zamanları ve paylaşma**. Tümleştirme çalışma zamanları sıklıkla değişmez ve CI/CD 'inizdeki tüm aşamalar arasında benzerdir. Sonuç olarak, Data Factory CI/CD 'nin tüm aşamalarında aynı ada ve aynı tümleştirme çalışma zamanları türüne sahip olmasını bekler. Tümleştirme çalışma zamanlarını tüm aşamalar arasında paylaşmak istiyorsanız, paylaşılan tümleştirme çalışma zamanlarını içeren bir üçlü fabrika kullanmayı düşünün. Bu paylaşılan fabrikası tüm ortamlarınızda bağlantılı tümleştirme çalışma zamanı türü olarak kullanabilirsiniz.

-   **Key Vault**. Azure Key Vault tabanlı bağlı hizmetler kullandığınızda, farklı ortamlar için ayrı anahtar kasaları tutarak daha fazla avantaj sağlayabilirsiniz. Ayrıca, her biri için ayrı izin düzeyleri yapılandırabilirsiniz. Örneğin, ekip üyelerinizin üretim gizli dizileri için izinleri olmasını istemeyebilirsiniz. Bu yaklaşımı izlerseniz, tüm aşamalar genelinde aynı gizli adları tutmanız önerilir. Aynı adı tutarsanız, tek şey, Kaynak Yöneticisi şablonu parametrelerinden biri olan Anahtar Kasası adı olduğundan, bu durumda Kaynak Yöneticisi şablonlarını CI/CD ortamları arasında değiştirmeniz gerekmez.

## <a name="unsupported-features"></a>Desteklenmeyen özellikler

- ADF, tasarıma göre tek tek seçme yürütmelerine veya kaynakların seçmeli yayımlamasına _izin vermez_ . Yayımlar, veri fabrikasında yapılan **Tüm** değişiklikleri içerir

    - Data Factory varlıkları birbirlerine bağlıdır, örneğin, Tetikleyiciler, işlem hatları, işlem hatları, veri kümelerine ve diğer işlem hatlarına bağlıdır, vb. Bir kaynak alt kümesinin seçmeli olarak yayımlanması beklenmeyen davranışlara ve hatalara neden _olabilir_
    - Seçmeli yayımlamanın gerekli olduğu nadir durumlarda, bir sık düzeltme düşünebilirsiniz. Daha fazla bilgi için bkz. [Hot-düzeltme üretim Dalı](#hot-fix-production-branch)

-   Özel dallardan yayımlayamazsınız

-   Şu andan itibaren, Bitbucket üzerinde projeler barındırılamıyor
