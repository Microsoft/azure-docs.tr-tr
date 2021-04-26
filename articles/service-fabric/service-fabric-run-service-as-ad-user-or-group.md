---
title: Azure Service Fabric hizmetini bir AD Kullanıcı veya grup olarak çalıştırma
description: Bir hizmeti Service Fabric Windows bağımsız kümesinde Active Directory Kullanıcı veya grup olarak çalıştırmayı öğrenin.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91840736"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Bir hizmeti Active Directory Kullanıcı veya grup olarak çalıştırma
Windows Server tek başına kümesinde, RunAs ilkesini kullanarak bir hizmeti Active Directory Kullanıcı veya grup olarak çalıştırabilirsiniz.  Varsayılan olarak, Service Fabric uygulamalar Fabric.exe işleminin altında çalıştığı hesap altında çalışır. Uygulamaları, paylaşılan barındırılan bir ortamda bile farklı hesaplar altında çalıştırmak, bunları diğerinden daha güvenli hale getirir. Bunun Active Directory Azure Active Directory (Azure AD) değil, etki alanınız içinde şirket içinde kullandığını unutmayın.  Ayrıca, bir hizmeti [Grup yönetilen hizmet hesabı (gMSA)](service-fabric-run-service-as-gmsa.md)olarak da çalıştırabilirsiniz.

Bir etki alanı kullanıcısı veya grubu kullanarak, izin verilen etki alanındaki diğer kaynaklara (örneğin, dosya paylaşımları) erişebilirsiniz.

Aşağıdaki örnek, *Benimcert* adlı bir sertifika kullanarak etki alanı parolalarını şifreleyen *testuser* adlı bir Kullanıcı Active Directory gösterir. `Invoke-ServiceFabricEncryptText`Gizli şifreleme metnini oluşturmak için PowerShell komutunu kullanabilirsiniz. Ayrıntılar için bkz. [Service Fabric uygulamalarında gizli dizileri yönetme](service-fabric-application-secret-management.md) .

Bant dışı bir yöntem kullanarak yerel makineye parolanın şifresini çözmek için sertifikanın özel anahtarını dağıtmanız gerekir (Azure 'da bu, Azure Resource Manager aracılığıyla yapılır). Daha sonra, Service Fabric hizmet paketini makineye dağıttığında, bu kimlik bilgileri altında çalıştırmak için Active Directory ve (Kullanıcı adıyla birlikte) kimlik doğrulaması yapabilir.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Bir hizmete RunAs ilkesi uygularsanız ve hizmet bildirimi uç nokta kaynaklarını HTTP protokolüyle bildirirse, bir **Securityaccesspolicy** de belirtmeniz gerekir.  Daha fazla bilgi için bkz. [http ve HTTPS uç noktaları için güvenlik erişimi Ilkesi atama](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Sonraki adım olarak, aşağıdaki makaleleri okuyun:
* [Uygulama modelini anlama](service-fabric-application-model.md)
* [Hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md)
* [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
