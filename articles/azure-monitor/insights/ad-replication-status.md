---
title: Azure Izleyici ile Active Directory çoğaltma durumunu izleme | Microsoft Docs
description: Active Directory Çoğaltma Durumu çözüm paketi, tüm çoğaltma hatalarıyla ilgili Active Directory ortamınızı düzenli olarak izler.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 01/24/2018
ms.openlocfilehash: 04112042c871f5268c64bda374f040f1bba92969
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931345"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Azure Izleyici ile Active Directory çoğaltma durumunu izleme

![AD Çoğaltma Durumu simgesi](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory, kurumsal BT ortamının temel bir bileşenidir. Yüksek kullanılabilirlik ve yüksek performans sağlamak için her etki alanı denetleyicisinin kendi Active Directory veritabanının bir kopyası vardır. Etki alanı denetleyicileri, değişiklikleri kuruluş genelinde yaymak için birbirleriyle çoğaltılır. Bu çoğaltma işlemindeki başarısızlıklar, kuruluş genelinde çeşitli sorunlara neden olabilir.

AD Çoğaltma Durumu çözüm paketi, tüm çoğaltma hatalarıyla ilgili Active Directory ortamınızı düzenli olarak izler.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Çözümü yükleme ve yapılandırma
Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın.

### <a name="install-agents-on-domain-controllers"></a>Aracıları etki alanı denetleyicilerine yükler
, Değerlendirilecek etki alanının üyesi olan etki alanı denetleyicilerine aracılar yüklemelisiniz. Ya da, aracıları üye sunuculara yüklemeli ve aracıları Azure Izleyici 'ye AD Çoğaltma verileri gönderecek şekilde yapılandırmanız gerekir. Windows bilgisayarlarını Azure Izleyici 'ye bağlamayı anlamak için bkz. [Windows bilgisayarlarını Azure izleyici 'ye bağlama](../../azure-monitor/platform/agent-windows.md). Etki alanı denetleyiciniz Azure Izleyici 'ye bağlamak istediğiniz mevcut bir System Center Operations Manager ortamının zaten parçasıysa, bkz. [Azure izleyiciyi Operations Manager bağlama](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Etki alanı olmayan Denetleyiciyi Etkinleştir
Etki alanı denetleyicilerinizin herhangi birine doğrudan Azure Izleyici 'ye bağlanmasını istemiyorsanız, Azure Izleyici 'ye bağlı etki alanındaki başka bir bilgisayarı kullanarak AD Çoğaltma Durumu çözüm paketine yönelik verileri toplayabilir ve verileri göndermesini sağlayabilirsiniz.

1. Bilgisayarın AD Çoğaltma Durumu çözümünü kullanarak izlemek istediğiniz etki alanının bir üyesi olduğunu doğrulayın.
2. [Windows bilgisayarını Azure izleyici 'ye bağlayın](../../azure-monitor/platform/om-agents.md) veya zaten bağlı değilse [Azure izleyici 'ye mevcut Operations Manager ortamınızı kullanarak bağlayın](../../azure-monitor/platform/om-agents.md).
3. Bu bilgisayarda, aşağıdaki kayıt defteri anahtarını ayarlayın:<br>Anahtar: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName > \Solutions\ADReplication**<br>Değer: **ıstarget**<br>Değer verisi: **true**

   > [!NOTE]
   > Bu değişiklikler, Microsoft Monitoring Agent hizmeti (HealthService. exe) yeniden başlatılana kadar etkili olmaz.
   > ### <a name="install-solution"></a>Çözümü yükler
   > **Active Directory çoğaltma durumu** çözümünü Log Analytics çalışma alanınıza eklemek için [bir izleme çözümü yüklerken](solutions.md#install-a-monitoring-solution) açıklanan süreci izleyin. Başka bir yapılandırma işlemi gerekmez.


## <a name="ad-replication-status-data-collection-details"></a>Veri toplama ayrıntılarını AD Çoğaltma Durumu
Aşağıdaki tabloda, verilerin AD Çoğaltma Durumu için nasıl toplandığı hakkında veri toplama yöntemleri ve diğer ayrıntılar gösterilmektedir.

| platformunun | Doğrudan aracı | SCOM Aracısı | Azure Depolama | SCOM gerekli mi? | Yönetim grubu aracılığıyla gönderilen SCOM Aracısı verileri | toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |Beş günde bir |



## <a name="understanding-replication-errors"></a>Çoğaltma hatalarını anlama

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

AD Çoğaltma Durumu kutucuğu Şu anda kaç tane çoğaltma hatası olduğunu gösterir. **Kritik çoğaltma hataları** , Active Directory ormanınızın [kaldırılma süresinin](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) %75 ' ının üzerinde veya üstünde olan hatalardır.

![AD Çoğaltma Durumu kutucuğu](./media/ad-replication-status/oms-ad-replication-tile.png)

Kutucuğa tıkladığınızda hatalar hakkında daha fazla bilgi görüntüleyebilirsiniz.
AD Çoğaltma Durumu Pano](./media/ad-replication-status/oms-ad-replication-dash.png) ![

### <a name="destination-server-status-and-source-server-status"></a>Hedef sunucu durumu ve kaynak sunucu durumu
Bu sütunlarda, hedef sunucuların ve çoğaltma hataları yaşayan kaynak sunucularının durumu gösterilmektedir. Her etki alanı denetleyicisi adından sonraki sayı, bu etki alanı denetleyicisindeki çoğaltma hatalarının sayısını belirtir.

Kaynak sunucu perspektifinden ve diğer kullanıcıların hedef sunucu perspektifinden sorun gidermesi daha kolay olduğundan, hedef sunucular ve kaynak sunucuların her ikisi için de hatalar gösterilir.

Bu örnekte, birçok hedef sunucunun kabaca aynı sayıda hataya sahip olduğunu görebilirsiniz, ancak diğerlerinden çok daha fazla hataya sahip bir kaynak sunucu (ADDC35) vardır. ADDC35 üzerinde, onun çoğaltma iş ortaklarına veri gönderememesine neden olan bir sorun olabilir. ADDC35 'deki sorunları düzeltmek, hedef sunucu alanında görüntülenen birçok hatayı çözebilir.

### <a name="replication-error-types"></a>Çoğaltma hatası türleri
Bu alan, kuruluşunuz genelinde algılanan hata türleri hakkında bilgi sağlar. Her hatanın benzersiz bir sayısal kodu ve hatanın temel nedenini belirlemenize yardımcı olabilecek bir ileti vardır.

Üstteki halka, ortamınızda hangi hataların daha fazla ve daha az sıklıkla görünceğinin bir fikrini sunar.

Birden çok etki alanı denetleyicisi aynı çoğaltma hatasıyla karşılaşdığını gösterir. Bu durumda, bir etki alanı denetleyicisinde bir çözümü bulabilir veya tanımlayabilir, ardından aynı hatadan etkilenen diğer etki alanı denetleyicilerinde yineleyebilirsiniz.

### <a name="tombstone-lifetime"></a>Silinmiş öğe ömrü
Kaldırılma süresi ömrü, silinmiş bir nesnenin kaldırılma olarak ifade edilen Active Directory veritabanında ne kadar süreyle korunduğunu belirler. Silinen bir nesne kaldırılma süresini geçtiğinde, bir çöp toplama işlemi onu Active Directory veritabanından otomatik olarak kaldırır.

Varsayılan kaldırılma süresi, Windows 'un en son sürümleri için 180 gündür, ancak eski sürümlerde 60 gündür ve açıkça bir Active Directory yöneticisi tarafından değiştirilebilir.

Bu, yaklaşan veya kaldırılma süresini aşan çoğaltma hatalarıyla karşılaşıp yaşamadığınızı bilmeniz önemlidir. İki etki alanı denetleyicisi, kaldırılma süresinin ötesinde devam eden bir çoğaltma hatası yaşalıyorsa, temel alınan çoğaltma hatası düzeltilse bile bu iki etki alanı denetleyicisi arasında çoğaltma devre dışı bırakılır.

Silinmiş öğe ömrü alanı, devre dışı çoğaltmanın gerçekleştiği yerleri belirlemenize yardımcı olur. **100 ' den fazla% TSL** kategorisindeki her hata, kaynak ve hedef sunucu arasında çoğaltılmamış olan bir bölümü, en azından ormanın kaldırılma süresini temsil eder.

Bu durumda, çoğaltma hatasını düzeltmek yeterli olmayacaktır. Çoğaltmayı yeniden başlatmadan önce, en azından, kalan nesneleri tanımlamak ve temizlemek için el ile araştırmanız gerekir. Bir etki alanı denetleyicisinin yetkisini almanız da gerekebilir.

Kaldırılma süresinden sonra kalıcı olan herhangi bir çoğaltma hatasını tanımlamaya ek olarak, **50-75% TSL** veya **75-100% TSL** kategorilerine kalan tüm hatalara dikkat etmek de isteyebilirsiniz.

Bunlar, geçici olarak değil, geçici olarak kalan ve bu nedenle müdahale etmeniz gereken hatalardan kaynaklanıyor. İyi haber, henüz kaldırılma süresine ulaşmamıştır. Bu sorunları hemen düzeltirseniz ve kaldırılma süresine ulaşmadan *önce* , çoğaltma en düşük el ile müdahale ile yeniden başlatılabilir.

Daha önce belirtildiği gibi, AD Çoğaltma Durumu çözümü için Pano kutucuğu ortamınızdaki *kritik* çoğaltma hatalarının sayısını gösterir ve bu, kaldırılma süresinin %75 ' ınden (TSL 'nin %100 ' inden fazla olan hatalar da dahil olmak üzere) hata olarak tanımlanır. Bu sayıyı 0 ' da tutmak için çaba yapın.

> [!NOTE]
> Tüm kaldırılma süresi yüzdesi hesaplamaları, Active Directory ormanınızın gerçek kaldırılma süresini temel alır. bu nedenle, özel bir kaldırma yaşam süresi değeri ayarlamış olsanız bile bu yüzdeleri doğru şekilde güvende olabilirsiniz.
>
>

### <a name="ad-replication-status-details"></a>AD çoğaltma durumu ayrıntıları
Listelerden birindeki herhangi bir öğeye tıkladığınızda, bir günlük sorgusu kullanarak onunla ilgili ek ayrıntılar görürsünüz. Sonuçlar yalnızca söz konusu öğeyle ilgili hataları gösterecek şekilde filtrelenmiştir. Örneğin, **hedef sunucu durumu (ADDC02)** altında listelenen ilk etki alanı denetleyicisine tıklarsanız, hedef sunucu olarak listelenen etki alanı denetleyicisi ile ilgili hataları göstermek üzere filtrelenmiş sorgu sonuçları görürsünüz:

![Sorgu sonuçlarında AD çoğaltma durumu hataları](./media/ad-replication-status/oms-ad-replication-search-details.png)

Buradan, daha fazla filtre uygulayabilir, günlük sorgusunu değiştirebilir ve bu şekilde devam edebilirsiniz. Azure Izleyici 'de günlük sorgularını kullanma hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük verilerini çözümleme](../../azure-monitor/log-query/log-query-overview.md).

**HelpLink** alanı, bu belirli hata hakkında ek ayrıntıları Içeren bir TechNet sayfasının URL 'sini gösterir. Sorun giderme ve hatayı düzeltme hakkında bilgi edinmek için bu bağlantıyı kopyalayıp tarayıcı pencerenize yapıştırabilirsiniz.

Ayrıca, sonuçları Excel 'e aktarmak için **dışarı aktar** ' a da tıklayabilirsiniz. Verilerin dışarı aktarılması, çoğaltma hatası verilerini dilediğiniz şekilde görselleştirmenize yardımcı olabilir.

![Excel 'de AD çoğaltma durumu hataları veriliyor](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD Çoğaltma Durumu SSS
**S: AD çoğaltma durumu verileri ne sıklıkta güncelleştiriliyor?**
Y: bilgi her beş günde bir güncelleştirilir.

**S: Bu verilerin ne sıklıkta güncelleştirileceğini yapılandırmak için bir yol var mı?**
Y: Şu anda değil.

**S: çoğaltma durumunu görmek için etki alanı denetleyicilerimin tümünü Log Analytics çalışma alanına eklemem gerekiyor mu?**
Y: Hayır, yalnızca tek bir etki alanı denetleyicisi eklenmelidir. Log Analytics çalışma alanınızda birden çok etki alanı denetleyiciniz varsa, bunların tümünün verileri Azure Izleyici 'ye gönderilir.

**S: Log Analytics çalışma alanına herhangi bir etki alanı denetleyicisi eklemek istemiyorum. AD Çoğaltma Durumu çözümünü kullanmaya devam edebilir miyim?**

C: Evet. Bir kayıt defteri anahtarının değerini etkinleştirmek için ayarlayabilirsiniz. Bkz. [etki alanı olmayan denetleyiciyi etkinleştirme](#enable-non-domain-controller).

**S: veri toplamayı yapan işlemin adı nedir?**
A: Danışmanorassessment. exe

**S: verilerin toplanması ne kadar sürer?**
Y: veri toplama süresi Active Directory ortamının boyutuna bağlıdır, ancak genellikle 15 dakikadan kısa sürer.

**S: ne tür veriler toplanır?**
Y: çoğaltma bilgileri LDAP aracılığıyla toplanır.

**S: veri toplandığında yapılandırmak için bir yol var mı?**
Y: Şu anda değil.

**S: veri toplamak için hangi izinlere ihtiyacım var?**
Y: Active Directory için normal Kullanıcı izinleri yeterlidir.

## <a name="troubleshoot-data-collection-problems"></a>Veri toplama sorunlarını giderme
AD Çoğaltma Durumu çözüm paketi, verileri toplamak için en az bir etki alanı denetleyicisinin Log Analytics çalışma alanınıza bağlanmasını gerektirir. Bir etki alanı denetleyicisine bağlanana kadar, **verilerin toplanmakta**olduğunu belirten bir ileti görüntülenir.

Etki alanı Denetleyicilerinizden birini bağlamak için yardıma ihtiyacınız varsa, [Windows bilgisayarlarını Azure izleyici 'ye bağlama](../../azure-monitor/platform/om-agents.md)sırasında belgeleri görüntüleyebilirsiniz. Alternatif olarak, etki alanı denetleyiciniz zaten var olan bir System Center Operations Manager ortamına bağlandıysa, [Azure izleyici 'ye System Center Operations Manager Connect (bağlantı](../../azure-monitor/platform/om-agents.md)) belgesine bakabilirsiniz.

Etki alanı denetleyicilerinizin herhangi birini doğrudan Azure Izleyici 'ye veya System Center Operations Manager bağlamak istemiyorsanız, bkz. [etki alanı dışı denetleyiciyi etkinleştirme](#enable-non-domain-controller).

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı Active Directory çoğaltma durumu verilerini görüntülemek için [Azure izleyici 'de günlük sorgularını](../../azure-monitor/log-query/log-query-overview.md) kullanın.
