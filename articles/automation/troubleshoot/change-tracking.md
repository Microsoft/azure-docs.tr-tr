---
title: Azure Otomasyonu Değişiklik İzleme ve envanter sorunlarını giderme
description: Bu makalede, Azure Otomasyonu Değişiklik İzleme ve envanter özelliği ile ilgili sorunların nasıl giderileceği ve çözüleceği açıklanır.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: dd027f94edad580836f0afb8c7293c81ca77605a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723835"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Değişiklik İzleme ve Stok özelliği sorunlarını giderme

Bu makalede, Azure Otomasyonu Değişiklik İzleme ve envanter sorunlarını giderme ve çözme işlemleri açıklanmaktadır. Değişiklik İzleme ve envanter hakkında genel bilgi için bkz. [değişiklik izleme ve envantere genel bakış](../change-tracking/overview.md).

## <a name="general-errors"></a>Genel hatalar

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Senaryo: makine farklı bir hesaba zaten kayıtlı

### <a name="issue"></a>Sorun

Aşağıdaki hata iletisini alırsınız:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Nedeni

Makine Değişiklik İzleme için zaten başka bir çalışma alanına dağıtıldı.

### <a name="resolution"></a>Çözüm

1. Makinenizin doğru çalışma alanına bildirimde bulunduğundan emin olun. Bunun nasıl doğrulanbileceğine ilişkin yönergeler için bkz. [Azure izleyici ile aracı bağlantısını doğrulama](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Ayrıca, bu çalışma alanının Azure Otomasyonu hesabınıza bağlı olduğundan emin olun. Onaylamak için Otomasyon hesabınıza gidin ve **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

1. Makinelerin Otomasyon hesabınıza bağlı Log Analytics çalışma alanında göründüğünden emin olun. Log Analytics çalışma alanında aşağıdaki sorguyu çalıştırın.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Makinenizi sorgu sonuçlarında görmüyorsanız, son zamanlarda iade edilmedi. Büyük olasılıkla yerel bir yapılandırma sorunu var. Log Analytics aracısını yeniden yüklemeniz gerekir.

   Makineniz sorgu sonuçlarında listelenirse, çözüm özelliği altında, **şifre** listesi ' nin listelendiğini doğrulayın. Bu, Değişiklik İzleme ve envantere kaydedilmiş olduğunu doğrular. Değilse, kapsam yapılandırma sorunlarını kontrol edin. Kapsam yapılandırması, hangi makinelerin Değişiklik İzleme ve envanter için yapılandırıldığını belirler. Hedef makinenin kapsam yapılandırmasını yapılandırmak için bkz. [Otomasyon hesabından değişiklik izleme ve envanteri etkinleştirme](../change-tracking/enable-from-automation-account.md).

   Çalışma alanınızda bu sorguyu çalıştırın.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Bir ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` sonuç alırsanız, çalışma alanınızda tanımlanan kotaya ulaşıldı ve bu da verilerin kaydedilmesini durdurdu. Çalışma alanınızda **kullanım ve tahmini maliyetler**' e gidin. Daha fazla veri kullanmanıza izin veren yeni bir **fiyatlandırma katmanı** seçin ya da **günlük tepesine** tıklayıp Cap 'i kaldırın.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Kullanım ve tahmini maliyetler." lightbox="./media/change-tracking/change-tracking-usage.png":::

Sorununuz hala çözülmedi ise, Windows için karma çalışanı yeniden yüklemek üzere [Windows karma Runbook Worker dağıtma](../automation-windows-hrw-install.md) bölümündeki adımları izleyin. Linux için,  [Linux karma runbook çalışanı dağıtma](../automation-linux-hrw-install.md)' daki adımları izleyin.

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Senaryo: Windows makineler için Değişiklik İzleme ve envanter kayıtları gösterilmiyor

#### <a name="issue"></a>Sorun

Özelliği için etkinleştirilmiş olan Windows makineler için herhangi bir Değişiklik İzleme ve envanter sonucu görmezsiniz.

#### <a name="cause"></a>Nedeni

Bu hata aşağıdaki nedenlerle olabilir:

* Windows için Azure Log Analytics Aracısı çalışmıyor.
* Otomasyon hesabına geri iletişim engelleniyor.
* Değişiklik İzleme ve envanter yönetim paketleri indirilmez.
* Etkinleştirilen VM, Windows için Log Analytics Aracısı ile sistem hazırlığı (Sysprep) ile hazırlanmamış bir kopyalanmış makineden gelmiş olabilir.

#### <a name="resolution"></a>Çözüm

Log Analytics Aracısı makinesinde, **C:\Program Files\Microsoft Monitoring Tors T\tors \ araçlar** ' a gidin ve aşağıdaki komutları çalıştırın:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Hala yardıma ihtiyacınız varsa tanılama bilgilerini toplayabilir ve desteğe başvurabilirsiniz.

> [!NOTE]
> Log Analytics Aracısı varsayılan olarak hata izleme imkanı sunar. Önceki örnekte olduğu gibi ayrıntılı hata iletilerini etkinleştirmek için `VER` parametresini kullanın. Bilgi izlemeleri için öğesini `INF` çağırdığınızda kullanın `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Windows için Log Analytics Aracısı çalışmıyor

Log Analytics Windows (**HealthService.exe**) aracısının makinede çalıştığını doğrulayın.

##### <a name="communication-to-automation-account-blocked"></a>Otomasyon hesabına yönelik iletişim engellendi

Makinedeki Olay Görüntüleyicisi denetleyin ve içindeki sözcüğe sahip tüm olayları arayın `changetracking` .

Değişiklik İzleme ve envanterin çalışması için izin verilmesi gereken adresler ve bağlantı noktaları hakkında bilgi edinmek için bkz. [ağ planlaması](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Yönetim paketleri indirilmedi

Aşağıdaki Değişiklik İzleme ve stok yönetim paketlerinin yerel olarak yüklü olduğunu doğrulayın:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Kopyalanmış makineden Sysprep uygulanmamış VM

Kopyalanmış bir görüntü kullanıyorsanız, önce görüntüyü Sysprep yapın ve ardından Windows için Log Analytics aracısını yükler.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Senaryo: Linux makinelerde Değişiklik İzleme ve envanter sonucu yok

#### <a name="issue"></a>Sorun

Özelliği için etkinleştirilen Linux makineleri için herhangi bir Değişiklik İzleme ve envanter sonucu görmezsiniz. 

#### <a name="cause"></a>Nedeni
Bu soruna özel nedenler aşağıda verilmiştir:
* Linux için Log Analytics Aracısı çalışmıyor.
* Linux için Log Analytics Aracısı doğru yapılandırılmamış.
* Dosya bütünlüğü izleme (FIM) çakışmaları var.

#### <a name="resolution"></a>Çözüm 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux için Log Analytics Aracısı çalışmıyor

Linux (**omsagent**) için Log Analytics aracısının arka plan programının makinenizde çalıştığını doğrulayın. Otomasyon hesabınıza bağlı Log Analytics çalışma alanında aşağıdaki sorguyu çalıştırın.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Makinenizi sorgu sonuçlarında görmüyorsanız, son zamanlarda iade edilmedi demektir. Büyük olasılıkla yerel bir yapılandırma sorunu var ve aracıyı yeniden yüklemeniz gerekir. Yükleme ve yapılandırma hakkında bilgi için bkz. [Log Analytics aracısında günlük verileri toplama](../../azure-monitor/agents/log-analytics-agent.md).

Makineniz sorgu sonuçlarında görünüyorsa, kapsam yapılandırmasını doğrulayın. Bkz. [Azure izleyici 'de izleme çözümlerini hedefleme](../../azure-monitor/insights/solution-targeting.md).

Bu sorunu giderme hakkında daha fazla bilgi için bkz. [sorun: herhangi bir Linux verisi görmüyorsunuz](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux için Log Analytics Aracısı doğru yapılandırılmamış

Linux için Log Analytics Aracısı, OMS günlük Toplayıcı Aracı kullanılarak günlük ve komut satırı çıkış koleksiyonu için doğru şekilde yapılandırılmamış olabilir. Bkz. [değişiklik izleme ve envantere genel bakış](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>FIM çakışmaları

Azure Güvenlik Merkezi 'nin FIM özelliği, Linux dosyalarınızın bütünlüğünü yanlış şekilde doğruluyor olabilir. FIM 'nin çalışır durumda olduğunu ve Linux dosya izleme için doğru şekilde yapılandırıldığını doğrulayın. Bkz. [değişiklik izleme ve envantere genel bakış](../change-tracking/overview.md).

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.