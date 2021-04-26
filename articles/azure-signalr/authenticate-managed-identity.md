---
title: Azure Active Directory ile yönetilen bir kimlik doğrulama
description: Bu makalede, Azure SignalR hizmetine erişmek için Azure Active Directory ile yönetilen bir kimliğin kimlik doğrulaması hakkında bilgi sağlanır
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092558"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Azure SignalR kaynaklarına erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama
Azure SignalR hizmeti, [Azure kaynakları için yönetilen kimliklerle](../active-directory/managed-identities-azure-resources/overview.md)Azure Active Directory (Azure AD) kimlik doğrulamasını destekler. Azure kaynakları için Yönetilen kimlikler, Azure sanal makinelerinde (VM), Işlev uygulamalarında, sanal makine ölçek kümelerinde ve diğer hizmetlerde çalışan uygulamalardan Azure AD kimlik bilgilerini kullanarak Azure SignalR hizmeti kaynaklarına erişim yetkisi verebilir. Azure AD kimlik doğrulamasıyla birlikte Azure kaynakları için Yönetilen kimlikler kullanarak, kimlik bilgilerini bulutta çalışan uygulamalarınızla depolamaktan kaçınabilirsiniz.

Bu makalede bir Azure VM 'den yönetilen kimlik kullanarak bir Azure SignalR hizmetine erişim yetkisi verme işlemleri gösterilmektedir.

## <a name="enable-managed-identities-on-a-vm"></a>VM 'de yönetilen kimlikleri etkinleştirme
VM 'nizden Azure SIgnalR hizmeti kaynaklarını yetkilendirmek üzere Azure kaynakları için Yönetilen kimlikler kullanabilmeniz için önce VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirmeniz gerekir. Azure kaynakları için yönetilen kimliklerin nasıl etkinleştireceğinizi öğrenmek için şu makalelerden birine bakın:

- [Azure portalındaki](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonu](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager istemci kitaplıkları](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD 'de yönetilen bir kimliğe izin verme
Uygulamanızdaki yönetilen bir kimlikten Azure SignalR hizmetine yönelik bir istek yetkilendirmek için, önce bu yönetilen kimlik için rol tabanlı erişim denetimi (RBAC) ayarlarını yapılandırın. Azure SignalR hizmeti, veya alma izinlerini çevreleyen RBAC rollerini tanımlar `AccessKey` `ClientToken` . RBAC rolü yönetilen bir kimliğe atandığında, yönetilen kimliğe uygun kapsamda Azure SignalR hizmetine erişim verilir.

RBAC rolleri atama hakkında daha fazla bilgi için bkz. [Azure SignalR hizmeti kaynaklarına erişim için Azure Active Directory Ile kimlik doğrulama](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Yönetilen kimliklerle Azure SignalR hizmetine bağlanma
Yönetilen kimliklerle Azure SignalR hizmetine bağlanmak için, rol ve uygun kapsamın kimliğini atamanız gerekir. Bu bölümdeki yordam, yönetilen bir kimlik altında çalışan ve Azure SignalR hizmeti kaynaklarına erişen basit bir uygulama kullanır.

Burada örnek bir Azure sanal makine kaynağı kullanıyoruz.

1. **Ayarlar** ' a gidin ve **kimlik**' i seçin. 
1. Görüntülenecek **durumu** **seçin.** 
1. Ayarları kaydetmek için **Kaydet**’i seçin. 

    ![Bir sanal makine için yönetilen kimlik](./media/authenticate/identity-virtual-machine.png)

Bu ayarı etkinleştirdikten sonra, Azure Active Directory (Azure AD) ve App Service ana bilgisayarında yapılandırılmış yeni bir hizmet kimliği oluşturulur.

Şimdi, bu hizmet kimliğini Azure SignalR hizmet kaynaklarınızın gerekli kapsamındaki bir role atayın.

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

## <a name="samples-code-while-configuring-your-app-server"></a>Uygulama sunucunuzu yapılandırırken örnek kod

Şu durumlarda aşağıdaki seçenekleri ekleyin `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Sonraki adımlar
- RBAC hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)nedir?
- Azure rol atamalarını Azure PowerShell, Azure CLı veya REST API ile atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure PowerShell ile yönetme](../role-based-access-control/role-assignments-powershell.md)  
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure CLı ile yönetme](../role-based-access-control/role-assignments-cli.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) REST API ile yönetme](../role-based-access-control/role-assignments-rest.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure Resource Manager şablonlarıyla yönetme](../role-based-access-control/role-assignments-template.md)

Aşağıdaki ilgili makalelere bakın:
- [Azure SignalR hizmetine erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama](authenticate-application.md)
- [Azure Active Directory kullanarak Azure SignalR hizmetine erişim yetkisi verme](authorize-access-azure-active-directory.md)