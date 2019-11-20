---
title: Service Fabric hizmet uç noktalarını belirtme | Microsoft Docs
description: HTTPS uç noktalarını ayarlama dahil olmak üzere bir hizmet bildiriminde uç nokta kaynaklarını açıklama
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: bb3fd77df60be68408fceea683ee4b8b74d77427
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242921"
---
# <a name="specify-resources-in-a-service-manifest"></a>Bir hizmet bildiriminde kaynakları belirtme
## <a name="overview"></a>Genel Bakış
Hizmet bildirimi, derlenmiş kod değiştirilmeden, hizmet tarafından kullanılan kaynakların bildirilmesini/değiştirilmesini sağlar. Azure Service Fabric, hizmet için uç nokta kaynaklarının yapılandırılmasını destekler. Hizmet bildiriminde belirtilen kaynaklara erişimi, uygulama bildirimindeki SecurityGroup aracılığıyla denetlenebilir. Kaynak bildirimi, bu kaynakların dağıtım zamanında değiştirilmesini sağlar, yani hizmetin yeni bir yapılandırma mekanizması tanıtılmasına gerek kalmaz. ServiceManifest. xml dosyası için şema tanımı, Service Fabric SDK ve araçlar ile birlikte *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*ile yüklenir.

## <a name="endpoints"></a>Uç Noktalar
Hizmet bildiriminde bir uç nokta kaynağı tanımlandığında Service Fabric, bir bağlantı noktası açıkça belirtilmediğinde, ayrılmış uygulama bağlantı noktası aralığından bağlantı noktaları atar. Örneğin, bu paragraftan sonra sunulan bildirim parçacığında belirtilen uç nokta *ServiceEndpoint1* bakın. Ayrıca, hizmetler bir kaynakta belirli bir bağlantı noktası da talep edebilir. Farklı küme düğümlerinde çalışan hizmet Çoğaltmalarından farklı bağlantı noktası numaraları atanabilir, ancak aynı düğümde çalışan bir hizmetin çoğaltmaları bağlantı noktasını paylaşır. Hizmet çoğaltmaları daha sonra bu bağlantı noktalarını, çoğaltma ve istemci isteklerini dinlemek için gereken şekilde kullanabilir.

> [!WARNING] 
> Tasarım statik bağlantı noktaları, kümele bildiriminde belirtilen uygulama bağlantı noktası aralığıyla çakışmamalıdır. Statik bir bağlantı noktası belirtirseniz, uygulamayı uygulama bağlantı noktası aralığı dışında atarsanız, bağlantı noktası çakışmalarına neden olur. Sürüm 6.5 CU2 UYGULAMAZSANıZ, bu tür bir çakışmayı tespit ettiğimiz ancak dağıtımın sevk edilen 6,5 davranışı ile eşitlenmiş halde devam etmesine izin veren bir **sistem durumu uyarısı** vereceğiz. Ancak, uygulama dağıtımını bir sonraki Ana sürümlerden önleyebiliriz.
>
> Sürüm 7,0 ile, uygulama bağlantı noktası aralığı kullanımı, HostingConfig:: ApplicationPortExhaustThresholdPercentage (varsayılan 80%) ötesine geçtiğinde bir **sistem durumu uyarısı** yayınlarız.
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Tek bir hizmet paketinde birden çok kod paketi varsa, kod paketinin **uç noktalar** bölümünde de başvurulması gerekir.  Örneğin, **ServiceEndpoint2a** ve **ServiceEndpoint2b** , farklı kod paketlerine başvuran aynı hizmet paketinden gelen noktalardır, her bir uç noktaya karşılık gelen kod paketi aşağıdaki gibi açıklığa kavuşturuldu:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Yapılandırma paketi ayarları dosyasından (Settings. xml) bulunan uç noktalar hakkında daha fazla bilgi edinmek için [durum bilgisi olan Reliable Services yapılandırma](service-fabric-reliable-services-configuration.md) konusuna bakın.

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Örnek: hizmetiniz için bir HTTP uç noktası belirtme
Aşağıdaki hizmet bildirimi &lt;Resources&gt; öğesinde bir TCP uç noktası kaynağı ve iki HTTP uç noktası kaynağı tanımlar.

HTTP uç noktaları, Service Fabric tarafından otomatik olarak ACL ' dir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Örnek: hizmetiniz için bir HTTPS uç noktası belirtme
HTTPS protokolü sunucu kimlik doğrulaması sağlar ve aynı zamanda istemci-sunucu iletişimini şifrelemek için de kullanılır. Service Fabric hizmetinizde HTTPS 'yi etkinleştirmek için, *ServiceEndpoint3*uç noktası için daha önce gösterildiği gibi hizmet bildiriminin *Kaynaklar-> uç noktaları-> uç noktası* bölümünde Protokolü belirtin.

> [!NOTE]
> Uygulamanın yükseltilmesi sırasında bir hizmetin Protokolü değiştirilemez. Yükseltme sırasında değiştirilmişse, bu bir değişiklik değildir.
> 

> [!WARNING] 
> HTTPS kullanırken, aynı düğüme dağıtılan farklı hizmet örnekleri (uygulamadan bağımsız) için aynı bağlantı noktasını ve sertifikayı kullanmayın. Farklı uygulama örneklerinde aynı bağlantı noktasını kullanarak iki farklı hizmetin yükseltilmesi, yükseltme hatasına neden olur. Daha fazla bilgi için bkz. [https uç noktaları ile birden çok uygulamayı yükseltme ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

HTTPS için ayarlamanız gereken örnek bir ApplicationManifest aşağıda verilmiştir. Sertifikanızın parmak izi sağlanmalıdır. EndpointRef, HTTPS protokolünü ayarladığınız ServiceManifest içindeki EndpointResource öğesine bir başvurudur. Birden fazla EndpointCertificate ekleyebilirsiniz.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Linux kümeleri için **My mağazam** , **/var/lib/sfcerts**klasörünü varsayılan olarak alır.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>ServiceManifest. xml dosyasında uç noktaları geçersiz kılma

ApplicationManifest içinde, bir Resourcekılmalar ekleyin bölümü, bu, Configkıkılatıon için eşdüzey bir bölüm olacaktır. Bu bölümde, hizmet bildiriminde belirtilen kaynaklar bölümündeki uç noktalar bölümünde geçersiz kılma seçeneğini belirtebilirsiniz. Çalışma zamanı 5.7.217/SDK 2.7.217 ve üzeri için uç noktaların geçersiz kılınması desteklenir.

ApplicationParameters kullanılarak ServiceManifest içindeki uç noktayı geçersiz kılmak için ApplicationManifest öğesini aşağıdaki gibi değiştirin:

Servicemanifestımport bölümünde yeni bir "Resourcekılmalar" bölümü ekleyin.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Parametreleri aşağıda ekleyin:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Uygulamayı dağıttığınızda, bu değerleri ApplicationParameters olarak geçirebilirsiniz.  Örnek:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Note: ApplicationParameters için değerler boşsa, karşılık gelen EndPointName için ServiceManifest içinde belirtilen varsayılan değere geri döneceğiz.

Örnek:

Belirttiğiniz hizmet bildiriminde

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Ve uygulama parametreleri için PORT1 ve Protocol1 değeri null ya da boş. Bağlantı noktası hala ServiceFabric tarafından kararmaya devam etmektedir. Ve protokol TCP olur.

Yanlış bir değer belirtdiğinizi varsayın. Bağlantı noktası gibi bir int yerine "foo" dize değerini belirttiniz.  New-ServiceFabricApplication komutu bir hata ile başarısız olur: ' Resourceoverride ' bölümündeki ' ServiceEndpoint1 ' özniteliği ' PORT1 ' adlı geçersiz kılma parametresi geçersiz. Belirtilen değer ' foo ' ve gerekli ' int '.
