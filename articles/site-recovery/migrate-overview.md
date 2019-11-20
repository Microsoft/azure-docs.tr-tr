---
title: Azure Site Recovery ile sunucu ve VM 'Leri Azure 'a geçirme
description: Şirket içi ve Azure IaaS VM 'lerinin Azure Site Recovery hizmeti kullanılarak Azure 'a nasıl geçirileceği açıklanmaktadır.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 8e256aac16bb8c2d2f1eca494981458f71cc2e4d
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620609"
---
# <a name="about-migration"></a>Geçiş hakkında

[Azure Site Recovery](site-recovery-overview.md) hizmetinin makineleri geçirmenize nasıl yardımcı olduğunu kısaca öğrenmek için bu makaleyi okuyun. 

Site Recovery kullanarak geçirebileceğiniz özellikler şunlardır:

- Şirket **Içinden Azure 'A geçiş**: şirket içi Hyper-V VM 'Lerini, VMware VM 'lerini ve fiziksel sunucuları Azure 'a geçirin. Geçişten sonra şirket içi makinelerde çalışan iş yükleri Azure VM'ler üzerinde çalışacaktır. 
- **Azure içinde geçiş**: Azure VM’lerini bir Azure bölgesinden diğerine geçirin. 
- **AWS geçirme**: AWS Windows örneklerini Azure IaaS’ye geçirin. 

> [!NOTE]
> Artık Azure geçişi hizmetini kullanarak şirket içinden Azure 'a geçiş yapabilirsiniz. [Daha fazla bilgi edinin](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Geçiş ile kast edilen nedir?

Şirket içi ve Azure VM 'lerinin olağanüstü durum kurtarması için Site Recovery kullanmanın yanı sıra, Site Recovery hizmetini kullanarak bunları geçirebilirsiniz. Fark nedir?

- Olağanüstü durum kurtarma için, makineleri düzenli olarak Azure 'a çoğaltmanız gerekir. Bir kesinti oluştuğunda, makineleri birincil siteden ikincil Azure sitesine devreder ve oradan erişebilirsiniz. Birincil site yeniden kullanılabilir olduğunda, Azure 'dan yeniden yük devreolursunuz.
- Geçiş için şirket içi makineleri Azure 'a veya Azure VM 'lerini ikincil bir bölgeye çoğaltmanız gerekir. Daha sonra VM 'yi birincil siteden ikinciye devreder ve geçiş işlemini tamamlayabilirsiniz. Yeniden çalışma işlemi yapılmaz.  


## <a name="migration-scenarios"></a>Geçiş senaryoları

**Senaryo** | **Ayrıntılar**
--- | ---
**Şirket içinden Azure 'a geçiş** | Şirket içi VMware VM 'Leri, Hyper-V VM 'Leri ve fiziksel sunucuları Azure 'a geçirebilirsiniz. Bunu yapmak için tam olağanüstü durum kurtarma için yaptığınız adımlarla neredeyse aynı adımları tamamlamayın. Yalnızca Azure 'dan şirket içi siteye geri yük devredemezsiniz.
**Azure bölgeleri arasında geçiş yapma** | Azure VM 'lerinden bir Azure bölgesinden diğerine geçiş yapabilirsiniz. Geçiş işlemi tamamlandıktan sonra, yalnızca geçirdiğiniz ikincil bölgede Azure VM 'Leri için olağanüstü durum kurtarmayı yapılandırabilirsiniz.
**AWS’yi Azure'a geçirme** | AWS örneklerini Azure VM’lerine geçirebilirsiniz. Site Recovery, AWS örneklerine geçiş amacıyla fiziksel sunucu olarak davranır. 

## <a name="next-steps"></a>Sonraki adımlar

- [Şirket içi makineleri Azure’a geçirme](migrate-tutorial-on-premises-azure.md)
- [VM’leri bir Azure bölgesinden diğerine geçirme](azure-to-azure-tutorial-migrate.md)
- [AWS’yi Azure'a geçirme](migrate-tutorial-aws-azure.md)
