---
title: Azure Sentinel 'de olay ölçümleriyle SOC 'i daha iyi yönetin | Microsoft Docs
description: Güvenlik Işlemleri merkezinizi (SOC) yönetmenize yardımcı olması için Azure Sentinel olay ölçümleri ekranındaki ve çalışma kitabındaki bilgileri kullanın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660738"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Olay ölçümleriyle SOC’nizi daha iyi yönetme

> [!IMPORTANT]
> Olay ölçümleri özellikleri şu anda genel önizlemededir.
> Bu özellikler, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bir güvenlik Operations Center (SOC) Yöneticisi olarak, takımınızın performansını ölçtüğünden her seferinde genel verimlilik ölçümlerine ve ölçülere sahip olmanız gerekir. Önem derecesi, MITRE taktiği, ortalama önceliklendirme süresi, çözülmesi gereken ortalama süre gibi birçok farklı ölçüte göre olay işlemlerini zaman içinde görmek isteyeceksiniz. Azure Sentinel artık, bu verileri Log Analytics yeni **Securityıncident** tablosu ve şeması ile ve ilgili **güvenlik işlemleri verimlilik** çalışma kitabında kullanılabilir hale getirir. Takımınızın zaman içindeki performansını görselleştirebilecek ve bu öngörüyü kullanarak verimliliği artırabilirsiniz. Ayrıca, özel denetim gereksinimlerinize ve KPI 'lerinize uyan özelleştirilmiş çalışma kitapları oluşturmak için, olay tablosuna karşı kendi KQL sorgularınızı yazabilir ve kullanabilirsiniz.

## <a name="use-the-security-incidents-table"></a>Güvenlik olayları tablosunu kullanın

**Securityıncident** tablosu, Azure Sentinel 'de yerleşik olarak bulunur. Bunu, **günlük** altındaki **securityınsights** koleksiyonundaki diğer tablolarla bulabilirsiniz. Bunu, Log Analytics diğer tablolar gibi sorgulayabilirsiniz.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Güvenlik olayları tablosu":::

Bir olayı her oluşturduğunuzda veya güncelleştirdiğinizde, tabloya yeni bir günlük girdisi eklenir. Bu, olaylara yapılan değişiklikleri izlemenize ve daha fazla güçlü SOC ölçümlerine olanak tanır, ancak bir olay için yinelenen girdileri (çalıştırdığınız sorguya bağlı olarak) kaldırmanız gerekebilse de bu tablo için sorgular oluşturulurken bunun en az olması gerekir. 

Örneğin, olay numaralarına göre sıralanmış tüm olayların bir listesini döndürmek istiyorsanız ancak olay başına en son günlüğü döndürmek istiyorsanız bunu, toplama işleviyle KQL [özetleme işlecini](/azure/data-explorer/kusto/query/summarizeoperator) kullanarak yapabilirsiniz `arg_max()` [](/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>Daha fazla örnek sorgu

Ortalama kapanış süresi:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Önceliklendirme süresi (Ortalama):
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Güvenlik işlemleri verimlilik çalışma kitabı

**Securityolaylarınızın** tablosunu tamamlamak IÇIN, SOC işlemlerinizi izlemek üzere kullanabileceğiniz bir kullanıma hazır **güvenlik işlemleri verimlilik** çalışma kitabı şablonu sunuyoruz. Çalışma kitabı aşağıdaki ölçümleri içerir: 
- Zaman içinde oluşturulan olay 
- Sınıflandırma, önem derecesi, sahip ve durum kapatıldıktan sonra oluşturulan olaylar 
- Ortalama önceliklendirme süresi 
- Ortalama kapanış süresi 
- Zaman içinde önem derecesi, sahip, durum, ürün ve tackler tarafından oluşturulan olaylar 
- Yüzdebirlik değeri önceliklendirme süresi 
- Kapanış süresi yüzdebirlik değeri 
- Sahibe göre önceliklendirme süresi 
- Son Etkinlikler 
- Son Kapanış sınıflandırmaları  

Bu yeni çalışma kitabı şablonunu Azure Sentinel gezinti menüsünden **çalışma kitapları** ' nı seçerek ve **Şablonlar** sekmesini seçerek bulabilirsiniz. Galeriden **güvenlik işlemleri verimliliği** ' nı seçin ve **kaydedilmiş çalışma kitabını görüntüle** ve **şablonu görüntüle** düğmelerinden birine tıklayın.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Güvenlik olayları çalışma kitabı Galerisi":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Güvenlik olayları çalışma kitabı Tamam":::

Özel gereksinimlerinize göre özel çalışma kitaplarınızı oluşturmak için şablonunu kullanabilirsiniz.

## <a name="securityincidents-schema"></a>Securityolayolayları şeması

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Azure Sentinel ile çalışmaya başlamak için Microsoft Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
- [Verilerinizi Azure Sentinel 'e](quickstart-onboard.md) [ekleme ve verilerinize ilişkin görünürlük alma ve olası tehditler](quickstart-get-visibility.md)hakkında bilgi edinin.