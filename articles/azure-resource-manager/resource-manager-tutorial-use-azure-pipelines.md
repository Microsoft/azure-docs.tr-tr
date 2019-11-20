---
title: Azure Pipelines ile sürekli tümleştirme
description: Azure Resource Manager şablonlarını sürekli olarak derlemeyi, test etmek ve dağıtmak hakkında bilgi edinin.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 73ab01b5e4ea923b990932986a938f8c1fb25629
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149296"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Öğretici: Azure Pipelines Azure Resource Manager şablonlarının sürekli tümleştirilmesi

Azure Resource Manager şablon projelerini sürekli olarak derlemek ve dağıtmak için Azure Pipelines kullanmayı öğrenin.

Azure DevOps, işleri planlamak, kod geliştirmeyle işbirliği yapmak ve uygulama derlemek ve dağıtmak için ekipleri desteklemek üzere Geliştirici Hizmetleri sağlar. Geliştiriciler Azure DevOps Services kullanarak bulutta çalışabilir. Azure DevOps, Web tarayıcınız veya IDE istemciniz aracılığıyla erişebileceğiniz tümleşik bir özellikler kümesi sağlar. Azure işlem hattı bu özelliklerden biridir. Azure Pipelines, tam özellikli bir sürekli tümleştirme (CI) ve sürekli teslim (CD) hizmetidir. Tercih ettiğiniz git sağlayıcınız ile birlikte çalışarak, en büyük bulut hizmetlerine dağıtabilirsiniz. Daha sonra Microsoft Azure, Google Cloud Platform veya Amazon Web Services için kodunuzun derleme, test etme ve dağıtımını otomatik hale getirebilirsiniz.

Bu öğretici, yeni Azure DevOps Services ve Azure Pipelines olan Azure Resource Manager şablon geliştiricileri için tasarlanmıştır. GitHub ve DevOps hakkında zaten bilgi sahibiyseniz, işlem [hattı oluşturmak](#create-a-pipeline)için atlayabilirsiniz.

> [!NOTE]
> Bir proje adı seçin. Öğreticiden gittiğinizde, **Azurermpipeline** herhangi birini proje adınızla değiştirin.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * GitHub deposu hazırlama
> * Azure DevOps projesi oluşturma
> * Azure işlem hattı oluşturma
> * İşlem hattı dağıtımını doğrulama
> * Şablonu güncelleştirme ve yeniden dağıtma
> * Kaynakları temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* Şablonunuz için bir depo oluşturmak üzere kullandığınız **bir GitHub hesabı**. Bir tane yoksa, [ücretsiz olarak bir tane oluşturabilirsiniz](https://github.com). GitHub depoları kullanma hakkında daha fazla bilgi için bkz. [GitHub depoları oluşturma](/azure/devops/pipelines/repos/github).
* **Git’i yükleyin**. Bu öğretici yönergesi, *Git Bash* veya *Git kabuğu*kullanır. Yönergeler için bkz. [Git 'ı Install]( https://www.atlassian.com/git/tutorials/install-git).
* **Azure DevOps organizasyonu**. Bir tane yoksa, ücretsiz olarak bir tane oluşturabilirsiniz. Bkz. [kuruluş veya proje koleksiyonu oluşturma]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **Kaynak Yöneticisi Araçları uzantısıyla [Visual Studio Code](https://code.visualstudio.com/)** . Bkz. [Uzantıyı yükleme](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>GitHub deposu hazırlama

GitHub, Kaynak Yöneticisi şablonları dahil olmak üzere proje kaynak kodunuzu depolamak için kullanılır. Desteklenen diğer depolar için bkz. [Azure DevOps tarafından desteklenen depolar](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>GitHub deposu oluşturma

GitHub hesabınız yoksa bkz. [Önkoşullar](#prerequisites).

1. [GitHub](https://github.com)'da oturum açın.
2. Sağ üst köşedeki hesap görüntünüzü seçin ve ardından **depolarınızı**seçin.

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub deposu oluşturma](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Yeşil düğme olan **Yeni**' yi seçin.
1. **Depo adı**alanına bir depo adı girin.  Örneğin, **Azurermpipeline-depo**. Herhangi bir **Azurermpipeline** öğesini proje adınızla değiştirmeyi unutmayın. Bu öğreticiden gezinmek için **genel** veya **özel** ' i seçebilirsiniz. Sonra **Depo oluştur**' u seçin.
1. URL 'YI yazın. Depo URL 'SI şu biçimdedir:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Bu depo, *uzak depo*olarak adlandırılır. Aynı projenin geliştiricilerin her biri kendi *yerel deposunu*kopyalayabilir ve değişiklikleri uzak depoda birleştirebilirler.

### <a name="clone-the-remote-repository"></a>Uzak depoyu Kopyala

1. Git kabuğu veya git Bash 'i açın.  [Ön koşullara](#prerequisites) bakın.
1. Geçerli klasörünüzün **GitHub**olduğunu doğrulayın.
1. Şu komutu çalıştırın:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    **[Youraccountname]** öğesini GitHub hesabınızın adıyla değiştirin ve **[Yourgithubdeptoryname]** öğesini önceki yordamda oluşturduğunuz depo adınızla değiştirin.

    Aşağıdaki ekran görüntüsünde bir örnek gösterilmektedir.

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub Bash oluştur](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**Createazurestorage** klasörü, şablonun depolandığı klasördür. **PWD** komutu klasör yolunu gösterir. Yol, aşağıdaki yordamda şablonu kaydettiğiniz yerdir.

### <a name="download-a-quickstart-template"></a>Hızlı başlangıç şablonu indirme

Bir şablon oluşturmak yerine bir [hızlı başlangıç şablonu]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)indirebilirsiniz. Bu şablon bir Azure depolama hesabı oluşturur.

1. Visual Studio Code 'u açın. [Ön koşullara](#prerequisites) bakın.
2. Aşağıdaki URL ile şablonu açın:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Dosyayı **Createazurestorage** Folder dizinine **azuredeploy. JSON** olarak kaydedin. Hem klasör adı hem de dosya adı ardışık düzende oldukları için kullanılır.  Bu adları değiştirirseniz, işlem hattında kullanılan adları güncelleştirmeniz gerekir.

### <a name="push-the-template-to-the-remote-repository"></a>Şablonu uzak depoya gönder

Azuredeploy. JSON yerel depoya eklendi. Sonra, şablonu uzak depoya yüklersiniz.

1. Açık değilse *Git kabuğu* veya *Git Bash*'i açın.
1. Dizini yerel deponuzdaki CreateAzureStorage klasörü olarak değiştirin.
1. **Azuredeploy. JSON** dosyasının klasörde olduğunu doğrulayın.
1. Şu komutu çalıştırın:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    LF hakkında bir uyarı alabilirsiniz. Uyarıyı yoksayabilirsiniz. **ana** dal ana daldır.  Genellikle her güncelleştirme için bir dal oluşturursunuz. Öğreticiyi basitleştirmek için, ana dalı doğrudan kullanırsınız.
1. Bir tarayıcıdan GitHub deponuza gidin.  URL, **https://github.com/[YourAccountName]/[YourGitHubRepository]** . **Createazurestorage** klasörünü ve **Azuredeploy. JSON** dosyasını klasörün içinde görürsünüz.

Şimdiye kadar, bir GitHub deposu oluşturdunuz ve depoya bir şablon yüklediniz.

## <a name="create-a-devops-project"></a>DevOps projesi oluşturma

Sonraki yordama geçebilmeniz için bir DevOps organizasyonu gerekir.  Bir tane yoksa, bkz. [Önkoşullar](#prerequisites).

1. [Azure DevOps](https://dev.azure.com)' da oturum açın.
1. Soldan bir DevOps organizasyonu seçin.

    ![Azure DevOps Azure Resource Manager Azure Pipelines Azure DevOps projesi oluşturma](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. **Create project** (Proje oluştur) öğesini seçin. Herhangi bir projeniz yoksa, proje oluştur sayfası otomatik olarak açılır.
1. Aşağıdaki değerleri girin:

    * **Proje adı**: bir proje adı girin. Öğreticinin en başından itibaren seçtiğiniz proje adını kullanabilirsiniz.
    * **Sürüm denetimi**: **Git**' i seçin. **Sürüm denetimini**görmek için **Gelişmiş** ' i genişletmeniz gerekebilir.

    Diğer özellikler için varsayılan değeri kullanın.
1. **Create project** (Proje oluştur) öğesini seçin.

Azure 'a projeler dağıtmak için kullanılan bir hizmet bağlantısı oluşturun.

1. Sol menünün altından **proje ayarları** ' nı seçin.
1. İşlem **hatları**altında **hizmet bağlantıları** ' nı seçin.
1. **Yeni hizmet bağlantısı**' nı seçin ve ardından **AzureResourceManager**' ı seçin.
1. Aşağıdaki değerleri girin:

    * **Bağlantı adı**: bir bağlantı adı girin. Örneğin, **Azurermpipeline-Conn**. Bu adı yazın, işlem hattınızı oluştururken adın olması gerekir.
    * **Kapsam düzeyi**: **abonelik**' ı seçin.
    * **Abonelik**: Aboneliğinizi seçin.
    * **Kaynak grubu**: boş bırakın.
    * **Tüm işlem hatlarının bu bağlantıyı kullanmasına Izin verin**. seçildiğinde
1. **Tamam**’ı seçin.

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Bu aşamada, aşağıdaki görevleri tamamladınız.  GitHub ve DevOps hakkında bilgi sahibi olduğunuz için önceki bölümleri atlarsanız, devam etmeden önce görevleri tamamlamalısınız.

- Bir GitHub deposu oluşturun ve [Bu şablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) depodaki **Createazurestorage** klasörüne kaydedin.
- Bir DevOps projesi oluşturun ve bir Azure Resource Manager hizmet bağlantısı oluşturun.

Şablon dağıtmak için bir adımla işlem hattı oluşturmak için:

1. Sol menüden işlem **hatları** ' nı seçin.
1. **Yeni işlem hattı**' nı seçin.
1. **Bağlan** sekmesinde **GitHub**' ı seçin. İstenirse, GitHub kimlik bilgilerinizi girin ve ardından yönergeleri izleyin. Aşağıdaki ekranı görürseniz, **yalnızca depoları Seç**' i seçin ve **& yüklemeyi Onayla**' yı seçmeden önce depolarınızın listede olduğunu doğrulayın.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yalnızca depoları Seç](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. **Seç** sekmesinden deponuzu seçin.  Varsayılan ad **[Youraccountname]/[Yourgithubdepotoryname]** .
1. **Yapılandır** sekmesinde **Başlatıcı işlem hattı**' nı seçin. İki komut dosyası adımı ile **Azure-Pipelines. i ml** işlem hattı dosyasını gösterir.
1. **Adımlar** bölümünü aşağıdaki YAML ile değiştirin:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Şöyle görünür:

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Aşağıdaki değişiklikleri yapın:

    * **Deloymentscope**: seçeneklerden dağıtım kapsamını seçin: `Management Group`, `Subscription` ve `Resource Group`. Bu öğreticide **kaynak grubunu** kullanın. Kapsamlar hakkında daha fazla bilgi edinmek için bkz. [dağıtım kapsamları](./resource-group-template-deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: daha önce oluşturduğunuz hizmet bağlantı adını belirtin.
    * **SubscriptionName**: hedef abonelik kimliğini belirtin.
    * **eylem**: **kaynak grubunu oluştur veya Güncelleştir** eylemi 2 eylem-1 yapar. Yeni bir kaynak grubu adı sağlanmışsa, bir kaynak grubu oluşturun; iki. belirtilen şablonu dağıtın.
    * **Resourcegroupname**: yeni bir kaynak grubu adı belirtin. Örneğin, **Azurermpipeline-RG**.
    * **konum**: kaynak grubu için konumu belirtin.
    * **Templatelocation**: **bağlantılı yapıt** belirtildiğinde, görev, doğrudan bağlı depodan şablon dosyasını arar.
    * **Csmfile** , şablon dosyasının yoludur. Şablonda tanımlanmış tüm parametrelerin varsayılan değerleri olduğundan, şablon parametreleri dosyası belirtmeniz gerekmez.

    Görev hakkında daha fazla bilgi için bkz. [Azure Kaynak grubu dağıtım görevi](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)ve [Azure Resource Manager şablonu Dağıtım görevi](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. **Kaydet ve çalıştır**’ı seçin.
1. **Kaydet '** i seçin ve yeniden çalıştırın. YAML dosyasının bir kopyası bağlı depoya kaydedilir. YAML dosyasını deponuza giderek görebilirsiniz.
1. İşlem hattının başarıyla yürütüldüğünü doğrulayın.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kaynak grubunu açın. Bu ad, ardışık düzen YAML dosyasında belirttiğiniz şeydir.  Oluşturulmuş bir depolama hesabı görürsünüz.  Depolama hesabı adı **Mağaza**ile başlar.
1. Açmak için depolama hesabı adını seçin.
1. **Özellikler**’i seçin. **Çoğaltmanın** **yerel olarak yedekli depolama (LRS)** olduğuna dikkat edin.

    ![Azure Resource Manager Azure DevOps Azure Pipelines Portal doğrulaması](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Güncelleştir ve yeniden Dağıt

Şablonu güncelleştirdiğinizde ve değişiklikleri uzak depoya gönderdiğinizde, işlem hattı bu durumda kaynakları, depolama hesabını otomatik olarak güncelleştirir.

1. Visual Studio Code yerel deponuzdan **azuredeploy. JSON** ' u açın.
1. **Storageaccounttype** değerinin **Standard_GRS** **olarak değerini güncelleştirin** . Aşağıdaki ekran görüntüsüne bakın:

    ![Azure Resource Manager Azure DevOps Azure Pipelines güncelleştirme YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Değişiklikleri kaydedin.
1. Git Bash/Shell ' den aşağıdaki komutları çalıştırarak, değişiklikleri uzak depoya gönderin.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    İlk komut yerel depoyu uzak depoyla eşitler. Uzak depoya, YAML dosyasının ardışık düzen eklendiğini unutmayın.

    Uzak deponun ana dalı güncelleştirildiğinden, işlem hattı yeniden tetiklenir.

Değişiklikleri doğrulamak için depolama hesabının çoğaltma özelliğini denetleyebilirsiniz.  Bkz. [dağıtımı doğrulama](#verify-the-deployment).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

GitHub deposunu ve Azure DevOps projesini de silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure Resource Manager şablonu dağıtmak için bir Azure DevOps işlem hattı oluşturacaksınız. Azure kaynaklarını birden fazla bölgede dağıtma ve güvenli dağıtım uygulamalarını kullanma hakkında bilgi edinmek için bkz.

> [!div class="nextstepaction"]
> [Güvenli dağıtım uygulamaları kullanma](./deployment-manager-tutorial.md)
