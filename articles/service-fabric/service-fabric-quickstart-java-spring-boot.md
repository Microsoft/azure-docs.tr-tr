---
title: 'Hızlı başlangıç: Azure Service Fabric Spring Boot uygulaması oluşturma'
description: Bu hızlı başlangıçta, bir Spring Boot örnek uygulaması kullanarak Azure Service Fabric için Spring Boot uygulaması dağıtırsınız.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: ad01697349eab20aa55fc6f6b59bc0d317c5a7e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530851"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Hızlı başlangıç: Azure 'da bir Java Spring Boot uygulaması dağıtma Service Fabric

Bu hızlı başlangıçta, Linux veya MacOS 'ta tanıdık komut satırı araçlarını kullanarak Azure Service Fabric bir Java Spring Boot uygulaması dağıtırsınız. Azure Service Fabric; mikro hizmetleri ve kapsayıcıları dağıtmayı ve yönetmeyi sağlayan bir dağıtılmış sistemler platformudur. 

## <a name="prerequisites"></a>Önkoşullar

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java ortamı](./service-fabric-get-started-linux.md#set-up-java-development) ve [yeumman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK & Service Fabric komut satırı arabirimi (CLı)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java ortamı ve Yeumman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK & Service Fabric komut satırı arabirimi (CLı)](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Örneği indirme

Bir Terminal penceresinde, Spring Boot [kullanmaya başlama](https://github.com/spring-guides/gs-spring-boot) örnek uygulamasını yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Spring Boot uygulamasını derleme 
*G/yay-önyükleme/tam* dizin içinde, uygulamayı derlemek için aşağıdaki komutu çalıştırın 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Spring Boot uygulamasını paketleme 
1. Klondaki *GS-Spring-Boot* dizininde `yo azuresfguest` komutunu çalıştırın. 

1. Her istem için aşağıdaki ayrıntıları girin.

    ![Spring Boot Yeumman girdileri](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. *Springservicefabric/SpringServiceFabric/SpringGettingStartedPkg/Code* klasöründe, *EntryPoint.sh* adlı bir dosya oluşturun. Aşağıdaki kodu *EntryPoint.sh* dosyasına ekleyin. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. *GS-Spring-Boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml* dosyasına **uç noktalar** kaynağı ekleme

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    *ServiceManifest.xml* şimdi şu şekilde görünür: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Bu aşamada, Spring Boot Kullanmaya Başlama örneği için Service Fabric’e dağıtabileceğiniz bir Service Fabric uygulaması oluşturdunuz.

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma

1. Aşağıdaki komutu çalıştırarak Ubuntu makinelerinde yerel kümenizi başlatın:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Mac kullanıyorsanız Docker görüntüsünden yerel kümeyi başlatın (burada Mac için yerel kümenizi ayarlama [önkoşullarına](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric) uyduğunuz varsayılır). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Yerel kümenin başlatılması biraz zaman alabilir. Kümenin sorunsuz çalıştığından emin olmak için, `http://localhost:19080` adresinden Service Fabric Explorer’a erişin. Sorunsuz beş düğüm, yerel kümenin çalıştığını belirtir. 
    
    ![Service Fabric Explorer sağlıklı düğümleri gösterir](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. *GS-Spring-Boot/SpringServiceFabric* klasörünü açın.
1. Yerel kümenize bağlanmak için aşağıdaki komutu çalıştırın.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. *İnstall.sh* betiğini çalıştırın.

    ```bash
    ./install.sh
    ```

1. Sık kullandığınız web tarayıcısını açın ve`http://localhost:8080` adresine giderek uygulamaya erişin.

    ![Spring Boot Service Fabric örneği](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Bir Service Fabric kümesine dağıtılmış Spring Boot uygulamasına artık erişebilirsiniz.

Daha fazla bilgi için Spring Web sitesinde Spring Boot [kullanmaya](https://spring.io/guides/gs/spring-boot/) başlama örneğine bakın.

## <a name="scale-applications-and-services-in-a-cluster"></a>Bir kümedeki uygulamaları ve hizmetleri ölçeklendirme

Hizmet yükündeki bir değişikliği karşılamak için kümedeki hizmetler kolayca ölçeklendirilebilir. Kümede çalıştırılan örnek sayısını değiştirerek bir hizmeti ölçeklendirebilirsiniz. Hizmetlerinizi ölçeklendirmenin birçok yolu vardır; örneğin, Service Fabric CLI’den (sfctl) betikler veya komutlar kullanabilirsiniz. Aşağıdaki adımlarda Service Fabric Explorer kullanılmaktadır.

Service Fabric Explorer tüm Service Fabric kümelerinde çalıştırılır ve tarayıcıdan kümelerin HTTP yönetim bağlantı noktasına (19080) göz atılarak (örneğin, `http://localhost:19080`) erişilebilir.

Web ön uç hizmetini ölçeklendirmek için aşağıdakileri yapın:

1. Kümenizde Service Fabric Explorer'ı açın. Örneğin: `http://localhost:19080`.
1. TreeView 'daki **Fabric:/SpringServiceFabric/SpringGettingStarted** düğümünün yanındaki üç nokta (**...**) simgesini seçin ve **hizmeti Ölçeklendir**' i seçin.

    ![Service Fabric Explorer ölçek hizmeti örneği](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Şimdi hizmetin örnek sayısını ölçeklendirebilirsiniz.

1. Numarayı **3** olarak değiştirin ve **hizmeti Ölçeklendir**' i seçin.

    Komut satırını kullanarak hizmeti ölçeklendirmenin alternatif bir yolu aşağıda verilmiştir.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Ağaç görünümünde **Fabric:/SpringServiceFabric/SpringGettingStarted** düğümünü seçin ve bölüm DÜĞÜMÜNÜ (GUID ile gösterilir) genişletin.

    ![Service Fabric Explorer ölçek hizmeti Tamam](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Hizmette üç örnek bulunur ve ağaç görünümünde örneklerin çalıştığı düğümler gösterilir.

Bu basit yönetim görevi sayesinde ön uç hizmetinin kullanıcı yükünü işlemek için kullanabileceği kaynakları iki katına çıkarmış oldunuz. Bir hizmetin güvenilir bir şekilde çalışması için birden fazla örneğe ihtiyaç duymadığınızı anlamanız önemlidir. Bir hizmet başarısız olursa Service Fabric, kümede yeni bir hizmet örneği çalışmasını sağlar.

## <a name="fail-over-services-in-a-cluster"></a>Bir kümedeki hizmetlerin yükünü devretme

Hizmet yük devretmesini göstermek için Service Fabric Explorer'ı kullanarak bir düğümü yeniden başlatma işleminin benzetimi yapılmıştır. Hizmetinizin yalnızca bir örneğinin çalıştığından emin olun.

1. Kümenizde Service Fabric Explorer'ı açın. Örneğin: `http://localhost:19080`.
1. Hizmetinizin örneğini çalıştıran düğümün yanındaki üç nokta (**...**) simgesini seçin ve düğümü yeniden başlatın.

    ![Service Fabric Explorer yeniden başlatma düğümü](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Hizmetinizin örneği bu durumda farklı bir düğüme taşınmıştır ve uygulamanızda kesinti yaşanmaz.

    ![Service Fabric Explorer yeniden başlatma düğümü başarılı](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Service Fabric’e Spring Boot uygulaması dağıtma
* Uygulamayı yerel kümenize dağıtma
* Birden çok düğüm arasında uygulamanın ölçeğini genişletme
* Kullanılabilirliği etkilemeden hizmetinizin yük devretmesini gerçekleştirme

Service Fabric’te Java uygulamalarıyla çalışma hakkında daha fazla bilgi için Java uygulamaları öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Java uygulaması dağıtma](./service-fabric-tutorial-create-java-app.md)
