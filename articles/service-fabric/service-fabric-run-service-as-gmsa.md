---
title: Bir Azure Service Fabric hizmetini gMSA hesabı altında çalıştırma
description: Bir hizmeti Service Fabric Windows tek başına kümesi üzerinde grup tarafından yönetilen hizmet hesabı (gMSA) olarak çalıştırmayı öğrenin.
ms.topic: how-to
ms.date: 03/29/2018
ms.openlocfilehash: 9750042764306c5df7a391429cc6926704db05ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91838917"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Grup tarafından Yönetilen Hizmet Hesabı olarak hizmet çalıştırma

Windows Server tek başına kümesinde *, bir Çalıştır ilkesi kullanarak* bir hizmeti *Grup yönetilen hizmet hesabı* (gMSA) olarak çalıştırabilirsiniz.  Varsayılan olarak, Service Fabric uygulamalar işlemin altında çalıştığı hesap altında çalışır `Fabric.exe` . Uygulamaları, paylaşılan barındırılan bir ortamda bile farklı hesaplar altında çalıştırmak, bunları diğerinden daha güvenli hale getirir. Bir gMSA kullanarak, uygulama bildiriminde depolanan parola veya şifreli parola yoktur.  Ayrıca, bir hizmeti [Active Directory Kullanıcı veya grup](service-fabric-run-service-as-ad-user-or-group.md)olarak da çalıştırabilirsiniz.

Aşağıdaki örnek, *svc-test $* adlı bir gMSA hesabı oluşturmayı, bu yönetilen hizmet hesabını küme düğümlerine dağıtmayı ve Kullanıcı sorumlusunu yapılandırmayı gösterir.

> [!NOTE]
> Tek başına Service Fabric kümeyle bir gMSA kullanmak için etki alanınız içinde şirket içinde Active Directory gerekir (Azure Active Directory (Azure AD) yerine).

Önkoşullar:

- Etki alanı bir KDS kök anahtarı gerektirir.
- Etki alanında en az bir Windows Server 2012 (veya R2) DC olması gerekir.

1. Active Directory etki alanı yöneticisinin cmdlet 'ini kullanarak Grup tarafından yönetilen bir hizmet hesabı oluşturmasını `New-ADServiceAccount` ve `PrincipalsAllowedToRetrieveManagedPassword` öğesinin tüm Service Fabric küme düğümlerini içerdiğinden emin olmasını sağlayın. `AccountName`, `DnsHostName` ve `ServicePrincipalName` benzersiz olmalıdır.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Service Fabric küme düğümlerinin her birinde (örneğin, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$` ), gMSA 'yı yükleyip test edin.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Kullanıcı sorumlusunu yapılandırın ve öğesini `RunAsPolicy` [kullanıcıya](./service-fabric-cluster-fabric-settings.md#runas)başvuracak şekilde yapılandırın.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Bir hizmete RunAs ilkesi uygularsanız ve hizmet bildirimi uç nokta kaynaklarını HTTP protokolüyle bildirirse, bir **Securityaccesspolicy** belirtmeniz gerekir.  Daha fazla bilgi için bkz. [http ve HTTPS uç noktaları için güvenlik erişimi Ilkesi atama](service-fabric-assign-policy-to-endpoint.md).
>

Aşağıdaki makaleler sonraki adımlarda size kılavuzluk eder:

- [Uygulama modelini anlama](service-fabric-application-model.md)
- [Hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md)
- [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
