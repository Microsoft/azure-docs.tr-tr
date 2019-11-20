---
title: Azure Service Fabric kapsayıcısı uygulama bildirimi örnekleri
description: Çok Kapsayıcılı Service Fabric bir uygulama için uygulama ve hizmet bildirimi ayarlarını yapılandırmayı öğrenin.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 2d79f7dbf492b9473bcff147891df308674a8cf0
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013240"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Birden çok kapsayıcılı uygulama ve hizmet bildirimi örnekleri
Aşağıda, çok Kapsayıcılı Service Fabric bir uygulama için uygulama ve hizmet bildirimlerinin örnekleri verilmiştir. Bu örneklerin amacı, hangi ayarların kullanılabildiğini ve bunların nasıl kullanılacağını gösterir. Bu uygulama ve hizmet bildirimleri [Windows Server 2016 kapsayıcı örnek](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) bildirimlerini temel alır.

Aşağıdaki özellikler gösteriliyor:

|Bildirim|Özellikler|
|---|---|
|[Uygulama bildirimi](#application-manifest)| [ortam değişkenlerini geçersiz kılma](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [kapsayıcı bağlantı noktasından konağa eşleme](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [kapsayıcı kayıt defteri kimlik doğrulamasını yapılandırma](service-fabric-get-started-containers.md#configure-container-repository-authentication), [kaynak](service-fabric-resource-governance.md)yönetimi, [yalıtım modunu](service-fabric-get-started-containers.md#configure-isolation-mode)yapılandırma, [işletim sistemi oluşturmaya özgü kapsayıcı görüntülerini belirleme](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService hizmet bildirimi](#frontendservice-service-manifest)| [ortam değişkenlerini ayarlama](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [uç nokta yapılandırma](service-fabric-get-started-containers.md#configure-communication), kapsayıcıya komut geçirme, bir [sertifikayı kapsayıcıya aktarma](service-fabric-securing-containers.md)| 
|[BackEndService hizmeti bildirimi](#backendservice-service-manifest)|[ortam değişkenlerini ayarlama](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [uç nokta yapılandırma](service-fabric-get-started-containers.md#configure-communication), [birim sürücüsünü yapılandırma](service-fabric-containers-volume-logging-drivers.md)| 

Belirli XML öğeleri hakkında daha fazla bilgi için bkz. [uygulama bildirimi öğeleri](#application-manifest-elements), [frontendservice hizmet bildirimi öğeleri](#frontendservice-service-manifest-elements)ve [BackendService hizmet bildirimi öğeleri](#backendservice-service-manifest-elements) .

## <a name="application-manifest"></a>Uygulama bildirimi

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService hizmet bildirimi

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService hizmeti bildirimi

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>Uygulama bildirim öğeleri
### <a name="applicationmanifest-element"></a>ApplicationManifest öğesi
Uygulama türünü ve sürümünü bildirimli olarak açıklar. Bileşen hizmetlerinin bir veya daha fazla hizmet bildirimine bir uygulama türü oluşturmak için başvurulur. Yapısal hizmetlerin yapılandırma ayarları, parametreli uygulama ayarları kullanılarak geçersiz kılınabilir. Varsayılan hizmetler, hizmet şablonları, sorumlular, ilkeler, tanılama kurulumu ve sertifikalar uygulama düzeyinde de bildirilebilecek. Daha fazla bilgi için bkz. [ApplicationManifest öğesi](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parameters öğesi
Bu uygulama bildiriminde kullanılan parametreleri bildirir. Uygulama örneği oluşturulduğunda ve uygulama ya da hizmet yapılandırma ayarlarını geçersiz kılmak için kullanılabilir olduğunda, bu parametrelerin değeri sağlanabilir. Daha fazla bilgi için bkz. [Parameters öğesi](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter öğesi
Bu bildirimde kullanılacak bir uygulama parametresi. Parametre değeri uygulama örneği oluşturma sırasında değiştirilebilir veya hiçbir değer sağlanmazsa varsayılan değer kullanılır. Daha fazla bilgi için bkz. [Parameter öğesi](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>Servicemanifestımport öğesi
Hizmet geliştiricisi tarafından oluşturulan bir hizmet bildirimini içeri aktarır. Uygulamadaki her bir bileşen hizmeti için bir hizmet bildirimi içeri aktarılmalıdır. Yapılandırma geçersiz kılmaları ve ilkeler hizmet bildirimi için bildirilebilecek. Daha fazla bilgi için bkz. [Servicemanifestımport öğesi](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef öğesi
Hizmet bildirimini başvuruya göre içeri aktarır. Şu anda hizmet bildirim dosyası (ServiceManifest. xml) yapı paketinde mevcut olmalıdır. Daha fazla bilgi için bkz. [Servicemanifestref öğesi](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="policies-element"></a>Policies öğesi
İçeri aktarılan hizmet bildirimine uygulanacak ilkeleri (bitiş noktası bağlama, paket paylaşımı, farklı çalıştır ve güvenlik erişimi) açıklar. Daha fazla bilgi için bkz. [Policies öğesi](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Servicepackageresourcehüküancepolicy öğesi
Tüm hizmet paketinin düzeyinde uygulanan kaynak idare ilkesini tanımlar. Daha fazla bilgi için bkz. [Servicepackageresourcegovernancepolicy öğesi](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Resourcehüküancepolicy öğesi
Bir kod paketi için kaynak sınırlarını belirtir. Daha fazla bilgi için bkz. [Resourcehüküancepolicy öğesi](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies öğesi
Kapsayıcı Konakları etkinleştirme ilkelerini belirtir. Daha fazla bilgi için bkz. [Containerhostpolicies öğesi](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Depobilgileri öğesi
Görüntüleri çekmek için kapsayıcı görüntüsü deposunun kimlik bilgileri. Daha fazla bilgi için bkz. [Depoıkimlik bilgileri öğesi](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding öğesi
Gösterilen kapsayıcı bağlantı noktasına bağlanacak bitiş noktası kaynağını belirtir. Daha fazla bilgi için bkz. [Portbinding öğesi](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Volume öğesi
Kapsayıcıya bağlanacak birimi belirtir. Daha fazla bilgi için bkz. [Volume öğesi](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption öğesi
Sürücüye geçirilecek sürücü seçenekleri. Daha fazla bilgi için bkz. [Driveroption öğesi](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Imagekılmalar öğesi
Windows Server kapsayıcıları, işletim sisteminin farklı sürümleri arasında uyumlu olmayabilir.  Kapsayıcı başına birden çok işletim sistemi görüntüsü belirtebilir ve bunları işletim sisteminin derleme sürümleriyle etiketleyebilirsiniz. Bir Windows komut isteminde "winver" komutunu çalıştırarak işletim sisteminin derleme sürümünü alın. Temel alınan işletim sistemi derleme sürümü 16299 (Windows Server sürüm 1709) ise, Service Fabric OS = "16299" ile etiketlenmiş kapsayıcı görüntüsünü seçer. Etiketlenmemiş bir kapsayıcı görüntüsünün işletim sisteminin tüm sürümlerinde çalıştığı ve hizmet bildiriminde belirtilen görüntünün üzerine belirlediği varsayılır. Daha fazla bilgi için bkz. [ımagekılmalar öğesi](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Image öğesi
Başlatılacak işletim sistemi derleme sürüm numarasına karşılık gelen kapsayıcı görüntüsü. Işletim sistemi özniteliği belirtilmemişse, kapsayıcı görüntüsünün işletim sisteminin tüm sürümlerinde çalıştığı varsayılır ve hizmet bildiriminde belirtilen görüntüyü geçersiz kılar. Daha fazla bilgi için bkz. [görüntü öğesi](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides öğesi
 Daha fazla bilgi için bkz. [EnvironmentOverrides öğesi](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>EnvironmentVariable öğesi
Ortam değişkeni. Daha fazla bilgi için bkz. [Environmentvariable öğesi](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef öğesi
Kapsayıcı ortamına sunulacak bir x509 sertifikası hakkındaki bilgileri belirtir. Sertifika, tüm küme düğümlerinin LocalMachine deposunda yüklü olmalıdır.
Uygulama başlatıldığında, çalışma zamanı sertifikayı okur ve bir PFX dosyası ve parola (Windows üzerinde) veya ped dosyası (Linux üzerinde) oluşturur.
PFX dosya ve parolaya Certificates_ServicePackageName_CodePackageName_CertName_PFX ve Certificates_ServicePackageName_CodePackageName_CertName_Password ortam değişkenleri kullanılarak kapsayıcıda erişilebilir. PED dosyasına Certificates_ServicePackageName_CodePackageName_CertName_PEM ve Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey ortam değişkenleri kullanılarak kapsayıcıda erişilebilir. Daha fazla bilgi için bkz. [CertificateRef öğesi](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices öğesi
Bu uygulama türüne karşı her uygulama oluşturulduğunda otomatik olarak oluşturulan hizmet örneklerini bildirir. Daha fazla bilgi için bkz. [DefaultServices öğesi](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Hizmet öğesi
Uygulama örneği oluşturulduğunda otomatik olarak oluşturulacak bir hizmet bildirir. Daha fazla bilgi için bkz. [hizmet öğesi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService öğesi
Durum bilgisi olmayan bir hizmet tanımlar. Daha fazla bilgi için bkz. [Statelessservice öğesi](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService hizmet bildirimi öğeleri
### <a name="servicemanifest-element"></a>ServiceManifest öğesi
Hizmet türünü ve sürümü bildirimli olarak açıklar. Bir veya daha fazla hizmet türünü desteklemek üzere bir hizmet paketi oluşturan bağımsız olarak yükseltilebilir kodu, yapılandırmayı ve veri paketlerini listeler. Kaynaklar, Tanılama ayarları ve hizmet türü, sistem durumu özellikleri ve yük dengeleme ölçümleri gibi hizmet meta verileri de belirtilir. Daha fazla bilgi için bkz. [Servicemanifest öğesi](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType öğesi
Bu bildirimde bir CodePackage tarafından desteklenen hizmet türlerini tanımlar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Hizmet türleri, kod paketi düzeyi değil, bildirim düzeyinde belirtilir. Daha fazla bilgi için bkz. [serviceType öğesi](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType öğesi
Durum bilgisi olmayan bir hizmet türünü açıklar. Daha fazla bilgi için bkz. [Statelessservicetype öğesi](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage öğesi
Tanımlı bir hizmet türünü destekleyen bir kod paketi açıklar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Elde edilen işlemlerin, çalışma zamanında desteklenen hizmet türlerini kaydetmesi beklenmektedir. Birden çok kod paketi olduğunda, her sistem, belirtilen hizmet türlerinden birini her ararken etkinleştirilir. Daha fazla bilgi için bkz. [CodePackage öğesi](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint öğesi
Giriş noktası tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı. Ayrı bir kurulum giriş noktasının varlığı, hizmet ana bilgisayarını uzun süreler boyunca yüksek ayrıcalıklarla çalıştırmak zorunda kalmaktan kaçınır. Giriş noktası tarafından belirtilen yürütülebilir dosya, SetupEntryPoint başarıyla çıktıktan sonra çalıştırılır. Elde edilen işlem sonlandırıldığında veya kilitlenirse, ortaya çıkan işlem izlenir ve yeniden başlatılır (SetupEntryPoint ile yeniden başlar). Daha fazla bilgi için bkz. [entryPoint öğesi](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost öğesi
 Daha fazla bilgi için bkz. [Containerhost öğesi](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>GörüntüAdı öğesi
https://hub.docker.com veya Azure Container Registry üzerindeki depo ve görüntü. Daha fazla bilgi için bkz. [GörüntüAdı öğesi](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables öğesi
Ortam değişkenlerini kapsayıcısına veya exe 'ye geçirin.  Daha fazla bilgi için bkz. [EnvironmentVariables öğesi](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable öğesi
Ortam değişkeni. Daha fazla bilgi için bkz. [Environmentvariable öğesi](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage öğesi
Bir Settings. xml dosyası içeren name özniteliğiyle adlandırılan bir klasörü bildirir. Bu dosya, işlemin çalışma zamanında okuyabildiği Kullanıcı tanımlı, anahtar-değer çifti ayarlarının bölümlerini içerir. Yükseltme sırasında, yalnızca ConfigPackage sürümü değiştiyse, çalışan işlem yeniden başlatılmaz. Bunun yerine, bir geri çağırma işlemi, dinamik olarak yeniden yüklenmesi için yapılandırma ayarlarının değiştiği süreci bilgilendirir. Daha fazla bilgi için bkz. [Configpackage öğesi](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage öğesi
Statik veri dosyalarını içeren name özniteliğiyle adlandırılan bir klasörü bildirir. Service Fabric, hizmet bildiriminde listelenen veri paketlerinden herhangi biri yükseltildiğinde konak ve destek paketlerinde belirtilen tüm EXEs ve Dllkonakları geri dönüştürecek. Daha fazla bilgi için bkz. [DataPackage öğesi](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resources öğesi
Bu hizmet tarafından kullanılan ve derlenen kodu değiştirmeden bildirilebilecek ve hizmet dağıtıldığında değiştirilebilen kaynakları açıklar. Bu kaynaklara erişim, uygulama bildiriminin asıl ve Ilke bölümleri aracılığıyla denetlenir. Daha fazla bilgi için bkz. [Resources öğesi](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints öğesi
Hizmetin uç noktalarını tanımlar. Daha fazla bilgi için bkz. [uç noktalar öğesi](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint öğesi
Daha fazla bilgi için bkz. [Endpoint öğesi](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService bildirim öğeleri
### <a name="servicemanifest-element"></a>ServiceManifest öğesi
Hizmet türünü ve sürümü bildirimli olarak açıklar. Bir veya daha fazla hizmet türünü desteklemek üzere bir hizmet paketi oluşturan bağımsız olarak yükseltilebilir kodu, yapılandırmayı ve veri paketlerini listeler. Kaynaklar, Tanılama ayarları ve hizmet türü, sistem durumu özellikleri ve yük dengeleme ölçümleri gibi hizmet meta verileri de belirtilir. Daha fazla bilgi için bkz. [Servicemanifest öğesi](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType öğesi
Bu bildirimde bir CodePackage tarafından desteklenen hizmet türlerini tanımlar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Hizmet türleri, kod paketi düzeyi değil, bildirim düzeyinde belirtilir. Daha fazla bilgi için bkz. [serviceType öğesi](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType öğesi
Durum bilgisi olmayan bir hizmet türünü açıklar. Daha fazla bilgi için bkz. [Statelessservicetype öğesi](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage öğesi
Tanımlı bir hizmet türünü destekleyen bir kod paketi açıklar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Elde edilen işlemlerin, çalışma zamanında desteklenen hizmet türlerini kaydetmesi beklenmektedir. Birden çok kod paketi olduğunda, her sistem, belirtilen hizmet türlerinden birini her ararken etkinleştirilir. Daha fazla bilgi için bkz. [CodePackage öğesi](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint öğesi
Giriş noktası tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı. Ayrı bir kurulum giriş noktasının varlığı, hizmet ana bilgisayarını uzun süreler boyunca yüksek ayrıcalıklarla çalıştırmak zorunda kalmaktan kaçınır. Giriş noktası tarafından belirtilen yürütülebilir dosya, SetupEntryPoint başarıyla çıktıktan sonra çalıştırılır. Elde edilen işlem sonlandırıldığında veya kilitlenirse, ortaya çıkan işlem izlenir ve yeniden başlatılır (SetupEntryPoint ile yeniden başlar). Daha fazla bilgi için bkz. [entryPoint öğesi](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost öğesi
Daha fazla bilgi için bkz. [Containerhost öğesi](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>GörüntüAdı öğesi
https://hub.docker.com veya Azure Container Registry üzerindeki depo ve görüntü. Daha fazla bilgi için bkz. [GörüntüAdı öğesi](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands öğesi
Kapsayıcıya virgülle ayrılmış komutların bir listesini geçirin. Daha fazla bilgi için bkz. [Commands öğesi](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables öğesi
Ortam değişkenlerini kapsayıcısına veya exe 'ye geçirin.  Daha fazla bilgi için bkz. [EnvironmentVariables öğesi](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable öğesi
Ortam değişkeni. Daha fazla bilgi için bkz. [Environmentvariable öğesi](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage öğesi
Bir Settings. xml dosyası içeren name özniteliğiyle adlandırılan bir klasörü bildirir. Bu dosya, işlemin çalışma zamanında okuyabildiği Kullanıcı tanımlı, anahtar-değer çifti ayarlarının bölümlerini içerir. Yükseltme sırasında, yalnızca ConfigPackage sürümü değiştiyse, çalışan işlem yeniden başlatılmaz. Bunun yerine, bir geri çağırma işlemi, dinamik olarak yeniden yüklenmesi için yapılandırma ayarlarının değiştiği süreci bilgilendirir. Daha fazla bilgi için bkz. [Configpackage öğesi](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resources öğesi
Bu hizmet tarafından kullanılan ve derlenen kodu değiştirmeden bildirilebilecek ve hizmet dağıtıldığında değiştirilebilen kaynakları açıklar. Bu kaynaklara erişim, uygulama bildiriminin asıl ve Ilke bölümleri aracılığıyla denetlenir. Daha fazla bilgi için bkz. [Resources öğesi](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints öğesi
Hizmetin uç noktalarını tanımlar. Daha fazla bilgi için bkz. [uç noktalar öğesi](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint öğesi
 Daha fazla bilgi için bkz. [Endpoint öğesi](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

