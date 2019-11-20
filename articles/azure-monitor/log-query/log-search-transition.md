---
title: Azure Log Analytics 'da verileri görüntüleme ve çözümleme | Microsoft Docs
description: Azure Izleyici günlük sorgu deneyimine Log Analytics günlük araması kullanıcıları için yardım.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: b738d532cb458506ca50a07dea11687040f99e02
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900339"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Log Analytics günlük aramadan Azure Izleyici günlüklerine geçiş
Log Analytics günlük araması kısa süre önce Azure Izleyici günlüklerini çözümlemek için yeni bir deneyimle değiştirilmiştir. Günlük arama sayfasına şu anda Azure portal **Log Analytics çalışma alanları** sayfasındaki **Günlükler (klasik)** menü öğesi aracılığıyla erişilebilir ancak 15 Şubat 2019 ' de kaldırılacaktır. Bu makalede, günlük aramadan geçiş yapmanıza yardımcı olmak için iki deneyim arasındaki farklar açıklanmaktadır. 

## <a name="filter-results-of-a-query"></a>Sorgunun sonuçlarını filtreleme
Günlük aramasında, arama sonuçları teslim edildiğinde bir filtre listesi görüntülenir. Sorguyu seçili filtreyle çalıştırmak için bir filtre seçin ve **Uygula** ' ya tıklayın.

![Günlük arama filtresi](media/log-search-transition/filter-log-search.png)

Azure Izleyici günlükleri ' nde filtreleri görüntülemek için *filtre (Önizleme)* öğesini seçin. Ekleme filtrelerini göstermek için filtre simgesine tıklayın. Sorguyu seçili filtreyle çalıştırmak için bir filtre seçin ve **çalıştır & Uygula** ' ya tıklayın.

![Günlükler filtresi](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Özel alanları Ayıkla 
Günlük aramasında, liste görünümünden [özel alanlar](../platform/custom-fields.md) ayıkladıktan sonra bir alanın menüsü, _tablodaki alanları Ayıkla_eylemini içerir.

![Günlük araması ayıklama alanları](media/log-search-transition/extract-fields-log-search.png)

Azure Izleyici günlüklerinde, tablo görünümünden özel alanları ayıkladığınızda. Sol oka tıklayarak bir kaydı genişletin ve _alanları Ayıkla_ eylemine erişmek için üç noktaya tıklayın.

![Günlükleri ayıklama alanları](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>İşlevler ve bilgisayar grupları
Günlük aramasında bir aramayı kaydetmek için, bir ad, kategori ve sorgu metni sağlamak üzere **kaydedilmiş aramalar** ' ı ve **Ekle** ' yi seçin. Bir işlev diğer adı ekleyerek bir [bilgisayar grubu](../platform/computer-groups.md) oluşturun.

![Günlük aramasını Kaydet](media/log-search-transition/save-search-log-search.png)

Geçerli sorguyu Azure Izleyici günlüklerine kaydetmek için **Kaydet**' i seçin. **Farklı kaydet** işlevini _işlevine_ değiştirin ve Işlev oluşturmak Için bir **işlev diğer adı** [sağlayın.](functions.md) Bir [bilgisayar grubu](../platform/computer-groups.md)için işlev diğer adını kullanmak üzere _Bu sorguyu bilgisayar grubu olarak kaydet_ ' i seçin.

![Günlük sorgusunu Kaydet](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Kaydedilen sorgular
Günlük aramasında, kaydedilmiş sorgularınızı, Eylem çubuğu öğesi **kaydedilmiş aramaları**aracılığıyla kullanılabilir. Azure Izleyici günlükleri ' nde, kaydedilmiş sorgulara [sorgu Gezgini](../log-query/get-started-portal.md#save-queries)'nden erişin.

![Sorgu Gezgini](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Özetlenen satırlarda detaya gitme
Günlük arama ' da, Özet sorgudaki bir satıra tıklayarak bu satırdaki ayrıntılı kayıtları listeleyen başka bir sorgu başlatabilirsiniz.

![Günlük araması ayrıntıya git](media/log-search-transition/drilldown-search.png)

Azure Izleyici günlüklerinde, bu kayıtları döndürmek için sorguyu değiştirmeniz gerekir. Sonuçlarda bulunan satırlardan birini genişletin ve sorguya eklemek için değerin yanındaki **+** tıklatın. Ardından **özetleme** komutunu açýklama ve sorguyu yeniden çalıştırın.

![Azure Izleyici günlüklerine git](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>İşlem yapın
Günlük aramasında, **eylem al**' ı seçerek bir [runbook 'u](take-action.md) arama sonuçlarından başlatabilirsiniz.

![İşlem yapın](media/log-search-transition/take-action-log-search.png)

Azure Izleyici günlükleri ' nde [günlük sorgusundan bir uyarı oluşturun](../platform/alerts-log.md). Uyarıya yanıt olarak çalışacak bir veya daha fazla eylem içeren bir eylem grubu yapılandırın.

![Eylem grubu](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Sonraki adımlar

- Yeni [Azure izleyici günlükleri deneyimi](get-started-portal.md)hakkında daha fazla bilgi edinin.