---
title: Şirket içi SQL Server Integration Services (SSIS) iş yüklerini SSIS Azure Data Factory (ADF) olarak geçirme
description: On-premises SSIS iş yüklerini ADF 'de SSIS 'e geçirin.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373615"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Şirket içi SSIS iş yüklerini ADF 'de SSIS 'ye geçirme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Genel Bakış

Veritabanı iş yüklerinizi, Azure SQL veritabanı veya Azure SQL yönetilen örneği gibi şirket içi SQL Server Azure veritabanı hizmetlerine geçirdiğinizde, birincil değer ile eklenen hizmetlerden biri olarak SQL Server Integration Services (SSIS) üzerindeki ETL iş yüklerinizin da geçirilmesi gerekir.

Azure Data Factory (ADF) içindeki Azure-SSIS Integration Runtime (IR), SSIS paketlerinin çalıştırılmasını destekler. Azure-SSIS IR sağlandıktan sonra, Azure 'da paketlerinizi dağıtmak ve çalıştırmak için SQL Server Veri Araçları (SSDT)/SQL Server Management Studio (SSMS) gibi tanıdık araçları ve dtınstall/dtutil/dtexec gibi komut satırı yardımcı programlarını kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure SSIS Asansör-ve-SHIFT 'e genel bakış](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Bu makalede, şirket içi SSIS 'den SSIS 'e yönelik ETL iş yüklerinizin geçiş süreci ADF 'de gösterilmektedir. Geçiş işlemi iki aşamadan oluşur: **değerlendirme** ve **geçiş**.

## <a name="assessment"></a>Değerlendirme

Tam bir geçiş planı oluşturmak için, eksiksiz bir değerlendirme, başarılı bir geçişin önlenmesi kaynak SSIS paketleriyle ilgili sorunları belirlemenize yardımcı olur.

Data Migration Yardımcısı (DMA), bu amaçla yerel olarak yüklenebilen ve çalıştırılabilen, ücretsiz indirilebilir bir araçtır. SSIS paketlerini toplu olarak değerlendirmek ve aşağıdaki kategorilerde sunulan uyumluluk sorunlarını belirlemek için, Type **Integration Services** türünde DMA değerlendirmesi projesi oluşturulabilir:

- Geçiş engelleyiciler: geçiş kaynak paketlerinin Azure-SSIS IR çalıştırılmasını engelleyen uyumluluk sorunları. DMA, bu sorunları gidermenize yardımcı olacak kılavuzluk sağlar.

- Bilgilendirici sorunlar: kaynak paketlerinde kullanılan kısmen desteklenen veya kullanımdan kaldırılan özellikler. DMA, Azure 'da sunulan kapsamlı öneriler, diğer yaklaşımların bir kümesini sağlar ve çözüm adımlarını azaltıcı adımları ortadan kaldırmanın yanı sıra.

### <a name="four-storage-types-for-ssis-packages"></a>SSIS paketleri için dört depolama türü

- SSIS Kataloğu (SSSıSDB). SQL Server 2012 ile birlikte sunulmuştur ve SSIS projeleri/paketleri ile çalışmak için kullanılan bir dizi saklı yordam, görünüm ve tablo değerli işlev içerir.
- Dosya sistemi.
- SQL Server sistem veritabanı (MSDB).
- SSIS paket deposu. İki alt tür üzerinde bir paket yönetim katmanı:
  - MSDB, SSIS paketlerini depolamak için kullanılan SQL Server bir sistem veritabanıdır.
  - SSIS paketlerini depolamak için kullanılan SQL Server yükleme yolundaki belirli bir klasör olan yönetilen dosya sistemi.

DMA, **DMA sürümü v 5.0**'Dan beri **dosya sistemi**, **paket deposu** ve **SSIS kataloğunda** depolanan paketlerin toplu değerlendirmesini desteklemektedir.

[DMA](/sql/dma/dma-overview)'yı alın ve [paket değerlendirmenizi gerçekleştirin](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Geçiş

Kaynak SSIS paketlerinin [depolama türlerine](#four-storage-types-for-ssis-packages) ve veritabanı iş yüklerinin geçiş hedefine bağlı olarak, SSIS  **paketlerini** geçirme adımları ve SSIS paket yürütmelerinin zamanlaması olan **SQL Server Agent işleri** farklılık gösterebilir. İki senaryo vardır:

- [Veritabanı iş yükü hedefi olarak **Azure SQL yönetilen örneği**](#azure-sql-managed-instance-as-database-workload-destination)
- [Veritabanı iş yükü hedefi olarak **Azure SQL veritabanı**](#azure-sql-database-as-database-workload-destination)

Ayrıca, geçiş hedefine toplu paket yeniden dağıtımı yapmak için [SSIS DevOps araçlarını](/sql/integration-services/devops/ssis-devops-overview)kullanmanın pratik bir yoludur.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>Veritabanı iş yükü hedefi olarak **Azure SQL yönetilen örneği**

| **Paket depolama türü** |SSIS paketlerini toplu geçirme|SSIS işlerinin toplu olarak geçirilmesi|
|-|-|-|
|SSıSDB|[**SSISDB** 'yi geçir](scenario-ssis-migration-ssisdb-mi.md)|<li>[SSIS işlerini Azure SQL yönetilen örnek aracısına geçirme](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Dosya Sistemi|Onları dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın veya dosya sistemlerinde VNet/şirket içinde barındırılan IR aracılığıyla erişmesini sağlayın. Daha fazla bilgi için bkz. [dtutil Utility](/sql/integration-services/dtutil-utility).|<li>[SSIS işlerini Azure SQL yönetilen örnek aracısına geçirme](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> [SSMS 'de SSIS Iş Geçiş Sihirbazı](how-to-migrate-ssis-job-ssms.md) ile geçiş <li>Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Dosyaları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın. Daha fazla bilgi için bkz. [SSIS paketlerini dışarı aktarma](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paket deposu|Onları SSMS/dtutil aracılığıyla paket deposuna aktarın veya dtınstall/dtutil/Manual Copy aracılığıyla paket deposuna yeniden dağıtın. Daha fazla bilgi için bkz. [Azure-SSIS Integration Runtime paket deposu ile paketleri yönetme](azure-ssis-integration-runtime-package-store.md).|<li>[SSIS işlerini Azure SQL yönetilen örnek aracısına geçirme](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>Veritabanı iş yükü hedefi olarak **Azure SQL veritabanı**

| **Paket depolama türü** |SSIS paketlerini toplu geçirme|İşleri toplu geçirme|
|-|-|-|
|SSıSDB|SSDT/SSMS aracılığıyla Azure-SSıSDB 'ye yeniden dağıtın. Daha fazla bilgi için bkz. [Azure 'DA SSIS paketlerini dağıtma](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Dosya Sistemi|Onları dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın veya dosya sistemlerinde VNet/şirket içinde barındırılan IR aracılığıyla erişmesini sağlayın. Daha fazla bilgi için bkz. [dtutil Utility](/sql/integration-services/dtutil-utility).|<li> [SSMS 'de SSIS Iş Geçiş Sihirbazı](how-to-migrate-ssis-job-ssms.md) ile geçiş <li> Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Dosyaları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın. Daha fazla bilgi için bkz. [SSIS paketlerini dışarı aktarma](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paket deposu|Onları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın veya dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın ya da bunları VNet/şirket içinde barındırılan IR aracılığıyla erişmek için dosya sistemlerinde saklayın. Daha fazla bilgi için bkz. dtutil Utility. Daha fazla bilgi için bkz. [dtutil Utility](/sql/integration-services/dtutil-utility).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure Data Factory](./introduction.md)
- [Veritabanı Geçiş Yardımcısı](/sql/dma/dma-overview)
- [SSIS iş yüklerini buluta yükselt ve Kaydır](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps Araçları](/sql/integration-services/devops/ssis-devops-overview)
- [SSIS paketlerini Azure SQL Yönetilen Örnek’e geçirme](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Paketleri Azure SQL veritabanı 'na yeniden dağıtın](../dms/how-to-migrate-ssis-packages.md)

- [Azure-SSIS Integration Runtime 'ten şirket içi veri erişimi](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Azure-SSIS Integration Runtime için kurulumu özelleştirme](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Azure'da SSIS paketlerinden Windows kimlik doğrulaması ile veri depolarına ve dosya paylaşımlarına erişme](ssis-azure-connect-with-windows-auth.md)
- [Yönetilen kimlik kimlik doğrulamasını kullanma](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Azure Key Vault kullanma](store-credentials-in-key-vault.md)
- [Yüksek performans için Azure-SSIS Integration Runtime yapılandırma](configure-azure-ssis-integration-runtime-performance.md)
- [Azure-SSIS Integration Runtime'ı belirli bir zamanlamaya göre başlatma ve durdurma](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'a dağıtılan SSIS paketlerini doğrulama](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure 'da dağıtılan SSIS paketlerini çalıştırma](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [İzleyici Azure-SSIS Integration Runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure 'da SSIS paket yürütmelerini zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)