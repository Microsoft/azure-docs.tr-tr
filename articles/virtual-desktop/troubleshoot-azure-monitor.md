---
title: Windows sanal masaüstü 'Nü Izleme sorunlarını giderme-Azure
description: Windows sanal masaüstü için Azure Izleyici ile ilgili sorunları giderme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a104f4d33e1bd38e130101b34d3fd2021de27cd2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445491"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure Izleyici sorunlarını giderme

Bu makalede, Windows sanal masaüstü için Azure Izleyici 'de yaygın sorunlara yönelik bilinen sorunlar ve çözümler sunulmaktadır.

## <a name="issues-with-configuration-and-setup"></a>Yapılandırma ve kurulum ile ilgili sorunlar

Yapılandırma çalışma kitabı kurulumu otomatik hale getirmek için düzgün çalışmıyorsa, ortamınızı el ile ayarlamak için bu kaynakları kullanabilirsiniz:

- Tanılamayı el ile etkinleştirmek veya Log Analytics çalışma alanına erişmek için bkz. [Log Analytics Için Windows sanal masaüstü tanılaması gönderme](diagnostics-log-analytics.md).
- Log Analytics uzantısını bir oturum konağına el ile yüklemek için, bkz. [Windows için Log Analytics sanal makine uzantısı](../virtual-machines/extensions/oms-windows.md).
- Yeni bir Log Analytics çalışma alanı ayarlamak için, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md).
- Performans sayaçlarını eklemek, kaldırmak veya düzenlemek için bkz. [performans sayaçlarını yapılandırma](../azure-monitor/agents/data-sources-performance-counters.md).
- Log Analytics çalışma alanı için Windows olay günlüklerini yapılandırmak için, bkz. [Log Analytics aracısıyla Windows olay günlüğü veri kaynakları toplama](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Verilerim doğru görüntülenmiyor

Verileriniz doğru şekilde görüntülenmiyorsa, aşağıdaki ortak çözümleri denetleyin:

- İlk olarak, [dağıtımınızı izlemek Için Windows sanal masaüstü Için Azure İzleyicisi 'Ni kullanma](azure-monitor.md)başlığı altında açıklandığı gibi yapılandırma çalışma kitabıyla doğru şekilde ayarladığınızdan emin olun. Herhangi bir sayaç veya olay eksikse, bunlarla ilişkili veriler Azure portal görünmez.
- Erişim izinlerinizi denetleyin & eksik izinler istemek için kaynak sahipleriyle iletişime geçin; Windows sanal masaüstünü izleyen herkes aşağıdaki izinleri gerektirir:
    - Windows sanal masaüstü kaynaklarınızı tutan Azure aboneliklerine yönelik okuma erişimi
    - Windows sanal masaüstü oturumu konaklarınızı tutan aboneliğin kaynak gruplarına okuma erişimi 
    - Kullanmakta olduğunuz Log Analytics çalışma alanlarına okuma erişimi
- Azure Izleyiciyi ve Log Analytics portala veri göndermesini sağlamak için sunucunuzun güvenlik duvarında giden bağlantı noktalarını açmanız gerekebilir. Bunu nasıl yapacağınızı öğrenmek için aşağıdaki makalelere bakın:
      - [Azure Izleyici giden bağlantı noktaları](../azure-monitor/app/ip-addresses.md)
      - [Güvenlik duvarı gereksinimleri Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Son etkinlikten veriler görmüyor musunuz? 15 dakika beklemek ve akışı yenilemek isteyebilirsiniz. Azure Izleyici, günlük verilerinin doldurulmasına yönelik 15 dakikalık bir gecikme süresine sahiptir. Daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük verisi alma süresi](../azure-monitor/logs/data-ingestion-time.md).

Herhangi bir bilgi eksik değilse ancak verileriniz doğru şekilde görüntülenmiyorsa, sorguda veya veri kaynaklarında bir sorun olabilir. [Bilinen sorunları ve sınırlamaları](#known-issues-and-limitations)gözden geçirin. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure Izleyicisini özelleştirmek istiyorum

Windows sanal masaüstü için Azure Izleyici, Azure Izleyici çalışma kitaplarını kullanır. Çalışma kitapları, Windows sanal masaüstü çalışma kitabı şablonunun bir kopyasını kaydetmenizi ve kendi özelleştirmelerinizi yapmanızı sağlar.

Tasarım yaparak, özel çalışma kitabı şablonları ürün grubundan güncelleştirmeleri otomatik olarak benimsemez. Daha fazla bilgi için bkz. [çalışma kitabı tabanlı Öngörüler sorunlarını giderme](../azure-monitor/insights/troubleshoot-workbooks.md) ve [çalışma kitaplarına genel bakış](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Verileri yorumlayamıyorum

[Windows sanal masaüstü sözlüğü Için Azure izleyici](azure-monitor-glossary.md)'de veri koşulları hakkında daha fazla bilgi edinin.

## <a name="the-data-i-need-isnt-available"></a>İhtiyacım olan veriler kullanılamıyor

Daha fazla performans sayacı veya Windows olay günlüğü izlemek isterseniz, bu kullanıcıların Log Analytics çalışma alanınıza tanılama bilgileri göndermesini ve bunları **konak Tanılama: konak tarayıcısı**'nda izlemenizi sağlayabilirsiniz. 

- Performans sayaçlarını eklemek için bkz. [performans sayaçlarını yapılandırma](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Windows olayları eklemek için bkz. [Windows olay günlüklerini yapılandırma](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Bir sorunu tanılamaya yardımcı olmak için bir veri noktası bulunamıyor musunuz? Bize geri bildirim gönderin!

- Geri bildirimleri nasıl bırakacağınızı öğrenmek için bkz. [sorun giderme genel bakış, geri bildirim ve Windows sanal masaüstü desteği](troubleshoot-set-up-overview.md).
- Windows sanal masaüstü [Geri Bildirim Hub 'ında](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)Windows sanal masaüstü geri bildirimini de bırakabilirsiniz.

## <a name="known-issues-and-limitations"></a>Bilinen sorunlar ve sınırlamalar

Aşağıda, farkındayız ve düzeltilmesi için çalıştık sorunlar ve sınırlamalar verilmiştir:

- Tek seferde yalnızca bir konak havuzunu izleyebilirsiniz. 
- Sık kullanılan ayarları kaydetmek için çalışma kitabının özel bir şablonunu kaydetmeniz gerekir. Özel şablonlar ürün grubundan güncelleştirmeleri otomatik olarak benimsemez.
- Yapılandırma çalışma kitabı bazen seçimlerinizi yüklerken "sorgu başarısız oldu" hatalarını gösterir. Sorguyu yenileyin, gerekirse seçiminizi yeniden girin ve hata kendi kendine çözümlenmelidir. 
- Bazı hata iletileri Kullanıcı dostu bir şekilde phrased değildir ve tüm hata iletileri belgelerde açıklanmaz.
- Toplam oturumlar performans sayacı oturumları küçük bir sayıya göre sayabilir ve toplam oturumlarınız, en fazla oturum sınırınız üzerinde bulunabilir.
- Kullanılabilir oturum sayısı, konak havuzundaki ölçeklendirme ilkelerini yansıtmıyor.   
- Çakışan veya beklenmedik bağlantı zamanlarını mi görüyorsunuz? Nadir bir bağlantı, bir bağlantının tamamlanma olayı eksik olabilir ve bazı görselleri ve ölçümleri etkileyebilir.
- Bağlanma süresi, kullanıcıların kimlik bilgilerini girmesi için gereken süreyi içerir; Bu deneyimle ilişkili olmakla kalmaz, bazı durumlarda yanlış Peaks gösterilebilir. 
    

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [dağıtımınızı izlemek Için Windows sanal masaüstü Için Azure izleyici 'Yi kullanma](azure-monitor.md).
- Veri depolama maliyetlerinizi tahmin etmek, ölçmek ve yönetmek için bkz. [Azure izleyici maliyetlerini tahmin](azure-monitor-costs.md)etme.
- Windows sanal masaüstü için Azure Izleyici ile ilgili hüküm ve kavramlar hakkında daha fazla bilgi edinmek için [sözlüğümüze](azure-monitor-glossary.md) göz atın.
