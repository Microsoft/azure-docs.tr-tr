---
title: Yönetilen kimlikle güvenli SQL veritabanı bağlantısı-Azure App Service | Microsoft Docs
description: Yönetilen bir kimlik kullanarak veritabanı bağlantısını daha güvenli hale getirme ve ayrıca bunu diğer Azure hizmetlerine uygulama hakkında bilgi edinin.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c9db4cb8b7c0a12b802d75ac78bd465dda650f6d
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156045"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Öğretici: Yönetilen kimlik kullanarak App Service’tan Azure SQL Veritabanı bağlantısını güvenli hale getirme

[App Service](overview.md), Azure’da yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Ayrıca, uygulamanız için [Azure SQL Veritabanı](overview-managed-identity.md)’na ve diğer Azure hizmetlerine erişimi güvenli hale getirmeye yönelik anahtar teslim bir çözüm olan [yönetilen kimliği](/azure/sql-database/) sağlar. App Service içindeki yönetilen kimlikler, bağlantı dizelerindeki kimlik bilgileri gibi uygulamanızdaki gizli dizileri ortadan kaldırarak uygulamanızı daha güvenli hale getirir. Bu öğreticide, aşağıdaki öğreticilerden birinde oluşturduğunuz örnek Web uygulamasına yönetilen kimliği ekleyeceksiniz: 

- [Öğretici: Azure 'da SQL veritabanı ile ASP.NET uygulaması derleme](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Öğretici: Azure App Service ASP.NET Core ve SQL veritabanı uygulaması oluşturma](app-service-web-tutorial-dotnetcore-sqldb.md)

İşiniz bittiğinde, örnek uygulamanız kullanıcı adı ve parolaya gerek kalmadan SQL Veritabanıa güvenli bir şekilde bağlanacaktır.

> [!NOTE]
> Bu öğreticide ele alınan adımlarda aşağıdaki sürümler desteklenir:
> 
> - .NET Framework 4.7.2 ve üzeri.
> - .NET Core 2,2 ve üzeri.
>

Öğrenirsiniz:

> [!div class="checklist"]
> * Yönetilen kimlikleri etkinleştirme
> * Yönetilen kimliğe SQL Veritabanı erişimi verme
> * SQL veritabanı ile Azure AD kimlik doğrulamasını kullanmak için Entity Framework yapılandırma
> * Azure AD kimlik doğrulaması kullanarak Visual Studio 'dan SQL veritabanı 'na bağlanma

> [!NOTE]
>Azure AD kimlik doğrulaması, şirket içi Active Directory (AD DS) [Tümleşik Windows kimlik doğrulamasından](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) _farklıdır_ . AD DS ve Azure AD, tamamen farklı kimlik doğrulama protokolleri kullanır. Daha fazla bilgi için [Azure AD Domain Services belgeleri](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makale, öğreticide kaldığınız yerden devam eder [: SQL veritabanı Ile Azure 'da bir ASP.NET uygulaması derleme](app-service-web-tutorial-dotnet-sqldatabase.md) veya [öğretici: Azure App Service IÇINDE ASP.NET Core ve SQL veritabanı uygulaması oluşturma](app-service-web-tutorial-dotnetcore-sqldb.md). Henüz yapmadıysanız, önce iki öğreticiden birini izleyin. Alternatif olarak, kendi .NET uygulamanıza yönelik adımları SQL veritabanı ile uyarlayabilirsiniz.

Arka uç olarak SQL veritabanı 'nı kullanarak uygulamanızda hata ayıklamak için, bilgisayarınızdan istemci bağlantısına izin verildiğinden emin olun. Aksi takdirde, [Azure Portal kullanarak sunucu DÜZEYI IP güvenlik duvarı kurallarını yönetme](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)konumundaki adımları IZLEYEREK istemci IP 'sini ekleyin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Azure AD kullanıcısına veritabanı erişimi verme

İlk olarak, SQL veritabanı sunucusunun Active Directory yöneticisi olarak bir Azure AD kullanıcısı atayarak SQL veritabanı 'na Azure AD kimlik doğrulamasını etkinleştirin. Bu Kullanıcı, Azure aboneliğinize kaydolmak için kullandığınız Microsoft hesabı farklıdır. Oluşturduğunuz, içeri aktardığınız, eşitlediği veya Azure AD 'ye davet ettiğiniz bir kullanıcı olmalıdır. İzin verilen Azure AD kullanıcıları hakkında daha fazla bilgi için bkz. [SQL veritabanı 'Nda Azure AD özellikleri ve sınırlamaları](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Azure AD kiracınızda henüz bir kullanıcı yoksa, [Azure Active Directory kullanarak Kullanıcı ekleme veya silme](../active-directory/fundamentals/add-users-azure-active-directory.md)adımlarını izleyerek bir tane oluşturun.

[`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) kullanarak Azure AD KULLANıCıSıNıN nesne kimliğini bulun ve *\<Kullanıcı-asıl adı >* ' nı değiştirin. Sonuç bir değişkene kaydedilir.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Azure AD 'deki tüm Kullanıcı asıl adlarının listesini görmek için `az ad user list --query [].userPrincipalName`çalıştırın.
>

Bu Azure AD kullanıcısını Cloud Shell [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) komutunu kullanarak Active Directory yönetici olarak ekleyin. Aşağıdaki komutta *\<Server-name >* öğesini SQL veritabanı sunucu adıyla (`.database.windows.net` soneki olmadan) değiştirin.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory Yöneticisi ekleme hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı sunucunuz için Azure Active Directory Yöneticisi sağlama](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Visual Studio 'Yu ayarlama

### <a name="windows"></a>Windows
Windows için Visual Studio, Azure AD kimlik doğrulamasıyla tümleşiktir. Visual Studio 'da geliştirme ve hata ayıklamayı etkinleştirmek için, menüden **dosya** > **Hesap ayarları** ' nı seçerek Azure AD kullanıcısını Visual Studio 'Ya ekleyin ve **Hesap Ekle**' ye tıklayın.

Azure hizmet kimlik doğrulaması için Azure AD kullanıcısını ayarlamak için, menüden **araçlar** > **Seçenekler** ' i seçin ve ardından **Azure hizmeti kimlik doğrulaması** > **Hesap seçimi**' ni seçin. Eklediğiniz Azure AD kullanıcısını seçip **Tamam**' a tıklayın.

Artık Azure AD kimlik doğrulaması ile SQL veritabanıyla arka uç olarak uygulamanızı geliştirmeye ve hata ayıklamaya hazırsınız.

### <a name="macos"></a>MacOS

Mac için Visual Studio Azure AD kimlik doğrulamasıyla tümleştirilmiştir. Ancak, daha sonra kullanacağınız [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) KITAPLıĞı Azure CLI 'dan belirteçleri kullanabilir. Visual Studio 'da geliştirme ve hata ayıklamayı etkinleştirmek için öncelikle yerel makinenize [Azure CLI yüklemeniz](https://docs.microsoft.com/cli/azure/install-azure-cli) gerekir.

Azure CLı yerel makinenize yüklendikten sonra Azure AD kullanıcısını kullanarak aşağıdaki komutla Azure CLı 'da oturum açın:

```bash
az login --allow-no-subscriptions
```
Artık Azure AD kimlik doğrulaması ile SQL veritabanıyla arka uç olarak uygulamanızı geliştirmeye ve hata ayıklamaya hazırsınız.

## <a name="modify-your-project"></a>Projenizi değiştirme

Projeniz için izleyeceğiniz adımlar, bunun bir ASP.NET projesi mi yoksa ASP.NET Core projesi olmasına bağlıdır.

- [ASP.NET Değiştir](#modify-aspnet)
- [ASP.NET Core Değiştir](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET Değiştir

Visual Studio 'da Paket Yöneticisi konsolunu açın ve [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet paketini ekleyin:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

*Web. config*dosyasında, dosyanın en üstünden çalışarak aşağıdaki değişiklikleri yapın:

- `<configSections>`içinde aşağıdaki bölüm bildirimini ekleyin:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Kapanış `</configSections>` etiketinin altında `<SqlAuthenticationProviders>`için aşağıdaki XML kodunu ekleyin.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- `MyDbConnection` adlı bağlantı dizesini bulun ve `connectionString` değerini `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`ile değiştirin. _\<Server-name >_ ve _\<db-name >_ sunucu adınızla ve veritabanı adınızla değiştirin.

Bu, SQL veritabanına bağlanmak için gereken her şey. Visual Studio 'da hata ayıklarken, kodunuz [Visual Studio 'Yu ayarlama](#set-up-visual-studio)bölümünde YAPıLANDıRDıĞıNıZ Azure AD kullanıcısını kullanır. SQL veritabanı sunucusunu daha sonra App Service uygulamanızın yönetilen kimliğinden bağlantıya izin verecek şekilde ayarlayacaksınız.

Uygulamayı yeniden çalıştırmak için `Ctrl+F5` yazın. Tarayıcınızdaki aynı CRUD uygulaması artık Azure AD kimlik doğrulamasını kullanarak doğrudan Azure SQL veritabanına bağlanıyor. Bu kurulum, Visual Studio 'dan veritabanı geçişleri çalıştırmanızı sağlar.

### <a name="modify-aspnet-core"></a>ASP.NET Core Değiştir

Visual Studio 'da Paket Yöneticisi konsolunu açın ve [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet paketini ekleyin:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

[ASP.NET Core ve SQL veritabanı öğreticisinde](app-service-web-tutorial-dotnetcore-sqldb.md), yerel geliştirme ortamı bir SQLite veritabanı dosyası kullandığından ve Azure üretim ortamı App Service bir bağlantı dizesi kullandığından, `MyDbConnection` bağlantı dizesi hiç kullanılmıyor. Active Directory kimlik doğrulamasıyla, her iki ortamın de aynı bağlantı dizesini kullanmasını istersiniz. *AppSettings. JSON*içinde, `MyDbConnection` bağlantı dizesinin değerini ile değiştirin:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

*Startup.cs*' de, daha önce eklediğiniz kod bölümünü kaldırın:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Ve aşağıdaki kodla değiştirin:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Daha sonra, SQL veritabanı için erişim belirtecine sahip Entity Framework veritabanı bağlamını sağlarsınız. *Data\MyDatabaseContext.cs*' de, boş `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` oluşturucusunun küme ayraçları içine aşağıdaki kodu ekleyin:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Bu tanıtım kodu, açıklık ve basitlik için zaman uyumludur.

Bu, SQL veritabanına bağlanmak için gereken her şey. Visual Studio 'da hata ayıklarken, kodunuz [Visual Studio 'Yu ayarlama](#set-up-visual-studio)bölümünde YAPıLANDıRDıĞıNıZ Azure AD kullanıcısını kullanır. SQL veritabanı sunucusunu daha sonra App Service uygulamanızın yönetilen kimliğinden bağlantıya izin verecek şekilde ayarlayacaksınız. `AzureServiceTokenProvider` sınıfı, belirteci bellekte önbelleğe alır ve süresi dolmadan hemen önce Azure AD 'den alır. Belirteci yenilemek için özel kod gerekmez.

> [!TIP]
> Yapılandırdığınız Azure AD kullanıcısının birden çok kiracıya erişimi varsa, uygun erişim belirtecini almak için istenen kiracı KIMLIĞIYLE `GetAccessTokenAsync("https://database.windows.net/", tenantid)` çağırın.

Uygulamayı yeniden çalıştırmak için `Ctrl+F5` yazın. Tarayıcınızdaki aynı CRUD uygulaması artık Azure AD kimlik doğrulamasını kullanarak doğrudan Azure SQL veritabanına bağlanıyor. Bu kurulum, Visual Studio 'dan veritabanı geçişleri çalıştırmanızı sağlar.

## <a name="use-managed-identity-connectivity"></a>Yönetilen kimlik bağlantısı kullan

Daha sonra, App Service uygulamanızı, sistem tarafından atanan yönetilen kimlik ile SQL veritabanına bağlanacak şekilde yapılandırırsınız.

### <a name="enable-managed-identity-on-app"></a>Uygulamada yönetilen kimliği etkinleştir

Azure uygulamanızda bir yönetilen kimlik etkinleştirmek için Cloud Shell’de [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) komutunu kullanın. Aşağıdaki komutta *\<app-name >* ' ı değiştirin.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Çıktının bir örneği aşağıda verilmiştir:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Yönetilen kimliği bir Azure AD grubuna ekleme

SQL veritabanınıza bu kimlik erişimi vermek için bir [Azure AD grubuna](../active-directory/fundamentals/active-directory-manage-groups.md)eklemeniz gerekir. Cloud Shell, aşağıdaki komut dosyasında gösterildiği gibi, _Myazuresqldbaccessgroup_adlı yeni bir gruba ekleyin:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Her komut için tam JSON çıktısını görmek istiyorsanız, `--query objectId --output tsv` parametrelerini bırakın.

### <a name="grant-permissions-to-azure-ad-group"></a>Azure AD grubuna izin verme

Cloud Shell’de SQLCMD komutunu kullanarak SQL Veritabanı oturumunu açın. _\<Server-name >_ öğesini SQL veritabanı sunucu adınızla değiştirin, _\<db-name >_ uygulamanızın kullandığı veritabanı adını ve\<_aad-User-Name >_ ve\<_AAD-Password >_ Azure AD Kullanıcı kimlik bilgileriyle değiştirin.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

İstediğiniz veritabanının SQL isteminde, Azure AD grubunu eklemek ve uygulamanızın ihtiyaç duyacağı izinleri vermek için aşağıdaki komutları çalıştırın. Örneğin, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Cloud Shell istemine geri dönmek için `EXIT` yazın.

### <a name="modify-connection-string"></a>Bağlantı dizesini değiştirme

*Web. config* veya *appSettings. JSON* ' da yaptığınız aynı değişikliklerin yönetilen kimlikle birlikte çalışıp çalışmadığını unutmayın. bu nedenle tek şey, Visual Studio 'nun uygulamanızı ilk kez dağıttığı App Service var olan bağlantı dizesini kaldırmaktan emin olur. Aşağıdaki komutu kullanın, ancak *\<app-name >* uygulamanızın adıyla değiştirin.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Değişikliklerinizi yayımlama

Bundan sonra tek yapmanız gereken, değişikliklerinizi Azure'da yayımlamaktır.

**[Öğretici: Azure 'Da SQL veritabanı ile bir ASP.NET uygulaması oluşturma](app-service-web-tutorial-dotnet-sqldatabase.md)** , değişikliklerinizi Visual Studio 'da yayımlayın. **Çözüm Gezgini**’nde **DotNetAppSqlDb** projenize sağ tıklayıp **Yayımla**’yı seçin.

![Çözüm Gezgini'nden yayımlama](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Yayımlama sayfasında **Yayımla**'ya tıklayın. 

**Öğreticiden ulaşdıysanız [: Azure App Service ASP.NET Core ve SQL veritabanı uygulaması oluşturma](app-service-web-tutorial-dotnetcore-sqldb.md)** , aşağıdaki komutlarla git kullanarak değişikliklerinizi yayımlayın:

```bash
git commit -am "configure managed identity"
git push azure master
```

Yeni web sayfası yapılacaklar listenizi gösterdiğinde uygulamanızın yönetilen kimliğini kullanarak veritabanına bağlanmakta olduğu anlamına gelir.

![Code First geçişten sonra Azure uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Bundan sonra yapılacaklar listesini daha önce olduğu gibi düzenleyebilirsiniz.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * Yönetilen kimlikleri etkinleştirme
> * Yönetilen kimliğe SQL Veritabanı erişimi verme
> * SQL veritabanı ile Azure AD kimlik doğrulamasını kullanmak için Entity Framework yapılandırma
> * Azure AD kimlik doğrulaması kullanarak Visual Studio 'dan SQL veritabanı 'na bağlanma

Web uygulamanıza özel bir DNS adı eşlemeyle ilgili bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Mevcut bir özel DNS adını Azure App Service eşleme](app-service-web-tutorial-custom-domain.md)
