---
title: Uzantıları kullanarak dağıtım sonrası yapılandırma
description: Dağıtım sonrası konfigürasyonlar sağlamak için Azure Resource Manager şablonu (ARM şablonu) uzantılarını nasıl kullanacağınızı öğrenin.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 23d5a9aaa546542218a6f839ab415184ff309928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934331"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Uzantıları kullanarak dağıtım sonrası yapılandırma sağlama

Azure Resource Manager şablonu (ARM şablonu) uzantıları, Azure kaynakları üzerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. En popüler bir sanal makine uzantılarıdır. Bkz. [Windows Için sanal makine uzantıları ve özellikleri](../../virtual-machines/extensions/features-windows.md)ve [Linux için sanal makine uzantıları ve özellikleri](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Uzantıları

Mevcut uzantılar şunlardır:

- [Microsoft. COMPUTE/virtualMachines/uzantıları](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. COMPUTE virtualMachineScaleSets/uzantıları](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft. HDInsight kümeleri/uzantıları](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft. SQL sunucuları/veritabanları/uzantıları](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Microsoft. Web/Sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Kullanılabilir uzantıları bulmak için [şablon başvurusuna](/azure/templates/)gidin. **Başlığa göre filtrele**' de **uzantı** girin.

Bu uzantıların nasıl kullanılacağını öğrenmek için bkz.:

- [Öğretici: ARM şablonlarıyla sanal makine uzantıları dağıtın](template-tutorial-deploy-vm-extensions.md).
- [Öğretici: ARM şablonlarıyla SQL BACPAC dosyalarını Içeri aktarma](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: ARM şablonlarıyla sanal makine uzantıları dağıtma](template-tutorial-deploy-vm-extensions.md)
