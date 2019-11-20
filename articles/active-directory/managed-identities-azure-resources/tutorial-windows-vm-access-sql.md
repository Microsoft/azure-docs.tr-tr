---
title: Azure SQL 'e erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma
description: Windows VM üzerinde bir sistem tarafından atanmış yönetilen kimlik kullanarak Azure SQL hizmetine erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/16/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a11c5489c97e1050e525c0b83c160c1360119b60
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433162"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Öğretici: Azure SQL hizmetine erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, Azure SQL Server'a erişmek amacıyla, Windows sanal makinesi (VM) için sistem tarafından atanmış bir kimliği nasıl kullanacağınız gösterilmektedir. Yönetilen Hizmet Kimlikleri Azure tarafından otomatik olarak yönetilir kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmanıza olanak tanır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM'nize Azure SQL sunucusu için erişim verme
> * SQL sunucusu için Azure AD kimlik doğrulamasını etkinleştirme
> * VM’nin sistem tarafından atanan kimliğini temsil eden veritabanında içerilen kullanıcı oluşturma
> * VM kimliğini kullanarak erişim belirteci alma ve Azure SQL sunucusunu sorgulamak için bunu kullanma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>VM'nize Azure SQL sunucusundaki bir veritabanı için erişim verme

VM’nize Azure SQL Server’daki bir veritabanına erişim vermek için var olan SQL sunucusunu kullanabilir veya yeni bir sunucu oluşturabilirsiniz. Azure portalını kullanarak yeni sunucu ve veritabanı oluşturmak için bu [Azure SQL hızlı başlangıcını](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) izleyin. [Azure SQL belgeleri](https://docs.microsoft.com/azure/sql-database/) arasında Azure CLI'nin ve Azure PowerShell'in kullanıldığı hızlı başlangıçlar da vardır.

VM'nize veritabanı erişimi verme işleminin iki adımı vardır:

1. SQL sunucusu için Azure AD kimlik doğrulamasını etkinleştirme.
2. VM’nin sistem tarafından atanan kimliğini temsil eden veritabanında bir **içerilen kullanıcı** oluşturun.

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>SQL sunucusu için Azure AD kimlik doğrulamasını etkinleştirme

Aşağıdaki adımları kullanarak [SQL sunucusu için Azure AD kimlik doğrulamasını yapılandırın](/azure/sql-database/sql-database-aad-authentication-configure):

1.  Azure portalında, sol gezintiden **SQL sunucuları**'nı seçin.
2.  Azure AD kimlik doğrulaması için etkinleştirilecek SQL sunucusuna tıklayın.
3.  Dikey pencerenin **Ayarlar** bölümünde **Active Directory yöneticisi**'ne tıklayın.
4.  Komut çubuğunda **Yönetici ayarla**'ya tıklayın.
5.  Sunucunun yöneticisi olacak bir Azure AD kullanıcı hesabı seçin ve **Seç**'e tıklayın.
6.  Komut çubuğunda **Kaydet**'e tıklayın.

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>VM’nin sistem tarafından atanan kimliğini temsil eden veritabanında içerilen kullanıcı oluşturma

Bu sonraki adım için, [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)'ya (SSMS) ihtiyacınız vardır. Başlamadan önce, Azure Ad tümleştirmesiyle ilgili arka plan bilgileri için aşağıdaki makaleleri gözden geçirmeniz yararlı olabilir:

- [SQL Veritabanı ve SQL Veri Ambarı ile Evrensel Kimlik Doğrulaması (MFA için SSMS desteği)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [SQL Veritabanı veya SQL Veri Ambarı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB, benzersiz AAD görünen adları gerektirir. Bu şekilde, yönetilen kimlik için etkinleştirilen kullanıcılar, gruplar ve hizmet sorumluları (uygulamalar) ve VM adları gibi AAD hesaplarının, görünen adlarıyla ilgili AAD 'de benzersiz olarak tanımlanması gerekir. SQL DB, bu tür kullanıcıların T-SQL oluşturma işlemi sırasında AAD görünen adını denetler ve benzersiz değilse, komut belirli bir hesap için benzersiz bir AAD görünen adı sağlaması isteğinde bulunur.

1. SQL Server Management Studio’yu başlatın.
2. **Sunucuya Bağlan** iletişim kutusunda, **Sunucu adı** alanına SQL sunucu adınızı girin.
3. **Kimlik Doğrulaması** alanında, **Active Directory - MFA ile Evrensel desteği**'ni seçin.
4. **Kullanıcı adı** alanında, sunucu yöneticisi olarak ayarladığınız Azure AD hesabının adını girin (örneğin, helen@woodgroveonline.com)
5. **Seçenekler**’e tıklayın.
6. **Veritabanına bağlan** alanında, yapılandırmak istediğiniz sistem dışı veritabanının adını girin.
7. **Bağlan**'a tıklayın. Oturum açma işlemini tamamlayın.
8. **Nesne Gezgini**'nde **Veritabanları** klasörünü genişletin.
9. Kullanıcı veritabanına sağ tıklayın ve **Yeni sorgu**'ya tıklayın.
10. Sorgu penceresinde, aşağıdaki satırı girin ve araç çubuğunda **Yürüt**'e tıklayın:

    > [!NOTE]
    > Aşağıdaki komutta yer alan `VMName`, önkoşullar bölümünde sistem tarafından atanan kimliği etkinleştirdiğiniz VM’nin adıdır.
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    Komutun başarıyla tamamlanması ve VM’nin sistem tarafından atanan kimliği için içerilen kullanıcıyı oluşturması gerekir.
11. Sorgu penceresini temizleyin, aşağıdaki satırı girin ve araç çubuğunda **Yürüt**'e tıklayın:

    > [!NOTE]
    > Aşağıdaki komutta yer alan `VMName`, önkoşullar bölümünde sistem tarafından atanan kimliği etkinleştirdiğiniz VM’nin adıdır.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Komutun başarıyla tamamlanması ve içerilen kullanıcıya veritabanının tamamını okuma erişimi vermesi gerekir.

VM'de çalıştırılan kod şimdi sistem tarafından atanan yönetilen kimliğinden belirteç alabilir ve belirteci kullanarak SQL Server'da kimlik doğrulaması yapabilir.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>VM’nin sistem tarafından atanan yönetilen kimliğini kullanarak erişim belirteci alma ve Azure SQL çağrısı yapmak için bunu kullanma

Azure SQL, Azure AD kimlik doğrulamasını yerel olarak desteklediğinden Azure kaynakları için yönetilen kimlikler kullanılarak alınan erişim belirteçlerini doğrudan kabul eder. SQL bağlantısı oluştururken **erişim belirteci** yöntemini kullanırsınız. Bu, Azure SQL’in Azure AD tümleştirmesi kapsamındadır ve bağlantı dizesinde kimlik bilgileri sağlama işleminden farklıdır.

Aşağıda, bir erişim belirteci kullanarak SQL bağlantısı açmak için bir .NET kod örneği verilmiştir. Bu kodun, VM’nin sistem tarafından atanan yönetilen kimliğinin uç noktasına erişebilmesi için VM üzerinde çalıştırılması gerekir. **.NET Framework 4,6** veya üzeri ya da **.NET Core 2,2** veya üzeri, erişim belirteci metodunu kullanmak için gereklidir. AZURE-SQL-SERVERNAME ve DATABASE değerlerini uygun şekilde değiştirin. Azure SQL için kaynak KIMLIĞI `https://database.windows.net/` ' dır.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Alternatif olarak, uygulama yazmak ve VM'de dağıtmak zorunda kalmadan uçtan uca kurulumu test etmenin hızlı bir yöntemi PowerShell kullanmaktır.

1.  Portalda, **Sanal Makineler**'e ve Windows sanal makinenize gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın.
2.  Windows VM'sini oluştururken eklendiğiniz hesabın **Kullanıcı adı** ve **Parola** değerlerini girin.
3.  Artık sanal makineyle **Uzak Masaüstü Bağlantısı**'nı oluşturduğunuza göre, uzak oturumda **PowerShell**'i açın.
4.  PowerShell’in `Invoke-WebRequest` komutunu kullanarak, yerel yönetilen kimliğin uç noktasına Azure SQL için erişim belirteci alma isteğinde bulunun.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Yanıtı JSON nesnesinden PowerShell nesnesine dönüştürün. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Yanıttan erişim belirtecini ayıklayın.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. SQL Server'a bir bağlantı açın. AZURE-SQL-SERVERNAME ve DATABASE değerlerini değiştirmeyi unutmayın.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ardından bir sorgu oluşturup sunucuya gönderin. TABLE değerini değiştirmeyi unutmayın.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Sorgunun sonuçlarını görüntülemek için `$DataSet.Tables[0]` değerini inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure SQL Server'a erişmek için sistem tarafından atanan yönetilen kimlik kullanmayı öğrendiniz. Azure SQL Server hakkında daha fazla bilgi için bkz:

> [!div class="nextstepaction"]
> [Azure SQL Veritabanı hizmeti](/azure/sql-database/sql-database-technical-overview)
