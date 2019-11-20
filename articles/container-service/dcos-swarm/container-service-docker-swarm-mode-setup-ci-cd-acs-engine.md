---
title: Kullanım DıŞı Azure Container Service altyapısı ve Sısınma modundaki CI/CD
description: Docker Sısınma modu, bir Azure Container Registry ve Azure DevOps ile Azure Container Service altyapısını kullanarak sürekli bir çok Kapsayıcılı .NET Core uygulaması sunun
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: fe24ab21a9a7d227d58e50c58f9aff2bd91e767f
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598561"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>Kullanım DıŞı Azure DevOps kullanarak ACS altyapısı ve Docker Sısınma modu ile Azure Container Service çok kapsayıcılı bir uygulama dağıtmak için tam CI/CD işlem hattı

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Bu makale, [Azure DevOps belgelerini kullanarak Docker Sısınma ile Azure Container Service çok kapsayıcılı bir uygulama dağıtmak Için tam CI/CD](container-service-docker-swarm-setup-ci-cd.md) işlem hattına dayalıdır*

Günümüzde, bulut için modern uygulamalar geliştirilirken en büyük zorluklardan biri, bu uygulamaları sürekli olarak sunabilmektedir. Bu makalede, kullanarak tam bir sürekli tümleştirme ve dağıtım (CI/CD) işlem hattı uygulamayı nasıl uygulayacağınızı öğreneceksiniz: 
* Docker Sısınma modundaki Azure Container Service altyapısı
* Azure Container Registry
* Azure DevOps

Bu makale, ASP.NET Core ile geliştirilen [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)'da bulunan basit bir uygulamayı temel alır. Uygulama dört farklı hizmetten oluşur: üç Web API 'si ve bir Web ön ucu:

![MyShop örnek uygulaması](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Amaç, bu uygulamayı Azure DevOps kullanarak bir Docker Sısınma modu kümesinde sürekli olarak sunabildir. Aşağıdaki şekilde bu sürekli teslim işlem hattı ayrıntıları verilmiştir:

![MyShop örnek uygulaması](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Bu adımların kısa bir açıklaması aşağıda verilmiştir:

1. Kod değişiklikleri kaynak kodu deposuna (burada, GitHub) kaydedilir 
2. GitHub, Azure DevOps 'da bir derlemeyi tetikler 
3. Azure DevOps, kaynakların en son sürümünü alır ve uygulamayı oluşturan tüm görüntüleri oluşturur 
4. Azure DevOps her görüntüyü Azure Container Registry hizmeti kullanılarak oluşturulan bir Docker kayıt defterine gönderir 
5. Azure DevOps yeni bir sürüm tetikler 
6. Sürüm, Azure Container Service kümesi ana düğümünde SSH kullanarak bazı komutları çalıştırır 
7. Kümedeki Docker Sısınma modu görüntülerin en son sürümünü çeker 
8. Uygulamanın yeni sürümü Docker Stack kullanılarak dağıtılır 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, aşağıdaki görevleri gerçekleştirmeniz gerekir:

- [Azure Container Service ACS altyapısıyla bir Ssıcak mod kümesi oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Azure Container Service'teki Swarm kümesine bağlanma](../container-service-connect.md)
- [Azure Container Registry oluşturma](../../container-registry/container-registry-get-started-portal.md)
- [Azure DevOps organizasyonu ve projesi oluşturma](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [GitHub deponuzu GitHub hesabınıza çatalla](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Azure Container Service’teki Docker Swarm düzenleyicisi eski tek başına Swarm’u kullanır. Şu anda, tümleşik [Swarm modu](https://docs.docker.com/engine/swarm/) (Docker 1.12 ve daha sonraki sürümleri) Azure Container Service'te desteklenen bir düzenleyici değildir. Bu nedenle, [Azure Marketi](https://azuremarketplace.microsoft.com)'Nde [ACS motoru](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), topluluk tarafından katkıda bulunulan [hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)veya Docker çözümü kullanıyoruz.
>

## <a name="step-1-configure-your-azure-devops-organization"></a>1\. adım: Azure DevOps kuruluşunuzu yapılandırma 

Bu bölümde, Azure DevOps kuruluşunuzu yapılandırırsınız. Azure DevOps Services uç noktaları yapılandırmak için, Azure DevOps projenizde, araç çubuğunda **Ayarlar** simgesine tıklayın ve **Hizmetler**' i seçin.

![Hizmetleri aç uç noktası](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Azure DevOps ve Azure hesabı 'nı bağlama

Azure DevOps projeniz ve Azure hesabınız arasında bir bağlantı kurun.

1. Sol tarafta **yeni hizmet uç noktası** > **Azure Resource Manager**' na tıklayın.
2. Azure DevOps 'ı Azure hesabınızla çalışacak şekilde yetkilendirmek için **aboneliğinizi** seçin ve **Tamam**'a tıklayın.

    ![Azure DevOps-Azure 'ı yetkilendir](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Azure DevOps ve GitHub 'ı bağlama

Azure DevOps projeniz ile GitHub hesabınız arasında bir bağlantı kurun.

1. Sol tarafta **yeni hizmet uç noktası** > **GitHub**' a tıklayın.
2. Azure DevOps 'ı GitHub hesabınızla çalışacak şekilde yetkilendirmek için **Yetkilendir** 'e tıklayın ve açılan penceredeki yordamı izleyin.

    ![Azure DevOps-GitHub 'ı yetkilendir](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Azure DevOps 'ı Azure Container Service kümenize bağlama

CI/CD işlem hattına almadan önce geçen adımlar, Azure 'daki Docker Sısınma kümenize yönelik dış bağlantıları yapılandırmaktır. 

1. Docker Sısınma kümesi için **SSH**türünde bir uç nokta ekleyin. Ardından, Sısınma kümenizin (ana düğüm) SSH bağlantı bilgilerini girin.

    ![Azure DevOps-SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Tüm yapılandırma şimdi yapılır. Sonraki adımlarda, uygulamayı oluşturup Docker Sısınma kümesine dağıtan CI/CD işlem hattını oluşturursunuz. 

## <a name="step-2-create-the-build-pipeline"></a>2\. adım: Derleme işlem hattını oluşturma

Bu adımda, Azure DevOps projeniz için bir derleme işlem hattı ayarlarsınız ve kapsayıcı görüntüleriniz için derleme iş akışını tanımlarsınız

### <a name="initial-pipeline-setup"></a>İlk işlem hattı kurulumu

1. Bir derleme işlem hattı oluşturmak için Azure DevOps projenize bağlanın ve **derleme & yayınla**' ya tıklayın. **Derleme tanımları** bölümünde **+ Yeni**' ye tıklayın. 

    ![Azure DevOps-yeni derleme işlem hattı](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. **Boş işlemi**seçin.

    ![Azure DevOps-yeni boş derleme işlem hattı](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Ardından, **değişkenler** sekmesine tıklayın ve iki yeni değişken oluşturun: **Registryurl** ve, **turl**. Kayıt defterinizin ve küme aracıları DNS 'nizin değerlerini yapıştırın.

    ![Azure DevOps-derleme değişkenleri yapılandırması](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. **Derleme tanımları** sayfasında, **Tetikleyiciler** sekmesini açın ve derlemeyi, Önkoşullarda oluşturduğunuz myshop projesinin çatallarıyla sürekli tümleştirme kullanacak şekilde yapılandırın. Ardından **toplu değişiklikler**' i seçin. **Dal belirtimi**olarak *Docker-Linux* ' u seçtiğinizden emin olun.

    ![Azure DevOps-derleme deposu yapılandırması](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Son olarak, **Seçenekler** sekmesine tıklayın ve varsayılan aracı kuyruğunu **barındırılan Linux önizlemesi**olarak yapılandırın.

    ![Azure DevOps-konak Aracısı yapılandırması](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Derleme iş akışını tanımlama
Sonraki adımlar derleme iş akışını tanımlar. İlk olarak, kodun kaynağını yapılandırmanız gerekir. Bunu yapmak için **GitHub** ve deponuzu ve dalınızı (Docker-Linux) seçin.

![Azure DevOps-kod kaynağını yapılandırma](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

*Myshop* uygulaması için oluşturulacak beş kapsayıcı görüntüsü vardır. Her görüntü, proje klasörlerinde bulunan Dockerfile kullanılarak oluşturulmuştur:

* Productsapı
* Proxy
* Oytingsapı
* RecommendationsApi
* ShopFront

Her görüntü için iki Docker adımı, bir görüntüyü oluşturmak ve bir diğeri de görüntüyü Azure Container Registry 'de göndermek için bir adım gerekir. 

1. Derleme iş akışında bir adım eklemek için **+ derleme Ekle adımı** ve **Docker**' ı seçin.

    ![Azure DevOps-derleme adımları Ekle](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Her görüntü için, `docker build` komutunu kullanan bir adımı yapılandırın.

    ![Azure DevOps-Docker derlemesi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Yapı işlemi için Azure Container Registry, **görüntü oluştur** eylemini ve her görüntüyü tanımlayan Dockerfile dosyasını seçin. **Çalışma dizinini** Dockerfile kök dizini olarak ayarlayın, **görüntü adını**tanımlayın ve **en son etiketi içer**' i seçin.
    
    Görüntü adı şu biçimde olmalıdır: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. **[Name]** öğesini görüntü adıyla değiştirin:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Her görüntü için, `docker push` komutunu kullanan ikinci bir adımı yapılandırın.

    ![Azure DevOps-Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Gönderme işlemi için, Azure Container kayıt defterinizi, **görüntü gönder** eylemini seçin, önceki adımda oluşturulan **görüntü adını** girin ve **en son etiketi içer**' i seçin.

4. Beş görüntünün her biri için derleme ve gönderme adımlarını yapılandırdıktan sonra, derleme iş akışında üç adım daha ekleyin.

   ![Azure DevOps-komut satırı ekleme görevi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Docker-Compose. yıml dosyasındaki *registryurl* oluşumunu registryurl değişkeniyle değiştirmek için bash betiği kullanan bir komut satırı görevi. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps-kayıt defteri URL 'siyle oluşturma dosyası güncelleştirme](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Docker-Compose. yıml dosyasındaki, bir bash betiğini, bir bash komut dosyası kullanarak, bir bash betiği kullanarak bir komut satırı görevi.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Sürümde kullanılabilmesi için güncelleştirilmiş oluşturma dosyasını yapı yapıtı olarak bırakılanlar görevi. Ayrıntılar için aşağıdaki ekrana bakın.

      ![Azure DevOps-yapıyı Yayımla](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps-yayın oluşturma dosyası](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Yapı ardışık yapınızı test etmek için **& kuyruğu kaydet** ' e tıklayın.

   ![Azure DevOps-Save ve Queue](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps-yeni kuyruk](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. **Derleme** doğruysa, bu ekranı görmeniz gerekir:

   ![Azure DevOps-derleme başarılı oldu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>3\. adım: Yayın işlem hattı oluşturma

Azure DevOps, [ortamların tamamında yayınları yönetmenizi](https://www.visualstudio.com/team-services/release-management/)sağlar. Uygulamanızın farklı ortamlarınızda (geliştirme, test, ön üretim ve üretim gibi) sorunsuz bir şekilde dağıtıldığından emin olmak için sürekli dağıtımı etkinleştirebilirsiniz. Azure Container Service Docker Ssıcak mod kümenizi temsil eden bir ortam oluşturabilirsiniz.

![Azure DevOps-ACS yayını](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>İlk yayın kurulumu

1. Yayın işlem hattı oluşturmak için, **yayınlar** >  **+ yayın** ' a tıklayın.

2. Yapıt kaynağını yapılandırmak için,**yapıt kaynak bağlantısı**' **na tıklayın.**  >  Burada, bu yeni yayın ardışık düzenini önceki adımda tanımladığınız yapıya bağlayın. Bundan sonra, Docker-Compose. yıml dosyası yayın sürecinde bulunur.

    ![Azure DevOps-yayın yapıtları](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Yayın tetikleyicisini yapılandırmak için **Tetikleyiciler** ' e tıklayın ve **sürekli dağıtım**' ı seçin. Tetikleyiciyi aynı yapıt kaynağında ayarlayın. Bu ayar, derleme başarıyla tamamlandığında yeni bir sürümün başlamasını sağlar.

    ![Azure DevOps-yayın Tetikleyicileri](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Sürüm değişkenlerini yapılandırmak için, **değişkenler** ' e tıklayın ve **+ değişken** ' i seçerek kayıt defteri bilgileriyle üç yeni değişken oluşturun: **Docker. UserName**, **Docker. Password**ve **Docker. Registry**. Kayıt defterinizin ve küme aracıları DNS 'nizin değerlerini yapıştırın.

    ![Azure DevOps-derleme deposu yapılandırması](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Önceki ekranda gösterildiği gibi Docker. Password içindeki **Kilitle** onay kutusuna tıklayın. Bu ayar, parolayı kısıtlamak için önemlidir.
    >

### <a name="define-the-release-workflow"></a>Yayın iş akışını tanımlama

Yayın iş akışı, eklediğiniz iki görevden oluşur.

1. Daha önce yapılandırdığınız SSH bağlantısını kullanarak, oluşturma dosyasını Docker Sısınma ana düğümündeki bir *dağıtım* klasörüne güvenli bir şekilde kopyalamak için bir görev yapılandırın. Ayrıntılar için aşağıdaki ekrana bakın.
    
    Kaynak klasör:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps-yayın SCP 'si](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Ana düğümde ve `docker` `docker stack deploy` komutları çalıştırmak için bash komutunu yürütmek üzere ikinci bir görev yapılandırın. Ayrıntılar için aşağıdaki ekrana bakın.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps-yayın Bash 'i](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Ana bilgisayarda yürütülen komut, aşağıdaki görevleri yapmak için Docker CLı ve Docker-Compose CLı kullanır:

   - Azure Container Registry 'de oturum açın ( **değişkenler** sekmesinde tanımlanan üç yapı değişkenini kullanır)
   - Sısınma uç noktasıyla çalışmak için **DOCKER_HOST** değişkenini tanımlayın (: 2375)
   - Önceki güvenli kopyalama görevi tarafından oluşturulan *dağıtım* klasörüne gidin ve Docker-Compose. yıml dosyasını içerir 
   - Yeni `docker stack deploy` görüntüleri çekmek ve kapsayıcıları oluşturmak için komutları yürütün.

     >[!IMPORTANT]
     > Önceki ekranda gösterildiği gibi, **stderr üzerinde başarısız oldu** onay kutusunu işaretlenmemiş olarak bırakın. Bu ayar, standart hata çıkışında kapsayıcılar durdurulduğunda veya silinmekte `docker-compose` olan çeşitli tanılama iletilerinin yazdırılmasından dolayı yayın sürecini tamamlamamızı sağlar. Onay kutusunu işaretlerseniz, Azure DevOps, yayın sırasında hata oluştuğunu bildirir, ancak her şey iyi bir şekilde gerçekleşse bile.
     >
3. Bu yeni sürüm ardışık düzenini kaydedin.

## <a name="step-4-test-the-cicd-pipeline"></a>4\. Adım: CI/CD işlem hattını sınama

Yapılandırma ile işiniz bittiğinde, bu yeni CI/CD işlem hattını test etme zamanı. Bunu test etmenin en kolay yolu, kaynak kodu güncellemek ve değişiklikleri GitHub deponuza yürütmesidir. Kodu gönderdikten sonra birkaç saniye sonra, Azure DevOps 'da çalışan yeni bir derleme görürsünüz. Başarılı bir şekilde tamamlandıktan sonra yeni bir sürüm tetiklenir ve Azure Container Service kümesinde uygulamanın yeni sürümü dağıtılır.

## <a name="next-steps"></a>Sonraki adımlar

* Azure DevOps ile CI/CD hakkında daha fazla bilgi için [Azure Pipelines belge](/azure/devops/pipelines/?view=azure-devops) makalesine bakın.
* ACS altyapısı hakkında daha fazla bilgi için bkz. [ACS altyapısı GitHub deposu](https://github.com/Azure/acs-engine).
* Docker Sısınma modu hakkında daha fazla bilgi için bkz. [Docker sısınma moduna genel bakış](https://docs.docker.com/engine/swarm/).
