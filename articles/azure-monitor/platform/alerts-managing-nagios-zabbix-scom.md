---
title: Azure Izleyici 'de System Center Operations Manager, Zabbix ve Nagios 'tan gelen uyarıları yönetme
description: Azure Izleyici 'de System Center Operations Manager, Zabbix ve Nagios 'tan gelen uyarıları yönetme
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 0e774633542f9b7f8b7de9f8f187821bfbe6b9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555624"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Azure Izleyici 'de System Center Operations Manager, Zabbix ve Nagios 'tan gelen uyarıları yönetme

Artık, [Azure izleyici](https://aka.ms/azure-alerts-overview)'de Nagios, Zabbix ve System Center Operations Manager uyarılarını görüntüleyebilirsiniz. Bu uyarılar Nagios/Zabbix sunucularıyla tümleştirmelere veya Log Analytics System Center Operations Manager. 

## <a name="prerequisites"></a>Önkoşullar
Bir uyarı türünde Log Analytics deposundaki tüm kayıtlar Azure Izleyici 'ye içeri aktarıldığından, bu kayıtları toplamak için gereken yapılandırmayı gerçekleştirmeniz gerekir.
1. **Nagios** ve **Zabbix** uyarıları için, [Bu sunucuları](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) Log Analytics [uyarıları gönderecek](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) şekilde yapılandırın.
1. **System Center Operations Manager** uyarılar için [Operations Manager yönetim grubunuzu Log Analytics çalışma alanınıza bağlayın](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Bunu izleyerek [uyarı yönetimi](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) çözümünü Azure çözüm marketi ' nden dağıtın. İşiniz bittiğinde, System Center Operations Manager oluşturulan tüm uyarılar Log Analytics içine aktarılır.

## <a name="view-your-alert-instances"></a>Uyarı örneklerinizi görüntüleme
İçeri aktarmayı Log Analytics yapılandırdıktan sonra, [Azure izleyici](https://aka.ms/azure-alerts-overview)'de bu izleme hizmetlerinden uyarı örneklerini görüntülemeye başlayabilirsiniz. Azure Izleyici 'de mevcut olduktan sonra, [Uyarı örneklerinizi yönetebilir](https://aka.ms/managing-alert-instances), [Bu uyarılarda oluşturulan akıllı grupları yönetebilir](https://aka.ms/managing-smart-groups) ve [uyarılarınızın ve akıllı grupların durumunu değiştirebilirsiniz](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Bu çözüm yalnızca Azure Izleyici 'de System Center Operations Manager/Zabbix/Nagios tetiklenen uyarı örneklerini görüntülemenize izin verir. Uyarı kuralı yapılandırması, yalnızca ilgili izleme araçlarında görüntülenebilir/düzenlenebilir. 
>  1. Tetiklenen tüm uyarı örnekleri hem Azure Izleyici 'de hem de Azure Log Analytics kullanılabilir. Şu anda, yalnızca belirtilen iki veya daha fazla tetiklenen uyarı arasından seçim yapmanın bir yolu yoktur.
>  1. System Center Operations Manager, Zabbix ve Nagios 'ın tüm uyarıları, temeldeki telemetri türü kullanılamadığından "bilinmiyor" sinyal türüne sahip.
>  1. Nagios uyarıları durum bilgisi değildir; Örneğin, bir uyarının [izleme koşulu](https://aka.ms/azure-alerts-overview) "tetiklenir" durumundan "çözümlendi" durumuna geçmeyecektir. Bunun yerine, her ikisi de ayrı bir uyarı örneği olarak "tetiklenir" ve "çözümlendi" görüntülenir. 

