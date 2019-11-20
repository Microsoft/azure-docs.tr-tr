---
title: Azure Service Fabric kapsayıcı uygulaması oluşturma | Microsoft Docs
description: Azure Service Fabric üzerinde ilk Windows kapsayıcı uygulamanızı oluşturun. Python uygulamasıyla bir Docker görüntüsü oluşturun, görüntüyü bir kapsayıcı kayıt defterine iletin, bir Service Fabric kapsayıcı uygulaması oluşturup dağıtın.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/25/2019
ms.author: atsenthi
ms.openlocfilehash: 4fd6de848756cedf21d7bb1f7f1be31175de6627
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838247"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Windows üzerinde ilk Service Fabric kapsayıcı uygulamanızı oluşturma

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Bir Service Fabric kümesindeki Windows kapsayıcısında mevcut olan bir uygulamayı çalıştırmak için uygulamanızda herhangi bir değişiklik yapılması gerekmez. Bu makalede, Python [Flask](http://flask.pocoo.org/) Web uygulaması Içeren bir Docker görüntüsü oluşturma ve bunu yerel makinenizde çalışan bir Service Fabric kümesine dağıtma işlemi adım adım açıklanmaktadır. Ayrıca, kapsayıcıya alınmış uygulamanızı [Azure Container Registry](/azure/container-registry/) aracılığıyla paylaşırsınız. Bu makale Docker hakkında temel bir anlayışınızın olduğunu varsayar. [Docker’a Genel Bakış](https://docs.docker.com/engine/understanding-docker/) makalesini okuyarak Docker hakkında bilgi edinebilirsiniz.

> [!NOTE]
> Bu makale Windows geliştirme ortamı için geçerlidir.  Service Fabric kümesi çalışma zamanının ve Docker çalışma zamanının aynı işletim sisteminde çalışıyor olması gerekir.  Windows kapsayıcılarını bir Linux kümesinde çalıştıramazsınız.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

* Şunları çalıştıran bir geliştirme bilgisayarı:
  * Visual Studio 2015 veya Visual Studio 2019.
  * [Service Fabric SDK’sı ve araçları](service-fabric-get-started.md).
  *  Windows için Docker. [Windows için Docker CE edinme (dengeli)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Docker’ı yükleyip başlattıktan sonra tepsi simgesine sağ tıklayıp **Windows kapsayıcılarına geç** öğesini seçin. Bu adım, Windows temelinde Docker görüntülerini çalıştırmak için gereklidir.

* Kapsayıcılarla Windows Server üzerinde çalışan üç veya daha fazla düğüme sahip bir Windows kümesi. 

  Bu makalede, Küme düğümleriniz üzerinde çalışan kapsayıcılarla Windows Server 'ın (derleme) sürümü, geliştirme makinmenizle aynı olmalıdır. Bunun nedeni, geliştirme makinenizde Docker görüntüsünü oluşturmanız ve kapsayıcı işletim sisteminin sürümleri ve dağıtıldığı ana bilgisayar IŞLETIM sistemi arasında uyumluluk kısıtlamaları vardır. Daha fazla bilgi için bkz. [Windows Server kapsayıcısı işletim sistemi ve konak işletim sistemi uyumluluğu](#windows-server-container-os-and-host-os-compatibility). 
  
Kümeniz için gerekli olan kapsayıcıları içeren Windows Server sürümünü öğrenmek için, geliştirme makinenizde bir Windows komut isteminden `ver` komutunu çalıştırın:

* Sürüm *x. x. 14323. x*içeriyorsa, [bir küme oluştururken](service-fabric-cluster-creation-via-portal.md), Işletim sistemi için *windowsserver 2016-Datacenter-with-containers* ' ı seçin.
  * Sürüm *x. x. 16299. x*içeriyorsa, [bir küme oluştururken](service-fabric-cluster-creation-via-portal.md)Işletim sistemi Için *Windowsserveraltı Datacenter-Core-1709--containers* ' ı seçin.

* Azure Container Registry’deki bir kayıt defteri - Azure aboneliğinizde [Kapsayıcı kayıt defteri oluşturun](../container-registry/container-registry-get-started-portal.md).

> [!NOTE]
> Windows 10 ' da çalışan bir Service Fabric kümesine kapsayıcı dağıtmak desteklenir.  Windows 10 ' un Windows kapsayıcıları 'nı çalıştırmak için nasıl yapılandırılacağı hakkında bilgi için [Bu makaleye](service-fabric-how-to-debug-windows-containers.md) bakın.
>   

> [!NOTE]
> Service Fabric sürümleri 6,2 ve üzeri, Windows Server sürüm 1709 ' de çalışan kümelere kapsayıcı dağıtımı desteği.  
> 

## <a name="define-the-docker-container"></a>Docker kapsayıcısını tanımlama

Docker Hub’ında bulunan [Python görüntüsünü](https://hub.docker.com/_/python/) temel alan bir görüntü oluşturun.

Bir Dockerfile içinde Docker kapsayıcınızı belirtin. Dockerfile, kapsayıcınızın içindeki ortamı ayarlama, çalıştırmak istediğiniz uygulamayı yükleme ve bağlantı noktalarını eşleme yönergelerinden oluşur. Dockerfile, görüntüyü oluşturan `docker build` komutunun girdisidir.

Boş bir dizin oluşturun ve *Dockerfile* dosyasını oluşturun (dosya uzantısı kullanmayın). Aşağıdakini *Dockerfile* dosyasına ekleyin ve değişikliklerinizi kaydedin:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Daha fazla bilgi için [Dockerfile başvurusunu](https://docs.docker.com/engine/reference/builder/) okuyun.

## <a name="create-a-basic-web-application"></a>Temel bir web uygulaması oluşturma
Bağlantı noktası 80 üzerinden dinleyen ve `Hello World!` döndüren bir Flask web uygulaması oluşturun. Aynı dizinde, *requirements.txt* dosyasını oluşturun. Aşağıdakini dosyaya ekleyin ve değişikliklerinizi kaydedin:
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

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Görüntü oluşturma
Web uygulamanızı çalıştıran görüntüyü oluşturmak için `docker build` komutunu çalıştırın. PowerShell penceresini açın ve Dockerfile dosyasını içeren dizine gidin. Şu komutu çalıştırın:

```
docker build -t helloworldapp .
```

Bu komut Dockerfile içindeki yönergeleri kullanarak yeni görüntüyü oluşturur ve `helloworldapp` olarak adlandırır (-t etiketi). Bir kapsayıcı görüntüsü oluşturmak için, önce temel görüntü uygulamanın eklendiği Docker Hub’ından indirilir. 

Oluşturma komutu tamamlandıktan sonra, yeni görüntü üzerindeki bilgileri görmek için `docker images` komutunu çalıştırın:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma
Görüntüyü kapsayıcı kayıt defterine göndermeden önce yerel olarak doğrulayın. 

Uygulamayı çalıştırın:

```
docker run -d --name my-web-site helloworldapp
```

*name*, çalışan kapsayıcıya bir ad verir (kapsayıcı kimliği yerine).

Kapsayıcı başladıktan sonra çalışan kapsayıcınıza bir tarayıcıdan bağlanabilmek için IP adresini bulun:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Bu komut herhangi bir şey döndürmezse aşağıdaki komutu çalıştırın ve IP adresi için **Ağ Ayarları**->**Ağlar** öğesini inceleyin:
```
docker inspect my-web-site
```

Çalışan kapsayıcıya bağlanın. Döndürülen IP adresini işaret eden bir Web tarayıcısı açın (örneğin, "http:\//172.31.194.61"). "Hello World!" başlığının tarayıcıda gösterildiğini görürsünüz.

Kapsayıcınızı durdurmak için şu komutu çalıştırın:

```
docker stop my-web-site
```

Kapsayıcıyı geliştirme makinenizden silin:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Görüntüyü kapsayıcı kayıt defterine gönderme

Kapsayıcının geliştirme makinenizde çalıştığını doğruladıktan sonra, görüntüyü Azure Container Registry içindeki kayıt defterinize gönderin.

[Kayıt defteri kimlik bilgilerinizle](../container-registry/container-registry-authentication.md), kapsayıcı kayıt defterinizde oturum açmak için ``docker login`` çalıştırın.

Aşağıdaki örnekte, bir Azure Active Directory [hizmet sorumlusunun](../active-directory/develop/app-objects-and-service-principals.md) kimliği ve parolası geçirilmiştir. Örneğin, bir otomasyon senaryosu için kayıt defterinize bir hizmet sorumlusu atamış olabilirsiniz. Ya da kayıt defteri Kullanıcı adınızı ve parolanızı kullanarak oturum açmanız gerekir.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Aşağıdaki komut, görüntünün kayıt defterinize ait tam yolu içeren bir etiketini veya diğer adını oluşturur. Bu örnek, kayıt defterinin kökünde dağınıklığı önlemek için ```samples``` ad alanına görüntüyü yerleştirir.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Görüntüyü kapsayıcı kayıt defterinize gönderin:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Visual Studio’da kapsayıcıya alınmış hizmet oluşturma
Service Fabric SDK’sı ve araçları, kapsayıcıya alınmış uygulamalar oluşturmanıza yardımcı olan bir hizmet şablonu sağlar.

1. Visual Studio’yu çalıştırın. **Dosya** > **Yeni** > **Proje**’yi seçin.
2. **Service Fabric uygulaması**’nı seçin, "MyFirstContainer" olarak adlandırın ve **Tamam**’a tıklayın.
3. **Hizmet şablonları** listesinden **Kapsayıcı**’yı seçin.
4. **Görüntü Adı** alanına, kapsayıcı deponuza gönderdiğiniz görüntünün dizini olan "myregistry.azurecr.io/samples/helloworldapp" değerini girin.
5. Hizmetinize bir ad verin ve **Tamam**’a tıklayın.

## <a name="configure-communication"></a>İletişimi yapılandırma
Kapsayıcıya alınmış hizmetin iletişim sağlayabilmesi için bir uç nokta gerekir. ServiceManifest.xml dosyasına protokol, bağlantı noktası ve tür bilgileriyle bir `Endpoint` öğesi ekleyin. Bu örnekte, 8081 numaralı sabit bağlantı noktası kullanılır. Hiçbir bağlantı noktası belirtilmemişse, uygulama bağlantı noktası aralığından rastgele bir bağlantı noktası seçilir. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Geçerli özellik değerleriyle ek uç nokta öğeleri bildirerek, bir hizmet için ek uç noktalar eklenebilir. Her bağlantı noktası yalnızca bir protokol değeri bildirebilir.

Uç nokta tanımlandığında, Service Fabric uç noktayı Adlandırma hizmetinde yayımlar. Kümede çalışan diğer hizmetler bu kapsayıcıyı çözümleyebilir. Ayrıca, [ters proxy](service-fabric-reverseproxy.md)’yi kullanarak kapsayıcıdan kapsayıcıya iletişim kurabilirsiniz. İletişim, ters proxy’nin HTTP dinleme bağlantı noktasını ve iletişim kurmak istediğiniz hizmetlerin adlarının ortam değişkenleri olarak sağlanmasıyla gerçekleştirilir.

Hizmet belirli bir bağlantı noktasında (bu örnekte 8081) dinliyor. Uygulama Azure'daki bir kümeye dağıtıldığında hem küme hem de uygulama bir Azure yük dengeleyicinin arkasında çalışır. Gelen trafiğin hizmete ulaşabilmesi için Azure yük dengeleyicide uygulama bağlantı noktası açık olmalıdır.  Azure yük dengeleyicide bu bağlantı noktasını bir [PowerShell betiği](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) kullanarak veya [Azure portalından](https://portal.azure.com) açabilirsiniz.

## <a name="configure-and-set-environment-variables"></a>Ortam değişkenlerini yapılandırma ve ayarlama
Ortam değişkenleri, hizmet bildirimindeki her kod paketi için belirtilebilir. Bu özellik, kapsayıcı olarak mı dağıtıldıklarına yoksa konuk yürütülebilir dosyası olarak mı işlendiklerine bakılmaksızın tüm hizmetlerde sağlanır. Ortam değişkeni değerlerini, uygulama bildiriminde geçersiz kılabilir veya dağıtım sırasında uygulama parametresi olarak belirtebilirsiniz.

Aşağıdaki hizmet bildirimi XML kod parçacığı, kod paketi için ortam değişkenlerinin nasıl belirtileceğini gösteren bir örnektir:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Bu ortam değişkenleri, uygulama bildiriminde geçersiz kılınabilir:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Kapsayıcı bağlantı noktasından konak bağlantı noktasına eşlemeyi ve kapsayıcıdan kapsayıcıya keşfi yapılandırma
Kapsayıcıyla iletişim kurmak için kullanılan konak bağlantı noktasını yapılandırın. Bağlantı noktası bağlama, hizmetin kapsayıcı içinde dinlediği bağlantı noktasını konaktaki bağlantı noktasıyla eşler. ApplicationManifest.xml dosyasının `PortBinding` öğesine bir `ContainerHostPolicies` öğesi ekleyin. Bu makalede `ContainerPort` değeri 80 (Dockerfile içinde belirtildiği gibi kapsayıcı 80 numaralı bağlantı noktasını gösterir) ve `EndpointRef` değeri "Guest1TypeEndpoint" (hizmet bildiriminde daha önce tanımlanmış olan uç nokta) olarak verilir. 8081 numaralı bağlantı noktasında hizmete gelen istekler, kapsayıcı üzerindeki 80 numaralı bağlantı noktasıyla eşlenir.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> Geçerli özellik değerleriyle ek PortBinding öğeleri bildirerek, bir hizmet için ek PortBindings eklenebilir.

## <a name="configure-container-repository-authentication"></a>Kapsayıcı deposu kimlik doğrulamasını yapılandırma

Kapsayıcı görüntüsü indirme için farklı kimlik doğrulama türlerini nasıl yapılandıracağınızı öğrenmek için bkz. [kapsayıcı deposu kimlik doğrulaması](configure-container-repository-credentials.md).

## <a name="configure-isolation-mode"></a>Yalıtım modunu yapılandırma
Windows, kapsayıcılar için iki yalıtım modunu destekler: İşlem ve Hyper-V. İşlem yalıtım moduyla, aynı konak makinesinde çalışan tüm kapsayıcılar çekirdeği konakla paylaşır. Hyper-V yalıtım moduyla, çekirdekler her Hyper-V kapsayıcısı ile kapsayıcı konağı arasında yalıtılır. Yalıtım modu, uygulama bildirimi dosyasında bulunan `ContainerHostPolicies` öğesinde belirtilir. Belirtilebilen yalıtım modları `process`, `hyperv` ve `default` modlarıdır. Varsayılan değer Windows Server konaklarındaki işlem yalıtım modudur. Windows 10 konakları üzerinde yalnızca Hyper-V yalıtım modu desteklenir, bu nedenle kapsayıcı, yalıtım modu ayarından bağımsız olarak Hyper-V yalıtım modunda çalışır. Aşağıdaki kod parçacığı uygulama bildirimi dosyasında yalıtım modunun nasıl belirtildiğini gösterir.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Hyperv yalıtım modu, iç içe sanallaştırma desteğine sahip Ev3 ve Dv3 Azure SKU’ları üzerinde kullanılabilir. 
   >
   >

## <a name="configure-resource-governance"></a>Kaynak idaresini yapılandırma
[Kaynak idaresi](service-fabric-resource-governance.md) kapsayıcının konakta kullanabildiği kaynakları kısıtlar. Uygulama bildiriminde belirtilen `ResourceGovernancePolicy` öğesi, hizmet kod paketinin kaynak sınırlarını tanımlamak için kullanılır. Şu kaynaklar için kaynak sınırları ayarlanabilir: Memory, MemorySwap, CpuShares (CPU göreli ağırlığı), MemoryReservationInMB, BlkioWeight (BlockIO göreli ağırlığı). Bu örnekte, Guest1Pkg hizmet paketi bulunduğu küme düğümlerinde bir çekirdek alır. Bellek sınırları mutlaktır; dolayısıyla, kod paketi 1024 MB bellekle (aynı genel garantili ayırmayla) sınırlıdır. Kod paketleri (kapsayıcılar veya işlemler) bu sınırı aşan miktarda bellek ayıramazlar ve bunu denediklerinde yetersiz bellek özel durumu ortaya çıkar. Kaynak sınırı zorlamasının çalışması için, hizmet paketi içindeki tüm kod paketlerinin bellek sınırlarının belirtilmiş olması gerekir.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK ayarlarını yapılandırma 

Service Fabric, v6.1 sürümünden itibaren [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) olaylarını otomatik olarak sistem durumu raporuyla tümleştirir. Bu, kapsayıcınızda **HEALTHCHECK** özelliği etkinse kapsayıcının sistem durumuna ilişkin Docker tarafından bildirilen her değişiklik için Service Fabric’in durumu bildireceği anlamına gelir. **health_status** özelliği [healthy](service-fabric-visualizing-your-cluster.md) olduğunda *Service Fabric Explorer*’da *OK* şeklinde bir durum raporu görüntülenirken, **health_status** özelliği *unhealthy* olduğunda *WARNING* görünür. 

En son v 6.4 yenileme sürümü ile başlayarak, Docker HEALTHCHECK değerlendirmelerinin hata olarak bildirilmesi gerektiğini belirtme seçeneğiniz vardır. Bu seçenek etkinleştirilirse, *health_status* *sağlıklı* olduğunda bir **Tamam** sistem durumu raporu görüntülenir ve *health_status* *sağlıksız*olduğunda **hata** görüntülenir.

Kapsayıcı durumunun izlenmesi için gerçekleştirilen gerçek denetimi gösteren **HEALTHCHECK** yönergesi, kapsayıcı görüntüsü oluşturulurken kullanılan Dockerfile dosyasında mevcut olmalıdır.

![HealthCheckHealthy][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

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
Varsayılan olarak, *ıncludedockerhealthstatusınsystemhealthreport* **true**olarak ayarlanır, *RestartContainerOnUnhealthyDockerHealthStatus* **false**olarak ayarlanır ve *treatcontainerunhealthyıstatusaserror* **false**olarak ayarlanır. 

*RestartContainerOnUnhealthyDockerHealthStatus* özelliği **true** olarak ayarlanırsa, tekrarlanan şekilde durumunun iyi olmadığı bildirilen kapsayıcılar yeniden başlatılır (muhtemelen diğer düğümlerde).

*Treatcontainerunhealthystatusaserror* **değeri true**olarak ayarlanırsa, kapsayıcının *health_status* *sağlıksız*olduğunda **hata** durumu raporları görüntülenir.

Tüm Service Fabric kümesi için **HEALTHCHECK** tümleştirmesini devre dışı bırakmak istiyorsanız [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) özelliğini **false** olarak ayarlamanız gerekir.

## <a name="deploy-the-container-application"></a>Kapsayıcı uygulamasını dağıtma
Tüm değişikliklerinizi kaydedin ve uygulamayı derleyin. Uygulamanızı yayımlamak için Çözüm Gezgini’nde **MyFirstContainer**’a sağ tıklayın ve **Yayımla**’yı seçin.

**Bağlantı Uç Noktası**’nda kümenin yönetim uç noktasını girin. Örneğin, `containercluster.westus2.cloudapp.azure.com:19000`. İstemci bağlantı uç noktasını [Azure portalında](https://portal.azure.com) kümenizin Genel Bakış sekmesinde bulabilirsiniz.

**Yayımla**’ta tıklayın.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), bir Service Fabric kümesindeki uygulama ve düğümleri inceleyip yönetmeye yönelik web tabanlı bir araçtır. Bir tarayıcı penceresi açıp `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/` konumuna gidin ve uygulama dağıtımını izleyin. Uygulama dağıtılır, ancak görüntü küme düğümlerine yüklenene kadar hatalı durumdadır (bu işlem, görüntü boyutuna bağlı olarak biraz zaman alabilir): ![Hata][1]

Uygulamanın ```Ready``` durumu ![Hazır][2] olduğunda uygulama hazırdır

Bir tarayıcıyı açın ve `http://containercluster.westus2.cloudapp.azure.com:8081` dizinine gidin. "Hello World!" başlığının tarayıcıda gösterildiğini görürsünüz.

## <a name="clean-up"></a>Temizleme

Küme çalışırken size ücret yansımaya devam edebilir, bu nedenle [kümenizi silmeyi](service-fabric-cluster-delete.md) düşünün.

Görüntüyü kapsayıcı kayıt defterine gönderdikten sonra, yerel görüntüyü geliştirme bilgisayarınızdan silebilirsiniz:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Windows Server kapsayıcısı işletim sistemi ve konak işletim sistemi uyumluluğu

Windows Server kapsayıcıları, bir konak işletim sisteminin tüm sürümleri arasında uyumlu değildir. Örneğin:
 
- Windows Server sürüm 1709 kullanılarak oluşturulan Windows Server kapsayıcıları, Windows Server sürüm 2016 çalıştıran bir konakta çalışmıyor. 
- Windows Server 2016 kullanılarak oluşturulan Windows Server kapsayıcıları, Hyper-V yalıtım modunda yalnızca Windows Server sürüm 1709 çalıştıran bir konakta çalışır. 
- Windows Server 2016 kullanılarak oluşturulan Windows Server kapsayıcılarıyla, kapsayıcı işletim sistemi ve ana bilgisayar işletim sistemi düzeltmesinin Windows Server 2016 çalıştıran bir konakta işlem yalıtım modunda çalışırken aynı olduğundan emin olmak gerekebilir.
 
Daha fazla bilgi için bkz. [Windows kapsayıcı sürümü uyumluluğu](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Service Fabric kümenize kapsayıcı oluştururken ve dağıttığınızda konak IŞLETIM sisteminin ve kapsayıcı işletim sisteminin uyumluluğunu göz önünde bulundurun. Örneğin:

- Küme düğümlerinizin işletim sistemi ile uyumlu bir işletim sistemi ile kapsayıcıları dağıttığınızdan emin olun.
- Kapsayıcı uygulamanız için belirtilen yalıtım modunun, dağıtıldığı düğümde kapsayıcı işletim sistemi desteğiyle tutarlı olduğundan emin olun.
- Küme düğümleriniz veya kapsayıcılarınıza işletim sistemi yükseltmelerinin uyumluluğunu nasıl etkileyebileceğini göz önünde bulundurun. 

Kapsayıcıların Service Fabric kümenize doğru şekilde dağıtıldığından emin olmak için aşağıdaki yöntemleri öneririz:

- Bir kapsayıcının oluşturulduğu Windows Server işletim sistemi sürümünü belirtmek için Docker görüntüleriniz ile açık görüntü etiketleme kullanın. 
- Uygulamanızın farklı Windows Server sürümleri ve yükseltmeleri arasında uyumlu olduğundan emin olmak için uygulama bildirimi dosyanızda [Işletim sistemi etiketlemesi](#specify-os-build-specific-container-images) kullanın.

> [!NOTE]
> Service Fabric sürüm 6,2 ve üzeri ile Windows Server 2016 tabanlı kapsayıcıları bir Windows 10 konağında yerel olarak dağıtabilirsiniz. Windows 10 ' da kapsayıcılar, uygulama bildiriminde ayarlanan yalıtım modundan bağımsız olarak Hyper-V yalıtım modunda çalışır. Daha fazla bilgi için bkz. [yalıtım modunu yapılandırma](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>İşletim sistemi derlemesine özgü kapsayıcı görüntüleri belirtme 

Windows Server kapsayıcıları, işletim sisteminin farklı sürümleri arasında uyumlu olmayabilir. Örneğin, Windows Server 2016 kullanılarak oluşturulan Windows Server kapsayıcıları, işlem yalıtım modunda Windows Server sürüm 1709 ' de çalışmaz. Bu nedenle, küme düğümleri en son sürüme güncelleniyorsa, işletim sisteminin önceki sürümleri kullanılarak oluşturulan kapsayıcı hizmetleri başarısız olabilir. Bunu, çalışma zamanının sürüm 6,1 ve daha yeni bir sürümü ile aşmak için, kapsayıcı başına birden çok işletim sistemi görüntüsü belirtmeyi ve uygulama bildiriminde işletim sisteminin yapı sürümleriyle etiketlemeyi destekler Service Fabric. Windows komut isteminde `winver` çalıştırarak işletim sisteminin yapı sürümünü edinebilirsiniz. Düğümlerde işletim sistemini güncelleştirmeden önce uygulama bildirimlerini güncelleştirin ve işletim sistemi başına görüntü geçersiz kılmalarını belirtin. Aşağıdaki kod parçacığında, **ApplicationManifest.xml** adlı uygulama bildiriminde nasıl birden çok kapsayıcı görüntüsü belirtileceği gösterilmektedir:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
WIndows Server 2016 için derleme sürümü 14393, Windows Server 1709 sürümü için derleme sürümü 16299’dur. Aşağıda gösterildiği gibi, hizmet bildiriminde kapsayıcı hizmeti başına tek bir görüntü belirtilmeye devam edilir:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > İşletim sistemi derleme sürümü etiketleme özellikleri yalnızca Windows’da Service Fabric için sunulmaktadır
   >

VM’deki temel işletim sistemi derleme 16299 (sürüm 1709) ise Service Fabric bu Windows Server sürümüne karşılık gelen kapsayıcı görüntüsünü seçer. Uygulama bildiriminde etiketli kapsayıcı görüntülerinin yanı sıra etiketsiz bir kapsayıcı görüntüsü de sağlanırsa, Service Fabric etiketsiz görüntüyü farklı sürümlerde çalışan bir görüntü olarak işler. Yükseltmeler sırasında sorunlardan kaçınmak için kapsayıcı görüntülerini açıkça etiketleyin.

Etiketlenmemiş kapsayıcı görüntüsü, ServiceManifest’te sağlanan görüntü için geçersiz kılma işlevi görür. Yani “myregistry.azurecr.io/samples/helloworldappDefault” görüntüsü ServiceManifest’teki “myregistry.azurecr.io/samples/helloworldapp” ImageName’i geçersiz kılar.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Tam Service Fabric uygulaması ve hizmet bildirimleri örneği
Bu makalede kullanılan tam hizmet ve uygulama bildirimleri aşağıda verilmiştir.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Kapsayıcı zorla sonlandırılmadan önceki zaman aralığını yapılandırın

Hizmet silme (veya başka bir düğüme taşıma) başladıktan sonra, çalışma zamanının kapsayıcı kaldırılmadan önce ne kadar bekleyeceğine ilişkin bir zaman aralığı yapılandırabilirsiniz. Zaman aralığını yapılandırma, kapsayıcıya `docker stop <time in seconds>` komutunu gönderir.  Daha ayrıntılı bilgi için bkz. [docker durdurma](https://docs.docker.com/engine/reference/commandline/stop/). Beklenecek zaman aralığı, `Hosting` bölümünde belirtilir. `Hosting` bölümü, bir yapılandırma yükseltmesinde küme oluşturmaya veya daha sonra eklenebilir. Aşağıdaki küme bildirimi kod parçacığı, bekleme aralığının nasıl ayarlandığını gösterir:

```json
"fabricSettings": [
    ...,
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
]
```
Varsayılan zaman aralığı 10 saniye olarak ayarlanır. Bu yapılandırma dinamik olduğundan, kümedeki yalnızca yapılandırmaya yönelik bir güncelleştirme zaman aşımını güncelleştirir. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Kullanılmayan kapsayıcı görüntülerini kaldırmak için çalışma zamanını yapılandırma

Service Fabric kümesini kullanılmayan kapsayıcı görüntülerini düğümden kaldıracak şekilde yapılandırabilirsiniz. Bu yapılandırma, düğümde çok fazla kapsayıcı görüntüsü varsa yeniden disk alanı elde edilmesine imkan tanır. Bu özelliği etkinleştirmek için aşağıdaki kod parçacığında gösterildiği gibi küme bildirimindeki [barındırma](service-fabric-cluster-fabric-settings.md#hosting) bölümünü güncelleştirin: 


```json
"fabricSettings": [
    ...,
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
]
```

Silinmemesi gereken görüntüleri `ContainerImagesToSkip` parametresi altında belirtebilirsiniz.  


## <a name="configure-container-image-download-time"></a>Kapsayıcı görüntüsü indirme süresini yapılandırma

Service Fabric çalışma zaman, kapsayıcı görüntülerinin indirilip ayıklanması için 20 dakika ayırır ve bu süre çoğu kapsayıcı görüntüsü için yeterlidir. Görüntüler büyükse veya ağ bağlantısı yavaşsa görüntü indirme ve ayıklama işlemi iptal edilmeden önce beklenecek sürenin artırılması gerekebilir. Zaman aşımı, aşağıdaki kod parçacığında gösterildiği gibi küme bildiriminin **Barındırma** bölümündeki **ContainerImageDownloadTimeout** özniteliği kullanılarak ayarlanabilir:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Kapsayıcı bekletme ilkesi ayarlama

Service Fabric (6.1 veya üzeri sürümler), kapsayıcı başlatma hatalarının tanılanmasına yardımcı olmak için sonlandırılan veya başlatılamayan kapsayıcıların bekletilmesini destekler. Bu ilke, aşağıdaki kod parçacığında gösterildiği gibi **ApplicationManifest.xml** dosyasında ayarlanabilir:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** ayarı, başarısız olduğunda bekletilecek kapsayıcı sayısını belirtir. Negatif bir değer belirtilirse başarısız olan tüm kapsayıcılar bekletilir. **ContainersRetentionCount** özniteliği belirtilmezse hiçbir kapsayıcı bekletilmez. **ContainersRetentionCount** özniteliği, kullanıcıların test ve üretim kümeleri için farklı değerler belirtebilmesi amacıyla Uygulama Parametrelerini destekler. Kapsayıcı hizmetinin diğer düğümlere taşınmasını önlemek için bu özellikler kullanılırken kapsayıcı hizmetinin belirli bir düğümü hedeflemesini sağlamak için yerleştirme kısıtlamaları kullanın. Bu özellik kullanılarak bekletilen tüm kapsayıcılar el ile kaldırılmalıdır.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Docker cinini özel bağımsız değişkenlerle başlatma

Service Fabric çalışma zamanı 6.2 sürümü ve üzeriyle, Docker cinini özel bağımsız değişkenler kullanarak başlatabilirsiniz. Özel bağımsız değişkenler belirtildiğinde, Service Fabric `--pidfile` bağımsız değişkeni hariç hiçbir bağımsız değişkeni Docker altyapısına geçirmez. Bu nedenle, `--pidfile` bir bağımsız değişken olarak geçirilmemelidir. Ayrıca, Service Fabric’in Daemon ile iletişim kurması için bağımsız değişken docker’ın Windows’da varsayılan ad kanalında (veya Linux’ta unix etki alanı yuvasında) dinlemeye devam etmesini sağlamalıdır. Özel bağımsız değişkenler, aşağıdaki kod parçacığında gösterildiği gibi **ContainerServiceArguments** altındaki **Barındırma** bölümü altındaki küme bildiriminde geçirilir: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric’te kapsayıcı](service-fabric-containers-overview.md) çalıştırma hakkında daha fazla bilgi edinin.
* [Kapsayıcı içinde .NET uygulaması dağıtma](service-fabric-host-app-in-a-container.md) öğreticisini okuyun.
* Service Fabric [uygulama yaşam döngüsü](service-fabric-application-lifecycle.md) hakkında bilgi edinin.
* GitHub’da [Service Fabric kapsayıcı kod örneklerine](https://github.com/Azure-Samples/service-fabric-containers) bakın.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
