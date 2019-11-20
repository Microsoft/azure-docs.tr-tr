---
title: Linux üzerinde Azure Service Fabric kapsayıcı uygulaması oluşturma | Microsoft Docs
description: Azure Service Fabric üzerinde ilk Linux kapsayıcı uygulamanızı oluşturun. Uygulamanızla bir Docker görüntüsü oluşturun, görüntüyü bir kapsayıcı kayıt defterine iletin, bir Service Fabric kapsayıcı uygulaması oluşturup dağıtın.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/4/2019
ms.author: atsenthi
ms.openlocfilehash: 2bb9a5e8e42901f22d9f68d691684614c7161620
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650664"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Linux üzerinde ilk Service Fabric kapsayıcı uygulamanızı oluşturma
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Bir Service Fabric kümesindeki Linux kapsayıcısında mevcut olan bir uygulamayı çalıştırmak için uygulamanızda herhangi bir değişiklik yapılması gerekmez. Bu makalede, Python [Flask](http://flask.pocoo.org/) web uygulaması içeren bir Docker görüntüsü oluşturma ve bunu Service Fabric kümesine dağıtma işlemlerinde size yol gösterilir. Ayrıca, kapsayıcıya alınmış uygulamanızı [Azure Container Registry](/azure/container-registry/) aracılığıyla paylaşırsınız. Bu makale Docker hakkında temel bir anlayışınızın olduğunu varsayar. [Docker’a Genel Bakış](https://docs.docker.com/engine/understanding-docker/) makalesini okuyarak Docker hakkında bilgi edinebilirsiniz.

> [!NOTE]
> Bu makale, bir Linux geliştirme ortamı için geçerlidir.  Service Fabric kümesi çalışma zamanının ve Docker çalışma zamanının aynı işletim sisteminde çalışıyor olması gerekir.  Linux kapsayıcılarını bir Windows kümesinde çalıştıramazsınız.

## <a name="prerequisites"></a>Önkoşullar
* Şunları çalıştıran bir geliştirme bilgisayarı:
  * [Service Fabric SDK’sı ve araçları](service-fabric-get-started-linux.md).
  * [Linux için Docker CE](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry’deki bir kayıt defteri - Azure aboneliğinizde [Kapsayıcı kayıt defteri oluşturun](../container-registry/container-registry-get-started-portal.md). 

## <a name="define-the-docker-container"></a>Docker kapsayıcısını tanımlama
Docker Hub’ında bulunan [Python görüntüsünü](https://hub.docker.com/_/python/) temel alan bir görüntü oluşturun. 

Bir Dockerfile içinde Docker kapsayıcınızı belirtin. Dockerfile, kapsayıcınızın içindeki ortamı ayarlama, çalıştırmak istediğiniz uygulamayı yükleme ve bağlantı noktalarını eşleme yönergelerinden oluşur. Dockerfile, görüntüyü oluşturan `docker build` komutunun girdisidir. 

Boş bir dizin oluşturun ve *Dockerfile* dosyasını oluşturun (dosya uzantısı kullanmayın). Aşağıdakini *Dockerfile* dosyasına ekleyin ve değişikliklerinizi kaydedin:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Daha fazla bilgi için [Dockerfile başvurusunu](https://docs.docker.com/engine/reference/builder/) okuyun.

## <a name="create-a-basic-web-application"></a>Temel bir web uygulaması oluşturma
Bağlantı noktası 80 üzerinden dinleyen ve "Merhaba Dünya!" başlığını döndüren bir Flask web uygulaması oluşturun. Aynı dizinde, *requirements.txt* dosyasını oluşturun. Aşağıdakini dosyaya ekleyin ve değişikliklerinizi kaydedin:
```
Flask
```

Ayrıca *app.py* dosyasını da oluşturun ve aşağıdaki kod parçacığını ekleyin:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Görüntü oluşturma
Web uygulamanızı çalıştıran görüntüyü oluşturmak için `docker build` komutunu çalıştırın. Bir PowerShell penceresi açıp *c:\temp\helloworldapp* dizinine gidin. Şu komutu çalıştırın:

```bash
docker build -t helloworldapp .
```

Bu komut Dockerfile içindeki yönergeleri kullanarak yeni görüntüyü oluşturur ve `helloworldapp` olarak adlandırır (-t etiketi). Bir kapsayıcı görüntüsü oluşturmak için, önce temel görüntü uygulamanın eklendiği Docker Hub’ından indirilir. 

Oluşturma komutu tamamlandıktan sonra, yeni görüntü üzerindeki bilgileri görmek için `docker images` komutunu çalıştırın:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma
Kapsayıcıya alınmış uygulamanızı kapsayıcı kayıt defterine göndermeden önce uygulamanın yerel olarak çalıştığını doğrulayın. 

Bilgisayarınızın 4000 numaralı bağlantı noktasını kapsayıcının ortaya konan 80 numaralı bağlantı noktasına eşleyerek uygulamayı çalıştırın:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name*, çalışan kapsayıcıya bir ad verir (kapsayıcı kimliği yerine).

Çalışan kapsayıcıya bağlanın. 4000 numaralı bağlantı noktasında döndürülen IP adresini işaret eden bir Web tarayıcısı açın, örneğin "http:\//localhost: 4000". "Hello World!" başlığının tarayıcıda gösterildiğini görürsünüz.

![Merhaba Dünya!][hello-world]

Kapsayıcınızı durdurmak için şu komutu çalıştırın:

```bash
docker stop my-web-site
```

Kapsayıcıyı geliştirme makinenizden silin:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Görüntüyü kapsayıcı kayıt defterine gönderme
Uygulamanın Docker'da çalıştığını doğruladıktan sonra, görüntüyü Azure Container Registry'de kayıt defterine gönderin.

Kayıt `docker login` defteri [kimlik bilgilerinizle](../container-registry/container-registry-authentication.md), kapsayıcı kayıt defterinizde oturum açmak için ' i çalıştırın.

Aşağıdaki örnekte, bir Azure Active Directory [hizmet sorumlusunun](../active-directory/develop/app-objects-and-service-principals.md) kimliği ve parolası geçirilmiştir. Örneğin, bir otomasyon senaryosu için kayıt defterinize bir hizmet sorumlusu atamış olabilirsiniz. Ya da kayıt defteri Kullanıcı adınızı ve parolanızı kullanarak oturum açmanız gerekir.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Aşağıdaki komut, görüntünün kayıt defterinize ait tam yolu içeren bir etiketini veya diğer adını oluşturur. Bu örnek, kayıt defterinin kökünde dağınıklığı önlemek için `samples` ad alanına görüntüyü yerleştirir.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Görüntüyü kapsayıcı kayıt defterinize gönderin:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Docker görüntüsünü Yeoman ile paketleme
Linux için Service Fabric SDK’sı uygulamanızı oluşturmayı ve kapsayıcı görüntüsü eklemeyi kolaylaştıran bir [Yeoman](https://yeoman.io/) oluşturucu içerir. Şimdi Yeoman kullanarak *SimpleContainerApp* adlı tek bir Docker kapsayıcısı olan bir uygulama oluşturalım.

Service Fabric kapsayıcı uygulaması oluşturmak için, terminal penceresini açın ve `yo azuresfcontainer` komutunu çalıştırın. 

Uygulamanıza (örneğin, `mycontainer`) ve uygulama hizmetine (örneğin, `myservice`) bir ad verin.

Görüntü adı için kapsayıcı kayıt defterindeki kapsayıcı görüntüsünün URL'sini sağlayın (örneğin: "myregistry.azurecr.io/samples/helloworldapp"). 

Bu görüntüde iş yükü giriş noktası tanımlanmış olduğundan, giriş komutlarının açıkça belirtilmesi gerekmez (komutlar kapsayıcının içinde çalıştırılır ve bu da başlatma sonrasında kapsayıcıyı çalışır durumda tutar). 

"1" örnek sayısı belirtin.

Bağlantı noktası eşlemesini uygun biçimde belirtin. Bu makalede, bağlantı noktası eşlemesi olarak sağlamanız ```80:4000``` gerekir. Bunu yaparak, ana makinedeki 4000 numaralı bağlantı noktasına gelen tüm gelen isteklerin kapsayıcıda 80 numaralı bağlantı noktasına yönlendirildiğini yapılandırdınız.

![Kapsayıcılar için Service Fabric Yeoman oluşturucusu][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Kapsayıcı deposu kimlik doğrulamasını yapılandırma

Kapsayıcı görüntüsü indirme için farklı kimlik doğrulama türlerini nasıl yapılandıracağınızı öğrenmek için bkz. [kapsayıcı deposu kimlik doğrulaması](configure-container-repository-credentials.md).

## <a name="configure-isolation-mode"></a>Yalıtım modunu yapılandırma
6,3 çalışma zamanı sürümüyle, sanal makine yalıtımı Linux kapsayıcıları için desteklenir ve bu nedenle kapsayıcılar için iki yalıtım modunu destekler: işlem ve Hyper-V. Hyper-V yalıtım modu ile, çekirdekler her kapsayıcı ve kapsayıcı ana bilgisayar arasında yalıtılır. Hyper-V yalıtımı, [clear kapsayıcıları](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker)kullanılarak uygulanır. Yalıtım modu, uygulama bildirimi dosyasındaki `ServicePackageContainerPolicy` öğesindeki Linux kümeleri için belirtilir. Belirtilebilen yalıtım modları `process`, `hyperv` ve `default` modlarıdır. Varsayılan işlem yalıtım modudur. Aşağıdaki kod parçacığı uygulama bildirimi dosyasında yalıtım modunun nasıl belirtildiğini gösterir.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Kaynak idaresini yapılandırma
[Kaynak idaresi](service-fabric-resource-governance.md) kapsayıcının konakta kullanabildiği kaynakları kısıtlar. Uygulama bildiriminde belirtilen `ResourceGovernancePolicy` öğesi, hizmet kod paketinin kaynak sınırlarını tanımlamak için kullanılır. Kaynak sınırları aşağıdaki kaynaklar için ayarlanabilir: Bellek, MemorySwap, CpuShares (CPU göreli ağırlığı), Memoryrezervationınmb, BlkioWeight (BlockIO göreli ağırlığı). Bu örnekte, Guest1Pkg hizmet paketi bulunduğu küme düğümlerinde bir çekirdek alır. Bellek sınırları mutlaktır; dolayısıyla, kod paketi 1024 MB bellekle (aynı genel garantili ayırmayla) sınırlıdır. Kod paketleri (kapsayıcılar veya işlemler) bu sınırı aşan miktarda bellek ayıramazlar ve bunu denediklerinde yetersiz bellek özel durumu ortaya çıkar. Kaynak sınırı zorlamasının çalışması için, hizmet paketi içindeki tüm kod paketlerinin bellek sınırlarının belirtilmiş olması gerekir.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK ayarlarını yapılandırma 

Service Fabric, v6.1 sürümünden itibaren [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) olaylarını otomatik olarak sistem durumu raporuyla tümleştirir. Bu, kapsayıcınızda **HEALTHCHECK** özelliği etkinse kapsayıcının sistem durumuna ilişkin Docker tarafından bildirilen her değişiklik için Service Fabric’in durumu bildireceği anlamına gelir. *health_status* özelliği *healthy* olduğunda [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)’da **OK** şeklinde bir durum raporu görüntülenirken, *health_status* özelliği *unhealthy* olduğunda **WARNING** görünür. 

En son v 6.4 yenileme sürümü ile başlayarak, Docker HEALTHCHECK değerlendirmelerinin hata olarak bildirilmesi gerektiğini belirtme seçeneğiniz vardır. Bu seçenek etkinleştirilirse, *health_status* *sağlıklı* olduğunda bir **Tamam** sistem durumu raporu görüntülenir ve *health_status* *sağlıksız*olduğunda **hata** görüntülenir.

Kapsayıcı durumunun izlenmesi için gerçekleştirilen gerçek denetimi gösteren **HEALTHCHECK** yönergesi, kapsayıcı görüntüsü oluşturulurken kullanılan Dockerfile dosyasında mevcut olmalıdır.

![HealthCheckHealthy][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

ApplicationManifest dosyasındaki **ContainerHostPolicies** kapsamında **HealthConfig** seçeneklerini belirterek **HEALTHCHECK** davranışını yapılandırabilirsiniz.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Varsayılan olarak, *ıncludedockerhealthstatusınsystemhealthreport* **true**olarak ayarlanır, *RestartContainerOnUnhealthyDockerHealthStatus* **false**olarak ayarlanır ve *treatcontainerunhealthyıstatusaserror* false olarak ayarlanır. 

*RestartContainerOnUnhealthyDockerHealthStatus* özelliği **true** olarak ayarlanırsa, tekrarlanan şekilde durumunun iyi olmadığı bildirilen kapsayıcılar yeniden başlatılır (muhtemelen diğer düğümlerde).

*Treatcontainerunhealthystatusaserror* **değeri true**olarak ayarlanırsa, kapsayıcının *health_status* *sağlıksız*olduğunda **hata** durumu raporları görüntülenir.

Tüm Service Fabric kümesi için **HEALTHCHECK** tümleştirmesini devre dışı bırakmak istiyorsanız [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) özelliğini **false** olarak ayarlamanız gerekir.

## <a name="deploy-the-application"></a>Uygulamayı dağıtma
Uygulama oluşturulduktan sonra Service Fabric CLI kullanarak yerel kümeye dağıtabilirsiniz.

Yerel Service Fabric kümesine bağlanın.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Uygulama paketini kümenin görüntü deposuna kopyalamak, uygulama türünü https://github.com/Azure-Samples/service-fabric-containers/ kaydetmek ve uygulamanın bir örneğini oluşturmak için konumundaki şablonlarda belirtilen Install betiğini kullanın.


```bash
./install.sh
```

Bir tarayıcı açın ve http:\//localhost: 19080/Explorer konumundaki Service Fabric Explorer gidin (Mac OS X vagrant kullanılıyorsa, localhost 'u VM 'nin özel IP 'si ile değiştirin). Uygulamalar düğümünü genişletin ve şu anda uygulamanızın türü için bir giriş ve bu türün ilk örneği için başka bir giriş olduğuna dikkat edin.

Çalışan kapsayıcıya bağlanın. 4000 numaralı bağlantı noktasında döndürülen IP adresini işaret eden bir Web tarayıcısı açın, örneğin "http:\//localhost: 4000". "Hello World!" başlığının tarayıcıda gösterildiğini görürsünüz.

![Merhaba Dünya!][hello-world]


## <a name="clean-up"></a>Temizleme
Yerel dağıtım kümesinden uygulama örneğini silmek ve uygulama türünün kaydını silmek için şablonda sağlanan kaldırma betiğini kullanın.

```bash
./uninstall.sh
```

Görüntüyü kapsayıcı kayıt defterine gönderdikten sonra yerel görüntüyü geliştirme bilgisayarınızdan silebilirsiniz:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Tam Service Fabric uygulaması ve hizmet bildirimleri örneği
Bu makalede kullanılan tam hizmet ve uygulama bildirimleri aşağıda verilmiştir.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Mevcut bir uygulamaya daha fazla hizmet ekleme

Yeoman kullanılarak zaten oluşturulmuş bir uygulamaya başka bir kapsayıcı hizmeti eklemek için aşağıdaki adımları uygulayın:

1. Dizini mevcut uygulamanın kök dizinine değiştirin. Örneğin Yeoman tarafından oluşturulan uygulama `MyApplication` ise `cd ~/YeomanSamples/MyApplication` olacaktır.
2. `yo azuresfcontainer:AddService`'i çalıştırın.

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Kapsayıcı zorla sonlandırılmadan önceki zaman aralığını yapılandırın

Hizmet silme (veya başka bir düğüme taşıma) başladıktan sonra, çalışma zamanının kapsayıcı kaldırılmadan önce ne kadar bekleyeceğine ilişkin bir zaman aralığı yapılandırabilirsiniz. Zaman aralığını yapılandırma, kapsayıcıya `docker stop <time in seconds>` komutunu gönderir.  Daha ayrıntılı bilgi için bkz. [docker durdurma](https://docs.docker.com/engine/reference/commandline/stop/). Beklenecek zaman aralığı, `Hosting` bölümünde belirtilir. Aşağıdaki küme bildirimi kod parçacığı, bekleme aralığının nasıl ayarlandığını gösterir:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Varsayılan zaman aralığı 10 saniye olarak ayarlanır. Bu yapılandırma dinamik olduğundan, kümedeki yalnızca yapılandırmaya yönelik bir güncelleştirme zaman aşımını güncelleştirir. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Kullanılmayan kapsayıcı görüntülerini kaldırmak için çalışma zamanını yapılandırma

Service Fabric kümesini kullanılmayan kapsayıcı görüntülerini düğümden kaldıracak şekilde yapılandırabilirsiniz. Bu yapılandırma, düğümde çok fazla kapsayıcı görüntüsü varsa yeniden disk alanı elde edilmesine imkan tanır. Bu özelliği etkinleştirmek için küme bildirimindeki `Hosting` bölümünü aşağıdaki kod parçacığında gösterildiği gibi güncelleştirin: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

Silinmemesi gereken görüntüleri `ContainerImagesToSkip` parametresi altında belirtebilirsiniz. 

## <a name="configure-container-image-download-time"></a>Kapsayıcı görüntüsü indirme süresini yapılandırma

Service Fabric çalışma zaman, kapsayıcı görüntülerinin indirilip ayıklanması için 20 dakika ayırır ve bu süre çoğu kapsayıcı görüntüsü için yeterlidir. Görüntüler büyükse veya ağ bağlantısı yavaşsa görüntü indirme ve ayıklama işlemi iptal edilmeden önce beklenecek sürenin artırılması gerekebilir. Zaman aşımı, aşağıdaki kod parçacığında gösterildiği gibi küme bildiriminin **Barındırma** bölümündeki **ContainerImageDownloadTimeout** özniteliği kullanılarak ayarlanabilir:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Kapsayıcı bekletme ilkesi ayarlama

Service Fabric (6.1 veya üzeri sürümler), kapsayıcı başlatma hatalarının tanılanmasına yardımcı olmak için sonlandırılan veya başlatılamayan kapsayıcıların bekletilmesini destekler. Bu ilke, aşağıdaki kod parçacığında gösterildiği gibi **ApplicationManifest.xml** dosyasında ayarlanabilir:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** ayarı, başarısız olduğunda bekletilecek kapsayıcı sayısını belirtir. Negatif bir değer belirtilirse başarısız olan tüm kapsayıcılar bekletilir. **ContainersRetentionCount** özniteliği belirtilmezse hiçbir kapsayıcı bekletilmez. **ContainersRetentionCount** özniteliği, kullanıcıların test ve üretim kümeleri için farklı değerler belirtebilmesi amacıyla Uygulama Parametrelerini destekler. Kapsayıcı hizmetinin diğer düğümlere taşınmasını önlemek için bu özellikler kullanılırken kapsayıcı hizmetinin belirli bir düğümü hedeflemesini sağlamak için yerleştirme kısıtlamaları kullanın. Bu özellik kullanılarak bekletilen tüm kapsayıcılar el ile kaldırılmalıdır.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Docker cinini özel bağımsız değişkenlerle başlatma

Service Fabric çalışma zamanı 6.2 sürümü ve üzeriyle, Docker cinini özel bağımsız değişkenler kullanarak başlatabilirsiniz. Özel bağımsız değişkenler belirtildiğinde, Service Fabric `--pidfile` bağımsız değişkeni hariç hiçbir bağımsız değişkeni Docker altyapısına geçirmez. Bu nedenle, `--pidfile` bir bağımsız değişken olarak geçirilmemelidir. Ayrıca, Service Fabric’in cin ile iletişim kurması için bağımsız değişken docker’ın Windows’da varsayılan ad kanalında (veya Linux’ta unix etki alanı yuvasında) dinlemeye devam etmesini sağlamalıdır. Özel bağımsız değişkenler, **ContainerServiceArguments** altındaki **Barındırma** bölümünde yer alan küme bildiriminde belirtilir. Aşağıdaki kod parçacığında bir örnek gösterilmektedir: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric’te kapsayıcı](service-fabric-containers-overview.md) çalıştırma hakkında daha fazla bilgi edinin.
* [Kapsayıcı içinde .NET uygulaması dağıtma](service-fabric-host-app-in-a-container.md) öğreticisini okuyun.
* Service Fabric [uygulama yaşam döngüsü](service-fabric-application-lifecycle.md) hakkında bilgi edinin.
* GitHub’da [Service Fabric kapsayıcı kod örneklerine](https://github.com/Azure-Samples/service-fabric-containers) bakın.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
