---
title: Oracle için BareMetal için düzeltme eki uygulama konuları
description: Oracle için BareMetal altyapısına yönelik işletim sistemi/çekirdek düzeltme eki uygulama konuları hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559221"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Oracle için BareMetal için düzeltme eki uygulama konuları

Bu makalede, Oracle için BareMetal altyapısına yönelik önemli işletim sistemi/çekirdek düzeltme eki uygulama konularına bakacağız.

Doğru ağ performansı ve sistem kararlılığı için, aşağıdaki uyumluluk tablosunda gösterildiği gibi ENIC ve Fnıc sürücülerinin işletim sistemine özgü sürümünü de yüklersiniz. 

Sunucular, uyumlu sürümlere sahip müşterilere dağıtılır. Ancak, işletim sistemi (OS)/Kernel düzeltme eki uygulama sırasında, sürücüler varsayılan sürücü sürümlerine geri alınabilir. Bu nedenle, uygun sürücü sürümünün işletim sistemi/çekirdek düzeltme eki uygulama işlemlerinin çalıştığını onaylamanız emin olun.

| İşletim sistemi satıcısı | İşletim sistemi paketi sürümü | Üretici Yazılımı Sürümü | eNIC sürücüsü | Fnıc sürücüsü |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7,6 | 3.2.3 ı | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7,6 | 4.1.1 b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Sonraki adımlar

Oracle için BareMetal 'ın Ethernet yapılandırması hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Oracle için BareMetal 'ın Ethernet yapılandırması](oracle-baremetal-ethernet.md)

