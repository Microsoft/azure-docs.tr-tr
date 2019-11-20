---
title: Sorgulamak için Machine Learning Services ile R kullanın
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu makalede bir Azure SQL veritabanına bağlanmak ve Transact-SQL deyimleriyle sorgulamak için Azure SQL veritabanı ile R betiği kullanma Machine Learning Services gösterilmektedir.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: a54b538247f81ea3bb0ea70a2af374158bd9e2ff
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826982"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Hızlı başlangıç: Azure SQL veritabanını sorgulamak için Machine Learning Services ile R kullanma (Önizleme)

Bu hızlı başlangıçta, bir Azure SQL veritabanına bağlanmak ve Transact-SQL deyimleriyle veri sorgulamak için Machine Learning Services ile [R](https://www.r-project.org/) 'nin nasıl kullanılacağı gösterilmektedir. Machine Learning Services, veritabanı içi R betiklerini yürütmek için kullanılan Azure SQL veritabanı 'nın bir özelliğidir. Daha fazla bilgi için bkz. [Azure SQL veritabanı Machine Learning Services R (Önizleme)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakilere sahip olduğunuzdan emin olun:

- Bir Azure SQL veritabanı. Azure SQL veritabanı 'nda bir veritabanı oluşturmak ve yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Tek veritabanı |
  |:--- |:--- |
  | Oluşturma| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Yapılandırma | [Sunucu düzeyi IP güvenlik duvarı kuralı](sql-database-server-level-firewall-rule.md) |
  | Veri yükleme | Hızlı başlangıç başına yüklenen Adventure Works |
  |||

  > [!NOTE]
  > R ile Azure SQL veritabanı Machine Learning Services önizlemesi sırasında, yönetilen örnek dağıtım seçeneği desteklenmez.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (R ile) etkin. Microsoft, genel önizleme sırasında, mevcut veya yeni veritabanınız için sizi kullanıma sunulacaktır ve makine öğrenimini etkinleştirecektir. [Önizlemeye kaydolma](sql-database-machine-learning-services-overview.md#signup)adımlarını izleyin.

- En son [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). R komut dosyalarını diğer veritabanı yönetimini veya sorgu araçlarını kullanarak çalıştırabilirsiniz, ancak bu hızlı başlangıçta SSMS 'yi kullanacaksınız.

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="create-code-to-query-your-sql-database"></a>SQL veritabanınızı sorgulamak için kod oluşturma

1. **SQL Server Management Studio**’yu açın ve SQL veritabanınıza bağlanın.

   Bağlantı için yardıma ihtiyacınız varsa bkz. [hızlı başlangıç: Azure SQL veritabanına bağlanmak ve sorgu sorgulamak için SQL Server Management Studio kullanma](sql-database-connect-query-ssms.md).

1. Tüm R betiğini [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) saklı yordamına geçirin.

   Betik `@script` bağımsız değişkeniyle geçirilir. `@script` bağımsız değişkeninin içindeki her şey geçerli bir R kodu olmalıdır.
   
   >[!IMPORTANT]
   >Bu örnekteki kod, veritabanınızı oluştururken kaynak olarak seçebileceğiniz örnek AdventureWorksLT verilerini kullanır. Veritabanınızda farklı veriler varsa, SELECT sorgusunda kendi veritabanınızdaki tabloları kullanın. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Hata alırsanız Machine Learning Services (R ile) genel önizleme sürümü SQL veritabanınız için etkinleştirilmemiş olabilir. Yukarıdaki [önkoşulları](#prerequisites) inceleyin.

## <a name="run-the-code"></a>Kodu çalıştırma

1. [Sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) saklı yordamını yürütün.

1. **İletiler** penceresinde Ilk 20 kategori/ürün satırı döndürüldüğünden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [İlk Azure SQL veritabanınızı tasarlama](sql-database-design-first-database.md)
- [Azure SQL veritabanı Machine Learning Services (R ile)](sql-database-machine-learning-services-overview.md)
- [Azure SQL veritabanı 'nda basit R betikleri oluşturma ve çalıştırma Machine Learning Services (Önizleme)](sql-database-quickstart-r-create-script.md)
- [Machine Learning Services kullanarak Azure SQL veritabanı 'nda gelişmiş R işlevleri yazma (Önizleme)](sql-database-machine-learning-services-functions.md)
