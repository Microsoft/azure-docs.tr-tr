---
title: Azure'da kapsayıcıları Service Fabric uygulaması olarak paketleme ve dağıtma | Microsoft Docs
description: Bu öğreticide, Yeoman kullanarak Azure Service Fabric uygulaması tanımı oluşturmayı ve uygulamayı paketlemeyi öğrenirsiniz.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Kapsayıcılar, Mikro Hizmetler, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b4931b62aaf04db2ee4ff6f4a5e2b96274b8af88
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385048"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application-using-yeoman"></a>Öğretici: Yeumman kullanarak kapsayıcıları Service Fabric uygulama olarak paketleme ve dağıtma

Bu öğretici, bir dizinin ikinci bölümüdür. Bu öğreticide, bir şablon oluşturma aracı (Yeoman) kullanılarak bir Service Fabric uygulaması tanımı oluşturulmaktadır. Daha sonra bu uygulama Service Fabric’e kapsayıcı dağıtmak için kullanılabilir. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Yeoman’ı yükleme
> * Yeoman’ı kullanarak bir uygulama paketi oluşturma
> * Uygulama paketinin ayarlarını kapsayıcılarla kullanıma uygun şekilde yapılandırma
> * Uygulama oluşturma
> * Uygulamayı dağıtma ve çalıştırma
> * Uygulamayı temizleme

## <a name="prerequisites"></a>Önkoşullar

* Bu öğretici serisinin [1. Bölümünde](service-fabric-tutorial-create-container-images.md) oluşturulup Azure Container Registry’ye gönderilen kapsayıcı görüntüleri kullanılır.
* Linux geliştirme ortamı [ayarlanmıştır](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Yeoman’ı yükleme

Service Fabric, Yeoman şablon oluşturucu kullanarak terminalden uygulama oluşturmaya yardımcı olacak yapı iskelesi araçları sağlar. Yeoman şablon oluşturucunuzun olduğundan emin olmak için aşağıdaki adımları izleyin.

1. Makinenize nodejs ve NPM’yi yükleyin. Mac OSX kullanıcılarının Homebrew paket yöneticisi'ni kullanması gerekir

    ```bash
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
    nvm install node 
    ```
2. NPM’den makinenize Yeoman şablon oluşturucuyu yükleyin

    ```bash
    npm install -g yo
    ```
3. Service Fabric Yeoman kapsayıcı oluşturucuyu yükleyin

    ```bash 
    npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Yeoman ile Docker görüntü kapsayıcısını paketleme

1. Kopyalanan deponun “container-tutorial” dizininde Service Fabric kapsayıcı uygulamasını oluşturmak için aşağıdaki komutu çalıştırın.

    ```bash
    yo azuresfcontainer
    ```
2. Uygulamanıza bir ad vermek için lütfen “TestContainer” yazın
3. Uygulama hizmetinize bir ad vermek için lütfen “azurevotefront” yazın.
4. Ön uç deposu için ACR’deki kapsayıcı görüntüsü yolu sağlayın. Örnek: “\<acrName>.azurecr.io/azure-vote-front:v1”. \<acrName> alanı, önceki öğreticide kullanılan değerle aynı olmalıdır.
5. Komutlar bölümünü boş bırakmak için Enter tuşuna basın.
6. Örnek sayısı olarak 1 belirtin.

Aşağıda yo komutunu çalıştırmanın girişi ve çıktısı gösterilmektedir:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Yeoman kullanılarak oluşturulmuş olan bir uygulamaya başka bir kapsayıcı hizmeti eklemek için aşağıdaki adımları uygulayın:

1. Dizini bir düzey değiştirerek **TestContainer** dizinine gidin. Örnek: *./TestContainer*
2.           `yo azuresfcontainer:AddService`'i çalıştırın.
3. Hizmete “azurevoteback” adını verin
4. Redis için kapsayıcı görüntüsü yolunu sağlayın: “alpine:redis”
5. Komutlar bölümünü boş bırakmak için Enter tuşuna basın
6. "1" örnek sayısı belirtin.

Kullanılan hizmeti eklemeye yönelik girişlerin tümü aşağıda gösterilmektedir:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Bu öğreticinin geri kalanında **TestContainer** dizininde çalışacağız. Örneğin, *./TestContainer/TestContainer*. Bu dizinde aşağıdakiler bulunmalıdır.

```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Uygulama bildirimini Azure Container Registry’ye ait kimlik bilgileriyle yapılandırın

Service Fabric’in kapsayıcı görüntülerini Azure Container Registry’den çekebilmesi için **ApplicationManifest.xml** dosyasında kimlik bilgilerini sağlamamız gerekir.

ACR Örneğinizde oturum açın. İşlemi tamamlamak için **az acr login** komutunu kullanın. Kapsayıcı kayıt defterine oluşturulduğunda verilen benzersiz adı sağlayın.

```bash
az acr login --name <acrName>
```

Komut tamamlandığında bir **Oturum Başarıyla Açıldı** iletisi döndürür.

Ardından, kapsayıcı kayıt defterinizin parolasını almak için aşağıdaki komutu çalıştırın. Bu parola, kapsayıcı görüntülerini çekerken ACR’de kimlik doğrulamak için Service Fabric tarafından kullanılır.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

**ApplicationManifest.xml** dosyasında ön uç hizmetinin **ServiceManifestImport** öğesinin altına kod parçacığını ekleyin. **AccountName** alanına kendi **acrName** değerinizi ve **Password** alanına bir önceki komuttan döndürülen parolayı girin. Bu belgenin sonunda tam bir **ApplicationManifest.xml** verilmiştir.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>İletişim ve kapsayıcı bağlantı noktalarıyla konak bağlantı noktalarını eşlemeyi yapılandırın

### <a name="configure-communication-port"></a>İletişim bağlantı noktasını yapılandırma

İstemcilerin hizmetinizle iletişim kurabilmesi için bir HTTP uç noktası yapılandırın. *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* dosyasını açın ve **ServiceManifest** öğesinde bir uç nokta kaynağı belirtin.  Protokolü, bağlantı noktasını ve adını ekleyin. Bu öğreticide hizmet, bağlantı noktası 80’i dinlemektedir. Aşağıdaki kod parçacığı, kaynakta *ServiceManifest* etiketinin altına yerleştirilir.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```

Benzer şekilde, arka uç hizmeti için Hizmet Bildirimi’ni (Service Manifest) değiştirin. *./TestContainer/azurevotebackPkg/ServiceManifest.xml* dosyasını açın ve **ServiceManifest** öğesinde bir uç nokta kaynağı belirtin. Bu öğreticide, redis için varsayılan değer olan 6379 kullanılmaktadır. Aşağıdaki kod parçacığı, kaynakta *ServiceManifest* etiketinin altına yerleştirilir.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

**UriScheme** değerinin sağlanması, kapsayıcı uç noktasını bulunabilirlik için Service Fabric Adlandırma hizmetine otomatik olarak kaydeder. Bu makalenin sonunda örnek olarak arka uç hizmetine yönelik tam bir ServiceManifest.xml örnek dosyası verilmiştir.

### <a name="map-container-ports-to-a-service"></a>Kapsayıcı bağlantı noktalarını bir hizmete eşleme

Kümedeki kapsayıcıları kullanıma sunmak için “ApplicationManifest.xml” dosyasında bir bağlantı noktası bağlaması oluşturulması da gerekir. **PortBinding** ilkesi **ServiceManifest.xml** dosyalarında tanımladığımız **Uç Noktalarına (Endpoints)** başvurur. Bu uç noktalarına gelen istekler, burada açılan ve bağlanan kapsayıcı bağlantı noktalarına eşlenir. 80 ve 6379 numaralı bağlantı noktalarını uç noktalarına bağlamak için **ApplicationManifest.xml** dosyasına aşağıdaki kodu ekleyin. Bu belgenin sonunda tam bir **ApplicationManifest.xml** verilmiştir.

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Arka uç hizmetine bir DNS adı ekleme

Service Fabric’in bu DNS adını arka uç hizmetine atayabilmesi için söz konusu adın **ApplicationManifest.xml** dosyasında belirtilmesi gerekir. Aşağıda gösterildiği gibi **Service** öğesine **ServiceDnsName** özniteliğini ekleyin:

```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

Ön uç hizmeti, Redis örneğinin DNS adını öğrenmek için bir ortam değişkenini okur. Bu ortam değişkeni, Docker görüntüsünü oluşturmak için kullanılan Dockerfile’da zaten tanımlanmıştır ve burada herhangi bir işlem yapılmasına gerek yoktur.

```Dockerfile
ENV REDIS redisbackend.testapp
```

Aşağıdaki kod parçacığı, ön uç Python kodunun Dockerfile’da belirtilen ortam değişkenini nasıl aldığını göstermektedir. Burada herhangi bir işlem yapılmasına gerek yoktur.

```python
# Get DNS Name
redis_server = os.environ['REDIS']

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Öğreticinin bu noktasında Service Package uygulamasına yönelik şablon bir kümeye dağıtılmak için hazırdır. Sıradaki öğreticide bu uygulama bir Service Fabric kümesine dağıtılıp burada çalıştırılmaktadır.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric kümesi oluşturma

Uygulamayı Azure'a dağıtmak için, uygulamayı çalıştıracak bir Service Fabric kümesine ihtiyacınız vardır. Aşağıdaki komutlar Azure 'da beş düğümlü bir küme oluşturur.  Komutlar Ayrıca kendinden imzalı bir sertifika oluşturur, bunu bir anahtar kasasına ekler ve sertifikayı bir pek dosyası olarak yerel olarak indirir. Yeni sertifika, dağıtıldıktan sonra kümenin güvenliğini sağlamak için kullanılır ve istemcilerin kimliğini doğrulamak için kullanılır.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  
# The certificate is downloaded locally as a PEM file.
az sf cluster create --resource-group $ResourceGroupName --location $Location \ 
--certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \ 
--cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName \ 
--vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Web ön ucu hizmeti, gelen trafik için 80 numaralı bağlantı noktasını dinlemek üzere yapılandırılmıştır. Varsayılan olarak, 80 numaralı bağlantı noktası, küme sanal makinelerinizdeki ve Azure Yük dengeleyicisinde açıktır.
>

Kendi kümenizi oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da Service Fabric kümesi oluşturma](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Uygulamayı oluşturup kümeye dağıtma

Uygulamayı, Service Fabric CLI’yi kullanarak Azure kümesine dağıtabilirsiniz. Service Fabric CLI makinenizde yüklü değilse [buradaki](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) yönergeleri uygulayarak yükleyin.

Azure’daki Service Fabric kümesine bağlanın. Örnek uç noktayı kendi uç noktanız ile değiştirin. Uç nokta, aşağıdaki gibi tam bir URL olmalıdır.  PED dosyası, önceden oluşturulmuş otomatik olarak imzalanan sertifikadır.

```bash
sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
```

**TestContainer** dizininde sağlanan yükleme betiğini kullanarak uygulama paketini kümenin görüntü deposuna kopyalayın, uygulama türünü kaydedin ve uygulamanın bir örneğini oluşturun.

```bash
./install.sh
```

Bir tarayıcı açın ve http:\//containertestcluster.eastus.cloudapp.Azure.com:19080/Explorer adresinde Service Fabric Explorer gidin. Uygulamalar düğümünü genişletin ve uygulamanızın türü için bir giriş ve örnek için başka bir giriş olduğuna dikkat edin.

![Service Fabric Explorer][sfx]

Çalışan uygulamaya bağlanmak için bir Web tarayıcısı açın ve küme URL 'sine gidin-örneğin, http:\//containertestcluster.eastus.cloudapp.Azure.com:80. Web kullanıcı arabiriminde Voting (Oylama) uygulamasını görmeniz gerekir.

![votingapp][votingapp]

## <a name="clean-up"></a>Temizleme

Kümeden uygulama örneğini silmek ve uygulama türünün kaydını silmek için şablonda sağlanan kaldırma betiğini kullanın. Bu komutun örneği temizlemesi zaman alır ve “install.sh” komutu bu betiğin hemen ardından çalıştırılamaz.

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Tamamlanan bildirimlerin örnekleri

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Ön uç ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Yeoman kullanılarak birden çok kapsayıcı bir Service Fabric uygulaması şeklinde paketlendi. Ardından, bu uygulama bir Service Fabric kümesine dağıtılıp burada çalıştırıldı. Aşağıdaki adımlar tamamlandı:

> [!div class="checklist"]
> * Yeoman’ı yükleme
> * Yeoman’ı kullanarak bir uygulama paketi oluşturma
> * Uygulama paketinin ayarlarını kapsayıcılarla kullanıma uygun şekilde yapılandırma
> * Uygulama oluşturma
> * Uygulamayı dağıtma ve çalıştırma
> * Uygulamayı temizleme

Service Fabric’te uygulamaya yönelik yük devretme ve ölçeklendirme hakkında bilgi edinmek için sıradaki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Uygulamalar için yük devretme ve ölçeklendirme hakkında bilgi edinin](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png