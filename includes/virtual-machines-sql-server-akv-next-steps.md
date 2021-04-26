---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 6195b949cc71043dfa7a12bdece7a311dbde5e21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559966"
---
## <a name="next-steps"></a>Sonraki adımlar

Azure Key Vault tümleştirmeyi etkinleştirdikten sonra SQL sanal makinenizde SQL Server şifrelemeyi etkinleştirebilirsiniz. İlk olarak, Anahtar Kasanızda bir asimetrik anahtar ve sanal makinenizde SQL Server içinde bir simetrik anahtar oluşturmanız gerekir. Daha sonra, veritabanınız ve yedeklemeleriniz için şifrelemeyi etkinleştirmek üzere T-SQL deyimlerini yürütebileceksiniz.

Avantajlarından faydalanabilirsiniz birkaç şifreleme biçimi vardır:

* [Saydam Veri Şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Şifrelenmiş yedeklemeler](/sql/relational-databases/backup-restore/backup-encryption)
* [Sütun düzeyinde şifreleme (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

Aşağıdaki Transact-SQL betikleri, bu alanların her biri için örnekler sağlar.

### <a name="prerequisites-for-examples"></a>Örnek önkoşulları

Her örnek iki önkoşul temel alır: **CONTOSO_KEY** adlı anahtar kasanızdan asimetrik bir anahtar ve **Azure_EKM_cred** adlı Akv tümleştirme özelliği tarafından oluşturulan kimlik bilgileri. Aşağıdaki Transact-SQL komutları, örnekleri çalıştırmak için bu önkoşulları ayarlar.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Saydam Veri Şifrelemesi (TDE)

1. TDE veritabanı altyapısı tarafından kullanılacak SQL Server bir oturum açma oluşturun, ardından kimlik bilgisini buna ekleyin.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. TDE için kullanılacak veritabanı şifreleme anahtarını oluşturun.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Şifrelenmiş yedeklemeler

1. Yedeklemeleri şifrelemek için veritabanı altyapısı tarafından kullanılacak SQL Server bir oturum açma oluşturun ve kimlik bilgisini buna ekleyin.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Anahtar kasasında depolanan asimetrik anahtarla şifrelemeyi belirterek veritabanını yedekleyin.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Sütun düzeyinde şifreleme (CLE)

Bu betik, anahtar kasasındaki asimetrik anahtarla korunan bir simetrik anahtar oluşturur ve sonra veritabanındaki verileri şifrelemek için simetrik anahtar kullanır.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Ek kaynaklar

Bu şifreleme özelliklerinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [SQL Server şifreleme ÖZELLIKLERIYLE ekm kullanma](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server#UsesOfEKM).

Bu makaledeki adımlarda, zaten bir Azure sanal makinesinde SQL Server çalıştırmakta olduğunuz varsayılmaktadır. Aksi takdirde bkz. [Azure 'da SQL Server sanal makinesi sağlama](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md). Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer yönergeler için bkz. [Azure sanal makinelerine genel bakış SQL Server](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).