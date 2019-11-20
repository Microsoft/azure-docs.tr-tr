---
title: .NET için Microsoft kimlik doğrulama kitaplığı 'nda belirteç önbelleği serileştirme
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak belirteç önbelleğinin serileştirme ve müşteri serileştirilmesi hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cae1a755b9f79ce2fd5084653f7b3c177f29832
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802735"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET içinde belirteç önbelleği serileştirme
[Belirteç](msal-acquire-cache-tokens.md)alındıktan sonra, Microsoft kimlik doğrulama KITAPLıĞı (msal) tarafından önbelleğe alınır.  Uygulama kodu, başka bir yöntem tarafından belirteç almadan önce önbellekten bir belirteç almayı denemelidir.  Bu makalede, MSAL.NET içinde belirteç önbelleğinin varsayılan ve özel serileştirmesi açıklanmaktadır.

Bu makale, MSAL.NET 3. x içindir. MSAL.NET 2. x ile ilgileniyorsanız, bkz. [msal.NET 2. x Içinde belirteç önbelleği serileştirme](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Mobil platformlar için varsayılan serileştirme

MSAL.NET ' de, bir bellek içi belirteç önbelleği varsayılan olarak sağlanır. Varsayılan olarak serileştirme, bir kullanıcı için platformun bir parçası olarak güvenli depolamanın kullanılabildiği platformlar için sağlanır. Bu durum Evrensel Windows Platformu (UWP), Xamarin. iOS ve Xamarin. Android için de kullanılır.

> [!Note]
> Bir Xamarin. Android projesini MSAL.NET 1. x ' den MSAL.NET 3. x ' e geçirdiğinizde, Visual Studio dağıtımları yerel depolamanın geri yüklemesini tetikleyeceğinden eski önbelleğe alınmış belirteçlerin geri gelmesinden kaçınmak için projenize `android:allowBackup="false"` eklemek isteyebilirsiniz. Bkz. [sorun #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Windows Masaüstü uygulamaları ve Web uygulamaları/Web API 'Leri için özel serileştirme

Özel serileştirme, mobil platformlarda (UWP, Xamarin. iOS ve Xamarin. Android) kullanılabilir olmadığını unutmayın. MSAL zaten bu platformlar için güvenli ve performanslı bir serileştirme mekanizması tanımlıyor. Ancak .NET masaüstü ve .NET Core Uygulamaları, değişen mimarilere sahiptir ve MSAL genel amaçlı bir serileştirme mekanizması uygulayamaz. Örneğin, Web siteleri belirteçleri bir Redsıs önbelleğinde depolamayı ya da masaüstü apps 'in belirteçleri şifrelenmiş bir dosyada depolamayı seçebilir. Bu nedenle serileştirme sağlanmamış. .NET masaüstü veya .NET Core 'ta kalıcı bir belirteç önbelleği uygulamasına sahip olmak için Serileştirmeyi özelleştirmeniz gerekir.

Aşağıdaki sınıflar ve arabirimler, belirteç önbelleği serileştirmesi içinde kullanılır:

- `ITokenCache`, belirteç önbelleği serileştirme isteklerine abone olan olayları ve farklı biçimlerde (ADAL v 3.0, MSAL 2. x ve MSAL 3. x = ADAL v 5.0) önbelleğin serileştirilmesi veya serileştirilmesi için Yöntemler tanımlar.
- `TokenCacheCallback` serileştirme işlemini işleyebilmeniz için olaylara geçirilen bir geri çağırma işlemi. `TokenCacheNotificationArgs`türü bağımsız değişkenlerle çağırılır.
- `TokenCacheNotificationArgs` yalnızca uygulamanın `ClientId` ve belirtecin kullanılabildiği kullanıcıya bir başvuru sağlar.

  ![Sınıf diyagramı](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET, sizin için belirteç önbellekleri oluşturur ve bir uygulamanın `UserTokenCache` ve `AppTokenCache` özelliklerini çağırdığınızda `IToken` önbelleği sağlar. Arabirimi kendiniz uygulamanız gerekmez. Özel bir belirteç önbelleği serileştirmesi uyguladığınızda, sorumluluğu şu şekilde olur:
> - `BeforeAccess` tepki verin ve "olaylar" (ya da zaman uyumsuz türleri) `AfterAccess`. `BeforeAccess` temsilcisi önbelleğin serisini kaldırmak, ancak `AfterAccess` bir önbelleğin serileştirilmesinden sorumludur.
> - Bu olay deposunun veya yükleme bloblarının bir parçası olarak olay bağımsız değişkeni aracılığıyla istediğiniz depolama alanına geçirilir.

Bir [genel istemci uygulaması](msal-client-applications.md) (Masaüstü) veya [Gizli istemci uygulaması](msal-client-applications.md)için bir belirteç önbelleği serileştirmesi yazıyorsanız, Stratejiler farklılık açıktır. (Web uygulaması/Web API 'si, Daemon uygulaması).

### <a name="token-cache-for-a-public-client"></a>Ortak istemci için belirteç önbelleği 

MSAL.NET v2. x, bir genel istemcinin belirteç önbelleğini serileştirmek için birkaç seçeneğiniz vardır. Önbelleği yalnızca MSAL.NET biçimine seri hale getirebilirsiniz (Birleşik biçim önbelleği MSAL ve platformlar arasında ortaktır).  ADAL v3 'in [eski](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) belirteç önbelleği serileştirmesini de destekleyebilirsiniz.

Belirteç önbelleği serileştirmesini özelleştirmek için ADAL.NET 3. x, ADAL.NET 5. x ve MSAL.NET arasındaki çoklu oturum açma durumunu paylaşmak için özelleştirme aşağıdaki örneğin bir parçası olarak açıklanmaktadır: [Active-Directory-DotNet-v1--v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> MSAL.NET 1.1.4-Preview belirteci önbellek biçimi artık MSAL 2. x içinde desteklenmiyor. MSAL.NET 1. x kullanan uygulamalarınız varsa, kullanıcılarınızın yeniden oturum açması gerekir. Alternatif olarak, ADAL 4. x (ve 3. x) ' den geçiş desteklenir.

#### <a name="simple-token-cache-serialization-msal-only"></a>Basit belirteç önbelleği serileştirme (yalnızca MSAL)

Masaüstü uygulamaları için bir belirteç önbelleğinin özel serileştirilmesi Naïve uygulamasının bir örneği aşağıda verilmiştir. Burada, kullanıcı belirteci önbelleği, uygulamayla aynı klasördeki bir dosyadır.

Uygulamayı oluşturduktan sonra, `TokenCacheHelper.EnableSerialization()` yöntemini çağırarak ve uygulamayı `UserTokenCache`geçirerek serileştirme etkinleştirilir.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` yardımcı sınıfı şöyle tanımlanır:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
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

Ortak istemci uygulamalarına yönelik bir ürün kalitesi belirteç önbelleği dosya tabanlı serileştirici Önizlemesi (Windows, Mac ve Linux üzerinde çalışan masaüstü uygulamaları için) [Microsoft. Identity. Client. Extensions. msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) açık kaynak kitaplığından bulunabilir. Bunu uygulamalarınıza şu NuGet paketinden dahil edebilirsiniz: [Microsoft. Identity. Client. Extensions. msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

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

Web Apps veya Web API 'Lerinde, önbellek oturum, Redsıs önbelleği veya bir veritabanı aracılığıyla faydalanabilir.

Web Apps veya Web API 'Lerinde, hesap başına tek bir belirteç önbelleği tutun.  Web Apps için, belirteç önbelleğinin hesap KIMLIĞI tarafından anahtarlanır olması gerekir.  Web API 'Leri için, bu hesabın API 'yi çağırmak için kullanılan belirtecin karması ile anahtarlanır olması gerekir. MSAL.NET, .NET Framework ve .NET Core alt platformlarında özel belirteç önbelleği serileştirmesini sağlar. Olaylar, önbelleğe erişildiğinde harekete geçirilir, uygulamalar önbelleğin serileştirip serileştirmeyeceğini veya seri durumdan çıkaramayacağını seçebilir. Kullanıcıları işleyen gizli istemci uygulamalarında (kullanıcıların oturum açması ve Web API 'Lerini çağıran Web uygulamaları ve aşağı akış Web API 'lerini çağıran Web API 'Leri), birçok kullanıcı olabilir ve kullanıcılar paralel olarak işlenir. Güvenlik ve performans nedenleriyle, önerimiz Kullanıcı başına bir önbellek serileştirilmemiz olur. Serileştirme olayları, işlenen kullanıcının kimliğine göre bir önbellek anahtarını hesaplar ve bu kullanıcı için bir belirteç önbelleğini seri hale getirme/kaldırma.

Web uygulamaları ve Web API 'Leri için belirteç önbelleklerinin nasıl kullanılacağına ilişkin örnekler, aşama [2-2 belirteç önbelleğindeki](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) [ASP.NET Core Web uygulaması öğreticisinde](https://ms-identity-aspnetcore-webapp-tutorial) bulunabilir. Uygulamalar için, [Microsoft-Authentication-Extensions-for-DotNet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) kitaplığı ( [Microsoft. Identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) klasöründe) [tokencacheproviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) klasörüne göz atalım. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki örneklerde belirteç önbelleği serileştirmesi gösterilmektedir.

| Örnek | Platform | Açıklama|
| ------ | -------- | ----------- |
|[Active-Directory-DotNet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Masaüstü (WPF) | Microsoft Graph API 'sini çağıran Windows Masaüstü .NET (WPF) uygulaması. ![Topoloji](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-DotNet-v1--v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Masaüstü (konsol) | Azure AD v 1.0 uygulamalarının (ADAL.NET kullanarak), belirli bir [belirteç önbelleği geçişinde](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) yakınsanmış uygulamalar (msal.NET kullanılarak) olarak da ADLANDıRıLAN Azure AD v 2.0 uygulamalarına geçişini gösteren Visual Studio çözümleri kümesi|
