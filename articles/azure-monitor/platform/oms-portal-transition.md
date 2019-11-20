---
title: OMS portalı Azure 'a taşınıyor | Microsoft Docs
description: OMS portalı, Azure portal taşınmakta olan tüm işlevlerle birlikte sunmaktır. Bu makalede, bu geçişle ilgili ayrıntılar sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 170973d15b5f49021a0507bdd2fd6a2632777d48
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932143"
---
# <a name="oms-portal-moving-to-azure"></a>OMS portalı Azure 'a taşınıyor

> [!NOTE]
> Bu makale, aksi belirtilmedikçe Azure genel bulutu ve kamu bulutu için geçerlidir.

**Azure genel bulutu için OMS portalı resmi olarak devre dışı bırakıldı. Azure ABD Kamu Bulutu için OMS portalı, 15 Mayıs 2019 tarihinde resmi olarak devre dışı bırakıldı.** Azure portal geçmek için heyecanlıyız ve geçişin kolay olmasını bekliyor. Ancak değişikliklerin zor olduğunu ve karışıklığa neden olabileceğini anladık. Bu makalenin geri kalanı, anahtar senaryolarından ve bu geçişin yol haritasını ele alacağından oluşur.

Azure portal tüm Azure hizmetlerinin merkezidir ve kaynakları sabitleme, kaynakları bulmak için akıllı arama ve kaynak yönetimine yönelik etiketleme gibi özelliklere sahip zengin bir yönetim deneyimi sunar. İzleme ve yönetim iş akışını birleştirmek ve kolaylaştırmak için, Azure portal OMS portal özelliklerini eklemeye başladık. OMS portalının tüm özellikleri artık Azure portal bir parçasıdır. Aslında Trafik Analizi gibi yeni özelliklerden bazıları yalnızca Azure portal kullanılabilir. OMS portalında yaptığınız her şeyi Azure portal ve daha fazlasını gerçekleştirebileceksiniz. Daha önce yapmadıysanız Azure portal kullanmaya başlamanız gerekir!

## <a name="what-is-changing"></a>Ne değişiyor? 
OMS portalının kullanımdan kalkması ile aşağıdaki değişiklikler duyuruluyor. Bu değişikliklerin her biri, aşağıdaki bölümlerde daha ayrıntılı olarak açıklanmıştır.

- Yalnızca Azure portal yeni [çalışma alanları](#new-workspaces) oluşturabilirsiniz.
- Yeni uyarı yönetimi deneyimi [uyarı yönetimi çözümünün yerini alır](#changes-to-alerts).
- [Kullanıcı erişimi yönetimi](#user-access-and-role-migration) artık Azure Portal Azure rol tabanlı erişim denetimi kullanılarak yapılır.
- Aynı işlevsellik, çalışma alanı sorguları aracılığıyla etkinleştirildiğinden [Application Insights Bağlayıcısı artık gerekli değildir](#application-insights-connector-and-solution) .
- [OMS mobil uygulaması](#oms-mobile-app) kullanım dışı bırakılıyor. 
- [NSG çözümü](#azure-network-security-group-analytics) , trafik analizi çözümü aracılığıyla kullanılabilen gelişmiş işlevlerle değiştiriliyor.
- System Center Operations Manager Log Analytics ile yeni bağlantılar, [güncelleştirilmiş yönetim paketleri](#system-center-operations-manager)gerektirir.
- [Güncelleştirme yönetimi](../../automation/automation-update-management.md)değişiklikler hakkında ayrıntılı bilgi için bkz. [OMS güncelleştirme dağıtımlarınızı Azure 'a geçirme](../../automation/migrate-oms-update-deployments.md) .


## <a name="what-should-i-do-now"></a>Şimdi ne yapmam gerekir?
Birçok özellik herhangi bir geçiş yapılmadan çalışmaya devam ederken, aşağıdaki görevleri gerçekleştirmeniz gerekir:

- [Kullanıcı izinlerinizi Azure Portal geçirmeniz](#user-access-and-role-migration) gerekir.
- Güncelleştirme Yönetimi çözümünü geçirme hakkında ayrıntılı bilgi için bkz. [OMS güncelleştirme dağıtımlarınızı Azure 'A geçirme](../../automation/migrate-oms-update-deployments.md) .

Azure portal geçiş hakkında daha fazla bilgi için [Log Analytics kullanıcılar IÇIN OMS portalından geçiş Için genel soruların Azure Portal](oms-portal-faq.md) bölümüne bakın. 

## <a name="user-access-and-role-migration"></a>Kullanıcı erişimi ve rol geçişi
Azure portal Access Management, OMS portalındaki erişim yönetiminden daha zengin ve daha güçlüdür. Log Analytics 'de erişim yönetiminin ayrıntıları için bkz. [Azure Izleyici günlükleri çalışma alanınızı tasarlama](design-logs-deployment.md) .

> [!NOTE]
> Bu makalenin önceki sürümleri, izinlerin otomatik olarak OMS portalından Azure portal dönüştürüleceğinin belirtilüyordu. Bu otomatik dönüştürme artık planlanmaz ve dönüştürmeyi kendiniz gerçekleştirmeniz gerekir.

Azure portal, herhangi bir değişiklik yapmanız gerekmiyorsa, bu durumda uygun erişiminizin olması zaten olabilir. Yöneticinizin size izin ataması gereken durumlarda uygun erişiminizin olmadığı birkaç durum vardır.

- OMS portalında salt okunur Kullanıcı izinleriniz var, ancak Azure portal izin yok. 
- OMS portalında katkıda bulunan izinleridir ancak Azure portal yalnızca okuyucu erişimi vardır.
 
Her iki durumda da yöneticinizin aşağıdaki tablodan uygun rolü el ile ataması gerekir. Bu rolü kaynak grubu veya abonelik düzeyinde atamanızı öneririz.  Bu durumlar için kısa süre içinde daha öngörülü rehberlik sağlanacaktır.

| OMS portalı izni | Azure rolü |
|:---|:---|
| ReadOnly | Log Analytics Okuyucusu |
| Katılımcı | Log Analytics Katkıda Bulunan |
| Yönetici | Sahip | 
 

## <a name="new-workspaces"></a>Yeni çalışma alanları
Artık OMS portalını kullanarak yeni çalışma alanları oluşturamayacaksınız. Azure portal yeni bir çalışma alanı oluşturmak için [Azure portal Log Analytics çalışma alanı oluşturma](../learn/quick-create-workspace.md) bölümündeki yönergeleri izleyin.

## <a name="changes-to-alerts"></a>Uyarılardaki değişiklikler

### <a name="alert-extension"></a>Uyarı Uzantısı  

Uyarılar, var olan uyarılar OMS portalında listelenmeye devam edecek [Azure Portal](alerts-extend.md) , ancak yalnızca Azure Portal yönetebilirsiniz. Uyarılara Log Analytics uyarı REST API veya Log Analytics uyarı kaynağı şablonunu kullanarak programlı bir şekilde eriştiğinizde, API aramalarınızdan, Azure Resource Manager şablonlarındaki ve PowerShell komutlarında eylemler yerine eylem gruplarını kullanmanız gerekir.

### <a name="alert-management-solution"></a>Uyarı yönetimi çözümü
Önceki duyurudan bir değişiklik olarak, [uyarı yönetimi çözümü](alert-management-solution.md) Azure Portal kullanılabilir olmaya devam eder ve tam olarak desteklenir. Çözümü Azure Marketi 'nden yüklemeye devam edebilirsiniz.

Uyarı yönetimi çözümü kullanılabilir olmaya devam ederken Azure [izleyici 'nin Birleşik uyarı arabirimini](alerts-overview.md) kullanarak Azure 'daki tüm uyarıları görselleştirin ve yönetebilirsiniz. Bu yeni deneyim, Azure 'daki birden fazla kaynaktan gelen uyarıları Log Analytics ' den gelen günlük uyarılarını yerel olarak toplar. Azure Izleyici 'nin Birleşik uyarı arabirimini kullanıyorsanız, uyarı yönetimi çözümü yalnızca, uyarıların System Center Operation Manager 'dan Azure 'a tümleştirilmesini etkinleştirmek için gereklidir. Azure Izleyici 'nin Birleşik uyarı arabiriminde, uyarılarınızın dağıtımlarını görebilir, akıllı gruplar aracılığıyla ilgili uyarıların otomatik olarak gruplanmasından yararlanabilir ve zengin filtreler uygularken birden çok aboneliğe ilişkin uyarıları görüntüleyebilirsiniz. Uyarı yönetiminde gelecekte yapılacak ilerleme, bu yeni deneyimden öncelikli olarak sunulacaktır. 

Uyarı yönetimi çözümü tarafından toplanan veriler (uyarı türüne sahip kayıtlar), çözüm çalışma alanı için yüklendiği sürece Log Analytics devam eder. 

## <a name="oms-mobile-app"></a>OMS mobil uygulaması
OMS mobil uygulaması, OMS portalı ile birlikte sunacaktır. OMS mobil uygulaması yerine, BT altyapınız, panolarınız ve kayıtlı sorgularla ilgili bilgilere erişmek için, mobil cihazınızda doğrudan tarayıcınızdan Azure portal erişebilirsiniz. Uyarıları almak için, [Azure eylem GRUPLARıNı](action-groups.md) SMS veya sesli çağrı biçiminde bildirimleri alacak şekilde yapılandırmanız gerekir

## <a name="application-insights-connector-and-solution"></a>Application Insights Bağlayıcısı ve çözüm
[Application Insights Bağlayıcısı](app-insights-connector.md) , Log Analytics çalışma alanına Application Insights verileri eklemenin bir yolunu sağlar. Altyapı ve uygulama verileri genelinde görünürlüğü etkinleştirmek için bu veri yinelemesi gerekiyordu. Mart, 2019 ' de genişletilmiş veri saklama desteğiyle Application Insights ve [birden çok Azure izleyici Application Insights kaynağını görüntüleyebilmenin](../log-query/unify-app-resource-data.md)yanı sıra [çapraz kaynak sorguları](../log-query/cross-workspace-query.md) gerçekleştirebilme olanağı sayesinde, yinelenmeye gerek yoktur Application Insights kaynaklarınızdan alınan veriler Log Analytics gönderin. Ayrıca, bağlayıcı, Log Analytics için uygulama özelliklerinin bir alt kümesini gönderir, ancak çapraz kaynak sorguları size gelişmiş esneklik kazandırır.  

Bu nedenle, Application Insights Bağlayıcısı kullanım dışı bırakılmıştır ve Azure Marketi 'nden, OMS portalı 30 Mart 2019 ' de kullanımdan kaldırılmıştır. Mevcut bağlantılar 30 Haziran 2019 ' e kadar çalışmaya devam edecektir. OMS portalının kullanımdan kaldırılması sayesinde, mevcut bağlantıları portaldan yapılandırmanın ve kaldırmanın bir yolu yoktur. Bu, Ocak 2019 ' de kullanıma sunulan REST API kullanılarak desteklenecektir ve [Azure güncelleştirmelerinde](https://azure.microsoft.com/updates/)bir bildirim gönderilir. 

## <a name="azure-network-security-group-analytics"></a>Azure Ağ Güvenlik Grubu Analizi
[Azure ağ güvenlik grubu analizi çözümü](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) , bulut ağlarında Kullanıcı ve uygulama etkinliğine ilişkin görünürlük sağlayan son başlatılan [Trafik Analizi](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) ile birlikte değişir. Trafik Analizi kuruluşunuzun ağ etkinliğini denetlemenize, uygulamaları ve verileri güvenli hale getirmenize, iş yükü performansını iyileştirmenize ve uyumlu kalmanıza yardımcı olur. 

Bu çözüm NSG akış günlüklerini analiz eder ve aşağıdakiler hakkında öngörüler sağlar.

- Azure ile Internet, genel bulut bölgeleri, sanal ağlar ve alt ağlar arasındaki trafik içindeki trafik akışları.
- Tanıcıların veya adanmış akış koleksiyonu gereçlerine gerek kalmadan ağınızdaki uygulamalar ve protokoller.
- En iyi taliciler, geveze uygulamaları, bulutta VM konuşmaları, trafik etkin noktaları.
- VNET 'lerde, kritik iş hizmetleri ve uygulamalar arasındaki ilişkilerden gelen trafiğin kaynakları ve hedefleri.
- Kötü amaçlı trafik, internet 'e açık bağlantı noktaları, Internet erişimi yapılmaya çalışan uygulamalar veya VM 'Ler dahil güvenlik.
- Sağlama veya yetersiz kullanım sorunlarını ortadan kaldırmanıza yardımcı olan kapasite kullanımı.

Mevcut kayıtlı aramalarınız, uyarılar, panolar çalışmaya devam edecek şekilde Log Analytics NSG günlükleri göndermek için tanılama ayarlarını kullanmaya devam edebilirsiniz. Çözümü zaten yüklemiş olan müşteriler daha fazla bildirimde bulununcaya kadar bu uygulamayı kullanmaya devam edebilir. 5 Eylül 'den itibaren, ağ güvenlik grubu analizi çözümü Market 'ten kaldırılacak ve topluluk aracılığıyla bir [Azure hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)olarak kullanıma sunulacaktır.

## <a name="system-center-operations-manager"></a>System Center Operations Manager
[Operations Manager yönetim grubunuzu Log Analytics bağladıysanız](om-agents.md), hiçbir değişiklik yapmadan çalışmaya devam edecektir. Ancak yeni bağlantılar için, [Operations Management Suite 'i yapılandırmak üzere Microsoft System Center Operations Manager yönetim paketindeki](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)yönergeleri izlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
- OMS portalından Azure portal 'a geçme hakkında rehberlik için bkz. [OMS portalından geçiş Için sık kullanılan sorular Azure portal Log Analytics kullanıcılar](oms-portal-faq.md) .
