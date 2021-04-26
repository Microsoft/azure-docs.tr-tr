---
title: .NET SDK kullanarak purview hesabı oluşturma
description: .NET SDK kullanarak bir Azure purview hesabı oluşturun.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587808"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Hızlı başlangıç: .NET SDK kullanarak bir purview hesabı oluşturma

Bu hızlı başlangıçta, .NET SDK kullanarak bir Azure purview hesabı oluşturma açıklanmaktadır 

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Kendi [Azure Active Directory kiracınız](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Hesabınız abonelikte kaynak oluşturma iznine sahip olmalıdır

* Tüm uygulamaların **depolama hesabı** ve **EventHub ad alanı** oluşturmasını engelleyen **Azure Ilkeniz** varsa, bir purview hesabı oluşturma işlemi sırasında girilebilecek şekilde etiketi kullanarak ilke özel durumu yapmanız gerekir. Ana neden her bir purview hesabının oluşturulması, bir yönetilen kaynak grubu ve bu kaynak grubu, bir depolama hesabı ve bir EventHub ad alanı içinde oluşturulması gerekir. Daha fazla bilgi için [bkz. Katalog portalı oluşturma](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

Bu makaledeki izlenecek yol, Visual Studio 2019 kullanır. Visual Studio 2013, 2015 veya 2017 yordamları biraz farklı.

### <a name="azure-net-sdk"></a>Azure .NET SDK’sı

[Azure .NET SDK’sını](https://azure.microsoft.com/downloads/) indirip makinenize yükleyin.

## <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory’de uygulama oluşturma

*Nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma* bölümündeki bölümden, bu görevleri yapmak için yönergeleri izleyin:

1. [Azure Active Directory uygulama oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)bölümünde, bu öğreticide oluşturmakta olduğunuz .NET uygulamasını temsil eden bir uygulama oluşturun. Oturum açma URL'si için, makalede gösterildiği gibi bir işlevsiz URL sağlayabilirsiniz (`https://contoso.org/exampleapp`).
2. [Oturum açmak için değerleri Al](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)' da, **uygulama KIMLIĞI** ve **Kiracı kimliği**' ni alın ve bu öğreticide daha sonra kullanacağınız bu değerleri unutmayın. 
3. [Sertifikalar ve gizlilikler](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)' da, **kimlik doğrulama anahtarını** alın ve bu öğreticide daha sonra kullanacağınız bu değeri unutmayın.
4. Uygulamayı [bir role ata](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)bölümünde, uygulamanın abonelikte veri fabrikaları oluşturabilmesi için uygulamayı abonelik düzeyinde **katkıda** bulunan rolüne atayın.

## <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

Ardından, Visual Studio 'da bir C# .NET konsol uygulaması oluşturun:

1. **Visual Studio 'yu** başlatın.
2. Başlangıç penceresinde **Yeni proje**  >  **konsol uygulaması (.NET Framework)** oluştur ' u seçin. .NET sürüm 4.5.2 veya üzeri gereklidir.
3. **Proje adı** alanına **purviewquickstart** yazın.
4. Projeyi oluşturmak için **Oluştur**'u seçin.

## <a name="install-nuget-packages"></a>NuGet paketlerini yükleme

1. **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**'nu seçin.
2. **Paket Yöneticisi konsolu** bölmesinde, paketleri yüklemek için aşağıdaki komutları çalıştırın. Daha fazla bilgi için bkz. [Microsoft. Azure. Management. purview NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Bir takip görünümü istemcisi oluşturma

1. **Program.cs** dosyasını açın ve ad alanlarına başvurular eklemek için aşağıdaki deyimleri ekleyin.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Aşağıdaki kodu, değişkenleri ayarlayan **Main** yöntemine ekleyin. Yer tutucuları kendi değerlerinizle değiştirin. Purview 'ın Şu anda kullanılabildiği Azure bölgelerinin bir listesi için **Azure takip görünümünde** arama yapın ve şu sayfada ilgilendiğiniz bölgeleri seçin: [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Aşağıdaki kodu, **Purviewmanagementclient** sınıfının bir örneğini oluşturan **Main** yöntemine ekleyin. Bu nesneyi bir purview hesabı oluşturmak için kullanırsınız.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Bir purview hesabı oluşturun

Aşağıdaki kodu bir **purview hesabı** oluşturan **Main** yöntemine ekleyin. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı derleyin ve başlatın, ardından yürütmeyi doğrulayın.

Konsol, purview hesabı oluşturma ilerlemesini yazdırır.

### <a name="sample-output"></a>Örnek çıktı

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Çıktıyı doğrulama

[Azure Portal](https://portal.azure.com) takip edilecek **hesaplar** sayfasına gidin ve yukarıdaki kodu kullanarak oluşturulan hesabı doğrulayın. 

## <a name="delete-purview-account"></a>Purview hesabını Sil

Bir purview hesabını programlı olarak silmek için, aşağıdaki kod satırlarını programa ekleyin: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Purview hesap adının kullanılabilir olup olmadığını denetleyin

Bir sağlamayla hesabının kullanılabilirliğini denetlemek için aşağıdaki kodu kullanın: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Ad kullanılabiliyorsa ve ' false ' olarak ayarlanırsa, yukarıdaki kod Yazdır ' true ' şeklindedir.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticideki kod, bir sağlamayla hesabı oluşturur, bir takip hesabı siler ve sağlamayla hesabının AD kullanılabilirliğini denetler. Artık .NET SDK 'sını indirebilir ve bir purview hesabı için gerçekleştirebileceğiniz diğer kaynak sağlayıcısı eylemleri hakkında bilgi edinebilirsiniz.

Kullanıcıların Azure purview hesabınıza erişmesine nasıl izin vereceğinizi öğrenmek için sonraki makaleye ilerleyin. 

> [!div class="nextstepaction"]
> [Azure purview hesabınıza kullanıcı ekleme](catalog-permissions.md)
