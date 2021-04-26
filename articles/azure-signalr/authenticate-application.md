---
title: Azure SignalR hizmetine erişmek için bir uygulamanın kimliğini doğrulama
description: Bu makale, Azure SignalR hizmetine erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama hakkında bilgi sağlar
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092600"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Azure SignalR hizmetine erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama
Microsoft Azure, Azure Active Directory (Azure AD) tabanlı kaynaklar ve uygulamalar için tümleşik erişim denetimi yönetimi sağlar. Azure SignalR hizmeti ile Azure AD kullanmanın önemli bir avantajı, kimlik bilgilerinizi artık kodda depolamanıza gerek kalmaz. Bunun yerine, Microsoft Identity platform 'dan bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Belirteç istemek için kaynak adı `https://signalr.azure.com/` . Azure AD, uygulamayı çalıştıran güvenlik sorumlusunun (bir uygulama, kaynak grubu veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya bir erişim belirteci döndürür ve uygulama Azure SignalR hizmet kaynaklarına yönelik istekleri yetkilendirmek için erişim belirtecini kullanabilir.

Azure AD güvenlik sorumlusuna bir rol atandığında Azure bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu veya Azure SignalR kaynağı kapsamına eklenebilir. Azure AD güvenliği, bir kullanıcıya, gruba, uygulama hizmeti sorumlusuna veya [Azure kaynakları için yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md)roller atayabilir. 

> [!NOTE]
> Rol tanımı, izinlerden oluşan bir koleksiyondur. Rol tabanlı erişim denetimi (RBAC), bu izinlerin rol ataması aracılığıyla nasıl uygulanacağını denetler. Rol ataması üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam. Daha fazla bilgi için bkz. [farklı rolleri anlama](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uygulamanızı bir Azure AD kiracısıyla kaydetme
Azure SignalR hizmeti kaynaklarını yetkilendirmek için Azure AD kullanmanın ilk adımı, uygulamanızı [Azure Portal](https://portal.azure.com/)BIR Azure AD kiracısıyla kaydediyor. Uygulamanızı kaydettiğinizde AD 'ye uygulama hakkında bilgi sağlarsınız. Daha sonra Azure AD, uygulamanızı Azure AD çalışma zamanı ile ilişkilendirmek için kullanabileceğiniz bir istemci KIMLIĞI (uygulama KIMLIĞI olarak da bilinir) sağlar. İstemci KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın. 

Aşağıdaki görüntüler, bir Web uygulamasını kaydetme adımlarını göstermektedir:

![Bir uygulamayı kaydetme](./media/authenticate/app-registrations-register.png)

> [!Note]
> Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, yeniden yönlendirme URI 'SI için geçerli bir URI belirtebilirsiniz. Yerel uygulamalar için, bu değerin gerçek bir URL olması gerekmez. Web uygulamaları için, yeniden yönlendirme URI 'si, belirteçlerin sağlandığı URL 'YI belirttiğinden geçerli bir URI olmalıdır.

Uygulamanızı kaydettikten sonra **Ayarlar** altında **uygulama (istemci) kimliğini** görürsünüz:

![Kayıtlı uygulamanın uygulama KIMLIĞI](./media/authenticate/application-id.png)

Bir uygulamayı Azure AD 'ye kaydetme hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>İstemci parolası oluşturma   
Uygulamanın bir belirteç istenirken kimliğini kanıtlamak için bir istemci parolası gerekir. İstemci parolasını eklemek için aşağıdaki adımları izleyin.

1. Azure portal uygulama kaydınız ' ne gidin.
1. **Sertifikalar & gizli** dizi ayarını seçin.
1. **İstemci** gizli dizileri altında yeni bir gizli dizi oluşturmak için **yeni istemci parolası** ' nı seçin.
1. Gizli dizi için bir açıklama sağlayın ve istenen süre sonu aralığını seçin.
1. Yeni Gizliliğin değerini hemen güvenli bir konuma kopyalayın. Fill değeri size yalnızca bir kez görüntülenir.

![Istemci parolası oluşturma](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Sertifikayı karşıya yükle

Ayrıca, bir istemci parolası oluşturmak yerine bir sertifikayı karşıya yükleyebilirsiniz.

![Sertifikayı karşıya yükle](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal kullanarak Azure rolleri atama  
Azure RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme hakkında daha fazla bilgi edinmek için [Bu makaleye](..//role-based-access-control/role-assignments-portal.md)bakın. 

Bir rol ataması için uygun kapsamı belirledikten sonra, Azure portal bu kaynağa gidin. Kaynak için erişim denetimi (ıAM) ayarlarını görüntüleyin ve rol atamalarını yönetmek için aşağıdaki yönergeleri izleyin:

1. [Azure Portal](https://portal.azure.com/), SignalR kaynağına gidin.
1. Azure SignalR için erişim denetimi ayarlarını göstermek üzere **Access Control (IAM)** seçeneğini belirleyin. 
1. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin. Araç çubuğunda **Ekle** düğmesini seçin ve ardından **rol ataması Ekle**' yi seçin. 

    ![Araç çubuğuna düğme Ekle](./media/authenticate/role-assignments-add-button.png)

1. **Rol ataması Ekle** sayfasında, aşağıdaki adımları uygulayın:
    1. Atamak istediğiniz **Azure SignalR rolünü** seçin. 
    1. Rolü atamak istediğiniz **güvenlik sorumlusunu** (Kullanıcı, Grup, hizmet sorumlusu) bulmak için arama yapın.
    1. Rol atamasını kaydetmek için **Kaydet** ' i seçin. 

        ![Uygulamaya rol atama](./media/authenticate/assign-role-to-application.png)

    1. Rolü atadığınız kimlik söz konusu rol altında listelenir. Örneğin, aşağıdaki görüntüde uygulamanın, `signalr-dev` `signalr-service` SignalR App Server rolünde olduğunu gösterir. 
        
        ![Rol atama listesi](./media/authenticate/role-assignment-list.png)

Kaynak grubuna veya aboneliğe kapsamlı bir rol atamak için de benzer adımları izleyebilirsiniz. Rolü ve kapsamını tanımladıktan sonra bu davranışı [Bu GitHub konumundaki](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)örneklerle test edebilirsiniz.

## <a name="sample-codes-while-configuring-your-app-server"></a>Uygulama sunucunuzu yapılandırırken örnek kodlar.

Şu durumlarda aşağıdaki seçenekleri ekleyin `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Ya da yalnızca `ConnectionString` dosyanızı yapılandırmanız yeterlidir `appsettings.json` .

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Kullanırken, `certificate` öğesini şöyle değiştirin `clientSecret` `clientCert` :

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Sonraki adımlar
- RBAC hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)nedir?
- Azure rol atamalarını Azure PowerShell, Azure CLı veya REST API ile atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure PowerShell ile yönetme](../role-based-access-control/role-assignments-powershell.md)  
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure CLı ile yönetme](../role-based-access-control/role-assignments-cli.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) REST API ile yönetme](../role-based-access-control/role-assignments-rest.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure Resource Manager şablonlarıyla yönetme](../role-based-access-control/role-assignments-template.md)

Aşağıdaki ilgili makalelere bakın:
- [Azure SignalR hizmetine erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md)
- [Azure Active Directory kullanarak Azure SignalR hizmetine erişim yetkisi verme](authorize-access-azure-active-directory.md)