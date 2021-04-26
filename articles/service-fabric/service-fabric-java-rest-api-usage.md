---
title: Azure Service Fabric Java Istemci API 'Leri
description: Service Fabric istemci REST API belirtimini kullanarak Service Fabric Java istemci API 'Leri oluşturma ve kullanma
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324633"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java Istemci API 'Leri

Service Fabric istemci API 'Leri, Azure 'da, şirket içinde, yerel geliştirme makinesinde veya başka bir bulutta bulunan bir Service Fabric kümesinde mikro hizmet tabanlı uygulamaları ve kapsayıcıları dağıtmaya ve yönetmeye olanak tanır. Bu makalede, Service Fabric istemci REST API 'lerinin en üstünde Service Fabric Java istemci API 'lerinin nasıl oluşturulacağı ve kullanılacağı açıklanmaktadır

## <a name="generate-the-client-code-using-autorest"></a>İstemci kodunu, oto Rest kullanarak oluşturma

[Oto Rest](https://github.com/Azure/autorest) , daha fazla Web hizmetine erişmek için istemci kitaplıkları üreten bir araçtır. Oto geri kalanına giriş, Openapı belirtim biçimini kullanarak REST API tanımlayan bir belirtimdir. [Service Fabric ISTEMCI REST API 'leri](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) bu belirtimi izler.

Service Fabric Java istemci kodunu, oto Rest aracını kullanarak oluşturmak için aşağıda bahsedilen adımları izleyin.

1. Makinenize nodejs ve NPM yükleme

    Linux kullanıyorsanız:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Mac OS X kullanıyorsanız:
    ```bash
    brew install node
    ```

2. NPM kullanarak oto Rest 'i yükler.
    ```bash
    npm install -g autorest
    ```

3. [Azure-REST-API-spec](https://github.com/Azure/azure-rest-api-specs) deposunu yerel makinenizde çatalla ve kopyalayın ve makinenizin terminalinde kopyalanmış konuma gidin.


4. Klonlanan depounuzda aşağıda bahsedilen konuma gidin.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Küme sürümünüz 6,0 değilse, kararlı klasörde ilgili dizine gidin.
    >   

5. Java istemci kodunu oluşturmak için aşağıdaki oto REST komutunu çalıştırın.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Aşağıda, oto Rest kullanımının kullanımını gösteren bir örnek verilmiştir.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Aşağıdaki komut, ``servicefabric.json`` Belirtim dosyasını girdi olarak alır ve klasörde Java istemci kodu oluşturur ``java-rest-api-     code`` ve kodu  ``servicefabricrest`` ad alanına barındırır. Bu adımdan sonra iki klasör ``models`` ``implementation`` ve klasörde oluşturulan iki dosya bulunur ``ServiceFabricClientAPIs.java`` ``package-info.java`` ``java-rest-api-code`` .


## <a name="include-and-use-the-generated-client-in-your-project"></a>Projenizde oluşturulan istemciyi ekleme ve kullanma

1. Oluşturulan kodu projenize uygun şekilde ekleyin. Oluşturulan kodu kullanarak bir kitaplık oluşturmanızı ve bu kitaplığı projenize eklemeyi öneririz.
2. Bir kitaplık oluşturuyorsanız, kitaplığınızın projesine aşağıdaki bağımlılığı ekleyin. Farklı bir yaklaşımı takip ediyorsanız, bağımlılığı uygun şekilde ekleyin.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Örneğin, Maven derleme sistemi kullanıyorsanız dosyanıza aşağıdakileri ekleyin ``pom.xml`` :

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Aşağıdaki kodu kullanarak bir RestClient oluşturun:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. İstemci nesnesini kullanın ve uygun çağrıları gereken şekilde yapın. İstemci nesnesinin kullanımını gösteren bazı örnekler aşağıda verilmiştir. Aşağıdaki API 'leri kullanmadan önce uygulama paketinin oluşturulup görüntü deposuna yüklendiğini varsayalım.
    * Uygulama sağlama
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Uygulama oluşturma

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Oluşturulan kodu anlama
Her API için, uygulamanın dört adet aşırı yüklemesini bulacaksınız. İsteğe bağlı parametreler varsa, bu isteğe bağlı parametreler dahil olmak üzere dört çeşitte daha bulabilirsiniz. Örneğin, API 'YI düşünün ``removeReplica`` .
 1. **public void removeReplica (dize düğ, UUID PartitionID, dize yineleme kimliği, Boole forceRemove, uzun zaman aşımı)**
    * Bu, removeReplica API çağrısının zaman uyumlu varyantıdır
 2. **ortak ServiceFuture \<Void> removereperepasync (dize düğ, UUID PartitionID, dize yineleme kimliği, Boole forceRemove, uzun zaman aşımı, son ServiceCallback \<Void> ServiceCallback)**
    * Bu API çağrısı değişkeni, gelecekteki tabanlı zaman uyumsuz programlama kullanmak ve geri çağırmaları kullanmak istiyorsanız kullanılabilir
 3. **Public observable \<Void> removereperepasync (dize düğ, UUID PartitionID, dize yineleme kimliği)**
    * Bu API çağrısı değişkeni, reaktif zaman uyumsuz programlama kullanmak istiyorsanız kullanılabilir
 4. **Public observable \<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync (dize düğ, UUID PartitionID, dize yineleme kimliği)**
    * Bu API çağrısı değişkeni, reaktif zaman uyumsuz programlama kullanmak ve ham Rest yanıtıyla başa çıkmak istiyorsanız kullanılabilir

## <a name="next-steps"></a>Sonraki adımlar
* [SERVICE fabrıc REST API 'leri](/rest/api/servicefabric/) hakkında bilgi edinin
