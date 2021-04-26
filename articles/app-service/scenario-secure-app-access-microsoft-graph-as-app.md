---
title: Öğretici-uygulama olarak Microsoft Graph Web uygulaması erişim | Mavisi
description: Bu öğreticide, Yönetilen kimlikler kullanarak Microsoft Graph verilere erişmeyi öğreneceksiniz.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 5bb52799836b1975de9d936e04fb53987effb300
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832635"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Öğretici: uygulama olarak güvenli bir uygulamadan Microsoft Graph erişin

Azure App Service çalıştıran bir Web uygulamasından Microsoft Graph erişme hakkında bilgi edinin.

:::image type="content" alt-text="Microsoft Graph erişimi gösteren diyagram." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Web uygulaması için Microsoft Graph çağırmak istiyorsunuz. Web uygulamanıza verilere erişim sağlamanın güvenli bir yolu, [sistem tarafından atanan yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md)kullanmaktır. Azure Active Directory yönetilen bir kimlik, App Service rol tabanlı erişim denetimi (RBAC) aracılığıyla uygulama kimlik bilgileri gerekmeden kaynaklara erişmesini sağlar. Web uygulamanıza yönetilen bir kimlik atadıktan sonra, Azure bir sertifikanın oluşturulmasını ve dağıtımını gerçekleştirir. Gizli dizileri veya uygulama kimlik bilgilerini yönetme konusunda endişelenmeniz gerekmez.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Bir Web uygulaması üzerinde sistem tarafından atanan yönetilen kimlik oluşturma.
> * Yönetilen bir kimliğe Microsoft Graph API izinleri ekleyin.
> * Yönetilen kimlikler kullanarak bir Web uygulamasından Microsoft Graph çağırın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [App Service kimlik doğrulama/yetkilendirme modülü etkinleştirilmiş](scenario-secure-app-authentication-app-service.md)Azure App Service üzerinde çalışan bir Web uygulaması.

## <a name="enable-managed-identity-on-app"></a>Uygulamada yönetilen kimliği etkinleştir

Web uygulamanızı Visual Studio aracılığıyla oluşturup yayımlarsanız, yönetilen kimlik sizin için uygulamanızda etkinleştirilmiştir. App Service 'te, sol bölmedeki **kimlik** ' i seçin ve ardından **sistem atandı**' ı seçin. **Durumun** **Açık** olarak ayarlandığını doğrulayın. Aksi takdirde, **Kaydet** ' i seçin ve ardından, sistem tarafından atanan yönetilen kimliği etkinleştirmek için **Evet** ' i seçin. Yönetilen kimlik etkinleştirildiğinde, durum **Açık** olarak ayarlanır ve nesne kimliği kullanılabilir.

Sonraki adımda ihtiyacınız olacak **nesne kimliği** değerini bir yere göz atın.

:::image type="content" alt-text="Sistem tarafından atanan kimliği gösteren ekran görüntüsü." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Microsoft Graph erişim izni verme

Microsoft Graph erişirken, yönetilen kimliğin gerçekleştirmek istediği işlem için doğru izinlere sahip olması gerekir. Şu anda Azure portal bu izinleri atama seçeneği yoktur. Aşağıdaki betik, istenen Microsoft Graph API izinlerini yönetilen kimlik hizmeti sorumlusu nesnesine ekler.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Betiği yürüttükten sonra, istenen API izinlerinin yönetilen kimliğe atandığını [Azure Portal](https://portal.azure.com) doğrulayabilirsiniz.

**Azure Active Directory** gidin ve **Kurumsal uygulamalar**' ı seçin. Bu bölme, kiracınızdaki tüm hizmet sorumlularını görüntüler. **Tüm uygulamalarda**, yönetilen kimlik için hizmet sorumlusu ' nı seçin. 

Bu öğreticiyi takip ediyorsanız, aynı görünen ada sahip iki hizmet sorumlusu vardır (örneğin, SecureWebApp2020094113531). **Giriş sayfası URL 'si** olan hizmet sorumlusu kiracınızdaki Web uygulamasını temsil eder. **Giriş sayfası URL 'si** olmadan hizmet sorumlusu, Web uygulamanız için sistem tarafından atanan yönetilen kimliği temsil eder. Yönetilen kimliğin **nesne kimliği** değeri, daha önce oluşturduğunuz yönetilen KIMLIğIN nesne kimliğiyle eşleşir.

Yönetilen kimlik için hizmet sorumlusunu seçin.

:::image type="content" alt-text="Tüm uygulamalar seçeneğini gösteren ekran görüntüsü." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

**Genel bakış** bölümünde **izinler**' i seçin ve Microsoft Graph için eklenen izinleri görürsünüz.

:::image type="content" alt-text="Izinler bölmesini gösteren ekran görüntüsü." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Microsoft Graph çağrısı (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfı, Microsoft Graph istekleri yetkilendirmek üzere kodunuzun belirteç kimlik bilgilerini almak için kullanılır. Belirteçleri getirmek ve bunları hizmet istemcisine eklemek için yönetilen kimliği kullanan [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfının bir örneğini oluşturun. Aşağıdaki kod örneği, kimliği doğrulanmış belirteç kimlik bilgisini alır ve onu gruptaki kullanıcıları alan bir hizmet istemci nesnesi oluşturmak için kullanır.

Bu kodu örnek bir uygulamanın parçası olarak görmek için [GitHub 'daki örneğe](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)bakın.

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Microsoft. Identity. Web. MicrosoftGraph istemci kitaplığı paketini yükler

.NET Core komut satırı arabirimini veya Visual Studio 'da Paket Yöneticisi konsolunu kullanarak [Microsoft. Identity. Web. MicrosoftGraph NuGet paketini](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) projenize yükler.

# <a name="command-line"></a>[Komut satırı](#tab/command-line)

Bir komut satırı açın ve proje dosyanızı içeren dizine geçiş yapın.

Install komutlarını çalıştırın.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Paket Yöneticisi](#tab/package-manager)

Visual Studio 'da projeyi/çözümü açın ve **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** komutunu kullanarak konsolunu açın.

Install komutlarını çalıştırın.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

---

### <a name="example"></a>Örnek

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyi bitirdiyseniz ve artık Web uygulamasına veya ilişkili kaynaklara ihtiyacınız yoksa, [oluşturduğunuz kaynakları temizleyin](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Bir Web uygulaması üzerinde sistem tarafından atanan yönetilen kimlik oluşturma.
> * Yönetilen bir kimliğe Microsoft Graph API izinleri ekleyin.
> * Yönetilen kimlikler kullanarak bir Web uygulamasından Microsoft Graph çağırın.

Bir [.NET Core uygulamasını](tutorial-dotnetcore-sqldb-app.md), [Python uygulamasını](tutorial-python-postgresql-app.md), [Java uygulamasını](tutorial-java-spring-cosmosdb.md)veya [Node.js uygulamasını](tutorial-nodejs-mongodb-app.md) bir veritabanına bağlamayı öğrenin.
