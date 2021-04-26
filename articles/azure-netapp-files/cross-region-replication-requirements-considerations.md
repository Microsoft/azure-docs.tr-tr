---
title: Azure NetApp Files birim çapraz bölge çoğaltmasını kullanma gereksinimleri ve konuları | Microsoft Docs
description: Azure NetApp Files birim çapraz bölge çoğaltma işlevini kullanma gereksinimlerini ve ilgili konuları açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: b-juche
ms.openlocfilehash: ed03e20f7a1a24d1a38e023b958959fdc6fdc326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579347"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Bölgeler arası çoğaltmayı kullanma gereksinimleri ve konuları 

Azure NetApp Files [birim çapraz bölge çoğaltma işlevini kullanma](cross-region-replication-create-peering.md) hakkında aşağıdaki gereksinimlere ve noktalara dikkat edin:  

## <a name="requirements-and-considerations"></a>Gereksinimler ve önemli noktalar 

* Bölgeler arası çoğaltma özelliği şu anda genel önizlemededir. [Azure NetApp Files çapraz bölge çoğaltma eklenebileceğinizi gönderimi sayfası](https://aka.ms/anfcrrpreviewsignup)aracılığıyla özelliğe erişmek için bir eklenebileceğinizi isteği göndermeniz gerekir. Bölgeler arası çoğaltma özelliğini kullanmadan önce Azure NetApp Files ekibinden bir resmi onay e-postası bekleyin.
* Azure NetApp Files çoğaltma yalnızca belirli sabit bölge çiftlerinde kullanılabilir. Bkz. [desteklenen bölge çiftleri](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB birimleri NFS birimleri ile birlikte desteklenir. SMB birimlerinin çoğaltılması, kaynak ve hedef NetApp hesaplarında bir Active Directory bağlantısı gerektirir. Hedef AD bağlantısının DNS sunucularına erişimi olmalıdır veya hedef bölgedeki Temsilcili alt ağdan erişilebilen etki alanı denetleyicileri ekler. Daha fazla bilgi için bkz. [Active Directory bağlantıları gereksinimleri](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Hedef hesap, kaynak birim bölgesinden farklı bir bölgede olmalıdır. Ayrıca, farklı bir bölgede var olan bir NetApp hesabını da seçebilirsiniz.  
* Hedef birimi okuma ve yazma için etkinleştirmek üzere [hedef bölgeye yük devretmek](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) için çoğaltma hedefi birimi salt okunurdur. 
* Azure NetApp Files çoğaltma şu anda birden çok aboneliği desteklemiyor; tüm çoğaltmalar tek bir abonelik altında gerçekleştirilmelidir.
* Her bölge için tek bir abonelik içinde çoğaltma için en fazla beş birim ayarlayabilirsiniz. Beş çoğaltma hedefi birimi (bir bölgedeki abonelik başına) için varsayılan kotada bir artış istemek üzere bir destek bileti açabilirsiniz. 
* Arabirim, kaynak birimde yeni eklenen bir anlık görüntüyü yansıtmak için beş dakikaya kadar bir gecikme olabilir.  
* Geçişli ve fanın/Out topolojileri desteklenmez.
* Anlık görüntüden oluşturulan kaynak birimler için birim çoğaltmasını yapılandırma işlemi şu anda desteklenmiyor.
* Çapraz bölge çoğaltmasını ayarladıktan sonra, çoğaltma işlemi kaynak birim ve hedef birim arasında başvurular sağlamak için *anlık görüntü anlık görüntüleri* oluşturur. Her artımlı aktarım için yeni bir tane oluşturulduğunda, ek yük yansıtma anlık görüntüleri otomatik olarak gösterilir. Çoğaltma ilişkisi ve birimi silinene kadar anlık görüntü yansıtma anlık görüntülerini silemezsiniz. 
* Çoğaltma ilişkisi etkin veya bozuk olduğunda ve ayrıca çoğaltma ilişkisi silindikten sonra, bir çoğaltma ilişkisinin kaynak hacminde el ile anlık görüntüleri silebilirsiniz. Hedef birim için el ile anlık görüntüleri, çoğaltma ilişkisi kesilene kadar silemezsiniz.
* Çoğaltma hedefi birimi oluşturulmadan önce alınmış bir anlık görüntüye döndüremezsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Birim çoğaltması oluşturma](cross-region-replication-create-peering.md)
* [Çoğaltma ilişkisinin uygunluk durumunu görüntüleme](cross-region-replication-display-health-status.md)
* [Olağanüstü durum kurtarmayı yönetme](cross-region-replication-manage-disaster-recovery.md)
* [Birim çoğaltma ölçümleri](azure-netapp-files-metrics.md#replication)
* [Birim çoğaltmalarını veya birimleri silme](cross-region-replication-delete.md)
* [Bölgeler arası çoğaltma sorunlarını giderme](troubleshoot-cross-region-replication.md)


