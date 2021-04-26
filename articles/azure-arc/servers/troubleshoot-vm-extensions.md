---
title: Azure Arc etkin sunucuları VM Uzantısı sorunlarını giderme
description: Bu makalede, Azure Arc etkin sunucularıyla oluşan Azure VM uzantıları ile ilgili sorunların nasıl giderileceği ve çözüleceği açıklanır.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584677"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Yay etkin sunucu sanal makine uzantısı sorunlarını giderme

Bu makalede, Arc etkin sunuculardaki Azure VM uzantıları 'nı dağıtmaya veya kaldırmaya çalışırken oluşabilecek sorunları giderme ve çözme hakkında bilgi sağlanır. Genel bilgiler için bkz. [Azure VM uzantılarını yönetme ve kullanma](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Genel sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal alınabilir.

Aşağıdaki sorun giderme adımları tüm VM uzantıları için geçerlidir.

1. Konuk Aracısı günlüğünü denetlemek için, uzantınızın `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` Windows için ve altında Linux için sağlanması durumunda etkinliğe bakın `/var/lib/GuestConfig/ext_mgr_logs` .

2. Windows hakkında daha fazla ayrıntı için belirli bir uzantının uzantı günlüklerini denetleyin `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . Uzantı çıkışı, Linux üzerinde yüklü her uzantı için bir dosyaya kaydedilir `/var/lib/GuestConfig/extension_logs` .

3. Hata kodları, bilinen sorunlar vb. için uzantıya özgü belge sorunlarını giderme bölümüne bakın. Her uzantı için ek sorun giderme bilgileri, uzantının genel bakış konusunun **sorun giderme ve destek** bölümünde bulunabilir. Bu, günlüğe yazılan hata kodlarının açıklamasını içerir. Uzantı makaleleri [Uzantılar tablosuna](manage-vm-extensions.md#extensions)bağlanır.

4. Sistem günlüklerine bakın. Uzantıya sahip olabilecek, Özel Paket Yöneticisi erişimi gerektiren başka bir uygulamanın uzun süre çalışan yüklemesi gibi diğer işlemleri denetleyin.

## <a name="troubleshooting-specific-extension-scenarios"></a>Belirli uzantı senaryolarında sorun giderme

### <a name="vm-insights"></a>VM öngörüleri

- Azure yay etkin bir sunucu için VM öngörülerini etkinleştirirken, bağımlılığı ve Log Analytics aracısını yükleyecek. Yavaş bir makinede veya yavaş ağ bağlantısıyla, yükleme işlemi sırasında zaman aşımlarını görmek mümkündür. Microsoft, bu durumun artırılmasına yardımcı olmak için bağlı makine aracısında bunu ele almak için gereken adımları ele geçiriyor. Bu arada, yükleme işlemi başarılı olabilir.

### <a name="log-analytics-agent-for-linux"></a>Linux için Log Analytics Aracısı

- Log Analytics Agent sürümü 1.13.9 (karşılık gelen uzantı sürümü 1.13.15), yüklenen verileri Azure Arc etkin sunucusunun kaynak KIMLIĞIYLE doğru şekilde işaretlemez. Günlükler hizmete gönderiliyor olsa da, **günlükleri** veya **öngörüleri** seçtikten sonra seçili etkin sunucudan verileri görüntülemeye çalıştığınızda hiçbir veri döndürülmez. Azure Izleyici günlüklerinden veya çalışma alanı kapsamındaki VM'ler için Azure İzleyici 'ten sorgular çalıştırarak verilerini görüntüleyebilirsiniz.

- Bazı dağıtımlar, Linux için Log Analytics Aracısı tarafından şu anda desteklenmemektedir. Aracı, Python 2 dahil olmak üzere ek bağımlılıkların yüklenmesini gerektirir. Destek matrisini ve önkoşulları [burada](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)gözden geçirin.

- Durum iletisinde 52 hata kodu eksik bağımlılığı gösterir. Hangi bağımlılığın eksik olduğu hakkında daha fazla bilgi için çıktıyı ve günlükleri kontrol edin.

- Yükleme başarısız olursa, uzantıya genel bakış konusunun **sorun giderme ve destek** bölümüne bakın. Çoğu durumda, durum iletisine eklenen bir hata kodu vardır. Linux için Log Analytics Aracısı için, bu VM uzantısı için genel sorun giderme bilgileriyle birlikte durum iletileri [burada](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)açıklanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

- [Microsoft Q&A](/answers/topics/azure-arc.html)aracılığıyla Azure uzmanlarından yanıtlar alın.

- [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.

- Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
