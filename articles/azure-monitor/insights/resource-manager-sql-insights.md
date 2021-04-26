---
title: SQL Insights için Kaynak Yöneticisi şablonu örnekleri
description: SQL öngörülerini dağıtmak ve yapılandırmak için örnek Azure Resource Manager şablonları.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730547"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>SQL Insights için Kaynak Yöneticisi şablonu örnekleri
Bu makalede, Azure 'da çalışan izleme SQL için SQL Insights 'ı etkinleştirmek üzere örnek [Azure Resource Manager şablonlar](../../azure-resource-manager/templates/template-syntax.md) bulunur.  SQL olduğumuz destek teklifi ve sürümleri hakkında ayrıntılı bilgi için bkz. [SQL Insights belgeleri](sql-insights-overview.md) . Her örnek, şablona sağlanacak örnek değerleri içeren bir şablon dosyası ve bir parametre dosyası içerir.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>SQL Insights izleme profili oluşturma
Aşağıdaki örnek, toplanacak SQL izleme verilerini, veri toplama sıklığını içeren bir SQL Insights izleme profili oluşturur ve verilerin gönderileceği çalışma alanını belirtir.


### <a name="template-file"></a>Şablon dosyası

[Git hub 'da şablon dosyasını](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)görüntüleyin.

### <a name="parameter-file"></a>Parametre dosyası

[Git hub 'da parametre dosyasını](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)görüntüleyin.


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>SQL Insights izleme profiline izleme VM 'si ekleme
Bir izleme profili oluşturduktan sonra, söz konusu VM yapılandırmasında belirttiğiniz SQL kaynaklarından verileri uzaktan toplamak üzere yapılandırılacak Azure sanal makineleri ayırmanız gerekir.  Daha fazla ayrıntı için SQL Insights 'ı etkinleştir belgelerine bakın.

Aşağıdaki örnek, belirtilen SQL kaynaklarından verileri toplamak için bir izleme sanal makinesi yapılandırır.


### <a name="template-file"></a>Şablon dosyası

[Git hub 'da şablon dosyasını](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)görüntüleyin.

### <a name="parameter-file"></a>Parametre dosyası

[Git hub 'da parametre dosyasını](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)görüntüleyin.


## <a name="create-an-alert-rule-for-sql-insights"></a>SQL Insights için bir uyarı kuralı oluşturma
Aşağıdaki örnek, belirtilen izleme profili kapsamındaki SQL kaynaklarını kapsayacak bir uyarı kuralı oluşturur.  Bu uyarı kuralı, uyarılar kullanıcı arabirimi bağlam panelinde SQL Insights Kullanıcı arabiriminde görüntülenir.  

Parametre dosyası, SQL Insights 'da sağladığımız uyarı şablonlarından birindeki değerlere sahiptir. Bu, SQL için topladığımız diğer veriler üzerinde uyarı olarak değişiklik yapabilir.  Şablon, uyarı kuralı için bir eylem grubu belirtmiyor.


#### <a name="template-file"></a>Şablon dosyası

[Git hub 'da şablon dosyasını](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)görüntüleyin.

### <a name="parameter-file"></a>Parametre dosyası

[Git hub 'da parametre dosyasını](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)görüntüleyin.





## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici için diğer örnek şablonları alın](../resource-manager-samples.md).
* [SQL Insights hakkında daha fazla bilgi edinin](sql-insights-overview.md).
