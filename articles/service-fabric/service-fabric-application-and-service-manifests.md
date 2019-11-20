---
title: Azure Service Fabric uygulamalarını ve hizmetlerini açıklama | Microsoft Docs
description: Service Fabric uygulamaları ve hizmetleri anlatmak için bildirimlerin nasıl kullanıldığını açıklar.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/12/2019
ms.author: atsenthi
ms.openlocfilehash: a5e452bf3dc9f35c345a5f27af829904b4839ece
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977120"
---
# <a name="service-fabric-application-and-service-manifests"></a>Uygulama ve hizmet bildirimlerini Service Fabric
Bu makalede, Service Fabric uygulamalarının ve hizmetlerin ApplicationManifest. xml ve ServiceManifest. xml dosyaları kullanılarak nasıl tanımlandığı ve sürümü oluşturulduğu açıklanmaktadır.  Daha ayrıntılı örnekler için bkz. [uygulama ve hizmet bildirimi örnekleri](service-fabric-manifest-examples.md).  Bu bildirim dosyaları için XML şeması [Servicefabricservicemodel. xsd şema belgelerinde](service-fabric-service-model-schema.md)belgelenmiştir.

> [!WARNING]
> Manifest XML dosya şeması alt öğelerin doğru sıralamasını zorlar.  Kısmi bir geçici çözüm olarak, Service Fabric bildirimlerini yazarken veya değiştirirken Visual Studio 'da "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" dosyasını açın. Bu, alt öğelerin sıralamasını kontrol etmeniz ve ıntellı bir fikir sağlar.

## <a name="describe-a-service-in-servicemanifestxml"></a>ServiceManifest. xml ' de bir hizmeti açıkla
Hizmet bildirimi, hizmet türünü ve sürümü bildirimli olarak tanımlar. Hizmet türü, sistem durumu özellikleri, Yük Dengeleme ölçümleri, hizmet ikili dosyaları ve yapılandırma dosyaları gibi hizmet meta verilerini belirtir.  Başka bir yöntem de, bir veya daha fazla hizmet türünü desteklemek üzere bir hizmet paketi oluşturan kod, yapılandırma ve veri paketlerini açıklar. Bir hizmet bildirimi, bağımsız olarak sürümlü birden çok kod, yapılandırma ve veri paketleri içerebilir. Aşağıda, [Oylama örnek uygulamasının](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) ASP.NET Core Web ön uç hizmeti için bir hizmet bildirimi verilmiştir (ve [daha ayrıntılı örnekler](service-fabric-manifest-examples.md)aşağıda verilmiştir):

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
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Sürüm** öznitelikleri yapılandırılmamış dizelerdir ve sistem tarafından ayrıştırılmaz. Sürüm öznitelikleri, her bileşenin yükseltme için sürümü için kullanılır.

**ServiceType** , bu bildirimde **codepackages** tarafından desteklenen hizmet türlerini bildirir. Bu hizmet türlerinden birine karşı bir hizmet örneği oluşturulduğunda, bu bildirimde belirtilen tüm kod paketleri giriş noktaları çalıştırılarak etkinleştirilir. Elde edilen işlemlerin, çalışma zamanında desteklenen hizmet türlerini kaydetmesi beklenmektedir. Hizmet türleri, kod paketi düzeyi değil, bildirim düzeyinde belirtilir. Bu nedenle, birden çok kod paketi olduğunda, her sistem, belirtilen hizmet türlerinden birini her ararken etkinleştirilir.

**Giriş noktası** tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı. **Setupentrypoint** , diğer herhangi bir giriş noktasından önce Service Fabric (genellikle *LocalSystem* hesabı) ile aynı kimlik bilgileriyle çalışan ayrıcalıklı bir giriş noktasıdır.  Ayrı bir kurulum giriş noktasının varlığı, hizmet ana bilgisayarını uzun süreler boyunca yüksek ayrıcalıklarla çalıştırmak zorunda kalmaktan kaçınır. **Giriş noktası** tarafından belirtilen yürütülebilir dosya, **setupentrypoint** başarıyla çıktıktan sonra çalıştırılır. İşlem sonlandırıldığında veya kilitlenirse, sonuçta elde edilen işlem izlenir ve yeniden başlatılır ( **Setupentrypoint**ile yeniden başlar).  

**Setupentrypoint** kullanmaya yönelik tipik senaryolar, hizmet başlamadan önce bir yürütülebilir dosya çalıştırdığınızda veya yükseltilmiş ayrıcalıklarla bir işlem gerçekleştirdiğinizde yapılır. Örneğin:

* Hizmet yürütülebilir dosyasının ihtiyaç duyacağı ortam değişkenlerini ayarlama ve başlatma. Bu, yalnızca Service Fabric programlama modelleriyle yazılmış yürütülebilir dosyalar ile sınırlı değildir. Örneğin, NPM. exe ' nin bir Node. js uygulamasını dağıtmak için yapılandırılmış bazı ortam değişkenlerine ihtiyacı vardır.
* Güvenlik sertifikaları yükleyerek erişim denetimini ayarlama.

SetupEntryPoint yapılandırma hakkında daha fazla bilgi için bkz [. bir hizmet kurulumu giriş noktası için Ilkeyi yapılandırma](service-fabric-application-runas-security.md)

**EnvironmentVariables** (önceki örnekte ayarlı değil), bu kod paketi için ayarlanan ortam değişkenlerinin bir listesini sağlar. Farklı hizmet örnekleri için farklı değerler sağlamak `ApplicationManifest.xml` üzere öğesinde ortam değişkenleri geçersiz kılınabilir. 

**Veri paketi** (önceki örnekte ayarlanmamış), çalışma zamanında işlem tarafından tüketilen rastgele statik verileri içeren **Name** özniteliğiyle adlandırılan bir klasörü bildirir.

**Configpackage** , bir *Settings. xml* dosyası içeren **Name** özniteliğiyle adlandırılan bir klasörü bildirir. Ayarlar dosyası, Kullanıcı tanımlı, anahtar-değer çifti ayarlarının, işlemin çalışma zamanında geri okuduğu bölümler içerir. Yükseltme sırasında, yalnızca **Configpackage** **sürümü** değiştiyse, çalışan işlem yeniden başlatılmaz. Bunun yerine, bir geri çağırma işlemi, dinamik olarak yeniden yüklenmesi için yapılandırma ayarlarının değiştiği süreci bilgilendirir. Örnek bir *Settings. xml* dosyası aşağıda verilmiştir:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Service Fabric hizmeti **uç noktası** bir Service Fabric kaynağına örnektir. Bir Service Fabric kaynak, derlenen kod değiştirilmeden bildirilemez/değiştirilebilir. Hizmet bildiriminde belirtilen Service Fabric kaynaklarına erişim uygulama bildiriminde **SecurityGroup** aracılığıyla denetlenebilir. Hizmet bildiriminde bir uç nokta kaynağı tanımlandığında Service Fabric, bir bağlantı noktası açıkça belirtilmediğinde, ayrılmış uygulama bağlantı noktası aralığından bağlantı noktaları atar. [Uç nokta kaynaklarını belirtme veya geçersiz kılma](service-fabric-service-manifest-resources.md)hakkında daha fazla bilgi edinin.

 
> [!WARNING]
> Tasarım statik bağlantı noktaları, kümele bildiriminde belirtilen uygulama bağlantı noktası aralığıyla çakışmamalıdır. Statik bir bağlantı noktası belirtirseniz, uygulamayı uygulama bağlantı noktası aralığı dışında atarsanız, bağlantı noktası çakışmalarına neden olur. Sürüm 6.5 CU2 UYGULAMAZSANıZ, bu tür bir çakışmayı tespit ettiğimiz ancak dağıtımın sevk edilen 6,5 davranışı ile eşitlenmiş halde devam etmesine izin veren bir **sistem durumu uyarısı** vereceğiz. Ancak, uygulama dağıtımını bir sonraki Ana sürümlerden önleyebiliriz.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>ApplicationManifest. xml dosyasında bir uygulamayı açıkla
Uygulama bildiriminde, uygulama türü ve sürümü bildirimli olarak açıklanmaktadır. Kararlı adlar, bölümleme şeması, örnek sayısı/çoğaltma faktörü, güvenlik/yalıtım ilkesi, yerleştirme kısıtlamaları, yapılandırma geçersiz kılmaları ve bileşen hizmet türleri gibi hizmet oluşturma meta verilerini belirtir. Uygulamanın yerleştirildiği Yük Dengeleme etki alanları da açıklanır.

Bu nedenle, uygulama bildirimi uygulama düzeyindeki öğeleri açıklar ve bir uygulama türü oluşturmak için bir veya daha fazla hizmet bildirimine başvurur. Burada, [Oylama örnek uygulaması](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) için uygulama bildirimi verilmiştir (ve [daha ayrıntılı örnekler](service-fabric-manifest-examples.md)aşağıda verilmiştir):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Hizmet bildirimleri gibi, **Sürüm** öznitelikleri yapılandırılmamış dizelerdir ve sistem tarafından ayrıştırılmaz. Sürüm öznitelikleri, her bir bileşeni yükseltmeler için sürüm için de kullanılır.

**Parametreler** , uygulama bildirimi boyunca kullanılan parametreleri tanımlar. Uygulama örneği oluşturulduğunda ve uygulama veya hizmet yapılandırma ayarlarını geçersiz kılabildiğinden, bu parametrelerin değerleri sağlanabilir.  Varsayılan parametre değeri, uygulama örneği oluşturma sırasında değer değiştirilmez kullanılır. Ayrı ortamlar için farklı uygulama ve hizmet parametrelerinin nasıl korunmasını öğrenmek için bkz. [birden çok ortam için uygulama parametrelerini yönetme](service-fabric-manage-multiple-environment-app-configuration.md).

**Servicemanifestımport** bu uygulama türünü oluşturan hizmet bildirimlerine başvurular içeriyor. Bir uygulama bildirimi birden fazla hizmet bildirimi içeri aktarmaları içerebilir, her biri bağımsız olarak sürümlenebilir. İçeri aktarılan hizmet bildirimleri bu uygulama türü içinde geçerli olan hizmet türlerini belirlenir. Servicemanifestımport içinde, ServiceManifest. XML dosyalarındaki Settings. xml ve ortam değişkenlerinin yapılandırma değerlerini geçersiz kılarsınız. **İlkeler** (önceki örnekte ayarlanmamış) son nokta bağlama, güvenlik ve erişim ve paket paylaşımı, içeri aktarılan hizmet bildirimlerinde ayarlanabilir.  Daha fazla bilgi için bkz. [uygulamanız için güvenlik Ilkelerini yapılandırma](service-fabric-application-runas-security.md).

**DefaultServices** , bu uygulama türüne karşı her uygulama oluşturulduğunda otomatik olarak oluşturulan hizmet örneklerini bildirir. Varsayılan hizmetler, yalnızca bir kolaydır ve oluşturulduktan sonra her bakımdan normal hizmetler gibi davranır. Bunlar, uygulama örneğindeki diğer hizmetlerle birlikte yükseltilecektir ve de kaldırılabilir. Bir uygulama bildiriminde birden çok varsayılan hizmet bulunabilir.

**Sertifikalar** (önceki örnekte ayarlı değil), [https uç noktalarını kurmak](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) veya [uygulama bildiriminde gizli dizileri şifrelemek](service-fabric-application-secret-management.md)için kullanılan sertifikaları bildirir.

**Yerleştirme kısıtlamaları** , hizmetlerin nerede çalışacağını tanımlayan deyimlerdir. Bu deyimler bir veya daha fazla düğüm özelliği için seçtiğiniz ayrı hizmetlere iliştirilir. Daha fazla bilgi için bkz. [yerleştirme kısıtlamaları ve Node özelliği sözdizimi](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**İlkeler** (önceki örnekte ayarlanmamış), hizmetin Service Fabric çalışma zamanına erişip erişemeyeceğini de içeren, uygulama düzeyinde ayarlanacak günlük toplama, [varsayılan farklı çalıştır](service-fabric-application-runas-security.md), [sağlık](service-fabric-health-introduction.md#health-policies)ve [güvenlik erişim](service-fabric-application-runas-security.md) ilkelerini açıklar.

> [!NOTE] 
> Varsayılan olarak, Service Fabric uygulamalar, uygulamaya özgü istekleri kabul eden bir uç nokta biçiminde Service Fabric çalışma zamanına ve yapı ve uygulamaya özgü dosyaları içeren konaktaki dosya yollarına işaret eden ortam değişkenlerine erişebilir . Uygulama güvenilmeyen kod barındırınca bu erişimi devre dışı bırakmayı düşünün (örneğin, provenance bilinmiyor veya uygulama sahibinin yürütülmesi güvenli olmadığı bilen kod). Daha fazla bilgi için lütfen [Service Fabric en iyi güvenlik uygulamaları](service-fabric-best-practices-security.md#platform-isolation)bölümüne bakın. 
>

**Sorumlular** (önceki örnekte ayarlanmamış) [Hizmetleri ve güvenli hizmet kaynaklarını çalıştırmak](service-fabric-application-runas-security.md)için gereken güvenlik sorumlularını (kullanıcılar veya gruplar) anlatmaktadır.  Sorumlular **ilkeler** bölümlerinde başvurulur.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Sonraki adımlar
- [Bir uygulamayı paketleyin](service-fabric-package-apps.md) ve dağıtıma hazırlamak için hazırlayın.
- [Uygulamaları dağıtın ve kaldırın](service-fabric-deploy-remove-applications.md).
- [Parametreleri ve ortam değişkenlerini farklı uygulama örnekleri Için yapılandırın](service-fabric-manage-multiple-environment-app-configuration.md).
- [Uygulamanız için güvenlik Ilkelerini yapılandırın](service-fabric-application-runas-security.md).
- [Https uç noktalarını ayarlayın](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Uygulama bildiriminde gizli dizileri şifreleyin](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



