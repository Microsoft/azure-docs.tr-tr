---
title: HPC çözümleri derlemek ve dağıtmak için Azure Pipelines kullanın-Azure Batch | Microsoft Docs
description: Azure Batch üzerinde çalışan bir HPC uygulaması için derleme/sürüm ardışık düzeni dağıtmayı öğrenin.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 47665171ee5ae137e0503b3e5fa1d369aeabb356
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840066"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>HPC çözümleri derlemek ve dağıtmak için Azure Pipelines kullanma

Azure DevOps Hizmetleri, özel bir uygulama oluştururken geliştirme ekipleri tarafından kullanılan bir dizi araç sağlar. Azure DevOps tarafından sunulan araçlar, yüksek performanslı işlem çözümlerinin otomatik olarak oluşturulmasına ve test edilmesine çevrilebilir. Bu makalede, Azure Batch üzerinde dağıtılan yüksek performanslı bir işlem çözümü için Azure Pipelines kullanarak bir sürekli tümleştirme (CI) ve sürekli dağıtım (CD) ayarlama işlemi gösterilmektedir.

Azure Pipelines, yazılım oluşturmaya, dağıtmaya, test etmeye ve izlemeye yönelik bir dizi modern CI/CD işlemi sağlar. Bu işlemler, yazılım teslimatını hızlandırarak altyapıyı ve işlemlerini desteklemek yerine kodunuza odaklanmanızı sağlar.

## <a name="create-an-azure-pipeline"></a>Azure işlem hattı oluşturma

Bu örnekte, bir Azure Batch altyapısını dağıtmak ve bir uygulama paketini serbest bırakmak için derleme ve sürüm işlem hattı oluşturacağız. Kodun yerel olarak geliştirildiği varsayıldığında, bu genel dağıtım akışdır:

![İşlem hatmızda dağıtım akışını gösteren diyagram](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Kurulum

Bu makaledeki adımları izlemek için bir Azure DevOps organizasyonu ve bir takım projesi gerekir.

* [Azure DevOps organizasyonu oluşturma](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Azure DevOps 'da proje oluşturma](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Ortamınız için kaynak denetimi

Kaynak denetimi ekiplerin kod temeli üzerinde yapılan değişiklikleri izlemesine ve kodun önceki sürümlerini incelemesine olanak sağlar.

Genellikle, kaynak denetimi, yazılım kodu ile birlikte ele bir şekilde düşünülebilir. Temel alınan altyapı nasıl? Bu, temel altyapınızı bildirimli olarak tanımlamak için Azure Resource Manager şablonlarını veya diğer açık kaynaklı alternatifleri kullanabileceğimizi kod olarak altyapıya getirir.

Bu örnek büyük ölçüde bir dizi Kaynak Yöneticisi şablonu (JSON belgeleri) ve var olan ikilileri kullanır. Bu örnekleri deponuza kopyalayabilir ve bunları Azure DevOps 'a gönderebilirsiniz.

Bu örnekte kullanılan CODEBASE yapısı aşağıdakine benzer;

* Bir **ARM-Templates** klasörü, bir dizi Azure Resource Manager şablonu içerir. Şablonlar Bu makalede açıklanmıştır.
* [FFmpeg örneğiyle Azure Batch .NET dosya işlemenin](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) bir kopyası olan bir **istemci uygulaması** klasörü. Bu makale için bu gerekli değildir.
* [FFmpeg 3,4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)Windows 64 bit sürümü olan **HPC-Application** klasörü.
* İşlem **hatları** klasörü. Bu, yapı sürecimiz için bir YAML dosyası içerir. Bu makalede ele alınmıştır.

Bu bölüm, sürüm denetimi ve Kaynak Yöneticisi şablonlarını tasarlama hakkında bilgi sahibi olduğunuzu varsayar. Bu kavramları bilmiyorsanız, daha fazla bilgi için aşağıdaki sayfalara bakın.

* [Kaynak denetimi nedir?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Azure Resource Manager şablonlarının yapısını ve söz dizimini anlama](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

Bu örnek, çözümümüzü dağıtmak için birden çok Kaynak Yöneticisi şablonunu kullanır. Bunu yapmak için belirli bir işlev parçasını uygulayan çok sayıda yetenek şablonu (birimlere veya modüllerle benzer) kullanacağız. Ayrıca, bu temel özellikleri bir araya getirmekten sorumlu olan uçtan uca bir çözüm şablonu kullanıyoruz. Bu yaklaşımın birkaç avantajı vardır:

* Temel alınan yetenek şablonları ayrı ayrı birim test edilebilir.
* Temel özellik şablonları bir kuruluşun içinde standart olarak tanımlanabilir ve birden çok çözümde yeniden kullanılabilir.

Bu örnekte, üç şablon dağıtan bir uçtan uca çözüm şablonu (Deployment. JSON) vardır. Temel şablonlar, çözümün belirli bir yönlerini dağıtmaktan sorumlu olan özellik şablonlarıdır.

![Azure Resource Manager şablonları kullanarak bağlantılı şablon yapısına örnek](media/batch-ci-cd/ARMTemplateHierarchy.png)

Bir Azure depolama hesabı için bakacağız ilk şablon. Çözümünüz, uygulamayı Batch hesabımızda dağıtmak için bir depolama hesabı gerektirir. Depolama hesapları için Kaynak Yöneticisi şablonları oluştururken [Microsoft. Storage kaynak türleri için Kaynak Yöneticisi şablonu Başvuru Kılavuzu ' nu](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) bilmeniz önemlidir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Daha sonra Azure Batch hesap şablonuna bakacağız. Azure Batch hesabı, havuzlarda (makine gruplandırmaları) çok sayıda uygulama çalıştırmak için bir platform görevi görür. Batch hesapları için Kaynak Yöneticisi şablonları oluştururken [Microsoft. Batch kaynak türleri için Kaynak Yöneticisi şablonu Başvuru Kılavuzu ' nu](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) bilmeniz önemlidir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Sonraki şablonda bir Azure Batch Havuzu (uygulamalarımızı işlemek için arka uç makineler) oluşturma örneği gösterilmektedir. Batch hesap havuzları için Kaynak Yöneticisi şablonları oluştururken [Microsoft. Batch kaynak türleri için Kaynak Yöneticisi şablonu Başvuru Kılavuzu ' nu](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) bilmeniz önerilir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Son olarak, bir Orchestrator ile benzer şekilde davranan bir şablonumuz vardır. Bu şablon, yetenek şablonlarının dağıtılmasından sorumludur.

Ayrıca, [bağlı Azure Resource Manager şablonlarını](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) ayrı bir makalede oluşturma hakkında daha fazla bilgi edinebilirsiniz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>HPC çözümü

Altyapı ve yazılım, kod olarak tanımlanabilir ve aynı depoda eklenebilir.

Bu çözüm için, FFmpeg uygulama paketi olarak kullanılır. FFmpeg paketi [buradan](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)indirilebilir.

![Örnek git deposu yapısı](media/batch-ci-cd/git-repository.jpg)

Bu deponun dört ana bölümü vardır:

* Altyapımızı kod olarak depolayan **ARM-Templates** klasörü
* FFmpeg için ikili dosyaları içeren **HPC-Application** klasörü
* Yapı işlem hatmız için tanımı içeren işlem **hatları** klasörü.
* **Isteğe bağlı**: .NET uygulaması için kod depolayacak olan **istemci-uygulama** klasörü. Bunu örnekte kullanmayın, ancak kendi projenizde, bir istemci uygulaması aracılığıyla HPC Batch uygulamasının çalıştırmalarını yürütmek isteyebilirsiniz.

> [!NOTE]
> Bu, bir kod temelinin yapısına yalnızca bir örnektir. Bu yaklaşım, uygulamanın, altyapının ve işlem hattı kodunun aynı depoda depolandığını gösteren amaçlar için kullanılır.

Kaynak kodu ayarlandığına göre, ilk derlemeyi başlayabiliriz.

## <a name="continuous-integration"></a>Sürekli tümleştirme

Azure DevOps Services içinde [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), uygulamalarınız için bir derleme, test ve dağıtım işlem hattı uygulamanıza yardımcı olur.

İşlem hattının bu aşamasında, testler genellikle kodu doğrulamak ve yazılımın uygun parçalarını derlemek için çalıştırılır. Testlerin sayısı ve türleri ve çalıştırdığınız ek görevler, daha geniş derleme ve yayın stratejinize göre değişir.

## <a name="preparing-the-hpc-application"></a>HPC uygulaması hazırlanıyor

Bu örnekte, **HPC-Application** klasörüne odaklanacağız. **HPC-Application** klasörü, Azure Batch hesabının içinden çalıştırılacak FFmpeg yazılımıdır.

1. Azure DevOps kuruluşunuzda Azure Pipelines yapılar bölümüne gidin. Yeni bir işlem **hattı**oluşturun.

    ![Yeni bir derleme işlem hattı oluşturun](media/batch-ci-cd/new-build-pipeline.jpg)

1. Derleme işlem hattı oluşturmak için iki seçeneğiniz vardır:

    a. [Görsel tasarımcı kullanma](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Bunu kullanmak için **Yeni işlem hattı** sayfasında "görsel tasarımcıyı kullan" a tıklayın.

    b. [YAML derlemelerini kullanma](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Yeni bir YAML işlem hattı oluşturarak yeni bir işlem hattı sayfasında Azure Repos veya GitHub seçeneğine tıklayabilirsiniz. Alternatif olarak, aşağıdaki örneği, kaynak denetilinizi kullanarak, Visual Designer ' a tıklayıp YAML şablonunu kullanarak var olan bir YAML dosyasına başvurabilirsiniz.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Yapı gerektiğinde yapılandırıldıktan sonra **& kuyruğu kaydet**' i seçin. Sürekli tümleştirme etkinse ( **Tetikleyiciler** bölümünde), depoya yeni bir kayıt yapıldığında derleme sırasında ayarlanan koşullara uyan yapı otomatik olarak tetiklenir.

    ![Var olan bir derleme işlem hattı örneği](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Azure Pipelines **Build** bölümüne giderek, Azure DevOps 'daki yapınızı sürmekte olan canlı güncelleştirmeleri görüntüleyin. Derleme tanımınızdan uygun derlemeyi seçin.

    ![Yapıınızdan canlı çıktıları görüntüleme](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> HPC Batch uygulamanızı yürütmek için bir istemci uygulaması kullanıyorsanız, bu uygulama için ayrı bir derleme tanımı oluşturmanız gerekir. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) belgelerinde çeşitli nasıl yapılır kılavuzlarından ulaşabilirsiniz.

## <a name="continuous-deployment"></a>Sürekli dağıtım

Azure Pipelines Ayrıca uygulamanızı ve temel altyapıyı dağıtmak için de kullanılır. [Yayın işlem hatları](https://docs.microsoft.com/azure/devops/pipelines/release) , sürekli dağıtımı sağlayan ve yayın işleminizi otomatikleştiren bileşendir.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Uygulamanızı ve temel altyapıyı dağıtma

Altyapıyı dağıtmaya yönelik birkaç adım vardır. [Bağlantılı şablonlar](../azure-resource-manager/resource-group-linked-templates.md)kullandığımızda, Bu şablonların ortak bir uç noktadan (http veya https) erişilebilir olması gerekir. Bu bir GitHub veya bir Azure Blob depolama hesabı ya da başka bir depolama konumunda bir depo olabilir. Karşıya yüklenen şablon yapıtları, özel bir modda tutulacağından ve paylaşılan erişim imzası (SAS) belirteci kullanılarak erişilen için güvenli durumda kalabilir. Aşağıdaki örnek, bir Azure Storage blobundan şablonlar içeren bir altyapının nasıl dağıtılacağını göstermektedir.

1. Yeni bir **yayın tanımı**oluşturun ve boş bir tanım seçin. Daha sonra yeni oluşturulan ortamı, işlem hatlarımıza uygun bir şekilde yeniden adlandırmamız gerekir.

    ![İlk yayın işlem hattı](media/batch-ci-cd/Release-0.jpg)

1. HPC uygulamamız için çıktıyı almak üzere derleme ardışık düzeninde bir bağımlılık oluşturun.

    > [!NOTE]
    > Bir kez daha, yayın tanımının içinde görevler oluşturulduğunda gerekli olacağı için **kaynak diğer adına**göz önünde bulunmanız gerekir.

    ![Uygun derleme ardışık düzeninde HPCApplicationPackage için yapıt bağlantısı oluşturma](media/batch-ci-cd/Release-1.jpg)

1. Başka bir yapıtın, bu kez bir Azure deposunun bağlantısını oluşturun. Bu, deponuzda depolanan Kaynak Yöneticisi şablonlarına erişmek için gereklidir. Kaynak Yöneticisi şablonlar derleme gerektirirken, bunları bir derleme işlem hattı aracılığıyla göndermeniz gerekmez.

    > [!NOTE]
    > Bir kez daha, yayın tanımının içinde görevler oluşturulduğunda gerekli olacağı için **kaynak diğer adına**göz önünde bulunmanız gerekir.

    ![Azure Repos yapıt bağlantısı oluşturma](media/batch-ci-cd/Release-2.jpg)

1. **Değişkenler** bölümüne gidin. İşlem hattınızda bir dizi değişken oluşturmanız önerilir, bu nedenle aynı bilgileri birden çok göreve yerleştirmezsiniz. Bunlar, bu örnekte kullanılan değişkenlerdir ve dağıtımı nasıl etkiler.

    * **Applicationstorageaccountname**: HPC uygulama ikililerini barındıracak depolama hesabının adı
    * **Batchaccountapplicationname**: Azure Batch hesabındaki uygulamanın adı
    * **Batchaccountname**: Azure Batch hesabının adı
    * **Batchaccountpoolname**: İşlem yapan VM havuzunun adı
    * **Batchapplicationıd**: Azure Batch uygulamasının benzersiz KIMLIĞI
    * **Batchapplicationversion**: Batch uygulamanızın anlamsal sürümü (yani, FFmpeg ikilileri)
    * **konum**: Dağıtılacak Azure kaynakları için konum
    * **Resourcegroupname**: Oluşturulacak kaynak grubunun adı ve kaynaklarınızın dağıtılacağı yer
    * **storageAccountName**: Bağlı Kaynak Yöneticisi şablonlarını barındıracak depolama hesabının adı

    ![Azure Pipelines yayını için ayarlanan değişkenler örneği](media/batch-ci-cd/Release-4.jpg)

1. Geliştirme ortamı görevlerine gidin. Aşağıdaki anlık görüntüde altı görevi görebilirsiniz. Bu görevler: sıkıştırılmış FFmpeg dosyalarını indirme, iç içe geçmiş Kaynak Yöneticisi şablonlarını barındırmak için bir depolama hesabı dağıtma, bu Kaynak Yöneticisi şablonları depolama hesabına kopyalama, Batch hesabını ve gerekli bağımlılıkları dağıtma, içinde bir uygulama oluşturma Azure Batch hesabı ve uygulama paketini Azure Batch hesabına yükleyin.

    ![Azure Batch HPC uygulamasını serbest bırakmak için kullanılan görevler örneği](media/batch-ci-cd/Release-3.jpg)

1. Indirme işlem **hattı yapıtı (Önizleme)** görevini ekleyin ve aşağıdaki özellikleri ayarlayın:
    * **Görünen ad:** ApplicationPackage 'i aracıya indir
    * **İndirilecek yapıt adı:** HPC-Application
    * **Indirilecek yol**: $ (System. DefaultWorkingDirectory)

1. Yapılarınızı depolamak için bir depolama hesabı oluşturun. Çözümdeki mevcut bir depolama hesabı kullanılabilir, ancak kendi kendine dahil edilen örnek ve yalıtımımız için, yapılarımız için ayrılmış bir depolama hesabı sunuyoruz (özellikle Kaynak Yöneticisi şablonlar).

    **Azure Kaynak grubu dağıtım** görevini ekleyin ve aşağıdaki özellikleri ayarlayın:
    * **Görünen ad:** Kaynak Yöneticisi şablonları için depolama hesabı dağıtma
    * **Azure aboneliği:** Uygun Azure aboneliğini seçin
    * **Eylem**: Kaynak grubu oluşturma veya güncelleştirme
    * **Kaynak grubu**: $ (resourcegroupname)
    * **Konum**: $ (konum)
    * **Şablon**: $ (System. ArtifactsDirectory)/ **{Yourazurerepoartifactsourcealias}** /ARM-Templates/storageaccount.exe
    * **Geçersiz kılma şablonu parametreleri**:-AccountName $ (storageAccountName)

1. Kaynak denetiminden yapıtları depolama hesabına yükleyin. Bunu gerçekleştirmek için bir Azure işlem hattı görevi vardır. Bu görevin bir parçası olarak, depolama hesabı kapsayıcısı URL 'SI ve SAS belirteci, Azure Pipelines bir değişkene alınabilir. Bu, bu aracı aşaması boyunca yeniden kullanılabilen anlamına gelir.

    **Azure dosya kopyalama** görevini ekleyin ve aşağıdaki özellikleri ayarlayın:
    * **Kaynak:** $ (System. ArtifactsDirectory)/ **{Yourazurerepoartifactsourcealias}** /ARM-Templates/
    * **Azure bağlantı türü**: Azure Resource Manager
    * **Azure aboneliği:** Uygun Azure aboneliğini seçin
    * **Hedef türü**: Azure Blob
    * **RM depolama hesabı**: $ (storageAccountName)
    * **Kapsayıcı adı**: Şablonlar
    * **Depolama kapsayıcısı URI 'si**: templatecontaineruri
    * **Depolama KAPSAYıCıSı SAS belirteci**: templatecontainersastoken

1. Orchestrator şablonunu dağıtın. Orchestrator şablonunu daha önce geri çek, SAS belirtecine ek olarak depolama hesabı kapsayıcısı URL 'SI için parametre olduğunu fark edeceksiniz. Kaynak Yöneticisi şablonunda gereken değişkenlerin yayın tanımının değişkenler bölümünde tutulduğuna ya da başka bir Azure Pipelines görevinden (örneğin, Azure Blob kopyalama görevinin bir parçası) ayarlandığını fark etmelisiniz.

    **Azure Kaynak grubu dağıtım** görevini ekleyin ve aşağıdaki özellikleri ayarlayın:
    * **Görünen ad:** Azure Batch dağıt
    * **Azure aboneliği:** Uygun Azure aboneliğini seçin
    * **Eylem**: Kaynak grubu oluşturma veya güncelleştirme
    * **Kaynak grubu**: $ (resourcegroupname)
    * **Konum**: $ (konum)
    * **Şablon**: $ (System. ArtifactsDirectory)/ **{Yourazurerepoartifactsourcealias}** /ARM-Templates/Deployment.exe
    * **Şablon parametrelerini geçersiz kıl**:```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Azure Key Vault görevleri kullanmak yaygın bir uygulamadır. Hizmet sorumlusu (Azure aboneliğinize bağlantı) uygun bir erişim ilkeleri ayarlandıysa, bir Azure Key Vault parolaları indirebilir ve işlem hattınızda değişken olarak kullanılabilir. Gizli anahtar adı, ilişkili değerle ayarlanır. Örneğin, sürüm tanımında sshPassword 'ın gizli anahtarı $ (sshPassword) ile birlikte başvurulmalıdır.

1. Sonraki adımlar Azure CLı 'yı çağırır. İlki Azure Batch bir uygulama oluşturmak için kullanılır. ve ilişkili paketleri karşıya yükleyin.

    **Azure CLI** görevini ekleyin ve aşağıdaki özellikleri ayarlayın:
    * **Görünen ad:** Azure Batch hesapta uygulama oluştur
    * **Azure aboneliği:** Uygun Azure aboneliğini seçin
    * **Betik konumu**: Satır içi betik
    * **Satır Içi betik**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. İkinci adım, ilişkili paketleri uygulamaya yüklemek için kullanılır. Bu durumda, FFmpeg dosyaları.

    **Azure CLI** görevini ekleyin ve aşağıdaki özellikleri ayarlayın:
    * **Görünen ad:** Paketi Azure Batch hesaba yükle
    * **Azure aboneliği:** Uygun Azure aboneliğini seçin
    * **Betik konumu**: Satır içi betik
    * **Satır Içi betik**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Uygulama paketinin sürüm numarası bir değişkene ayarlı. Bu, paketin önceki sürümlerinin üzerine yazılması sizin için işe yarar ve Azure Batch gönderilen paketin sürüm numarasını el ile denetlemek istiyorsanız kullanışlıdır.

1. **Yeni bir yayın oluşturmak > yayın**' i seçerek yeni bir yayın oluşturun. Tetiklendikten sonra, durumu görüntülemek için yeni sürüme yönelik bağlantıyı seçin.

1. Ortamınızın altında **Günlükler** düğmesini seçerek, etkin çıktıyı aracıdan görüntüleyebilirsiniz.

    ![Yayınlarınızın durumunu görüntüleyin](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Ortamı test etme

Ortam kurulduktan sonra, aşağıdaki testlerin başarıyla tamamlandıklarını onaylayın.

PowerShell komut isteminden Azure CLı kullanarak yeni Azure Batch hesabına bağlanın.

* Azure hesabınızda ile `az login` oturum açın ve kimlik doğrulaması için yönergeleri izleyin.
* Şu anda Batch hesabının kimliğini doğrulayın:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Kullanılabilir uygulamaları listeleyin

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Havuzun geçerli olduğunu denetleme

```azurecli
az batch pool list
```

Bu komutun çıktısından değerini `currentDedicatedNodes` aklınızda edin. Bu değer bir sonraki testte ayarlanır.

#### <a name="resize-the-pool"></a>Havuzu yeniden boyutlandır

Havuzu yeniden boyutlandır iş ve görev testi için kullanılabilir işlem düğümleri olacak şekilde, yeniden boyutlandırma tamamlanana ve kullanılabilir düğümler bulunduğundan geçerli durumu görmek için havuz listesi komutuyla denetleyin

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleye ek olarak, .NET ve Python kullanarak FFmpeg kullanan iki öğretici vardır. Basit bir uygulama aracılığıyla Batch hesabıyla etkileşim kurma hakkında daha fazla bilgi için bu öğreticilere bakın.

* [Python API 'sini kullanarak Azure Batch ile paralel iş yükü çalıştırma](tutorial-parallel-python.md)
* [.NET API kullanarak Azure Batch ile paralel iş yükü çalıştırma](tutorial-parallel-dotnet.md)
