---
title: Sürekli tümleştirme ve dağıtım
description: Azure Pipelines kullanarak sürekli tümleştirme ve dağıtım için yerleşik desteğe sahip SQL veri ambarı için kurumsal sınıf veritabanı DevOps deneyimi.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fd03072f4e69fac43874e822ebb06063436ef72c
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646143"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Azure SQL veri ambarı için sürekli tümleştirme ve dağıtım

Bu basit öğreticide, SQL Server veri araçları (SSDT) veritabanı projenizi Azure DevOps ile tümleştirme ve sürekli tümleştirme ve dağıtım ayarlamak için Azure Pipelines yararlanma özetlenmektedir. Bu öğretici, SQL veri ambarı ile sürekli tümleştirme ve dağıtım işlem hattınızı oluşturmanın ikinci adımıdır. 

## <a name="before-you-begin"></a>Başlamadan önce

- [Kaynak denetimi tümleştirme öğreticisine](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration) gidin

- SQL veri ambarı (Önizleme) için SSDT önizleme bitleri (16,3 Preview 2 ve üzeri) yüklü olan [Şirket içinde barındırılan bir aracı](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops#install) oluşturun

- Azure DevOps 'ı ayarlama ve bu sunucuya bağlanma

  > [!NOTE]
  > SSDT Şu anda, şirket içinde barındırılan bir aracının kullanmanız gereken önizleme aşamasındadır. Microsoft tarafından barındırılan aracılar önümüzdeki birkaç ay içinde güncelleştirilecektir.

## <a name="continuous-integration-with-visual-studio-build"></a>Visual Studio derlemesi ile sürekli tümleştirme

1. Azure Pipelines gidin ve yeni bir derleme işlem hattı oluşturun

      ![Yeni işlem hattı](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Yeni İşlem Hattı")

2. Kaynak kodu deponuzu (Azure Repos git) seçin ve .NET Masaüstü uygulama şablonunu seçin

      ![İşlem hattı kurulumu](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "İşlem hattı kurulumu") 

3. YAML Dosyanızı, aracınızın uygun havuzunu kullanacak şekilde düzenleyin. YAML dosyanız şuna benzer şekilde görünmelidir:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Bu noktada, kaynak denetimi depo ana dalınızdaki tüm iadelerinizin, veritabanı projenizin başarılı bir Visual Studio derlemesini otomatik olarak tetiklemesi gereken basit bir ortamınız vardır. Yerel veritabanı projenizde bir değişiklik yaparak ve bu değişikliği ana dalınıza iade ederek Otomasyon 'un uçtan uca çalıştığını doğrulayın.


## <a name="continuous-deployment-with-the-azure-sql-database-deployment-task"></a>Azure SQL veritabanı dağıtım göreviyle sürekli dağıtım

1. [Azure SQL veritabanı dağıtım görevini](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) kullanarak yeni bir görev ekleyin ve hedef veri ambarınıza bağlanmak için gerekli alanları girin. Bu görev çalıştırıldığında, önceki derleme işleminden oluşturulan DACPAC, hedef veri ambarına dağıtılır.

      ![Dağıtım görevi](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Dağıtım görevi")

2. Kendi kendine barındırılan bir aracı kullanırken, ortam değişkeninizi SQL veri ambarı için doğru SqlPackage. exe ' yi kullanacak şekilde ayarladığınızdan emin olun. Yol şuna benzemelidir:

      ![Ortam değişkeni](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Ortam değişkeni")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   İşlem hattınızı çalıştırın ve doğrulayın. Yerel olarak değişiklik yapabilir ve bir otomatik derleme ve dağıtım oluşturması gereken kaynak denetimi değişiklikler iade edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL veri ambarı mimarisini](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture) keşfet
- Hızlı [bir şekılde SQL veri ambarı oluşturun][create a SQL Data Warehouse]
- [Örnek verileri yükleyin][load sample data].
- [Videoları](/azure/sql-data-warehouse/sql-data-warehouse-videos) keşfet



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
