---
title: VMware yazılım güncelleştirme sıklığı
description: Azure VMware çözümü için desteklenen VMware yazılım güncelleştirme sıklığı.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869872"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Microsoft, Azure VMware Çözüm özel bulutu 'ndaki VMware yazılımlarının (ESXi, vCenter, PSC ve NXS) yaşam döngüsü yönetiminden sorumludur.

Özel bulut yazılımı, VMware 'den yazılım paketi yayınını izleyen bir zamanlamaya göre yükseltilir. Özel bulutunuz yükseltmeler için kapalı kalma süresi gerektirmez.

Özel bulut yazılım paketi yükseltmeleri, Yazılımı VMware 'den en son yazılım paketi sürümünün bir sürümü içinde tutar. Özel bulut yazılımı sürümleri, bireysel yazılım bileşenlerinin en son sürümlerinden (ESXi, NSX-T, vCenter, vSAN) farklı olabilir.

Yazılım güncelleştirmeleri şunları içerir:

- **Düzeltme ekleri** -VMware tarafından yayınlanan güvenlik düzeltme ekleri veya hata düzeltmeleri
- **Güncelleştirmeler** -bir VMware Stack bileşeninin ikincil sürüm değişikliği
- **Yükseltmeler** -bir VMware Stack bileşeninin ana sürüm değişikliği

Microsoft, VMware 'den kullanılabilir hale geldiğinde kritik bir güvenlik düzeltme ekini sınar.

Belgelenmiş VMware geçici çözümleri, sonraki zamanlanan güncelleştirmeler dağıtılana kadar ilgili bir düzeltme ekinin yüklenmesi yerine uygulanır. 
