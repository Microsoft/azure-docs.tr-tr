---
title: Linux 'ta Apache Tomcat sunucusu için Azure Service Fabric kapsayıcısı oluşturma | Microsoft Docs
description: Azure Service Fabric 'de Apache Tomcat sunucusunda çalışan bir uygulamayı açığa çıkarmak için Linux kapsayıcısı oluşturun. Uygulamanızla ve Apache Tomcat sunucusuyla bir Docker görüntüsü oluşturun, görüntüyü bir kapsayıcı kayıt defterine gönderin, Service Fabric kapsayıcı uygulaması oluşturun ve dağıtın.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 7e14a027f17c15c83a4ce25a211ef6106f2d2eaa
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170596"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Linux 'ta Apache Tomcat Server çalıştıran Service Fabric kapsayıcısı oluşturma
Apache Tomcat, Java Servlet ve Java sunucu teknolojilerinin popüler, açık kaynaklı bir uygulamasıdır. Bu makalede Apache Tomcat ve basit bir Web uygulamasıyla kapsayıcı oluşturma, kapsayıcıyı Linux çalıştıran bir Service Fabric kümesine dağıtma ve Web uygulamasına bağlanma işlemlerinin nasıl yapılacağı gösterilmektedir.  

Apache Tomcat hakkında daha fazla bilgi edinmek için bkz. [Apache Tomcat giriş sayfası](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Önkoşullar
* Çalıştıran bir geliştirme bilgisayarı:
  * [SDK ve araçlar Service Fabric](service-fabric-get-started-linux.md).
  * [Linux Için Docker CE](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLı](service-fabric-cli.md)

* Azure Container Registry bir kapsayıcı kayıt defteri. Azure aboneliğinizde [Azure Portal](../container-registry/container-registry-get-started-portal.md) veya [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry)kullanarak bir kapsayıcı kayıt defteri oluşturabilirsiniz. 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tomcat görüntüsü oluşturun ve yerel olarak çalıştırın
Apache Tomcat görüntüsünü ve basit bir Web uygulamasını temel alan bir Docker görüntüsü oluşturmak ve ardından bunu yerel sisteminizdeki bir kapsayıcıda çalıştırmak için bu bölümdeki adımları izleyin. 
 
1. Geliştirme bilgisayarınızda [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) örnekleri deposuna Başlarken Service Fabric klonlayın.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Dizinleri Apache Tomcat sunucusu örnek dizinine değiştirme (*Service-Fabric-Java-alma-başlatma/kapsayıcı-Apache-Tomcat-Web-Server-Sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Docker Hub ve Tomcat sunucu örneğinde bulunan resmi [Tomcat görüntüsünü](https://hub.docker.com/_/tomcat/) temel alan bir Docker dosyası oluşturun. *Service-Fabric-Java-alma-başlatma/kapsayıcı-Apache-Tomcat-Web-Server-Sample* dizininde, *dockerfile* adlı bir dosya oluşturun (dosya uzantısı olmadan). Aşağıdaki *Dockerfile dosyasına* ekleyin ve değişikliklerinizi kaydedin:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Daha fazla bilgi için [Dockerfile başvurusuna](https://docs.docker.com/engine/reference/builder/) bakın.


4. Web uygulamanızı çalıştıran görüntüyü oluşturmak için `docker build` komutunu çalıştırın:

   ```bash
   docker build . -t tomcattest
   ```

   Bu komut Dockerfile içindeki yönergeleri kullanarak yeni görüntüyü oluşturur (-t etiketleme) `tomcattest`. Bir kapsayıcı görüntüsü oluşturmak için, temel görüntü ilk olarak Docker Hub 'ından indirilir ve uygulamaya eklenir. 

   Oluşturma komutu tamamlandığında, yeni görüntü hakkındaki bilgileri görmek için `docker images` komutunu çalıştırın:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Kapsayıcı kayıt defterine göndermeden önce Kapsayıcılı uygulamanızın yerel olarak çalıştığını doğrulayın:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` kapsayıcıyı adlandırır, bu sayede KIMLIĞI yerine kolay bir ad kullanarak buna başvurabilirsiniz.
   * `-p` kapsayıcı ile ana bilgisayar işletim sistemi arasındaki bağlantı noktası eşlemeyi belirtir. 

   > [!Note]
   > @No__t-0 parametresiyle açtığınız bağlantı noktası, Tomcat uygulamanızın istekleri dinlediği bağlantı noktası olmalıdır. Geçerli örnekte, HTTP istekleri için 8080 numaralı bağlantı noktasında dinlemek üzere *ApacheTomcat/conf/Server. xml* dosyasında yapılandırılmış bir bağlayıcı vardır. Bu bağlantı noktası konaktaki 8080 numaralı bağlantı noktasına eşlenir. 

   Diğer parametreler hakkında daha fazla bilgi için bkz. [Docker Run belgeleri](https://docs.docker.com/engine/reference/commandline/run/).

1. Kapsayıcınızı test etmek için bir tarayıcı açın ve aşağıdaki URL 'Lerden birini girin. "Merhaba Dünya!" çeşidinin bir türevini görürsünüz her URL için hoş geldiniz ekranı.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Merhaba Dünya/sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Kapsayıcıyı durdurup geliştirme bilgisayarınızdan silin:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Tomcat görüntüsünü kapsayıcı Kayıt defterinize gönderme
Tomcat görüntüsünün geliştirme bilgisayarınızdaki bir kapsayıcıda çalıştığını doğruladığınıza göre, kapsayıcıyı kapsayıcı kayıt defterindeki bir depoya gönderin. Bu makale, görüntüyü depolamak için Azure Container Registry kullanır, ancak bazı adımları değişikliklerle seçtiğiniz herhangi bir kapsayıcı kayıt defterini kullanabilirsiniz. Bu makalede, kayıt defteri adının *myregistry* olduğu varsayılır ve tam kayıt defteri adı myregistry.azurecr.io olur. Bunları senaryonuz için uygun şekilde değiştirin. 

1. [Kayıt defteri kimlik bilgilerinizle](../container-registry/container-registry-authentication.md), kapsayıcı kayıt defterinizde oturum açmak için `docker login` çalıştırın.

   Aşağıdaki örnek Azure Active Directory [hizmet SORUMLUSUNUN](../active-directory/develop/app-objects-and-service-principals.md)kimliğini ve parolasını geçirir. Örneğin, bir Otomasyon senaryosunda Kayıt defterinize bir hizmet sorumlusu atamış olabilirsiniz. Ya da kayıt defteri Kullanıcı adınızı ve parolanızı kullanarak oturum açmanız gerekir.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Aşağıdaki komut, kayıt defteriniz için tam yolu olan görüntünün bir etiketini veya diğer adını oluşturur. Bu örnek, kayıt defterinin kökünde dağınıklığı önlemek için görüntüyü `samples` ad alanına koyar.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Görüntüyü kapsayıcı Kayıt defterinize gönderin:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Service Fabric kapsayıcı uygulaması oluşturma ve dağıtma
Tomcat görüntüsünü bir kapsayıcı kayıt defterine itilmiş olduğunuza göre, Kayıt defterinizden Tomcat görüntüsünü çeken Service Fabric bir kapsayıcı uygulaması oluşturup dağıtabilir ve bunu kümenizde kapsayıcılı bir hizmet olarak çalıştırabilirsiniz. 

1. Yerel kopya dışında yeni bir dizin oluşturun ( *Service-Fabric-Java-Başlarken* dizin ağacının dışında). Bu anahtara geçin ve bir kapsayıcı uygulaması için bir yapı iskelesi oluşturmak için Yeumman kullanın: 

   ```bash
   yo azuresfcontainer 
   ```
   İstendiğinde aşağıdaki değerleri girin:

   * Uygulamanızı adlandırın: ServiceFabricTomcat
   * Uygulama hizmetinin adı: TomcatService
   * Görüntü adını girin: kapsayıcı kayıt defterinizde kapsayıcı görüntüsünün URL 'sini sağlayın; Örneğin, myregistry.azurecr.io/samples/tomcattest.
   * Komutlar: bu alanı boş bırakın. Bu görüntüde bir iş yükü giriş noktası tanımlanmış olduğundan, giriş komutlarının açıkça belirtilmesi gerekmez (bir kapsayıcı içinde çalıştırılan ve bu, kapsayıcıyı başlangıçtan sonra çalışır durumda tutan komutlar).
   * Konuk kapsayıcı uygulaması örneklerinin sayısı: 1

   ![Kapsayıcılar için Yeumman Oluşturucu Service Fabric](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. Hizmet bildiriminde (*Servicefabrictomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest. xml*), uygulamanızın istekleri dinlediği bağlantı noktasını açmak Için kök **Servicemanfest** etiketinin altına aşağıdaki XML 'i ekleyin. **Endpoint** etiketi, uç nokta için protokolü ve bağlantı noktasını bildirir. Bu makalede Kapsayıcılı hizmet 8080 numaralı bağlantı noktasını dinler: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Uygulama bildiriminde (*Servicefabrictomcat/servicefabrictomcat/ApplicationManifest. xml*), **servicemanifestımport** etiketinin altına aşağıdaki XML 'i ekleyin. İmza kayıt defterinizin adı ve parola ile oturum açmak için gereken parolayı içeren **AccountName** ve **parolayı** değiştirin.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   **Containerhostpolicies** etiketi, kapsayıcı konaklarını etkinleştirmek için ilkeleri belirler.
    
   * **Portbinding** etiketi, kapsayıcı bağlantı noktası ile ana bilgisayar bağlantı noktası eşleme ilkesini yapılandırır. Kapsayıcı, Dockerfile içinde belirtildiği gibi 8080 bağlantı noktasını kullanıma sunduğundan **containerport** özniteliği 8080 olarak ayarlanır. **Endpointref** özniteliği, önceki adımda hizmet bildiriminde tanımlanan uç nokta olan "EndpointTest" olarak ayarlanır. Bu nedenle, 8080 numaralı bağlantı noktasında hizmete gelen istekler, kapsayıcıda 8080 numaralı bağlantı noktasına eşlenir. 
   * **Depo\kimlik bilgileri** etiketi, kapsayıcının görüntünün aldığı (özel) depoyla kimliğini doğrulamak için gereken kimlik bilgilerini belirtir. Görüntü ortak bir depodan çekilmeniz durumunda bu ilkeye ihtiyacınız yoktur.
    

12. *Servicefabrictomcat* klasöründe, Service Fabric kümenize bağlanın. 

   * Yerel Service Fabric kümesine bağlanmak için şunu çalıştırın:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Güvenli bir Azure kümesine bağlanmak için, istemci sertifikasının *Servicefabrictomcat* dizininde. pek dosyası olarak bulunduğundan emin olun ve şunu çalıştırın: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Önceki komutta `your-certificate.pem` değerini istemci sertifikası dosyanızın adıyla değiştirin. Geliştirme ve test ortamlarında, küme sertifikası genellikle istemci sertifikası olarak kullanılır. Sertifikanız kendinden imzalı değilse, `-no-verify` parametresini atlayın. 
       
     Küme sertifikaları genellikle. pfx dosyaları olarak yerel olarak indirilir. Henüz bir pek biçiminde sertifikanız yoksa, bir. pfx dosyasından bir. ped dosyası oluşturmak için aşağıdaki komutu çalıştırabilirsiniz:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     . Pfx dosyanız parola korumalı değilse, son parametre için `-passin pass:` kullanın.


13. Uygulamayı kümenize dağıtmak için şablonda sağlanmış olan Install betiğini çalıştırın. Betik, uygulama paketini kümenin görüntü deposuna kopyalar, uygulama türünü kaydeder ve uygulamanın bir örneğini oluşturur.

     ```bash
     ./install.sh
     ```

   Install betiğini çalıştırdıktan sonra bir tarayıcı açın ve Service Fabric Explorer şuraya gidin:
    
   * Yerel bir kümede `http://localhost:19080/Explorer` ' ı kullanın (Mac OS X vagrant kullanılıyorsa, *localhost* 'u VM 'nin özel IP 'si ile değiştirin).
   * Güvenli bir Azure kümesinde `https://PublicIPorFQDN:19080/Explorer` ' ı kullanın. 
    
   **Uygulamalar** düğümünü genişletin ve artık uygulama türü, **Servicefabrictomcattype**ve bu türün ilk örneği için başka bir giriş olduğunu unutmayın. Uygulamanın tam olarak dağıtılması birkaç dakika sürebilir, bu nedenle hasta olmalıdır.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Tomcat sunucusundaki uygulamaya erişmek için bir tarayıcı penceresi açın ve aşağıdaki URL 'Lerden birini girin. Yerel kümeye dağıttıysanız, *Publicıporfqdn*için *localhost* kullanın. "Merhaba Dünya!" çeşidinin bir türevini görürsünüz her URL için hoş geldiniz ekranı.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Temizle
Kümeden uygulama örneğini silmek ve uygulama türünün kaydını silmek için şablonda belirtilen kaldırma betiğini kullanın.

```bash
./uninstall.sh
```

Görüntüyü kapsayıcı kayıt defterine gönderdikten sonra, yerel görüntüyü geliştirme bilgisayarınızdan silebilirsiniz:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Sonraki adımlar
* Ek Linux kapsayıcı özellikleri hakkında hızlı adımlar için [Linux üzerinde ilk Service Fabric kapsayıcı uygulamanızı oluşturun](service-fabric-get-started-containers-linux.md).
* Linux kapsayıcılarıyla ilgili daha ayrıntılı adımlar için, [Linux kapsayıcı uygulaması oluşturma öğreticisi](service-fabric-tutorial-create-container-images.md) öğreticisini okuyun.
* [Service Fabric kapsayıcı](service-fabric-containers-overview.md)çalıştırma hakkında daha fazla bilgi edinin.


