---
title: Belirteç önbelleği serileştirme (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak belirteç önbelleğinin serileştirme ve müşteri serileştirilmesi hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 60ce3d32ffa20fc9117890528eac053d1af9fdf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583917"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET içinde belirteç önbelleği serileştirme
[Belirteç](msal-acquire-cache-tokens.md)alındıktan sonra, Microsoft kimlik doğrulama KITAPLıĞı (msal) tarafından önbelleğe alınır.  Uygulama kodu, başka bir yöntem tarafından belirteç almadan önce önbellekten bir belirteç almayı denemelidir.  Bu makalede, MSAL.NET içinde belirteç önbelleğinin varsayılan ve özel serileştirmesi açıklanmaktadır.

Bu makale, MSAL.NET 3. x içindir. MSAL.NET 2. x ile ilgileniyorsanız, bkz. [msal.NET 2. x Içinde belirteç önbelleği serileştirme](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Mobil platformlar için varsayılan serileştirme

MSAL.NET ' de, bir bellek içi belirteç önbelleği varsayılan olarak sağlanır. Varsayılan olarak serileştirme, bir kullanıcı için platformun bir parçası olarak güvenli depolamanın kullanılabildiği platformlar için sağlanır. Bu durum Evrensel Windows Platformu (UWP), Xamarin. iOS ve Xamarin. Android için de kullanılır.

> [!Note]
> Bir Xamarin. Android projesini MSAL.NET 1. x ' den MSAL.NET 3. x ' e geçirdiğinizde, `android:allowBackup="false"` Visual Studio dağıtımları yerel depolama 'nın geri yüklemesini tetikleyeceğinden eski önbelleğe alınmış belirteçlerin geri gelmesinden kaçınmak için projenize eklemek isteyebilirsiniz. Bkz. [sorun #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Windows Masaüstü uygulamaları ve Web uygulamaları/Web API 'Leri için özel serileştirme

Özel serileştirme, mobil platformlarda (UWP, Xamarin. iOS ve Xamarin. Android) kullanılabilir olmadığını unutmayın. MSAL zaten bu platformlar için güvenli ve performanslı bir serileştirme mekanizması tanımlıyor. Ancak .NET masaüstü ve .NET Core Uygulamaları, değişen mimarilere sahiptir ve MSAL genel amaçlı bir serileştirme mekanizması uygulayamaz. Örneğin, Web siteleri belirteçleri bir Redsıs önbelleğinde depolamayı ya da masaüstü apps 'in belirteçleri şifrelenmiş bir dosyada depolamayı seçebilir. Bu nedenle serileştirme sağlanmamış. .NET masaüstü veya .NET Core 'ta kalıcı bir belirteç önbelleği uygulamasına sahip olmak için serileştirme 'yı özelleştirin.

Aşağıdaki sınıflar ve arabirimler, belirteç önbelleği serileştirmesi içinde kullanılır:

- `ITokenCache`, belirteç önbelleği serileştirme isteklerine abone olmak için olayları ve çeşitli biçimlerde (ADAL v 3.0, MSAL 2. x ve MSAL 3. x = ADAL v 5.0) önbelleğe alma veya seri hale getirme yöntemlerini tanımlar.
- `TokenCacheCallback` , serileştirme işlemini işleyebilmeniz için olaylara geçirilmiş bir geri çağırma işlemi. Bunlar, türündeki bağımsız değişkenlerle çağırılır `TokenCacheNotificationArgs` .
- `TokenCacheNotificationArgs` yalnızca `ClientId` uygulamanın ve belirtecin kullanılabildiği kullanıcıya bir başvuru sağlar.

  ![Sınıf diyagramı](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET sizin için belirteç önbellekleri oluşturur ve `IToken` bir uygulamanın ve özelliklerini çağırdığınızda önbellek sağlar `UserTokenCache` `AppTokenCache` . Arabirimi kendiniz uygulamanız gerekmez. Özel bir belirteç önbelleği serileştirmesi uyguladığınızda, sorumluluğu şu şekilde olur:
> - `BeforeAccess`Ve `AfterAccess` "olaylara" (ya da zaman uyumsuz türleri) tepki verir. Bu `BeforeAccess` temsilci önbelleğin serisini kaldırmak, ancak `AfterAccess` önbelleğin serileştirilmesinden sorumludur.
> - Bu olay deposunun veya yükleme bloblarının bir parçası olarak olay bağımsız değişkeni aracılığıyla istediğiniz depolama alanına geçirilir.

Bir [genel istemci uygulaması](msal-client-applications.md) (Masaüstü) veya [Gizli istemci uygulaması](msal-client-applications.md)için bir belirteç önbelleği serileştirmesi yazıyorsanız, Stratejiler farklılık açıktır. (Web uygulaması/Web API 'si, Daemon uygulaması).

### <a name="token-cache-for-a-public-client"></a>Ortak istemci için belirteç önbelleği

MSAL.NET v2. x, bir genel istemcinin belirteç önbelleğini serileştirmek için birkaç seçeneğiniz vardır. Önbelleği yalnızca MSAL.NET biçimine seri hale getirebilirsiniz (Birleşik biçim önbelleği MSAL ve platformlar arasında ortaktır).  ADAL v3 'in [eski](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) belirteç önbelleği serileştirmesini de destekleyebilirsiniz.

Belirteç önbelleği serileştirmesini özelleştirmek için ADAL.NET 3. x, ADAL.NET 5. x ve MSAL.NET arasındaki çoklu oturum açma durumunu paylaşmak için özelleştirme aşağıdaki örneğin bir parçası olarak açıklanmaktadır: [Active-Directory-DotNet-v1--v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> MSAL.NET 1.1.4-Preview belirteci önbellek biçimi artık MSAL 2. x içinde desteklenmiyor. MSAL.NET 1. x kullanan uygulamalarınız varsa, kullanıcılarınızın yeniden oturum açması gerekir. Alternatif olarak, ADAL 4. x (ve 3. x) ' den geçiş desteklenir.

#### <a name="simple-token-cache-serialization-msal-only"></a>Basit belirteç önbelleği serileştirme (yalnızca MSAL)

Masaüstü uygulamaları için bir belirteç önbelleğinin özel serileştirilmesi Naïve uygulamasının bir örneği aşağıda verilmiştir. Burada, kullanıcı belirteci önbelleği, uygulamayla aynı klasördeki bir dosyadır.

Uygulamayı oluşturduktan sonra, `TokenCacheHelper.EnableSerialization()` yöntemini çağırarak ve uygulamayı geçirerek serileştirme etkinleştirilir `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper`Yardımcı sınıfı şöyle tanımlanır:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Ortak istemci uygulamalarına yönelik ürün kalitesi belirteç önbelleği dosya tabanlı serileştirici (Windows, Mac ve Linux üzerinde çalışan masaüstü uygulamaları için), [Microsoft. Identity. Client. Extensions. msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) açık kaynak kitaplığından edinilebilir. Bunu uygulamalarınıza şu NuGet paketinden dahil edebilirsiniz: [Microsoft. Identity. Client. Extensions. msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Çift belirteç önbelleği serileştirme (MSAL Birleşik önbellek ve ADAL v3)

Her ikisi de birleştirilmiş önbellek biçimiyle (ADAL.NET 4. x, MSAL.NET 2. x ve aynı kuşak ya da daha eski olan diğer Msallar, aynı platformda) belirteç önbelleği serileştirme uygulamak istiyorsanız aşağıdaki koda göz atın:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Bu zaman yardımcı sınıfı şu şekilde tanımlanır:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web uygulaması için belirteç önbelleği (gizli istemci uygulaması)

Web Apps veya Web API 'Lerinde, önbellek oturum, Redsıs önbelleği veya bir veritabanı aracılığıyla faydalanabilir. Web Apps veya Web API 'Lerinde her hesap için bir belirteç önbelleğini tutmanız gerekir. 

Web Apps için, belirteç önbelleğinin hesap KIMLIĞI tarafından anahtarlanır olması gerekir.

Web API 'Leri için, bu hesabın API 'yi çağırmak için kullanılan belirtecin karması ile anahtarlanır olması gerekir.

MSAL.NET, .NET Framework ve .NET Core alt platformlarında özel belirteç önbelleği serileştirmesini sağlar. Olaylar, önbelleğe erişildiğinde harekete geçirilir, uygulamalar önbelleğin serileştirip serileştirmeyeceğini veya seri durumdan çıkaramayacağını seçebilir. Kullanıcıları işleyen gizli istemci uygulamalarında (kullanıcıların oturum açması ve Web API 'Lerini çağıran Web uygulamaları ve aşağı akış Web API 'lerini çağıran Web API 'Leri), birçok kullanıcı olabilir ve kullanıcılar paralel olarak işlenir. Güvenlik ve performans nedenleriyle, önerimiz Kullanıcı başına bir önbellek serileştirilmemiz olur. Serileştirme olayları, işlenen kullanıcının kimliğine bağlı olarak bir önbellek anahtarını hesaplar ve bu kullanıcı için bir belirteç önbelleğinin serileştirilmesi/serisini kaldıramıyor.

[Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web) kitaplığı, belirteç önbelleği serileştirmesini içeren [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) Önizleme paketi sağlar:

| Genişletme yöntemi | Microsoft. Identity. Web Sub ad alanı | Description  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | Bellek belirteci önbelleği serileştirme. Bu uygulama örneklerde harika. Ayrıca, Web uygulaması yeniden başlatıldığında belirteç önbelleğinin kaybolup olmadığını aklınızda bulundurmayabilmeniz için üretim uygulamalarında da iyidir. `AddInMemoryTokenCaches``MsalMemoryTokenCacheOptions`, kullanılmadığı takdirde önbellek girişinin süresinin dolacağı süreyi belirtmenize olanak tanıyan, isteğe bağlı bir tür parametresi alır.
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | Belirteç önbelleği Kullanıcı oturumuna bağlanır. Tanımlama bilgisi çok büyük hale gelecağından KIMLIK belirteci çok sayıda talep içeriyorsa bu seçenek ideal değildir.
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | Belirteç önbelleği, ASP.NET Core uygulamasına karşı bir bağdaştırıcıdır `IDistributedCache` , bu nedenle dağıtılmış bellek önbelleği, redsıs önbelleği, dağıtılmış bir NCache veya bir SQL Server önbelleği arasından seçim yapabilirsiniz. Uygulamalar hakkındaki ayrıntılar için `IDistributedCache` bkz https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache ..

İşte, bir ASP.NET Core uygulamasında [Başlangıç](/aspnet/core/fundamentals/startup) sınıfının [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) yönteminde bellek içi önbelleğin kullanılmasına bir örnek:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

Olası Dağıtılmış önbelleklere örnek olarak şunlar verilebilir:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Kullanımları, aşama [2-2 belirteç önbelleğindeki](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) [ASP.NET Core Web uygulaması öğreticisinde](/aspnet/core/tutorials/first-mvc-app/) öne çıkanlar.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki örneklerde belirteç önbelleği serileştirmesi gösterilmektedir.

| Örnek | Platform | Description|
| ------ | -------- | ----------- |
|[Active-Directory-DotNet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Masaüstü (WPF) | Microsoft Graph API 'sini çağıran Windows Masaüstü .NET (WPF) uygulaması. ![Diyagramda, bir belirteci etkileşimli bir şekilde alarak ve Microsoft Graph için Azure A 'da masaüstü uygulaması W P F TodoListClient ile bir topoloji gösterilmektedir.](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-DotNet-v1--v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Masaüstü (konsol) | Azure AD v 1.0 uygulamalarının (ADAL.NET kullanarak) Microsoft Identity platform uygulamalarına (MSAL.NET kullanılarak) geçişini gösteren Visual Studio çözümleri kümesi. Özellikle, bkz. [belirteç önbelleği geçişi](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
