---
title: Uzantıları kullanarak dağıtım sonrası yapılandırma
description: Dağıtım sonrası konfigürasyonlar sağlamak için Azure Resource Manager Şablon Uzantıları kullanmayı öğrenin.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: c7896efb453cac478202efedb268c4bc838aef3a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150363"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Uzantıları kullanarak dağıtım sonrası yapılandırma sağlama

Şablon Uzantıları, Azure kaynakları üzerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. En popüler bir sanal makine uzantılarıdır. Bkz. [Windows Için sanal makine uzantıları ve özellikleri](../virtual-machines/extensions/features-windows.md)ve [Linux için sanal makine uzantıları ve özellikleri](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Uzantılar

Mevcut uzantılar şunlardır:

- [Microsoft. COMPUTE/virtualMachines/uzantıları](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. COMPUTE virtualMachineScaleSets/uzantıları](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft. HDInsight kümeleri/uzantıları](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft. SQL sunucuları/veritabanları/uzantıları](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft. Web/Sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Kullanılabilir uzantıları bulmak için [şablon başvurusuna](https://docs.microsoft.com/azure/templates/)gidin. **Başlığa göre filtrele**' de **uzantı**girin.

Bu uzantıların nasıl kullanılacağını öğrenmek için bkz.:

- [Öğretici: Azure Resource Manager şablonlarıyla sanal makine uzantıları dağıtın](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Öğretici: Azure Resource Manager şablonlarıyla SQL BACPAC dosyalarını Içeri aktarma](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure Resource Manager şablonlarıyla sanal makine uzantılarını dağıtma](./resource-manager-tutorial-deploy-vm-extensions.md)
