---
title: Azure Izleyici günlükleri dağıtımınızı tasarlama | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de bir çalışma alanı dağıtmaya hazırlama müşterilerine yönelik konular ve öneriler açıklanmaktadır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: 8502c35a145e4a041f9d44b8396fe16f5db3febc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384213"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Azure İzleyici Günlükleri dağıtımınızı tasarlama

Azure Izleyici, [günlük](data-platform-logs.md) verilerini bir Azure kaynağı olan bir Log Analytics çalışma alanında ve verilerin toplandığı, toplandığı ve yönetim sınırı olarak hizmet verdiği bir kapsayıcı olarak depolar. Azure aboneliğinizde bir veya daha fazla çalışma alanı dağıtabileceğiniz sürece, kuruluşunuzun ihtiyaçlarına uygun maliyetli, yönetilebilir ve ölçeklenebilir bir dağıtım sunmak için ilk dağıtımınızın kılavuzumuzu takip etmek amacıyla anlamanız gereken birkaç önemli noktalar vardır.

Çalışma alanındaki veriler, her biri farklı veri türlerini depolayan ve verileri oluşturan kaynağı temel alan kendi benzersiz özellik kümesine sahip olan tablolar halinde düzenlenir. Çoğu veri kaynağı, Log Analytics çalışma alanında kendi tablolarına yazar.

![Örnek çalışma alanı veri modeli](./media/design-logs-deployment/logs-data-model-01.png)

Bir Log Analytics çalışma alanı şunları sağlar:

* Veri depolama için coğrafi bir konum.
* Önerilen tasarım stratejilerimizden birini izleyerek farklı kullanıcılara erişim hakları vererek veri yalıtımı.
* [Fiyatlandırma katmanı](./manage-cost-storage.md#changing-pricing-tier), [bekletme](./manage-cost-storage.md#change-the-data-retention-period)ve [veri dönüşü](./manage-cost-storage.md#manage-your-maximum-daily-data-volume)gibi ayarların yapılandırılması için kapsam.

Çalışma alanları fiziksel kümeler üzerinde barındırılır. Varsayılan olarak, sistem bu kümeleri oluşturuyor ve yönetiyor. 4 TB 'den fazla/günden fazla alan müşterilerin çalışma alanları için kendi adanmış kümelerini oluşturması beklenir; bu sayede daha iyi denetim ve daha yüksek alım oranı sağlanır.

Bu makalede, tasarım ve geçiş konuları, erişim denetimine genel bakış ve BT kuruluşunuz için önerdiğimiz tasarım uygulamalarının anlaşılmasına ilişkin ayrıntılı bir genel bakış sunulmaktadır.



## <a name="important-considerations-for-an-access-control-strategy"></a>Erişim denetimi stratejisi için önemli noktalar

İhtiyaç duyduğunuz çalışma alanı sayısını belirlemek aşağıdaki gereksinimlerinden biri veya daha fazlası tarafından etkilenir:

* Küresel bir şirkettir ve veri egemenliği veya uyumluluk nedenleriyle belirli bölgelerde depolanan günlük verilerine ihtiyacınız vardır.
* Azure kullanıyorsanız ve çalışma alanını, yönettiği Azure kaynaklarıyla aynı bölgede bulundurarak giden veri aktarımı ücretlerini ortadan kaldırmak istiyorsanız.
* Birden çok Departmanı veya iş grubunu yönetebilir ve bunların her birinin kendi verilerini görmesini, ancak başkalarından verileri görmelerini istersiniz. Ayrıca, birleştirilmiş bir çapraz departman veya iş grubu görünümü için bir iş gereksinimi yoktur.

Günümüzde BT kurumları, merkezi bir, merkezi olarak veya her iki yapının de içinden bir arada bir karma olarak modellenmiştir. Sonuç olarak, aşağıdaki çalışma alanı dağıtım modelleri yaygın olarak bu kuruluş yapılarından birine eşlemek için kullanılır:

* **Merkezi**: tüm Günlükler merkezi bir çalışma alanında depolanır ve tek bir takım tarafından yönetilir ve Azure izleyici, ekip başına farklılaştırılan erişim sağlar. Bu senaryoda kolayca yönetilmesi, kaynaklar arasında arama yapmak ve çapraz bağıntılı Günlükler vardır. Çalışma alanı, aboneliğinizdeki birden fazla kaynaktan toplanan veri miktarına bağlı olarak, farklı kullanıcılara erişim denetimi sağlamak için ek yönetim yüküyle önemli ölçüde büyüyebilir. Bu model "hub ve bağlı bileşen" olarak bilinir.
* **Merkezi olmayan: her** takımın, sahip olduğu ve yönettikleri bir kaynak grubunda kendi çalışma alanı oluşturulmuş ve günlük verileri kaynak başına ayrılmış. Bu senaryoda, çalışma alanı güvenli tutulabilir ve erişim denetimi, kaynak erişimiyle tutarlıdır, ancak günlükleri çapraz bir şekilde ilişkilendirmek zor olabilir. Birçok kaynağın geniş bir görünümüne ihtiyacı olan kullanıcılar verileri anlamlı bir şekilde analiz edemez.
* **Karma**: güvenlik denetimi uyumluluk gereksinimleri bu senaryoyu daha karmaşıklaştırır çünkü birçok kuruluş her iki dağıtım modelini de paralel olarak uygular. Bu, genellikle günlük kapsamındaki boşluklar ile karmaşık, pahalı ve bakım açısından zor bir yapılandırmaya neden olur.

Veri toplamak için Log Analytics aracılarını kullanırken, aracı dağıtımınızı planlamak için aşağıdakileri anlamanız gerekir:

* Windows aracılarından veri toplamak için, [her aracıyı bir veya daha fazla çalışma alanına rapor verecek şekilde yapılandırabilirsiniz](./../agents/agent-windows.md), bu da bir System Center Operations Manager yönetim grubuna bildirimde bulunur. Windows Aracısı en fazla dört çalışma alanı rapor edebilir.
* Linux Aracısı çoklu barındırıltı desteklemez ve yalnızca tek bir çalışma alanına rapor verebilir.

System Center Operations Manager 2012 R2 veya sonraki bir sürümünü kullanıyorsanız:

* Her bir Operations Manager yönetim grubu [yalnızca bir çalışma alanına bağlanabilir](../agents/om-agents.md). 
* Bir yönetim grubuna rapor veren Linux bilgisayarların doğrudan bir Log Analytics çalışma alanına rapor verecek şekilde yapılandırılması gerekir. Linux bilgisayarlarınız zaten bir çalışma alanına doğrudan raporlama yaptıysanız ve bunları Operations Manager izlemek istiyorsanız, [bir Operations Manager yönetim grubuna raporlamak](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)için aşağıdaki adımları izleyin. 
* Log Analytics Windows aracısını Windows bilgisayarına yükleyebilir ve hem bir çalışma alanıyla hem de bir çalışma alanı ile tümleşik Operations Manager, farklı bir çalışma alanı için rapor verebilirsiniz.

## <a name="access-control-overview"></a>Erişim denetimine genel bakış

Azure rol tabanlı erişim denetimi (Azure RBAC) sayesinde kullanıcılara yalnızca çalışma alanındaki izleme verileriyle çalışmak için ihtiyaç duydukları erişim miktarını verebilirsiniz. Bu, tüm kaynaklarınız üzerinde etkin olan toplanan verileri depolamak için tek bir çalışma alanı kullanarak BT kuruluşunuzun işletim modeliyle uyum kurmanıza olanak tanır. Örneğin, ekibinize Azure sanal makinelerinde (VM 'Ler) barındırılan altyapı hizmetlerinden sorumlu erişim verirsiniz ve bu nedenle yalnızca VM 'Ler tarafından oluşturulan günlüklere erişim elde edersiniz. Bu, yeni kaynak bağlamı günlük modelimizi takip eden bir kaynaktır. Bu modelin temeli bir Azure kaynağı tarafından oluşturulan her günlük kaydına yöneliktir ve bu kaynakla otomatik olarak ilişkilendirilir. Günlükler, kaynaklara dayalı olarak kapsam ve Azure RBAC sağlayan bir merkezi çalışma alanına iletilir.

Bir kullanıcının erişimi olan veriler, aşağıdaki tabloda listelenen faktörlerin birleşimiyle belirlenir. Her biri aşağıdaki bölümlerde açıklanmıştır.

| Faktör | Açıklama |
|:---|:---|
| [Erişim modu](#access-mode) | Kullanıcının çalışma alanına erişmek için kullandığı yöntem.  Kullanılabilir verilerin kapsamını ve uygulanan erişim denetimi modunu tanımlar. |
| [Erişim denetimi modu](#access-control-mode) | Çalışma alanında izinlerin, çalışma alanında veya kaynak düzeyinde uygulanıp uygulanmadığını tanımlayan ayar. |
| [İzinler](./manage-access.md) | Çalışma alanı veya kaynak için bir kişiye veya kullanıcı grubuna uygulanan izinler. Kullanıcının erişimi olacak verileri tanımlar. |
| [Tablo düzeyinde Azure RBAC](./manage-access.md#table-level-azure-rbac) | Erişim modundan veya erişim denetimi modundan bağımsız olarak tüm kullanıcılara uygulanan isteğe bağlı ayrıntılı izinler. Bir kullanıcının erişebileceği veri türlerini tanımlar. |

## <a name="access-mode"></a>Erişim modu

*Erişim modu* , bir kullanıcının Log Analytics çalışma alanına nasıl eriştiğini ve erişebileceği verilerin kapsamını nasıl tanımladığını gösterir. 

Kullanıcılara verilere erişim için iki seçenek vardır:

* **Çalışma alanı bağlamı**: izninizin bulunduğu çalışma alanındaki tüm günlükleri görüntüleyebilirsiniz. Bu moddaki sorgular, çalışma alanındaki tüm tablolardaki tüm verilerin kapsamına alınır. Bu, Azure portal **Azure izleyici** menüsünden **Günlükler** ' i seçerken olduğu gibi, kapsam olarak çalışma alanıyla erişildiğinde kullanılan erişim modudur.

    ![Çalışma alanından Log Analytics bağlamı](./media/design-logs-deployment/query-from-workspace.png)

* **Kaynak bağlamı**: belirli bir kaynak, kaynak grubu veya abonelik için çalışma alanına eriştiğinizde, örneğin Azure Portal bir kaynak menüsünden **Günlükler** ' i seçtiğinizde, yalnızca erişiminiz olan tüm tablolardaki kaynakların günlüklerini görüntüleyebilirsiniz. Bu moddaki sorgular yalnızca söz konusu kaynakla ilişkili verilere göre kapsamlandırılır. Bu mod, ayrıntılı Azure RBAC 'yi de mümkün.

    ![Kaynaktan Log Analytics bağlamı](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Günlükler yalnızca ilgili kaynakla düzgün şekilde ilişkilendirildiklerinde kaynak bağlamı sorguları için kullanılabilir. Şu anda aşağıdaki kaynaklarda sınırlamalar var:
    > - Azure dışındaki bilgisayarlar-yalnızca [sunucular Için Azure Arc](../../azure-arc/servers/index.yml) aracılığıyla kaynak bağlamı için desteklenir
    > - Service Fabric
    > - Application Insights-yalnızca [çalışma alanı tabanlı Application Insights kaynağı](../app/create-workspace-resource.md) kullanılırken kaynak bağlamı için desteklenir
    >
    > Bir sorgu çalıştırıp ilgilendiğiniz kayıtları inceleyerek günlüklerin kaynakları ile düzgün bir şekilde ilişkilendiriliyorsa test edebilirsiniz. Doğru kaynak KIMLIĞI [_ResourceId](./log-standard-columns.md#_resourceid) özellikte ise, veriler kaynak merkezli sorgular tarafından kullanılabilir.

Azure Izleyici, günlük aramasını gerçekleştirdiğiniz bağlama göre otomatik olarak doğru modu belirler. Kapsam her zaman Log Analytics en sol üst bölümünde sunulur.

### <a name="comparing-access-modes"></a>Erişim modlarını karşılaştırma

Aşağıdaki tabloda erişim modları özetlenmektedir:

| Sorun | Çalışma alanı bağlamı | Kaynak bağlamı |
|:---|:---|:---|
| Her bir model kim içindir? | Yönetim Merkezi. Veri toplamayı ve çok çeşitli kaynaklara erişmesi gereken kullanıcıları yapılandırması gereken yöneticiler. Şu anda Azure dışındaki kaynaklar için günlüklere erişmesi gereken kullanıcılar için de gereklidir. | Uygulama takımları. İzlenmekte olan Azure kaynaklarının yöneticileri. |
| Kullanıcı günlükleri görüntülemek için ne gerekir? | Çalışma alanı izinleri. [Çalışma alanı izinlerini kullanarak erişimi yönetme](./manage-access.md#manage-access-using-workspace-permissions)Içindeki **çalışma alanı izinlerine** bakın. | Kaynağa yönelik okuma erişimi. Bkz. [Azure izinleri kullanarak erişimi yönetme](./manage-access.md#manage-access-using-azure-permissions)içindeki **kaynak izinleri** . İzinler devralınabilir (örneğin, kapsayan kaynak grubundan) veya doğrudan kaynağa atanabilir. Kaynak için günlüklere izin verilecek izinler otomatik olarak atanır. |
| İzinlerin kapsamı nedir? | Alanında. Çalışma alanına erişimi olan kullanıcılar, çalışma alanındaki tüm günlükleri izinleri olan tablolardan sorgulayabilir. Bkz. [tablo erişim denetimi](./manage-access.md#table-level-azure-rbac) | Azure kaynağı. Kullanıcı herhangi bir çalışma alanından erişimi olan belirli kaynaklar, kaynak grupları veya abonelikler için günlükleri sorgulayabilir, ancak diğer kaynakların günlüklerini sorgulayamaz. |
| Kullanıcı günlüklere nasıl erişebilir? | <ul><li>**Günlükleri** **Azure izleyici** menüsünden başlatın.</li></ul> <ul><li>**Log Analytics çalışma alanlarından** **günlükleri** başlatın.</li></ul> <ul><li>Azure Izleyici [çalışma kitaplarından](../visualizations.md#workbooks).</li></ul> | <ul><li>Azure kaynağı menüsündeki **günlükleri** Başlat</li></ul> <ul><li>**Günlükleri** **Azure izleyici** menüsünden başlatın.</li></ul> <ul><li>**Log Analytics çalışma alanlarından** **günlükleri** başlatın.</li></ul> <ul><li>Azure Izleyici [çalışma kitaplarından](../visualizations.md#workbooks).</li></ul> |

## <a name="access-control-mode"></a>Erişim denetimi modu

*Erişim denetimi modu* , çalışma alanı için izinlerin nasıl belirlendiğini tanımlayan her çalışma alanındaki bir ayardır.

* **Çalışma alanı Izinleri iste**: Bu denetim modu, AYRıNTıLı Azure RBAC 'e izin vermez. Bir kullanıcının çalışma alanına erişmesi için, çalışma alanına veya belirli tablolara izin verilmesi gerekir.

    Bir kullanıcı çalışma alanına, çalışma alanı bağlamı modundan sonra erişirse, erişim izni verilen herhangi bir tablodaki tüm verilere erişebilirler. Bir Kullanıcı, kaynak bağlamı modundan sonra çalışma alanına eriştiğinde, erişim izni verilen her tabloda yalnızca o kaynağa yönelik verilere erişebilirler.

    Bu, 2019 Mart 'tan önce oluşturulan tüm çalışma alanları için varsayılan ayardır.

* **Kaynak veya çalışma alanı Izinlerini kullanın**: Bu denetim modu, AYRıNTıLı Azure RBAC sağlar. Kullanıcılara, yalnızca Azure izni atayarak görüntüleyebileceği kaynaklarla ilişkili verilere erişim izni verilebilir `read` . 

    Bir kullanıcı çalışma alanı bağlam modunda çalışma alanına eriştiğinde, çalışma alanı izinleri geçerlidir. Bir Kullanıcı, kaynak-bağlam modundaki çalışma alanına eriştiğinde yalnızca kaynak izinleri doğrulanır ve çalışma alanı izinleri yoksayılır. Bir kullanıcı için Azure RBAC 'yi, çalışma alanı izinlerinden kaldırarak ve kaynak izinlerinin tanınmasını sağlayarak etkinleştirin.

    Bu, Mart 2019 ' den sonra oluşturulan tüm çalışma alanları için varsayılan ayardır.

    > [!NOTE]
    > Bir kullanıcının çalışma alanında yalnızca kaynak izinleri varsa, çalışma alanına yalnızca çalışma alanı erişim modunun **kaynak veya çalışma alanı Izinlerini kullanacak** şekilde ayarlandığını varsayarak kaynak bağlam modunu kullanarak erişebilirler.

Portalda erişim denetimi modunu değiştirme hakkında bilgi edinmek için, PowerShell ile veya Kaynak Yöneticisi şablonu kullanarak bkz. [erişim denetimi modunu yapılandırma](./manage-access.md#configure-access-control-mode).

## <a name="scale-and-ingestion-volume-rate-limit"></a>Ölçek ve Alım birimi hız sınırı

Azure Izleyici, her ay büyüyen bir hızda petabaytlarca veri gönderen binlerce müşteriyi sunan yüksek ölçekli bir veri hizmetidir. Çalışma alanları, depolama alanlarında sınırlı değildir ve verilerin petabaytlarca birine büyüyebilir. Ölçek nedeniyle çalışma alanlarını bölme gereksinimi yoktur.

Azure izleyici müşterilerini ve arka uç altyapısını korumak ve yalıtmak için, ani ve (sel durumlarından korunmak üzere tasarlanan bir varsayılan alım oranı sınırı vardır. Varsayılan hız sınırı **6 GB/dakika** 'tır ve normal alımı etkinleştirmek üzere tasarlanmıştır. Alma birimi sınır ölçümü hakkında daha fazla bilgi için bkz. [Azure izleyici hizmet limitleri](../service-limits.md#data-ingestion-volume-rate).

4 TB/gün 'tan az alan müşteriler genellikle bu limitleri karşılamaz. Daha yüksek birimler alan veya normal işlemlerinin bir parçası olarak ani artışlar olan müşteriler, alım oranı sınırının gerçekleştiği [adanmış kümelere](./logs-dedicated-clusters.md) geçmeyi düşünmelidir.

Alma hızı limiti sınırı etkinleştirildiğinde veya eşiğin %80 ' ine ulaşıldığında, çalışma alanınızdaki *işlem* tablosuna bir olay eklenir. İzlemeniz ve bir uyarı oluşturmanız önerilir. Veri alımı ile daha fazla ayrıntı için bkz. [birim oranı](../service-limits.md#data-ingestion-volume-rate).


## <a name="recommendations"></a>Öneriler

![Kaynak bağlamı tasarım örneği](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Bu senaryo, BT kuruluşunuzun aboneliğindeki veri egemenliği veya mevzuata uyumluluğuyla sınırlandırılmamış ya da kaynaklarınızın dağıtıldığı bölgelerle eşleşmesi gereken tek bir çalışma alanı tasarımını ele almaktadır. Kuruluşunuzun güvenliğine ve BT yöneticisine, Azure erişim yönetimi ve daha güvenli erişim denetimi ile geliştirilmiş tümleştirmeden yararlanma olanağı sağlar.

Farklı ekipler tarafından tutulan altyapı ve uygulamaları destekleyen tüm kaynaklar, izleme çözümleri ve Application Insights Öngörüler, toplanan günlük verilerini BT kuruluşunun merkezi paylaşılan çalışma alanına iletecek şekilde yapılandırılmıştır. Her ekipte bulunan kullanıcılara, erişimi verilen kaynaklar için günlüklere erişim verilir.

Çalışma alanı mimarinizi dağıttıktan sonra Azure [ilkesi](../../governance/policy/overview.md)ile Azure kaynakları üzerinde bunu zorunlu kılabilirsiniz. Bu, ilke tanımlamak ve Azure kaynaklarınızla uyumluluğu sağlamak için bir yol sağlar, böylece tüm kaynak günlüklerini belirli bir çalışma alanına gönderir. Örneğin, Azure sanal makineler veya sanal makine ölçek kümeleri ile, çalışma alanı uyumluluğunu ve rapor sonuçlarını değerlendiren mevcut ilkeleri kullanabilir veya uyumlu değil olarak düzeltmek için özelleştirebilirsiniz.  

## <a name="workspace-consolidation-migration-strategy"></a>Çalışma alanı birleştirme geçiş stratejisi

Birden çok çalışma alanı dağıtmış olan ve kaynak bağlamı erişim modeliyle birleştirme ile ilgilenilen müşteriler için, önerilen erişim modeline geçiş yapmak için artımlı bir yaklaşım yapmanızı ve bu hızlıca veya kararlılığı elde etmeye çalışmamanız önerilir. Makul bir zaman çizelgesini planlamak, geçirmek, doğrulamak ve devre dışı bırakmak için aşamalı bir yaklaşımdan sonra, planlanmamış olayların veya bulut işlemlerinizin beklenmedik etkilerden etkilenmenize yardımcı olur. Uyumluluk veya iş nedenleriyle ilgili bir veri saklama ilkeniz yoksa, işlem sırasında geçirdiğiniz çalışma alanındaki verileri saklamak için uygun süreyi değerlendirmeniz gerekir. Kaynakları paylaşılan çalışma alanına raporlamak için yeniden yapılandırırken, yine de özgün çalışma alanındaki verileri gerekli şekilde çözümleyebilirsiniz. Geçiş işlemi tamamlandıktan sonra, verileri bekletme döneminin sonundan önce orijinal çalışma alanında tutmayı kapsamlıyorsanız, silin.

Bu modele geçişinizi planlarken, aşağıdakileri göz önünde bulundurun:

* Hangi sektör düzenlemelerine ve ile uyumlu olması gereken veri saklama ilkelerine ilişkin dahili ilkelere anlayın.
* Uygulama ekiplerinizin mevcut kaynak bağlam işlevselliği içinde çalıştığından emin olun.
* Üretim ortamında uygulamadan önce, uygulama ekipleriniz için kaynaklara verilen erişimi ve bir geliştirme ortamında test edin.
* Çalışma alanını, **kaynak veya çalışma alanı Izinlerini kullanacak** şekilde yapılandırın.
* Çalışma alanını okumak ve sorgulamak için uygulama ekiplerinin iznini kaldırın.
* Tüm izleme çözümlerini, kapsayıcı öngörüleri ve/veya VM'ler için Azure İzleyici, Otomasyon hesaplarınız ve Güncelleştirme Yönetimi, başlangıç/durdurma VM 'Leri gibi yönetim çözümleriniz ve özgün çalışma alanında dağıtılan yönetim çözümleri için etkinleştirin ve yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda önerilen güvenlik izinlerini ve denetimleri uygulamak için [günlüklere erişimi yönet](./manage-access.md)' i gözden geçirin.
