---
title: Kapsayıcılar için Azure Izleyici bölge eşlemeleri
description: Bu makalede kapsayıcılar için Azure Izleyici, Log Analytics çalışma alanı ve özel ölçümler arasında desteklenen bölge eşlemeleri açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.openlocfilehash: f22c62dddf2e38fa2c9471ce98b49a8aa32390b3
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554025"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Kapsayıcılar için Azure Izleyici tarafından desteklenen bölge eşlemeleri

 Kapsayıcılar için Azure Izleyicisini etkinleştirirken, Log Analytics çalışma alanını ve AKS kümesini bağlamak ve Azure Izleyici 'ye gönderilen özel ölçümleri toplamak için yalnızca belirli bölgeler desteklenir.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics çalışma alanı desteklenen eşlemeler

AKS kümesi kaynakları veya Log Analytics çalışma alanı diğer bölgelerde bulunabilir ve eşlemelerimiz aşağıdaki tabloda gösterilmiştir.

|**AKS küme bölgesi** | **Log Analytics çalışma alanı bölgesi** |
|-----------------------|------------------------------------|
|**Doğu** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Avustralya** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asya Pasifik** | |
|Eastaya |Eastaya |
|Güneydoğu |Güneydoğu |
|**Brezilya** | |
|BrazilSouth | Güneydoğu ABD |
|**Kanada** ||
|Canadaorta |Canadaorta |
|Canadadoğu |Canadaorta |
|**Avrupa** | |
|Francecna al |Francecna al |
|FranceSouth |Francecna al |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|Ukbatı |UKSouth |
|WestEurope |WestEurope |
|**Hindistan** | |
|Merkezileştirme Hindistan |Merkezileştirme Hindistan |
|Güneydoğu |Merkezileştirme Hindistan |
|WestIndia |Merkezileştirme Hindistan |
|**Japonya** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Güney Kore** | |
|KoreaCentral |KoreaCentral |
|Koreagüney |KoreaCentral |
|**ABD** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|ABD Hükümeti Virginia |ABD Hükümeti Virginia |

<sup>1</sup> kapasite depoları nedeniyle, yeni kaynaklar oluşturulurken bölge kullanılamıyor. Buna bir Log Analytics çalışma alanı dahildir. Ancak, bölgede önceden var olan bağlı kaynakların çalışmaya devam etmesi gerekir.

## <a name="custom-metrics-supported-regions"></a>Özel Ölçüm desteklenen bölgeler

Azure Kubernetes Services (AKS) kümeleri düğümlerinden ve yığınlarından ölçümlerin toplanması yalnızca aşağıdaki [Azure bölgelerinde](../platform/metrics-custom-overview.md#supported-regions)özel ölçümler olarak yayımlama için desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

AKS kümenizi izlemeye başlamak için, izlemeyi etkinleştirmek üzere gereksinimleri ve kullanılabilir yöntemleri anlamak üzere [kapsayıcılar Için Azure Izleyicisini nasıl etkinleştireceğinizi](container-insights-onboard.md) gözden geçirin.  
