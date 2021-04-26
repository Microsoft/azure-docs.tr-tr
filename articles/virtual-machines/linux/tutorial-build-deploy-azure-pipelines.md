---
title: Eğitim-Azure Pipelines kullanarak Azure VM 'lerine CI/CD
description: Bu öğreticide, YAML tabanlı Azure işlem hattını kullanarak bir Node.js uygulamasının Azure VM 'lerine sürekli tümleştirme (CI) ve sürekli dağıtımı (CD) ayarlamayı öğreneceksiniz.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552499"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Öğretici: Azure DevOps Services ve Azure Pipelines kullanarak uygulamanızı Azure 'da Linux sanal makinelerine dağıtın

Sürekli tümleştirme (CI) ve sürekli dağıtım (CD) her kod yürütmesinden sonra kodunuzu oluşturabileceğiniz, yayıntabileceğiniz ve dağıtabileceğiniz bir işlem hattı oluşturur. Bu belge, Azure Pipelines kullanarak çok makineli dağıtımlar gerçekleştirmek için bir CI/CD işlem hattı ayarlama ile ilgili adımları içerir.

Azure Pipelines, hem şirket içi hem de herhangi bir bulutta sanal makinelere dağıtımlar için tam ve tam özellikli bir CI/CD otomasyon araçları kümesi sağlar.

Bu öğreticide bir YAML tabanlı CI/CD işlem hattı ayarlayarak, her biri uygulamayı çalıştırmak için Web sunucusu olarak görev yapacak şekilde Linux sanal makinelerinden oluşan bir Azure Pipelines [ortamına](/azure/devops/pipelines/process/environments) kaynak olarak dağıtırsınız.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Örnek bir uygulama alın.
> * Örnek uygulamayı oluşturmak için bir YAML tabanlı Azure Pipelines CI işlem hattı oluşturun.
> * Azure sanal makineleri için Azure Pipelines ortamı oluşturma
> * Azure Pipelines CD işlem hattı oluşturun.
> * El ile ve CI ile tetiklenen dağıtımlar yürütme.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure DevOps Services kuruluşunuzda () oturum açın **https://dev.azure.com/** . 
  Ücretsiz bir [Azure DevOps Services kuruluşu](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) edinebilirsiniz.

  > [!NOTE]
  > Daha fazla bilgi için, bkz. [Azure DevOps Services’a bağlanma](/azure/devops/organizations/projects/connect-to-projects).

*  Dağıtım hedefi için bir Linux sanal makinesi gerekir.  Daha fazla bilgi için bkz. [Azure CLI ile Linux sanal makineleri oluşturma ve yönetme](./tutorial-manage-vm.md).

*  Sanal makineniz için 80 numaralı gelen bağlantı noktasını açın. Daha fazla bilgi için bkz. [Azure portalını kullanarak ağ güvenlik grupları oluşturma](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Örnek uygulama kodunuzu alın

Zaten GitHub 'da dağıtmak istediğiniz bir uygulamanız varsa, bu kod için bir işlem hattı oluşturmayı deneyebilirsiniz.

Ancak, yeni bir kullanıcılığımızda, örnek kodumuzu kullanarak daha iyi bir başlangıç yapabilirsiniz. Bu durumda, GitHub 'da bu depoyu çatalla:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic, [Maven](https://spring.io/guides/gs/maven/)kullanılarak oluşturulan bir [Java Spring önyükleme](https://spring.io/guides/gs/spring-boot) uygulamasıdır.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Bu Node.js uygulama [Yeumman](https://yeoman.io/learning/index.html)ile oluşturulmuştur. Express, bower ve grunt kullanır. Ayrıca bağımlılıklar olarak bazı npm paketlerini içerir.
> Örnek, Nginx’i ayarlayan ve uygulamayı dağıtan bir betik de içerir. Sanal makinelerde yürütülür. Betik özellikle:
> 1. Node, Nginx ve PM2'yi yükler.
> 2. Nginx ve PM2’yi yapılandırır.
> 3. Düğüm uygulamasını başlatır.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Linux VM önkoşulları

Yukarıda bahsedilen örnek uygulamalar Ubuntu 16,04 ' de test edilmiştir ve bu hızlı başlangıç için aynı Linux VM sürümünü kullanmanızı öneririz.
Uygulama için kullanılan çalışma zamanı yığınına göre aşağıda açıklanan ek adımları izleyin.

#### <a name="java"></a>[Java](#tab/java)

- Java Spring Boot ve Spring bulut tabanlı uygulamaları dağıtmak için, [Bu](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) şablonu kullanarak Azure 'da tümüyle desteklenen bir OpenJDK tabanlı çalışma zamanı sağlayan bir Linux sanal makinesi oluşturun.
- Tomcat sunucusunda Java servi 'leri dağıtmak için, [Bu](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) Azure şablonunu kullanarak Java 8 Ile BIR Linux VM oluşturun ve [bir hizmet olarak Tomcat 9. x 'i yapılandırın](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Java EE tabanlı uygulamayı dağıtmak için bir Azure şablonu kullanarak bir [LINUX VM 'si oluşturun + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) veya Linux VM [+ Java + WebLogic 12. x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) veya Linux VM + Java + bir [Linux sanal makinesi](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + jpatron 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Bir JavaScript uygulaması veya Node.js uygulaması yüklemek için, uygulamayı dağıtmak üzere NGINX web sunucusuna sahip bir Linux VM 'si gerekir.
Zaten NGINX ile bir Linux sanal makinesi yoksa, [Bu örnekteki](./quick-create-cli.md)adımları kullanarak Azure 'da şimdi bir tane oluşturun.

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Azure sanal makinelerle Azure Pipelines ortamı oluşturma

Sanal makineler, [ortamlar](/azure/devops/pipelines/process/environments) içinde kaynak olarak eklenebilir ve çok makineli dağıtımlar için hedeflenebilir. Ortamında dağıtım geçmişi görünümleri VM 'den işlem hattına ve sonra işlemeye izlenebilirlik sağlar.

"**Ortamlar**" hub 'ında "işlem **hatları**" bölümünün içinde bir ortam oluşturabilirsiniz.
1.  Azure DevOps kuruluşunuzda oturum açın ve projenize gidin.
2.  Projenizde, işlem **hatları** sayfasına gidin. Ardından **ortamlar** ' ı seçin ve **Ortam Oluştur**' a tıklayın. Ortam için bir **ad** (gerekli) ve bir **Açıklama** belirtin.
3.  Ortama eklenecek **kaynak** olarak **sanal makineler** ' i seçin ve **İleri**' ye tıklayın.
4.  Işletim sistemi (Windows/Linux) ve **kopya PS kayıt betiğini** seçin. 
5.  Şimdi bu ortama kaydedilecek hedef VM 'lerde bir yönetici PowerShell komut isteminden kopyalanmış betiği çalıştırın.
    > [!NOTE]
    > - Oturum açmış kullanıcının kişisel erişim belirteci, kopyalanmış betiğin kullanılamamasına neden olan bir gün içinde süresi dolan betiğe önceden eklenir.
    > - VM 'niz üzerinde çalışan bir aracı zaten varsa, ortama kaydolmak için "aracı" için benzersiz bir ad sağlayın.
6.  VM kaydedildikten sonra, ortamın "kaynaklar" sekmesinde ortam kaynağı olarak görünmeye başlar.

    ![Vmoluşturma](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Daha fazla VM eklemek için, "Kaynak Ekle" seçeneğine tıklayarak ve kaynak olarak "sanal makineler" i seçerek betiği yeniden görüntüleyebilir ve kopyalayabilirsiniz. Bu komut dosyası, bu ortama eklenecek tüm VM 'Ler için aynı kalır. 
8.  Her makine, uygulamanızın dağıtımını koordine etmek için Azure Pipelines etkileşime girer.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Etkileşimli PowerShell kayıt betiğinin (veya) bir parçası olarak sanal makineye Etiketler ekleyebilirsiniz. Ayrıca, kaynaklar görünümündeki her bir VM kaynağının sonundaki üç noktaya tıklayarak kaynak görünümünden aynı şekilde ekleme/kaldırma yapabilirsiniz.

   Atadığınız Etiketler, ortam bir dağıtım işinde kullanıldığında dağıtımı belirli sanal makinelere sınırlamanıza izin verir. Etiketler 256 karakterle sınırlıdır, ancak kullanabileceğiniz etiket sayısı için bir sınır yoktur.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>CI derleme işlem hattınızı tanımlama

Web uygulamanızı yayımlayan bir sürekli tümleştirme (CI) derleme işlem hattının yanı sıra Ubuntu sunucusunda yerel olarak çalıştırılabilecek bir dağıtım betiği de gerekir. Kullanmak istediğiniz çalışma zamanına göre bir CI derleme işlem hattı ayarlayın. 

1. Azure DevOps kuruluşunuzda oturum açın ve projenize gidin.

1. Projenizde, işlem **hatları** sayfasına gidin. Sonra yeni bir işlem hattı oluşturmak için eylemi seçin.

1. İlk olarak kaynak kodunuzun konumu olarak **GitHub** ' i seçerek sihirbazın adımlarını gözden geçir.

1. Oturum açmak için GitHub 'a yönlendirilirsiniz. Bu durumda, GitHub kimlik bilgilerinizi girin.

1. Depo listesi göründüğünde, istediğiniz örnek uygulama deponuzu seçin.

1. Azure Pipelines, deponuzu analiz eder ve uygun bir işlem hattı şablonu önerir.

#### <a name="java"></a>[Java](#tab/java)

**Başlangıç** şablonunu seçin ve Java projenizi oluşturan aşağıdaki YAML kod parçacığını kopyalayın ve Apache Maven ile testleri çalıştırır:

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Daha fazla bilgi için, [Maven Ile Java uygulamanızı derleme](/azure/devops/pipelines/ecosystems/java)bölümünde bahsedilen adımları izleyin.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

**Başlangıç** şablonunu seçin ve NPM ile genel Node.js projesi oluşturan aşağıdaki YAML kod parçacığını kopyalayın.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Daha fazla bilgi için, [Gulp ile Node.js uygulamanızı oluşturma](/azure/devops/pipelines/ecosystems/javascript)bölümündeki adımları izleyin.

- BT 'nin ne yaptığını görmek için ardışık düzene göz atın. Tüm varsayılan girişlerin kodunuz için uygun olduğundan emin olun.

- **Kaydet ve Çalıştır**' ı seçin, ardından **doğrudan ana dalda Yürüt**' ü seçin ve ardından **Kaydet** ' i seçin ve yeniden çalıştırın.

- Yeni bir çalıştırma başlatılır. Çalıştırmanın bitmesini bekleyin.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Linux VM 'ye dağıtmak için CD adımları tanımlama

1. Yukarıdaki YAML söz dizimini kullanarak daha önce sahip olduğunuz ortama ve VM kaynaklarına başvurarak, yukarıdaki işlem hattının YAML dosyasını bir [dağıtım işi](/azure/devops/pipelines/process/deployment-jobs) içerecek şekilde değiştirin:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Ortamdaki her bir sanal makine için tanımladığınız **etiketleri** belirterek dağıtımı almak için ortamdan belirli sanal makine kümelerini seçebilirsiniz.
Dağıtım işi için tüm YAML şeması [aşağıda](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) verilmiştir.

3. Dağıtım stratejisi ya da `runOnce` `rolling` olarak belirtebilirsiniz. 

   `runOnce` , ve gibi tüm yaşam döngüsü `preDeploy` `deploy` `routeTraffic` `postRouteTraffic` kancalarının bir kez yürütüldüğü en basit dağıtım stratejisidir. Ardından, ya da `on:` `success` `on:` `failure` yürütülür.

   Aşağıda, için YAML kod parçacığı örneği verilmiştir `runOnce` :
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Aşağıda, her yinelemede 5 ' e kadar hedef sanal makine güncelleştirmeleri için bir sıralı strateji tanımlamak üzere kullanabileceğiniz YAML kod parçacığı örneği verilmiştir. `maxParallel` , paralel olarak, öğesine dağıtılabilecek hedef sayısını tespit eder. Dağıtım yapılan hedefleri dışlayarak herhangi bir zamanda kullanılabilir kalması gereken mutlak sayı veya hedef yüzdesi için seçim hesapları. Dağıtım sırasında başarı ve başarısızlık koşullarını tespit etmek için de kullanılır.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Bu işin her çalıştırılmasındaki dağıtım geçmişi, `<environment name>` VM 'leri oluşturduğunuz ve kaydettiğiniz ortama göre kaydedilir.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>İşlem hattınızı çalıştırın ve ortamda izlenebilirlik görünümlerini alın
Ortamın dağıtımlar görünümü, yürütmelerin ve iş öğelerinin tam izlenebilirliği ve ortam/kaynak başına bir çapraz işlem hattı dağıtım geçmişi sağlar.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Sonraki adımlar
- Yeni oluşturduğunuz işlem hattını [özelleştirmek](/azure/devops/pipelines/customize-pipeline) için devam edebilirsiniz.
- YAML işlem hatlarında yapabileceğiniz diğer şeyleri öğrenmek için bkz. [YAML şema başvurusu](/azure/devops/pipelines/yaml-schema).
- LAMP (Linux, Apache, MySQL ve PHP) yığınını dağıtma hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [LAMP yığını dağıtma](tutorial-lamp-stack.md)
