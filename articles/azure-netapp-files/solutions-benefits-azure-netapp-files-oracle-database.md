---
title: Oracle Database ile Azure NetApp Files kullanmanın avantajları | Microsoft Docs
description: Teknolojiyi açıklar ve Oracle doğrudan NFS (dNFS) ve geleneksel NFS istemcisi arasında bir performans karşılaştırması sağlar. Azure NetApp Files ile dNFS kullanmanın avantajlarını gösterir.
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932507"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Oracle Database ile Azure NetApp Files kullanmanın avantajları

Oracle Direct NFS (dNFS), işletim sisteminin kendi NFS sürücüsünden daha yüksek performans kullanmasını mümkün kılar. Bu makalede teknoloji açıklanmakta ve dNFS ile geleneksel NFS istemcisi (çekirdek NFS) arasında bir performans karşılaştırması sunulmaktadır. Ayrıca, Azure NetApp Files ile dNFS kullanmanın avantajlarını ve kolaylığı gösterilmektedir.  

## <a name="how-oracle-direct-nfs-works"></a>Oracle Direct NFS nasıl işe yarar?

Aşağıdaki Özet Oracle doğrudan NFS 'nin yüksek düzeyde nasıl çalıştığını açıklamaktadır:

* Oracle Direct NFS, işletim sistemi arabellek önbelleğini atlar. Veriler, Kullanıcı alanında yalnızca bir kez önbelleğe alınır ve bellek kopyalarının yükünü ortadan kaldırır.  

* Geleneksel NFS istemcisi aşağıda gösterildiği gibi tek bir ağ akışı kullanır:    

    ![Tek bir ağ akışı kullanan geleneksel NFS istemcisi](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle doğrudan NFS, birden çok ağ akışı arasında ağ trafiği yükünü dengeleyerek performansı geliştirir. Sınanmış ve aşağıda gösterildiği gibi, 650 farklı ağ bağlantıları Oracle Database tarafından dinamik olarak oluşturulmuştur:  

    ![Oracle Direct NFS performansı artırma](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Doğrudan NFS Için Oracle SSS](http://www.orafaq.com/wiki/Direct_NFS) , Oracle dnfs 'nin en iyi duruma GETIRILMIŞ bir NFS istemcisi olduğunu gösterir. Bu, NAS depolama cihazlarında (TCP/IP üzerinden erişilebilir) bulunan NFS depolamaya hızlı ve ölçeklenebilir erişim sağlar. dNFS, birincil olarak DAS veya SAN depolama ile kullanılan ASM gibi veritabanı çekirdekte yerleşik olarak bulunur. Bu nedenle, bu şekilde, *NAS depolama uygularken dNFS kullanılması ve San depolama uygularken asm kullanılır.*

dNFS, Oracle 18c ' de varsayılan seçenektir.

dNFS, Oracle Database 11g ile başlayarak kullanılabilir. Aşağıdaki diyagramda dNFS yerel NFS ile karşılaştırılmaktadır. DNFS kullandığınızda, bir Azure sanal makinesi üzerinde çalışan bir Oracle veritabanı, yerel NFS istemcisinden daha fazla g/ç 'yi kullanabilir.

![Oracle ve Azure NetApp Files yerel NFS ile dNFS karşılaştırması](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

İki komut çalıştırarak ve veritabanını yeniden başlatarak dNFS 'yi etkinleştirebilir veya devre dışı bırakabilirsiniz.

Şunları etkinleştirmek için:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Devre dışı bırakmak için:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Oracle Direct NFS ile birleştirilmiş Azure NetApp Files

Oracle dNFS 'nin performansını Azure NetApp Files hizmetiyle geliştirebilirsiniz. Hizmet, uygulamanızın performansı üzerinde size toplam denetim sağlar. Son derece yoğun uygulamaları karşılayabilir. Azure NetApp Files ile Oracle dNFS birleşimi, iş yükleriniz için harika bir avantaj sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure NetApp Files kullanan çözüm mimarileri](azure-netapp-files-solution-architectures.md)
- [Azure 'da Oracle uygulamalarına ve çözümlerine genel bakış](../virtual-machines/workloads/oracle/oracle-overview.md)