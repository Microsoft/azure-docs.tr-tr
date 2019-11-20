---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 28b53864abc37a880a9573cc9657231aff862336
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029879"
---
Coğrafi olarak yedekli depolama (GRS), verilerinizi birincil bölgeden yüzlerce mil uzakta olan ikincil bir bölgeye çoğaltarak belirli bir yıl boyunca en az% 99.99999999999999 (16 9) nesne dayanıklılığı sağlamak üzere tasarlanmıştır. Depolama hesabınızda GRS etkinse, tüm bölgesel bir kesinti veya birincil bölgenin kurtarılamaz bir olağanüstü durum durumunda bile verileriniz dayanıklı olur.

GRS 'yi kabul ediyorsanız, aralarından seçim yapabileceğiniz iki ilgili seçeneğe sahipsiniz:

* GRS, verilerinizi ikincil bölgedeki başka bir veri merkezine çoğaltır, ancak Microsoft birincil ve ikincil bölgeye bir yük devretme işlemi başlattığında bu veriler okunabilir.
* Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS), GRS 'yi temel alır. RA-GRS, verilerinizi ikincil bölgedeki başka bir veri merkezine çoğaltır ve ayrıca ikincil bölgeden okuma seçeneği sağlar. RA-GRS sayesinde, Microsoft 'un birincili ikincil bölgeye yük devretme işlemi başlatıp başlatmadığına bakılmaksızın ikincil bölgeden okuyabilirsiniz.

GRS veya RA-GRS kullanan bir depolama hesabı için tüm veriler önce yerel olarak yedekli depolama (LRS) ile çoğaltılır. Bir güncelleştirme öncelikle birincil konuma kaydedilir ve LRS kullanılarak çoğaltılır. Güncelleştirme daha sonra GRS kullanarak ikincil bölgeye zaman uyumsuz olarak çoğaltılır. Veriler ikincil konuma yazıldığında, LRS kullanarak bu konumda da çoğaltılır.

Birincil ve ikincil bölgeler, ayrı hata etki alanları genelinde çoğaltmaları yönetir ve depolama ölçek birimi içindeki etki alanlarını yükseltir. Depolama ölçek birimi, veri merkezi içindeki temel çoğaltma birimidir. Bu düzeyde çoğaltma, LRS tarafından sağlanır; daha fazla bilgi için bkz [. yerel olarak yedekli depolama (LRS): Azure depolama](../articles/storage/common/storage-redundancy-lrs.md)için düşük maliyetli veri artıklığı.

Hangi çoğaltma seçeneğinin kullanılacağına karar verirken bu noktaları göz önünde bulundurun:

* Coğrafi bölge yedekli depolama (GZRS) (Önizleme), üç Azure kullanılabilirlik alanında verileri eşzamanlı olarak çoğaltarak ve ardından verileri zaman uyumsuz olarak ikincil bölgeye çoğaltırken maksimum dayanıklılık ile birlikte yüksek kullanılabilirlik sağlar. İkincil bölgeye okuma erişimini de etkinleştirebilirsiniz. GZRS belirli bir yıl boyunca nesnelerin en az% 99.99999999999999 (16 9) oranında dayanıklılığını sağlamak üzere tasarlanmıştır. GZRS hakkında daha fazla bilgi için bkz. [coğrafi bölge yedekli depolama, yüksek oranda kullanılabilirlik ve en yüksek dayanıklılık (Önizleme) için](../articles/storage/common/storage-redundancy-gzrs.md).
* Bölgesel olarak yedekli depolama (ZRS), üç Azure kullanılabilirlik alanı arasında zaman uyumlu çoğaltma ile yüksek kullanılabilirlik sağlar ve GRS veya RA-GRS 'den bazı senaryolar için daha iyi bir seçenek olabilir. ZRS hakkında daha fazla bilgi için bkz. [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Zaman uyumsuz çoğaltma, verilerin birincil bölgeye yazıldığı zamandan, ikincil bölgeye çoğaltılmasıyla ilgili bir gecikme içerir. Bölgesel bir olağanüstü durum durumunda, bu veriler birincil bölgeden kurtarılamazsa, ikincil bölgeye henüz çoğaltılmamış değişiklikler kaybolabilir.
* GRS ile Microsoft, ikincil bölgede bir yük devretme işlemi başlatmadığı takdirde okuma veya yazma erişimi için kullanılamaz. Yük devretme durumunda, yük devretme tamamlandıktan sonra bu verilere okuma ve yazma erişiminiz olacaktır. Daha fazla bilgi için lütfen bkz. [olağanüstü durum kurtarma Kılavuzu](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Uygulamanızın ikincil bölgeden okuması gerekiyorsa, RA-GRS veya RA-GZRS (Önizleme) seçeneğini etkinleştirin.
