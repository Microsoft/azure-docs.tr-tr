---
title: Jenkins kullanarak Azure Service Fabric Linux uygulamalarınız için sürekli derleme ve Tümleştirme | Microsoft Docs
description: Jenkins kullanarak Service Fabric Linux uygulamanız için sürekli derleme ve Tümleştirme
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: jeconnoc
ms.openlocfilehash: b757a0a5f3ce968b396fa89d5b32c18257d620c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875093"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Linux uygulamalarınızı derlemek ve dağıtmak için Jenkins kullanın
Jenkins, uygulamanızın sürekli tümleştirme ve dağıtımı için yaygın olarak kullanılan bir araçtır. Jenkins kullanarak Azure Service Fabric uygulamanızı derleme ve dağıtma işlemi aşağıda açıklanmaktadır.

## <a name="topic-overview"></a>Konuya genel bakış
Bu makalede, Jenkins ortamınızı ayarlamanın yanı sıra, uygulamanızı oluşturulduktan sonra bir Service Fabric kümesine dağıtmanın farklı yolları ele alınmaktadır. Jenkins 'i başarıyla kurmak, GitHub 'dan değişiklikleri çekmek, uygulamanızı derlemek ve kümenize dağıtmak için bu genel adımları izleyin:

1. [Önkoşulları](#prerequisites)yüklediğinizden emin olun.
1. Daha sonra Jenkins 'i ayarlamak için şu bölümlerden birindeki adımları uygulayın:
   * [Service Fabric kümesi Içinde Jenkins ayarlama](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Service Fabric kümesi dışında Jenkins 'ı ayarlama](#set-up-jenkins-outside-a-service-fabric-cluster)veya
   * [Service Fabric eklentisini mevcut bir Jenkins ortamına yükler](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Jenkins 'i ayarladıktan sonra, uygulamanızda değişiklik yapıldığında Jenkins 'i tetiklemek için GitHub 'ı ayarlamak ve değişiklikleri GitHub 'dan çekmek üzere derleme adımı aracılığıyla Jenkins iş işlem hattınızı yapılandırmak için [bir Jenkins Işi oluşturma ve yapılandırma](#create-and-configure-a-jenkins-job) bölümündeki adımları izleyin. ve uygulamanızı oluşturun. 
1. Son olarak, uygulamanızı Service Fabric kümenize dağıtmak için Jenkins iş oluşturma sonrası adımını yapılandırın. Jenkins 'i, uygulamanızı bir kümeye dağıtmak üzere yapılandırmanın iki yolu vardır:    
   * Geliştirme ve test ortamları için, [küme yönetimi uç noktası kullanarak dağıtımı Yapılandır](#configure-deployment-using-cluster-management-endpoint)' ı kullanın. Bu, ayarlanacak en basit dağıtım yöntemidir.
   * Üretim ortamları için [Azure kimlik bilgilerini kullanarak dağıtımı Yapılandır](#configure-deployment-using-azure-credentials)' ı kullanın. Microsoft bu yöntemi üretim ortamları için önerir çünkü Azure kimlik bilgileri ile bir Jenkins işinin Azure kaynaklarınıza olan erişimini sınırlayabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

- Git 'in yerel olarak yüklü olduğundan emin olun. Uygun git sürümünü işletim sisteminize göre [Git İndirmeleri sayfasından](https://git-scm.com/downloads) yükleyebilirsiniz. Git 'i yeni başladıysanız [Git belgelerinden](https://git-scm.com/docs)daha fazla bilgi edinin.
- Bu makalede, uygulama oluşturmak ve dağıtmak için GitHub [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) 'da *Service Fabric kullanmaya başlama örneği* kullanılmaktadır. Bu depoyu, ya da yönergelerdeki bazı değişikliklerle izlemek üzere çatalla kendi GitHub projenizi kullanmaktır.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Mevcut bir Jenkins ortamına Service Fabric eklentisi 'ni yükler
Service Fabric eklentisini mevcut bir Jenkins ortamına ekliyorsanız, şunlar gerekir:

- [SERVICE fabrıc CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > CLı 'yi Kullanıcı düzeyinde değil sistem düzeyinde yüklediğinizden emin olun, böylece Jenkins CLı komutlarını çalıştırabilir. 
   >

- Java uygulamalarını dağıtmak için hem Gradle hem de [JDK 8,0 'Yi açın](service-fabric-get-started-linux.md#set-up-java-development). 
- .NET Core 2,0 uygulamaları dağıtmak için [.net core 2,0 SDK 'sını](service-fabric-get-started-linux.md#set-up-net-core-20-development)yüklemelisiniz. 

Ortamınız için gereken önkoşulları yükledikten sonra Jenkins Market 'te Azure Service Fabric eklentisini arayabilir ve yükleyebilirsiniz.

Eklentiyi yükledikten sonra, [bir Jenkins Işi oluşturma ve yapılandırma işini](#create-and-configure-a-jenkins-job)atlayın.


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Jenkins’i bir Service Fabric kümesi içinde ayarlama

Jenkins’i bir Service Fabric kümesinin içinde veya dışında ayarlayabilirsiniz. Aşağıdaki bölümlerde, kapsayıcı örneğinin durumunu kaydetmek için bir Azure depolama hesabı kullanılırken bir kümenin içinde nasıl ayarlanacağı gösterilmektedir.

### <a name="prerequisites"></a>Önkoşullar
- Docker 'ın yüklü olduğu Service Fabric Linux kümesi vardır. Azure 'da çalışan Service Fabric kümelerinde Docker zaten yüklü. Kümeyi yerel olarak çalıştırıyorsanız (Onebox geliştirme ortamı), `docker info` komut ile makinenizde Docker 'ın yüklü olup olmadığını denetleyin. Yüklü değilse, aşağıdaki komutları kullanarak yükleme yapın:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > 8081 bağlantı noktasının kümede özel bir uç nokta olarak belirtildiğinden emin olun. Yerel bir küme kullanıyorsanız, bağlantı noktası 8081 ' nin konak makinede açık olduğundan ve genel kullanıma açık bir IP adresine sahip olduğundan emin olun.
   >

### <a name="steps"></a>Adımlar
1. Aşağıdaki komutları kullanarak uygulamayı kopyalayın:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Jenkins kapsayıcısının durumunu bir dosya paylaşımında kalıcı hale getirin:
   1. Kümenizle **aynı bölgede** , gibi bir adla `sfjenkinsstorage1`Azure depolama hesabı oluşturun.
   1. Depolama hesabı altında gibi `sfjenkins`bir ada sahip bir **dosya paylaşma** oluşturun.
   1. Dosya paylaşma için **Bağlan** ' a tıklayın ve **Linux 'tan bağlanma**altında görüntülediği değerleri aklınızda, değerin aşağıdaki gibi görünmesi gerekir:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > CIFS paylaşımlarını bağlamak için, küme düğümlerinde CIFS-utils paketinin yüklü olması gerekir.      
   >

1. `setupentrypoint.sh` Betikteki yer tutucu değerlerini adım 2 ' deki Azure depolama ayrıntıları ile güncelleştirin.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Yukarıdaki `[REMOTE_FILE_SHARE_LOCATION]` 2. adımdaki `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` bağlantı çıktısından alınan değerle değiştirin.
   * Yukarıdaki `[FILE_SHARE_CONNECT_OPTIONS_STRING]` 2. adımdaki `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` değerle değiştirin.

1. **Yalnızca güvenli küme:** 
   
   Jenkins 'den güvenli bir kümedeki uygulamaların dağıtımını yapılandırmak için, Jenkins kapsayıcısının içinden küme sertifikasına erişilebilir olması gerekir. *ApplicationManifest. xml* dosyasında, **containerhostpolicies** etiketinin altında bu sertifika başvurusunu ekleyin ve parmak izi değerini küme sertifikası ile güncelleştirin.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ayrıca, ApplicationManifest *. xml* dosyasındaki **ApplicationManifest** (root) etiketinin altına aşağıdaki satırları ekleyin ve parmak izi değerini küme sertifikası ile güncelleştirin.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Kümeye bağlanın ve kapsayıcı uygulamasını yükler.

   **Güvenli küme**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Yukarıdaki komut, sertifikayı pek biçiminde alır. Sertifikanız PFX biçimindeyse, dönüştürmek için aşağıdaki komutu kullanabilirsiniz. PFX dosyanız parola korumalı değilse, **passin** parametresini olarak `-passin pass:`belirtin.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Güvenli olmayan küme**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Bu işlem, kümeye bir Jenkins kapsayıcısı yükler ve küme, Service Fabric Explorer kullanılarak izlenebilir.

   > [!NOTE]
   > Jenkins görüntüsünün kümeye indirilmesi birkaç dakika sürebilir.
   >

1. Tarayıcınızdan `http://PublicIPorFQDN:8081` sayfasına gidin. Bu işlem, oturum açmak için gereken ilk yönetici parolasının yolunu sağlar. 
1. Jenkins kapsayıcısının hangi düğümde çalıştığını öğrenmek için Service Fabric Explorer bakın. Secure Shell (SSH) Bu düğümde oturum açın.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. `docker ps -a` kullanarak kapsayıcı örneğinin kimliğini alın.
1. Secure Shell (SSH) kapsayıcıda oturum açın ve Jenkins portalında gösterilen yolu yapıştırın. Örneğin, portalda yolu `PATH_TO_INITIAL_ADMIN_PASSWORD`gösteriyorsa, aşağıdaki komutları çalıştırın:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Jenkins Başlarken sayfasında, yüklenecek eklentileri seçin seçeneğini belirleyin, **yok** onay kutusunu seçin ve sonra da yüklensin ' i tıklatın.
1. Bir kullanıcı oluşturun veya yönetici olarak devam etmek için seçin.

Jenkins 'i ayarladıktan sonra, [bir Jenkins Işi oluşturma ve yapılandırma işini](#create-and-configure-a-jenkins-job)atlayın.  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Jenkins’i Service Fabric kümesi dışında ayarlama

Jenkins’i bir Service Fabric kümesinin içinde veya dışında ayarlayabilirsiniz. Aşağıdaki bölümlerde, kümenin dışında nasıl ayarlandığı gösterilmektedir.

### <a name="prerequisites"></a>Önkoşullar
- Makinenizde Docker 'ın yüklü olduğundan emin olun. Terminalden Docker yüklemek için aşağıdaki komutlar kullanılabilir:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Terminalde çalıştırdığınızda `docker info` , çıktıda Docker hizmetinin çalıştığını göstermesi gerekir.

### <a name="steps"></a>Adımlar
1. Service Fabric Jenkins kapsayıcı görüntüsünü çekin: `docker pull rapatchi/jenkins:latest`. Bu görüntü, Service Fabric Jenkins eklentisi önceden yüklenmiş şekilde gelir.
1. Kapsayıcı görüntüsünü çalıştırın:`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Kapsayıcı görüntüsü örneğinin kimliğini alın. `docker ps –a` komutuyla tüm Docker kapsayıcılarını listeleyebilirsiniz
1. Jenkins portalında aşağıdaki adımlarla oturum açın:

   1. Ana bilgisayarınızdan Jenkins kabuğu 'nda oturum açın. Kapsayıcı KIMLIĞININ ilk dört basamağını kullanın. Örneğin, kapsayıcı kimliği ise `2d24a73b5964`kullanın. `2d24`

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Jenkins kabuğundan kapsayıcı örneğiniz için yönetici parolasını alın:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Jenkins panosunda oturum açmak için aşağıdaki URL 'YI bir Web tarayıcısında açın: `http://<HOST-IP>:8080`. Jenkins 'in kilidini açmak için önceki adımdaki parolayı kullanın.
   1. (İsteğe bağlı.) İlk kez oturum açtıktan sonra, kendi kullanıcı hesabınızı oluşturabilir ve bu adımları aşağıdaki adımlarda kullanabilir veya yönetici hesabını kullanmaya devam edebilirsiniz. Bir kullanıcı oluşturursanız, bu kullanıcıyla devam etmeniz gerekir.
1. [Yeni BIR SSH anahtarı oluşturma ve SSH aracısına ekleme](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)adımlarını kullanarak, GitHub 'ı Jenkins ile çalışacak şekilde ayarlayın.
   * GitHub’dan sağlanan yönergeleri kullanarak SSH anahtarını oluşturun ve depoyu barındıran GitHub hesabına SSH anahtarını ekleyin.
   * Önceki bağlantıda belirtilen komutları Jenkins Docker kabuğunda (ana bilgisayarınızda değil) çalıştırın.
   * Ana bilgisayarınızdan Jenkins kabuğunda oturum açmak için aşağıdaki komutu kullanın:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Jenkins kapsayıcı görüntüsünün barındırıldığı kümenin veya makinenin, herkese açık bir IP adresi olduğundan emin olun. Bunun olması, Jenkins örneğinin GitHub'dan bildirimler almasını sağlar.

Jenkins 'i ayarladıktan sonra, bir sonraki bölüme geçin, [bir Jenkins Işi oluşturun ve yapılandırın](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Bir Jenkins işi oluşturma ve yapılandırma

Bu bölümdeki adımlarda, bir Jenkins işinin bir GitHub deposunda değişikliklere yanıt olarak nasıl yapılandırılacağı, değişiklikleri nasıl getirileceği ve oluşturulacağı gösterilmektedir. Bu bölümün sonunda, uygulamayı bir geliştirme/test ortamına veya bir üretim ortamına dağıtmaktan bağımsız olarak uygulamanızı dağıtmaya yönelik son adımlara yönlendirilirsiniz. 

1. Jenkins panosunda **Yeni öğe**' ye tıklayın.
1. Bir öğe adını girin (örneğin, **MyJob**). **Serbest stil proje**’yi seçip **Tamam**’a tıklayın.
1. Iş yapılandırması sayfası açılır. (Jenkins panosundan yapılandırmaya ulaşmak için işe tıklayın ve ardından **Yapılandır**' a tıklayın).

1. **Genel** sekmesinde, **GitHub projesi**kutusunu IŞARETLEYIN ve GitHub proje URL 'nizi belirtin. Bu URL, Jenkins sürekli tümleştirme, sürekli dağıtım (CI/CD) akışı ile tümleştirmek istediğiniz Service Fabric Java uygulamasını barındırır (örneğin, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. **Kaynak kodu yönetimi** sekmesinde **Git**' i seçin. Jenkins CI/CD akışıyla tümleştirmek istediğiniz Service Fabric Java uygulamasını barındıran deponun URL'sini belirtin (örneğin, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Ayrıca, hangi dalın derlemenize de belirtebilirsiniz (örneğin, `/master`).
1. *GitHub* deponuzu Jenkins ile konuşmak için yapılandırın:

   a. GitHub deposu sayfanızda **Ayarlar** > **tümleştirmeler ve hizmetler**' e gidin.

   b. **Hizmet Ekle**’yi seçin, **Jenkins** yazın ve **Jenkins-GitHub eklentisi**’ni seçin.

   c. Jenkins web kancası URL'nizi girin (varsayılan olarak, `http://<PublicIPorFQDN>:8081/github-webhook/` olmalıdır). **Hizmet ekle/güncelleştir** öğesine tıklayın.

   d. Jenkins örneğinize bir test olayı gönderilir. GitHub’da web kancasının yanında yeşil renkli bir onay işareti görürsünüz ve projeniz derlenir.

1. Jenkins 'teki **Derleme tetikleyicileri** sekmesinde istediğiniz derleme seçeneğini belirleyin. Bu örnekte, depoya gönderim yapıldığında bir derlemeyi tetiklemeniz, bu nedenle **gıtscm yoklaması yoklaması Için GitHub kanca tetikleyicisi**' ni seçin. (Daha önce bu seçenek **GitHub’a bir değişiklik uygulandığında derle** olarak adlandırılıyordu.)
1. **Derleme** sekmesinde, Java uygulaması veya .NET Core uygulaması oluşturma yönteminize bağlı olarak aşağıdakilerden birini yapın:

   * **Java uygulamaları için:** **Derleme adımı ekle** açılır listesinden **Gradle betiğini çağır**' ı seçin. **Gelişmiş**'e tıklayın. Gelişmiş menüsünde, uygulamanız için **kök derleme betiğinin** yolunu belirtin. Belirtilen yoldan build.gradle’ı alır ve buna göre çalışır. [Actorcounter uygulaması](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)için şu şekilde olur: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric Jenkins Derleme eylemi][build-step]

   * **.NET Core uygulamaları için:** **Derleme adımı ekle** açılır listesinden **kabuğu Çalıştır**' ı seçin. Görüntülenen komut kutusunda, önce dizinin build.sh dosyasının bulunduğu yola değiştirilmesi gerekir. Dizin değiştirildikten sonra, build.sh betiği çalıştırılabilir ve uygulamayı derler.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Aşağıdaki ekran görüntüsünde, bir Jenkins iş adı CounterServiceApplication ile [sayaç hizmeti](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) örneğini oluşturmak için kullanılan komutların bir örneği gösterilmektedir.

      ![Service Fabric Jenkins Derleme eylemi][build-step-dotnet]

1. Jenkins 'i derleme sonrası eylemlerindeki bir Service Fabric kümesine dağıtmak üzere yapılandırmak için, bu kümenin sertifikasının Jenkins kapsayıcısında konumunun olması gerekir. Jenkins kapsayıcının kümenizin içinde veya dışında çalışıp çalışmadığını ve küme sertifikasının konumunu aklınızda olduğunuza bağlı olarak aşağıdakilerden birini seçin:

   * **Kümenizin içinde çalışan Jenkins için:** Sertifikanın yolu, *Certificates_JenkinsOnSF_Code_MyCert_PEM* ortam değişkeninin değeri kapsayıcının içinden yankılandırarak bulunabilir.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Kümenizin dışında çalışan Jenkins için:** Küme sertifikasını kapsayıcınıza kopyalamak için aşağıdaki adımları izleyin:
     1. Sertifikanızın pek biçiminde olması gerekir. PEA dosyanız yoksa, sertifika PFX dosyasından bir tane oluşturabilirsiniz. PFX dosyanız parola korumalı değilse, konaktan aşağıdaki komutu çalıştırın:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        PFX dosyası parola korumalıysa, parolayı `-passin` parametresine ekleyin. Örneğin:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Jenkins Kapsayıcınızın kapsayıcı kimliğini almak için konaktan çalıştırın `docker ps` .
     1. Aşağıdaki Docker komutuyla PEA dosyasını kapsayıcınıza kopyalayın:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Neredeyse tamamladınız! Jenkins işini açık tutun. Geri kalan tek görev, uygulamanızı Service Fabric kümenize dağıtmak için oluşturma sonrası adımları yapılandırmaktır:

* Bir geliştirme veya test ortamına dağıtmak için, [küme yönetimi uç noktası kullanarak dağıtımı yapılandırma](#configure-deployment-using-cluster-management-endpoint)bölümündeki adımları izleyin.
* Bir üretim ortamına dağıtmak için [Azure kimlik bilgilerini kullanarak dağıtımı yapılandırma](#configure-deployment-using-azure-credentials)bölümündeki adımları izleyin.

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Küme yönetim uç noktasını kullanarak dağıtımı yapılandırma
Geliştirme ve test ortamları için, uygulamanızı dağıtmak üzere küme yönetim uç noktasını kullanabilirsiniz. Uygulamanızı dağıtmak için derleme sonrası eyleminin küme yönetimi uç noktasıyla yapılandırılması, en az sayıda kurulum gerektirir. Bir üretim ortamına dağıtım yapıyorsanız, dağıtım sırasında kullanmak üzere bir Azure Active Directory hizmet sorumlusu yapılandırmak için [Azure kimlik bilgilerini kullanarak dağıtımı yapılandırma](#configure-deployment-using-azure-credentials) bölümüne atlayın.    

1. Jenkins işinde, **oluşturma sonrası eylemler** sekmesine tıklayın. 
1. **Derleme Sonrası Eylemler** açılır listesinden **Service Fabric Projesini Dağıt**’ı seçin. 
1. **Service Fabric küme yapılandırması**altında **Service Fabric yönetim uç noktası** radyo düğmesini seçin.
1. **Yönetim ana bilgisayarı**için kümeniz için bağlantı uç noktası girin; Örneğin `{your-cluster}.eastus.cloudapp.azure.com`.
1. **Istemci anahtarı** ve **Istemci sertifikası**için Jenkins KAPSAYıCıNıZDA ped dosyasının konumunu girin; Örneğin `/var/jenkins_home/clustercert.pem`. (Sertifikanın konumunu, [bir Jenkins Işi oluşturma ve yapılandırma işinin](#create-and-configure-a-jenkins-job)son adımını kopyaladınız.)
1. Uygulama **yapılandırması**altında uygulama **adı**, **uygulama türü**ve (göreli) **yolunu uygulama bildirim** alanları ' na yapılandırın.

   ![Jenkins Service Fabric oluşturma sonrası eylem yönetim uç noktasını yapılandırma](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. **Yapılandırmayı Doğrula**' ya tıklayın. Doğrulama başarıyla tamamlandıktan sonra **Kaydet**' e tıklayın. Jenkins iş ardışık düzeni artık tam olarak yapılandırılmıştır. Dağıtımınızı test etmek için [sonraki adımlara](#next-steps) atlayın.

## <a name="configure-deployment-using-azure-credentials"></a>Azure kimlik bilgilerini kullanarak dağıtımı yapılandırma
Üretim ortamları için, uygulamanızı dağıtmak üzere bir Azure kimlik bilgisinin yapılandırılması önemle önerilir. Bu bölümde, oluşturma sonrası eyleminde uygulamanızı dağıtmak için kullanmak üzere bir Azure Active Directory hizmet sorumlusunu yapılandırma işlemi gösterilmektedir. Jenkins işinin izinlerini sınırlamak için dizininizdeki rollere hizmet sorumluları atayabilirsiniz. 

Geliştirme ve test ortamları için uygulamanızı dağıtmak üzere Azure kimlik bilgilerini veya küme yönetim uç noktasını yapılandırabilirsiniz. Küme yönetimi uç noktasının nasıl yapılandırılacağı hakkında ayrıntılı bilgi için bkz. [küme yönetim uç noktası kullanarak dağıtımı yapılandırma](#configure-deployment-using-cluster-management-endpoint).   

1. Azure aboneliğinize bir Azure Active Directory hizmet sorumlusu oluşturmak ve bu izinleri atamak için, [bir Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)bölümündeki adımları izleyin. Aşağıdakilere dikkat edin:

   * Konusundaki adımları takip ederken, aşağıdaki değerleri kopyalamaya ve kaydettiğinizden emin olun: *Uygulama kimliği*, *uygulama anahtarı*, *dizin kimliği (KIRACı kimliği)* ve *abonelik kimliği*. Jenkins 'te Azure kimlik bilgilerini yapılandırmak için bunlara ihtiyacınız vardır.
   * Dizininiz için [gerekli izinlere](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) sahip değilseniz, bir yöneticiden size izinleri vermesini veya hizmet sorumlusunu sizin için oluşturmasını isteyin veya şu noktada kümeniz için yönetim uç noktasını yapılandırmanız gerekir. Jenkins 'te işiniz Için oluşturma sonrası eylemler.
   * [Azure Active Directory uygulama oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) bölümünde, **oturum açma URL**'si IÇIN iyi biçimlendirilmiş bir URL girebilirsiniz.
   * [Uygulamayı bir role ata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) bölümünde, uygulamanızı kümeniz için kaynak grubunda *okuyucu* rolü atayabilirsiniz.

1. Jenkins işine geri döndüğünüzde **Derleme sonrası eylemler** sekmesine tıklayın.
1. **Derleme Sonrası Eylemler** açılır listesinden **Service Fabric Projesini Dağıt**’ı seçin. 
1. **Küme yapılandırması Service Fabric**altında **Service Fabric kümesini seçin** radyo düğmesini seçin. **Azure kimlik bilgileri**' nin yanına **Ekle** ' ye tıklayın. Jenkins kimlik bilgileri sağlayıcısını seçmek için **Jenkins** ' e tıklayın.
1. Jenkins kimlik bilgileri sağlayıcısında, **tür** açılır listesinden **Microsoft Azure hizmet sorumlusu** ' nı seçin.
1. Aşağıdaki alanları ayarlamak için adım 1 ' de hizmet sorumlunuzu ayarlarken kaydettiğiniz değerleri kullanın:

   * **ISTEMCI kimliği**: *Uygulama Kimliği*
   * **Istemci parolası**: *Uygulama anahtarı*
   * **KIRACı kimliği**: *Dizin KIMLIĞI*
   * **ABONELIK kimliği**: *Abonelik KIMLIĞI*
1. Jenkins 'teki kimlik bilgisini ve kısa bir **açıklamayı**seçmek için kullandığınız açıklayıcı bir **kimlik** girin. Ardından **hizmet sorumlusunu Doğrula ' ya**tıklayın. Doğrulama başarılı olursa **Ekle**' ye tıklayın.

   ![Jenkins Service Fabric Azure kimlik bilgilerini girin](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. **Service Fabric küme yapılandırması**altına döndüğünüzde, **Azure kimlik bilgileri**için yeni kimlik bilgilerinizin seçildiğinden emin olun. 
1. **Kaynak grubu** açılır listesinden, uygulamayı dağıtmak istediğiniz kümenin kaynak grubunu seçin.
1. **Service Fabric** açılır listesinden, uygulamayı dağıtmak istediğiniz kümeyi seçin.
1. **Istemci anahtarı** ve **Istemci sertifikası**için JENKINS kapsayıcınıza ped dosyasının konumunu girin. Örneğin, `/var/jenkins_home/clustercert.pem`. 
1. Uygulama **yapılandırması**altında uygulama **adı**, **uygulama türü**ve (göreli) **yolunu uygulama bildirim** alanları ' na yapılandırın.
    ![Jenkins Service Fabric oluşturma sonrası eylem Azure kimlik bilgilerini yapılandırma](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. **Yapılandırmayı Doğrula**' ya tıklayın. Doğrulama başarıyla tamamlandıktan sonra **Kaydet**' e tıklayın. Jenkins iş ardışık düzeni artık tam olarak yapılandırılmıştır. Dağıtımınızı test etmek için [sonraki adımlarla](#next-steps) devam edin.

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins eklentisiyle ilgili sorunları giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
GitHub ve Jenkins yapılandırılmıştır. Depo çatalınızda `reliable-services-actor-sample/Actors/ActorCounter` projede bazı örnek değişiklikler yapmayı düşünün. https://github.com/Azure-Samples/service-fabric-java-getting-started Değişikliklerinizi uzak `master` dala (veya birlikte çalışmak üzere yapılandırdığınız herhangi bir dala) gönderin. Bunun yapılması, yapılandırmış olduğunuz `MyJob` Jenkins işini tetikler. GitHub 'dan değişiklikleri getirir, bunları oluşturur ve uygulamayı derleme sonrası eylemlerinde belirttiğiniz kümeye dağıtır.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

