---
title: Azure SSIS Integration Runtime için AAD 'yi etkinleştir
description: Bu makalede Azure-SSIS Integration Runtime oluşturmak için Azure Data Factory yönetilen kimlikle Azure Active Directory kimlik doğrulamasının nasıl etkinleştirileceği açıklanır.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: cd3f590e1869b28f0ac08ce98da32a98160e4e86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392740"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime için Azure Active Directory kimlik doğrulamasını etkinleştirme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, Azure Active Directory (Azure AD) kimlik doğrulamasının Azure Data Factory (ADF) için yönetilen kimlikle nasıl etkinleştirileceği ve geleneksel kimlik doğrulama yöntemleri yerine (SQL kimlik doğrulaması gibi) kullanılması gösterilmektedir:

- SQL veritabanı veya SQL yönetilen örneği için sizin adınıza SSIS Katalog veritabanı 'nı (SSıSDB) sağlayacak bir Azure-SSIS Integration Runtime (IR) oluşturun.

- Azure-SSIS IR üzerinde SSIS paketlerini çalıştırırken çeşitli Azure kaynaklarına bağlanın.

ADF 'nizin yönetilen kimliği hakkında daha fazla bilgi için bkz. [yönetilen kimlik Data Factory](./data-factory-service-identity.md).

> [!NOTE]
>
> - Bu senaryoda, ADF 'niz için yönetilen kimliğe sahip Azure AD kimlik doğrulaması yalnızca, SSIS IR 'nizin oluşturulması ve sonraki başlangıç işlemlerinde ve SSıSDB 'nin sağlaması ve SSD 'ye bağlanması için kullanılır. SSIS paketi yürütmeleri için SSIS IR 'niz, SSıSDB sağlama sırasında oluşturulan tam olarak yönetilen hesaplarla SQL kimlik doğrulaması kullanarak SSSıSDB 'ye bağlanmaya devam edecektir.
> - SSIS IR 'nizi SQL kimlik doğrulaması kullanarak zaten oluşturduysanız, bunu şu anda PowerShell aracılığıyla Azure AD kimlik doğrulaması kullanacak şekilde yeniden yapılandıramazsınız, ancak bunu Azure portal/ADF uygulaması aracılığıyla yapabilirsiniz. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL veritabanı 'nda Azure AD 'yi etkinleştirme

SQL veritabanı, bir Azure AD kullanıcısı ile veritabanı oluşturmayı destekler. İlk olarak, ADF 'niz için yönetilen kimliğe sahip bir Azure AD grubu oluşturmanız gerekir. Daha sonra, bir Azure AD kullanıcısını SQL veritabanınız için Active Directory yöneticisi olarak ayarlamanız ve ardından bu kullanıcıyı kullanarak SQL Server Management Studio (SSMS) üzerinde bağlantı oluşturmanız gerekir. Son olarak, Azure AD grubunu temsil eden bir kapsanan Kullanıcı oluşturmanız gerekir, böylece ADF 'nizin yönetilen kimliği, sizin adınıza SSıSDB oluşturmak için Azure-SSIS IR tarafından kullanılabilir.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>ADF 'niz için yönetilen kimlikle bir üye olarak bir Azure AD grubu oluşturun

Azure AD PowerShell kullanarak mevcut bir Azure AD grubunu kullanabilir veya yeni bir tane oluşturabilirsiniz.

1.  [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) modülünü yükler.

2.  Kullanarak oturum açın `Connect-AzureAD` , bir grup oluşturmak ve bir değişkende kaydetmek için aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Sonuç aşağıdaki örneğe benzer şekilde görünür ve aynı zamanda değişken değerini de görüntüler:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  ADF 'niz için yönetilen kimliği gruba ekleyin. Asıl yönetilen kimlik nesne KIMLIĞINI (ör. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc) almak için [Data Factory Için yönetilen kimlik](./data-factory-service-identity.md) nesnesini izleyebilirsiniz.

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Grup üyeliğini daha sonra da denetleyebilirsiniz.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>SQL veritabanı için Azure AD kimlik doğrulamasını yapılandırma

Aşağıdaki adımları kullanarak, [SQL Ile Azure AD kimlik doğrulamasını yapılandırabilir ve yönetebilirsiniz](../azure-sql/database/authentication-aad-configure.md) :

1.  Azure Portal, sol taraftaki gezinmede **tüm hizmetler**  ->  **SQL Server** ' ı seçin.

2.  Azure AD kimlik doğrulamasıyla yapılandırmak için SQL veritabanı 'nda sunucunuzu seçin.

3.  Dikey pencerenin **Ayarlar** bölümünde **yönetici Active Directory**' yi seçin.

4.  Komut çubuğunda **yönetici ayarla**' yı seçin.

5.  Sunucu Yöneticisi yapmak için bir Azure AD Kullanıcı hesabı seçin ve ardından Seç ' i seçin **.**

6.  Komut çubuğunda Kaydet ' i seçin **.**

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>SQL veritabanında Azure AD grubunu temsil eden bir kapsanan Kullanıcı oluşturma

Bu sonraki adım için [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) gerekir.

1. SSMS 'yi başlatın.

2. **Sunucuya Bağlan** iletişim kutusunda sunucu **adı** alanına sunucunuzun adını girin.

3. **Kimlik doğrulama** alanında **MFA desteğiyle Active Directory-Universal** ' i seçin (diğer iki Active Directory kimlik doğrulama türünü de KULLANABILIRSINIZ, bkz. [SQL Ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md)).

4. **Kullanıcı adı** alanına, sunucu yöneticisi olarak AYARLADıĞıNıZ Azure AD hesabının adını girin, örneğin testuser@xxxonline.com .

5. **Bağlan** ' ı seçin ve oturum açma işlemini doldurun.

6. **Nesne Gezgini**, **veritabanları**  ->  **sistem veritabanları** klasörünü genişletin.

7. **Ana** veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

8. Sorgu penceresinde, aşağıdaki T-SQL komutunu girin ve araç çubuğunda **Yürüt** ' ü seçin.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Komutun, grubu temsil eden bir kapsanan Kullanıcı oluşturması başarıyla tamamlanır.

9. Sorgu penceresini temizleyin, aşağıdaki T-SQL komutunu girin ve araç çubuğunda **Yürüt** ' ü seçin.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Komutun, içerilen kullanıcıya bir veritabanı (SSSıSDB) oluşturma özelliği verilerek başarıyla tamamlanmalıdır.

10. SSSıSDB 'niz SQL kimlik doğrulaması kullanılarak oluşturulduysa ve erişmek için Azure-SSIS IR Azure AD kimlik doğrulamasını kullanmak üzere geçiş yapmak istiyorsanız, önce **ana** veritabanına izin verme adımlarının başarıyla tamamlandığını doğrulayın. Ardından, **SSISDB** veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

11. Sorgu penceresinde, aşağıdaki T-SQL komutunu girin ve araç çubuğunda **Yürüt** ' ü seçin.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Komutun, grubu temsil eden bir kapsanan Kullanıcı oluşturması başarıyla tamamlanır.

12. Sorgu penceresini temizleyin, aşağıdaki T-SQL komutunu girin ve araç çubuğunda **Yürüt** ' ü seçin.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Komutun, bulunan kullanıcıya SSSıSDB 'ye erişme özelliği verilerek başarıyla tamamlanmalıdır.

## <a name="enable-azure-ad-on-sql-managed-instance"></a>SQL yönetilen örneği üzerinde Azure AD 'yi etkinleştirme

SQL yönetilen örneği, ADF 'niz için yönetilen kimliğe sahip bir veritabanı oluşturmayı destekler. ADF 'niz için yönetilen kimliğe bir Azure AD grubuna katılmanız veya SQL yönetilen örneği 'nde bu grubu temsil eden bir kapsanan Kullanıcı oluşturmanız gerekir.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için Azure AD kimlik doğrulamasını yapılandırma

[SQL yönetilen örneği için Azure Active Directory Yöneticisi sağlama](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)bölümündeki adımları izleyin.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>ADF 'niz için yönetilen kimliği, SQL yönetilen örneği 'nde Kullanıcı olarak ekleyin

Bu sonraki adım için [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) gerekir.

1.  SSMS 'yi başlatın.

2.  **Sysadmin** olan SQL Server bir HESABı kullanarak SQL yönetilen örneğine bağlanın. Bu, Azure SQL yönetilen örneği için Azure AD Server sorumluları (oturum açmalar) GA olduktan sonra kaldırılacak geçici bir kısıtlamadır. Oturum açmayı oluşturmak için bir Azure AD yönetici hesabı kullanmayı denerseniz şu hatayı görürsünüz: Msg 15247, Level 16, State 1, 1. satır, kullanıcının bu eylemi gerçekleştirme izni yok.

3.  **Nesne Gezgini**, **veritabanları**  ->  **sistem veritabanları** klasörünü genişletin.

4.  **Ana** veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

5.  Sorgu penceresinde, ADF 'nizin yönetilen kimliğini Kullanıcı olarak eklemek için aşağıdaki T-SQL betiğini yürütün

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Komut, ADF 'niz için yönetilen kimliğe (SSSıSDB) bir veritabanı oluşturma özelliği verilerek başarıyla tamamlanır.

6.  SSSıSDB 'niz SQL kimlik doğrulaması kullanılarak oluşturulduysa ve erişmek için Azure-SSIS IR Azure AD kimlik doğrulamasını kullanmak üzere geçiş yapmak istiyorsanız, önce **ana** veritabanına izin verme adımlarının başarıyla tamamlandığını doğrulayın. Ardından, **SSISDB** veritabanına sağ tıklayın ve **Yeni sorgu**' yı seçin.

7.  Sorgu penceresinde, aşağıdaki T-SQL komutunu girin ve araç çubuğunda **Yürüt** ' ü seçin.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Komutun, ADF 'niz için SSıSDB 'ye erişme özelliği için yönetilen kimliğe izin veren başarıyla tamamlanmalıdır.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure portal/ADF uygulamasında Azure-SSIS IR sağlama

Azure-SSIS IR Azure portal/ADF uygulamasında sağladığınızda, **SQL ayarları** SAYFASıNDA, **ADF 'niz IÇIN yönetilen kimlikle AAD kimlik doğrulaması kullan** seçeneğini belirleyin. Aşağıdaki ekran görüntüsünde, SSıSDB barındıran SQL veritabanı ile IR ayarları gösterilmektedir. SSSıSDB barındıran SQL yönetilen örneği olan IR için, **Katalog veritabanı hizmet katmanı** ve **Azure hizmetlerinin erişime izin ver** ayarları geçerli değildir.

Azure-SSIS IR oluşturma hakkında daha fazla bilgi için, bkz. [Azure Data Factory Azure-SSIS tümleştirme çalışma zamanı oluşturma](./create-azure-ssis-integration-runtime.md).

![Azure-SSIS tümleştirme çalışma zamanı ayarları](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell ile Azure-SSIS IR sağlama

PowerShell ile Azure-SSIS IR sağlamak için aşağıdaki işlemleri yapın:

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modülünü yükler.

2.  Betiğinizdeki `CatalogAdminCredential` parametre ayarlamayın. Örnek:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>SSIS paketlerini yönetilen kimlik doğrulaması ile çalıştırma

Azure-SSIS IR üzerinde SSIS paketlerini çalıştırdığınızda, çeşitli Azure kaynaklarına bağlanmak için yönetilen kimlik kimlik doğrulamasını kullanabilirsiniz. Şu anda aşağıdaki bağlantı yöneticilerinde yönetilen kimlik kimlik doğrulamasını zaten destekliyoruz.

- [OLE DB bağlantı Yöneticisi](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Bağlantı Yöneticisi](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure depolama bağlantı Yöneticisi](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
