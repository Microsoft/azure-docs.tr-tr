---
title: Activedirectoryınteractive SQL 'e bağlanır
description: C#SqlAuthenticationMethod. Activedirectoryınteractıve modunu kullanarak Azure SQL veritabanı 'na bağlanmak için açıklamaları içeren kod örneği.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692309"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication Azure SQL veritabanı 'na bağlanma

Bu makale, Azure C# SQL veritabanı 'na bağlanan bir program sağlar. Program, [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)destekleyen etkileşimli mod kimlik doğrulamasını kullanır.

SQL araçları için Multi-Factor Authentication desteği hakkında daha fazla bilgi için, bkz. [SQL Server veri araçları (SSDT) Azure Active Directory desteği](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL veritabanı için Multi-Factor Authentication

.NET Framework Version 4.7.2 'dan başlayarak, enum [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) yeni bir değere sahiptir: `ActiveDirectoryInteractive`. Bir istemci C# programında, Enum değeri SISTEMI Azure SQL veritabanına bağlanmak için Multi-Factor Authentication destekleyen Azure Active Directory (Azure AD) etkileşimli modunu kullanacak şekilde yönlendirir. Programı çalıştıran kullanıcı aşağıdaki iletişim kutularını görür:

* Bir Azure AD Kullanıcı adını görüntüleyen ve kullanıcının parolasını soran bir iletişim kutusu.

   Kullanıcının etki alanı Azure AD ile federe ise, bu iletişim kutusu görünmez, çünkü parola gerekli değildir.

   Azure AD ilkesi kullanıcıya Multi-Factor Authentication uygular, sonraki iki iletişim kutusu görüntülenir.

* Kullanıcı Multi-Factor Authentication ilk kez geçtiğinde, sistem, SMS iletilerinin gönderileceği cep telefonu numarasını soran bir iletişim kutusu görüntüler. Her ileti, kullanıcının bir sonraki iletişim kutusunda girmesi gereken *doğrulama kodunu* sağlar.

* Sistemin bir cep telefonuna gönderdiği Multi-Factor Authentication doğrulama kodu isteyen bir iletişim kutusu.

Azure AD 'yi Multi-Factor Authentication gerektirecek şekilde yapılandırma hakkında daha fazla bilgi için bkz. [bulutta azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)kullanmaya başlama.

Bu iletişim kutularının ekran görüntüleri için bkz. [SQL Server Management Studio ve Azure AD için Multi-Factor Authentication 'ı yapılandırma](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> .NET Framework API 'Leri [.NET API tarayıcısı araç sayfası](https://docs.microsoft.com/dotnet/api/)ile arayabilirsiniz.
>
> [İsteğe bağlı? Term =&lt;arama değeri&gt; parametresiyle](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)de doğrudan arama yapabilirsiniz.

## <a name="configure-your-c-application-in-the-azure-portal"></a>C# Uygulamanızı Azure Portal yapılandırma

Başlamadan önce, oluşturulmuş ve kullanılabilir bir [Azure SQL veritabanı sunucusu](sql-database-get-started-portal.md) olmalıdır.

### <a name="register-your-app-and-set-permissions"></a>Uygulamanızı kaydetme ve izinleri ayarlama

Azure AD kimlik doğrulamasını kullanmak için C# programınızın BIR Azure AD uygulaması olarak kaydedilmesi gerekir. Bir uygulamayı kaydetmek için bir Azure AD yöneticisi veya Azure AD *uygulama geliştirici* rolüne atanmış bir kullanıcı olmanız gerekir. Rol atama hakkında daha fazla bilgi için, bkz. [Azure Active Directory kullanıcılara yönetici ve yönetici olmayan roller atama](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Bir uygulama kaydının tamamlanması, bir **uygulama kimliği**oluşturur ve görüntüler. Programınız bağlanmak için bu KIMLIĞI içermelidir.

Uygulamanız için gerekli izinleri kaydetmek ve ayarlamak için:

1. Azure portal **Azure Active Directory** > **yeni kayıt** > **uygulama kayıtları** seçin.

    ![Uygulama kaydı](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Uygulama kaydı oluşturulduktan sonra, **uygulama kimliği** değeri oluşturulur ve görüntülenir.

    ![Uygulama KIMLIĞI görüntülendi](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Izin eklemek** > **API izinleri** ' ni seçin.

    ![Kayıtlı uygulama için izin ayarları](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Kuruluşumun kullandığı API 'leri** seçin > **Azure SQL veritabanı** 'nı arama > yazın ve **Azure SQL veritabanı**' nı seçin.

    ![Azure SQL veritabanı için API erişimi ekleme](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **Temsilci izinleri** > **user_impersonation** > **Ekle**' yi seçin.

    ![Azure SQL veritabanı için API 'ye izin verme](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>SQL veritabanı sunucunuz için bir Azure AD yöneticisi ayarlama

C# Programınızın çalışması için, BIR Azure SQL Server yöneticisinin SQL veritabanı sunucunuz Için BIR Azure AD yöneticisi ataması gerekir. 

**SQL Server** sayfasında, **yönetici > yönetici kümesi** **Active Directory** ' ni seçin.

Azure AD yöneticileri ve Azure SQL veritabanı kullanıcıları hakkında daha fazla bilgi için [SQL veritabanı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)konusundaki ekran görüntülerine bakın.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Belirli bir veritabanına yönetici olmayan kullanıcı ekleme (isteğe bağlı)

SQL veritabanı sunucusu için bir Azure AD Yöneticisi C# örnek programı çalıştırabilir. Veritabanında yer alıyorsa, bir Azure AD kullanıcısı programı çalıştırabilir. Veritabanında zaten bulunan ve veritabanı üzerinde `ALTER ANY USER` iznine sahip olan bir Azure AD SQL Yöneticisi veya bir Azure AD kullanıcısı bir kullanıcı ekleyebilir.

SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) komutuyla veritabanına bir kullanıcı ekleyebilirsiniz. `CREATE USER [<username>] FROM EXTERNAL PROVIDER` bunun bir örneğidir.

Daha fazla bilgi için bkz. [SQL veritabanı, yönetilen örnek veya SQL veri ambarı ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Yeni kimlik doğrulaması Enum değeri

C# Örnek, [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) ad alanını kullanır. Multi-Factor Authentication için özel ilgi alanı, aşağıdaki değerlere sahip olan enum `SqlAuthenticationMethod`:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Multi-Factor Authentication uygulamak için bu değeri bir Azure AD Kullanıcı adı ile birlikte kullanın. Bu değer, mevcut makalenin odaklanmaktadır. Kullanıcı parolası için iletişim kutularını görüntüleyerek ve bu kullanıcıya Multi-Factor Authentication varsa Multi-Factor Authentication doğrulama için etkileşimli bir deneyim üretir. Bu değer, .NET Framework Version 4.7.2 ile başlayarak kullanılabilir.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  *Federasyon* hesabı için bu değeri kullanın. Bir Federasyon hesabı için, Kullanıcı adı Windows etki alanı tarafından bilinir. Bu kimlik doğrulama yöntemi Multi-Factor Authentication desteklemez.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Azure AD Kullanıcı adı ve parolası gerektiren kimlik doğrulaması için bu değeri kullanın. Azure SQL veritabanı kimlik doğrulaması yapar. Bu yöntem Multi-Factor Authentication desteklemez.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Azure portal C# parametre değerlerini ayarlama

C# Programın başarıyla çalışması için, statik alanlara uygun değerler atamanız gerekir. Burada gösterildiği gibi, örnek değerleri olan alanlar. Ayrıca, gereken değerleri edinebileceğiniz Azure portal konumları da gösterilir.

| Statik alan adı | Örnek değer | Azure portal nerede |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL server** > **ada göre filtrele** |
| AzureAD_UserID | "Auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **Kullanıcı** > **Yeni Konuk Kullanıcı** |
| Initial_DatabaseName | "myDatabase" | SQL **server** > SQL **veritabanları** |
| Clientapplicationıd | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **uygulama kayıtları** > **ada göre ara** > **uygulama kimliği** |
| RedirectUri | Yeni URI ("https://mywebserver.com/") | **Azure Active Directory** > **uygulama kayıtları** > **ada göre ara** >  *[-App-kaydınız]*  > **ayarları** > **redirecturne**<br /><br />Bu makalede, burada kullanılmadığından, tüm geçerli değerler RedirectUri için uygundur. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>SQL Server Management Studio ile doğrula

C# Programı çalıştırmadan önce, kurulum ve yapılandırmaların SQL Server Management Studio (SSMS) ' de doğru olup olmadığını kontrol etmek iyi bir fikirdir. Daha C# sonra herhangi bir program hatası, kaynak koduna daha dar olabilir.

### <a name="verify-sql-database-firewall-ip-addresses"></a>SQL veritabanı güvenlik duvarı IP adreslerini doğrulama

Aynı bilgisayardaki SSMS 'yi, C# programı çalıştırmayı planladığınız aynı binaya çalıştırın. Bu test için, herhangi bir **kimlik doğrulama** modu Tamam olur. Veritabanı sunucusu güvenlik duvarının IP adresinizi kabul etmediğinin bir göstergesi varsa, yardım için bkz. [Azure SQL veritabanı sunucu düzeyi ve veritabanı düzeyinde güvenlik duvarı kuralları](sql-database-firewall-configure.md) .

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory Multi-Factor Authentication doğrulayın

SSMS 'yi yeniden çalıştırın. bu kez **kimlik doğrulaması** , **MFA desteğiyle Active Directory Universal**olarak ayarlanmıştır. Bu seçenek SSMS sürüm 17,5 veya üstünü gerektirir.

Daha fazla bilgi için bkz. [SSMS ve Azure AD için Multi-Factor Authentication yapılandırma](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Veritabanında bir Konuk Kullanıcı kullanıyorsanız, veritabanı için Azure AD etki alanı adını da sağlamanız gerekir: **ad etki alanı adı veya KIRACı kimliği** > **seçeneklerini** belirleyin. Azure portal etki alanı adını bulmak için **özel etki alanı adlarını** > **Azure Active Directory** seçin. C# Örnek programda, bir etki alanı adı sağlanması gerekli değildir.

## <a name="c-code-example"></a>C#kod örneği

Örnek C# program, [*Microsoft. IdentityModel. clients. ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) dll derlemesini kullanır.

Bu paketi yüklemek için, Visual Studio 'da **proje** > **NuGet Paketlerini Yönet**' i seçin. **Microsoft. IdentityModel. clients. ActiveDirectory**araması yapın ve bunları yükler.

Bu, C# kaynak kodu örneğidir.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Bu, C# test çıkışının bir örneğidir.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
