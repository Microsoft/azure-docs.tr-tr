---
title: Bir Azure Service Fabric hizmetini gMSA hesabı altında çalıştırın | Microsoft Docs
description: Service Fabric Windows tek başına kümesinde bir hizmeti gMSA olarak çalıştırmayı öğrenin.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d00eceffebb222196191a389058c0feb496e169a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307653"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Grup tarafından Yönetilen Hizmet Hesabı olarak hizmet çalıştırma
Windows Server tek başına kümesinde, bir Çalıştır ilkesi kullanarak bir hizmeti grup yönetilen hizmet hesabı (gMSA) olarak çalıştırabilirsiniz.  Varsayılan olarak, Service Fabric uygulamalar yapı. exe işleminin altında çalıştığı hesap altında çalışır. Uygulamaları, paylaşılan barındırılan bir ortamda bile farklı hesaplar altında çalıştırmak, bunları diğerinden daha güvenli hale getirir. Bunun Active Directory Azure Active Directory (Azure AD) değil, etki alanınız içinde şirket içinde kullandığını unutmayın. Bir gMSA kullanarak, uygulama bildiriminde depolanan parola veya şifreli parola yoktur.  Ayrıca, bir hizmeti [Active Directory Kullanıcı veya grup](service-fabric-run-service-as-ad-user-or-group.md)olarak da çalıştırabilirsiniz.

Aşağıdaki örnekte *svc-test $* ; adlı bir gMSA hesabının nasıl oluşturulacağı gösterilmektedir. Bu yönetilen hizmet hesabını küme düğümlerine dağıtma; ve Kullanıcı sorumlusunu yapılandırma.

Önkoşulların önkoşulları:
- Etki alanı bir KDS kök anahtarı gerektirir.
- Etki alanında en az bir Windows Server 2012 (veya R2) DC olması gerekir.

1. Bir Active Directory etki alanı yöneticisi, `New-ADServiceAccount` komutunu kullanarak Grup tarafından yönetilen hizmet hesabı oluşturun ve bunun tüm Service Fabric küme düğümlerini `PrincipalsAllowedToRetrieveManagedPassword` içerdiğinden emin olun. `AccountName`, `DnsHostName` ve`ServicePrincipalName` benzersiz olmalıdır.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Service Fabric küme düğümlerinin her birinde (örneğin, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), gMSA 'yı yükleyip test edin.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Kullanıcı sorumlusunu yapılandırın ve RunAsPolicy öğesini kullanıcıya başvuracak şekilde yapılandırın.
    
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
> Bir hizmete RunAs ilkesi uygularsanız ve hizmet bildirimi uç nokta kaynaklarını HTTP protokolüyle bildirirse, bir **Securityaccesspolicy**belirtmeniz gerekir.  Daha fazla bilgi için bkz. [http ve HTTPS uç noktaları için güvenlik erişimi Ilkesi atama](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Sonraki adım olarak, aşağıdaki makaleleri okuyun:
* [Uygulama modelini anlama](service-fabric-application-model.md)
* [Hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md)
* [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
