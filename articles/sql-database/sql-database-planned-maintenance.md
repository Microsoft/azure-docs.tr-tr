---
title: Azure bakım olaylarını planlama
description: Planlı bakım olaylarını Azure SQL veritabanınıza hazırlama hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821311"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Azure SQL veritabanı 'nda Azure bakım olaylarını planlama

Azure SQL veritabanınızda planlı bakım olaylarına hazırlanma hakkında bilgi edinin.

## <a name="what-is-a-planned-maintenance-event"></a>Planlı bakım olayı nedir?

Her veritabanı için Azure SQL DB, bir çoğaltmanın birincil olduğu veritabanı çoğaltmalarının bir çekirdeğini saklar. Her zaman bir birincil çoğaltmanın çevrimiçi bakım olması gerekir ve en az bir ikincil çoğaltma sağlıklı olmalıdır. Planlı bakım sırasında, veritabanı çekirdeğinin üyeleri tek seferde çevrimdışı olacak ve istemci kapalı kalma süresi olmamasını sağlamak için en az bir tane yanıt veren birincil çoğaltma ve çevrimiçi bir ikincil çoğaltma olduğundan emin olur. Birincil çoğaltmanın çevrimdışı hale getirilmesi gerektiğinde, bir ikincil çoğaltmanın yeni birincil hale gelmesi için bir yeniden yapılandırma/yük devretme işlemi meydana gelir.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Planlı bir bakım olayı sırasında beklenmeniz gerekenler

Yeniden yapılandırma/yük devretme işlemleri genellikle 30 saniye içinde tamamlanır – ortalama 8 saniyedir. Zaten bağlıysa, uygulamanızın veritabanının yeni birincil çoğaltmasını sağlıklı bir şekilde kopyalaması gerekir. Yeni birincil çoğaltma çevrimiçi olmadan önce veritabanı yeniden yapılandırılması sırasında yeni bir bağlantı denendiğinde, 40613 hatasını alırsınız (veritabanı kullanılamıyor): "' {ServerName} ' sunucusundaki" veritabanı ' {DatabaseName} ' Şu anda kullanılamıyor. Lütfen bağlantıyı daha sonra yeniden deneyin. ". Veritabanınızda çalışan uzun bir sorgu varsa, bu sorgu yeniden yapılandırma sırasında kesintiye uğratılacaktır ve yeniden başlatılması gerekir.

## <a name="retry-logic"></a>Yeniden deneme mantığı

Bir bulut veritabanı hizmetine bağlanan tüm istemci üretim uygulamaları sağlam bir bağlantı [yeniden deneme mantığı](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)uygulamalıdır. Bu durum, bu durumların azaltılmasına yardımcı olur ve genellikle hataları son kullanıcıya saydam hale getirir.

## <a name="frequency"></a>Frequency

Ortalama olarak, 1,7 planlı bakım olayları her ay oluşur.

## <a name="resource-health"></a>Kaynak Durumu

SQL veritabanınız oturum açma hatalarıyla karşılaşıyorsa, geçerli durum için [Azure portal](https://portal.azure.com) [kaynak durumu](../service-health/resource-health-overview.md#get-started) penceresine bakın. Sistem durumu geçmişi bölümü her bir olayın kesinti süresini (kullanılabilir olduğunda) içerir.


## <a name="next-steps"></a>Sonraki adımlar

- SQL veritabanı için [kaynak durumu](sql-database-resource-health.md) hakkında daha fazla bilgi edinin
- Yeniden deneme mantığı hakkında daha fazla bilgi için bkz: [geçici hatalar Için yeniden deneme mantığı](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
