---
title: 'Öğretici: yönetilen kimlik ile verilere erişin'
description: Yönetilen bir kimlik kullanarak veritabanı bağlantısını daha güvenli hale getirme ve ayrıca bunu diğer Azure hizmetlerine uygulama hakkında bilgi edinin.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 0012c3d208998786ef5aa34320f3bccc4e51ebe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782792"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Öğretici: Yönetilen kimlik kullanarak App Service’tan Azure SQL Veritabanı bağlantısını güvenli hale getirme

[App Service](overview.md), Azure’da yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Ayrıca, uygulamanız için [Azure SQL Veritabanı](/azure/sql-database/)’na ve diğer Azure hizmetlerine erişimi güvenli hale getirmeye yönelik anahtar teslim bir çözüm olan [yönetilen kimliği](overview-managed-identity.md) sağlar. App Service içindeki yönetilen kimlikler, bağlantı dizelerindeki kimlik bilgileri gibi uygulamanızdaki gizli dizileri ortadan kaldırarak uygulamanızı daha güvenli hale getirir. Bu öğreticide, aşağıdaki öğreticilerden birinde oluşturduğunuz örnek Web uygulamasına yönetilen kimliği ekleyeceksiniz: 

- [Öğretici: Azure SQL veritabanı ile Azure 'da bir ASP.NET uygulaması derleme](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Öğretici: Azure App Service ASP.NET Core ve Azure SQL veritabanı uygulaması oluşturma](tutorial-dotnetcore-sqldb-app.md)

İşiniz bittiğinde, örnek uygulamanız kullanıcı adı ve parolaya gerek kalmadan SQL Veritabanıa güvenli bir şekilde bağlanacaktır.

> [!NOTE]
> Bu öğreticide ele alınan adımlarda aşağıdaki sürümler desteklenir:
> 
> - .NET Framework 4.7.2 ve üzeri
> - .NET Core 2,2 ve üzeri
>

Öğrenirsiniz:

> [!div class="checklist"]
> * Yönetilen kimlikleri etkinleştirme
> * Yönetilen kimliğe SQL Veritabanı erişimi verme
> * SQL veritabanı ile Azure AD kimlik doğrulamasını kullanmak için Entity Framework yapılandırma
> * Azure AD kimlik doğrulaması kullanarak Visual Studio 'dan SQL veritabanı 'na bağlanma

> [!NOTE]
>Azure AD kimlik doğrulaması, şirket içi Active Directory (AD DS) [Tümleşik Windows kimlik doğrulamasından](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) _farklıdır_ . AD DS ve Azure AD, tamamen farklı kimlik doğrulama protokolleri kullanır. Daha fazla bilgi için bkz. [Azure AD Domain Services belgeleri](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makale, öğreticide kaldığınız yerden devam eder [: SQL veritabanı Ile Azure 'da bir ASP.NET uygulaması derleme](app-service-web-tutorial-dotnet-sqldatabase.md) veya [öğretici: Azure App Service IÇINDE ASP.NET Core ve SQL veritabanı uygulaması oluşturma](tutorial-dotnetcore-sqldb-app.md). Henüz yapmadıysanız, önce iki öğreticiden birini izleyin. Alternatif olarak, kendi .NET uygulamanıza yönelik adımları SQL veritabanı ile uyarlayabilirsiniz.

Arka uç olarak SQL veritabanı 'nı kullanarak uygulamanızda hata ayıklamak için, bilgisayarınızdan istemci bağlantısına izin verildiğinden emin olun. Aksi takdirde, [Azure Portal kullanarak sunucu DÜZEYI IP güvenlik duvarı kurallarını yönetme](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)konumundaki adımları IZLEYEREK istemci IP 'sini ekleyin.

Ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Azure AD kullanıcısına veritabanı erişimi verme

İlk olarak, bir Azure AD kullanıcısını sunucunun Active Directory yöneticisi olarak atayarak SQL veritabanı 'na Azure AD kimlik doğrulamasını etkinleştirin. Bu Kullanıcı, Azure aboneliğinize kaydolmak için kullandığınız Microsoft hesabı farklıdır. Oluşturduğunuz, içeri aktardığınız, eşitlediği veya Azure AD 'ye davet ettiğiniz bir kullanıcı olmalıdır. İzin verilen Azure AD kullanıcıları hakkında daha fazla bilgi için bkz. [SQL veritabanı 'Nda Azure AD özellikleri ve sınırlamaları](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Azure AD kiracınızda henüz bir kullanıcı yoksa, [Azure Active Directory kullanarak Kullanıcı ekleme veya silme](../active-directory/fundamentals/add-users-azure-active-directory.md)adımlarını izleyerek bir tane oluşturun.

Ve yerini kullanarak Azure AD kullanıcısının nesne KIMLIĞINI bulun [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) *\<user-principal-name>* . Sonuç bir değişkene kaydedilir.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Azure AD 'deki tüm Kullanıcı asıl adlarının listesini görmek için, öğesini çalıştırın `az ad user list --query [].userPrincipalName` .
>

Bu Azure AD kullanıcısını Cloud Shell komut kullanarak Active Directory yönetici olarak ekleyin [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) . Aşağıdaki komutta, yerine *\<server-name>* sunucu adı (sonek olmadan) ile değiştirin `.database.windows.net` .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory Yöneticisi ekleme hakkında daha fazla bilgi için bkz. [sunucunuz için Azure Active Directory Yöneticisi sağlama](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>Visual Studio'yu ayarlama

### <a name="windows-client"></a>Windows istemcisi
Windows için Visual Studio, Azure AD kimlik doğrulamasıyla tümleşiktir. Visual Studio 'da geliştirme ve hata ayıklamayı etkinleştirmek için, menüden **Dosya** hesap ayarları ' nı seçerek Azure AD kullanıcısını Visual Studio 'ya ekleyin  >   ve **Hesap Ekle**' ye tıklayın.

Azure hizmet kimlik doğrulaması için Azure AD kullanıcısını ayarlamak için, menüden **Araçlar**  >  **Seçenekler** ' i seçin ve ardından **Azure hizmeti kimlik doğrulama**  >  **hesabı seçimi**' ni seçin. Eklediğiniz Azure AD kullanıcısını seçip **Tamam**' a tıklayın.

Artık Azure AD kimlik doğrulaması ile SQL veritabanıyla arka uç olarak uygulamanızı geliştirmeye ve hata ayıklamaya hazırsınız.

### <a name="macos-client"></a>macOS istemcisi

Mac için Visual Studio Azure AD kimlik doğrulamasıyla tümleştirilmiştir. Ancak, daha sonra kullanacağınız [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) KITAPLıĞı Azure CLI 'dan belirteçleri kullanabilir. Visual Studio 'da geliştirme ve hata ayıklamayı etkinleştirmek için öncelikle yerel makinenize [Azure CLI yüklemeniz](/cli/azure/install-azure-cli) gerekir.

Azure CLı yerel makinenize yüklendikten sonra Azure AD kullanıcısını kullanarak aşağıdaki komutla Azure CLı 'da oturum açın:

```azurecli
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
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

*Web.config*' de, dosyanın en üstünden çalışarak aşağıdaki değişiklikleri yapın:

- İçinde `<configSections>` , aşağıdaki bölüm bildirimini içine ekleyin:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- kapanış etiketinin altında `</configSections>` , için AŞAĞıDAKI XML kodunu ekleyin `<SqlAuthenticationProviders>` .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Çağrılan bağlantı dizesini bulun `MyDbConnection` ve `connectionString` değerini ile değiştirin `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . _\<server-name>_ Ve ' i _\<db-name>_ sunucu adınızla ve veritabanı adınızla değiştirin.

> [!NOTE]
> Az önce kaydettiğiniz SqlAuthenticationProvider, daha önce yüklediğiniz AppAuthentication kitaplığının üzerine dayalıdır. Varsayılan olarak, sistem tarafından atanan bir kimlik kullanır. Kullanıcı tarafından atanan bir kimliğin yararlanmak için ek bir yapılandırma sağlamanız gerekir. Lütfen AppAuthentication kitaplığı için [bağlantı dizesi desteği](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) 'ne bakın.

Bu, SQL veritabanına bağlanmak için gereken her şey. Visual Studio 'da hata ayıklarken, kodunuz [Visual Studio 'Yu ayarlama](#set-up-visual-studio)bölümünde YAPıLANDıRDıĞıNıZ Azure AD kullanıcısını kullanır. SQL veritabanı 'nı daha sonra App Service uygulamanızın yönetilen kimliğinden bağlantıya izin verecek şekilde ayarlayacaksınız.

`Ctrl+F5`Uygulamayı yeniden çalıştırmak için yazın. Tarayıcınızdaki aynı CRUD uygulaması artık Azure AD kimlik doğrulamasını kullanarak doğrudan Azure SQL veritabanına bağlanıyor. Bu kurulum, Visual Studio 'dan veritabanı geçişleri çalıştırmanızı sağlar.

### <a name="modify-aspnet-core"></a>ASP.NET Core Değiştir

Visual Studio 'da Paket Yöneticisi konsolunu açın ve [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet paketini ekleyin:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

[ASP.NET Core ve SQL veritabanı öğreticisinde](tutorial-dotnetcore-sqldb-app.md), `MyDbConnection` yerel geliştirme ortamı bir SQLite veritabanı dosyası kullandığından ve Azure üretim ortamı App Service bir bağlantı dizesi kullandığından, bağlantı dizesi hiç kullanılmıyor. Active Directory kimlik doğrulamasıyla, her iki ortamın de aynı bağlantı dizesini kullanmasını istersiniz. *appsettings.json* öğesinde, `MyDbConnection` bağlantı dizesinin değerini ile değiştirin:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Daha sonra, SQL veritabanı için erişim belirtecine sahip Entity Framework veritabanı bağlamını sağlarsınız. *Data\MyDatabaseContext.cs*' de, boş oluşturucunun küme ayraçları içine aşağıdaki kodu ekleyin `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` :

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Bu tanıtım kodu, açıklık ve basitlik için zaman uyumludur.

Bu, SQL veritabanına bağlanmak için gereken her şey. Visual Studio 'da hata ayıklarken, kodunuz [Visual Studio 'Yu ayarlama](#set-up-visual-studio)bölümünde YAPıLANDıRDıĞıNıZ Azure AD kullanıcısını kullanır. SQL veritabanı 'nı daha sonra App Service uygulamanızın yönetilen kimliğinden bağlantıya izin verecek şekilde ayarlayacaksınız. `AzureServiceTokenProvider`Sınıfı, belirteci bellekte önbelleğe alır ve süresi dolmadan hemen önce Azure AD 'den alır. Belirteci yenilemek için özel kod gerekmez.

> [!TIP]
> Yapılandırdığınız Azure AD kullanıcısının birden çok kiracıya erişimi varsa, `GetAccessTokenAsync("https://database.windows.net/", tenantid)` uygun erişim belirtecini almak için istenen KIRACı kimliğiyle çağrı yapın.

`Ctrl+F5`Uygulamayı yeniden çalıştırmak için yazın. Tarayıcınızdaki aynı CRUD uygulaması artık Azure AD kimlik doğrulamasını kullanarak doğrudan Azure SQL veritabanına bağlanıyor. Bu kurulum, Visual Studio 'dan veritabanı geçişleri çalıştırmanızı sağlar.

## <a name="use-managed-identity-connectivity"></a>Yönetilen kimlik bağlantısı kullan

Daha sonra, App Service uygulamanızı, sistem tarafından atanan yönetilen kimlik ile SQL veritabanına bağlanacak şekilde yapılandırırsınız.

> [!NOTE]
> Bu bölümdeki yönergeler sistem tarafından atanan bir kimlik için olduğunda, Kullanıcı tarafından atanan bir kimlik yalnızca kolayca kullanılabilir. Bunu yapmak için: `az webapp identity assign command`istenen kullanıcı tarafından atanan kimliği atamak için değişikliğini yapmanız gerekir. Ardından, SQL kullanıcısını oluştururken, site adı yerine Kullanıcı tarafından atanan kimlik kaynağının adını kullandığınızdan emin olun.

### <a name="enable-managed-identity-on-app"></a>Uygulamada yönetilen kimliği etkinleştir

Azure uygulamanızda bir yönetilen kimlik etkinleştirmek için Cloud Shell’de [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) komutunu kullanın. Aşağıdaki komutta, öğesini değiştirin *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Çıkış örneği:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Yönetilen kimliğe izin ver

> [!NOTE]
> İsterseniz, kimliği bir [Azure AD grubuna](../active-directory/fundamentals/active-directory-manage-groups.md)ekleyebilir, ardından SQL veritabanı erişimini kimlik yerıne Azure AD grubuna atayabilirsiniz. Örneğin, aşağıdaki komutlar önceki adımdan yönetilen kimliği _Myazuresqldbaccessgroup_ adlı yeni bir gruba ekler:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Cloud Shell’de SQLCMD komutunu kullanarak SQL Veritabanı oturumunu açın. _\<server-name>_ _\<db-name>_ Uygulamanızın kullandığı veritabanı adı Ile ve _\<aad-user-name>_ _\<aad-password>_ Azure AD Kullanıcı kimlik bilgilerinizle birlikte sunucu adınızla değiştirin.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

İstediğiniz veritabanına yönelik SQL isteminde, uygulamanızın ihtiyaç duyacağı izinleri vermek için aşağıdaki komutları çalıştırın. Örneğin, 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* , Azure AD 'de yönetilen kimliğin adıdır. Kimlik sistem tarafından atanmışsa, ad App Service uygulamanızın adı ile her zaman aynıdır. Bir Azure AD grubuna izinler vermek için, bunun yerine grubun görünen adını kullanın (örneğin, *Myazuresqldbaccessgroup*).

Cloud Shell istemine geri dönmek için `EXIT` yazın.

> [!NOTE]
> Yönetilen kimliklerin arka uç Hizmetleri, bir hedef kaynağın belirtecini yalnızca süresi dolarsa güncelleştiren [bir belirteç önbelleği de sağlar](overview-managed-identity.md#obtain-tokens-for-azure-resources) . SQL veritabanı izinlerinizi yapılandırırken bir hata yaparsanız ve uygulamanızla belirteç almaya *çalıştıktan sonra* izinleri değiştirmeye çalışırsanız, önbelleğe alınan belirtecin süresi dolana kadar güncelleştirilmiş izinlerle yeni bir belirteç alınmaz.

> [!NOTE]
> AAD, şirket içi SQL Server için desteklenmez ve bu, Mssıs 'yi içerir. 

### <a name="modify-connection-string"></a>Bağlantı dizesini değiştirme

*Web.config* veya *appsettings.jsüzerinde* yaptığınız değişikliklerin yönetilen kimlikle birlikte çalışıp çalışmadığını unutmayın. bu nedenle, tek şey, Visual Studio 'nun uygulamanızı ilk kez dağıttığı App Service var olan bağlantı dizesini kaldırmaktan emin olur. Aşağıdaki komutu kullanın, ancak *\<app-name>* uygulamanızın adıyla değiştirin.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Değişikliklerinizi yayımlama

Bundan sonra tek yapmanız gereken, değişikliklerinizi Azure'da yayımlamaktır.

**[Öğretici: Azure 'Da SQL veritabanı ile bir ASP.NET uygulaması oluşturma](app-service-web-tutorial-dotnet-sqldatabase.md)**, değişikliklerinizi Visual Studio 'da yayımlayın. **Çözüm Gezgini**’nde **DotNetAppSqlDb** projenize sağ tıklayıp **Yayımla**’yı seçin.

![Çözüm Gezgini'nden yayımlama](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Yayımlama sayfasında **Yayımla**'ya tıklayın. 

**Öğreticiden ulaşdıysanız [: Azure App Service ASP.NET Core ve SQL veritabanı uygulaması oluşturma](tutorial-dotnetcore-sqldb-app.md)**, aşağıdaki komutlarla git kullanarak değişikliklerinizi yayımlayın:

```bash
git commit -am "configure managed identity"
git push azure main
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