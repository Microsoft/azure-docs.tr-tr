---
title: Azure 'da sürekli tümleştirme ve Azure Pipelines Service Fabric uygulama dağıtma | Microsoft Docs
description: Bu öğreticide, Azure Pipelines kullanarak Service Fabric bir uygulama için sürekli tümleştirme ve dağıtım ayarlamayı öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 13411e813e96a1548b912c024f75404dd856fee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838965"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Öğretici: Service Fabric kümesine CI/CD ile uygulama dağıtma

Bu öğretici, bir serinin dört bölümüdür ve Azure Pipelines kullanarak bir Azure Service Fabric uygulaması için sürekli tümleştirme ve dağıtımın nasıl ayarlanacağını açıklar.  Mevcut bir Service Fabric uygulaması gereklidir; örnek olarak [.NET uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md) bölümünde oluşturulan uygulama kullanılır.

Serinin üçüncü bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Projenize kaynak denetimi ekleme
> * Azure Pipelines’da derleme işlem hattı oluşturma
> * Azure Pipelines’da yayın işlem hattı oluşturma
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [.NET Service Fabric uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md)
> * [Uygulamayı uzak kümeye dağıtma](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Bir ASP.NET Core ön uç hizmetine HTTPS uç noktası ekleme](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Azure Pipelines kullanarak CI/CD yapılandırma
> * [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Visual Studio 2019](https://www.visualstudio.com/) ' i yükleyip **Azure geliştirme** ve **ASP.net ve Web geliştirme** iş yüklerini yüklersiniz.
* [Service Fabric SDK'yı yükleyin](service-fabric-get-started.md)
* Azure’da Windows Service Fabric kümesi oluşturun; örneğin, [bu öğreticiyi izleyin](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Azure DevOps kuruluşu](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) oluşturun. Bu, Azure DevOps 'da bir proje oluşturmanıza ve Azure Pipelines kullanmanıza olanak sağlar.

## <a name="download-the-voting-sample-application"></a>Voting örnek uygulamasını indirme

[Bu öğretici serisinin birinci kısmında](service-fabric-tutorial-create-dotnet-app.md) Voting örnek uygulamasını oluşturmadıysanız, indirebilirsiniz. Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Yayımlama profili hazırlama

[Uygulamayı oluşturduğunuza](service-fabric-tutorial-create-dotnet-app.md) ve [uygulamayı Azure’a dağıttığınıza](service-fabric-tutorial-deploy-app-to-party-cluster.md) göre, artık sürekli tümleştirme ayarlamaya hazırsınız.  İlk olarak, Azure Pipelines içinde yürüten dağıtım işlemi tarafından kullanılmak üzere uygulamanız dahilinde bir yayımlama profili hazırlayın.  Yayımlama profili, daha önce oluşturduğunuz kümeyi hedefleyecek şekilde yapılandırılmalıdır.  Visual Studio’yu başlatın ve mevcut Service Fabric uygulaması projesini açın.  **Çözüm Gezgini**'nde uygulamaya sağ tıklayın ve **Yayımla...** öğesini seçin.

Sürekli tümleştirme iş akışınızda kullanmak üzere uygulama projenizin içinde bir hedef profil seçin (örneğin Bulut).  Küme bağlantısı uç noktasını belirtin.  Azure DevOps’daki her dağıtımda uygulamanızın yükseltilmesi için **Uygulamayı Yükselt** onay kutusunu işaretleyin.  Ayarları yayımlama profiline kaydetmek için **Kaydet** bağlantısına tıklayın ve ardından **İptal**’e tıklayarak iletişim kutusunu kapatın.

![Gönderim profili][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Visual Studio çözümünüzü yeni bir Azure DevOps Git deposunda paylaşma

Derlemeler oluşturabilmek için uygulamanızın kaynak dosyalarını Azure DevOps’daki bir projede paylaşın.

Visual Studio’nun sağ alt köşesindeki durum çubuğunda **Kaynak Denetimi’ne Ekle** -> **Git**’i seçerek projeniz için yeni bir yerel Git deposu oluşturun.

**Takım Gezgini**’ndeki **Gönderim** görünümünde **Azure DevOps’a Gönder**’in altında yer alan **Git Deposunda Yayımla** düğmesini seçin.

![Git deposunu gönderme][push-git-repo]

E-postanızı doğrulayın ve **Azure DevOps Etki Alanı** açılır listesinde hesabınızı seçin. Deponuzun adını girin ve **Depoyu yayımla**’yı seçin.

![Git deposunu gönderme][publish-code]

Depoyu yayımlamak, hesabınızda yerel depoyla aynı ada sahip yeni bir proje oluşturur. Mevcut projede depoyu oluşturmak için, **Depo adının** yanındaki **Gelişmiş**’e tıklayın ve bir proje seçin. **Web üzerinde görüntüleyin**’i seçerek kodunuzu web’de görüntüleyebilirsiniz.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Azure Pipelines ile sürekli teslimi yapılandırma

Azure Pipelines yapı işlem hattı, sıralı olarak yürütülen bir yapı adımları kümesinden oluşan bir iş akışını açıklar. Service Fabric kümenize dağıtmak üzere Service Fabric uygulama paketini ve diğer yapıtları üreten bir derleme işlem hattı oluşturun. [Azure Pipelines derleme işlem hatları](https://www.visualstudio.com/docs/build/define/create) hakkında daha fazla bilgi edinin. 

Bir Azure Pipelines yayın işlem hattı, kümeye uygulama paketi dağıtan bir iş akışını açıklar. Derleme işlem hattı ve yayın işlem hattı ile birlikte kullanıldığında kaynak dosyalardan başlayıp kümenizde çalışan bir uygulamada biten iş akışının tamamını yürütür. [Azure Pipelines yayın işlem hatları](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)hakkında daha fazla bilgi edinin.

### <a name="create-a-build-pipeline"></a>Derleme işlem hattı oluşturma

Web tarayıcısını açın ve şu adresteki yeni projenize gidin: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

İşlem **hatları** sekmesini seçin ve ardından **Yeni işlem hattı**' na tıklayın.

![Yeni İşlem Hattı][new-pipeline]

El ile ve zamanlanan derlemeler için kaynak olarak **Git Azure Repos** , **Oylama** takım projesi, **Oylama** deposu ve **ana** varsayılan dalı seçin.  Daha sonra **Devam**’a tıklayın.

![Depoyu seçin][select-repo]

**Şablon seç** alanında **Azure Service Fabric uygulaması** şablonunu seçin ve **Uygula**'ya tıklayın.

![Derleme şablonu seçme][select-build-template]

**Görevler**' de, **Aracı havuzu**olarak "barındırılan VS2017" yazın.

![Görevleri seçme][save-and-queue]

**Tetikleyiciler**’in altında **Sürekli tümleştirmeyi etkinleştir**'i işaretleyerek sürekli tümleştirmeyi etkinleştirin. **Dal filtreleri**içinde **dal belirtimi** varsayılan olarak **Master**' dir. Derlemeyi el ile başlatmak için **Kaydet ve kuyruğa al**’ı seçin.

![Tetikleyicileri seçme][save-and-queue2]

Derlemeler gönderme veya iade işlemleriyle de tetiklenir. Derleme ilerleme durumunu denetlemek için **derlemeler** sekmesine geçin.  Derlemeyi başarıyla yürüttüğünü doğruladıktan sonra, uygulamanızı bir kümeye dağıtan bir yayın işlem hattı tanımlayın.

### <a name="create-a-release-pipeline"></a>Yayın işlem hattı oluşturma

İşlem **hatları** sekmesini seçin, sonra **yayınlar**ve **+ Yeni işlem hattı**' nı seçin.  **Şablon seç** alanında, listeden **Azure Service Fabric Dağıtımı** şablonunu ve sonra da **Uygula**'yı seçin.

![Yayın şablonunu seçme][select-release-template]

Yeni küme bağlantısı eklemek için **Görevler**->**Ortam 1** ve sonra da **+Yeni**'yi seçin.

![Küme bağlantısı ekleme][add-cluster-connection]

**Yeni Service Fabric Bağlantısı ekle** görünümünde **Sertifika Tabanlı** veya **Azure Active Directory** kimlik doğrulamasını seçin.  Bağlantı adı olarak "mysftestcluster" ve küme uç noktası olarak "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (veya dağıtım yaptığınız kümenin uç noktası) belirtin.

Sertifika tabanlı kimlik doğrulaması için, kümeyi oluşturmak için kullanılan sunucu sertifikasının **sunucu sertifikası parmak izini** ekleyin.  **İstemci sertifikası** alanında, istemci sertifika dosyasının base-64 kodlamasını ekleyin. Sertifikanın bu base-64 kodlamalı gösterimini nasıl alacağınızı öğrenmek için bu alanın yardım açılan kutusuna bakın. Ayrıca sertifika için **Parola** ekleyin.  Ayrı bir istemci sertifikanız yoksa, küme veya sunucu sertifikasını kullanabilirsiniz.

Azure Active Directory kimlik bilgileri için, kümeyi oluştururken kullanılan sunucu sertifikasının **Sunucu sertifikası parmak izi**'ni ve ayrıca **Kullanıcı adı** ve **Parola** alanlarına kümeye bağlanırken kullanmak istediğiniz kimlik bilgilerini girin.

**Ekle**'ye tıklayarak küme bağlantısını kaydedin.

Ardından, yayın işlem hattının derlemeden çıkışı bulabilmesi için işlem hattına bir derleme yapıtı ekleyin. **İşlem Hattı**'nı ve **Yapıtlar**-> **+Ekle**'yi seçin.  **Kaynak (Derleme tanımı)** alanında, daha önce oluşturmuş olduğunuz derleme işlem hattını seçin.  **Ekle**’ye tıklayarak derleme yapıtını kaydedin.

![Yapıt ekleme][add-artifact]

Derleme tamamlandığında otomatik olarak bir yayın oluşturulması için sürekli dağıtım tetikleyicisini etkinleştirin. Yapıttaki şimşek simgesine tıklayın, tetikleyiciyi etkinleştirin ve **Kaydet**'e tıklayarak yayın işlem hattını kaydedin.

![Tetikleyici etkinleştirme][enable-trigger]

Yayını el ile oluşturmak için **+ Yayın** -> **Yayın Oluştur** -> **Oluştur**'u seçin. Yayının ilerleme durumunu **Yayınlar** sekmesinden takip edebilirsiniz.

Dağıtımın başarılı olduğunu ve uygulamanın kümede çalıştığını doğrulayın.  Bir Web tarayıcısı açın ve `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`gidin.  Uygulama sürümünü not alın (bu örnekte "1.0.0.20170616.3").

## <a name="commit-and-push-changes-trigger-a-release"></a>Değişiklikleri işleme ve gönderme, yayını tetikleme

Azure DevOps'da yapılan bazı kod değişikliklerini denetleyerek sürekli tümleştirme işlem hattının çalıştığını doğrulayın.

Siz kodunuzu yazarken, değişiklikleriniz Visual Studio tarafından otomatik olarak izlenir. Sağ alt kısımdaki durum çubuğunda bekleyen değişiklikler simgesini (![Beklemede][pending]) seçerek değişiklikleri yerel Git deponuza işleyin.

Takım Gezgini'ndeki **Değişiklikler** görünümünde, güncelleştirmenizi açıklayan bir ileti ekleyin ve değişikliklerinizi işleyin.

![Tümünü işleme][changes]

Yayımlanmamış değişiklikler durum çubuğu simgesini (![Yayımlanmamış değişiklikler][unpublished-changes]) veya Takım Gezgini'nde Eşitleme görünümünü seçin. Azure Pipelines kodunuzu güncelleştirmek için **Gönder** ' i seçin.

![Değişiklikleri gönderme][push]

Değişiklikleri Azure Pipelines göndermek, otomatik olarak bir derlemeyi tetikler.  Derleme işlem hattı başarıyla tamamlandığında, otomatik olarak bir yayın oluşturulur ve kümede uygulamayı yükseltme işlemini başlatır.

Derlemenizin ilerleme durumunu denetlemek için, Visual Studio'nun **Takım Gezgini**'nde **Derlemeler** sekmesine geçin.  Derlemenin başarıyla yürütüldüğünü doğruladıktan sonra, uygulamanızı kümeye dağıtan bir yayın işlem hattı belirleyin.

Dağıtımın başarılı olduğunu ve uygulamanın kümede çalıştığını doğrulayın.  Bir Web tarayıcısı açın ve `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`gidin.  Uygulama sürümünü not alın (bu örnekte "1.0.0.20170815.3").

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Uygulamayı güncelleştirme

Uygulamada kod değişikliklerini yapın.  Önceki adımları izleyerek değişiklikleri kaydedin ve işleyin.

Uygulamanın yükseltmesi başladığında, Service Fabric Explorer'da yükseltmenin ilerleme durumunu izleyebilirsiniz:

![Service Fabric Explorer][sfx2]

Uygulama yükseltmesi birkaç dakika sürebilir. Yükseltme tamamlandığında, uygulama bir sonraki sürümde çalışıyor olacaktır.  Bu örnekte "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Projenize kaynak denetimi ekleme
> * Derleme işlem hattı oluşturma
> * Yayın işlem hattı oluşturma
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
