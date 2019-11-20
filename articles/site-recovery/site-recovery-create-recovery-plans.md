---
title: Azure Site Recovery kurtarma planlarını oluşturma/özelleştirme
description: Azure Site Recovery hizmetini kullanarak olağanüstü durum kurtarma için kurtarma planları oluşturmayı ve özelleştirmeyi öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 9bb5a1a3aa0c2a4681ddecb5e20df41d481755ec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084518"
---
# <a name="create-and-customize-recovery-plans"></a>Kurtarma planlarını oluşturma ve özelleştirme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)' de bir kurtarma planının nasıl oluşturulacağı ve özelleştirileceği açıklanır. Başlamadan önce, kurtarma planları hakkında [daha fazla bilgi edinin](recovery-plan-overview.md) .

## <a name="create-a-recovery-plan"></a>Kurtarma planı oluşturma

1. Kurtarma Hizmetleri kasasında **kurtarma planları (Site Recovery)**  >  **+ kurtarma planı**' nı seçin.
2. **Kurtarma planı oluştur**' da plan için bir ad belirtin.
3. Plandaki makinelere göre bir kaynak ve hedef seçin ve dağıtım modeli için **Kaynak Yöneticisi** ' yi seçin. Kaynak konumu, yük devretme ve kurtarma için etkinleştirilen makinelere sahip olmalıdır. 

   **Yük devretme** | **Kaynak** | **Hedef** 
   --- | --- | ---
   Azure-Azure arası | Azure bölgesi |Azure bölgesi
   VMware 'den Azure 'a | Yapılandırma Sunucusu | Azure
   Fiziksel makineleri Azure 'a | Yapılandırma Sunucusu | Azure   
   VMM tarafından Azure 'a yönetilen Hyper-V  | VMM görünen adı | Azure
   VMM 'den Azure 'a Hyper-V | Hyper-V site adı | Azure
   VMM 'den VMM 'ye |VMM kolay adı | VMM görünen adı 

   > [!NOTE]
   > Bir kurtarma planı aynı kaynak ve hedefe sahip makineler içerebilir. VMM tarafından yönetilen VMware ve Hyper-V VM 'Leri aynı planda olamaz. VMware VM 'Leri ve fiziksel sunucular, kaynağın bir yapılandırma sunucusu olduğu planda olabilir.

2. **Öğe seçin sanal makineler**bölümünde, plana eklemek istediğiniz makineleri (veya çoğaltma grubunu) seçin. Daha sonra, **Tamam**'a tıklayın.
    - Makineler, planda varsayılan grup (Grup 1) olarak eklenir. Yük devretmeden sonra, bu gruptaki tüm makineler aynı anda başlatılır.
    - Yalnızca belirttiğiniz kaynak ve hedef konumlarda makineler seçebilirsiniz. 
1. Planı oluşturmak için **Tamam** ' ı tıklatın.

## <a name="add-a-group-to-a-plan"></a>Plana grup ekleme

Grup grubu temelinde farklı davranışlar belirleyebilmeniz için ek gruplar oluşturur ve farklı gruplara makineler eklersiniz. Örneğin, bir gruptaki makinelerin yük devretmeden sonra ne zaman başlaması gerektiğini belirtebilir veya grup başına özelleştirilmiş eylemleri belirtebilirsiniz.

1. **Kurtarma planları**' nda plana sağ tıklayın > **özelleştirin**. Varsayılan olarak, bir plan oluşturduktan sonra, ona eklediğiniz tüm makineler varsayılan Grup 1 ' de bulunur.
2. **+ Grup**' a tıklayın. Varsayılan olarak, yeni bir grup eklendiği sırada numaralandırılır. En fazla yedi grup olabilir.
3. Yeni gruba taşımak istediğiniz makineyi seçin, **grubu Değiştir**' e tıklayın ve ardından yeni Grup ' u seçin. Alternatif olarak, **korunan öğe**> Grup adına sağ tıklayın ve gruba makine ekleyin. Bir makine veya çoğaltma grubu, bir kurtarma planında yalnızca bir gruba ait olabilir.


## <a name="add-a-script-or-manual-action"></a>Betik veya el ile eylem ekleme

Bir komut dosyası veya el ile eylem ekleyerek bir kurtarma planını özelleştirebilirsiniz. Şunlara dikkat edin:

- Azure 'a çoğaltma yapıyorsanız, Azure Otomasyonu runbook 'larını kurtarma planınızla tümleştirebilirsiniz. [Daha fazla bilgi edinin](site-recovery-runbook-automation.md).
- System Center VMM tarafından yönetilen Hyper-V VM 'lerini çoğaltırken, şirket içi VMM sunucusunda bir betik oluşturabilir ve kurtarma planına ekleyebilirsiniz.
- Bir komut dosyası eklediğinizde, grup için yeni bir eylem kümesi ekler. Örneğin, Grup 1 için bir ön adım kümesi, ad *grubu 1 ile oluşturulur: ön adımlar*. Tüm ön adımlar bu küme içinde listelenmiştir. Birincil siteye yalnızca dağıtılan bir VMM sunucusu varsa, bir komut dosyası ekleyebilirsiniz.
- El ile eylem eklerseniz, kurtarma planı çalıştırıldığında, el ile gerçekleştirilen eylemi eklediğiniz noktada duraklar. Bir iletişim kutusu, el ile gerçekleştirilen eylemin tamamlandığını belirtmenizi ister.
- VMM sunucusunda bir betik oluşturmak için [Bu makaledeki](hyper-v-vmm-recovery-script.md)yönergeleri izleyin.
- Betikler ikincil siteye yük devretme sırasında ve ikincil siteden birincil konuma yeniden çalışma sırasında uygulanabilir. Destek, çoğaltma senaryonuza bağlıdır:
    
    **Senaryo** | **Yük devretme** | **İlk duruma döndürme**
    --- | --- | --- 
    Azure-Azure arası  | Runbook | Runbook
    Vmware’den Azure’a | Runbook | NA 
    VMM 'den Azure 'a Hyper-V | Runbook | Betik
    Azure'da Hyper-V sitesi | Runbook | NA
    VMM 'den ikincil VMM 'ye | Betik | Betik

1. Kurtarma planında, eylemin eklenmesi gereken adıma tıklayın ve eylemin ne zaman gerçekleşmesi gerektiğini belirtin:
    1. Eylemin yük devretmeden sonra gruptaki makineler başlatılmadan önce gerçekleşmesini istiyorsanız, **ön eylem Ekle**' yi seçin.
    1. İşlemin yük devretmeden sonra başlatılacak olan makineler sonrasında gerçekleşmesini istiyorsanız, **gönderme eylemi Ekle**' yi seçin. Eylemin konumunu taşımak için **Yukarı taşı** veya **aşağı taşı** düğmelerini seçin.
2. **Ekle eyleminde** **betik** veya **el ile eylem**' i seçin.
3. El ile eylem eklemek istiyorsanız, aşağıdakileri yapın:
    1. Eylem için bir ad yazın ve eylem yönergeleri yazın. Yük devretmeyi çalıştıran kişi bu yönergeleri görür.
    1. Tüm yük devretme türleri için el ile eylem eklemek isteyip istemediğinizi belirtin (test, yük devretme, planlı yük devretme (ilgiliyse)). Daha sonra, **Tamam**'a tıklayın.
4. Bir betik eklemek istiyorsanız, aşağıdakileri yapın:
    1. VMM betiği ekliyorsanız, **VMM betiğine yük devretme**' yı seçin ve **betik yolu** ' nda, paylaşımın göreli yolunu yazın. Örneğin, paylaşım \\\<VMMServerName > \MSSCVMMLibrary\RPScripts yolunda bulunuyorsa, şu yolu belirtin: \Rpscripts\rpscript.exe. ps1.
    1. Bir Azure Otomasyonu çalıştırma defteri ekliyorsanız, runbook 'un bulunduğu **Azure Otomasyonu hesabını** belirtin ve uygun **Azure runbook betiğini**seçin.
5. Komut dosyasının beklendiği gibi çalıştığından emin olmak için kurtarma planının test yük devretmesini çalıştırın.

## <a name="watch-a-video"></a>Bir video izleyin

Bir kurtarma planının nasıl oluşturulacağını gösteren bir video izleyin.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Sonraki adımlar

[Yük devretme çalıştırma](site-recovery-failover.md)hakkında daha fazla bilgi edinin.  

    
