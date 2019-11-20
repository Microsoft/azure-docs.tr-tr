---
title: Service Fabric Azure dosyaları birim sürücüsü (GA) | Microsoft Docs
description: Service Fabric, kapsayıcıınızdan birimleri yedeklemek için Azure dosyalarının kullanılmasını destekler. Bu, şu anda önizleme aşamasındadır.
services: service-fabric
author: athinanthny
manager: chackdan
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.topic: conceptual
ms.date: 6/10/2018
ms.author: atsenthi
ms.openlocfilehash: 70784e2c8c91d39c34ba503cc3ebfcf3469939d9
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013491"
---
# <a name="service-fabric-azure-files-volume-driver"></a>Service Fabric Azure dosyaları birim sürücüsü
Docker kapsayıcıları için [Azure dosya](/azure/storage/files/storage-files-introduction) tabanlı birimler sağlayan bir [Docker birimi](https://docs.docker.com/engine/extend/plugins_volume/) eklentisi artık **GA (genel kullanıma sunuldu)** .

Bu Docker birim eklentisi, Service Fabric kümelerine dağıtılabilen bir Service Fabric uygulaması olarak paketlenir. Amacı, kümeye dağıtılan diğer Service Fabric kapsayıcı uygulamaları için Azure dosya tabanlı birimler sağlamaktır.

> [!NOTE]
> Azure Files Volume eklentisinin sürümü 6.5.661.9590, GA (genel kullanıma sunuldu) bir sürümdür. 
>

## <a name="prerequisites"></a>Önkoşullar
* Azure Files Volume eklentisinin Windows sürümü yalnızca [Windows Server sürüm 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [windows 10 sürüm 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) veya sonraki işletim sistemlerinde kullanılabilir.

* Azure Files Volume eklentisinin Linux sürümü, Service Fabric tarafından desteklenen tüm işletim sistemi sürümlerinde kullanılabilir.

* Azure Files Volume eklentisi yalnızca Service Fabric sürüm 6,2 ve daha yeni sürümlerde kullanılabilir.

* Service Fabric kapsayıcı uygulamasının birim olarak kullanacağı bir dosya paylaşma oluşturmak için [Azure dosyaları belgelerindeki](/azure/storage/files/storage-how-to-create-file-share) yönergeleri izleyin.

* [PowerShell 'i Service Fabric modülü](/azure/service-fabric/service-fabric-get-started) veya [sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) yüklü olarak yüklemeniz gerekir.

* Hyper-V kapsayıcıları kullanıyorsanız, aşağıdaki kod parçacıklarının Azure Resource Manager şablonunuzda (Azure Cluster) veya ClusterConfig. JSON (tek başına küme) ClusterManifest (yerel küme) veya fabricSettings bölümüne eklenmesi gerekir.

ClusterManifest içinde, barındırma bölümünde aşağıdakilerin eklenmesi gerekir. Bu örnekte, birim adı **sfazurefile** ve kümede dinlediği bağlantı noktası **19100**' dir. Bunları kümeniz için doğru değerlerle değiştirin.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Azure Resource Manager şablonunuzun fabricSettings bölümünde (Azure dağıtımları için) veya ClusterConfig. JSON (tek başına dağıtımlar için), aşağıdaki kod parçacığının eklenmesi gerekir. Yeniden, birim adı ve bağlantı noktası değerlerini kendi değerlerinizle değiştirin.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Service Fabric Azure dosyaları uygulamasını dağıtma

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Belirtilen PowerShell betiği aracılığıyla Azure Resource Manager kullanma (önerilir)

Kümeniz Azure 'u temel alıyorsa, kullanım kolaylığı için Azure Resource Manager uygulama kaynak modeli kullanarak uygulamalara dağıtım yapmanızı ve kod olarak altyapı sürdürme modeline doğru geçiş yapmanıza yardımcı olması önerilir. Bu yaklaşım, Azure dosyaları birim sürücüsü için uygulama sürümünün izlenmesi gereksinimini ortadan kaldırır. Ayrıca, desteklenen her işletim sistemi için ayrı Azure Resource Manager şablonlarını korumanıza olanak sağlar. Betik, Azure dosyaları uygulamasının en son sürümünü dağıtmakta olduğunu varsayar ve işletim sistemi türü, küme aboneliği KIMLIĞI ve kaynak grubu için Parametreler alır. Betiği [Service Fabric indirme sitesinden](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)indirebilirsiniz. Bunun, Azure Files Volume eklentisinin, Docker Daemon 'dan gelen istekleri 19100 ' ye dinlediği bağlantı noktası olan ListenPort 'yi otomatik olarak ayarladığına unutmayın. Bunu, "listenPort" adlı parametre ekleyerek değiştirebilirsiniz. Bağlantı noktasının, kümenin veya uygulamalarınızın kullandığı diğer bağlantı noktaları ile çakışmadığından emin olun.
 

Windows için Azure Resource Manager dağıtım komutu:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Linux için Azure Resource Manager dağıtım komutu:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Betiği başarıyla çalıştırdıktan sonra, [uygulamanızı yapılandırma bölümüne atlayabilirsiniz.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Tek başına kümeler için el ile dağıtım

Kapsayıcılarınız için birimleri sağlayan Service Fabric uygulaması [Service Fabric indirme sitesinden](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.516.9494.zip)indirilebilir. Uygulama, [PowerShell](./service-fabric-deploy-remove-applications.md), [CLI](./service-fabric-application-lifecycle-sfctl.md) veya [FabricClient API 'leri](./service-fabric-deploy-remove-applications-fabricclient.md)aracılığıyla kümeye dağıtılabilir.

1. Komut satırını kullanarak, dizini indirilen uygulama paketinin kök dizinine değiştirin.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Daha sonra, [ApplicationPackagePath] ve [ımattotoreconnectionstring] için uygun değerlerle birlikte uygulama paketini görüntü deposuna kopyalayın:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Uygulama türünü kaydetme

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Bu uygulamayı oluşturun, **listenport** uygulama parametre değerine yakın bir uyarı ödeyerek. Bu değer, Azure Files Volume eklentisinin Docker Daemon 'dan istekleri dinlediği bağlantı noktasıdır. Uygulamaya girilen bağlantı noktasının ClusterManifest 'deki VolumePluginPorts ile eşleştiğinden ve kümenin veya uygulamalarınızın kullandığı diğer bağlantı noktalarıyla çakışmadığından emin olun.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter, kapsayıcılara SMB takmaları eşlemeyi desteklemez ([yalnızca Windows Server sürüm 1709 ' de desteklenir](/virtualization/windowscontainers/manage-containers/container-storage)). Bu kısıtlama, 1709 'den eski sürümlerde ağ birimi eşlemeyi ve Azure dosyaları birim sürücülerini engeller.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Uygulamayı yerel bir geliştirme kümesinde dağıtma
Yukarıdaki 1-3 adımlarını izleyin [.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Azure dosyaları toplu eklenti uygulaması için varsayılan hizmet örneği sayısı-1 ' dir. Bu, kümedeki her düğüme dağıtılan bir hizmetin bir örneği olduğu anlamına gelir. Ancak, Azure dosyaları toplu eklenti uygulamasını yerel bir geliştirme kümesine dağıttığınızda, hizmet örneği sayısı 1 olarak belirtilmelidir. Bu, **InstanceCount** uygulama parametresi aracılığıyla yapılabilir. Bu nedenle, yerel bir geliştirme kümesinde Azure dosyaları toplu eklenti uygulaması oluşturma komutu şunlardır:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Uygulamalarınızı birimi kullanacak şekilde yapılandırma
Aşağıdaki kod parçacığında, Azure dosya tabanlı bir birimin uygulamanızın uygulama bildirimi dosyasında nasıl belirtime gösterilmektedir. İlgilendiğiniz belirli öğe, **birim** etikettir:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure Files Volume eklentisinin sürücü adı **sfazurefile**' dir. Bu değer, uygulama bildiriminde **birim** etiketi öğesinin **sürücü** özniteliği için ayarlanır.

Yukarıdaki kod parçacığında bulunan **birim** etiketinde, Azure Files Volume eklentisi aşağıdaki öznitelikleri gerektirir:
- **Kaynak** -bu, birimin adıdır. Kullanıcı, birimi için herhangi bir ad seçebilir.
- **Hedef** -bu öznitelik, birimin çalışan kapsayıcı içinde eşlendiği konumdur. Bu nedenle, hedef kapsayıcıda zaten mevcut olan bir konum olamaz

Yukarıdaki kod parçacığında bulunan **Driveroption** öğelerinde gösterildiği gibi, Azure Files Volume eklentisi aşağıdaki sürücü seçeneklerini destekler:
- **PaylaşımAdı** -kapsayıcının birimini sağlayan Azure dosyaları dosya paylaşımının adı.
- **storageAccountName** -Azure dosyaları dosya paylaşımının bulunduğu Azure depolama hesabının adı.
- **Storageaccountkey** -Azure dosyaları dosya paylaşımının bulunduğu Azure depolama hesabı için erişim anahtarı.
- **Storageaccountfqdn** -depolama hesabıyla ilişkili etki alanı adı. StorageAccountFQDN belirtilmemişse, etki alanı adı storageAccountName ile varsayılan sonek (. File. Core. Windows. net) kullanılarak oluşturulur.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Kendi birim veya günlük sürücünüzü kullanma
Service Fabric Ayrıca kendi özel [biriminiz](https://docs.docker.com/engine/extend/plugins_volume/) veya [günlük](https://docs.docker.com/engine/admin/logging/overview/) sürücüleriniz kullanımına izin verir. Docker Volume/Logging sürücüsü kümede yüklü değilse, RDP/SSH protokollerini kullanarak el ile yükleyebilirsiniz. Bu protokollerle yüklemeyi, bir [sanal makine ölçek kümesi başlatma betiği](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) veya [setupentrypoint betiği](/azure/service-fabric/service-fabric-application-model)aracılığıyla gerçekleştirebilirsiniz.

[Azure Için Docker birim sürücüsünü](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) yüklemeye yönelik betiğe örnek olarak aşağıdaki gibidir:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Uygulamalarınızda, yüklediğiniz birim veya günlük sürücüsünü kullanmak için, uygulama bildiriminizde **Containerhostpolicies** altındaki **Volume** ve **LogConfig** öğelerinde uygun değerleri belirtmeniz gerekir.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Bir birim eklentisi belirtirken Service Fabric, belirtilen parametreleri kullanarak birimi otomatik olarak oluşturur. Volume öğesi için **kaynak** etiketi birimin adıdır ve **sürücü** etiketi birim sürücüsü eklentisini belirtir. **Hedef** etiket, **kaynağın** çalışan kapsayıcı içinde eşlendiği konumdur. Bu nedenle, hedef Kapsayıcınız içinde zaten bulunan bir konum olamaz. Seçenekler, aşağıdaki gibi **Driveroption** etiketi kullanılarak belirtilebilir:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Uygulama parametreleri, önceki bildirim parçacığında gösterildiği gibi birimlerde desteklenir (örneğin, bir kullanım `MyStorageVar` için arama yapın).

Bir Docker günlük sürücüsü belirtilmişse, kümedeki günlükleri işlemek için aracıları (veya kapsayıcıları) dağıtmanız gerekir. **Driveroption** etiketi, günlük sürücüsü seçeneklerini belirtmek için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
* Birim sürücüsü dahil olmak üzere kapsayıcı örneklerini görmek için lütfen [Service Fabric kapsayıcı örneklerini](https://github.com/Azure-Samples/service-fabric-containers) ziyaret edin
* Kapsayıcıları bir Service Fabric kümesine dağıtmak için [Service Fabric bir kapsayıcı dağıtma](service-fabric-deploy-container.md) makalesine başvurun
