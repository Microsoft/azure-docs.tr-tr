---
title: Sistem ve yerel güvenlik hesapları altında bir Azure Service Fabric hizmeti çalıştırın | Microsoft Docs
description: Service Fabric uygulamasının sistem ve yerel güvenlik hesapları altında nasıl çalıştırılacağını öğrenin.  Hizmetlerinizi güvenli bir şekilde çalıştırmak için güvenlik sorumluları oluşturun ve farklı çalıştır ilkesini uygulayın.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: atsenthi
ms.openlocfilehash: 8b0ddc619a7e840b0379a790bd21e7beae812109
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600058"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Bir hizmeti yerel kullanıcı hesabı veya yerel sistem hesabı olarak çalıştırma
Azure Service Fabric kullanarak kümede çalışan uygulamaların farklı Kullanıcı hesapları altında güvenli hale getirebilirsiniz. Varsayılan olarak, Service Fabric uygulamalar yapı. exe işleminin altında çalıştığı hesap altında çalışır. Service Fabric Ayrıca, bir yerel kullanıcı veya sistem hesabı altında uygulama çalıştırma yeteneği sağlar. Desteklenen yerel sistem hesabı türleri, **LocalUser**, **NetworkService**, **LocalService**ve **LocalSystem**.  Windows bağımsız kümesinde Service Fabric çalıştırıyorsanız, [Active Directory etki alanı hesapları](service-fabric-run-service-as-ad-user-or-group.md) veya [Grup yönetilen hizmet hesapları](service-fabric-run-service-as-gmsa.md)altında bir hizmet çalıştırabilirsiniz.

Uygulama bildiriminde, **sorumlular** bölümünde hizmetleri çalıştırmak veya kaynakları güvenli hale getirmek için gereken kullanıcı hesaplarını tanımlarsınız. Ayrıca, bir veya daha fazla kullanıcının birlikte yönetilmesi için Kullanıcı grupları tanımlayabilir ve oluşturabilirsiniz. Bu, farklı hizmet giriş noktaları için birden fazla kullanıcı olduğunda ve grup düzeyinde kullanılabilen ortak ayrıcalıklara ihtiyaç duyduklarında yararlı olur.  Daha sonra kullanıcılardan, belirli bir hizmete veya uygulamadaki tüm hizmetlere uygulanan bir RunAs ilkesinde başvurulur. 

Varsayılan olarak, RunAs ilkesi ana giriş noktasına uygulanır.  Ayrıca, [bir sistem hesabı altında belirli yüksek ayrıcalıklı kurulum işlemlerini](service-fabric-run-script-at-service-startup.md)veya hem ana hem de kurulum giriş noktalarını çalıştırmanız gerekiyorsa, kurulum giriş noktasına bir runas ilkesi de uygulayabilirsiniz.  

> [!NOTE] 
> Bir hizmete RunAs ilkesi uygularsanız ve hizmet bildirimi uç nokta kaynaklarını HTTP protokolüyle bildirirse, bir **Securityaccesspolicy**belirtmeniz gerekir.  Daha fazla bilgi için bkz. [http ve HTTPS uç noktaları için güvenlik erişimi Ilkesi atama](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Yerel Kullanıcı olarak bir hizmet çalıştırma
Uygulamanın içindeki bir hizmetin güvenliğini sağlamaya yardımcı olmak için kullanılabilecek bir yerel kullanıcı oluşturabilirsiniz. Uygulama bildiriminin sorumlular bölümünde bir **LocalUser** hesap türü belirtildiğinde Service Fabric, uygulamanın dağıtıldığı makinelerde yerel kullanıcı hesapları oluşturur. Varsayılan olarak, bu hesaplar uygulama bildiriminde belirtilen adlara sahip değildir (örneğin, *Customer3* , aşağıdaki uygulama bildirimi örneğinde). Bunun yerine, dinamik olarak oluşturulur ve rastgele parolalara sahiptir.

Bir **servicemanifestımport**Için **runaspolicy** bölümünde, hizmet kodu paketini çalıştırmak için **sorumlular** bölümünden Kullanıcı hesabını belirtin.  Aşağıdaki örnek, bir yerel kullanıcı oluşturmayı ve ana giriş noktasına bir RunAs ilkesi uygulamayı gösterir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Yerel Kullanıcı grubu oluştur
Kullanıcı grupları oluşturabilir ve gruba bir veya daha fazla kullanıcı ekleyebilirsiniz. Bu, farklı hizmet giriş noktaları için birden fazla kullanıcı varsa ve grup düzeyinde kullanılabilen belirli ortak ayrıcalıklara sahip olmaları gerekiyorsa yararlıdır. Aşağıdaki uygulama bildirimi örneği, yönetici ayrıcalıklarına sahip *Localadmingroup* adlı yerel bir grubu gösterir. İki Kullanıcı, *Customer1* ve *Customer2*, bu yerel grubun üyesi yaptı. **Servicemanifestımport** bölümünde, *Stateful1Pkg* kod paketini *Customer2*olarak çalıştırmak için bir runas ilkesi uygulanır.  *Web1Pkg* kod paketini *Customer1*olarak çalıştırmak için başka bir runas ilkesi uygulanır.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Tüm hizmet kodu paketlerine varsayılan ilke uygulama
Belirli bir **Runaspolicy** tanımlı olmayan tüm kod paketleri için varsayılan bir kullanıcı hesabı belirtmek üzere **DefaultRunAsPolicy** bölümünü kullanın. Bir uygulama tarafından kullanılan hizmet bildiriminde belirtilen kod paketlerinin çoğunun aynı kullanıcı altında çalışması gerekiyorsa, uygulama yalnızca bu kullanıcı hesabıyla bir varsayılan RunAs ilkesi tanımlayabilir. Aşağıdaki örnek, bir kod paketinde bir **Runaspolicy** belirtilmemişse, kod paketinin, sorumlular bölümünde belirtilen **Mydefaultaccount** kullanıcısı altında çalışması gerektiğini belirtir.  Desteklenen hesap türleri LocalUser, NetworkService, LocalSystem ve LocalService ' dir.  Yerel bir kullanıcı veya hizmet kullanıyorsanız, hesap adını ve parolayı da belirtin.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Konsol yeniden yönlendirme kullanarak bir kod paketinde yerel olarak hata ayıklama
Bazen, çalışan bir hizmetten konsol çıkışını görmek için hata ayıklama amacıyla yararlı olur. Çıktıyı bir dosyaya yazan hizmet bildirimindeki giriş noktasında konsol yeniden yönlendirme ilkesi ayarlayabilirsiniz. Dosya çıktısı, uygulamanın dağıtıldığı ve çalıştırıldığı küme düğümünde **log** adlı uygulama klasörüne yazılır. 

> [!WARNING]
> Uygulama yük devretmesini etkileyebileceğinden, bu uygulamayı üretimde dağıtılan bir uygulamada hiçbir şekilde kullanmayın. Bunu *yalnızca* yerel geliştirme ve hata ayıklama amacıyla kullanın.  
> 
> 

Aşağıdaki hizmet bildirimi örneği, bir FileRetentionCount değeri ile konsol yeniden yönlendirmeyi etkinleştirmeyi gösterir:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama modelini anlama](service-fabric-application-model.md)
* [Hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md)
* [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
