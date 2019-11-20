---
title: Azure Izleyici günlükleri dağıtımınızı tasarlama | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de bir çalışma alanı dağıtmaya hazırlama müşterilerine yönelik konular ve öneriler açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 09/20/2019
ms.openlocfilehash: ae737b908aad95f61cef922b493b41752da68f14
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932361"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Azure Izleyici günlükleri dağıtımınızı tasarlama

Azure Izleyici, [günlük](data-platform-logs.md) verilerini bir Azure kaynağı olan bir Log Analytics çalışma alanında ve verilerin toplandığı, toplandığı ve yönetim sınırı olarak hizmet verdiği bir kapsayıcı olarak depolar. Azure aboneliğinizde bir veya daha fazla çalışma alanı dağıtabilmeniz sırasında, ilk dağıtımınızın size uygun maliyetli, yönetilebilir ve ölçeklenebilir bir ücret sunmak için yönergelerimizi takip etmeniz için anlamanız gereken birkaç önemli noktalar vardır. kurumlarınızın ihtiyacı olan dağıtım toplantısı.

Çalışma alanındaki veriler, her biri farklı veri türlerini depolayan ve verileri oluşturan kaynağı temel alan kendi benzersiz özellik kümesine sahip olan tablolar halinde düzenlenir. Çoğu veri kaynağı, Log Analytics çalışma alanında kendi tablolarına yazar.

![Örnek çalışma alanı veri modeli](./media/design-logs-deployment/logs-data-model-01.png)

Bir Log Analytics çalışma alanı şunları sağlar:

* Veri depolama için coğrafi bir konum.
* Önerilen tasarım stratejilerimizden birini izleyerek farklı kullanıcılara erişim hakları vererek veri yalıtımı.
* [Fiyatlandırma katmanı](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [bekletme](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)ve [veri dönüşü](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume)gibi ayarların yapılandırılması için kapsam.

Bu makalede, tasarım ve geçiş konuları, erişim denetimine genel bakış ve BT kuruluşunuz için önerdiğimiz tasarım uygulamalarının anlaşılmasına ilişkin ayrıntılı bir genel bakış sunulmaktadır.



## <a name="important-considerations-for-an-access-control-strategy"></a>Erişim denetimi stratejisi için önemli noktalar

İhtiyaç duyduğunuz çalışma alanı sayısını belirlemek aşağıdaki gereksinimlerinden biri veya daha fazlası tarafından etkilenir:

* Küresel bir şirkettir ve veri egemenliği veya uyumluluk nedenleriyle belirli bölgelerde depolanan günlük verilerine ihtiyacınız vardır.
* Azure kullanıyorsanız ve çalışma alanını, yönettiği Azure kaynaklarıyla aynı bölgede bulundurarak giden veri aktarımı ücretlerini ortadan kaldırmak istiyorsanız.
* Birden çok Departmanı veya iş grubunu yönetebilir ve bunların her birinin kendi verilerini görmesini, ancak başkalarından verileri görmelerini istersiniz. Ayrıca, birleştirilmiş bir çapraz departman veya iş grubu görünümü için bir iş gereksinimi yoktur.

Günümüzde BT kurumları, merkezi bir, merkezi olarak veya her iki yapının de içinden bir arada bir karma olarak modellenmiştir. Sonuç olarak, aşağıdaki çalışma alanı dağıtım modelleri yaygın olarak bu kuruluş yapılarından birine eşlemek için kullanılır:

* **Merkezi**: tüm Günlükler merkezi bir çalışma alanında depolanır ve tek bir takım tarafından yönetilir ve Azure izleyici, ekip başına farklılaştırılan erişim sağlar. Bu senaryoda kolayca yönetilmesi, kaynaklar arasında arama yapmak ve çapraz bağıntılı Günlükler vardır. Çalışma alanı, aboneliğinizdeki birden fazla kaynaktan toplanan veri miktarına bağlı olarak, farklı kullanıcılara erişim denetimi sağlamak için ek yönetim yüküyle önemli ölçüde büyüyebilir.
* **Merkezi olmayan: her**takımın, sahip olduğu ve yönettikleri bir kaynak grubunda kendi çalışma alanı oluşturulmuş ve günlük verileri kaynak başına ayrılmış. Bu senaryoda, çalışma alanı güvenli tutulabilir ve erişim denetimi, kaynak erişimiyle tutarlıdır, ancak günlükleri çapraz bir şekilde ilişkilendirmek zor olabilir. Birçok kaynağın geniş bir görünümüne ihtiyacı olan kullanıcılar verileri anlamlı bir şekilde analiz edemez.
* **Karma**: güvenlik denetimi uyumluluk gereksinimleri bu senaryoyu daha karmaşıklaştırır çünkü birçok kuruluş her iki dağıtım modelini de paralel olarak uygular. Bu, genellikle günlük kapsamındaki boşluklar ile karmaşık, pahalı ve bakım açısından zor bir yapılandırmaya neden olur.

Veri toplamak için Log Analytics aracılarını kullanırken, aracı dağıtımınızı planlamak için aşağıdakileri anlamanız gerekir:

* Windows aracılarından veri toplamak için, [her aracıyı bir veya daha fazla çalışma alanına rapor verecek şekilde yapılandırabilirsiniz](../../azure-monitor/platform/agent-windows.md), bu da bir System Center Operations Manager yönetim grubuna bildirimde bulunur. Windows Aracısı en fazla dört çalışma alanı rapor edebilir.
* Linux Aracısı çoklu barındırıltı desteklemez ve yalnızca tek bir çalışma alanına rapor verebilir.

System Center Operations Manager 2012 R2 veya sonraki bir sürümünü kullanıyorsanız:

* Her bir Operations Manager yönetim grubu [yalnızca bir çalışma alanına bağlanabilir](../platform/om-agents.md). 
* Bir yönetim grubuna rapor veren Linux bilgisayarların doğrudan bir Log Analytics çalışma alanına rapor verecek şekilde yapılandırılması gerekir. Linux bilgisayarlarınız zaten bir çalışma alanına doğrudan raporlama yaptıysanız ve bunları Operations Manager izlemek istiyorsanız, [bir Operations Manager yönetim grubuna raporlamak](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)için aşağıdaki adımları izleyin. 
* Log Analytics Windows aracısını Windows bilgisayarına yükleyebilir ve hem bir çalışma alanıyla hem de bir çalışma alanı ile tümleşik Operations Manager, farklı bir çalışma alanı için rapor verebilirsiniz.

## <a name="access-control-overview"></a>Erişim denetimine genel bakış

Rol tabanlı erişim denetimi (RBAC) sayesinde kullanıcılara ve yalnızca çalışma alanındaki izleme verileriyle çalışmak için ihtiyaç duydukları erişim miktarını verebilirsiniz. Bu, tüm kaynaklarınız üzerinde etkin olan toplanan verileri depolamak için tek bir çalışma alanı kullanarak BT kuruluşunuzun işletim modeliyle uyum kurmanıza olanak tanır. Örneğin, ekibinize Azure sanal makinelerinde (VM 'Ler) barındırılan altyapı hizmetlerinden sorumlu erişim verirsiniz ve bu nedenle yalnızca VM 'Ler tarafından oluşturulan günlüklere erişim elde edersiniz. Bu, yeni kaynak bağlamı günlük modelimizi takip eden bir kaynaktır. Bu modelin temeli bir Azure kaynağı tarafından oluşturulan her günlük kaydına yöneliktir ve bu kaynakla otomatik olarak ilişkilendirilir. Günlükler, kaynaklara göre kapsamı ve RBAC sağlayan bir merkezi çalışma alanına iletilir.

Bir kullanıcının erişimi olan veriler, aşağıdaki tabloda listelenen faktörlerin birleşimiyle belirlenir. Her biri aşağıdaki bölümlerde açıklanmıştır.

| Çarpan | Açıklama |
|:---|:---|
| [Erişim modu](#access-mode) | Kullanıcının çalışma alanına erişmek için kullandığı yöntem.  Kullanılabilir verilerin kapsamını ve uygulanan erişim denetimi modunu tanımlar. |
| [Erişim denetimi modu](#access-control-mode) | Çalışma alanında izinlerin, çalışma alanında veya kaynak düzeyinde uygulanıp uygulanmadığını tanımlayan ayar. |
| [İzinler](manage-access.md) | Çalışma alanı veya kaynak için bir kişiye veya kullanıcı grubuna uygulanan izinler. Kullanıcının erişimi olacak verileri tanımlar. |
| [Tablo düzeyi RBAC](manage-access.md#table-level-rbac) | Erişim modundan veya erişim denetimi modundan bağımsız olarak tüm kullanıcılara uygulanan isteğe bağlı ayrıntılı izinler. Bir kullanıcının erişebileceği veri türlerini tanımlar. |

## <a name="access-mode"></a>Erişim modu

*Erişim modu* , bir kullanıcının Log Analytics çalışma alanına nasıl eriştiğini ve erişebileceği verilerin kapsamını nasıl tanımladığını gösterir. 

Kullanıcılara verilere erişim için iki seçenek vardır:

* **Çalışma alanı bağlamı**: izninizin bulunduğu çalışma alanındaki tüm günlükleri görüntüleyebilirsiniz. Bu moddaki sorgular, çalışma alanındaki tüm tablolardaki tüm verilerin kapsamına alınır. Bu, Azure portal **Azure izleyici** menüsünden **Günlükler** ' i seçerken olduğu gibi, kapsam olarak çalışma alanıyla erişildiğinde kullanılan erişim modudur.

    ![Çalışma alanından Log Analytics bağlamı](./media/design-logs-deployment/query-from-workspace.png)

* **Kaynak bağlamı**: belirli bir kaynak, kaynak grubu veya abonelik için çalışma alanına eriştiğinizde, örneğin Azure Portal bir kaynak menüsünden **Günlükler** ' i seçerken, yalnızca sahip olduğunuz tüm tablolardaki kaynakların günlüklerini görüntüleyebilirsiniz. erişimi. Bu moddaki sorgular yalnızca söz konusu kaynakla ilişkili verilere göre kapsamlandırılır. Bu mod, ayrıntılı RBAC de sunar.

    ![Kaynaktan Log Analytics bağlamı](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Günlükler yalnızca ilgili kaynakla düzgün şekilde ilişkilendirildiklerinde kaynak bağlamı sorguları için kullanılabilir. Şu anda aşağıdaki kaynaklarda sınırlamalar var:
    > - Azure dışındaki bilgisayarlar
    > - Service Fabric
    > - Application Insights
    >
    > Bir sorgu çalıştırıp ilgilendiğiniz kayıtları inceleyerek günlüklerin kaynakları ile düzgün bir şekilde ilişkilendiriliyorsa test edebilirsiniz. Doğru kaynak KIMLIĞI [_Resourceıd](log-standard-properties.md#_resourceid) özelliğinde ise, veriler kaynak merkezli sorgular tarafından kullanılabilir.

Azure Izleyici, günlük aramasını gerçekleştirdiğiniz bağlama göre otomatik olarak doğru modu belirler. Kapsam her zaman Log Analytics en sol üst bölümünde sunulur.

### <a name="comparing-access-modes"></a>Erişim modlarını karşılaştırma

Aşağıdaki tabloda erişim modları özetlenmektedir:

| | Çalışma alanı-bağlam | Kaynak bağlamı |
|:---|:---|:---|
| Her bir model kim içindir? | Yönetim Merkezi. Veri toplamayı ve çok çeşitli kaynaklara erişmesi gereken kullanıcıları yapılandırması gereken yöneticiler. Şu anda Azure dışındaki kaynaklar için günlüklere erişmesi gereken kullanıcılar için de gereklidir. | Uygulama takımları. İzlenmekte olan Azure kaynaklarının yöneticileri. |
| Kullanıcı günlükleri görüntülemek için ne gerekir? | Çalışma alanı izinleri. [Çalışma alanı izinlerini kullanarak erişimi yönetme](manage-access.md#manage-access-using-workspace-permissions)Içindeki **çalışma alanı izinlerine** bakın. | Kaynağa yönelik okuma erişimi. Bkz. [Azure izinleri kullanarak erişimi yönetme](manage-access.md#manage-access-using-azure-permissions)içindeki **kaynak izinleri** . İzinler devralınabilir (örneğin, kapsayan kaynak grubundan) veya doğrudan kaynağa atanabilir. Kaynak için günlüklere izin verilecek izinler otomatik olarak atanır. |
| İzinlerin kapsamı nedir? | Alanında. Çalışma alanına erişimi olan kullanıcılar, çalışma alanındaki tüm günlükleri izinleri olan tablolardan sorgulayabilir. Bkz. [tablo erişim denetimi](manage-access.md#table-level-rbac) | Azure kaynağı. Kullanıcı herhangi bir çalışma alanından erişimi olan belirli kaynaklar, kaynak grupları veya abonelikler için günlükleri sorgulayabilir, ancak diğer kaynakların günlüklerini sorgulayamaz. |
| Kullanıcı günlüklere nasıl erişebilir? | <ul><li>**Günlükleri** **Azure izleyici** menüsünden başlatın.</li></ul> <ul><li>**Log Analytics çalışma alanlarından** **günlükleri** başlatın.</li></ul> <ul><li>Azure Izleyici [çalışma kitaplarından](../visualizations.md#workbooks).</li></ul> | <ul><li>Azure kaynağı menüsündeki **günlükleri** Başlat</li></ul> <ul><li>**Günlükleri** **Azure izleyici** menüsünden başlatın.</li></ul> <ul><li>**Log Analytics çalışma alanlarından** **günlükleri** başlatın.</li></ul> <ul><li>Azure Izleyici [çalışma kitaplarından](../visualizations.md#workbooks).</li></ul> |

## <a name="access-control-mode"></a>Erişim denetimi modu

*Erişim denetimi modu* , çalışma alanı için izinlerin nasıl belirlendiğini tanımlayan her çalışma alanındaki bir ayardır.

* **Çalışma alanı Izinleri iste**: Bu denetim modu parçalı RBAC 'e izin vermez. Bir kullanıcının çalışma alanına erişmesi için, çalışma alanına veya belirli tablolara izin verilmesi gerekir.

    Bir kullanıcı çalışma alanına, çalışma alanı bağlamı modundan sonra erişirse, erişim izni verilen herhangi bir tablodaki tüm verilere erişebilirler. Bir Kullanıcı, kaynak bağlamı modundan sonra çalışma alanına eriştiğinde, erişim izni verilen her tabloda yalnızca o kaynağa yönelik verilere erişebilirler.

    Bu, 2019 Mart 'tan önce oluşturulan tüm çalışma alanları için varsayılan ayardır.

* **Kaynak veya çalışma alanı Izinlerini kullanın**: Bu denetim modu parçalı RBAC 'e izin verir. Kullanıcılara yalnızca Azure `read` izni atayarak görüntüleyebilecekleri kaynaklarla ilişkili verilere erişim verilebilir. 

    Bir kullanıcı çalışma alanı bağlam modunda çalışma alanına eriştiğinde, çalışma alanı izinleri geçerlidir. Bir Kullanıcı, kaynak-bağlam modundaki çalışma alanına eriştiğinde yalnızca kaynak izinleri doğrulanır ve çalışma alanı izinleri yoksayılır. Çalışma alanı izinlerinden kaldırarak ve kaynak izinlerinin tanınmasını sağlayarak bir kullanıcı için RBAC 'yi etkinleştirin.

    Bu, Mart 2019 ' den sonra oluşturulan tüm çalışma alanları için varsayılan ayardır.

    > [!NOTE]
    > Bir kullanıcının çalışma alanında yalnızca kaynak izinleri varsa, çalışma alanına yalnızca çalışma alanı erişim modunun **kaynak veya çalışma alanı Izinlerini kullanacak**şekilde ayarlandığını varsayarak kaynak bağlam modunu kullanarak erişebilirler.

Portalda erişim denetimi modunu değiştirme hakkında bilgi edinmek için, PowerShell ile veya Kaynak Yöneticisi şablonu kullanarak bkz. [erişim denetimi modunu yapılandırma](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Alım birimi hız sınırı

Azure Izleyici, her ay büyüyen bir hızda çok sayıda müşteriye hizmet veren binlerce müşteriyi sunan yüksek ölçekli bir veri hizmetidir. Varsayılan alma hızı eşiği, çalışma alanı başına **500 MB/dk** olarak ayarlanır. Verileri daha yüksek bir fiyata tek bir çalışma alanına gönderirseniz, bazı veriler bırakılır ve eşik aşılmaya devam edilirken her 6 saatte bir olay, çalışma alanınızda *işlem* tablosuna gönderilir. Alım biriminiz, hız sınırını aşmaya devam ediyorsa veya bir süre önce bu sınıra ulaşmayı bekliyorsanız, bir destek isteği açarak çalışma alanınıza bir artış isteğinde bulunabilir.
 
Çalışma alanınızdaki bu tür bir olay hakkında bildirim almak için, sıfırdan farklı sonuç sayısına sahip uyarı mantığı temeli ile aşağıdaki sorguyu kullanarak bir [günlük uyarı kuralı](alerts-log.md) oluşturun.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Öneriler

![Kaynak bağlamı tasarım örneği](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Bu senaryo, BT kuruluşları aboneliğinizdeki, veri egemenliği veya mevzuata uyumluluğuyla sınırlandırılmamış ya da kaynaklarınızın dağıtıldığı bölgelerle eşleşmesi gereken tek bir çalışma alanı tasarımını ele almaktadır. Kuruluşların güvenlik ve BT yöneticinizin, Azure erişim yönetimiyle geliştirilmiş tümleştirmeden ve daha güvenli erişim denetimiyle yararlanmasını sağlar.

Farklı ekipler tarafından tutulan Application Insights ve VM'ler için Azure İzleyici gibi tüm kaynaklar, izleme çözümleri ve Öngörüler, toplanan günlük verilerini BT kuruluşlarına iletecek şekilde yapılandırılmıştır. Merkezi paylaşılan çalışma alanı. Her ekipte bulunan kullanıcılara, erişimi verilen kaynaklar için günlüklere erişim verilir.

Çalışma alanı mimarinizi dağıttıktan sonra Azure [ilkesi](../../governance/policy/overview.md)ile Azure kaynakları üzerinde bunu zorunlu kılabilirsiniz. Bu, ilke tanımlamak ve Azure kaynaklarınızla uyumluluğun yanı sıra tüm tanılama günlüklerini belirli bir çalışma alanına gönderecek şekilde sağlamak için bir yol sağlar. Örneğin, Azure sanal makineler veya sanal makine ölçek kümeleri ile, çalışma alanı uyumluluğunu ve rapor sonuçlarını değerlendiren mevcut ilkeleri kullanabilir veya uyumlu değil olarak düzeltmek için özelleştirebilirsiniz.  

## <a name="workspace-consolidation-migration-strategy"></a>Çalışma alanı birleştirme geçiş stratejisi

Birden çok çalışma alanı dağıtmış olan ve kaynak bağlamı erişim modeliyle birleştirme ile ilgilenilen müşteriler için, önerilen erişim modeline geçiş yapmak için artımlı bir yaklaşım yapmanızı ve bunu elde etmeye çalışmamayı öneririz. hızlı veya kararlılığı kolay. Makul bir zaman çizelgesini planlamak, geçirmek, doğrulamak ve devre dışı bırakmak için aşamalı bir yaklaşımdan sonra, planlanmamış olayların veya bulut işlemlerinizin beklenmedik etkilerden etkilenmenize yardımcı olur. Uyumluluk veya iş nedenleriyle ilgili bir veri saklama ilkeniz yoksa, işlem sırasında geçirdiğiniz çalışma alanındaki verileri saklamak için uygun süreyi değerlendirmeniz gerekir. Kaynakları paylaşılan çalışma alanına raporlamak için yeniden yapılandırırken, yine de özgün çalışma alanındaki verileri gerekli şekilde çözümleyebilirsiniz. Geçiş işlemi tamamlandıktan sonra, verileri bekletme döneminin sonundan önce orijinal çalışma alanında tutmayı kapsamlıyorsanız, silin.

Bu modele geçişinizi planlarken, aşağıdakileri göz önünde bulundurun:

* Hangi sektör düzenlemelerine ve ile uyumlu olması gereken veri saklama ilkelerine ilişkin dahili ilkelere anlayın.
* Uygulama ekiplerinizin mevcut kaynak bağlam işlevselliği içinde çalıştığından emin olun.
* Üretim ortamında uygulamadan önce, uygulama ekipleriniz için kaynaklara verilen erişimi ve bir geliştirme ortamında test edin.
* Çalışma alanını, **kaynak veya çalışma alanı Izinlerini kullanacak**şekilde yapılandırın.
* Çalışma alanını okumak ve sorgulamak için uygulama ekiplerinin iznini kaldırın.
* Tüm izleme çözümlerini, kapsayıcılar için Azure Izleyici ve/veya VM'ler için Azure İzleyici, Otomasyon hesabınız ve Güncelleştirme Yönetimi, başlatma/durdurma VM 'Leri gibi yönetim çözümleriniz, orijinalde dağıtılan alanında.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda önerilen güvenlik izinlerini ve denetimleri uygulamak için [günlüklere erişimi yönet](manage-access.md)' i gözden geçirin.