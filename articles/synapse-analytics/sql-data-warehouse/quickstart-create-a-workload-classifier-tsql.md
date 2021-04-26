---
title: 'Hızlı başlangıç: iş yükü Sınıflandırıcısı oluşturma-T-SQL'
description: T-SQL ' y i kullanarak yüksek öneme sahip bir iş yükü Sınıflandırıcısı oluşturun.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677377"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Hızlı başlangıç: T-SQL kullanarak iş yükü Sınıflandırıcısı oluşturma

Bu hızlı başlangıçta, kuruluşunuzun CEO 'SU için yüksek önem derecesine sahip bir iş yükü sınıflandırıcısını hızlı bir şekilde oluşturacaksınız. Bu iş yükü Sınıflandırıcısı, CEO sorgularının, sıradaki daha düşük öneme sahip diğer sorgulara göre öncelikli olması için izin verir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!NOTE]
> Azure SYNAPSE Analytics 'te adanmış bir SQL havuzu örneği oluşturmak, yeni bir faturalanabilir hizmetle sonuçlanabilir.  Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç, Azure SYNAPSE Analytics 'te ayrılmış bir SQL havuzu sağladığınızı ve DENETIM VERITABANı izinlerine sahip olduğunuzu varsayar. Bir tane oluşturmanız gerekiyorsa, **Mysampledatawarehouse** adlı özel bir SQL havuzu oluşturmak için [Oluştur ve Bağlan-Portal](create-data-warehouse-portal.md) ' ı kullanın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-login-for-theceo"></a>TheCEO için oturum açma oluştur

`master`' TheCEO ' IÇIN [oturum açma oluştur](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kullanarak veritabanında SQL Server kimlik doğrulaması oturumu oluşturun.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Kullanıcı oluştur

"TheCEO" adlı Kullanıcı, mySampleDataWarehouse içinde [Oluştur](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>İş yükü Sınıflandırıcısı oluşturma

Yüksek öneme sahip "TheCEO" için bir [iş yükü Sınıflandırıcısı](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oluşturun.

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

Veri ambarı birimleri ve adanmış SQL havuzunuzdaki depolanan veriler için ücretlendiriliyorsunuz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır.

- Verileri depolama alanında tutmak istiyorsanız, adanmış SQL havuzunu kullanmadığınız zaman, işlem duraklatabilirsiniz. İşlem duraklatıldığında yalnızca veri depolama alanı için ücret ödersiniz. Verilerle çalışmaya hazırsanız, işlem işlemini sürdürmeniz gerekir.
- Gelecekteki ücretleri kaldırmak istiyorsanız adanmış SQL havuzunu silebilirsiniz.

Kaynakları temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın, adanmış SQL havuzunuzu seçin.

    ![Kaynakları temizleme](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlem duraklatmak için **Duraklat** düğmesini seçin. Adanmış SQL havuzu duraklatıldığında, bir **Başlat** düğmesi görürsünüz.  İşlem işlemini sürdürmesini sağlamak için **Başlat**' ı seçin.

3. İşlem veya depolama için ücretlendirilmemek üzere adanmış SQL havuzunu kaldırmak için **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Artık bir iş yükü Sınıflandırıcısı oluşturdunuz. Nasıl çalıştığını görmek için birkaç sorguyu TheCEO olarak çalıştırın. Sorguları ve atanan önemi görüntülemek için [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) bakın.
- Adanmış SQL havuzu iş yükü yönetimi hakkında daha fazla bilgi için bkz. [Iş yükü önemi](sql-data-warehouse-workload-importance.md) ve [iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md).
- [Iş yükü önemini yapılandırmak](sql-data-warehouse-how-to-configure-workload-importance.md) ve [Iş yükü yönetimini yönetmek ve izlemek](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)için nasıl yapılır makalelerine bakın.
