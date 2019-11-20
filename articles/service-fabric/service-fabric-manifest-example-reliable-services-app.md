---
title: Azure Service Fabric güvenilir hizmetler uygulama bildirimi örnekleri
description: Güvenilir hizmetler Service Fabric uygulaması için uygulama ve hizmet bildirimi ayarlarını yapılandırmayı öğrenin.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 9cc79610b6dc9f9d2869a41e0b483168087368cc
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013235"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Güvenilir hizmetler uygulaması ve hizmet bildirimi örnekleri
Aşağıda, bir ASP.NET Core Web ön ucuna ve durum bilgisi olan arka uca sahip bir Service Fabric uygulaması için uygulama ve hizmet bildirimlerinin örnekleri verilmiştir. Bu örneklerin amacı, hangi ayarların kullanılabildiğini ve bunların nasıl kullanılacağını gösterir. Bu uygulama ve hizmet bildirimleri [Service Fabric .net hızlı başlangıç](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) bildirimlerini temel alır.

Aşağıdaki özellikler gösteriliyor:

|Bildirim|Özellikler|
|---|---|
|[Uygulama bildirimi](#application-manifest)| [kaynak](service-fabric-resource-governance.md)İdaresi, [bir hizmeti yerel yönetici hesabı olarak çalıştırma](service-fabric-application-runas-security.md), [tüm hizmet kodu paketlerine varsayılan bir ilke uygulama](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [Kullanıcı ve grup sorumluları oluşturma](service-fabric-application-runas-security.md), hizmet örnekleri arasında bir veri paketi paylaşma, [hizmet uç noktalarını geçersiz kılma](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService hizmet bildirimi| [Hizmet başlangıcında betik çalıştırma](service-fabric-run-script-at-service-startup.md), [bir HTTPS uç noktası tanımlama](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService hizmeti bildirimi| Bir [yapılandırma paketi bildirme](service-fabric-application-and-service-manifests.md), [veri paketi bildirme](service-fabric-application-and-service-manifests.md), [uç nokta yapılandırma](service-fabric-service-manifest-resources.md)| 

Belirli XML öğeleri hakkında daha fazla bilgi için bkz. [uygulama bildirimi öğeleri](#application-manifest-elements), [votingweb hizmeti bildirim öğeleri](#votingweb-service-manifest-elements)ve [votingdata Service bildirim öğeleri](#votingdata-service-manifest-elements) .

## <a name="application-manifest"></a>Uygulama bildirimi

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb hizmeti bildirimi

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData hizmeti bildirimi

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
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

### <a name="resourceoverrides-element"></a>Resourcekılmalar öğesi
Hizmet bildirim kaynaklarında belirtilen uç noktalar için kaynak geçersiz kılmalarını belirtir. Daha fazla bilgi için bkz. [Resourcekılmalar öğesi](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Endpoints öğesi
Geçersiz kılınacak bitiş noktaları. Daha fazla bilgi için bkz. [uç noktalar öğesi](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint öğesi
Geçersiz kılmak için hizmet bildiriminde belirtilen uç nokta. Daha fazla bilgi için bkz. [Endpoint öğesi](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Policies öğesi
İçeri aktarılan hizmet bildirimine uygulanacak ilkeleri (bitiş noktası bağlama, paket paylaşımı, farklı çalıştır ve güvenlik erişimi) açıklar. Daha fazla bilgi için bkz. [Policies öğesi](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Servicepackageresourcehüküancepolicy öğesi
Tüm hizmet paketinin düzeyinde uygulanan kaynak idare ilkesini tanımlar. Daha fazla bilgi için bkz. [Servicepackageresourcegovernancepolicy öğesi](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Resourcehüküancepolicy öğesi
Bir CodePackage için kaynak sınırlarını belirtir. Daha fazla bilgi için bkz. [Resourcehüküancepolicy öğesi](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy öğesi
Bir kod, yapılandırma veya veri paketinin aynı hizmet türünün hizmet örnekleri arasında paylaşılıp paylaşılmayacağını gösterir. Daha fazla bilgi için bkz. [Packagesharingpolicy öğesi](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy öğesi
Bir hizmet bildiriminde tanımlanan bir kaynaktaki (bir uç nokta gibi) bir sorumlu için erişim izinleri verir. Genellikle, güvenlik risklerini en aza indirmek için hizmetlerin farklı kaynaklara erişimini denetlemek ve kısıtlamak çok yararlıdır. Bu, uygulama, farklı geliştiriciler tarafından geliştirilen bir market 'ten bir hizmetler koleksiyonundan oluşturulduğunda özellikle önemlidir. Daha fazla bilgi için bkz. [Securityaccesspolicy öğesi](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy öğesi
Bir hizmet kodu paketinin çalışacağı yerel kullanıcı veya yerel sistem hesabını belirtir. Etki alanı hesapları Azure Active Directory kullanılabildiği Windows Server dağıtımlarında desteklenir. Varsayılan olarak, uygulamalar Fabric. exe işleminin altında çalıştığı hesap altında çalışır. Uygulamalar, sorumlular bölümünde bildirilmelidir olması gereken diğer hesaplar olarak da çalıştırılabilir. Bir hizmete RunAs ilkesi uygularsanız ve hizmet bildirimi uç nokta kaynaklarını HTTP protokolüyle bildirirse, bu uç noktalara ayrılan bağlantı noktalarının RunAs için doğru bir şekilde erişim denetimi olduğundan emin olmak için bir SecurityAccessPolicy belirtmeniz gerekir hizmetin altında çalıştığı kullanıcı hesabı. Bir HTTPS uç noktası için, istemciye döndürülecek sertifikanın adını göstermek üzere bir EndpointBindingPolicy de tanımlamanız gerekir. Daha fazla bilgi için bkz. [Runaspolicy öğesi](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices öğesi
Bu uygulama türüne karşı her uygulama oluşturulduğunda otomatik olarak oluşturulan hizmet örneklerini bildirir. Daha fazla bilgi için bkz. [DefaultServices öğesi](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Hizmet öğesi
Uygulama örneği oluşturulduğunda otomatik olarak oluşturulacak bir hizmet bildirir. Daha fazla bilgi için bkz. [hizmet öğesi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService öğesi
Durum bilgisi olan bir hizmeti tanımlar. Daha fazla bilgi için bkz. [Statefulservice öğesi](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService öğesi
Durum bilgisi olmayan bir hizmet tanımlar. Daha fazla bilgi için bkz. [Statelessservice öğesi](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Sorumlular öğesi
Bu uygulamanın hizmetleri ve güvenli kaynakları çalıştırması için gereken güvenlik sorumlularını (kullanıcılar, gruplar) açıklar. Sorumlular ilkeler bölümlerinde başvurulur. Daha fazla bilgi için bkz. [sorumlular öğesi](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Groups öğesi
İlkeler bölümünde başvurulabilen güvenlik sorumluları olarak bir grup kümesi bildirir. Farklı hizmet giriş noktaları için birden fazla kullanıcı varsa ve grup düzeyinde kullanılabilen belirli ortak ayrıcalıklara sahip olmaları gerekiyorsa, gruplar yararlıdır. Daha fazla bilgi için bkz. [gruplar öğesi](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Group öğesi
İlke içinde başvurulabilen bir güvenlik sorumlusu olarak bir grup bildirir. Daha fazla bilgi için bkz. [Grup öğesi](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Üyelik öğesi
 Daha fazla bilgi için bkz. [Üyelik öğesi](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup öğesi
 Daha fazla bilgi için bkz. [Systemgroup öğesi](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Users öğesi
İlkeler bölümünde başvurulabilen güvenlik sorumluları olarak bir kullanıcı kümesi bildirir. Daha fazla bilgi için bkz. [Users öğesi](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Kullanıcı öğesi
Bir kullanıcıyı, ilkelerde başvurulabilen bir güvenlik sorumlusu olarak bildirir. Daha fazla bilgi için bkz. [Kullanıcı öğesi](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf öğesi
Kullanıcılar, var olan herhangi bir üyelik grubuna eklenebilir, bu nedenle bu üyelik grubunun tüm özelliklerini ve güvenlik ayarlarını devralmasını sağlayabilirsiniz. Üyelik grubu, farklı hizmetler veya aynı hizmet (farklı bir makinede) tarafından erişilmesi gereken dış kaynakları güvenli hale getirmek için kullanılabilir. Daha fazla bilgi için bkz. [memberOf öğesi](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup öğesi
Kullanıcının ekleneceği sistem grubu.  Sistem grubu, Gruplar bölümünde tanımlanmalıdır. Daha fazla bilgi için bkz. [Systemgroup öğesi](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Group öğesi
Kullanıcının ekleneceği grup.  Grup, Gruplar bölümünde tanımlanmalıdır. Daha fazla bilgi için bkz. [Grup öğesi](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Policies öğesi
Uygulama düzeyinde uygulanacak ilkeleri (günlük koleksiyonu, varsayılan farklı çalıştır, sistem durumu ve güvenlik erişimi) açıklar. Daha fazla bilgi için bkz. [Policies öğesi](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy öğesi
Servicemanifestımport bölümünde tanımlanmış belirli bir RunAsPolicy olmayan tüm hizmet kodu paketleri için varsayılan bir kullanıcı hesabı belirtin. Daha fazla bilgi için bkz. [DefaultRunAsPolicy öğesi](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>VotingWeb hizmeti bildirim öğeleri
### <a name="servicemanifest-element"></a>ServiceManifest öğesi
Hizmet türünü ve sürümü bildirimli olarak açıklar. Bir veya daha fazla hizmet türünü desteklemek üzere bir hizmet paketi oluşturan bağımsız olarak yükseltilebilir kodu, yapılandırmayı ve veri paketlerini listeler. Kaynaklar, Tanılama ayarları ve hizmet türü, sistem durumu özellikleri ve yük dengeleme ölçümleri gibi hizmet meta verileri de belirtilir. Daha fazla bilgi için bkz. [Servicemanifest öğesi](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType öğesi
Bu bildirimde bir CodePackage tarafından desteklenen hizmet türlerini tanımlar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Hizmet türleri, kod paketi düzeyi değil, bildirim düzeyinde belirtilir. Daha fazla bilgi için bkz. [serviceType öğesi](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType öğesi
Durum bilgisi olmayan bir hizmet türünü açıklar. Daha fazla bilgi için bkz. [Statelessservicetype öğesi](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage öğesi
Tanımlı bir hizmet türünü destekleyen bir kod paketi açıklar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Elde edilen işlemlerin, çalışma zamanında desteklenen hizmet türlerini kaydetmesi beklenmektedir. Birden çok kod paketi olduğunda, her sistem, belirtilen hizmet türlerinden birini her ararken etkinleştirilir. Daha fazla bilgi için bkz. [CodePackage öğesi](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint öğesi
Varsayılan olarak, diğer herhangi bir giriş noktasındaki Service Fabric (genellikle NETWORKSERVICE hesabı) ile aynı kimlik bilgileriyle çalışan ayrıcalıklı bir giriş noktası. Giriş noktası tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı. Ayrı bir kurulum giriş noktasının varlığı, hizmet ana bilgisayarını uzun süreler boyunca yüksek ayrıcalıklarla çalıştırmak zorunda kalmaktan kaçınır. Daha fazla bilgi için bkz. [Setupentrypoint öğesi](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost öğesi
 Daha fazla bilgi için bkz. [Exehost öğesi](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program öğesi
Yürütülebilir dosya adı.  Örneğin, "MySetup. bat" veya "MyServiceHost. exe". Daha fazla bilgi için bkz. [program öğesi](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Arguments öğesi
 Daha fazla bilgi için bkz. [arguments öğesi](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder öğesi
Uygulamanın dağıtıldığı küme düğümündeki kod paketindeki işlemin çalışma dizini. Üç değer belirtebilirsiniz: Iş (varsayılan), CodePackage veya CodeBase. Kod temeli, çalışma dizininin, EXE 'nin kod paketinde tanımlandığı dizine ayarlandığını belirtir. CodePackage, çalışma dizinini, EXE 'nin kod paketi dizininde tanımlandığı yere bakılmaksızın kod paketinin kökü olacak şekilde ayarlar. Çalışma dizini, düğümde oluşturulan benzersiz bir klasöre ayarlanır.  Bu klasör tüm uygulama örneği için aynıdır. Varsayılan olarak, uygulamadaki tüm işlemlerin çalışma dizini, uygulama çalışma klasörüne ayarlanır. Bu, işlemlerin verileri yazabilmesi durumunda yer alabilir. Bu klasörler farklı uygulama örnekleri arasında paylaşılabilmesi ve silinemediğinden, kod paketine veya kod tabanına veri yazılması önerilmez. Daha fazla bilgi için bkz. [WorkingFolder öğesi](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection öğesi

> [!WARNING]
> Konsol yeniden yönlendirmeyi bir üretim uygulamasında kullanmayın, yalnızca yerel geliştirme ve hata ayıklama için kullanın. Konsol çıkışını başlangıç betiğinin, uygulamanın dağıtıldığı ve çalıştırıldığı küme düğümünde "log" adlı uygulama klasöründeki bir çıkış dosyasına yönlendirir. Daha fazla bilgi için bkz. [Consoleredirection öğesi](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint öğesi
Giriş noktası tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı. Ayrı bir kurulum giriş noktasının varlığı, hizmet ana bilgisayarını uzun süreler boyunca yüksek ayrıcalıklarla çalıştırmak zorunda kalmaktan kaçınır. Giriş noktası tarafından belirtilen yürütülebilir dosya, SetupEntryPoint başarıyla çıktıktan sonra çalıştırılır. Elde edilen işlem sonlandırıldığında veya kilitlenirse, ortaya çıkan işlem izlenir ve yeniden başlatılır (SetupEntryPoint ile yeniden başlar). Daha fazla bilgi için bkz. [entryPoint öğesi](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost öğesi
 Daha fazla bilgi için bkz. [Exehost öğesi](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage öğesi
Bir Settings. xml dosyası içeren PackageRoot altında Name özniteliğiyle adlandırılan bir klasörü bildirir. Bu dosya, işlemin çalışma zamanında okuyabildiği Kullanıcı tanımlı, anahtar-değer çifti ayarlarının bölümlerini içerir. Yükseltme sırasında, yalnızca ConfigPackage sürümü değiştiyse, çalışan işlem yeniden başlatılmaz. Bunun yerine, bir geri çağırma işlemi, dinamik olarak yeniden yüklenmesi için yapılandırma ayarlarının değiştiği süreci bilgilendirir. Daha fazla bilgi için bkz. [Configpackage öğesi](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resources öğesi
Bu hizmet tarafından kullanılan ve derlenen kodu değiştirmeden bildirilebilecek ve hizmet dağıtıldığında değiştirilebilen kaynakları açıklar. Bu kaynaklara erişim, uygulama bildiriminin asıl ve Ilke bölümleri aracılığıyla denetlenir. Daha fazla bilgi için bkz. [Resources öğesi](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints öğesi
Hizmetin uç noktalarını tanımlar. Daha fazla bilgi için bkz. [uç noktalar öğesi](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint öğesi
Geçersiz kılmak için hizmet bildiriminde belirtilen uç nokta. Daha fazla bilgi için bkz. [Endpoint öğesi](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData hizmeti bildirim öğeleri
### <a name="servicemanifest-element"></a>ServiceManifest öğesi
Hizmet türünü ve sürümü bildirimli olarak açıklar. Bir veya daha fazla hizmet türünü desteklemek üzere bir hizmet paketi oluşturan bağımsız olarak yükseltilebilir kodu, yapılandırmayı ve veri paketlerini listeler. Kaynaklar, Tanılama ayarları ve hizmet türü, sistem durumu özellikleri ve yük dengeleme ölçümleri gibi hizmet meta verileri de belirtilir. Daha fazla bilgi için bkz. [Servicemanifest öğesi](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType öğesi
Bu bildirimde bir CodePackage tarafından desteklenen hizmet türlerini tanımlar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Hizmet türleri, kod paketi düzeyi değil, bildirim düzeyinde belirtilir. Daha fazla bilgi için bkz. [serviceType öğesi](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType öğesi
Durum bilgisi olan bir hizmet türünü açıklar. Daha fazla bilgi için bkz. [Statefulservicetype öğesi](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage öğesi
Tanımlı bir hizmet türünü destekleyen bir kod paketi açıklar. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Elde edilen işlemlerin, çalışma zamanında desteklenen hizmet türlerini kaydetmesi beklenmektedir. Birden çok kod paketi olduğunda, her sistem, belirtilen hizmet türlerinden birini her ararken etkinleştirilir. Daha fazla bilgi için bkz. [CodePackage öğesi](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint öğesi
Giriş noktası tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı. Ayrı bir kurulum giriş noktasının varlığı, hizmet ana bilgisayarını uzun süreler boyunca yüksek ayrıcalıklarla çalıştırmak zorunda kalmaktan kaçınır. Giriş noktası tarafından belirtilen yürütülebilir dosya, SetupEntryPoint başarıyla çıktıktan sonra çalıştırılır. Elde edilen işlem sonlandırıldığında veya kilitlenirse, ortaya çıkan işlem izlenir ve yeniden başlatılır (SetupEntryPoint ile yeniden başlar). Daha fazla bilgi için bkz. [entryPoint öğesi](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost öğesi
 Daha fazla bilgi için bkz. [Exehost öğesi](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program öğesi
Yürütülebilir dosya adı.  Örneğin, "MySetup. bat" veya "MyServiceHost. exe". Daha fazla bilgi için bkz. [program öğesi](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder öğesi
Uygulamanın dağıtıldığı küme düğümündeki kod paketindeki işlemin çalışma dizini. Üç değer belirtebilirsiniz: Iş (varsayılan), CodePackage veya CodeBase. Kod temeli, çalışma dizininin, EXE 'nin kod paketinde tanımlandığı dizine ayarlandığını belirtir. CodePackage, çalışma dizinini, EXE 'nin kod paketi dizininde tanımlandığı yere bakılmaksızın kod paketinin kökü olacak şekilde ayarlar. Çalışma dizini, düğümde oluşturulan benzersiz bir klasöre ayarlanır.  Bu klasör tüm uygulama örneği için aynıdır. Varsayılan olarak, uygulamadaki tüm işlemlerin çalışma dizini, uygulama çalışma klasörüne ayarlanır. Bu, işlemlerin verileri yazabilmesi durumunda yer alabilir. Bu klasörler farklı uygulama örnekleri arasında paylaşılabilmesi ve silinemediğinden, kod paketine veya kod tabanına veri yazılması önerilmez. Daha fazla bilgi için bkz. [WorkingFolder öğesi](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage öğesi
Bir Settings. xml dosyası içeren PackageRoot altında Name özniteliğiyle adlandırılan bir klasörü bildirir. Bu dosya, işlemin çalışma zamanında okuyabildiği Kullanıcı tanımlı, anahtar-değer çifti ayarlarının bölümlerini içerir. Yükseltme sırasında, yalnızca ConfigPackage sürümü değiştiyse, çalışan işlem yeniden başlatılmaz. Bunun yerine, bir geri çağırma işlemi, dinamik olarak yeniden yüklenmesi için yapılandırma ayarlarının değiştiği süreci bilgilendirir. Daha fazla bilgi için bkz. [Configpackage öğesi](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage öğesi
Çalışma zamanında işlem tarafından tüketilen statik veri dosyalarını içeren PackageRoot altında Name özniteliğiyle adlandırılan bir klasörü bildirir. Service Fabric, hizmet bildiriminde listelenen veri paketlerinden herhangi biri yükseltildiğinde konak ve destek paketlerinde belirtilen tüm EXEs ve Dllkonakları geri dönüştürecek. Daha fazla bilgi için bkz. [DataPackage öğesi](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resources öğesi
Bu hizmet tarafından kullanılan ve derlenen kodu değiştirmeden bildirilebilecek ve hizmet dağıtıldığında değiştirilebilen kaynakları açıklar. Bu kaynaklara erişim, uygulama bildiriminin asıl ve Ilke bölümleri aracılığıyla denetlenir. Daha fazla bilgi için bkz. [Resources öğesi](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints öğesi
Hizmetin uç noktalarını tanımlar. Daha fazla bilgi için bkz. [uç noktalar öğesi](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint öğesi
Geçersiz kılmak için hizmet bildiriminde belirtilen uç nokta. Daha fazla bilgi için bkz. [Endpoint öğesi](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

