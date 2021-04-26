---
title: Oracle veritabanınızı Azure BareMetal altyapısında kurtarma
description: Oracle veritabanınızı Azure BareMetal altyapısında nasıl kurtarabileceğinizi öğrenin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590339"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Oracle veritabanınızı Azure BareMetal altyapısında kurtarma

Tüm hata senaryolarından tek bir teknoloji korunurken, özellikleri birleştirmek veritabanı yöneticilerinin veritabanını neredeyse her durumda kurtarmasına olanak tanır.

## <a name="causes-of-database-failure"></a>Veritabanı hatasına neden olan nedenler

Veritabanı hatalarının çoğu nedeni olabilir, ancak genellikle çeşitli kategoriler altına girer:

- Veri işleme hataları.
- Çevrimiçi yeniden yineleme günlükleri kaybı.
- Veritabanı denetim dosyalarının kaybolması.
- Veritabanı veri dosyalarının kaybolması.
- Fiziksel veri bozulması.

## <a name="choose-your-method-of-recovery"></a>Kurtarma yönteminizi seçin

Kurtarma türü hata türüne bağlıdır. Bir nesnenin bırakılmakta olduğunu veya verilerin yanlış bir şekilde değiştirildiğini varsayalım. Daha sonra, en hızlı çözüm genellikle bir flashback veritabanı işlemi yapmak için kullanılır. Diğer durumlarda Azure NetApp Files bir anlık görüntü üzerinden kurtarma, istediğiniz kurtarmayı sağlayabilir. Yukarıda açıklanan tüm veri koruma seçenekleri uygulanırsa, aşağıdaki şekilde karar ağacı ortak hata ve kurtarma senaryolarını temsil eder.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Veritabanı kurtarma karar ağacının diyagramı." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Bu örnek karar ağacının yalnızca bir veritabanı yöneticisinin lens 'ten görüntülendiğini aklınızda bulundurun. Her dağıtım, seçeneklerin sırasını değiştirecek farklı gereksinimlere sahip olabilir. Örneğin, Data Guard aracılığıyla farklı bir bölgeye bir veritabanı rolü geçişi gerçekleştirmek uygulama performansına olumsuz bir etkiye sahip olabilir. Anlık görüntü kurtarma yöntemine daha düşük bir RTO verebilir. RTO/RPO gereksinimlerinin karşılanmasını sağlamak için, bu işlemleri yapmanızı ve gerektiğinde bunları yürütmek için belgelenmiş yordamlar oluşturmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

BareMetal altyapısı hakkında daha fazla bilgi edinin:

- [Azure 'da BareMetal Altyapısı nedir?](../../concepts-baremetal-infrastructure-overview.md)
- [Azure 'da BareMetal altyapı örnekleri bağlama](../../connect-baremetal-infrastructure.md)
