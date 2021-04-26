---
title: VM öngörüleri Konuk sistem durumu 'nda izlemeyi yapılandırma (Önizleme)
description: Azure portal kullanarak VM öngörüleri Konuk sistem durumu (Önizleme) için varsayılan izlemenin nasıl değiştirileceğini açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 6bda6c9e5f6e23e9e15c12fd507645fc72159302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583454"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>VM öngörüleri Konuk sistem durumu 'nda izlemeyi yapılandırma (Önizleme)
VM öngörüleri Konuk durumu, bir sanal makinenin durumunu, düzenli aralıklarla örneklendiği bir dizi performans ölçümlerine göre tanımlanan şekilde görüntülemenize olanak sağlar. Bu makalede, Azure portal kullanarak varsayılan izlemenin nasıl değiştirileceği açıklanır. Ayrıca, [bir veri toplama kuralı kullanarak izlemeyi yapılandırmak](vminsights-health-configure-dcr.md)için gereken izleyicilerin temel kavramlarını açıklar.

## <a name="open-monitor-configuration"></a>İzleyici yapılandırmasını aç
İzleyici ve **yapılandırma** sekmesini seçerek Azure Portal İzleyici yapılandırmasını açın.

[![İzleme ayrıntıları yapılandırması](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>İzleyicileri etkinleştirme veya devre dışı bırakma
Birim izleyicilerinin ve toplam izleyicilerin, izleyiciyi etkinleştirmenizi veya devre dışı bırakmanızı sağlayan bir **sistem durumu İzleyicisi durum** ayarı vardır. Bir izleyici etkinleştirildiğinde, sistem durumu görüntülenir ve VM 'nin sistem durumunu ayarlamak için kullanılır. Bir izleyici devre dışı bırakıldığında, sistem durumu hesaplanmaz ve VM 'nin sistem durumunu ayarlamak için kullanılmaz.

| Ayar | Açıklama |
|:---|:---|
| Etkin | İzleyici, üst öğesinin ayarından bağımsız olarak etkinleştirilir. |
| Devre dışı | İzleyici, üst öğesinin ayarından bağımsız olarak devre dışı bırakılır. |
| Üst öğeyle aynı | İzleyici, üst öğesinin ayarına bağlı olarak etkinleştirilecek veya devre dışı bırakılacak. |

Bir izleyici devre dışı bırakıldığında, aşağıdaki örnekte gösterildiği gibi herhangi bir ölçüt kullanılamaz olarak gösterilir.

![Devre dışı izleme](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Bir üst izleme devre dışıysa, tüm alt izleyiciler de devre dışı bırakılır. Alt izlemeyi açıkça etkinleştirirseniz, üst öğe de etkinleştirilir, ancak yapılandırma durumu aynı kalır. Bu durumda, üst monitörde aşağıdaki iletiyi alacaksınız.
>
> *İzleyicinin yapılandırılan durumu ' devre dışı ' olduğundan bir tutarsızlık var, ancak sistem durumu bunu yansıtmıyor. Bunun nedeni, yapılandırılan değişikliklerin yayılmakta olması veya alt izlemelerinin herhangi birinin açıkça etkinleştirilmiş olması nedeniyle oluşur.*

## <a name="enable-or-disable-virtual-machine"></a>Sanal makineyi etkinleştir veya devre dışı bırak
Tüm izleyicileri geçici olarak durdurmak için bir VM 'nin izlenmesini devre dışı bırakabilirsiniz. Üzerinde bakım gerçekleştirirken bir VM için izlemeyi devre dışı bırakabilirsiniz.

| Ayar | Açıklama |
|:---|:---|
| Etkin  | Bilgisayarın sistem durumu görüntülenir. |
| Devre dışı | Bilgisayarın sistem durumu **devre dışı** olarak gösterilir. Uyarılar oluşturulmaz. |

## <a name="health-state-logic"></a>Sistem durumu mantığı
Bir birim izleyicisinin sistem durumu mantığı, sistem durumunu ayarlamaya yönelik kriterleri tanımlar. Bir izleyicinin kaç sistem durumu olduğunu ve her bir sistem durumunun nasıl hesaplanacağını belirten eşiği belirtebilirsiniz.

![Örnek durum ölçütleri](media/vminsights-health-configure/sample-health-criteria.png)

Toplama izleyicileri hiçbir sistem durumu mantığını belirtmez. Sistem durumu, bunların altındaki birim izleyicileri tarafından sistem durumu toplamasına göre ayarlanır.

Birim izleyicilerinin her biri iki veya üç sistem durumuna sahiptir. Her zaman sağlıklı bir duruma ve isteğe bağlı olarak bir uyarı durumuna, kritik duruma veya her ikisine birden sahip olur. Uyarı ve kritik durumlar, izleyicinin bu duruma ne zaman ayarlandığını tanımlayan benzersiz ölçütler gerektirir. Diğer durumlar için ölçütler karşılanmıyorsa, bu durum ayarlandığı için sağlıklı durumun ölçütü yoktur.

Aşağıdaki örnekte, CPU kullanımı aşağıdaki sistem durumlarına ayarlanır:

- %90 ' den fazla olursa kritik öneme sahiptir.
- %80 değerinden büyük veya bu değere eşit olursa uyarı.
- %80 altındaysa sağlıklı. Bu, diğer koşullardan hiçbirinin uygulanmamasından bu yana kapsanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Veri toplama kurallarını kullanarak izleyicileri ölçeklendirerek yapılandırın.](vminsights-health-configure-dcr.md)
