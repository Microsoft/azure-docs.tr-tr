---
title: Contoso geçiş Serisi | Microsoft Docs
description: Contoso tarafından şirket içi veri merkezini Azure 'a geçirmek için kullanılan geçiş stratejisi ve senaryolarına genel bir bakış sağlar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: raynew
ms.openlocfilehash: d20c0be47f44cdce8ea895007494565d37f5923f
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179148"
---
# <a name="contoso-migration-series"></a>Contoso geçiş serisi


Kurgusal kuruluşun, şirket içi altyapıyı [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) buluta nasıl geçirdiğini gösteren bir dizi makalemiz vardır. 

Seriler, altyapının geçişini ayarlamayı ve farklı geçiş türlerini çalıştırmayı gösteren bilgi ve senaryolar içerir. Senaryolar, ilerleyen karmaşıklıklarla artar. Makaleler, contoso şirketinin geçiş işini nasıl tamamladığını gösterir, ancak genel okuma ve belirli yönergelerin işaretçisi içinde sağlanır.

## <a name="migration-articles"></a>Geçiş makaleleri

Serideki makaleler aşağıdaki tabloda özetlenmiştir.  

- Her bir geçiş senaryosu, geçiş stratejisini belirleyen biraz farklı iş hedefleri tarafından desteklenir.
- Her bir dağıtım senaryosu için, iş açısından teşvik eden noktalar ve hedefler hakkında bilgiler, önerilen bir mimari, geçişi gerçekleştirme adımları ve geçiş tamamlandıktan sonraki adımlar ve temizleme önerisi sağlarız.

**Makale** | **Ayrıntılar** 
--- | --- 
[Makale 1: Bakýþ](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Makale serisine, contoso 'nun geçiş stratejisine ve serideki kullanılan örnek uygulamalara genel bakış. 
[Makale 2: Azure altyapısını dağıtma](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso, şirket içi altyapısını ve Azure altyapısını geçiş için hazırlar. Serideki tüm geçiş makaleleri için aynı altyapı kullanılır. 
[Makale 3: Azure 'a geçiş için şirket içi kaynakları değerlendirme](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso, VMware üzerinde çalışan şirket içi SmartHotel360 uygulamasının bir değerlendirmesini çalıştırır. Contoso değerlendirir uygulama VM 'Leri, Azure geçişi hizmetini ve uygulama SQL Server veritabanını kullanarak Data Migration Yardımcısı.
[Makale 4: Azure VM ve SQL veritabanı yönetilen örneği üzerinde uygulamayı yeniden barındırma](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso, şirket içi SmartHotel360 uygulaması için Azure 'a bir yükseltme ve kaydırma geçişi çalıştırır. Contoso, [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)kullanarak uygulama ön uç VM 'sini geçirir. Contoso, [Azure veritabanı geçiş hizmeti](https://docs.microsoft.com/azure/dms/dms-overview)'ni kullanarak uygulama veritabanını BIR Azure SQL veritabanı yönetilen örneğine geçirir.
[Makale 5: Azure VM 'lerinde uygulamayı yeniden barındırma](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso, SmartHotel360 App VM 'lerini Site Recovery hizmetini kullanarak Azure VM 'lerine geçirir. 
[Makale 6: Azure VM 'lerinde ve bir SQL Server AlwaysOn kullanılabilirlik grubunda uygulamayı yeniden barındırma](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso, SmartHotel360 uygulamasını geçirir. Contoso, uygulama VM 'lerini geçirmek için Site Recovery kullanır. Uygulama veritabanını bir AlwaysOn kullanılabilirlik grubu tarafından korunan bir SQL Server kümesine geçirmek için veritabanı geçiş hizmetini kullanır. 
[Makale 7: Azure VM 'lerinde bir Linux uygulamasını yeniden barındırma](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso, Linux Osbilet uygulamasının Site Recovery hizmeti kullanılarak Azure VM 'lerine bir kaldırma ve kaydırma geçişi tamamlar.
[Makale 8: Azure VM 'lerinde Linux uygulamasını yeniden barındırma ve MySQL için Azure veritabanı](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso, Linux Osbilet uygulamasını Site Recovery kullanarak Azure VM 'lerine geçirir. Bu, MySQL Workbench kullanarak uygulama veritabanını MySQL için Azure Veritabanı’na geçirir. 
[Makale 9: Azure Web uygulaması ve Azure SQL veritabanı 'nda uygulamayı yeniden düzenleme](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso, SmartHotel360 uygulamasını bir Azure Web uygulamasına geçirir ve uygulama veritabanını veritabanı Geçiş Yardımcısı bir Azure SQL Server örneğine geçirir.     
[Makale 10: Bir Azure Web uygulamasındaki Linux uygulamasını ve MySQL için Azure veritabanı 'nı yeniden düzenleme](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso, Linux Osbilet uygulamasını Azure Traffic Manager kullanarak birden çok Azure bölgesinde Azure Web uygulamasına geçirir ve sürekli teslim için GitHub ile tümleşiktir. Contoso, uygulama veritabanını bir MySQL için Azure veritabanı örneğine geçirir. 
[Makale 11: Azure DevOps Services Team Foundation Server yeniden düzenleme](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso, şirket içi Team Foundation Server dağıtımını Azure 'daki Azure DevOps Services geçirir.
[Makale 12: Azure kapsayıcıları ve Azure SQL veritabanı 'nda bir uygulamayı yeniden mimarın](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso, SmartHotel uygulamasını Azure 'a geçirir. Daha sonra, uygulama Web katmanını Azure Service Fabric ve Azure SQL veritabanı 'nda çalışan bir Windows kapsayıcısı olarak yeniden Mimarlar.
[Makale 13: Azure 'da bir uygulamayı yeniden derleme](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso, Azure App Service, Azure Kubernetes hizmeti (AKS), Azure Işlevleri, Azure bilişsel hizmetler ve Azure Cosmos DB dahil olmak üzere bir dizi Azure özelliği ve hizmeti kullanarak SmartHotel uygulamasını yeniden oluşturur.
[Makale 14: Azure 'a geçişi ölçeklendirme](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Contoso, geçiş kombinasyonlarını tamamladıktan sonra Azure 'a tam geçişe göre ölçeklendirmeye hazırlar.

## <a name="next-steps"></a>Sonraki adımlar

- Bulut geçişi [hakkında bilgi edinin](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) .
- [Veritabanı geçiş kılavuzundaki](https://datamigration.microsoft.com/)diğer senaryolar (kaynak/hedef çiftleri) için geçiş stratejileri hakkında bilgi edinin.
