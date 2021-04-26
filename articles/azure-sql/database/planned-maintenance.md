---
title: Azure bakım olaylarını planlayın
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde planlı bakım olaylarına hazırlanma hakkında bilgi edinin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: sstein
ms.date: 3/23/2021
ms.openlocfilehash: 259a8581d16f4fd6958a0d9ec2631f667d362b19
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579473"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde Azure bakım olaylarını planlayın
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde veritabanınızda planlı bakım olaylarına hazırlanma hakkında bilgi edinin.

## <a name="what-is-a-planned-maintenance-event"></a>Planlı bakım olayı nedir?

Azure SQL veritabanı ve Azure SQL yönetilen örnek hizmetlerini güvenli, uyumlu, kararlı ve iyi durumda tutmak için güncelleştirmeler neredeyse sürekli hizmet bileşenleri üzerinden gerçekleştiriliyor. [Sık yama](https://aka.ms/azuresqlhotpatching)gibi modern ve sağlam hizmet mimarisi ve yenilikçi teknolojiler sayesinde güncelleştirmelerin büyük bölümü, hizmet kullanılabilirliği bakımından tamamen saydamdır ve etkili değildir. Hala, bazı güncelleştirme türleri kısa hizmet kesintileri oluşmasına neden olur ve özel bir işleme gerektirir. 

Her veritabanı için, Azure SQL veritabanı ve Azure SQL yönetilen örneği, bir çoğaltmanın birincil olduğu veritabanı çoğaltmalarının çekirdeğini korur. Her zaman, birincil bir çoğaltmanın çevrimiçi bakım olması ve en az bir ikincil çoğaltmanın sağlıklı olması gerekir. Planlı bakım sırasında, veritabanı çekirdeğinin üyeleri tek seferde çevrimdışı olacak ve istemci kapalı kalma süresi olmamasını sağlamak için en az bir tane yanıt veren birincil çoğaltma ve çevrimiçi bir ikincil çoğaltma olduğundan emin olur. Birincil çoğaltmanın çevrimdışı hale getirilmesi gerektiğinde, bir ikincil çoğaltmanın yeni birincil hale gelmesi için bir yeniden yapılandırma işlemi meydana gelir.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Planlı bir bakım olayı sırasında beklenmeniz gerekenler

Bakım olayı, bakım olayının başlangıcında birincil ve ikincil çoğaltmaların yarışmasına bağlı olarak, tek veya birden fazla yeniden yapılandırma üretebilir. Ortalama olarak, planlı bakım olayı başına 1,7 yeniden yapılandırma gerçekleştirilir. Yeniden yapılandırma genellikle 30 saniye içinde tamamlanır. Ortalama sekiz saniyedir. Zaten bağlantı varsa, uygulamanızın veritabanınızın yeni birincil çoğaltmasına yeniden bağlanması gerekir. Yeni birincil çoğaltma çevrimiçi olmadan önce veritabanı yeniden yapılandırılması sırasında yeni bir bağlantı denendiğinde, 40613 hatasını alırsınız (veritabanı kullanılamıyor): *"' {ServerName} ' sunucusundaki" veritabanı ' {DatabaseName} ' Şu anda kullanılamıyor. Lütfen bağlantıyı daha sonra yeniden deneyin. "* Veritabanınızda uzun süreli bir sorgu varsa, bu sorgu yeniden yapılandırma sırasında kesintiye uğratılacaktır ve yeniden başlatılması gerekir.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Planlı bakım olayının benzetimini yapma

İstemci uygulamanızın üretime dağıtmadan önce bakım olaylarına dayanıklı olmasını sağlamak, uygulama hatalarının riskini azaltmaya yardımcı olur ve son kullanıcılarınız için uygulama kullanılabilirliğine katkıda bulunur. [Uygulama hatası dayanıklılığı](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla#testing-application-fault-resiliency) POWERSHELL, clı veya REST API ile test ederek, planlı bakım olayları sırasında istemci uygulamanızın davranışını test edebilirsiniz. Ayrıca bkz. yönetilen örnek için [el ile yük devretme başlatma](https://aka.ms/mifailover-techblog) . Birincil çoğaltmayı çevrimdışına getiren bakım olayı olarak aynı davranışı üretir.

## <a name="retry-logic"></a>Yeniden deneme mantığı

Bir bulut veritabanı hizmetine bağlanan tüm istemci üretim uygulamaları sağlam bir bağlantı [yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)uygulamalıdır. Bu, yeniden yapılandırmaların son kullanıcılara saydam hale getirme veya en azından negatif etkileri en aza indirmenize yardımcı olur.

## <a name="resource-health"></a>Kaynak durumu

Veritabanınız oturum açma hatalarıyla karşılaşıyorsa, geçerli durum için [Azure portal](https://portal.azure.com) [kaynak durumu](../../service-health/resource-health-overview.md#get-started) penceresine bakın. Sistem durumu geçmişi bölümü her bir olayın kesinti süresini (kullanılabilir olduğunda) içerir.

## <a name="maintenance-window-feature"></a>Bakım penceresi özelliği

Bakım penceresi özelliği, uygun Azure SQL veritabanları ve SQL yönetilen örnekleri için öngörülebilir bakım penceresi zamanlamalarının yapılandırılmasını sağlar. Daha fazla bilgi için [bakım penceresine](maintenance-window.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı ve Azure SQL yönetilen örneği için [kaynak durumu](resource-health-to-troubleshoot-connectivity.md) hakkında daha fazla bilgi edinin.
- Yeniden deneme mantığı hakkında daha fazla bilgi için bkz. [geçici hatalar Için yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Bakım penceresi özelliğiyle bakım](maintenance-window.md) penceresi zamanlamalarını yapılandırın.
