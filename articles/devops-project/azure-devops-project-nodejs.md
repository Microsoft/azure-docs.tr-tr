---
title: GatsbyJS ve Azure DevOps Starter ile bir PWA için CI/CD işlem hattı oluşturma
description: GatsbyJS ve Basitleştirilmiş Azure DevOps Starter oluşturma deneyimini kullanarak bir NodeJS aşamalı Web uygulaması (PWA) oluşturmayı öğrenin.
ms.prod: devops
ms.technology: devops-cicd
documentationcenter: vs-devops-build
author: mijacobs
ms.author: mijacobs
ms.workload: web
ms.topic: quickstart
ms.date: 03/24/2020
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d6d8a10dffa3801fe343b3b479265fac5612b021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556565"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Azure DevOps Starter ile Node.js için Azure Pipelines bir CI/CD işlem hattı oluşturma

Bu hızlı başlangıçta, [Gatsbyjs](https://www.gatsbyjs.org/) ve Basitleştirilmiş Azure DevOps Starter oluşturma deneyimini kullanarak bir NodeJS aşamalı Web UYGULAMASı (PWA) oluşturacaksınız. İşiniz bittiğinde, Azure Pipelines ' de PWA 'niz için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı vardır. Azure DevOps Starter, geliştirme, dağıtma ve izleme için gerekenleri ayarlar.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Azure DevOps](https://azure.microsoft.com/services/devops/) organizasyonu.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure Portal](https://portal.azure.com)oturum açın ve sol bölmede **kaynak oluştur**' u seçin. 

   ![Azure portal Azure kaynağı oluşturma](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. Node.js örnek uygulamasını seçin.   

    ![Node.js örneği seçin](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Varsayılan örnek çerçeve **Express.js**’dir. Seçimi **basit Node.js uygulaması** olarak değiştirip **İleri**' yi seçin. 

    ![Basit Node.js uygulamasını seçin](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Bu adımda sunulan dağıtım hedefleri, adım 2 ' de seçilen uygulama çerçevesi tarafından belirlenir. Bu örnekte, varsayılan dağıtım hedefi **Windows Web uygulamasıdır** . **Kapsayıcılar için Web App** kümeyi bırakın ve **İleri ' yi** seçin.

    ![Dağıtım hedefini seçin](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Proje adı ve Azure aboneliği yapılandırma

1. DevOps Başlatıcı oluşturma iş akışının son adımında, bir proje adı atar, bir Azure aboneliği seçin ve **bitti**' yi seçin.  

    ![Proje adı atama ve abonelik seçme](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Projeniz derlenirken ve uygulamanız Azure 'a dağıtıldığında bir Özet sayfası görüntülenir. Kısa bir süre sonra, [Azure DevOps kuruluşunuzda](https://dev.azure.com/) bir git deposu, Kanban panosu, bir dağıtım işlem hattı, test planları ve uygulamanız için gereken yapıtları içeren bir proje oluşturulur.  

## <a name="managing-your-project"></a>Projenizi yönetme

1. **Tüm kaynaklara** gidin ve DevOps Starter 'nizi bulun. **DevOps başlangıç** hesabınızı seçin.

    ![Kaynak listesinde Azure DevOps panosu](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Proje giriş sayfanız, kod deposu, CI/CD işlem hattı ve çalışan uygulamanıza yönelik bir bağlantı için görünürlük sağlayan bir panoya yönlendirilirsiniz. Uygulamanızı **Azure DevOps** 'da görüntülemek Için **proje giriş sayfasını** seçin ve başka bir tarayıcı sekmesinde, canlı örnek uygulamayı görüntülemek için **uygulama uç noktasını** seçin. Bu örneği daha sonra GatsbyJS tarafından oluşturulan PWA kullanacak şekilde değiştiririz.

    ![Azure DevOps panosu](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Azure DevOps projenizden, ekip üyelerini işbirliği yapmak ve işinizi izlemeye başlamak için bir Kanban panosu kurmak üzere davet edebilirsiniz. Daha fazla bilgi için [buraya](/azure/devops/user-guide/what-is-azure-devops)bakın.

![Azure DevOps genel bakış](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Depoyu kopyalama ve Gatsınızı PWA 'dan yüklemesi

DevOps Starter, Azure Repos veya GitHub 'da bir git deposu oluşturur. Bu örnek, bir Azure deposu oluşturdu. Sonraki adım, depoyu klonlamadır ve değişiklikler yapar.

1. **DevOps projenizden** **depoları** seçin ve ardından **Kopyala**' ya tıklayın.  Git deposunu masaüstünüze kopyalamak için çeşitli mekanizmalar vardır.  Geliştirme deneyiminize uygun olanı seçin.  

    ![Depoyu kopyalama](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Depo, masaüstünüze kopyalandıktan sonra, başlangıç şablonunda bazı değişiklikler yapın. Terminalinizden GatsbyJS CLı 'yı yükleyerek başlayın.

   ```powershell
    npm install -g gatsby
   ```

1. Terminalden deponuzın köküne gidin. Şöyle görünen üç klasör içermelidir:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Uygulama klasöründeki tüm dosyaları bir Gatsby Starter ile değiştirecek şekilde istemiyoruz. Aşağıdaki komutları sırayla kesmek için sırayla çalıştırın.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Örnek bir PWA oluşturmak için Gatsby CLı kullanın. `gatsby new`PWA Sihirbazı ' nı başlatmak için terminalden çalıştırın ve `gatsby-starter-blog` Başlangıç şablonunuz için öğesini seçin. Bu örneğe benzemelidir:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Artık adlı bir klasörünüz var `my-gatsby-project` . Olarak yeniden adlandırın `Application` ve içine kopyalayın `Dockerfile` .
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. En sevdiğiniz düzenleyicide Dockerfile dosyasını açın ve ilk satırı ' den ' e değiştirin `FROM node:8` `FROM node:12` . Bu değişiklik, kapsayıcının sürüm 8. x yerine 12. x sürümünü Node.js kullanmasını sağlar. GatsbyJS Node.js için daha modern sürümler gerektirir.

1. Ardından, uygulama klasöründeki dosya package.jsaçın ve [komut dosyaları alanını](https://docs.npmjs.com/files/package.json#scripts) düzenleyerek geliştirme ve üretim sunucularınızın tüm kullanılabilir ağ arabirimlerini (örneğin 0.0.0.0) ve bağlantı noktası 80 ' i dinlemesine emin olun. Bu ayarlar olmadan, Container App Service, kapsayıcıyı Kapsayıcınız içinde çalışan Node.js uygulamanıza yönlendiremedi. `scripts`Alan aşağıda verilmiştir. Özellikle,, `develop` `serve` ve `start` hedeflerini varsayılanlarından değiştirmek istersiniz.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>CI/CD işlem hatlarınızı düzenleyin

1. Önceki bölümde kodu işlemeden önce, derleme ve yayın işlem hatlarınız üzerinde bazı değişiklikler yapın. ' Derleme işlem hattınızı düzenleyin ve düğüm görevini Node.js 12. x sürümünü kullanacak şekilde güncelleştirin. **Görev sürümü** alanını 1. x ve **Sürüm** alanı olarak 12. x olarak ayarlayın.

    ![Node.js 12. x olarak güncelleştir](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. Bu hızlı başlangıçta, birim testleri oluşturmadık ve derleme işlem hattımızda bu adımları devre dışı bıraktık. Testleri yazdığınızda, bu adımları yeniden etkinleştirebilirsiniz. **Test bağımlılıklarını yüklemek** ve **birim testlerini çalıştırmak** ve devre dışı bırakmak için sağ tıklayın.

    ![Yapı testlerini devre dışı bırak](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Yayın işlem hattınızı düzenleyin.

    ![Yayın ardışık düzenini düzenleme](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Yapı ardışık düzeninde olduğu gibi, düğüm görevini 12. x kullanacak şekilde değiştirin ve iki test görevini devre dışı bırakın. Yayınlarınızın bu ekran görüntüsüne benzemesi gerekir.

    ![Yayın işlem hattı tamamlandı](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Tarayıcının sol tarafında **Görünümler/index. Pug** dosyasına gidin.

1. **Düzenle**' yi seçin ve ardından H2 başlığında bir değişiklik yapın.  Örneğin, **Azure DevOps Starter ile hemen kullanmaya** başlayın yazın veya başka bir değişiklik yapın.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps başlangıç panosuna gidin.   
Artık sürmekte olan bir derleme görmeniz gerekir. Yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Değişikliklerinizi işleyin ve Azure CI/CD işlem hattını inceleyin

Önceki iki adımda, git deponuza bir Gatsby tarafından üretilen PWA eklediniz ve kodu derlemek ve dağıtmak için işlem hatlarınızı düzenlediniz. Kodu teslim edebilir ve derleme ve yayın işlem hattı aracılığıyla ilerlemeyi izleyebilirsiniz.

1. Projenizin git deposunun kökünden bir terminalde, kodunuzu Azure DevOps projenize göndermek için aşağıdaki komutları çalıştırın:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Bir derleme tamamlandıktan hemen sonra başlatılır `git push` . **Azure DevOps panosundan** ilerlemeyi izleyebilirsiniz.

3. Birkaç dakika sonra, derleme ve yayın işlem hatlarınız bitirilmeli ve PWA 'nın bir kapsayıcıya dağıtılması gerekir. Yukarıdaki panodan **uygulama uç noktası** bağlantısına tıklayın ve Bloglar Için bir Gatsby Starter projesi görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık kaynaklara gerek duymadığınızda oluşturduğunuz Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulur. Bu derleme ve yayın işlem hatlarını takımınızın ihtiyaçlarını karşılayacak şekilde değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](/azure/devops/pipelines/release/define-multistage-release-process)
