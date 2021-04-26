---
title: .NET-hizmetten hizmete kimlik doğrulaması-Data Lake Storage 1.
description: .NET SDK kullanarak Azure Active Directory kullanarak Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması elde etme hakkında bilgi edinin
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010308"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK kullanarak Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması
> [!div class="op_single_selector"]
> * [Java'yı kullanma](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK’sını kullanma](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python’u kullanma](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API’sini kullanma](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Bu makalede, Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması yapmak için .NET SDK 'nın nasıl kullanılacağını öğreneceksiniz. .NET SDK kullanarak Data Lake Storage 1. Son Kullanıcı kimlik doğrulaması için bkz. [.NET SDK kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Önkoşullar
* **Visual Studio 2013 veya üzeri**. Aşağıdaki yönergelerde Visual Studio 2019 kullanılır.

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Active Directory "Web" uygulaması oluşturun**. [Azure Active Directory kullanarak, Data Lake Storage 1. Ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-using-active-directory.md)adımlarını tamamlamış olmanız gerekir.

## <a name="create-a-net-application"></a>.NET uygulaması oluşturma
1. Visual Studio 'da **Dosya** menüsünü, **Yeni**' yi ve ardından **Proje**' yi seçin.
2. **Konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.
3. **Proje adı** alanına girin `CreateADLApplication` ve ardından **Oluştur**' u seçin.

4. NuGet paketlerini projenize ekleyin.

   1. Çözüm Gezgini'nde proje adına sağ tıklayın ve **NuGet Paketlerini Yönet**'e tıklayın.
   2. **NuGet Paket Yöneticisi** sekmesinde, **paket kaynağının** **NuGet.org** olarak ayarlandığından ve **ön sürümü dahil** et onay kutusunun işaretli olduğundan emin olun.
   3. Aşağıdaki NuGet paketlerini arayıp yükleyin:

      * `Microsoft.Azure.Management.DataLake.Store` - Bu öğreticide v2.1.3-preview kullanılır.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Bu öğreticide v2.2.12 kullanılır.

        ![NuGet kaynağı ekleme](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Yeni bir Azure Data Lake hesabı oluşturun")
   4. **NuGet Paket Yöneticisi 'ni** kapatın.

5. **Program.cs** öğesini açın, var olan kodu silin ve ardından ad alanlarına başvurular eklemek için aşağıdaki deyimleri ekleyin.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>İstemci gizli anahtarı ile hizmetten hizmete kimlik doğrulaması
Bu kod parçacığını .NET istemci uygulamanıza ekleyin. Yer tutucu değerlerini bir Azure AD Web uygulamasından alınan değerlerle değiştirin (bir önkoşul olarak listelenir). Bu kod parçacığı, Azure AD Web uygulaması için gizli anahtar/anahtar kullanarak uygulamanızın **etkileşimli olmayan** Data Lake Storage 1. kimlik doğrulamasını yapmanızı sağlar.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Yukarıdaki kod parçacığı bir yardımcı işlevi kullanır `GetCreds_SPI_SecretKey` . Bu yardımcı işlevin kodu [GitHub 'da burada](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey)bulunabilir.

## <a name="service-to-service-authentication-with-certificate"></a>Sertifika ile hizmetten hizmete kimlik doğrulaması

Bu kod parçacığını .NET istemci uygulamanıza ekleyin. Yer tutucu değerlerini bir Azure AD Web uygulamasından alınan değerlerle değiştirin (bir önkoşul olarak listelenir). Bu kod parçacığı, Azure AD Web uygulaması sertifikasını kullanarak uygulamanızın **etkileşimli olmayan** Data Lake Storage 1. kimlik doğrulamasını yapmanızı sağlar. Azure AD uygulaması oluşturma hakkında yönergeler için bkz. [sertifikalarla hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Yukarıdaki kod parçacığı bir yardımcı işlevi kullanır `GetCreds_SPI_Cert` . Bu yardımcı işlevin kodu [GitHub 'da burada](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert)bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, .NET SDK kullanarak Data Lake Storage 1. kimlik doğrulaması yapmak için hizmetten hizmete kimlik doğrulamasını nasıl kullanacağınızı öğrendiniz. Artık Data Lake Storage 1. çalışmak için .NET SDK 'sını kullanma hakkında konuşabilecek aşağıdaki makalelere bakabilirsiniz.

* [.NET SDK kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-net-sdk.md)
* [.NET SDK kullanarak Data Lake Storage 1. veri işlemleri](data-lake-store-data-operations-net-sdk.md)
