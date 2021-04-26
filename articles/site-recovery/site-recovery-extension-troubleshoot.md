---
title: Azure Site Recovery ile olağanüstü durum kurtarma için Azure VM Uzantısı sorunlarını giderme
description: Azure Site Recovery ile olağanüstü durum kurtarma için Azure VM uzantısıyla ilgili sorunları giderin.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009713"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Azure VM uzantısıyla ilgili sorunları giderme

Bu makalede, VM Aracısı ve uzantısıyla ilgili Azure Site Recovery hatalarını çözmenize yardımcı olabilecek sorun giderme adımları sunulmaktadır.

## <a name="low-system-resources"></a>Düşük sistem kaynakları

Bu sorun, sistem kullanılabilir belleğin düşük olması durumunda oluşur ve Mobility hizmeti yüklemesi için bellek ayıramayabilir. Yüklemenin devam edebilmesi ve başarıyla tamamlanabilmesi için yeterli bellek boşaldığınızdan emin olun.

## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery uzantısı zaman aşımı  

Hata iletisi: "uzantı işleminin başlatılması izlenirken görev yürütme zaman aşımına uğradı"<br>
Hata kodu: "151076"

 Azure Site Recovery, korumayı etkinleştirme işinin bir parçası olarak sanal makineye bir uzantı yükledi. Aşağıdaki koşullardan herhangi biri, korumanın tetiklemesini engelleyebilir ve işin başarısız olmasına neden olabilir. Aşağıdaki sorun giderme adımlarını tamamlayıp işleminizi yeniden deneyin:

- [Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'Leri için)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 'de yüklü olan aracı güncel değil (Linux VM 'Leri için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery uzantısı güncelleştirilemiyor veya yüklenemiyor](#the-site-recovery-extension-fails-to-update-or-load)

Hata iletisi: "önceki Site Recovery uzantısı işlemi beklenenden daha uzun sürüyor."<br>
Hata kodu: "150066"

- [Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'Leri için)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 'de yüklü olan aracı güncel değil (Linux VM 'Leri için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery uzantısı durumu yanlış](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>VM Aracısı yanıt vermediği için koruma başarısız oluyor

Hata iletisi: "uzantı işleminin başlatılması izlenirken görev yürütme zaman aşımına uğradı."<br>
Hata kodu: "151099"

Sanal makinedeki Azure Konuk Aracısı, Ready durumunda değilse bu hata oluşabilir.

[Azure Portal](https://portal.azure.com/)Azure Konuk aracısının durumunu kontrol edebilirsiniz. Korumaya çalıştığınız sanal makineye gidin ve **VM**  >  **ayarları**  >  **Özellikler**  >  **Aracı durumu**' nda durumu denetleyin. Çoğu zaman, sanal makine yeniden başlatıldıktan sonra aracının durumu için de hazırlık yapılır. Bununla birlikte, yeniden başlatılamıyor veya sorunu yaşamaya devam ediyorsanız, aşağıdaki sorun giderme adımlarını izleyin:

- [Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'Leri için)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM 'de yüklü olan aracı güncel değil (Linux VM 'Leri için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Hata iletisi: "uzantı işleminin başlatılması izlenirken görev yürütme zaman aşımına uğradı."<br>
Hata kodu: "151095"

Bu hata, Linux makinesindeki aracı sürümü güncel olmadığında oluşur. Aşağıdaki sorun giderme adımını doldurun:

- [VM 'de yüklü olan aracı güncel değil (Linux VM 'Leri için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Nedenler ve çözümler

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'Leri için)

#### <a name="solution"></a>Çözüm
VM Aracısı bozulmuş olabilir veya hizmet durdurulmuş olabilir. VM Aracısı 'nı yeniden yüklemek en son sürümü almaya yardımcı olur. Hizmet ile iletişimin yeniden başlatılmasına de yardımcı olur.

1. Windows Azure Konuk Aracısı hizmetinin VM hizmetlerinde çalışıp çalışmadığını belirleme (Services. msc). Windows Azure Konuk Aracısı hizmetini yeniden başlatın.    
1. Hizmetler 'de Windows Azure Konuk Aracısı hizmeti görünmüyorsa, Denetim Masası 'nı açın. Windows Konuk Aracısı hizmetinin yüklü olup olmadığını görmek için **Programlar ve Özellikler '** e gidin.
1. Windows Azure Konuk Aracısı **Programlar ve Özellikler**' de görünürse, Microsoft Azure Konuk aracısını kaldırın.
1. [Aracı MSI ' nın en son sürümünü](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)indirip yükleyin. Yüklemeyi tamamlaması için yönetici haklarına sahip olmanız gerekir.
1. Windows Azure Konuk Aracısı hizmetinin hizmetler 'de göründüğünü doğrulayın.
1. Koruma işini yeniden başlatın.

Ayrıca, VM 'de [Microsoft .NET 4,5 ' nin yüklü](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) olduğunu doğrulayın. VM aracısının hizmetle iletişim kurması için .NET 4,5 gerekir.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 'de yüklü olan aracı güncel değil (Linux VM 'Leri için)

#### <a name="solution"></a>Çözüm
Linux VM 'Leri için aracıyla ilgili veya uzantı ile ilgili çoğu başarısızlık, süresi geçmiş bir VM aracısını etkileyen sorunlardan kaynaklanır. Bu sorunu gidermek için aşağıdaki genel yönergeleri izleyin:

1. [LINUX VM aracısını güncelleştirme](../virtual-machines/extensions/update-linux-agent.md)yönergelerini izleyin.

   > [!NOTE]
   > Aracıyı yalnızca bir dağıtım deposu aracılığıyla güncelleştirmenizi *önemle tavsiye* ederiz. Doğrudan GitHub 'dan aracı kodunu indirmenizi ve güncelleştirmeyi önermiyoruz. Dağıtım için en son aracı kullanılamıyorsa, nasıl yükleneceğine ilişkin yönergeler için dağıtım desteğiyle iletişim kurun. En son aracıyı denetlemek için GitHub deposundaki [Windows Azure Linux Aracısı](https://github.com/Azure/WALinuxAgent/releases) sayfasına gidin.

1. Aşağıdaki komutu çalıştırarak Azure aracısının VM üzerinde çalıştığından emin olun: `ps -e`

   İşlem çalışmıyorsa, aşağıdaki komutları kullanarak yeniden başlatın:

   - Ubuntu için: `service walinuxagent start`
   - Diğer dağıtımlar için: `service waagent start`

1. [Otomatik yeniden başlatma aracısını yapılandırın](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Sanal makinenin korunmasını etkinleştirin.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery uzantısı güncelleştirilemiyor veya yüklenemiyor

Uzantı durumu "boş", "NotReady" veya "geçiş" olarak gösterilir.

#### <a name="solution"></a>Çözüm

Uzantıyı kaldırın ve işlemi yeniden başlatın.

Uzantıyı kaldırmak için:

1. [Azure Portal](https://portal.azure.com/), yedekleme hatası yaşayan VM 'ye gidin.
1. **Ayarlar**'ı seçin.
1. **Uzantılar**'ı seçin.
1. **Site Recovery uzantısı**' nı seçin.
1. **Kaldır**'ı seçin.

Linux VM için, VMSnapshot uzantısı Azure portal gösterilmezse [Azure Linux aracısını güncelleştirin](../virtual-machines/extensions/update-linux-agent.md). Ardından korumayı çalıştırın.

Bu adımları tamamladığınızda, uzantının koruma sırasında yeniden yüklenmesine neden olur.
