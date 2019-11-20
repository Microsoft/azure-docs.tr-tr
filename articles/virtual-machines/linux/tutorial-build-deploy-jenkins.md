---
title: Eğitim-Jenkins 'den Azure VM 'lerine Azure DevOps Services ile CI/CD
description: Bu öğreticide, bir Node.js uygulaması için Jenkins kullanarak Visual Studio Team Services veya Microsoft Team Foundation Server’daki Release Management’tan Azure sanal makinelerine yönelik sürekli tümleştirme (CI) ve sürekli dağıtımın (CD) nasıl ayarlanacağını öğreneceksiniz
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 734b0a516d4a9fe882545dd5cde5a57d8af719cb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034578"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Öğretici: Jenkins ve Azure DevOps Services kullanarak uygulamanızı Azure üzerinde Linux sanal makinelerine dağıtma

Sürekli tümleştirme (CI) ve sürekli dağıtım (CD), kodunuzu derleyebileceğiniz, yayınlayabileceğiniz ve dağıtabileceğiniz bir işlem hattı oluşturur. Azure DevOps Services, Azure’a dağıtım için eksiksiz ve tam özellikli bir dizi CI/CD otomasyon aracı sağlar. Jenkins, CI/CD otomasyonu sağlayan, popüler bir üçüncü taraf CI/CD sunucu tabanlı aracıdır. Bulut uygulamanızı ve hizmetinizi nasıl sunacağınızı özelleştirmek için Azure DevOps Services ve Jenkins’i birlikte kullanabilirsiniz.

Bu öğreticide, Node.js web uygulaması derlemek için Jenkins’i kullanacaksınız. Ardından, Linux sanal makineleri (VM’ler) içeren

bir [dağıtım grubuna](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) dağıtmak için Azure DevOps kullanın. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Örnek uygulamayı alma.
> * Jenkins eklentilerini yapılandırma.
> * Node.js için Jenkins Serbest stil projesi yapılandırma.
> * Azure DevOps Services tümleştirmesi için Jenkins’i yapılandırın.
> * Jenkins hizmet uç noktası oluşturma.
> * Azure sanal makineleri için dağıtım grubu oluşturma.
> * Azure Pipelines bir yayın işlem hattı oluşturun.
> * El ile ve CI ile tetiklenen dağıtımlar yürütme.

## <a name="before-you-begin"></a>Başlamadan önce

* Bir Jenkins sunucusuna erişmeniz gerekir. Henüz bir Jenkins sunucusu oluşturmadıysanız bkz. [Azure sanal makinesinde Jenkins yöneticisi oluşturma](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Azure DevOps Services kuruluşunuzda oturum açın (**https://{yourorganization}.visualstudio.com**). 
  Ücretsiz bir [Azure DevOps Services kuruluşu](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) edinebilirsiniz.

  > [!NOTE]
  > Daha fazla bilgi için, bkz. [Azure DevOps Services’a bağlanma](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Dağıtım hedefi için bir Linux sanal makinesi gerekir.  Daha fazla bilgi için bkz. [Azure CLI ile Linux sanal makineleri oluşturma ve yönetme](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Sanal makineniz için 80 numaralı gelen bağlantı noktasını açın. Daha fazla bilgi için bkz. [Azure portalını kullanarak ağ güvenlik grupları oluşturma](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Örnek uygulamayı alma

Bir Git deposunda depolanan, dağıtılacak bir uygulamanız olması gerekir.
Bu öğretici için, [GitHub’dan erişebileceğiniz bu örnek uygulamayı](https://github.com/azure-devops/fabrikam-node) kullanmanızı öneririz. Bu öğretici, Node.js ve bir uygulama yüklemek için kullanılan örnek bir betik içerir. Kendi deponuzla çalışmak istiyorsanız benzer bir örnek yapılandırmanız gerekir.

Bu uygulamanın çatalını oluşturun ve bu öğreticinin daha sonraki adımlarında kullanmak üzere konumu (URL) not edin. Daha fazla bilgi için bkz. [Depo çatalı oluşturma](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Uygulama, [Yeoman](https://yeoman.io/learning/index.html) aracılığıyla oluşturulmuştur. Express, bower ve grunt kullanır. Ayrıca bağımlılıklar olarak bazı npm paketlerini içerir.
> Örnek, Nginx’i ayarlayan ve uygulamayı dağıtan bir betik de içerir. Sanal makinelerde yürütülür. Betik özellikle:
> 1. Node, Nginx ve PM2'yi yükler.
> 2. Nginx ve PM2’yi yapılandırır.
> 3. Düğüm uygulamasını başlatır.

## <a name="configure-jenkins-plug-ins"></a>Jenkins eklentilerini yapılandırma

İlk olarak iki Jenkins eklentisini yapılandırmanız gerekir: **NodeJS** ve **VS Team Services Sürekli Dağıtımı**.

1. Jenkins hesabınızı açın ve **Jenkins’i yönet** seçeneğini belirleyin.
2. **Jenkins’i yönet** sayfasında **Eklentileri yönet** seçeneğini belirleyin.
3. Listeyi filtreleyerek **NodeJS** eklentisini bulun ve **Yeniden başlatmadan yükle** seçeneğini belirleyin.
    ![Jenkins’e NodeJS eklentisini ekleme](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Listeyi filtreleyerek **VS Team Services Sürekli Dağıtımı** eklentisini bulun ve **Yeniden başlatmadan yükle** seçeneğini belirleyin.
5. Jenkins panosuna geri dönüp **Jenkins’i yönet** seçeneğini belirleyin.
6. **Genel Araç Yapılandırması** seçeneğini belirleyin. **NodeJS** öğesini bulun ve **NodeJS yüklemeleri** seçeneğini belirleyin.
7. **Otomatik olarak yükle** seçeneğini belirleyin ve bir **Ad** değeri girin.
8. **Kaydet**’i seçin.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Node.js için Jenkins Freestyle projesi yapılandırma

1. **Yeni Öğe**’yi seçin. Bir öğe adı girin.
2. **Serbest stilde proje**’yi seçin. **Tamam**’ı seçin.
3. **Kaynak Kodu Yönetimi** sekmesinde **Git**’i seçin ve uygulama kodunuzu içeren deponun ve dalın ayrıntılarını girin.    
    ![Derlemenize bir depo ekleme](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. **Derleme Tetikleyicileri** sekmesinde **SCM’yi Yokla** seçeneğini belirleyin ve üç dakikada bir Git deposundaki değişiklikleri yoklamak için `H/03 * * * *` zamanlamasını girin. 
5. **Derleme Ortamı** sekmesinde **Düğüm &amp; npm bin/ klasör YOLUNU sağla** seçeneğini belirleyin ve **NodeJS Yüklemesi** değerini seçin. **npmrc dosyası** değerini **use system default** olarak ayarlanmış şekilde bırakın.
6. **Derleme** sekmesinde **Kabuk yürüt**’ü seçin ve `npm install` komutunu girerek tüm bağımlılıkların güncelleştirildiğinden emin olun.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Azure DevOps Services tümleştirmesi için Jenkins’i yapılandırma

> [!NOTE]
> Aşağıdaki adımlar için kullandığınız kişisel erişim belirtecinin, Azure DevOps Services’ta *Yayınlama* (okuma, yazma, yürütme ve yönetme) iznini içerdiğinden emin olun.
 
1.  Önceden oluşturmadıysanız, Azure DevOps Services kuruluşunuzda bir PAT oluşturun. Jenkins, Azure DevOps Services kuruluşunuza erişmek için bu bilgileri zorunlu tutar. Bu bölümün ilerleyen kısımlarındaki adımlar için belirteç bilgilerini saklayın.
  
    Belirteç oluşturma hakkında bilgi edinmek için [Azure DevOps Services için nasıl kişisel erişim belirteci oluştururum?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) bölümünü okuyun.
2. **Derleme Sonrası Eylemler** sekmesinde **Derleme sonrası eylem ekle** seçeneğini belirleyin. **Yapıtları arşivle**’yi seçin.
3. **Arşivlenecek dosyalar** için `**/*` değerini girerek tüm dosyaları dahil edin.
4. Başka bir eylem oluşturmak için **Derleme sonrası eylem ekle**’yi seçin.
5. **TFS/Team Services’te yayınlamayı tetikle** seçeneğini belirleyin. Azure DevOps Services kuruluşunuz için **https://{your-organization-name}.visualstudio.com** gibi bir URI girin.
6. **Proje** adını girin.
7. Yayın işlem hattı için bir ad seçin. (Bu işlem hattını daha sonra Azure DevOps Services’ta oluşturun.)
8. Azure DevOps Services veya Team Foundation Server ortamınıza bağlanmak için kimlik bilgilerini girin:
   - Azure DevOps Services kullanıyorsanız, **Kullanıcı adı** alanını boş bırakın. 
   - Team Foundation Server’ın şirket içi bir sürümünü kullanıyorsanız kullanıcı adı ve parola girin.    
   ![Jenkins derleme sonrası eylemlerini yapılandırma](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Jenkins projesini kaydedin.


## <a name="create-a-jenkins-service-endpoint"></a>Jenkins hizmet uç noktası oluşturma

Hizmet uç noktası, Azure DevOps Services’ın Jenkins’e bağlanmasına olanak sağlar.

1. Azure DevOps Services’ta **Hizmetler** sayfasını açın, **Yeni Hizmet Uç Noktası** listesini açın ve **Jenkins**’i seçin.
   ![Jenkins uç noktası ekleme](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Bağlantı için bir ad girin.
3. Jenkins sunucunuzun URL’sini girin ve **Güvenilmeyen SSL sertifikalarını kabul et** seçeneğini belirleyin. Örnek bir URL: **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Jenkins hesabınız için kullanıcı adı ve parolayı girin.
5. Bilgilerin doğru olup olmadığını denetlemek için **Bağlantıyı doğrula**’yı seçin.
6. Hizmet uç noktasını oluşturmak için **Tamam**’ı seçin.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Azure sanal makineleri için dağıtım grubu oluşturma

Yayın işlem hattının sanal makinenize dağıtılabilmesi için Azure DevOps Services aracısını kaydetmek üzere bir [dağıtım grubunuz](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) olması gerekir. Dağıtım grupları, dağıtım için hedef makinelerin mantıksal gruplarının tanımlanmasını ve her bir makineye gerekli aracının yüklenmesini kolaylaştırır.

   > [!NOTE]
   > Aşağıdaki yordamda, önkoşulları yüklediğinizden ve *betiği sudo ayrıcalıklarıyla çalıştırmadığınızdan* emin olun.

1. **Derleme ve Yayın** hub’ının **Yayınlar&amp; sekmesini açın,** Dağıtım grupları **’nı açın ve** + Yeni **’yi seçin.
2. Dağıtım grubu için bir ad ve isteğe bağlı bir açıklama girin. Ardından **Oluştur**’u seçin.
3. Dağıtım hedefi sanal makineniz için işletim sistemini seçin. Örneğin, **Ubuntu 16.04+** seçeneğini belirleyin.
4. **Kimlik doğrulaması için betikte kişisel bir erişim belirteci kullan** seçeneğini belirleyin.
5. **Sistem önkoşulları** bağlantısını seçin. İşletim sisteminiz için önkoşullarını yükleyin.
6. **Betiği panoya kopyala**’yı seçerek betiği kopyalayın.
7. Dağıtım hedefi sanal makinenizde oturum açın ve betiği çalıştırın. Betiği sudo ayrıcalıklarıyla çalıştırmayın.
8. Yüklemeden sonra sizden dağıtım grubu etiketleri istenir. Varsayılanları kabul edin.
9. Azure DevOps Services’ta, **Dağıtım Grupları** bölümündeki **Hedefler** kısmında yeni kaydettiğiniz sanal makinenizin olup olmadığını denetleyin.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Azure Pipelines yayın işlem hattı oluşturma

Yayın işlem hattı, Azure Pipelines’ın uygulamayı dağıtmak için kullandığı işlemi belirtir. Bu örnekte, bir kabuk betiği yürütürsünüz.

Azure Pipelines’da yayın işlem hattı oluşturmak için:

1. **Derleme**  Yayın **hub’ının &amp;Yayınlar** sekmesini açın ve **Yayın işlem hattı oluştur**’u seçin. 
2. **Boş** şablonunu seçerek **Boş işlem** ile başlamayı seçin.
3. **Yapıtlar** bölümünde **+ Yapıt Ekle** seçeneğini belirleyin ve **Kaynak türü** için **Jenkins**’i seçin. Jenkins hizmet uç noktası bağlantınızı seçin. Ardından Jenkins kaynak işini seçin ve **Ekle** seçeneğini belirleyin.
4. **Ortam 1**’in yanındaki üç noktayı seçin. **Dağıtım grubu aşaması ekle**’yi seçin.
5. Dağıtım grubunuzu seçin.
5. **Dağıtım grubu aşaması+’na görev eklemek için**  seçeneğini belirleyin.
6. **Kabuk Betiği** görevini seçin ve **Ekle** seçeneğini belirleyin. **Kabuk Betiği** görevi, Node.js’yi yükleyip uygulamayı başlatmak için her bir sunucuda çalıştırılacak bir betik için yapılandırmayı sağlar.
8. **Betik Yolu** için **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh** girin.
9. **Gelişmiş**’i seçin ve **Çalışma Dizinini Belirtin** seçeneğini etkinleştirin.
10. **Çalışma Dizini** için **$(System.DefaultWorkingDirectory)/Fabrikam-Node** girin.
11. Yayın işlem hattının adını, Jenkins’te derlemenin **Derleme Sonrası Eylemler** sekmesinde belirttiğiniz ad olacak şekilde düzenleyin. Jenkins, kaynak yapıtlar güncelleştirildiğinde yeni yayını tetikleyebilmesi için bu adı zorunlu tutar.
12. **Kaydet**’i seçin ve **Tamam**’ı seçerek yayın işlem hattını kaydedin.

## <a name="execute-manual-and-ci-triggered-deployments"></a>El ile ve CI ile tetiklenen dağıtımlar yürütme

1. **+ Yayın**’ı seçin ve **Yayın Oluştur** seçeneğini belirleyin.
2. Vurgulanan açılır listede tamamladığınız derlemeyi seçin ve **Kuyruk** seçeneğini belirleyin.
3. Açılır iletide yayın bağlantısını seçin. Örneğin: "**Yayın-1** yayını oluşturulmuştur."
4. Yayın konsolu çıktısını izlemek için **Günlükler** sekmesini açın.
5. Tarayıcınızda, dağıtım grubunuza eklediğiniz sunuculardan birinin URL’sini açın. Örneğin, **http://{your-server-ip-address}** girin.
6. Kaynak Git deposuna gidin ve app/views/index.jade dosyasındaki **h1** başlığının içeriklerini, değiştirilen bazı metinlerle değiştirin.
7. Değişikliğinizi işleyin.
8. Birkaç dakika sonra, Azure DevOps’un **Yayınlar** sayfasında yeni bir yayının oluşturulduğunu görürsünüz. Gerçekleşen dağıtımı görmek için yayını açın. Tebrikler!

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins eklentisiyle ilgili sorunlarını giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, derleme için Jenkins’i ve yayın için Azure DevOps Services’ı kullanarak bir uygulamanın Azure’a dağıtımını otomatikleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Jenkins’te uygulamanızı derleme.
> * Azure DevOps Services tümleştirmesi için Jenkins’i yapılandırın.
> * Azure sanal makineleri için dağıtım grubu oluşturma.
> * VM’leri yapılandıran ve uygulamayı dağıtan bir yayın işlem hattı oluşturun.

LAMP (Linux, Apache, MySQL ve PHP) yığınını dağıtma hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [LAMP yığını dağıtma](tutorial-lamp-stack.md)
