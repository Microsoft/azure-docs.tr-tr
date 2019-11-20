---
title: 'Hızlı başlangıç: Azure Service Fabric Spring Boot uygulaması oluşturma'
description: Bu hızlı başlangıçta, bir Spring Boot örnek uygulaması kullanarak Azure Service Fabric için bir Spring Boot uygulaması dağıtırsınız.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ec6addb348b8c13f124ec225e056d2003a93c38
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703514"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Hızlı başlangıç: Azure 'da bir Java Spring Boot uygulaması dağıtma Service Fabric

Bu hızlı başlangıçta, bir Java Spring önyükleme uygulamasının Azure Service Fabric 'a nasıl dağıtılacağı gösterilmektedir. Azure Service Fabric, mikro hizmetler ve kapsayıcılar dağıtmak ve yönetmek için dağıtılmış bir sistem platformudur. 

Bu hızlı başlangıç, Spring Web sitesinden [Başlarken](https://spring.io/guides/gs/spring-boot/) örneğini kullanır. Tanıdık komut satırı araçlarını kullanarak, bu hızlı başlangıç, Spring Boot örneğini Service Fabric bir uygulama olarak dağıtma konusunda size yol gösterir. İşiniz bittiğinde, Spring Boot Başlarken örnek Service Fabric çalışır.

![Spring Boot Service Fabric örneği](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Bu hızlı başlangıçta şunları nasıl yapacağınızı öğreneceksiniz:

* Service Fabric için bir Spring Boot uygulaması dağıtma
* Uygulamayı yerel kümenize dağıtma
* Birden çok düğümde uygulamanın ölçeğini genişletme
* Kullanılabilirliğine hiçbir isabet olmadan hizmetinizin yük devretmesini gerçekleştirme

## <a name="prerequisites"></a>Prerequisites

Bu hızlı başlangıcı gerçekleştirmek için:

1. Service Fabric SDK & Service Fabric komut satırı arabirimini (CLı) yükler

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. ['Un](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Git 'i yükler](https://git-scm.com/)
1. Yeumman 'ı yükler

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. ['Un](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Java ortamını ayarlama

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  ['Un](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Örneği indirin

Bir Terminal penceresinde, Spring Boot kullanmaya başlama örnek uygulamasını yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Spring Boot uygulamasını oluşturma 
1. @No__t-0 dizininin içinde, uygulamayı derlemek için aşağıdaki komutu çalıştırın 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Spring Boot uygulamasını paketleme 
1. Kopyanızda `gs-spring-boot` dizininde `yo azuresfguest` komutunu çalıştırın. 

1. Her istem için aşağıdaki ayrıntıları girin.

    ![Spring Boot Yeumman girdileri](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. @No__t-0 klasöründe, `entryPoint.sh` adlı bir dosya oluşturun. @No__t-0 dosyasına aşağıdakini ekleyin. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. *GS-Spring-Boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest. xml* dosyasında **uç noktalar** kaynağını ekleyin

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    **Servicemanifest. xml** şu şekilde görünür: 

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

Bu aşamada, Spring Boot kullanmaya başlama örneği için Service Fabric dağıtabileceğiniz bir Service Fabric uygulaması oluşturdunuz.

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma

1. Aşağıdaki komutu çalıştırarak Ubuntu makinelerinde yerel kümenizi başlatın:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Mac kullanıyorsanız, Docker görüntüsünden yerel kümeyi başlatın (Bu, Mac için yerel kümenizi ayarlamaya yönelik [önkoşulları](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) takip ettiğiniz varsayılırsa). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Yerel kümenin başlatılması biraz zaman alır. Kümenin tam olarak olduğunu doğrulamak için Service Fabric Explorer **http://localhost:19080** ' e erişin. Sağlıklı olan beş düğüm, yerel kümenin çalışır durumda olduğunu gösterir. 
    
    ![Service Fabric Explorer sağlıklı düğümleri gösterir](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. @No__t-0 klasörünü açın.
1. Yerel kümenize bağlanmak için aşağıdaki komutu çalıştırın.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. @No__t-0 betiğini çalıştırın.

    ```bash
    ./install.sh
    ```

1. En sevdiğiniz web tarayıcınızı açın ve `http://localhost:8080` ' a erişerek uygulamaya erişin.

    ![Spring Boot Service Fabric örneği](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Artık bir Service Fabric kümesine dağıtılan Spring Boot uygulamasına erişebilirsiniz.

## <a name="scale-applications-and-services-in-a-cluster"></a>Bir kümedeki uygulamaları ve Hizmetleri ölçeklendirme

Hizmetler, hizmetlerde yükün bir değişikliğe uyum sağlamak için bir küme genelinde ölçeklendirilebilir. Kümede çalışan örneklerin sayısını değiştirerek bir hizmeti ölçeklendirmeniz gerekir. Hizmetlerinizi ölçeklendirmenin birçok yolu vardır. Örneğin, Service Fabric CLı (sfctl) içindeki betikleri veya komutları kullanabilirsiniz. Aşağıdaki adımlar Service Fabric Explorer kullanır.

Service Fabric Explorer, tüm Service Fabric kümelerinde çalışır ve kümenin HTTP yönetim bağlantı noktasına (19080) göz atarak bir tarayıcıdan erişilebilir. Örneğin, `http://localhost:19080`.

Web ön uç hizmetini ölçeklendirmek için aşağıdakileri yapın:

1. Service Fabric Explorer kümenizdeki açın (örneğin, `http://localhost:19080`).
1. TreeView 'daki **Fabric:/SpringServiceFabric/SpringGettingStarted** düğümünün yanındaki üç nokta ( **...** ) simgesini seçin ve **hizmeti Ölçeklendir**' i seçin.

    ![Service Fabric Explorer ölçek hizmeti örneği](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Artık hizmetin örnek sayısını ölçeklendirmeye seçim yapabilirsiniz.

1. Numarayı **3** olarak değiştirin ve **hizmeti Ölçeklendir**' i seçin.

    Komut satırını kullanarak hizmeti ölçeklendirmek için alternatif bir yol aşağıdaki gibidir.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Ağaç görünümünde **Fabric:/SpringServiceFabric/SpringGettingStarted** düğümünü seçin ve bölüm DÜĞÜMÜNÜ (GUID ile gösterilir) genişletin.

    ![Service Fabric Explorer ölçek hizmeti Tamam](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Hizmette üç örnek vardır ve ağaç görünümü örneklerin üzerinde çalıştığı düğümleri gösterir.

Bu basit yönetim göreviyle, ön uç hizmetinin Kullanıcı yükünü işlemesi için kullanılabilir kaynakları iki katına çıkarmış olursunuz. Güvenilir bir şekilde çalışması için bir hizmetin birden fazla örneğinin gerekli olmadığı anlaşılması önemlidir. Bir hizmet başarısız olursa, Service Fabric yeni bir hizmet örneğinin kümede çalıştığından emin olur.

## <a name="fail-over-services-in-a-cluster"></a>Kümedeki hizmetleri yük devretme

Hizmet yük devretmesini göstermek için, düğüm yeniden başlatması Service Fabric Explorer kullanılarak benzetilir. Hizmetinizin yalnızca bir örneğinin çalıştığından emin olun.

1. Service Fabric Explorer kümenizdeki açın (örneğin, `http://localhost:19080`).
1. Hizmetinizin örneğini çalıştıran düğümün yanındaki üç nokta ( **...** ) simgesini seçin ve düğümü yeniden başlatın.

    ![Service Fabric Explorer yeniden başlatma düğümü](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Hizmetinizin örneği farklı bir düğüme taşınır ve uygulamanız kapalı kalma süresine sahip değildir.

    ![Service Fabric Explorer yeniden başlatma düğümü başarılı](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Service Fabric için bir Spring Boot uygulaması dağıtma
* Uygulamayı yerel kümenize dağıtma
* Birden çok düğümde uygulamanın ölçeğini genişletme
* Kullanılabilirliğine hiçbir isabet olmadan hizmetinizin yük devretmesini gerçekleştirme

Service Fabric 'de Java uygulamalarıyla çalışma hakkında daha fazla bilgi edinmek için Java uygulamalarına yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Java uygulaması dağıtma](./service-fabric-tutorial-create-java-app.md)
