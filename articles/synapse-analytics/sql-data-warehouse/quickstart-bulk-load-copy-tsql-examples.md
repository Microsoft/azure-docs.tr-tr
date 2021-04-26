---
title: COPY ifadesiyle kimlik doğrulama mekanizmaları
description: Verileri toplu olarak yüklemek için kimlik doğrulama mekanizmalarını özetler
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 510f2556fba42176817b782fe48d01d76eaa3fd7
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568463"
---
# <a name="securely-load-data-using-synapse-sql"></a>SYNAPSE SQL kullanarak güvenli bir şekilde veri yükleme

Bu makalede, [Copy ifadesiyle](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)ilgili güvenli kimlik doğrulama mekanizmalarına örnekler verilmektedir. COPY yöntemi, SYNAPSE SQL 'de verileri toplu yüklemenin en esnek ve güvenli yoludur.
## <a name="supported-authentication-mechanisms"></a>Desteklenen kimlik doğrulama mekanizmaları

Aşağıdaki matris, her dosya türü ve depolama hesabı için desteklenen kimlik doğrulama yöntemlerini açıklar. Bu, kaynak depolama konumu ve hata dosyası konumu için geçerlidir.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure blob depolama**  | SAS/MSı/HIZMET SORUMLUSU/ANAHTARı/AAD |                      SAS/ANAHTAR                       |                      SAS/ANAHTAR                       |
| **Azure Data Lake Gen2** | SAS/MSı/HIZMET SORUMLUSU/ANAHTARı/AAD | SAS (blob<sup>1</sup>)/MSI (DFS<sup>2</sup>)/SERVICE PRINCIPAL/Key/AAD | SAS (blob<sup>1</sup>)/MSI (DFS<sup>2</sup>)/SERVICE PRINCIPAL/Key/AAD |

1: Bu kimlik doğrulama yöntemi için dış konum yolunuzda. blob uç noktası (**. blob**. Core.Windows.net) gereklidir.

2: Bu kimlik doğrulama yöntemi için dış konum yolunuzda. DFS uç noktası (**. DFS**. Core.Windows.net) gereklidir.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Satır Sonlandırıcı olarak LF ile depolama hesabı anahtarı (UNIX stili yeni satır)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Satır besleme/yeni satır karakterini belirtmek için onaltılık değeri (0x0A) kullanın. Note, COPY ifadesinin ' \n ' dizesini ' \r\n ' (satır başı yeni satır) olarak yorumlayacak.

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Satır Sonlandırıcı olarak CRLF ile paylaşılan erişim Imzaları (SAS) (Windows stili yeni satır)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - ' \R\r\n ' olarak yorumlanabilecek ve sorunların ayrıştırmasına neden olabilecek ROWSONLANDıRıCı ' \r\n ' olarak belirtmeyin

## <a name="c-managed-identity"></a>C. Yönetilen Kimlik

Depolama Hesabınız VNet 'e eklendiğinde yönetilen kimlik kimlik doğrulaması gerekir. 

### <a name="prerequisites"></a>Önkoşullar

1. Bu [kılavuzu](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) kullanarak Azure PowerShell'i yükleyin.
2. Genel amaçlı v1 veya blob depolama hesabınız varsa öncelikle bu [kılavuzda](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) yer alan adımları izleyerek genel amaçlı v2 sürümüne yükseltmeniz gerekir.
3. Azure depolama hesabı **güvenlik duvarları ve sanal ağlar** ayarları menüsünde **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin vermeniz** gerekir. Daha fazla bilgi için bu [kılavuza](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) bakın.

#### <a name="steps"></a>Adımlar

1. Tek başına adanmış bir SQL havuzunuz varsa, PowerShell kullanarak SQL Server 'ı Azure Active Directory (AAD) ile kaydedin: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Bu adım, bir Synapse çalışma alanı içindeki adanmış SQL havuzları için gerekli değildir.

1. Bir Synapse çalışma alanınız varsa, çalışma alanınızın sistem tarafından yönetilen kimliğini kaydedin:

   1. Azure portal SYNAPSE çalışma alanınıza gidin
   2. Yönetilen kimlikler dikey penceresine git 
   3. "İşlem hatları Izin ver" seçeneğinin etkinleştirildiğinden emin olun
   
   ![Çalışma alanı sistem MSI 'yi Kaydet](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Bu [Kılavuzu](../../storage/common/storage-account-create.md)kullanarak **genel amaçlı v2 depolama hesabı** oluşturun.

   > [!NOTE]
   >
   > - Genel amaçlı bir v1 veya blob depolama hesabınız varsa, önce bu [Kılavuzu](../../storage/common/storage-account-upgrade.md)kullanarak **v2 'ye yükseltmeniz** gerekir.
   > - Azure Data Lake Storage 2. ile ilgili bilinen sorunlar için lütfen bu [kılavuza](../../storage/blobs/data-lake-storage-known-issues.md)bakın.

1. Depolama hesabınız altında **Access Control (IAM)** bölümüne gidin ve **rol ataması Ekle**' yi seçin. Azure Active Directory (AAD) ile kaydettiğiniz adanmış SQL havuzunuzu barındıran sunucuya veya çalışma alanına **Depolama Blobu verileri katkıda bulunan** Azure rolü atayın.

   > [!NOTE]
   > Yalnızca sahibi ayrıcalığına sahip Üyeler bu adımı gerçekleştirebilir. Çeşitli Azure yerleşik rolleri için bu [kılavuza](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)bakın.
   
    > [!IMPORTANT]
    > **Depolama** **blobu veri** sahibini, katkıda bulunan veya okuyucu Azure rolünü belirtin. Bu roller, sahip, katkıda bulunan ve okuyucunuzun Azure yerleşik rollerinin farklıdır. 

    ![Azure RBAC iznini yükleme için verme](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Şimdi "yönetilen kimlik" belirten kopyalama ifadesini çalıştırabilirsiniz:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Azure Active Directory Kimlik Doğrulaması
#### <a name="steps"></a>Adımlar

1. Depolama hesabınız altında **Access Control (IAM)** bölümüne gidin ve **rol ataması Ekle**' yi seçin. Azure AD kullanıcısına **Depolama Blobu veri sahibi, katkıda bulunan veya okuyucu** Azure rolü atayın. 

    > [!IMPORTANT]
    > **Depolama** **blobu veri** sahibini, katkıda bulunan veya okuyucu Azure rolünü belirtin. Bu roller, sahip, katkıda bulunan ve okuyucunuzun Azure yerleşik rollerinin farklıdır.

    ![Azure RBAC iznini yükleme için verme](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Aşağıdaki [belgelere](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)gıderek Azure AD kimlik doğrulamasını yapılandırın. 

3. Artık herhangi bir kimlik bilgisi belirtmeden COPY ifadesini çalıştırabileceğiniz Active Directory kullanarak SQL havuzunuza bağlanın:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Hizmet Sorumlusu Kimlik Doğrulaması
#### <a name="steps"></a>Adımlar

1. [Azure Active Directory uygulaması oluşturma](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Uygulama KIMLIĞINI al](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [Kimlik doğrulama anahtarını al](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [V1 OAuth 2,0 belirteç uç noktasını alın](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. Depolama hesabınızdaki [Azure AD uygulamanıza okuma, yazma ve yürütme Izinleri atama](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder)
6. Şimdi COPY ifadesini çalıştırabilirsiniz:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - OAuth 2,0 belirteci uç noktasının **v1** sürümünü kullanın

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı sözdizimi için [kopyalama ekstresi makale](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) makalesini denetleyin
- En iyi yöntemleri yüklemek için [veri yüklemeye genel bakış](./design-elt-data-loading.md#what-is-elt) makalesini denetleyin
