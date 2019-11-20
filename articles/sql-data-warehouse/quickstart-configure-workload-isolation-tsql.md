---
title: 'Hızlı başlangıç: iş yükü yalıtımını yapılandırma-T-SQL'
description: İş yükü yalıtımını yapılandırmak için T-SQL ' i kullanın.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 10/29/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 92f8aaad1cc3279142d419faa2852406c2956595
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685987"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Hızlı başlangıç: T-SQL kullanarak iş yükü yalıtımını yapılandırma

Bu hızlı başlangıçta hızlı bir şekilde veri yükleme için kaynak ayırma için bir iş yükü grubu ve sınıflandırıcının oluşturulması gerekir. İş yükü grubu, sistem kaynaklarının %20 ' sini bir veri yüküne ayırır.  İş yükü Sınıflandırıcısı, istekleri verileri yükleyen iş yükü grubuna atayacaktır.  Veri yükleri için %20 yalıtımıyla SLA 'Lara vurmaları garanti edilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!NOTE]
> Bir SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir.  Ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Ön koşullar
 
Bu hızlı başlangıç, zaten bir SQL veri ambarınızın olduğunu ve DENETIM VERITABANı izinlerine sahip olduğunuzu varsayar. Gerekiyorsa [mySampleDataWarehouse](create-data-warehouse-portal.md) adlı bir veri ambarı oluşturmak için **Oluşturma ve Bağlanma - portal** bölümünü kullanabilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-login-for-dataloads"></a>DataLoads için oturum açma oluşturma

' ELTLogin' için [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql) ' yı kullanarak `master` veritabanında SQL Server kimlik doğrulaması oturumu oluşturun.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Kullanıcı Oluştur

MySampleDataWarehouse içinde [Kullanıcı oluşturma](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "Eltlogin"

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>İş yükü grubu oluşturma
%20 yalıtım ile DataLoads için bir [iş yükü grubu](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) oluşturun.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>İş yükü Sınıflandırıcısı oluşturma

Eltlogın 'i DataLoads iş yükü grubuyla eşlemek için bir [iş yükü Sınıflandırıcısı](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) oluşturun.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'ELTLogin'
      ,MEMBERNAME = 'DataLoads')
;
```

## <a name="view-existing-workload-groups-and-classifiers"></a>Mevcut iş yükü gruplarını ve sınıflandırıcıları görüntüleme

```sql
SELECT * FROM 
sys.workload_management_workload_groups

SELECT * FROM 
sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Veri ambarı birimleri ve veri Ambarınızda depolanan veriler için ücret ödersiniz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır.

- Verileri depoda tutmak istiyorsanız, veri ambarını kullanmadığınız zamanlarda işlemi duraklatabilirsiniz. İşlem duraklatıldığında yalnızca veri depolama alanı için ücret ödersiniz. Verilerle çalışmaya hazırsanız, işlem işlemini sürdürmeniz gerekir.
- Gelecekteki ücretlendirmeleri kaldırmak istiyorsanız, veri ambarını silebilirsiniz.

Kaynakları temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın, veri Ambarınızda öğesini seçin.

    ![Kaynakları temizleme](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlem duraklatmak için **Duraklat** düğmesini seçin. Veri ambarı duraklatıldığında, bir **Başlat** düğmesi görürsünüz.  İşlem işlemini sürdürmesini sağlamak için **Başlat**' ı seçin.

3. İşlem veya depolama için ücretlendirilmemek üzere veri ambarını kaldırmak için **Sil**' i seçin.

4. Oluşturduğunuz SQL Server 'ı kaldırmak için önceki görüntüde **MyNewServer-20180430.Database.Windows.net** ' ı seçin ve **Sil**' i seçin.  Sunucuyu silmek sunucuyla ilişkili tüm veritabanlarını da sileceğinden bu silme işlemini gerçekleştirirken dikkatli olun.

5. Kaynak grubunu kaldırmak için **Myresourcegroup**' ı seçin ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Artık bir iş yükü grubu oluşturdunuz. Nasıl çalıştığını görmek için, ELTLogin olarak birkaç sorgu çalıştırın. Sorguları ve atanan iş yükü grubunu görüntülemek için bkz. [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) .
- Azure SQL veri ambarı iş yükü yönetimi hakkında daha fazla bilgi için bkz. [Iş yükü yönetimi](sql-data-warehouse-workload-management.md) ve [iş yükü yalıtımı](sql-data-warehouse-workload-isolation.md).
