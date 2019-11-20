---
title: 'Hızlı başlangıç: iş yükü Sınıflandırıcısı oluşturma-T-SQL '
description: T-SQL ' y i kullanarak yüksek öneme sahip bir iş yükü Sınıflandırıcısı oluşturun.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 1375605b6dab385b53af9212023767003e686e60
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646299"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Hızlı başlangıç: T-SQL kullanarak iş yükü Sınıflandırıcısı oluşturma

Bu hızlı başlangıçta, kuruluşunuzun CEO 'SU için yüksek önem derecesine sahip bir iş yükü sınıflandırıcısını hızlı bir şekilde oluşturacaksınız. Bu iş yükü Sınıflandırıcısı, CEO sorgularının, sıradaki daha düşük öneme sahip diğer sorgulara göre öncelikli olması için izin verir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!NOTE]
> Bir SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir.  Ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıç, zaten bir SQL veri ambarınızın olduğunu ve DENETIM VERITABANı izinlerine sahip olduğunuzu varsayar. Gerekiyorsa [mySampleDataWarehouse](create-data-warehouse-portal.md) adlı bir veri ambarı oluşturmak için **Oluşturma ve Bağlanma - portal** bölümünü kullanabilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-login-for-theceo"></a>TheCEO için oturum açma oluştur

' TheCEO ' için [create LOGIN](/sql/t-sql/statements/create-login-transact-sql) ' i kullanarak `master` veritabanında SQL Server kimlik doğrulaması oturumu oluşturun.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Kullanıcı Oluştur

"TheCEO" adlı Kullanıcı, mySampleDataWarehouse içinde [Oluştur](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest)

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>İş yükü Sınıflandırıcısı oluşturma

Yüksek öneme sahip "TheCEO" için bir [iş yükü Sınıflandırıcısı](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) oluşturun.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Mevcut sınıflandırıcıları görüntüleme

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
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

- Artık bir iş yükü Sınıflandırıcısı oluşturdunuz. Nasıl çalıştığını görmek için birkaç sorguyu TheCEO olarak çalıştırın. Sorguları ve atanan önemi görüntülemek için bkz. [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) .
- Azure SQL veri ambarı iş yükü yönetimi hakkında daha fazla bilgi için bkz. [Iş yükü önemi](sql-data-warehouse-workload-importance.md) ve [iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md).
- [Iş yükü önemini yapılandırmak](sql-data-warehouse-how-to-configure-workload-importance.md) ve [Iş yükü yönetimini yönetmek ve izlemek](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)için nasıl yapılır makalelerine bakın.
