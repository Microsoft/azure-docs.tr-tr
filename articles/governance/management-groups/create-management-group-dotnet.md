---
title: 'Hızlı başlangıç: .NET Core ile bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için .NET Core 'u kullanırsınız.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592594"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Hızlı başlangıç: .NET Core ile bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- _ClientID_ ve _ClientSecret_ dahil olmak üzere bir Azure hizmet sorumlusu. Azure Ilkesiyle kullanılmak üzere bir hizmet sorumlusu yoksa veya yeni bir tane oluşturmak istiyorsanız, bkz. [.NET Yönetim kitaplıkları Için Azure kimlik doğrulaması](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sonraki adımlarda bunu yapacağımız için .NET Core paketlerini yüklemek için adımı atlayın.

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Uygulama kurulumu

Yönetim gruplarını yönetmek üzere .NET Core 'u etkinleştirmek için yeni bir konsol uygulaması oluşturun ve gerekli paketleri yükler.

1. En son .NET Core 'un yüklü olduğundan emin olun (en azından **3.1.8**). Henüz yüklenmemişse, [DotNet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core)adresinden indirin.

1. "MgCreate" adlı yeni bir .NET Core konsol uygulaması başlatın:

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Dizinleri yeni proje klasörüne değiştirin ve Azure Ilkesi için gerekli paketleri yükler:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Varsayılan değerini `program.cs` aşağıdaki kodla değiştirin ve güncelleştirilmiş dosyayı kaydedin:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Konsol uygulaması oluşturun ve yayımlayın `mgCreate` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Yönetim grubunu oluşturma

Bu hızlı başlangıçta kök yönetim grubunda yeni bir yönetim grubu oluşturacaksınız.

1. `{run-folder}`Önceki komutla tanımladığınız dizinleri değiştirin `dotnet publish` .

1. Terminalde aşağıdaki komutu girin:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

- `{tenantId}` -Kiracı KIMLIĞINIZLE değiştirin
- `{clientId}` -Hizmet sorumlunun istemci KIMLIĞIYLE değiştirin
- `{clientSecret}` -Hizmet sorumlunun istemci gizli anahtarı ile değiştirin
- `{groupID}` -Yeni yönetim grubunuzun KIMLIĞIYLE değiştirin
- `{displayName}` -Yeni yönetim grubunuzun kolay adıyla değiştirin

Sonuç, kök yönetim grubundaki yeni bir yönetim grubudur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

- Portal üzerinden yeni yönetim grubunu silin.

- .NET Core konsol uygulamasını ve yüklü paketleri kaldırmak istiyorsanız `mgCreate` proje klasörünü silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)