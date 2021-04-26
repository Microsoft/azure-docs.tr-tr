---
title: VM 'Leri Başlat/Durdur v2 (Önizleme) genel bakış
description: Bu makalede, VM 'Leri Başlat/Durdur v2 (Önizleme) özelliğinin nasıl kaldırılacağı açıklanır.
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112031"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>VM 'Leri Başlat/Durdur v2 'yi kaldırma (Önizleme)

Azure VM 'lerinizin çalışma durumunu yönetmek üzere VM 'Leri Başlat/Durdur v2 (Önizleme) özelliğini etkinleştirdikten sonra, kullanmayı durdurmayı seçebilirsiniz. Bu özelliğin kaldırılması, VM 'Leri Başlat/Durdur v2 (Önizleme) desteğiyle aşağıdaki kaynakları depolamak için ayrılmış kaynak grubu silinerek yapılabilir:

- Azure Işlevleri uygulamaları
- Azure Logic Apps zamanlamalar
- Application Insights örneği
- Azure Storage hesabı

## <a name="delete-the-dedicated-resource-group"></a>Adanmış kaynak grubunu silme

Kaynak grubunu silmek için [Azure Resource Manager kaynak grubu ve kaynak silme](../../azure-resource-manager/management/delete-resource-group.md) makalesinde özetlenen adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu özelliği yeniden dağıtmak için bkz. [dağıtımı Başlat/Durdur v2](deploy.md) (Önizleme).