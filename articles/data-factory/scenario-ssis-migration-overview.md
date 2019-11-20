---
title: Şirket içi SSIS iş yüklerini SSIS 'ye geçirme Azure Data Factory
description: On-premises SSIS iş yüklerini ADF 'de SSIS 'e geçirin.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 99b7de8c0e24fdbd12742b26394e61c956638525
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038269"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Şirket içi SSIS iş yüklerini ADF 'de SSIS 'ye geçirme

## <a name="overview"></a>Genel Bakış

Veritabanı iş SQL Server yüklerinizi, Azure SQL veritabanı veya Azure SQL veritabanı yönetilen örneği olan Azure veritabanı hizmetlerine geçirdiğinizde, örneğin, birincil değerden biri olarak SQL Server Integration Services (SSIS) üzerindeki ETL iş yükleriniz hizmetlerin de geçirilmesi gerekir.

Azure Data Factory (ADF) içindeki Azure-SSIS Integration Runtime (IR), SSIS paketlerinin çalıştırılmasını destekler. Azure-SSIS IR sağlandıktan sonra, Azure 'da paketlerinizi dağıtmak ve çalıştırmak için SQL Server Veri Araçları (SSDT)/SQL Server Management Studio (SSMS) gibi tanıdık araçları ve dtınstall/dtutil/dtexec gibi komut satırı yardımcı programlarını kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure SSIS Asansör-ve-SHIFT 'e genel bakış](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Bu makalede, şirket içi SSIS 'den SSIS 'e yönelik ETL iş yüklerinizin geçiş süreci ADF 'de gösterilmektedir. Geçiş işlemi iki aşamadan oluşur: **değerlendirme** ve **geçiş**.

## <a name="assessment"></a>Değerlendirme

Tam bir geçiş planı oluşturmak için, eksiksiz bir değerlendirme, başarılı bir geçişin önlenmesi kaynak SSIS paketleriyle ilgili sorunları belirlemenize yardımcı olur.

Data Migration Yardımcısı (DMA), bu amaçla yerel olarak yüklenebilen ve çalıştırılabilen, ücretsiz indirilebilir bir araçtır. SSIS paketlerini toplu olarak değerlendirmek ve aşağıdaki kategorilerde sunulan uyumluluk sorunlarını belirlemek için, Type **Integration Services** türünde DMA değerlendirmesi projesi oluşturulabilir:

- Geçiş engelleyiciler: Bu, geçiş kaynak paketlerinin Azure-SSIS IR çalıştırılmasını engelleyen uyumluluk sorunlarıdır. DMA, bu sorunları gidermenize yardımcı olacak kılavuzluk sağlar.

- Bilgilendirici sorunlar: Bunlar kısmen destekleniyor veya kaynak paketlerinde kullanılan kullanım dışı özelliklerdir. DMA, Azure 'da sunulan kapsamlı öneriler, diğer yaklaşımların bir kümesini sağlar ve çözüm adımlarını azaltıcı adımları ortadan kaldırmanın yanı sıra.

### <a name="four-storage-types-for-ssis-packages"></a>SSIS paketleri için dört depolama türü

- SSIS Kataloğu (SSSıSDB). Bu, SQL Server 2012 ile birlikte sunulmuştur ve SSIS projeleri/paketleri ile çalışmak için kullanılan bir dizi saklı yordam, görünüm ve tablo değerli işlev içerir.
- Dosya sistemi.
- SQL Server sistem veritabanı (MSDB).
- SSIS paket deposu. Bu, iki alt tür üzerinde bulunan bir paket yönetimi katmanıdır:
  - MSDB, SSIS paketlerini depolamak için kullanılan SQL Server bir sistem veritabanıdır.
  - SSIS paketlerini depolamak için kullanılan SQL Server yükleme yolundaki belirli bir klasör olan yönetilen dosya sistemi.

DMA, **DMA sürümü v 5.0**'Dan beri **dosya sistemi**, **paket deposu**ve **SSIS kataloğunda** depolanan paketlerin toplu değerlendirmesini desteklemektedir.

[DMA](https://docs.microsoft.com/sql/dma/dma-overview)'yı alın ve [paket değerlendirmenizi gerçekleştirin](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Geçiş

Kaynak SSIS paketlerinin [depolama türlerine](#four-storage-types-for-ssis-packages) ve veritabanı iş yüklerinin geçiş hedefine bağlı olarak, SSIS **paketlerini** geçirme adımları ve SSIS paket yürütmelerinin zamanlaması olan **SQL Server Agent işleri** farklılık gösterebilir. İki senaryo vardır:

- [Veritabanı iş yükü hedefi olarak **Azure SQL veritabanı yönetilen örneği**](#azure-sql-database-managed-instance-as-database-workload-destination)
- [Veritabanı iş yükü hedefi olarak **Azure SQL veritabanı**](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>Veritabanı iş yükü hedefi olarak **Azure SQL veritabanı yönetilen örneği**

| **Paket depolama türü** |SSIS paketlerini toplu geçirme|SSIS işlerinin toplu olarak geçirilmesi|
|-|-|-|
|SSıSDB|[**SSISDB** 'yi geçir](scenario-ssis-migration-ssisdb-mi.md)|[SSIS işlerini Azure SQL veritabanı yönetilen örnek aracısına geçirme](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Dosya Sistemi|Onları dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın veya dosya sistemlerinde VNet/şirket içinde barındırılan IR aracılığıyla erişmesini sağlayın. Daha fazla bilgi için bkz. [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Dosyaları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın. Daha fazla bilgi için bkz. [SSIS paketlerini dışarı aktarma](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paket deposu|Onları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın veya dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın ya da bunları VNet/şirket içinde barındırılan IR aracılığıyla erişmek için dosya sistemlerinde saklayın. Daha fazla bilgi için bkz. dtutil Utility. Daha fazla bilgi için bkz. [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>Veritabanı iş yükü hedefi olarak **Azure SQL veritabanı**

| **Paket depolama türü** |SSIS paketlerini toplu geçirme|İşleri toplu geçirme|
|-|-|-|
|SSıSDB|SSDT/SSMS aracılığıyla Azure-SSıSDB 'ye yeniden dağıtın. Daha fazla bilgi için bkz. [Azure 'DA SSIS paketlerini dağıtma](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Dosya Sistemi|Onları dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın veya dosya sistemlerinde VNet/şirket içinde barındırılan IR aracılığıyla erişmesini sağlayın. Daha fazla bilgi için bkz. [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Dosyaları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın. Daha fazla bilgi için bkz. [SSIS paketlerini dışarı aktarma](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paket deposu|Onları SSMS/dtutil aracılığıyla dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dışarı aktarın veya dtınstall/dtutil/Manual Copy aracılığıyla dosya paylaşımlarına/Azure dosyalarına yeniden dağıtın ya da bunları VNet/şirket içinde barındırılan IR aracılığıyla erişmek için dosya sistemlerinde saklayın. Daha fazla bilgi için bkz. dtutil Utility. Daha fazla bilgi için bkz. [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Betikler/SSMS/ADF portalı aracılığıyla ADF işlem hattına/etkinliklere/tetikleyicilere Dönüştür. Daha fazla bilgi için bkz. [SSMS zamanlama özelliği](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Veritabanı Geçiş Yardımcısı](https://docs.microsoft.com/sql/dma/dma-overview)
- [SSIS iş yüklerini buluta yükselt ve Kaydır](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS paketlerini Azure SQL veritabanı yönetilen örneği 'ne geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Paketleri Azure SQL veritabanı 'na yeniden dağıtın](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'a dağıtılan SSIS paketlerini doğrulama](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure 'da dağıtılan SSIS paketlerini çalıştırma](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime'ı izleme](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Azure 'da SSIS paket yürütmelerini zamanlama](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
