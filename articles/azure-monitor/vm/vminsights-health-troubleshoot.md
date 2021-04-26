---
title: VM Insights Konuk durumu sorunlarını giderme (Önizleme)
description: VM öngörüleri sistem durumu ile ilgili sorunlar yaşıyorsanız gerçekleştirebileceğiniz sorun giderme adımlarını açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932786"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>VM Insights Konuk durumu sorunlarını giderme (Önizleme)
Bu makalede, VM Insights sistem durumu ile ilgili sorun yaşıyorsanız gerçekleştirebileceğiniz sorun giderme adımları açıklanmaktadır.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Yükseltme kullanılabilir iletisi, Konuk sistem durumu yükseltildikten sonra hala görüntülenir 

- VM 'nin küresel Azure 'da çalıştığını doğrulayın. Yay etkin sunucular henüz desteklenmiyor.
- Sanal makinenin bölgesinin ve işletim sistemi sürümünün desteklenen [VM'ler için Azure izleyici Konuk sistem durumunu etkinleştir (Önizleme)](vminsights-health-enable.md)bölümünde açıklandığı şekilde desteklendiğini doğrulayın.
- Konuk sistem durumu uzantısının 0 çıkış koduyla başarıyla yüklendiğini doğrulayın.
- Azure Izleyici Aracısı uzantısının başarıyla yüklendiğini doğrulayın.
- Sanal makine için sistem tarafından atanan yönetilen kimliğin etkinleştirildiğini doğrulayın.
- Sanal makine için Kullanıcı tarafından atanan yönetilen kimliklerin belirtilmemiş olduğunu doğrulayın.
- Yerel ayar *ABD İngilizcesi* olan Windows sanal makinelerinin doğrulanması. Yerelleştirme Şu anda Azure Izleyici Aracısı tarafından desteklenmiyor.
- Sanal makinenin ağ ara sunucusunu kullanmıyor olduğunu doğrulayın. Azure Izleyici Aracısı Şu anda proxy 'leri desteklemiyor.
- Sistem durumu uzantısı aracısının hatasız başlatıldığını doğrulayın. Aracı başlatılamazsa, aracının durumu bozulmuş olabilir. Aracı durumu klasörünün içeriğini silin ve aracıyı yeniden başlatın.
  - Linux için: Daemon, *Vmguesthealthagent*' dir. Durum klasörü */var/seçenek/vmguesthealthagent/**
  - Windows için: hizmet *VM Konuk sistem durumu aracısıdır*. Durum klasörü _%ProgramData%\microsoft\vmguesthealthagent \\ *_.
- Azure Izleyici aracısının ağ bağlantısı olduğunu doğrulayın. 
  - Sanal makineden, PING _<region> . Handler.Control.Monitor.Azure.com_' yi deneyin. Örneğin, westeurope içindeki bir sanal makine için, _westeurope.Handler.Control.Monitor.Azure.com:443_ ping yapmayı deneyin.
- Sanal makinenin, Log Analytics çalışma alanıyla aynı bölgedeki bir veri toplama kuralıyla bir ilişkisi olduğunu doğrulayın.
  -  DCR yapısının doğru olduğundan emin olmak için [VM'ler için Azure izleyici Konuk sistem durumunu etkinleştir (Önizleme)](vminsights-health-enable.md) bölümünde **veri toplama kuralı (DCR) oluşturma** bölümüne bakın. Durum Uzantısı Yapılandırması ' nda bulunan ve uzantıya sayaç göndermek için sistem durumu uzantısı yapılandırma bölümünde üç sayaç ve *InputDataSources* bölümü ile ilgili dikkat *çekici bir durum* belirleyin.
-  Konuk sistem durumu uzantısı hataları için sanal makineyi kontrol edin.
   -  Linux için: _/var/log/Azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*. log_ konumundaki günlükleri denetleyin.
   -  Windows için: _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent \{ Uzantısı sürümü} \* . log_ konumundaki günlükleri denetleyin.
-  Azure Izleyici Aracısı hataları için sanal makineyi kontrol edin.
   -  Linux için: _/var/log/MDSD. *_ konumundaki günlükleri denetleyin.
   -  Windows için: _C:\windowsazure\resources \* {VMName} konumundaki günlükleri kontrol edin. AMADataStore_.
 



## <a name="error-message-that-no-data-is-available"></a>Kullanılabilir veri yok iletisi 

![Veri yok](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Sanal makinenin yapılandırma gereksinimlerini karşıladığını doğrulayın

1. Sanal makinenin bir Azure sanal makinesi olduğunu doğrulayın. Sunucular için Azure Arc şu anda desteklenmemektedir.
2. Sanal makinede [desteklenen bir işletim sisteminin](vminsights-health-enable.md?current-limitations.md) çalıştığını doğrulayın.
3. Sanal makinenin [desteklenen bölgelerden birinde](vminsights-health-enable.md?current-limitations.md) bulunduğunu doğrulayın.
4. Log Analytics çalışma alanının [desteklenen bölgelerden birinde](vminsights-health-enable.md?current-limitations.md) bulunduğunu doğrulayın.

### <a name="verify-that-the-vm-is-properly-onboarded"></a>VM 'nin düzgün şekilde eklendi olduğunu doğrulama
Azure Izleyici Aracısı uzantısının ve konuk VM sistem durumu aracısının sanal makinede başarıyla sağlandığını doğrulayın. Azure portal sanal makinenin menüsünden **Uzantılar** ' ı seçin ve iki aracının listelendiğinden emin olun.

![VM uzantıları](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Sanal makinede sistem tarafından atanan kimliğin etkinleştirildiğini doğrulayın
Sistem tarafından atanan kimliğin sanal makinede etkinleştirildiğini doğrulayın. Azure portal sanal makinenin menüsünden **kimlik** ' i seçin. Kullanıcı tarafından yönetilen kimlik etkinleştirilirse, sistem tarafından yönetilen kimliğin durumu ne olursa olsun, Azure Izleyici Aracısı Yapılandırma hizmetiyle iletişim kuramaz ve konuk sistem durumu uzantısı çalışmaz.

![Sistem tarafından atanan kimlik](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Veri toplama kuralını doğrula
Veri kaynağı olarak sistem durumu uzantısı belirten veri toplama kuralının sanal makineyle ilişkili olduğunu doğrulayın.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Yetersiz izinler nedeniyle hatalı istek için hata iletisi
Bu hata, **Microsoft. WorkloadMonitor** kaynak sağlayıcısı 'nın abonelikte kayıtlı olmadığını gösterir. Bu kaynak sağlayıcıyı kaydetme hakkındaki ayrıntılar için bkz. [Azure kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Hatalı istek](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>Konuk sistem durumu etkinleştirildikten sonra sistem durumu "bilinmiyor" olarak gösterilir.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Windows düğümlerinde performans sayaçlarının doğru şekilde çalıştığını doğrulama 
Konuk sistem durumu, kümeden performans sayaçlarını toplayabilmekte olan aracıyı kullanır. temel performans sayacı kitaplıkları kümesi bozulmuş olabilir ve yeniden oluşturulması gerekebilir. Performans sayaçlarını yeniden derlemek için [performans sayacı kitaplık değerlerini el ile yeniden oluşturma](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) bölümündeki yönergeleri izleyin.





## <a name="next-steps"></a>Sonraki adımlar

- [VM öngörülerinin Konuk sistem durumu özelliğine bir genel bakış elde edin](vminsights-health-overview.md)