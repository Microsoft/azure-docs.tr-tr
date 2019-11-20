---
title: .NET için Azure CDN kitaplığını kullanmaya başlama | Microsoft Docs
description: Visual Studio kullanarak Azure CDN'yi yönetmek üzere .NET uygulamalar yazmayı öğrenin.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594173"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN ile geliştirmeye başlama
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Kullanabileceğiniz [.NET için Azure CDN Kitaplığı](/dotnet/api/overview/azure/cdn) oluşturma ve CDN profili ve uç noktaları yönetimini otomatikleştirmek için.  Bu öğreticide, birkaç kullanılabilir işlemleri gösteren basit bir .NET konsol uygulaması oluşturulmasını adım adım göstermektedir.  Bu öğreticide, ayrıntılı olarak .NET için Azure CDN kitaplığı tüm yönlerini açıklamak için tasarlanmamıştır.

Bu öğreticiyi tamamlamak için Visual Studio 2015 ihtiyacınız vardır.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ücretsiz olarak indirebilirsiniz.

> [!TIP]
> [Projeyi bu öğreticiden](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) MSDN'de indirilebilir.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Projenizi oluşturmak ve Nuget paketleri Ekle
Size sunduğumuz CDN profili için bir kaynak grubu oluşturduğunuz ve CDN profili ve uç noktaları grubu içindeki yönetmek için Azure AD uygulama izin verilen göre biz uygulamamızı oluşturmaya başlayabilir.

İçinde Visual Studio 2015 ' i **dosya**, **yeni**, **proje...**  yeni proje iletişim kutusunu açın.  Genişletin **Visual C#** , ardından **Windows** soldaki bölmede.  Tıklayın **konsol uygulaması** Orta bölmedeki.  Projenizi adlandırın ve ardından tıklayın **Tamam**.  

![Yeni Proje](./media/cdn-app-dev-net/cdn-new-project.png)

Projemizin Nuget paketleri içinde yer alan bazı Azure kitaplıkları kullanmayı düşünüyor olabilir.  Bu projeye ekleyelim.

1. Tıklayın **Araçları** menüsünde **Nuget Paket Yöneticisi**, ardından **Paket Yöneticisi Konsolu**.
   
    ![Nuget paketlerini Yönet](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Paket Yöneticisi Konsolu'nda yüklemek için aşağıdaki komutu yürütün **Active Directory Authentication Library (ADAL)** :
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Yüklemek için aşağıdakileri yürütün **Azure CDN Yönetim kitaplığı**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Yönergeleri, sabitleri, main yöntemi ve yardımcı yöntemler
Yazılan programımız temel yapısını geçelim.

1. Program.cs sekmede geri değiştirin `using` üst aşağıdaki yönergeleri:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Bizim yöntemleri kullanacaksınız bazı sabitleri tanımlamak ihtiyacımız var.  İçinde `Program` sınıfı, ancak önce `Main` yöntemi aşağıdakileri ekleyin.  Dahil olmak üzere, yer tutucuları değiştirdiğinizden emin olun  **&lt;açılı ayraçlar&gt;** , gerektiğinde kendi değerlerinizle.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Ayrıca sınıf düzeyinde bu iki değişkenleri tanımlayın.  Bunlar daha sonra profil ve uç noktası zaten mevcut olup olmadığını belirlemek için kullanacağız.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Değiştirin `Main` yöntemini aşağıdaki şekilde:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Bizim diğer yöntemlerden bazıları "Evet/Hayır" sorularınız varsa kullanıcıdan istemek için oluşturacaksınız.  Bunu biraz kolaylaştırmak için aşağıdaki yöntemi ekleyin:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Temel yapısını programımız yazılır, çağıran yöntemleri oluşturmanız gerekir `Main` yöntemi.

## <a name="authentication"></a>Authentication
Azure CDN Yönetim kitaplığı kullanabiliriz önce bizim hizmet sorumlusu kimlik doğrulaması ve kimlik doğrulama belirteci almak ihtiyacımız var.  Bu yöntem, belirteci almak için ADAL kullanır.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Tek tek kullanıcı kimlik doğrulaması kullanıyorsanız `GetAccessToken` yöntemi biraz farklı görünür.

> [!IMPORTANT]
> Yalnızca bireysel kullanıcı kimlik doğrulaması yerine bir hizmet sorumlusu seçme durumlarda bu kod örneği kullanın.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Değiştirdiğinizden emin olun `<redirect URI>` yeniden yönlendirme URI'si uygulamayı Azure AD'ye kaydettiniz, girdiğiniz ile.

## <a name="list-cdn-profiles-and-endpoints"></a>Liste CDN profili ve uç noktaları
Artık CDN işlemleri gerçekleştirmek hazırız.  Bizim yöntemi yapacağı ilk şey profilleri ve uç noktaları, kaynak grubundaki listesidir ve bizim sabitlerle belirtilen profil ve uç nokta adları için bir eşleşme bulduğunda biz çoğaltmaları oluşturmak çalışmaması, Not daha sonra kullanmak üzere sağlar.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN profili ve uç noktaları oluşturma
Ardından, bir profil oluşturacağız.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Profil oluşturulduktan sonra bir uç nokta oluşturacağız.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Yukarıdaki örnek adlı bir kaynak uç noktası atar *Contoso* bir ana bilgisayar adı ile `www.contoso.com`.  Bu ana bilgisayar adı için kendi kaynağın işaret edecek şekilde değiştirmeniz gerekir.
> 
> 

## <a name="purge-an-endpoint"></a>Bir uç noktasını temizleme
Uç noktası oluşturuldu varsayıldığında, biz Programımıza gerçekleştirmek isteyebileceğiniz yaygın görevlerden biri içeriği bizim uç noktasını temizleme.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> Yukarıdaki örnekte, dize içinde `/*` bitiş noktası yolu kök dizinindeki her şeyi temizlemek istediğiniz gösterir.  Bu denetimi için eşdeğerdir **temizleme tüm** Azure portalının "temizleme" iletişim kutusunda. İçinde `CreateCdnProfile` yöntemi oluşturduğum bizim profili olarak bir **verizon'dan Azure CDN** kodu kullanarak profil `Sku = new Sku(SkuName.StandardVerizon)`, böylece bu başarılı olur.  Ancak, **akamai'den Azure CDN** profilleri desteklemez **temizleme tüm**, Bu öğretici için Akamai profili kullanıyordu, ı temizlemek için belirli yollar eklemeniz gerekir.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN profili ve uç noktalarını silme
Son yöntemleri, bizim uç nokta ve profili siler.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Programı çalıştırma
Biz artık derleyebilir ve program tıklayarak çalıştırın **Başlat** Visual Studio'daki düğmesi.

![Çalışan bir program](./media/cdn-app-dev-net/cdn-program-running-1.png)

Program yukarıdaki istemi ulaştığında, kaynak grubunuzda Azure portalına dönün ve profili oluşturulmuş olması gerekir.

![Başarılı!](./media/cdn-app-dev-net/cdn-success.png)

Biz, programın geri kalanını çalıştırmak için istemleri ardından doğrulayabilirsiniz.

![Program Tamamlanıyor](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Sonraki Adımlar
Bu kılavuz, tamamlanan projeden görmek için [örneği indirin](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

.NET için Azure CDN Yönetim kitaplığı hakkındaki ek belgeleri bulmak için görüntüleme [MSDN başvuru](/dotnet/api/overview/azure/cdn).

CDN kaynaklarınızı yönetmek [PowerShell](cdn-manage-powershell.md).

