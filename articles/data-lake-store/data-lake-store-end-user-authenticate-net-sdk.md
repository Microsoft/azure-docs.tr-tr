---
title: Son Kullanıcı kimlik doğrulaması-.NET Data Lake Storage 1.-Azure
description: .NET SDK ile Azure Active Directory kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması elde etme hakkında bilgi edinin
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89004791"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması
> [!div class="op_single_selector"]
> * [Java'yı kullanma](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK’sını kullanma](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python’u kullanma](data-lake-store-end-user-authenticate-python.md)
> * [REST API’sini kullanma](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Bu makalede, Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması yapmak için .NET SDK 'nın nasıl kullanılacağını öğreneceksiniz. .NET SDK kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması için bkz. [.NET SDK kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Önkoşullar
* **Visual Studio 2013 veya üzeri**. Aşağıdaki yönergelerde Visual Studio 2019 kullanılır.

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Bir Azure Active Directory "yerel" uygulaması oluşturun**. [Azure Active Directory kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-using-active-directory.md)adımlarını tamamlamış olmanız gerekir.

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

5. **Program. cs** 'yi açın
6. Using deyimlerini aşağıdaki satırlarla değiştirin:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Son kullanıcı kimlik doğrulaması
Bu kod parçacığını .NET istemci uygulamanıza ekleyin. Yer tutucu değerlerini bir Azure AD yerel uygulamasından alınan değerlerle değiştirin (önkoşul olarak listelenir). Bu kod parçacığı, uygulamanızın Data Lake Storage 1. ile **etkileşimli** olarak doğrulanmasını sağlar, bu da Azure kimlik bilgilerinizi girmeniz istenir.

Kullanım kolaylığı için, aşağıdaki kod parçacığı istemci KIMLIĞI ve yeniden yönlendirme URI 'SI için tüm Azure abonelikleri için geçerli olan varsayılan değerleri kullanır. Aşağıdaki kod parçacığında yalnızca kiracı KIMLIĞINIZ için değer sağlamanız gerekir. Kiracı kimliğini [alma](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)sırasında belirtilen yönergeleri kullanarak kiracı kimliğini alabilirsiniz.
    
- Main () işlevini aşağıdaki kodla değiştirin:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Önceki kod parçacığı hakkında bilgi almak için birkaç şey:

* Yukarıdaki kod parçacığı, bir yardımcı işlevleri `GetTokenCache` ve kullanır `GetCreds_User_Popup` . Bu yardımcı işlevlerin kodu [GitHub 'da burada](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache)bulunabilir.
* Kod parçacığı daha hızlı bir şekilde tamamlamanıza yardımcı olmak için, tüm Azure abonelikleri için varsayılan olarak kullanılabilen bir yerel uygulama istemci KIMLIĞINI kullanır. Böylece **bu kod parçacığını uygulamanızda olduğu gibi kullanabilirsiniz**.
* Ancak, kendi Azure AD etki alanınızı ve uygulama istemci kimliğinizi kullanmak istemiyorsanız, bir Azure AD yerel uygulaması oluşturmanız ve ardından oluşturduğunuz uygulamaya ait Azure AD kiracı kimliği, istemci kimliği ve yeniden yönlendirme URI’sini kullanmanız gerekir. Yönergeler için, bkz. [Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması için Active Directory uygulaması oluşturma](data-lake-store-end-user-authenticate-using-active-directory.md) .

  
## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, .NET SDK kullanarak Azure Data Lake Storage 1. kimlik doğrulaması yapmak için son kullanıcı kimlik doğrulamasını nasıl kullanacağınızı öğrendiniz. Artık Azure Data Lake Storage 1. çalışmak için .NET SDK 'sını kullanma hakkında konuşabilecek aşağıdaki makalelere bakabilirsiniz.

* [.NET SDK kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-net-sdk.md)
* [.NET SDK kullanarak Data Lake Storage 1. veri işlemleri](data-lake-store-data-operations-net-sdk.md)

