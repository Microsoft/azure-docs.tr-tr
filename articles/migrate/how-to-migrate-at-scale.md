---
title: Çok sayıda Vm'lerini azure'a geçişini otomatikleştirmek | Microsoft Docs
description: Çok sayıda Azure Site Recovery kullanarak sanal makineleri geçirmek için komut dosyaları kullanmayı açıklar
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: b45a158569b3be8250728293c1bf73c1a860a0f6
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67808015"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Azure Site Recovery kullanarak bir VM ölçek geçiş

Bu makalede Azure Site RECOVERY'yi kullanarak VM'lerin çok sayıda geçirmek için betikleri kullanma anlamanıza yardımcı olur. Bu komut dosyalarını, yükleme için kullanılabilir [Azure PowerShell örnekleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) github deposu. Betikler, VMware, AWS, GCP Vm'leri ve fiziksel sunucuları azure'da yönetilen disklere geçirmek için kullanılabilir. Bu betikler, fiziksel sunucuları olarak sanal makineleri geçiriyorsanız Hyper-V sanal makineleri geçirmek için de kullanabilirsiniz. Azure Site Recovery PowerShell yararlanan komut dosyalarını belgelenmiştir [burada](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Geçerli sınırlamalar:
- ' % S'hedef sanal makine, yalnızca birincil NIC için statik IP adresi belirtme desteği
- Azure hibrit avantajı ilgili betikleri almayan girişlerinin portalında çoğaltılan sanal makinenin özelliklerini el ile güncelleştirmeniz gerekir

## <a name="how-does-it-work"></a>Nasıl çalışır?

### <a name="prerequisites"></a>Önkoşullar
Başlamadan önce aşağıdakileri yapmanız gerekir:
- Site Recovery kasası, Azure aboneliğinizde oluşturduğunuzdan emin olun
- Yapılandırma sunucusu ve işlem sunucusu kaynak ortamda yüklü olan ve kasa ortamı bulabilir olduğundan emin olun
- Bir çoğaltma ilkesi oluşturuldu ve yapılandırma sunucusu ile ilişkili emin olun
- (Bu, şirket içi sanal makineleri çoğaltmak için kullanılacak) yapılandırma sunucusu sanal makine yönetici hesabı eklediğinizden emin olun
- Azure'da hedef yapıtları oluşturduğunuzdan emin olun
    - Hedef kaynak grubu
    - Hedef depolama hesabı (ve kaynak grubu) - premium yönetilen disklere geçirme planlıyorsanız, bir premium depolama hesabı oluşturma
    - Önbellek depolama hesabına (ve kaynak grubu) - kasa ile aynı bölgede standart depolama hesabı oluşturma
    - Hedef yük devretme için sanal ağ (ve kaynak grubu)
    - Hedef alt ağ
    - Hedef yük devretme testi için sanal ağ (ve kaynak grubu)
    - Kullanılabilirlik kümesi (gerekirse)
    - Hedef ağ güvenlik grubu ve kaynak grubu
- ' % S'hedef sanal makine özellikleri üzerinde karar verdiğiniz emin olun.
    - Hedef VM adı
    - Hedef VM boyutu azure'da (Azure geçişi değerlendirmesi'ni kullanarak karar)
    - Birincil NIC VM'deki özel IP adresi
- Betiklerin indirme [Azure PowerShell örnekleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) github deposu

### <a name="csv-input-file"></a>CSV giriş dosyası
Tamamlanan tüm ön koşullar oluşturduktan sonra geçirmek istediğiniz her bir kaynak makine için verileri içeren bir CSV dosyası oluşturmanız gerekir. ' % S'giriş CSV giriş ayrıntılarını içeren bir üst bilgi satırı ve geçirilmesi gereken her makineye ilişkin ayrıntılı bir satır olması gerekir. Tüm betikler, aynı CSV dosyası üzerinde çalışacak şekilde tasarlanmıştır. Örnek bir CSV şablonu scripts klasörü başvuru amacıyla kullanılabilir.

### <a name="script-execution"></a>Betik yürütme
CSV hazır hale geldikten sonra şirket içi sanal makineleri geçirmek için aşağıdaki adımları yürütebilirsiniz:

**Adım #** | **Betik adı** | **Açıklama**
--- | --- | ---
1\. | asr_startmigration.ps1 | Tüm sanal makineler için çoğaltmayı etkinleştirme csv dosyasında listelenen, betik iş ayrıntılarını CSV çıkışında, her VM için oluşturur.
2 | asr_replicationstatus.ps1 | Çoğaltma durumunu, betik durumu ile bir csv, her VM için oluşturur.
3 | asr_updateproperties.ps1 | Çoğaltılan ve korumalı Vm'leri olduktan sonra VM (işlem ve ağ özellikleri) hedef özelliklerini güncelleştirmek için bu betiği kullanın
4 | asr_propertiescheck.ps1 | Özellikleri uygun şekilde güncelleştirilir olmadığını doğrulayın
5 | asr_testmigration.ps1 |  Csv dosyasında listelenen sanal makinelerinin yük devretme testi başlatmak için betik iş ayrıntılarını CSV çıkışında, her VM için oluşturur.
6 | asr_cleanuptestmigration.ps1 | El ile doğrulama sonra başarısız olan Vm'leri test, test yük devretmesi Vm'leri temizlemek için bu betiği kullanın
7 | asr_migration.ps1 | Csv dosyasında listelenen VM'ler planlanmamış yük devretme gerçekleştirmek, betiği bir CSV çıkışında iş ayrıntılarını ile her VM için oluşturur. Betik bilgisayar şirket içi Vm'leri uygulama tutarlılığı için yük devretmeyi tetiklemeden önce tavsiye edilir betiği çalıştırmadan önce Vm'lerini el ile kapatın.
8 | asr_completemigration.ps1 | Vm'lerde yürütme işlemini gerçekleştirmek ve Azure Site Recovery varlıklarını silme
9 | asr_postmigration.ps1 | NIC yük devretmesi için ağ güvenlik grupları atamayı düşünüyorsanız, bunu yapmak için bu betiği kullanabilirsiniz. ' % S'hedef sanal makine olarak herhangi bir NIC bir NSG atar.

## <a name="how-to-migrate-to-managed-disks"></a>Yönetilen disklere geçirmek nasıl?
Betik varsayılan olarak, Azure yönetilen diskler için sanal makineleri geçirir. Sağlanan hedef depolama hesabı bir premium depolama hesabı, premium yönetilen diskler geçiş sonrası oluşturulur. Önbellek depolama hesabı, standart bir hesap olarak yine de olabilir. Standart diskler, hedef depolama hesabı bir standart depolama hesabı ise, geçiş sonrasında oluşturulur. 

## <a name="next-steps"></a>Sonraki adımlar

[Daha fazla bilgi edinin](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) Azure Site Recovery kullanarak azure'a geçirme hakkında sunucuları
