---
title: Windows Server 2003 sunucularını Azure geçişi ile geçiş için hazırlama
description: Windows Server 2003 sunucularının Azure geçişi ile geçiş için nasıl hazırlanacağını öğrenin.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753765"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Windows Server 2003 makinelerini geçiş için hazırlama

Bu makalede, Windows Server 2003 çalıştıran makinelerin Azure 'a geçiş için nasıl hazırlanacağı açıklanmaktadır. 


> [!NOTE]
> [Windows Server 2003 genişletilmiş desteği](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) 14 Temmuz 2015 tarihinde sona erdi.  Azure destek ekibi, Azure 'da Windows Server 2003 ' i çalıştırmaya yönelik sorunları gidermeye yardımcı olmaya devam etmektedir. Ancak, bu destek, işletim sistemi düzeyinde sorun giderme veya düzeltme eki gerektirmeyen sorunlarla sınırlıdır. Uygulamalarınızı Windows Server 'ın daha yeni bir sürümünü çalıştıran Azure örneklerine geçirmek, Azure bulutunun esnekliğini ve güvenilirliğini etkili bir şekilde kullandığınızdan emin olmak için önerilen yaklaşımdır. Ancak, Windows Server 2003 ' ı Azure 'a geçirmeyi hala seçerseniz, Windows sunucunuz VMware veya Hyper-V üzerinde çalışan bir sanal makine ise Azure geçişi: sunucu geçiş aracı ' nı kullanabilirsiniz.


- [Hyper-V VM](tutorial-migrate-hyper-v.md) 'Lerini ve [VMware VM](tutorial-migrate-vmware.md) 'lerini Azure 'a geçirmek için aracısız geçiş kullanabilirsiniz.
- Geçişten sonra Azure VM 'lerine bağlanmak için, Azure VM 'de Hyper-V tümleştirme hizmetlerinin yüklü olması gerekir. Windows Server 2003 makineler bu varsayılan olarak yüklü değildir.
- Hyper-V tümleştirme hizmetlerini yüklemek için doğrudan indirme bağlantısı yoktur, bu nedenle şunları yapmanız gerekir:
    - Yüklü olmayan Hyper-V VM 'Leri için, Hyper-V rolüyle Windows Server 2012 R2/Windows Server 2012 çalıştıran bir makineye Tümleştirme Hizmetleri için yükleme dosyalarını ayıkladıktan sonra yükleyiciyi Windows Server 2003 makinesine kopyalamanız gerekir. Yükleme dosyaları Windows Server 2016 çalıştıran makinelerde kullanılamaz.
    - VMware VM 'Leri için, Azure VM geçişten sonra başlatıldığında tümleştirme hizmetlerini yükleyen bir başlangıç görevi oluşturursunuz.


## <a name="install-on-hyper-v-vms"></a>Hyper-V VM 'lerine yüklemesi

Geçişten önce, Hyper-V tümleştirme hizmetlerinin yüklü olup olmadığını denetleyin ve gerekirse yükleme yapın.

1. Yüklü olup olmadığını denetlemek için [Bu yönergeleri](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) izleyin.
2. Yüklü değilse, Hyper-V rolüyle Windows Server 2012 R2/Windows Server 2012 çalıştıran bir makinede oturum açın.
3. **C:\windows\system32\vmguest.exe** konumundaki yükleme dosyasına gidin ve dosyayı bağlayın.
2. Yükleme klasörünü Windows Server 2003 makinesine kopyalayın ve Tümleştirme Hizmetleri 'ni yükleme.
4. Yükleme sonrasında, Tümleştirme Hizmetleri ' nde varsayılan ayarları bırakabilirsiniz. 

## <a name="install-on-vmware-vms"></a>VMware VM 'lerine yüklemesi

1. Hyper-V rolüyle Windows Server 2012 R2/Windows Server 2012 çalıştıran bir makinede oturum açın.
2. **C:\windows\system32\vmguest.exe** konumundaki yükleme dosyasına gidin ve dosyayı bağlayın.
3. Yükleme klasörünü VMware VM 'ye kopyalayın.
4. VM 'deki komut satırından komutunu çalıştırın ```gpedit.msc``` .
5. **Bilgisayar yapılandırması**  >  **Windows ayarları**  >  **betikleri (başlatma/kapatır)** öğesini açın.
6. **Başlangıç**  >    >  **betik adı** Ekle ' de setup.exe adresini yazın.
7. Azure 'a geçişten sonra betik, Azure VM ilk kez başlatıldığında çalışır.
8. Azure VM 'yi el ile yeniden başlatın. Önyükleme tanılamasında bir yeniden başlatmanın gerekli olduğunu belirten bir açılır pencere vardır.
9. Betiği çalıştıktan ve Azure VM 'de Hyper-V Tümleştirme Hizmetleri yüklendikten sonra, betiği başlangıçtan kaldırabilirsiniz.
10. Yükleme sonrasında, Tümleştirme Hizmetleri ' nde varsayılan ayarları bırakabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

- [VMware](migrate-support-matrix-vmware-migration.md) ve [Hyper-V](migrate-support-matrix-hyper-v-migration.md) VM 'leri için geçiş gereksinimlerini gözden geçirin.
- [VMware](server-migrate-overview.md) ve [Hyper-V](tutorial-migrate-hyper-v.md) VM 'lerini geçirin.
