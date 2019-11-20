---
title: Azure Service Fabric kümesine CI/CD ile kapsayıcı uygulaması dağıtma
description: Bu öğreticide, Visual Studio Azure DevOps kullanarak bir Azure Service Fabric kapsayıcı uygulaması için sürekli tümleştirme ve dağıtım ayarlamayı öğreneceksiniz.
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
ms.date: 08/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: a2dc6aeb7dc2a62c543a58c322c23c9661c6940a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832736"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Öğretici: Service Fabric kümesine CI/CD ile kapsayıcı uygulaması dağıtma

Bu öğretici, bir serinin ikinci bölümüdür ve Visual Studio ve Azure DevOps kullanılarak Azure Service Fabric kapsayıcı uygulaması için sürekli tümleştirme ve dağıtımın nasıl ayarlanacağını açıklar.  Var olan bir Service Fabric uygulaması gereklidir; örnek olarak [Bir Windows kapsayıcısındaki .NET uygulamasını Azure Service Fabric’e dağıtma](service-fabric-host-app-in-a-container.md) öğreticisinde oluşturulan uygulama örnek olarak kullanılmıştır.

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Projenize kaynak denetimi ekleme
> * Visual Studio 'da derleme tanımı oluşturma Takım Gezgini
> * Visual Studio 'da bir yayın tanımı oluşturun Takım Gezgini
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure’da bir kümeye sahip olmak veya [bu öğreticide küme oluşturmak](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Buna kapsayıcılı bir uygulama dağıtmak](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Yayımlama profili hazırlama

[Bir kapsayıcı uygulaması dağıttığınıza](service-fabric-host-app-in-a-container.md) göre, sürekli tümleştirmeyi ayarlamaya hazırsınız.  İlk olarak, uygulamanızda Azure DevOps içinde yürütülen dağıtım işleminin kullanacağı yayımlama profilini hazırlayın.  Yayımlama profili, daha önce oluşturduğunuz kümeyi hedefleyecek şekilde yapılandırılmalıdır.  Visual Studio’yu başlatın ve mevcut Service Fabric uygulaması projesini açın.  **Çözüm Gezgini**'nde uygulamaya sağ tıklayın ve **Yayımla...** öğesini seçin.

Sürekli tümleştirme iş akışınızda kullanmak üzere uygulama projenizin içinde bir hedef profil seçin (örneğin Bulut).  Küme bağlantısı uç noktasını belirtin.  Azure DevOps’daki her dağıtımda uygulamanızın yükseltilmesi için **Uygulamayı Yükselt** onay kutusunu işaretleyin.  Ayarları yayımlama profiline kaydetmek için **Kaydet** bağlantısına tıklayın ve ardından **İptal**’e tıklayarak iletişim kutusunu kapatın.

![Gönderim profili][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Visual Studio çözümünüzü yeni bir Azure DevOps Git deposunda paylaşma

Derleme oluşturabilmeniz için uygulama kaynak dosyalarınızı Azure DevOps 'daki bir takım projesi ile paylaşabilirsiniz.

Visual Studio’nun sağ alt köşesindeki durum çubuğunda **Kaynak Denetimi’ne Ekle** -> **Git**’i seçerek projeniz için yeni bir yerel Git deposu oluşturun.

**Takım Gezgini**’ndeki **Gönderim** görünümünde **Azure DevOps’a Gönder**’in altında yer alan **Git Deposunda Yayımla** düğmesini seçin.

![Git deposunu gönderme][push-git-repo]

E-postanızı doğrulayın ve **Hesap** açılır penceresinde kuruluşunuzu seçin. Henüz bir kuruluşunuz yoksa, bir kuruluş ayarlamanız gerekebilir. Deponuzun adını girin ve **Depoyu yayımla**’yı seçin.

![Git deposunu gönderme][publish-code]

Depoyu yayımlamak, hesabınızda yerel depoyla aynı adda yeni bir takım projesi oluşturur. Mevcut takım projesinde depoyu oluşturmak için, **Depo adının** yanındaki **Gelişmiş**’e tıklayın ve bir takım projesi seçin. **Web üzerinde görüntüleyin**’i seçerek kodunuzu web’de görüntüleyebilirsiniz.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Azure Pipelines ile sürekli teslimi yapılandırma

Azure DevOps derleme tanımı, sıralı olarak yürütülen bir yapı adımları kümesinden oluşan bir iş akışını açıklar. Service Fabric kümenize dağıtmak üzere Service Fabric uygulama paketini ve diğer yapıtları üreten bir derleme tanımı oluşturun. Azure DevOps [derleme tanımları](https://www.visualstudio.com/docs/build/define/create)hakkında daha fazla bilgi edinin. 

Azure DevOps sürüm tanımı, bir kümeye uygulama paketi dağıtan bir iş akışını açıklar. Derleme tanımı ve yayın tanımı birlikte kullanıldığında kaynak dosyalardan başlayıp kümenizde çalışan bir uygulamada biten iş akışının tamamını yürütür. Azure DevOps [sürüm tanımları](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)hakkında daha fazla bilgi edinin.

### <a name="create-a-build-definition"></a>Derleme tanımı oluşturma

Yeni takım projenizi, bir Web tarayıcısında https://dev.azure.com giderek, kuruluşunuzu seçerek ve ardından yeni proje ' ye tıklayarak açın. 

Sol panelde **ardışık düzen** seçeneğini belirleyin ve ardından **Yeni işlem hattı**' na tıklayın.

>[!NOTE]
>Derleme tanımı şablonunu görmüyorsanız **Yeni YAML işlem hattı oluşturma deneyimi** özelliğinin kapalı olduğundan emin olun. Bu özellik, DevOps hesabınızın **Önizleme Özellikleri** bölümünde yapılandırılır.

![Yeni İşlem Hattı][new-pipeline]

Kaynak olarak **Azure Repos git** ' i, takım projenizin adını, proje deponuzu ve **ana** varsayılan dalı veya el ile zamanlanmış derlemeleri seçin.  Daha sonra **Devam**’a tıklayın.

**Şablon seç** alanında **Docker desteğine sahip Azure Service Fabric uygulaması** şablonunu seçin ve **Uygula**'ya tıklayın.

![Derleme şablonu seçme][select-build-template]

**Görevler**’de, **Aracı havuzu** olarak **Hosted VS2017**’yi seçin.

![Görevleri seçme][task-agent-pool]

**Görüntüleri etiketle**’ye tıklayın.

**Kapsayıcı Kayıt Defteri Türü**’nde, **Azure Container Kayıt Defteri**’ni seçin. Bir **Azure Aboneliği** seçin ve ardından **Yetkilendir**’e tıklayın. Bir **Azure Container Kayıt Defteri** seçin.

![Docker Etiketi görüntülerini seçme][select-tag-images]

**Görüntüleri gönder**’e tıklayın.

**Kapsayıcı Kayıt Defteri Türü**’nde, **Azure Container Kayıt Defteri**’ni seçin. Bir **Azure Aboneliği** seçin ve ardından **Yetkilendir**’e tıklayın. Bir **Azure Container Kayıt Defteri** seçin.

![Docker Görüntüleri gönderi seçme][select-push-images]

**Tetikleyiciler** sekmesinde, **sürekli tümleştirmeyi etkinleştir**' i denetleyerek sürekli tümleştirmeyi etkinleştirin. **Dal filtreleri** bölümünde **+ Ekle**'ye tıklayın, **Dal belirtimi** **ana** varsayılan değerine döner.

El ile bir derleme başlatmak için, **Derleme işlem hattını ve kuyruğunu kaydet iletişim kutusunda**, **Kaydet ve kuyruğa al**’a tıklayın.

![Tetikleyicileri seçme][save-and-queue]

Derlemeler gönderme veya iade işlemleriyle de tetiklenir. Derleme ilerleme durumunu denetlemek için **derlemeler** sekmesine geçin.  Derlemeyi başarıyla yürüttüğünü doğruladıktan sonra, uygulamanızı bir kümeye dağıtan bir yayın tanımı tanımlayın.

### <a name="create-a-release-definition"></a>Yayın tanımı oluşturma

Sol panelde **ardışık düzen** seçeneğini belirleyip **yayınlar**ve sonra **+ Yeni işlem hattı**' nı seçin.  **Şablon seç** alanında, listeden **Azure Service Fabric Dağıtımı** şablonunu ve sonra da **Uygula**'yı seçin.

![Yayın şablonunu seçme][select-release-template]

Yeni bir küme bağlantısı eklemek için **Görevler**’i, ardından **Ortam 1**’, ve daha sonra **+Yeni**’yi seçin.

![Küme bağlantısı ekleme][add-cluster-connection]

**Yeni Service Fabric Bağlantısı ekle** görünümünde **Sertifika Tabanlı** veya **Azure Active Directory** kimlik doğrulamasını seçin.  Bağlantı adı olarak "mysftestcluster" ve küme uç noktası olarak "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (veya dağıtım yaptığınız kümenin uç noktası) belirtin.

Sertifika tabanlı kimlik doğrulaması için, kümeyi oluştururken kullanılan sunucu sertifikasının **Sunucu sertifikası parmak izi**'ni ekleyin.  **İstemci sertifikası** alanında, istemci sertifika dosyasının base-64 kodlamasını ekleyin. Sertifikanın bu base-64 kodlamalı gösterimini nasıl alacağınızı öğrenmek için bu alanın yardım açılan kutusuna bakın. Ayrıca sertifika için **Parola** ekleyin.  Ayrı bir istemci sertifikanız yoksa, küme veya sunucu sertifikasını kullanabilirsiniz.

Azure Active Directory kimlik bilgileri için, kümeyi oluştururken kullanılan sunucu sertifikasının **Sunucu sertifikası parmak izi**'ni ve ayrıca **Kullanıcı adı** ve **Parola** alanlarına kümeye bağlanırken kullanmak istediğiniz kimlik bilgilerini girin.

**Ekle**'ye tıklayarak küme bağlantısını kaydedin.



Aracı Aşaması altında **Service Fabric Uygulaması Dağıtma**’ya tıklayın.
**Docker Ayarları**’na ve ardından **Docker Ayarlarını Yapılandır**’a tıklayın. **Kayıt Defteri Kimlik Bilgileri Kaynağı**’nda, **Azure Resource Manager Hizmeti Bağlantısı**’nı seçin. Ardından **Azure aboneliğinizi** seçin.

![Yayın işlem hattı aracısı][release-pipeline-agent]

Ardından, yayın tanımının derlemeden çıkışı bulabilmesi için işlem hattına bir derleme yapıtı ekleyin. **İşlem Hattı**'nı ve **Yapıtlar**-> **+Ekle**'yi seçin.  **Kaynak (Derleme tanımı)** alanında, daha önce oluşturmuş olduğunuz derleme tanımını seçin.  **Ekle**’ye tıklayarak derleme yapıtını kaydedin.

![Yapıt ekleme][add-artifact]

Derleme tamamlandığında otomatik olarak bir yayın oluşturulması için sürekli dağıtım tetikleyicisini etkinleştirin. Yapıttaki şimşek simgesine tıklayın, tetikleyiciyi etkinleştirin ve **Kaydet**'e tıklayarak yayın tanımını kaydedin.

![Tetikleyici etkinleştirme][enable-trigger]

Yayını el ile oluşturmak için **+ Yayın** -> **Yayın Oluştur** -> **Oluştur**'u seçin. Yayının ilerleme durumunu **Yayınlar** sekmesinden takip edebilirsiniz.

Dağıtımın başarılı olduğunu ve uygulamanın kümede çalıştığını doğrulayın.  Bir Web tarayıcısı açın ve `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`gidin.  Uygulama sürümünü not alın (bu örnekte "1.0.0.20170616.3").

## <a name="commit-and-push-changes-trigger-a-release"></a>Değişiklikleri işleme ve gönderme, yayını tetikleme

Azure DevOps'da yapılan bazı kod değişikliklerini denetleyerek sürekli tümleştirme işlem hattının çalıştığını doğrulayın.

Siz kodunuzu yazarken, değişiklikleriniz Visual Studio tarafından otomatik olarak izlenir. Sağ alt kısımdaki durum çubuğunda bekleyen değişiklikler simgesini (![Beklemede][pending]) seçerek değişiklikleri yerel Git deponuza işleyin.

Takım Gezgini'ndeki **Değişiklikler** görünümünde, güncelleştirmenizi açıklayan bir ileti ekleyin ve değişikliklerinizi işleyin.

![Tümünü işleme][changes]

Yayımlanmamış değişiklikler durum çubuğu simgesini (![Yayımlanmamış değişiklikler][unpublished-changes]) veya Takım Gezgini'nde Eşitleme görünümünü seçin. Azure DevOps’da kodunuzu güncelleştirmek için **Gönder**'i seçin.

![Değişiklikleri gönderme][push]

Değişikliklerin Azure DevOps'a gönderilmesi otomatik olarak derlemeyi tetikler.  Derleme tanımı başarıyla tamamlandığında, otomatik olarak bir yayın oluşturulur ve kümede uygulamayı yükseltme işlemini başlatır.

Derlemenizin ilerleme durumunu denetlemek için, Visual Studio'nun **Takım Gezgini**'nde **Derlemeler** sekmesine geçin.  Derlemenin başarıyla yürütüldüğünü doğruladıktan sonra, uygulamanızı kümeye dağıtan bir yayın tanımı belirleyin.

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
> * Derleme tanımı oluşturma
> * Yayın tanımı oluşturma
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

Öğreticinin sonraki bölümünde [kapsayıcınızı izlemeyi](service-fabric-tutorial-monitoring-wincontainers.md) nasıl ayarlayacağınızı öğrenebilirsiniz.

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
