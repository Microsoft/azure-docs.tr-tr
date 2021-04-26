---
title: Azure Site Recovery kurtarma planlarını oluşturma/özelleştirme
description: Azure Site Recovery hizmetini kullanarak olağanüstü durum kurtarma için kurtarma planları oluşturmayı ve özelleştirmeyi öğrenin.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009730"
---
# <a name="create-and-customize-recovery-plans"></a>Kurtarma planlarını oluşturma ve özelleştirme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)'de yük devretme için bir kurtarma planı oluşturma ve özelleştirme açıklanır. Başlamadan önce, kurtarma planları hakkında [daha fazla bilgi edinin](recovery-plan-overview.md) .

## <a name="create-a-recovery-plan"></a>Kurtarma planı oluşturma

1. Kurtarma Hizmetleri kasasında **kurtarma planları (Site Recovery)**  >  **+ kurtarma planı**' nı seçin.
2. **Kurtarma planı oluştur**' da plan için bir ad belirtin.
3. Plandaki makinelere göre bir kaynak ve hedef seçin ve dağıtım modeli için **Kaynak Yöneticisi** ' yi seçin. Kaynak konumu, yük devretme ve kurtarma için etkinleştirilen makinelere sahip olmalıdır. 

    **Yük devretme** | **Kaynak** | **Hedef** 
   --- | --- | ---
   Azure-Azure arası | Azure bölgesini seçin | Azure bölgesini seçin
   VMware 'den Azure 'a | Yapılandırma sunucusunu seçin | Azure 'ı seçin
   Fiziksel makineleri Azure 'a | Yapılandırma sunucusunu seçin | Azure 'ı seçin   
   Hyper-V’den Azure’a | Hyper-V site adını seçin | Azure 'ı seçin
   Hyper-V (VMM tarafından yönetilen) Azure 'a  | VMM sunucusunu seçin | Azure 'ı seçin
  
    Şunlara dikkat edin:
    - Azure 'a yük devretme ve Azure 'dan yeniden çalışma için bir kurtarma planı kullanabilirsiniz.
    - Kaynak konumu, yük devretme ve kurtarma için etkinleştirilen makinelere sahip olmalıdır.
    - Bir kurtarma planı aynı kaynak ve hedefe sahip makineler içerebilir.
    - VMM tarafından yönetilen VMware VM 'leri ve Hyper-V VM 'lerini aynı planda ekleyebilirsiniz.
    - VMware VM 'Leri ve fiziksel sunucuları aynı planda olabilir.

4. **Öğe seçin sanal makineler** bölümünde, plana eklemek istediğiniz makineleri (veya çoğaltma grubunu) seçin. Daha sonra, **Tamam**'a tıklayın.
    - Makineler, planda varsayılan grup (Grup 1) olarak eklenir. Yük devretmeden sonra, bu gruptaki tüm makineler aynı anda başlatılır.
    - Yalnızca belirttiğiniz kaynak ve hedef konumlarda makineler seçebilirsiniz. 
5. Planı oluşturmak için **Tamam** ' ı tıklatın.

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
    
    **Senaryo** | **Yük devretme** | **Döndürülmesini**
    --- | --- | --- 
    Azure-Azure arası  | Runbook | Runbook
    Vmware’den Azure’a | Runbook | NA 
    VMM 'den Azure 'a Hyper-V | Runbook | Komut Dosyası
    Azure'da Hyper-V sitesi | Runbook | NA
    VMM 'den ikincil VMM 'ye | Komut Dosyası | Komut Dosyası

1. Kurtarma planında, eylemin eklenmesi gereken adıma tıklayın ve eylemin ne zaman gerçekleşmesi gerektiğini belirtin:
    1. Eylemin yük devretmeden sonra gruptaki makineler başlatılmadan önce gerçekleşmesini istiyorsanız, **ön eylem Ekle**' yi seçin.
    1. İşlemin yük devretmeden sonra başlatılacak olan makineler sonrasında gerçekleşmesini istiyorsanız, **gönderme eylemi Ekle**' yi seçin. Eylemin konumunu taşımak için **Yukarı taşı** veya **aşağı taşı** düğmelerini seçin.
2. **Ekle eyleminde** **betik** veya **el ile eylem**' i seçin.
3. El ile eylem eklemek istiyorsanız, aşağıdakileri yapın:
    1. Eylem için bir ad yazın ve eylem yönergeleri yazın. Yük devretmeyi çalıştıran kişi bu yönergeleri görür.
    1. Tüm yük devretme türleri için el ile eylem eklemek isteyip istemediğinizi belirtin (test, yük devretme, planlı yük devretme (ilgiliyse)). Daha sonra, **Tamam**'a tıklayın.
4. Bir betik eklemek istiyorsanız, aşağıdakileri yapın:
    1. VMM betiği ekliyorsanız, **VMM betiğine yük devretme**' yı seçin ve **betik yolu** ' nda, paylaşımın göreli yolunu yazın. Örneğin, paylaşma \\ \<VMMServerName> \Msscvmmlibrary\rpscripts dizininde bulunuyorsa, yolu belirtin: \RPScripts\RPScript.PS1.
    1. Bir Azure Otomasyonu çalıştırma defteri ekliyorsanız, runbook 'un bulunduğu **Azure Otomasyonu hesabını** belirtin ve uygun **Azure runbook betiğini** seçin.
5. Komut dosyasının beklendiği gibi çalıştığından emin olmak için kurtarma planının test yük devretmesini çalıştırın.

## <a name="watch-a-video"></a>Nasıl yapılacağını görmek için

Bir kurtarma planının nasıl oluşturulacağını gösteren bir video izleyin.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Sonraki adımlar

[Yük devretme çalıştırma](site-recovery-failover.md)hakkında daha fazla bilgi edinin.  

    
