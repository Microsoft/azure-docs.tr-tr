---
title: Azure Otomasyonu ile veritabanlarını yönetme
description: Azure Otomasyonu hizmetinin Azure SQL veritabanlarını ölçekli ölçekte yönetmek için nasıl kullanılabileceği hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822815"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure Otomasyonu 'Nu kullanarak Azure SQL veritabanlarını yönetme

Bu kılavuz size Azure Otomasyonu hizmetini ve Azure SQL veritabanlarınızın yönetimini basitleştirmek için nasıl kullanılabileceğini tanıtacaktır.

## <a name="what-is-azure-automation"></a>Azure Otomasyonu Nedir?

[Azure Otomasyonu](https://azure.microsoft.com/services/automation/) , işlem otomasyonu aracılığıyla bulut yönetimini basitleştirecek bir Azure hizmetidir. Azure Otomasyonu, uzun süre çalışan, el ile, hata açısından açık ve sık tekrarlanan görevler kullanmak, kuruluşunuzun güvenilirliğini, verimliliğini ve değer süresini artırmak için otomatikleştirilebilir.

Azure Otomasyonu, yüksek güvenilirlik ve yüksek kullanılabilirliğe sahip bir iş akışı yürütme altyapısı sağlar ve kuruluşunuz büyüdükçe gereksinimlerinizi karşılayacak şekilde ölçeklendirilir. Azure Otomasyonu 'nda, süreçler el ile, üçüncü taraf sistemlere veya zamanlanan aralıklarda, görevlerin tam olarak gerektiğinde gerçekleşmesini sağlayabilirsiniz.

İşlem yükünü düşürün ve BT/DevOps personelini, bulut yönetim görevlerinizi Azure Otomasyonu tarafından otomatik olarak çalışacak şekilde taşıyarak iş değeri ekleyen işlere odaklanmak üzere boşaltın.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Azure Otomasyonu, Azure SQL veritabanlarını yönetme konusunda nasıl yardımcı olabilir?

Azure SQL veritabanı, Azure Otomasyonu 'nda [Azure PowerShell araçlarında](/powershell/azure/overview)bulunan [Azure SQL veritabanı PowerShell cmdlet 'leri](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) kullanılarak yönetilebilir. Azure Otomasyonu, bu Azure SQL veritabanı PowerShell cmdlet 'lerini kullanıma sunulmuştur. bu sayede, hizmet içinde tüm SQL DB yönetim görevlerinizi gerçekleştirebilirsiniz. Bu cmdlet 'leri Azure Otomasyonu 'nda diğer Azure hizmetleri için cmdlet 'lerle de eşleştirerek, Azure hizmetlerinde ve üçüncü taraf sistemlerde karmaşık görevleri otomatikleştirebilirsiniz.

Azure Otomasyonu Ayrıca, PowerShell kullanarak SQL komutları vererek doğrudan SQL sunucularıyla iletişim kurabilir.

[Azure Otomasyonu Runbook Galerisi](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) , Azure SQL veritabanlarının, diğer Azure hizmetlerinin ve üçüncü taraf sistemlerin yönetimini otomatikleştirmeye başlamak için çeşitli ürün ekibi ve topluluk runbook 'ları içerir. Galeri runbook 'ları şunları içerir:

- [SQL Server veritabanında SQL sorguları çalıştırma](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Bir Azure SQL veritabanını zamanlamaya göre dikey olarak ölçeklendirme (yukarı veya aşağı)](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Veritabanı en büyük boyutuna yaklaşırsa bir SQL tablosunu kesin](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Yüksek oranda parçalanmış olmaları durumunda tabloları bir Azure SQL veritabanında dizin oluşturma](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Sonraki adımlar

Azure Otomasyonu ile ilgili temel bilgileri ve Azure SQL veritabanlarını yönetmek için nasıl kullanılabileceğini öğrendiğinize göre, Azure Otomasyonu hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin.

- [Azure Otomasyonu genel bakış](../automation/automation-intro.md)
- [İlk runbook’um](../automation/automation-first-runbook-graphical.md)
- [Azure Otomasyonu: buluttaki SQL aracınız](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 