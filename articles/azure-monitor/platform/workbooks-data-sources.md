---
title: Birden çok Azure veri kaynağından Azure Izleyici çalışma kitaplarını kullanarak etkileşimli raporlar oluşturun | Microsoft docs
description: Birden çok veri kaynağından oluşturulan önceden oluşturulmuş ve özel parametreli Azure Izleyici çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dd5068da31c3aa863fc56022834a28b60ee15004
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166206"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Izleyici çalışma kitapları veri kaynakları

Çalışma kitapları çok sayıda veri kaynağı ile uyumludur. Bu makale, şu anda Azure Izleyici çalışma kitapları için kullanılabilen veri kaynakları boyunca size yol gösterecektir.

## <a name="logs"></a>Günlükler

Çalışma kitapları aşağıdaki kaynaklardan günlükleri sorgulamaya izin verir:

* Azure Izleyici günlükleri (Application Insights kaynakları ve Log Analytics çalışma alanları.)
* Kaynak merkezli veriler (etkinlik günlükleri)

Çalışma kitabı yazarları, temel kaynak verilerini dönüştüren KQL sorgularını, metin, grafikler veya kılavuzlar olarak görselleştirilebilen bir sonuç kümesi seçmek için kullanabilir.

![Çalışma kitabı günlüğü rapor arabiriminin ekran görüntüsü](./media/workbooks-overview/logs.png)

Çalışma kitabı yazarları, gerçekten Birleşik bir zengin raporlama deneyimi oluşturarak birden çok kaynak genelinde kolayca sorgu yapabilir.

## <a name="metrics"></a>Ölçümler

Azure kaynakları, çalışma kitapları aracılığıyla erişilebilen [ölçümleri](data-platform-metrics.md) yayar. Ölçümlere, hedef kaynakları, istenen ölçümleri ve bunların toplamasını belirtmenize olanak tanıyan özelleştirilmiş bir denetim aracılığıyla çalışma kitaplarından erişilebilir. Bu veriler daha sonra grafiklerde veya kılavuzlarda çizilebilirler.

![CPU kullanımının çalışma kitabı ölçüm grafiklerinin ekran görüntüsü](./media/workbooks-overview/metrics-graph.png)

![Çalışma kitabı ölçümleri arabiriminin ekran görüntüsü](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Kaynak Grafiği 

Çalışma kitapları, Azure Kaynak Grafiği (ARG) kullanarak kaynakları ve bunların meta verilerini sorgulamayı destekler. Bu işlevsellik öncelikle raporlar için özel sorgu kapsamları oluşturmak için kullanılır. Kaynak kapsamı, bağımsız değişken tarafından desteklenen bir KQL-alt kümesi ile ifade edilir ve genellikle yaygın kullanım örnekleri için yeterlidir.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, Azure Kaynak Grafiği ' ni seçmek üzere sorgu türü açılan listesini kullanın ve hedeflenecek abonelikleri seçin. İlgi çekici bir kaynak alt kümesi seçen ARG KQL-alt kümesini eklemek için sorgu denetimini kullanın.


![Azure Kaynak Grafiği KQL sorgusunun ekran görüntüsü](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Uyarılar (Önizleme)

Çalışma kitapları, kullanıcıların kaynaklarıyla ilgili etkin uyarıları görselleştirmesine olanak tanır. Bu özellik bildirim verileri (uyarı) ve tanılama bilgilerini (ölçümler, Günlükler) tek bir raporda oluşturan raporların oluşturulmasına olanak sağlar. Bu bilgiler ayrıca, bu veri kaynakları genelinde öngörüleri birleştiren zengin raporlar oluşturmak için birlikte birleştirilebilir.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için sorgu türü açılan listesini kullanarak Uyarılar ' ı seçin ve hedeflenecek abonelikleri, kaynak gruplarını veya kaynakları seçin. Analitik gereksinimleriniz için uyarıların ilginç bir alt kümesini seçmek üzere uyarı filtresi açılan listelerini kullanın.

![Uyarı sorgusunun ekran görüntüsü](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>İş yükü sistem durumu (Önizleme)

Azure Izleyici, Windows veya Linux konuk işletim sistemlerinin kullanılabilirliğini ve performansını önceden izleyerek izleyen işlevlere sahiptir. Azure Izleyici, temel bileşenleri ve bunların ilişkilerini, bu bileşenlerin sistem durumunu ölçme ölçütlerini ve sağlıksız bir durum algılandığında hangi bileşenlerin sizi uyarmasını sağlar. Çalışma kitapları, kullanıcıların zengin etkileşimli raporlar oluşturmak için bu bilgileri kullanmasına izin verir.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, **sorgu türü** açılan listesini kullanarak Iş yükü sistem durumunu seçin ve hedeflenen abonelik, kaynak grubu veya VM kaynakları ' nı seçin. Analiz gereksinimleriniz için sistem durumu olaylarının ilgi çekici bir alt kümesini seçmek üzere durum filtre açılan listelerini kullanın.

![Uyarı sorgusunun ekran görüntüsü](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure Kaynak durumu 

Çalışma kitapları, Azure Kaynak durumu almayı ve zengin, etkileşimli sistem durumu raporları oluşturmak için diğer veri kaynaklarıyla birleştirmeyi destekler

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için **sorgu türü** açılan listesini kullanarak Azure sistem durumu ' nu seçin ve hedeflenecek kaynakları seçin. Analiz gereksinimleriniz için kaynak sorunlarının ilginç bir alt kümesini seçmek üzere durum filtre açılan listelerini kullanın.

![Uyarı sorgusunun ekran görüntüsü](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Veri Gezgini (Önizleme)

Artık çalışma kitapları, güçlü [kusto](https://docs.microsoft.com/azure/kusto/query/index) sorgu diliyle [Azure Veri Gezgini](https://docs.microsoft.com/azure/data-explorer/) kümelerinden sorgulama desteğine sahiptir.   

![Kusto sorgu penceresinin ekran görüntüsü](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](workbooks-visualizations.md) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](workbooks-access-control.md) ve paylaşma.
