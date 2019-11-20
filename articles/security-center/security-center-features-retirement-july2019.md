---
title: Güvenlik Merkezi özelliklerini kullanımdan kaldırma (2019 Temmuz) | Microsoft Docs
description: Bu makalede, güvenlik merkezi 'nde 31 Temmuz 2019 ' de kullanımdan kaldırılan özellikler açıklanmaktadır.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: c4fe185f5d203b1b0eba306c80cded14ad82bc98
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438637"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Güvenlik Merkezi özelliklerini kullanımdan kaldırma (2019 Temmuz)

> [!NOTE]
> Bu belgede, 31 Temmuz 2019 ' de Azure Güvenlik Merkezi 'nden kullanımdan kaldırılan özelliklerin listesi ayrıntılı olarak listelenmektedir.
>
>

Azure Güvenlik Merkezi 'Nde on Temmuz 2019 ' e kadar olan altı ay boyunca birkaç [geliştirme](https://azure.microsoft.com/updates/?product=security-center) yaptık.
Bu geliştirilmiş özelliklerle, 31 Temmuz 2019 ' de Güvenlik Merkezi 'nden bazı yedekli özellikleri ve ilgili API 'Leri kaldırdık.

Bu kullanımdan kaldırılan özelliklerin çoğu, Azure Güvenlik Merkezi veya Azure Log Analytics diğer işlevleriyle değiştirilebilir. Diğer özellikler, [Azure Sentinel (Önizleme)](https://azure.microsoft.com/services/azure-sentinel/)kullanılarak uygulanabilir.

Kullanımdan kaldırılan güvenlik merkezi özellikleri şunları içerir:

- [Olaylar panosu](#menu_events)
- [Arama menüsü girdisi](#menu_search)
- [Kimlik ve erişim üzerindeki klasik kimlik & erişim bağlantısını görüntüleyin (Önizleme)](#menu_classicidentity)
- [Güvenlik uyarıları eşlemesindeki güvenlik olayları eşleme düğmesi (Önizleme)](#menu_securityeventsmap)
- [Özel uyarı kuralları (Önizleme)](#menu_customalerts)
- [Tehdit koruması güvenlik uyarılarında araştır düğmesi](#menu_investigate)
- [Güvenlik çözümlerinin bir alt kümesi](#menu_solutions)
- [Güvenlik ilkeleri için güvenlik yapılandırmasını düzenleme](#menu_securityconfigurations)
- [Log Analytics çalışma alanları için güvenlik ve denetim panosu (başlangıçta OMS portalında kullanılır)](#menu_securityomsdashboard)

Bu makalede, kullanımdan kaldırılan her özellik ve değiştirme özelliklerini uygulamak için uygulayabileceğiniz adımlar hakkında ayrıntılı bilgiler sağlanmaktadır.

## Olaylar panosu<a name="menu_events"></a>

Güvenlik Merkezi, makinelerinizdeki güvenlikle ilgili çeşitli yapılandırma ve olayları toplamak için Microsoft Monitoring Agent kullanır. Bu olayları çalışma alanlarınızdaki depolar. [Olaylar panosu](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) , bu verileri görüntülemenize ve Log Analytics için bir giriş noktası sağlamanıza olanak tanır.

Bir çalışma alanı seçtiğinizde görüntülenen olaylar panosunu kullanımdan kaldırdı:

![Olaylar panosu][2]

### <a name="events-dashboard---the-new-experience"></a>Olaylar panosu-yeni deneyim

Çalışma Alanlarınızdaki önemli olaylarını görüntülemek için Azure Log Analytics 'in yerel yeteneklerini kullanmanızı öneririz.

Güvenlik Merkezi 'nde özel önemli olayları oluşturduysanız, bunlar erişilebilir olacaktır. Log Analytics, **çalışma alanı** > **kayıtlı aramalar**' a gidin. Verileriniz kaybolmaz veya değiştirilmez. Yerel önemli olayları, Log Analytics aynı ekrandan de mevcuttur.

![Çalışma alanı kaydedilmiş aramaları][3]

## Arama menüsü girdisi<a name="menu_search"></a>

Azure Güvenlik Merkezi şu anda Azure Izleyici günlükleri aramasını kullanarak güvenlik verilerinizi alır ve analiz eder. Bu ekran, arama sayfası Log Analytics bir pencere işlevi görür ve kullanıcıların seçili çalışma alanlarında arama sorguları çalıştırmasına olanak sağlar. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi arama](https://docs.microsoft.com/azure/security-center/security-center-search). Bu arama penceresi kullanımdan kaldırıldı:

![Arama sayfası][4]

### <a name="search-menu-entry---the-new-experience"></a>Arama menüsü girişi-yeni deneyim

Çalışma alanlarınızda arama sorguları gerçekleştirmek için Azure Log Analytics yerel yeteneklerini kullanmanızı öneririz. Azure Log Analytics gidin ve **Günlükler**' i seçin.

![Log Analytics günlükleri sayfası][5]

## Klasik kimlik & erişimi (Önizleme)<a name="menu_classicidentity"></a>

Güvenlik Merkezi 'ndeki klasik kimlik & erişim deneyimi şu anda Log Analytics bir kimlik ve erişim bilgileri panosunu gösterir. Bu panoyu görüntülemek için:

1. **Klasik kimlik & erişimi görüntüle**' yi seçin.

   ![Kimlik sayfası][6]

1. **Erişim panosunu & kimliği**görüntüleyin.

    ![Kimlik sayfası-çalışma alanı seçimi][7]

1. Çalışma alanınızdaki kimlik ve erişim bilgilerini görüntülemek için Log Analytics **& erişim** panosunu açmak üzere bir çalışma alanı seçin.

   ![Kimlik sayfası-Pano][8]

Önceki adımlarda gösterilen üç ekranın hepsini devre dışı bırakıyoruz. Verileriniz Log Analytics güvenlik çözümünde kullanılabilir kalır ve değiştirilmez veya kaldırılmaz.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klasik kimlik & erişimi (Önizleme)-yeni deneyim

Log Analytics panosu, tek bir çalışma alanında Öngörüler göstermiştir. Ancak, yerel güvenlik merkezi özellikleri tüm aboneliklere ve bunlarla ilişkili tüm çalışma alanlarına görünürlük sağlar. Güvenli puanlarına göre derecelendirilmesi gereken önerilerin ne kadar önemli olduğuna odaklanmanızı sağlayan, kullanımı kolay bir görünüme erişebilirsiniz.

Güvenlik Merkezi 'nde **kimlik & erişim (Önizleme)** seçilerek Log Analytics ' deki **kimlik & erişim** panosunun tüm özelliklerine erişilebilir.

![Kimlik sayfası-klasik deneyim kullanımdan kaldırma][9]

## Güvenlik olayları Haritası<a name="menu_securityeventsmap"></a>

Güvenlik Merkezi, güvenlik tehditlerini belirlemenize yardımcı olmak için size bir [güvenlik uyarıları Haritası](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) sağlar. Bu eşlemedeki **güvenlik olaylarına git eşleme** düğmesi, seçili çalışma alanındaki ham güvenlik olaylarını görüntülemenize olanak tanıyan bir pano açar.

**Güvenlik olayları eşleme** düğmesine ve çalışma alanı başına panosuna git ' i kaldırdık.

![Güvenlik uyarıları Haritası-düğme][10]

**Güvenlik olayları eşlemesini git** düğmesine tıkladığınızda, (şimdi kullanımdan kaldırılan) tehdit bilgileri panosu açılır.

![Tehdit zekası panosu][11]

Tehdit bilgileri panosunu görüntülemek için bir çalışma alanı seçtiğinizde, Log Analytics ' de (artık kullanımdan kaldırılan) güvenlik uyarıları Haritası (Önizleme) ekranını açtınız.

![Log Analytics 'de güvenlik uyarıları Haritası][12]

Mevcut verileriniz Log Analytics güvenlik çözümünde kullanılabilir kalır ve değiştirilmez veya kaldırılmaz.

### <a name="security-events-map---the-new-experience"></a>Güvenlik olayları Haritası-yeni deneyim

Güvenlik Merkezi 'Nde yerleşik olarak bulunan uyarılar eşleme işlevini kullanmanızı öneririz: **güvenlik uyarıları Haritası (Önizleme)** . Bu işlevsellik, iyileştirilmiş bir deneyim sağlar ve tüm aboneliklerde ve ilişkili çalışma alanlarında çalışmaktadır. Ortamınız genelinde üst düzey bir görünüm sunar ve tek bir çalışma alanına odaklanmıyor.

## Özel uyarı kuralları (Önizleme)<a name="menu_customalerts"></a>

Temel altyapısı kullanımdan kalkdığı için, 30 Haziran 2019 ' de [özel uyarı deneyimini kullanımdan](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) kaldırdı. Kullanımdan kaldırma tarihinden sonra özel güvenlik uyarıları artık oluşturulmaz.
[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 'i etkinleştirmenizi ve özel uyarılarınızı orada yeniden oluşturmanızı öneririz. Alternatif olarak, Azure Izleyici günlük uyarıları ile uyarılarınızı de oluşturabilirsiniz.

Azure Sentinel ile özel uyarılar oluşturmak için:

1. [Azure Sentinel 'ı açın](https://portal.azure.com/#create/Microsoft.ASI/preview) ve özel uyarılarınızın depolandığı çalışma alanını seçin
1. Menüden **analiz** seçin
1. Azure Sentinel 'de özel uyarılar oluşturma hakkında aşağıdaki [öğreticideki](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) yönergeleri izleyin

Azure Sentinel 'i kullanmaya ilgileniyorsanız, uyarılarınızı Azure Izleyici günlük uyarıları ile oluşturabilirsiniz. Yönergeler için bkz. Azure izleyici 'yi [kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) ve [Azure izleyici 'de günlük uyarıları](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Özel uyarılar][13]

Özel uyarılar kullanımdan kaldırma hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde özel uyarı kuralları (Önizleme)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Güvenlik uyarıları araştırması<a name="menu_investigate"></a>

Güvenlik Merkezi 'ndeki [araştırma özelliği,](https://docs.microsoft.com/azure/security-center/security-center-investigation) olası bir güvenlik olayını önceliklendirmenize yardımcı olur. Özelliği, bir olayın kapsamını anlamanıza ve kök nedenini izlemenize olanak sağlar. Bu özelliği [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)'de geliştirilmiş bir deneyimle değiştirildiğinden Güvenlik Merkezi ' nden kaldırdık.

![Güvenlik olayı][14]

Bir **güvenlik olayı** ekranından **araştır** düğmesini seçtiğinizde Log Analytics araştırma panosunu (Önizleme) açarsınız. Araştırma panosunu kullanımdan kaldırdı.

Mevcut verileriniz Log Analytics güvenlik çözümünde kullanılabilir kalır ve değiştirilmez veya kaldırılmaz.

![Log Analytics araştırma panosu][15]

### <a name="investigation---the-new-experience"></a>Araştırma-yeni deneyim

Zengin araştırma deneyimi için [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 'e geçiş yapmanız önerilir. Azure Sentinel, kuruluşunuzun veri kaynakları genelinde güvenlik tehditleri arayalım için güçlü arama ve sorgu araçları sağlar.

## Güvenlik çözümlerinin alt kümesi<a name="menu_solutions"></a>

Güvenlik Merkezi, [Azure 'da tümleşik güvenlik çözümlerini](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)etkinleştirebilir. Güvenlik Merkezi 'nden aşağıdaki iş ortağı çözümlerini kullanımdan kaldırdı. Bu çözümler [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 'de, ek veri kaynaklarıyla birlikte etkinleştirilir.

- [Yeni nesil güvenlik duvarı ve Web uygulaması güvenlik duvarı çözümleri](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Ortak olay biçimini (CEF) destekleyen güvenlik çözümlerinin tümleştirilmesi](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Kimlik Koruması](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Kullanımdan kaldırıldıktan sonra, kullanıcı arabiriminden veya API 'den önceki listede bahsedilen çözüm türlerini ekleyemez veya değiştiremezsiniz. Azure Güvenlik Merkezi, artık bu iş ortağı çözümlerinin yeni örneklerini bulamaz.

Bağlı çözümleriniz varsa Azure Sentinel 'e geçiş yapmanız önerilir.

![Güvenlik merkezleri çözümleri][16]

## Güvenlik ilkeleri için güvenlik yapılandırmasını düzenleme<a name="menu_securityconfigurations"></a>

Azure Güvenlik Merkezi, işletim sistemini güçlendirme amacıyla [150'den fazla önerilen kuraldan](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) oluşan bir küme uygulayarak güvenlik yapılandırmalarını izler. Bu kurallar güvenlik duvarları, denetim, parola ilkeleri ve daha fazlası ile ilgilidir. Bir makinenin yapılandırmasında güvenlik açığı varsa Güvenlik Merkezi bu konuyla ilgili bir güvenlik önerisi oluşturur. [Güvenlik Yapılandırması düzenleme ekranı](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) , müşterilerin Güvenlik Merkezi 'nde varsayılan işletim sistemi güvenlik yapılandırmasını özelleştirmesini sağlar.

Bu önizleme özelliğini kullanımdan kaldırdı. Güvenlik yapılandırmalarınızı, kullanımdan kaldırma tarihinden sonra varsayılan değerlerine geri döndürmek için [aşağıdaki yönergeleri](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization) kullanarak API veya PowerShell aracılığıyla bunu yapın

![Güvenlik yapılandırmasını düzenle][17]

### <a name="edit-security-configurations---the-new-experience"></a>Güvenlik yapılandırmasını düzenleme-yeni deneyim

[Konuk yapılandırma aracısını](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)desteklemek Için Güvenlik Merkezi 'ni etkinleştirmek istiyorduk. Bu tür bir güncelleştirme, daha zengin bir özellik kümesine olanak tanır; bu da daha fazla işletim sistemi desteği ve konuk yapılandırma için Azure Konuk içi ilkelerin tümleştirilmesi. Bu değişiklikler etkinleştirildikten sonra, Ayrıca, konfigürasyonları ölçeklendirerek denetleyebilir ve bunları otomatik olarak yeni kaynaklara uygulayabilirsiniz.

## Log Analytics çalışma alanları için güvenlik ve denetim panosu<a name="menu_securityomsdashboard"></a>

Güvenlik ve denetim panosu başlangıçta OMS portalında kullanıldı. Log Analytics, pano, önemli güvenlik olayları ve tehditleri, tehdit bilgileri Haritası ve çalışma alanına kaydedilen güvenlik olaylarının kimlik ve erişim değerlendirmesi için çalışma alanı başına genel bakış sağlar. Panoyu kaldırdık. Pano Kullanıcı arabiriminde zaten önerdiğimiz gibi Azure Güvenlik Merkezi 'ne geçiş yapmanız önerilir.

![Log Analytics güvenlik panosu][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Güvenlik ve denetim panosu-yeni deneyim

Azure Güvenlik Merkezi 'ne geçiş yapmanız önerilir. Birden çok aboneliğe ve bunlarla ilişkili çalışma alanlarına ve daha zengin bir özellik kümesine sahip güvenlik genel bakışını sağlar.

Güvenlik Merkezi için [GitHub deposundaki](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) güvenlik ve denetim panosunu dolduran özgün Log Analytics sorgularını alabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/)hakkında daha fazla bilgi edinin.
- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel)hakkında daha fazla bilgi edinin.

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
