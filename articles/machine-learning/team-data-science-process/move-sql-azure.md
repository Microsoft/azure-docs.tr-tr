---
title: Azure SQL veritabanı 'na veri taşıma-ekip veri bilimi Işlemi
description: Verileri düz dosyalardan (CSV veya TSV biçimlerinden) veya SQL Server depolanan verilerden Azure SQL veritabanı 'na taşıyın.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309525"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Azure Machine Learning için Azure SQL Veritabanına veri taşıma

Bu makalede, verileri düz dosyalardan (CSV veya TSV biçimlerinden) veya SQL Server ' de depolanan verilerden Azure SQL veritabanı 'na taşıma seçenekleri özetlenmektedir. Verileri buluta taşımaya yönelik bu görevler, ekip veri bilimi sürecinin bir parçasıdır.

Machine Learning için SQL Server verileri taşıma seçeneklerini özetleyen bir konu için bkz. [Azure sanal makinesinde SQL Server verileri taşıma](move-sql-server-virtual-machine.md).

Aşağıdaki tabloda, verileri bir Azure SQL veritabanına taşıma seçenekleri özetlenmektedir.

| <b>KAYNAKTAKI</b> | <b>Hedef: Azure SQL veritabanı</b> |
| --- | --- |
| <b>Düz dosya (CSV veya TSV biçimli)</b> |[SQL sorgusunu toplu ekleme](#bulk-insert-sql-query) |
| <b>Şirket içi SQL Server</b> |1.[düz dosyaya aktar](#export-flat-file)<br> 2. [SQL veritabanı geçiş Sihirbazı](#insert-tables-bcp)<br> 3. [veritabanı yedekleme ve geri yükleme](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Önkoşullar
Burada özetlenen yordamlarda şunları yapmanız gerekir:

* Bir **Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* Bir **Azure depolama hesabı**. Bu öğreticide verileri depolamak için bir Azure depolama hesabı kullanın. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md) makalesine bakın. Depolama hesabını oluşturduktan sonra, depolamaya erişmek için kullanılan hesap anahtarını edinmeniz gerekir. Bkz. [depolama hesabı erişim anahtarlarını yönetme](../../storage/common/storage-account-keys-manage.md).
* **Azure SQL veritabanına** erişim. Azure SQL veritabanı ayarlamanız gerekirse [Microsoft Azure SQL veritabanı kullanmaya başlamak](../../azure-sql/database/single-database-create-quickstart.md) IÇIN Azure SQL veritabanı 'nın yeni bir örneğini sağlama hakkında bilgi sağlanır.
* **Azure PowerShell** yerel olarak yüklendi ve yapılandırıldı. Yönergeler için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/).

**Veri**: geçiş Işlemi, [NYC TAXI veri kümesi](https://chriswhong.com/open-data/foil_nyc_taxi/)kullanılarak gösterilmiştir. NYC TAXI veri kümesi, seyahat verileri ve FAIRS hakkında bilgiler içerir ve Azure Blob depolama alanında kullanılabilir: [NYC TAXI verileri](https://www.andresmh.com/nyctaxitrips/). Bu dosyaların bir örneği ve açıklaması [NYC TAXI gezme veri kümesi açıklamasında](sql-walkthrough.md#dataset)verilmiştir.

Burada açıklanan yordamları kendi verilerinize uyarlayabilirsiniz ya da NYC TAXI veri kümesini kullanarak açıklanan adımları izleyebilirsiniz. NYC TAXI veri kümesini SQL Server veritabanınıza yüklemek için, [verileri SQL Server veritabanına toplu Içeri aktarma](sql-walkthrough.md#dbload)bölümünde özetlenen yordamı izleyin.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Verileri düz bir dosya kaynağından Azure SQL veritabanına taşıma
Düz dosyalardaki (CSV veya TSV biçimli) veriler, toplu ekleme SQL sorgusu kullanarak bir Azure SQL veritabanına taşınabilir.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> SQL sorgusunu toplu ekleme
Toplu ekleme SQL sorgusunu kullanan yordamın adımları, verileri bir Azure VM üzerindeki SQL Server bir düz dosya kaynağından taşıma yönlerine benzer. Ayrıntılar için bkz. [toplu ekleme SQL sorgusu](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> SQL Server verileri Azure SQL veritabanına taşıma
Kaynak veriler SQL Server depolanıyorsa, verileri bir Azure SQL veritabanına taşımaya yönelik çeşitli olanaklar vardır:

1. [Düz dosyaya aktar](#export-flat-file)
2. [SQL Veritabanı Geçiş Sihirbazı](#insert-tables-bcp)
3. [Veritabanı yedekleme ve geri yükleme](#db-migration)
4. [Azure Data Factory](#adf)

İlk üç adım, verileri aynı yordamları kapsayan [bir Azure sanal makinesinde SQL Server taşıma](move-sql-server-virtual-machine.md) bölümündeki bölümlerle benzerdir. Bu konudaki uygun bölümlerin bağlantıları aşağıdaki yönergelerde verilmiştir.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Düz dosyaya aktar
Düz bir dosyaya dışa aktarma adımları, [düz dosyaya dışa aktarma](move-sql-server-virtual-machine.md#export-flat-file)içinde kapsanan yönlere benzerdir.

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>SQL Veritabanı Geçiş Sihirbazı
SQL veritabanı geçiş Sihirbazı 'Nı kullanma adımları [SQL veritabanı geçiş Sihirbazı](move-sql-server-virtual-machine.md#sql-migration)'nda ele alınan yönlere benzerdir.

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Veritabanı yedekleme ve geri yükleme
Veritabanı yedeklemesini ve geri yüklemeyi kullanma adımları, [veritabanı yedekleme ve geri yükleme](move-sql-server-virtual-machine.md#sql-backup)bölümünde listelenen yönlere benzerdir.

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Bu konudaki verileri bir Azure SQL veritabanına taşıma hakkında bilgi edinin Azure Data Factory (ADF), [SQL Server verileri Azure Data Factory ile SQL Azure 'e taşıyın](move-sql-azure-adf.md). Bu konuda, Azure Blob depolama aracılığıyla bir SQL Server veritabanından Azure SQL veritabanına veri taşımak için ADF 'nin nasıl kullanılacağı gösterilmektedir.

Verilerin karma şirket içi ve bulut kaynaklarıyla sürekli geçirilmesi gerektiğinde ADF kullanmayı göz önünde bulundurun.  ADF Ayrıca verilerin Dönüştürmelere ihtiyacı olduğunda veya geçiş sırasında yeni iş mantığına ihtiyacı olduğunda de yardımcı olur. ADF, verilerin düzenli aralıklarla taşınmasını yöneten basit JSON betikleri kullanılarak işlerin zamanlamasını ve izlenmesini sağlar. ADF Ayrıca karmaşık işlemler için destek gibi başka yetenekler de içerir.