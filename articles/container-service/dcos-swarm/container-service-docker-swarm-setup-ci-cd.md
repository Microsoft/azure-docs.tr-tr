---
title: Kullanım DıŞı Azure Container Service ve Sısınma ile CI/CD
description: Sürekli olarak çok kapsayıcılı bir .NET Core uygulaması sunmak için Docker Sısınma, bir Azure Container Registry ve Azure DevOps ile Azure Container Service kullanın
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 8990f1f8e4cda5a6cc8b8d3197b843662b1397a5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598545"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>Kullanım DıŞı Azure DevOps Services kullanarak Docker Sısınma ile Azure Container Service çok kapsayıcılı bir uygulama dağıtmak için tam CI/CD işlem hattı

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Bulut için modern uygulamalar geliştirilirken en büyük zorluklardan biri, bu uygulamaları sürekli olarak sunabilmektedir. Bu makalede, Docker Sısınma, Azure Container Registry ve Azure Pipelines yönetimiyle Azure Container Service kullanarak tam bir sürekli tümleştirme ve dağıtım (CI/CD) işlem hattı uygulamayı nasıl uygulayacağınızı öğreneceksiniz.

Bu makale, ASP.NET Core ile geliştirilen [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs)'da bulunan basit bir uygulamayı temel alır. Uygulama dört farklı hizmetten oluşur: üç Web API 'si ve bir Web ön ucu:

![MyShop örnek uygulaması](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Amaç, Azure DevOps Services kullanarak bu uygulamayı bir Docker Sısınma kümesinde sürekli olarak sunmaya yönelik bir uygulamadır. Aşağıdaki şekilde bu sürekli teslim işlem hattı ayrıntıları verilmiştir:

![MyShop örnek uygulaması](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Bu adımların kısa bir açıklaması aşağıda verilmiştir:

1. Kod değişiklikleri kaynak kodu deposuna (burada, GitHub) kaydedilir 
1. GitHub, Azure DevOps Services bir derlemeyi tetikler 
1. Azure DevOps Services, kaynakların en son sürümünü alır ve uygulamayı oluşturan tüm görüntüleri oluşturur 
1. Azure DevOps Services her görüntüyü Azure Container Registry hizmeti kullanılarak oluşturulan bir Docker kayıt defterine gönderir 
1. Azure DevOps Services yeni bir sürümü tetikler 
1. Sürüm, Azure Container Service kümesi ana düğümünde SSH kullanarak bazı komutları çalıştırır 
1. Kümedeki Docker Sısınma görüntülerin en son sürümünü çeker 
1. Uygulamanın yeni sürümü Docker Compose kullanılarak dağıtılır 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, aşağıdaki görevleri gerçekleştirmeniz gerekir:

- [Azure Container Service'te Swarm kümesi oluşturma](container-service-deployment.md)
- [Azure Container Service'teki Swarm kümesine bağlanma](../container-service-connect.md)
- [Azure Container Registry oluşturma](../../container-registry/container-registry-get-started-portal.md)
- [Azure DevOps Services kuruluş ve proje oluşturma](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [GitHub deponuzu GitHub hesabınıza çatalla](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Docker 'ın yüklü olduğu bir Ubuntu (14,04 veya 16,04) makinesine de ihtiyacınız vardır. Bu makine, Azure Pipelines süreçler sırasında Azure DevOps Services tarafından kullanılır. Bu makineyi oluşturmanın bir yolu, [Azure Marketi](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/)'nde bulunan görüntüyü kullanmaktır. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>1\. adım: Azure DevOps Services kuruluşunuzu yapılandırma 

Bu bölümde, Azure DevOps Services kuruluşunuzu yapılandırırsınız.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Azure DevOps Services Linux derleme Aracısı yapılandırma

Docker görüntüleri oluşturmak ve bu görüntüleri bir Azure DevOps Services derlemeden bir Azure Kapsayıcı kayıt defterine göndermek için bir Linux Aracısı kaydetmeniz gerekir. Şu yükleme seçenekleriniz vardır:

* [Linux üzerinde bir Aracı dağıtma](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Azure DevOps Services aracısını çalıştırmak için Docker 'ı kullanma](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Docker tümleştirme Azure DevOps Services uzantısını yükler

Microsoft, Azure Pipelines işlemlerinde Docker ile çalışmak için bir Azure DevOps Services uzantısı sağlar. Bu uzantı [Azure DevOps Services marketi](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker)'nde kullanılabilir. Bu uzantıyı Azure DevOps Services kuruluşunuza eklemek için, **yükler** ' e tıklayın:

![Docker tümleştirmesini yükler](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Kimlik bilgilerinizi kullanarak Azure DevOps Services kuruluşunuza bağlanmanız istenir. 

### <a name="connect-azure-devops-services-and-github"></a>Azure DevOps Services ve GitHub 'ı bağlama

Azure DevOps Services projeniz ile GitHub hesabınız arasında bir bağlantı kurun.

1. Azure DevOps Services projenizde, araç çubuğunda **Ayarlar** simgesine tıklayın ve **Hizmetler**' i seçin.

    ![Azure DevOps Services-dış bağlantı](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Sol tarafta **yeni hizmet uç noktası** > **GitHub**' a tıklayın.

    ![Azure DevOps Services-GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Azure DevOps Services GitHub hesabınızla çalışacak şekilde yetkilendirmek için **Yetkilendir** ' e tıklayın ve açılan penceredeki yordamı izleyin.

    ![Azure DevOps Services-GitHub 'ı yetkilendir](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Azure DevOps Services Azure Container Registry ve Azure Container Service kümenize bağlama

CI/CD işlem hattına almadan önce geçen adımlar, Azure 'da kapsayıcı kayıt defteriniz ve Docker Sısınma kümeniz için dış bağlantıları yapılandırmaktır. 

1. Azure DevOps Services projenizin **Hizmetler** ayarları ' nda **Docker kayıt defteri**türünde bir hizmet uç noktası ekleyin. 

1. Açılan pencerede, Azure Container Registry 'nizin URL 'sini ve kimlik bilgilerini girin.

    ![Azure DevOps Services-Docker kayıt defteri](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Docker Sısınma kümesi için **SSH**türünde bir uç nokta ekleyin. Ardından, Sısınma kümenizin SSH bağlantı bilgilerini girin.

    ![Azure DevOps Services-SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Tüm yapılandırma şimdi yapılır. Sonraki adımlarda, uygulamayı oluşturup Docker Sısınma kümesine dağıtan CI/CD işlem hattını oluşturursunuz. 

## <a name="step-2-create-the-build-pipeline"></a>2\. adım: Derleme işlem hattını oluşturma

Bu adımda, Azure DevOps Services projeniz için bir yapı işlem hattı ayarlarsınız ve kapsayıcı görüntüleriniz için derleme iş akışını tanımlarsınız

### <a name="initial-pipeline-setup"></a>İlk işlem hattı kurulumu

1. Bir derleme işlem hattı oluşturmak için Azure DevOps Services projenize bağlanın ve **derleme & sürüm**' ya tıklayın. 

1. **Derleme tanımları** bölümünde **+ Yeni**' ye tıklayın. **Boş** şablonu seçin.

    ![Azure DevOps-yeni derleme işlem hattı](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Yeni derlemeyi GitHub deposu kaynağıyla yapılandırın, **sürekli tümleştirmeyi**denetleyin ve Linux aracınızı kaydettiğiniz aracı kuyruğunu seçin. Derleme işlem hattını oluşturmak için **Oluştur** ' a tıklayın.

    ![Azure DevOps Services-derleme işlem hattı oluşturma](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. **Derleme tanımları** sayfasında, önce **Depo** sekmesini açın ve derlemeyi, Önkoşullarda oluşturduğunuz myshop projesinin çatalını kullanacak şekilde yapılandırın. **Varsayılan dal**olarak *ACS-docs* ' i seçtiğinizden emin olun.

    ![Azure DevOps Services-depo yapılandırması oluştur](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. **Tetikleyiciler** sekmesinde, her işlemeden sonra tetiklenecek derlemeyi yapılandırın. **Sürekli tümleştirme** ve **toplu değişiklikler**' i seçin.

    ![Azure DevOps Services-tetikleyici yapılandırması oluştur](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Derleme iş akışını tanımlama
Sonraki adımlar derleme iş akışını tanımlar. *Myshop* uygulaması için oluşturulacak beş kapsayıcı görüntüsü vardır. Her görüntü, proje klasörlerinde bulunan Dockerfile kullanılarak oluşturulmuştur:

* Productsapı
* Proxy
* Oytingsapı
* RecommendationsApi
* ShopFront

Her görüntü için, biri görüntüyü derlemek ve bir diğeri de görüntüyü Azure Container Registry 'de göndermek için iki Docker adımı eklemeniz gerekir. 

1. Derleme iş akışında bir adım eklemek için **+ derleme Ekle adımı** ve **Docker**' ı seçin.

    ![Azure DevOps Services-derleme adımları Ekle](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Her görüntü için, `docker build` komutunu kullanan bir adımı yapılandırın.

    ![Azure DevOps Services-Docker derlemesi](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Yapı işlemi için, Azure Container kayıt defterinizi, **görüntü oluşturma** eylemini ve her görüntüyü tanımlayan Dockerfile dosyasını seçin. **Oluşturma bağlamını** dockerfile kök dizini olarak ayarlayın ve **görüntü adını**tanımlayın. 
    
    Önceki ekranda gösterildiği gibi, Azure Container Registry 'nizin URI 'siyle birlikte görüntü adını başlatın. (Bu örnekteki derleme tanımlayıcısı gibi, görüntünün etiketini parametreleştirmek için de bir yapı değişkeni kullanabilirsiniz.)

1. Her görüntü için, `docker push` komutunu kullanan ikinci bir adımı yapılandırın.

    ![Azure DevOps Services-Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Gönderme işlemi için, Azure Container kayıt defterinizi, **bir görüntü gönder** eylemini seçin ve önceki adımda oluşturulan **görüntü adını** girin.

1. Beş görüntünün her biri için derleme ve gönderme adımlarını yapılandırdıktan sonra, derleme iş akışına iki adım daha ekleyin.

    a. Docker-Compose. yıml dosyasındaki *BuildNumber* oluşumunu geçerli derleme kimliğiyle değiştirmek için bash betiği kullanan bir komut satırı görevi. Ayrıntılar için aşağıdaki ekrana bakın.

    ![Azure DevOps Services-oluşturma dosyasını güncelleştir](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Sürümde kullanılabilmesi için güncelleştirilmiş oluşturma dosyasını yapı yapıtı olarak bırakılanlar görevi. Ayrıntılar için aşağıdaki ekrana bakın.

    ![Azure DevOps Services-oluşturma dosyası Yayımla](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. **Kaydet** ' e tıklayın ve derleme işlem hattınızı adlandırın.

## <a name="step-3-create-the-release-pipeline"></a>3\. adım: Yayın işlem hattı oluşturma

Azure DevOps Services [ortamlar genelinde yayınları yönetmenizi](https://www.visualstudio.com/team-services/release-management/)sağlar. Uygulamanızın farklı ortamlarınızda (geliştirme, test, ön üretim ve üretim gibi) sorunsuz bir şekilde dağıtıldığından emin olmak için sürekli dağıtımı etkinleştirebilirsiniz. Azure Container Service Docker Sısınma kümenizi temsil eden yeni bir ortam oluşturabilirsiniz.

![Azure DevOps Services-ACS yayını](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>İlk yayın kurulumu

1. Yayın işlem hattı oluşturmak için, **yayınlar** >  **+ yayın** ' a tıklayın.

1. Yapıt kaynağını yapılandırmak için,**yapıt kaynak bağlantısı**' **na tıklayın.**  >  Burada, bu yeni yayın ardışık düzenini önceki adımda tanımladığınız yapıya bağlayın. Bunu yaptığınızda, Docker-Compose. yıml dosyası yayın sürecinde kullanılabilir.

    ![Azure DevOps Services-yayın yapıtları](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Yayın tetikleyicisini yapılandırmak için **Tetikleyiciler** ' e tıklayın ve **sürekli dağıtım**' ı seçin. Tetikleyiciyi aynı yapıt kaynağında ayarlayın. Bu ayar, yapı başarıyla tamamlandıktan hemen sonra yeni bir sürümün başlamasını sağlar.

    ![Azure DevOps Services-yayın Tetikleyicileri](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Yayın iş akışını tanımlama

Yayın iş akışı, eklediğiniz iki görevden oluşur.

1. Daha önce yapılandırdığınız SSH bağlantısını kullanarak, oluşturma dosyasını Docker Sısınma ana düğümündeki bir *dağıtım* klasörüne güvenli bir şekilde kopyalamak için bir görev yapılandırın. Ayrıntılar için aşağıdaki ekrana bakın.

    ![Azure DevOps Services-yayın SCP 'si](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Ana düğümde ve `docker` `docker-compose` komutları çalıştırmak için bash komutunu yürütmek üzere ikinci bir görev yapılandırın. Ayrıntılar için aşağıdaki ekrana bakın.

    ![Azure DevOps Services-Bash yayını](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Ana bilgisayarda yürütülen komut, aşağıdaki görevleri yapmak için Docker CLı ve Docker-Compose CLı kullanın:

   - Azure Container Registry 'de oturum açın ( **değişkenler** sekmesinde tanımlanan üç yapı varinlarını kullanır)
   - Sısınma uç noktasıyla çalışmak için **DOCKER_HOST** değişkenini tanımlayın (: 2375)
   - Önceki güvenli kopyalama görevi tarafından oluşturulan *dağıtım* klasörüne gidin ve Docker-Compose. yıml dosyasını içerir 
   - Yeni `docker-compose` görüntüleri çekmek, hizmetleri durdurmak, Hizmetleri kaldırmak ve kapsayıcıları oluşturmak için komutları yürütün.

     >[!IMPORTANT]
     > Önceki ekranda gösterildiği gibi, **stderr üzerinde başarısız oldu** onay kutusunu işaretlenmemiş olarak bırakın. Bu önemli bir ayardır, çünkü `docker-compose` kapsayıcılar gibi çeşitli tanılama iletileri, standart hata çıktısındaki bir şekilde durdurulur veya silinir. Onay kutusunu işaretlerseniz, Azure DevOps Services yayın sırasında oluşan, ancak her şey iyi gitse de oluşan hataları raporlar.
     >
1. Bu yeni sürüm ardışık düzenini kaydedin.


>[!NOTE]
>Eski Hizmetleri durdurmakta ve yenisini çalıştırdığımızda bu dağıtımda bazı kapalı kalma süreleri vardır. Mavi yeşil bir dağıtım gerçekleştirerek bunu önlemek mümkündür.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4\. adımı. CI/CD işlem hattını sınama

Yapılandırma ile işiniz bittiğinde, bu yeni CI/CD işlem hattını test etme zamanı. Bunu test etmenin en kolay yolu, kaynak kodu güncellemek ve değişiklikleri GitHub deponuza yürütmesidir. Kodu gönderdikten sonra birkaç saniye sonra, Azure DevOps Services ' de çalışan yeni bir yapı görürsünüz. Başarılı bir şekilde tamamlandıktan sonra yeni bir yayın tetiklenir ve Azure Container Service kümesinde uygulamanın yeni sürümünü dağıtacaktır.

## <a name="next-steps"></a>Sonraki Adımlar

* Azure DevOps Services ile CI/CD hakkında daha fazla bilgi için [Azure Pipelines belge](/azure/devops/pipelines/?view=azure-devops) makalesine bakın.
