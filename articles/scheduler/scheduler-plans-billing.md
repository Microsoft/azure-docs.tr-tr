---
title: Planlar ve faturalandırma-Azure Scheduler
description: Azure Scheduler için planlar ve faturalandırma hakkında bilgi edinin
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 4b63367ab9686eee66bf3f00dddc2e2efe4cb941
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300861"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Azure Scheduler için planlar ve faturalandırma

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

## <a name="job-collection-plans"></a>İş koleksiyonu planları

Azure Scheduler 'da, bir iş koleksiyonu belirli sayıda iş içerir. İş koleksiyonu faturalandırılabilir varlıktır ve standart, P10 Premium ve P20 Premium planlarına gelir ve burada açıklanmıştır: 

| İş koleksiyonu planı | Koleksiyon başına en fazla iş | En fazla yinelenme | Abonelik başına en fazla iş koleksiyonu | Sınırlar | 
|:--- |:--- |:--- |:--- |:--- |
| **Standart** | koleksiyon başına 50 iş | Dakikada bir. İşlemler dakikada birden çok kez çalıştırılamaz. | Her Azure aboneliği 100 adede kadar standart iş koleksiyonuna sahip olabilir. | Zamanlayıcı tam özellik kümesine erişim | 
| **P10 Premium** | koleksiyon başına 50 iş | Dakikada bir. İşlemler dakikada birden çok kez çalıştırılamaz. | Her Azure aboneliğinin en fazla 10.000 P10 Premium iş koleksiyonu olabilir. Daha fazla koleksiyon için <a href="mailto:wapteams@microsoft.com">bizimle Iletişime geçin</a>. | Zamanlayıcı tam özellik kümesine erişim |
| **P20 Premium** | koleksiyon başına 1000 iş | Dakikada bir. İşlemler dakikada birden çok kez çalıştırılamaz. | Her Azure aboneliğinin en fazla 5.000 P20 Premium iş koleksiyonu olabilir. Daha fazla koleksiyon için <a href="mailto:wapteams@microsoft.com">bizimle Iletişime geçin</a>. | Zamanlayıcı tam özellik kümesine erişim |
|||||| 

## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma ayrıntıları için bkz. [Scheduler fiyatlandırması](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Yükseltme veya düşürme planları

Herhangi bir zamanda, standart, P10 Premium ve P20 Premium planlarında bir iş koleksiyonu planını yükseltebilir veya indirgeyeleyebilirsiniz.

## <a name="active-status-and-billing"></a>Etkin durum ve faturalandırma

Azure aboneliğinizin tamamı faturalandırma sorunları nedeniyle geçici olarak devre dışı duruma geçmediği takdirde iş koleksiyonları her zaman etkindir. Ve bir iş koleksiyonundaki tüm işleri tek bir işlem aracılığıyla devre dışı bırakabilseniz de, bu eylem iş koleksiyonunun faturalandırma durumunu değiştirmez, bu nedenle iş koleksiyonu *hala* faturalandırılır. Boş iş koleksiyonları etkin kabul edilir ve faturalandırılır.

Bir iş koleksiyonunun faturalandırılmamış olduğundan emin olmak için iş koleksiyonunu silmeniz gerekir.

## <a name="standard-billable-units"></a>Standart faturalanabilir birimler

Standart bir faturalanabilir birim en fazla 10 Standart iş koleksiyonuna sahip olabilir. Standart bir iş koleksiyonu koleksiyon başına en fazla 50 iş yaptığından, tek bir standart faturalandırma birimi, Azure aboneliğinizin en fazla 500 iş veya ayda yaklaşık 22 *milyon* iş yürütmeleri olmasını sağlar. Bu liste, çeşitli standart iş koleksiyonlarına göre faturalandırılırsınız.

* 1 ila 10 Standart iş koleksiyonunuz varsa, bir standart faturalandırma birimi için faturalandırılırsınız. 

* 11 ila 20 Standart iş koleksiyonunuz varsa, iki standart faturalandırma birimi için faturalandırılırsınız. 

* 21 ila 30 standart iş koleksiyonunuz varsa, üç standart faturalandırma birimi için faturalandırılırsınız ve bu şekilde devam edersiniz.

## <a name="p10-premium-billable-units"></a>P10 Premium faturalanabilir birimler

P10 Premium faturalanabilir birim en fazla 10.000 P10 Premium iş koleksiyonuna sahip olabilir. Bir P10 Premium iş koleksiyonunda koleksiyon başına en fazla 50 iş olabilir. bir P10 Premium faturalandırma birimi, Azure aboneliğinizin en fazla 500.000 iş veya ayda yaklaşık 22 *milyar* iş yürütmelerinin olmasını sağlar. 

P10 Premium iş koleksiyonları standart iş koleksiyonlarıyla aynı özellikleri sağlar, ancak birçok iş koleksiyonu gerektiren uygulamalar için bir fiyat kesmesi sunar ve daha fazla ölçeklenebilirlik sağlar. Bu liste, çeşitli P10 Premium iş koleksiyonlarına göre faturalandırılırsınız.

* 1 ila 10.000 P10 Premium iş koleksiyonunuz varsa, bir P10 Premium faturalandırma birimi için faturalandırılırsınız. 

* 10.001 ve 20.000 P10 Premium iş koleksiyonlarınız varsa, 2 P10 Premium faturalandırma birimi için faturalandırılırsınız ve bu şekilde devam edersiniz.

## <a name="p20-premium-billable-units"></a>P20 Premium faturalanabilir birimler

P20 Premium faturalanabilir birim en fazla 5.000 P20 Premium iş koleksiyonuna sahip olabilir. Bir P20 Premium iş koleksiyonunda iş koleksiyonu başına en fazla 1.000 iş olabilir. bir P20 Premium faturalandırma birimi, Azure aboneliğinizin en fazla 5.000.000 iş veya ayda neredeyse 220 *milyar* iş yürütmeleri olmasını sağlar.

P20 Premium iş koleksiyonları, P10 Premium iş koleksiyonlarıyla aynı özellikleri sağlar, ancak aynı zamanda koleksiyon başına daha fazla iş sayısını ve P10 Premium 'dan genel olarak toplam iş sayısını da destekler ve daha fazla ölçeklenebilirlik sağlar.

## <a name="plan-comparison"></a>Plan karşılaştırması

* 100 ' den fazla standart iş koleksiyonunuz (10 Standart faturalandırma birimi) varsa, tüm iş koleksiyonlarını bir Premium planda bulundurarak daha iyi bir anlaşma edinebilirsiniz.

* Bir standart iş koleksiyonunuz ve bir Premium iş koleksiyonunuz varsa, bir standart faturalandırma birimi *ve* bir Premium faturalandırma birimi için faturalandırılırsınız.

  Zamanlayıcı hizmeti, standart veya Premium olan etkin iş koleksiyonlarının sayısını temel alır.

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Scheduler nedir?](scheduler-intro.md)
* [Azure Scheduler kavramları, terminolojisi ve varlık hiyerarşisi](scheduler-concepts-terms.md)
* [Azure Scheduler sınırları, varsayılanları ve hata kodları](scheduler-limits-defaults-errors.md)