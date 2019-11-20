---
title: TDE sertifikası ile yönetilen örneği geçirme
description: Azure SQL veritabanı yönetilen örneği 'ne saydam veri şifrelemesi ile bir veritabanının veritabanı şifreleme anahtarını koruyan sertifikayı geçirme
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 202267d4fffc7c6f0d99cd2a6ef3bbee82947a81
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132397"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>TDE korumalı veritabanının sertifikasını Azure SQL veritabanı yönetilen örneği 'ne geçirme

Yerel geri yükleme seçeneği kullanılarak [Saydam veri şifrelemesi](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) tarafından korunan bir VERITABANıNı Azure SQL veritabanı yönetilen örneği 'ne geçirirken, şirket Içi veya ıaas SQL Server karşılık gelen sertifikanın veritabanı geri yüklemeden önce geçirilmesi gerekir. Bu makale, sertifikanın Azure SQL Veritabanı Yönetilen Örneği’ne el ile geçiş işleminde size yol gösterir:

> [!div class="checklist"]
> * Sertifikayı Kişisel Bilgi Değişimi (.pfx) dosyası olarak dışarı aktarma
> * Sertifikayı dosyadan base-64 dizesine ayıklama
> * PowerShell cmdlet’ini kullanarak bunu karşıya yükleme

Tam yönetilen hizmet kullanılarak hem TDE korumalı veritabanının hem de ilgili sertifikanın sorunsuz geçişini sağlamaya yönelik alternatif bir seçenek için bkz. [Azure Veritabanı Geçiş Hizmeti'ni kullanarak şirket içi veritabanınızı Yönetilen Örneğe geçirme](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Geçirilen sertifika yalnızca TDE korumalı veritabanını geri yüklemek için kullanılır. Geri yükleme işlemi yapıldıktan kısa süre sonra, geçirilen sertifika, örnekte belirlediğiniz saydam veri şifrelemesinin türüne bağlı olarak, hizmet tarafından yönetilen sertifika veya asimetrik anahtar tarafından farklı bir koruyucu ile değiştirilmiştir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Bu makaledeki adımları tamamlayabilmeniz için şu önkoşullar gereklidir:

- Şirket içi sunucuya veya dosya olarak dışarı aktarılan sertifikaya erişimi olan başka bir bilgisayara yüklenmiş [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) komut satırı aracı. Pvk2Pfx aracı, tek başına kendi içinde bir komut satırı ortamı olan [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk)'in bir parçasıdır.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell) sürüm 5.0 veya üstü yüklenmiş olmalıdır.
- Azure PowerShell modül [yüklendi ve güncelleştirildi](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Az. SQL modülü](https://www.powershellgallery.com/packages/Az.Sql).
  PowerShell modülünü yüklemek/güncelleştirmek için PowerShell’de şu komutları çalıştırın:

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>TDE sertifikasını Kişisel Bilgi Değişimi (.pfx) dosyasında dışarı aktarma

Sertifika doğrudan kaynak SQL Server’dan veya sertifika depolama alanından (burada tutuluyorsa) dışarı aktarılabilir.

### <a name="export-certificate-from-the-source-sql-server"></a>Sertifikayı kaynak SQL Server’dan dışarı aktarma

Sertifikayı SQL Server Management Studio ile dışarı aktarmak ve bunu pfx biçimine dönüştürmek için aşağıdaki adımları kullanın. Bu adımlarda sertifikanın dosya adları ve yolları için *TDE_Cert* ve *full_path* genel adları kullanılır. Bunlar gerçek adlarla değiştirilmelidir.

1. SSMS’de yeni bir sorgu penceresi açın ve kaynak SQL Server’a bağlanın.
2. TDE korumalı veritabanlarını listelemek ve geçirilecek veritabanının şifrelemesini koruyan sertifikanın adını almak için şu betiği kullanın:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE sertifikalarının listesi](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. Sertifikayı, ortak ve özel anahtar bilgilerinin tutulduğu dosya çiftine (.cer ve .pvk) dışarı aktarmak için şu betiği yürütün:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![yedek TDE sertifikası](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. Pvk2Pfx aracını kullanarak, sertifika bilgilerini yeni oluşturulmuş dosya çiftinden Kişisel Bilgi Değişimi (.pfx) dosyasına kopyalamak için PowerShell konsolunu kullanın:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Sertifika depolama alanından sertifikayı dışarı aktarma

Sertifika SQL Server’ın yerel makine sertifika depolama alanında tutuluyorsa şu adımlar kullanılarak dışarı aktarılabilir:

1. PowerShell konsolunu açın ve Microsoft Yönetim Konsolu’nun Sertifikalar ek bileşenini açmak için şu komutu yürütün:

   ```powershell
   certlm
   ```

2. Sertifikalar MMC ek bileşeninde, sertifikaların listesini görmek için Kişisel -> Sertifikalar yolunu genişletin

3. Sertifikaya sağ tıklayın ve Dışarı Aktar... seçeneğine tıklayın

4. Sertifikayı ve özel anahtarı Kişisel Bilgi Değişimi biçiminde dışarı aktarmak için sihirbazı izleyin

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Azure PowerShell cmdlet 'ini kullanarak Azure SQL veritabanı yönetilen örneği 'ne sertifika yükleme

1. PowerShell’deki hazırlık adımlarını başlatın:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. Tüm hazırlık adımları tamamladıktan sonra, base-64 kodlamalı sertifikayı hedef Yönetilen Örneğe yüklemek için şu komutları çalıştırın:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Sertifikaya artık belirtilen Yönetilen Örnekten ulaşılabilir ve buna karşılık gelen TDE korumalı veritabanının yedeği başarılı bir şekilde geri yüklenebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şirket içi veya IaaS SQL Server Azure SQL veritabanı yönetilen örneği ' ne Saydam Veri Şifrelemesi ile veritabanının şifreleme anahtarını koruyan sertifikayı nasıl geçirebileceğiniz öğrendiniz.

Azure SQL Veritabanı Yönetilen Örneği’nde veritabanı yedeğini geri yüklemeyi öğrenmek için bkz. [Veritabanı yedeğini Azure SQL Veritabanı Yönetilen Örneği’ne geri yükleme](sql-database-managed-instance-get-started-restore.md).
