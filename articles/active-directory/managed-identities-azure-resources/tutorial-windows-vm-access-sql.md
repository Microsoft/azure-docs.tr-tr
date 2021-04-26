---
title: 'Öğretici: Azure SQL veritabanına erişmek için yönetilen bir kimlik kullanma-Windows-Azure AD'
description: Azure SQL veritabanına erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma sürecinde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856521"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Öğretici: Azure SQL hizmetine erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, Azure SQL veritabanına erişmek için bir Windows sanal makinesi (VM) için sistem tarafından atanan bir kimliğin nasıl kullanılacağı gösterilmektedir. Yönetilen Hizmet Kimlikleri Azure tarafından otomatik olarak yönetilir kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmanıza olanak tanır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
>
> * VM 'nize Azure SQL veritabanı erişimi verme
> * Azure AD kimlik doğrulamasını etkinleştirme
> * VM’nin sistem tarafından atanan kimliğini temsil eden veritabanında içerilen kullanıcı oluşturma
> * VM kimliğini kullanarak bir erişim belirteci alın ve Azure SQL veritabanını sorgulamak için kullanın

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Etkinleştir

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Erişim verme

VM 'nize Azure SQL veritabanı 'nda bir veritabanına erişim izni vermek için, var olan bir [MANTıKSAL SQL Server](../../azure-sql/database/logical-servers.md) kullanabilir veya yeni bir tane oluşturabilirsiniz. Azure portalını kullanarak yeni sunucu ve veritabanı oluşturmak için bu [Azure SQL hızlı başlangıcını](../../azure-sql/database/single-database-create-quickstart.md) izleyin. [Azure SQL belgeleri](/azure/sql-database/) arasında Azure CLI'nin ve Azure PowerShell'in kullanıldığı hızlı başlangıçlar da vardır.

VM'nize veritabanı erişimi verme işleminin iki adımı vardır:

1. Sunucu için Azure AD kimlik doğrulamasını etkinleştirin.
2. VM’nin sistem tarafından atanan kimliğini temsil eden veritabanında bir **içerilen kullanıcı** oluşturun.

### <a name="enable-azure-ad-authentication"></a>Azure AD kimlik doğrulamasını etkinleştirme

**[Azure AD kimlik doğrulamasını yapılandırmak](../../azure-sql/database/authentication-aad-configure.md)için:**

1. Azure portalında, sol gezintiden **SQL sunucuları**'nı seçin.
2. Azure AD kimlik doğrulaması için etkinleştirilecek SQL sunucusuna tıklayın.
3. Dikey pencerenin **Ayarlar** bölümünde **Active Directory yöneticisi**'ne tıklayın.
4. Komut çubuğunda **Yönetici ayarla**'ya tıklayın.
5. Sunucunun yöneticisi olacak bir Azure AD kullanıcı hesabı seçin ve **Seç**'e tıklayın.
6. Komut çubuğunda **Kaydet**'e tıklayın.

### <a name="create-contained-user"></a>Kapsanan kullanıcı oluştur

Bu bölümde, veritabanında VM 'nin sistem tarafından atanan kimliğini temsil eden kapsanan bir kullanıcının nasıl oluşturulacağı gösterilmektedir. Bu adım için [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) gerekir. Başlamadan önce, Azure Ad tümleştirmesiyle ilgili arka plan bilgileri için aşağıdaki makaleleri gözden geçirmeniz yararlı olabilir:

- [SQL veritabanı ve Azure SYNAPSE Analytics ile evrensel kimlik doğrulaması (MFA için SSMS desteği)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [SQL veritabanı veya Azure SYNAPSE Analytics ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](../../azure-sql/database/authentication-aad-configure.md)

SQL DB, benzersiz AAD görünen adları gerektirir. Bu şekilde, kullanıcılar, gruplar ve hizmet sorumluları (uygulamalar) gibi AAD hesaplarının ve yönetilen kimlik için etkinleştirilen VM adlarının, görünen adlarıyla ilgili AAD 'de benzersiz olarak tanımlanması gerekir. SQL DB, bu tür kullanıcıların T-SQL oluşturma işlemi sırasında AAD görünen adını denetler ve benzersiz değilse, komut belirli bir hesap için benzersiz bir AAD görünen adı sağlaması isteğinde bulunur.

**Kapsanan kullanıcı oluşturmak için:**

1. SQL Server Management Studio’yu başlatın.
2. **Sunucuya Bağlan** iletişim kutusunda sunucu **adı** alanına sunucunuzun adını girin.
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

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    Komutun başarıyla tamamlanması ve VM’nin sistem tarafından atanan kimliği için içerilen kullanıcıyı oluşturması gerekir.
11. Sorgu penceresini temizleyin, aşağıdaki satırı girin ve araç çubuğunda **Yürüt**'e tıklayın:

    > [!NOTE]
    > Aşağıdaki komutta yer alan `VMName`, önkoşullar bölümünde sistem tarafından atanan kimliği etkinleştirdiğiniz VM’nin adıdır.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Komutun başarıyla tamamlanması ve içerilen kullanıcıya veritabanının tamamını okuma erişimi vermesi gerekir.

VM 'de çalışan kod artık sistem tarafından atanan yönetilen kimliği kullanarak bir belirteç alabilir ve bu belirteci kullanarak sunucuda kimlik doğrulaması yapabilir.

## <a name="access-data"></a>Verilere erişme

Bu bölümde, VM 'nin sistem tarafından atanan yönetilen kimliğini kullanarak bir erişim belirtecinin nasıl alınacağı ve Azure SQL çağrısı için nasıl kullanılacağı gösterilmektedir. Azure SQL, Azure AD kimlik doğrulamasını yerel olarak desteklediğinden Azure kaynakları için yönetilen kimlikler kullanılarak alınan erişim belirteçlerini doğrudan kabul eder. SQL bağlantısı oluştururken **erişim belirteci** yöntemini kullanırsınız. Bu, Azure SQL’in Azure AD tümleştirmesi kapsamındadır ve bağlantı dizesinde kimlik bilgileri sağlama işleminden farklıdır.

Aşağıda, bir erişim belirteci kullanarak SQL bağlantısı açmak için bir .NET kod örneği verilmiştir. VM 'nin sistem tarafından atanan yönetilen kimliğin uç noktasına erişebilmesi için kodun VM üzerinde çalışması gerekir. **.NET Framework 4,6** veya üzeri ya da **.NET Core 2,2** veya üzeri, erişim belirteci metodunu kullanmak için gereklidir. AZURE-SQL-SERVERNAME ve DATABASE değerlerini uygun şekilde değiştirin. Azure SQL için kaynak KIMLIĞI ' ni aklınızda bulunur `https://database.windows.net/` .

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
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Alternatif olarak, uygulama yazmak ve VM'de dağıtmak zorunda kalmadan uçtan uca kurulumu test etmenin hızlı bir yöntemi PowerShell kullanmaktır.

1. Portalda, **Sanal Makineler**'e ve Windows sanal makinenize gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın.
2. Windows VM 'yi oluştururken eklediğiniz **Kullanıcı adınızı** ve **parolanızı** girin.
3. Sanal makineyle bir **Uzak Masaüstü bağlantısı** oluşturduğunuza göre, uzak oturumda **PowerShell** ' i açın.
4. PowerShell’in `Invoke-WebRequest` komutunu kullanarak, yerel yönetilen kimliğin uç noktasına Azure SQL için erişim belirteci alma isteğinde bulunun.

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

5. Sunucu bağlantısı açın. AZURE-SQL-SERVERNAME ve DATABASE değerlerini değiştirmeyi unutmayın.

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

## <a name="disable"></a>Devre Dışı Bırak

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure SQL veritabanına erişmek için sistem tarafından atanan yönetilen kimlik kullanmayı öğrendiniz. Azure SQL veritabanı hakkında daha fazla bilgi için bkz.

> [!div class="nextstepaction"]
> [Azure SQL Veritabanı](../../azure-sql/database/sql-database-paas-overview.md)
