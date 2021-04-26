---
title: İş sürekliliği ve olağanüstü durum kurtarma
description: Verilerinizi korumak, kesintiye uğratan olayları hızla kurtarmak, kritik iş işlevleri için gereken kaynakları geri yüklemek ve Azure Logic Apps için iş sürekliliği sağlamak için stratejinizi tasarlayın
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: f974a99c59b19b5df7bf6ffcc66c2dc133743f0a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790548"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Azure Logic Apps için iş sürekliliği ve olağanüstü durum kurtarma

İşletmenizin ve müşterilerinizin öngörülemeyen etkinliklerin etkisini ve etkilerini azaltmaya yardımcı olmak için, verileri koruyabilmeniz, kritik iş işlevlerini destekleyen kaynakları hızlıca geri yüklemek ve [ *iş sürekliliği* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)sürdürmek için işlemleri çalıştırmaya devam etmek üzere bir [ *olağanüstü durum kurtarma* (Dr)](https://en.wikipedia.org/wiki/Disaster_recovery) çözümüne sahip olduğunuzdan emin olun. Örneğin, kesintiler, temeldeki altyapıda veya depolama, ağ ya da işlem kaynakları, kurtarılamaz uygulama arızaları, hatta tam bir veri merkezi kaybıyla kesintileri, kayıpları içerebilir. İş sürekliliği ve olağanüstü durum kurtarma (BCDR) çözümüne hazırlanın, kuruluşunuz veya kuruluşunuz kesintileri, planlı veya planlanmamış bir şekilde daha hızlı yanıt verebilir ve müşterileriniz için kapalı kalma süresini azaltabilir.

Bu makale, [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kullanarak otomatik iş akışları oluşturduğunuzda uygulayabileceğiniz BCDR kılavuzlarını ve stratejilerini sağlar. Mantıksal uygulama iş akışları, yazmanız gereken kod miktarını azaltarak uygulamalar, bulut hizmetleri ve şirket içi sistemler arasında verileri daha kolay bir şekilde tümleştirmenize ve düzenlemenize yardımcı olur. BCDR 'yi planlarken, yalnızca mantıksal uygulamalarınızın değil, Logic Apps ile birlikte kullandığınız Azure kaynaklarını da göz önünde bulundurduğunuzdan emin olun:

* Mantıksal uygulamalardan diğer uygulamalar, hizmetler ve sistemlere oluşturduğunuz [Bağlantılar](../connectors/apis-list.md) . Daha fazla bilgi için bu konunun ilerleyen bölümlerindeki [kaynaklarla bağlantı](#resource-connections) bölümüne bakın.

* Şirket içi sistemlerdeki verilere erişmek için mantıksal uygulamalarınızda oluşturduğunuz ve kullandığınız Azure kaynakları olan şirket [içi veri ağ geçitleri](../logic-apps/logic-apps-gateway-connection.md) . Her ağ geçidi kaynağı, yerel bir bilgisayarda ayrı bir [veri ağ geçidi yüklemesini](../logic-apps/logic-apps-gateway-install.md) temsil eder. Daha fazla bilgi için bu konunun ilerleyen kısımlarında yer [alan şirket içi veri ağ geçitleri](#on-premises-data-gateways) bölümüne bakın.

* Logic Apps 'in [işletmeden işletmeye (B2B) kurumsal tümleştirme](../logic-apps/logic-apps-enterprise-integration-overview.md) senaryolarında kullandığı yapıtları tanımladığınız ve depoladığınız [tümleştirme hesapları](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Örneğin, [tümleştirme hesapları için çapraz bölge olağanüstü durum kurtarma ayarlayabilirsiniz](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* Bir Azure sanal ağı içindeki yalıtılmış Logic Apps çalışma zamanı örneğinde çalışan Logic Apps oluşturduğunuz [tümleştirme hizmeti ortamları (sesleri)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . Bu mantıksal uygulamalar daha sonra söz konusu sanal ağdaki bir güvenlik duvarının arkasında korunan kaynaklara erişebilir.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Birincil ve ikincil konumlar

Her mantıksal uygulamanın, dağıtım için kullanmak istediğiniz konumu belirtmesi gerekir. Bu konum, genel çok kiracılı Azure 'da "Batı ABD" gibi genel bir bölgedir veya daha önce oluşturduğunuz ve bir Azure sanal ağına dağıttığınız bir tümleştirme hizmeti ortamı (ıSE). Mantıksal uygulamaları bir ıSE 'de çalıştırmak, mantıksal uygulamaları küresel bir Azure bölgesinde çalıştırmaya benzerdir, bu da olağanüstü durum kurtarma stratejinizin her iki senaryoya de uygulanmasıdır. Öte yandan, sesleri yalnızca sesleri açık olan kaynaklara erişimi yapılandırma gibi başka hususlar vardır.

> [!NOTE]
> Mantıksal uygulamanız aynı zamanda, bir tümleştirme hesabında depolanan ticari iş ortakları, sözleşmeler, şemalar, haritalar ve sertifikalar gibi B2B yapıtları ile de çalışıyorsa, tümleştirme hesabınız ve mantıksal uygulamalarınızın her ikisi de aynı konumu belirtmelidir.

Bu olağanüstü durum kurtarma stratejisi, bir bekleme veya yedekleme mantıksal uygulamasına [*Yük devretme*](https://en.wikipedia.org/wiki/Failover) için birincil mantıksal uygulamanızı, Azure Logic Apps de kullanılabildiği alternatif bir konuma ayarlamaya odaklanır. Bu şekilde, birincil, kayıpları, kesintiler veya hatalara sahip olursa ikincil iş üzerinde işlem yapabilir. Bu strateji, ikincil mantıksal uygulamanızın ve bağımlı kaynaklarınızın zaten dağıtılması ve alternatif konuma hazırlanmasının gerekli olmasını gerektirir.

İyi DevOps uygulamalarını izlerseniz, mantıksal uygulamalarınızı ve bunların bağımlı kaynaklarını tanımlamak ve dağıtmak için [Azure Resource Manager şablonlarını](../azure-resource-manager/management/overview.md) zaten kullanıyorsunuz. Kaynak Yöneticisi şablonlar, size tek bir dağıtım tanımı kullanma olanağı sağlar ve ardından her dağıtım hedefi için kullanılacak yapılandırma değerlerini sağlamak üzere parametre dosyalarını kullanabilir. Bu özellik, aynı mantıksal uygulamayı farklı ortamlara (örneğin, geliştirme, test ve üretim) dağıtabileceğiniz anlamına gelir. Aynı mantıksal uygulamayı farklı Azure bölgelerine veya Ayrıca, [eşleştirilmiş bölgeler](../best-practices-availability-paired-regions.md)kullanan olağanüstü durum kurtarma stratejilerini destekleyen farklı Azure bölgelerine de dağıtabilirsiniz.

Yük devretme stratejisi için mantıksal uygulamalarınızın ve konumlarından bu gereksinimleri karşılaması gerekir:

* İkincil mantıksal uygulama örneğinin, birincil mantıksal uygulama örneği ile aynı uygulamalara, hizmetlere ve sistemlere erişimi vardır.

* Her iki mantıksal uygulama örneği de aynı ana bilgisayar türüne sahiptir. Bu nedenle, her iki örnek de küresel çok kiracılı Azure 'daki bölgelere dağıtılır ya da her iki örnek de sesleri, mantıksal uygulamalarınızın bir Azure sanal ağındaki kaynaklara doğrudan erişmesini sağlayan bir şekilde dağıtılır. En iyi uygulamalar ve BCDR 'nin eşleştirilmiş bölgeleri hakkında daha fazla bilgi için bkz. [iş sürekliliği ve olağanüstü durum kurtarma (BCDR): Azure eşlenmiş bölgeler](../best-practices-availability-paired-regions.md).

  Örneğin, birincil mantıksal uygulama bir ıSE içinde çalıştırıldığında ve [Ise sürümlü bağlayıcıları](../connectors/managed.md#ise-connectors), Azure sanal ağındaki kaynakları çağırmak için http eylemlerini veya her ikisini de kullandığında, birincil ve ikincil konumların her ikisi de bit olmalıdır. Bu senaryoda, ikincil mantıksal uygulamanız aynı zamanda ikincil konumda birincil mantıksal uygulama olarak benzer bir kuruluma sahip olmalıdır.

  > [!NOTE]
  > Daha gelişmiş senaryolar için hem çok kiracılı Azure hem de bir ıSE 'yi konumlar olarak karıştırabilirsiniz. Ancak, [Logic Apps 'in BIR ıSE 'de nasıl çalıştığı, çok kiracılı Azure ile arasındaki farkları](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)göz önünde bulundurduğunuzdan ve anladığınızdan emin olun.

* Sesleri kullanıyorsanız, bu veya yükü işlemek için [yeterli kapasiteye sahip olduklarından emin olun](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) .

#### <a name="example-multi-tenant-azure"></a>Örnek: çok kiracılı Azure

Bu örnek, bu senaryo için küresel çok kiracılı Azure 'daki ayrı bölgelere dağıtılan birincil ve ikincil mantıksal uygulama örneklerini gösterir. Tek bir [Kaynak Yöneticisi şablonu](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) , mantıksal uygulama örneklerini ve bu mantıksal uygulamalar için gereken bağımlı kaynakları tanımlar. Ayrı parametre dosyaları her dağıtım konumu için kullanılacak yapılandırma değerlerini belirtir:

![Ayrı konumlardaki birincil ve ikincil mantıksal uygulama örnekleri](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Örnek: Integration Service ortamı

Bu örnek, önceki birincil ve ikincil mantıksal uygulama örneklerini gösterir, ancak birbirinden ayrı olarak dağıtılır. Tek bir Kaynak Yöneticisi şablonu, mantıksal uygulama örneklerini, bu mantıksal uygulamalar için gereken bağımlı kaynakları ve dağıtım konumları olarak sesleri tanımlar. Ayrı parametre dosyaları her bir konumda dağıtım için kullanılacak yapılandırma değerlerini tanımlar:

![Farklı konumlardaki birincil ve ikincil Logic Apps](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Kaynaklarla bağlantı

Azure Logic Apps, [yerleşik Tetikleyiciler ve eylemler](../connectors/apis-list.md) ile mantıksal uygulamanızın Azure depolama hesapları, SQL Server veritabanları, iş veya okul e-posta hesapları gibi diğer uygulamalarla ve diğer kaynaklarla çalışmak için kullanabileceği yüzlerce yönetilen bağlayıcı sağlar. Mantıksal uygulamanızın bu kaynaklara erişmesi gerekiyorsa, bu kaynaklara erişimin kimliğini doğrulayan bağlantılar oluşturursunuz. Her bağlantı, belirli bir konumda bulunan ve diğer konumlardaki kaynaklar tarafından kullanılamayan ayrı bir Azure kaynağıdır.

Olağanüstü durum kurtarma stratejiniz için, mantıksal uygulama örneklerinize göre bağımlı kaynakların mevcut olduğu konumları göz önünde bulundurun:

* Birincil örneğiniz ve bağımlı kaynaklarınız farklı konumlarda mevcuttur. Bu durumda, ikincil örneğiniz aynı bağımlı kaynaklara veya uç noktalara bağlanabilir. Ancak, ikincil örneğiniz için özel olarak bağlantılar oluşturmanız gerekir. Bu şekilde, birincil konumunuz kullanılamaz hale gelirse, ikincil bağlantıları etkilenmez.

  Örneğin, birincil mantıksal uygulamanızın Salesforce gibi bir dış hizmete bağlandığını varsayalım. Genellikle, dış hizmetin kullanılabilirliği ve konumu, mantıksal uygulamanızın kullanılabilirliğine bağlıdır. Bu durumda, ikincil örneğiniz aynı hizmete bağlanabilir ancak kendi bağlantısına sahip olmalıdır.

* Hem birincil örneğiniz hem de bağımlı kaynaklarınız aynı konumda bulunur. Bu durumda, ikincil örneğinizin bu kaynaklara erişmeye devam edebilmesi için bağımlı kaynaklar farklı bir konumda yedeklemeler veya çoğaltılmış sürümler içermelidir.

  Örneğin, birincil mantıksal uygulamanızın aynı konum veya bölgede (örneğin, Azure SQL veritabanı) bir hizmete bağlandığını varsayalım. Bu bölgenin tamamı kullanılamaz hale gelirse, bu bölgedeki Azure SQL veritabanı hizmeti de büyük olasılıkla kullanılamıyor olabilir. Bu durumda, ikincil örneğinizin aynı veritabanına ayrı bir bağlantıyla birlikte çoğaltılan veya yedek bir veritabanı kullanmasını istersiniz.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Şirket içi veri ağ geçitleri

Mantıksal uygulamanız çok kiracılı Azure 'da çalışıyorsa ve SQL Server veritabanları gibi şirket içi kaynaklara erişmesi gerekiyorsa, Şirket [içi veri ağ geçidini](../logic-apps/logic-apps-gateway-install.md) yerel bir bilgisayara yüklemeniz gerekir. Daha sonra Azure portal bir veri ağ geçidi kaynağı oluşturabilirsiniz, böylece mantıksal uygulamanız, kaynağa bir bağlantı oluşturduğunuzda ağ geçidini kullanabilir.

Veri ağ geçidi kaynağı, mantıksal uygulama kaynağınız gibi bir konum veya Azure bölgesi ile ilişkilendirilir. Olağanüstü durum kurtarma stratejinizde, mantıksal uygulamanızın kullanması için veri ağ geçidinin kullanılabilir olmaya devam ettiğinden emin olun. Birden çok ağ geçidi yüklemesi olduğunda [ağ geçidiniz için yüksek kullanılabilirlik](../logic-apps/logic-apps-gateway-install.md#high-availability) sağlayabilirsiniz.

> [!NOTE]
> Mantıksal uygulamanız bir tümleştirme hizmeti ortamında (ıSE) çalışıyorsa ve şirket içi veri kaynakları için yalnızca ıSE sürümlü bağlayıcılar kullanıyorsa, ıSE bağlayıcıları şirket içi kaynağa doğrudan erişim sağladığından veri ağ geçidine gerek kalmaz.
>
> İstediğiniz şirket içi kaynak için kurumsal sürümlü bir bağlayıcı yoksa, mantıksal uygulamanız bağlantıyı ücretsiz olarak değil, genel çok kiracılı Azure 'da çalışan ıSE olmayan bir bağlayıcı kullanarak yine de oluşturabilir. Ancak, bu bağlantı Şirket içi veri ağ geçidini gerektirir.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Etkin-etkin ve aktif-pasif roller

Birincil ve ikincil konumlarınızı, bu konumlardaki mantıksal uygulama örneklerinizin bu rolleri oynayabilmesi için ayarlayabilirsiniz:

| Birincil-ikincil rol | Description |
|------------------------|-------------|
| *Etkin-etkin* | Her iki konumdaki birincil ve ikincil mantıksal uygulama örnekleri şu desenlerden birini izleyerek istekleri etkin bir şekilde işler: <p><p>- *Yük Dengeleme*: her iki örneğin, her bir örneğe bir uç nokta dinlemesi ve gerektiğinde trafiği yük dengelemesi sağlayabilirsiniz. <p>- *Rekabet tüketicileri*: örneklerin bir kuyruktan iletiler için rekabet etmesi için her iki örneğin birden rekabet eden tüketici işlevi görmesini sağlayabilirsiniz. Bir örnek başarısız olursa, diğer örnek iş yükünü devralır. |
| *Aktif-pasif* | Birincil mantıksal uygulama örneği, ikincil örnek pasif iken (devre dışı veya etkin değil) tüm iş yükünü etkin bir şekilde işler. İkincil bir sinyal bekler veya hata nedeniyle, birincil kullanım dışı veya çalışmayan bir sinyal bekler ve iş yükünü etkin örnek olarak alır. |
| Oluşturulmuştur | Bazı logic Apps etkin-etkin bir rol oynar, diğer Logic Apps ise aktif-pasif bir rol oynar. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Etkin-etkin örnekler

Bu örnekler, her iki mantıksal uygulama örneğinin istekleri veya iletileri etkin bir şekilde işleyeceği etkin-etkin kurulumu gösterir. Bazı başka sistem veya hizmetler, örnekler arasında istekleri veya iletileri, örneğin şu seçeneklerden birini dağıtır:

* Trafiği yönlendiren bir donanım parçası gibi bir "fiziksel" yük dengeleyici

* [Azure Load Balancer](../load-balancer/load-balancer-overview.md) veya [Azure API Management](../api-management/api-management-key-concepts.md)gibi bir "yazılım" yük dengeleyici. API Management, gelen trafiğin yükünü dengelemeye yönelik ilkeleri belirtebilirsiniz. Veya, durum izlemeyi destekleyen bir hizmeti (örneğin, [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)) kullanabilirsiniz.

  Bu örnek öncelikle Azure Load Balancer gösterdiği halde, senaryonuzun gereksinimlerinize en uygun seçeneği kullanabilirsiniz:

  ![Yük dengeleyici veya durum bilgisi olmayan hizmet kullanan "etkin-etkin" Kurulum](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Her mantıksal uygulama örneği bir tüketici görevi görür ve her iki örneğin bir kuyruktan iletiler için rekabet eder:

  !["Rekabet eden tüketiciler" kullanan "aktif-aktif" Kurulum](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktif-pasif örnekler

Bu örnek, birincil mantıksal uygulama örneğinin tek bir konumda etkin olduğu, ikincil örnek başka bir konumda etkin olmadığı halde etkin-Pasif kurulumunu gösterir. Birincil bir kesinti veya hata yaşıyorsa, bir operatörünün, iş yükünde ikinciye yapılacak bir komut dosyası çalıştırmasını sağlayabilirsiniz.

!["Rekabet eden tüketiciler" kullanan "aktif-pasif" Kurulum](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Etkin-etkin ve etkin-Pasif ile birleşim

Bu örnek, birincil konumun etkin mantıksal uygulama örneklerine sahip olduğu bir Birleşik kurulumu gösterir, ancak ikincil konum aktif-pasif Logic App örnekleri içerir. Birincil konum bir kesinti veya hata yaşıyorsa, zaten kısmi bir iş yükünü işleyen ikincil konumdaki etkin mantıksal uygulama, iş yükünün tamamını ele geçirebilir.

* Birincil konumda, etkin bir mantıksal uygulama iletiler için Azure Service Bus kuyruğu dinler, ancak başka bir etkin mantıksal uygulama, Office 365 Outlook yoklama tetikleyicisi kullanarak e-postaları denetler.

* İkincil konumda, etkin bir mantıksal uygulama aynı Service Bus kuyruğundan iletileri dinleyerek ve rekabet ederek birincil konumdaki mantıksal uygulama ile birlikte kullanılabilir. Yani, pasif bir etkin olmayan mantıksal uygulama, birincil konum kullanılamadığında, ancak okuyarak e-postalardan kaçınmak için *devre dışı* bırakıldığında e-postaları denetlemek için bekleme süresi üzerinde bekler.

![Yinelenme tetikleyicilerini kullanan "aktif-pasif" ve "aktif-pasif" birleşimi](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Mantıksal uygulama durumu ve geçmişi

Mantıksal uygulamanız tetiklenip çalışmaya başladığında, uygulamanın durumu uygulamanın başladığı ve başka bir konuma aktarılamayan aynı konumda depolanır. Bir hata veya kesinti oluşursa, devam eden tüm iş akışı örnekleri terk edilir. Ayarlanmış birincil ve ikincil konumlara sahip olduğunuzda, yeni iş akışı örnekleri ikinci konumda çalışmaya başlar.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Bırakılan devam eden örnekleri azaltma

Bırakılan devam eden iş akışı örneklerinin sayısını en aza indirmek için uygulayabileceğiniz çeşitli ileti desenlerinden seçim yapabilirsiniz, örneğin:

* [Sabit yönlendirme SLIP deseninin](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Bir iş işlemini daha küçük aşamalar halinde ayıran bu kurumsal ileti deseninin. Her aşamada, söz konusu aşama için iş yükünü işleyen bir mantıksal uygulama ayarlarsınız. Mantıksal uygulamalarınız birbirleriyle iletişim kurmak için Azure Service Bus kuyrukları veya konuları gibi zaman uyumsuz mesajlaşma protokolü kullanır. Bir işlemi daha küçük bir aşamaya bölediğiniz zaman, başarısız bir Logic App örneğine takılmış olabilecek iş işlemlerinin sayısını azaltırsınız. Bu düzen hakkında daha fazla genel bilgi için bkz. [kurumsal tümleştirme desenleri-yönlendirme makbuzu](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Bu örnek, her mantıksal uygulamanın bir aşamayı temsil ettiği ve işlemdeki bir sonraki mantıksal uygulamayla iletişim kurmak için Service Bus kuyruğu kullanan bir yönlendirme SLIP modelini gösterir.

  ![İş sürecini, Azure Service Bus kuyrukları kullanarak birbirleriyle iletişim kuran Logic Apps tarafından temsil edilen aşamalara böler](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Birincil ve ikincil mantıksal uygulama örneklerinin her ikisi de konumlarında aynı yönlendirme makbuzu düzeniyle karşılaşlarsa, bu örnekler için [etkin-etkin roller](#roles) ayarlayarak [rekabet tüketicileri modelini](/azure/architecture/patterns/competing-consumers) uygulayabilirsiniz.

* [İşlem Yöneticisi (aracı) kalıbı](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Zaman aşımı stili olmadan Özeti kilitle](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Tetikleyici ve çalıştırma geçmişine erişim

Mantıksal uygulamanızın geçmiş iş akışı yürütmeleri hakkında daha fazla bilgi edinmek için uygulamanın tetikleyicisini gözden geçirebilir ve geçmişi çalıştırabilirsiniz. Mantıksal uygulamanın geçmiş yürütme geçmişi, mantıksal uygulamanın çalıştırıldığı konum veya bölgede depolanır, bu da bu geçmişi farklı bir konuma geçiremeyeceğiniz anlamına gelir. Birincil örneğiniz ikincil bir örneğe yük devreder, her bir örneğin tetikleyicisine ve bu örneklerin çalıştırıldığı ilgili konumlarda çalışma geçmişine erişebilirsiniz. Ancak, mantıksal uygulamalarınızın Azure Log Analytics çalışma alanına tanılama olayları gönderecek şekilde ayarlayarak mantıksal uygulamanızın geçmişiyle ilgili konum belirsiz bilgiler alabilirsiniz. Daha sonra birden fazla konumda çalışan Logic Apps genelinde sistem durumunu ve geçmişi gözden geçirebilirsiniz.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Tetikleyici tür Kılavuzu

Logic Apps 'te kullandığınız tetikleyici türü, olağanüstü durum kurtarma stratejinizdeki konumlar arasında mantıksal uygulamaları nasıl ayarlayabileceğinizi belirleyen seçeneklerinizi belirler. Mantıksal uygulamalarda kullanabileceğiniz kullanılabilir tetikleyici türleri şunlardır:

* [Yinelenme tetikleyicisi](#recurrence-trigger)
* [Yoklama tetikleyicisi](#polling-trigger)
* [İstek tetikleyicisi](#request-trigger)
* [Web kancası tetikleyicisi](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Yinelenme tetikleyicisi

**Yineleme** tetikleyicisi, belirli bir hizmet veya uç noktadan bağımsızdır ve yalnızca belirtilen bir zamanlamayı ve başka hiçbir ölçütü temel almaz, örneğin:

* Her 10 dakikada bir sabit sıklık ve Aralık
* 5:00 PM 'de her ayın son Pazartesi günü gibi daha gelişmiş bir zamanlama

Mantıksal uygulamanız bir yinelenme tetikleyicisi ile başlıyorsa, birincil ve ikincil mantıksal uygulama örneklerinizi [etkin-Pasif rollerle](#roles)ayarlamanız gerekir. Bir kesinti veya felaket sonrasında bir iş işlemini geri yüklemek için hedef süreye işaret eden *Kurtarma süresi hedefini* (RTO) azaltmak için, mantıksal uygulama örneklerinizi [etkin-Pasif roller](#roles) ve [Pasif etkin roller](#roles)ile birlikte ayarlayabilirsiniz. Bu kurulumda, zamanlamayı konumlar arasında bölecektir.

Örneğin, her 10 dakikada bir çalışması gereken bir mantıksal uygulamanız olduğunu varsayalım. Birincil konum kullanılamaz hale gelirse, ikincil konumun iş üzerinde ele geçirmesine olanak sağlamak için mantıksal uygulamalarınızı ve konumlarınızı ayarlayabilirsiniz:

![Yinelenme tetikleyicilerini kullanan "aktif-pasif" ve "pasif-aktif" birleşimi](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Birincil konumda, bu mantıksal uygulamalar için [etkin-Pasif roller](#roles) ayarlayın:

  * *Etkin* mantıksal uygulama için yineleme tetikleyicisi ' ni saatin en üstünde başlayacak şekilde ayarlayın ve 20 dakikada bir tekrarlayın, örneğin: 9:00, 9:20.

  * *Pasif* devre dışı mantıksal uygulama Için, yinelenme tetikleyicisini aynı zamanlamaya göre ayarlayın, ancak saatin sonunda 10 dakika sonra başlayın ve yaklaşık 20 dakikada bir tekrarlayın, örneğin, 9:10, 9:30.

* İkincil konumda, bu mantıksal uygulamalar için [Pasif etkin](#roles) ' i ayarlayın:

  * *Pasif* devre dışı mantıksal uygulama Için, yinelenme tetikleyicisini birincil konumdaki etkin mantıksal uygulamayla aynı zamanlamaya göre ayarlayın, bu, saatin en üstünde ve 20 dakikada bir yinelenir, örneğin 9:00, 9:10, vb.

  * *Etkin* etkinleştirilmiş mantıksal uygulama Için, yinelenme tetikleyicisini birincil konumdaki pasif Logic App ile aynı zamanlamaya göre ayarlayın, bu, saatin sonunda 10 dakika içinde başlayacak ve 20 dakikada bir tekrarlanacak şekilde (örneğin, 9:10, 9:20, vb.) aynı zamanlamaya göre.

Şimdi, birincil konumda kesintiye uğratan bir olay oluşursa, pasif mantıksal uygulamayı alternatif konumda etkinleştirin. Bu şekilde, hatanın zaman alırsa, bu kurulum gecikme süresi boyunca eksik Yinelenmeler sayısını sınırlandırır.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Yoklama tetikleyicisi

İşlem için yeni verilerin belirli bir hizmet veya uç noktadan kullanılabilir olup olmadığını düzenli olarak denetlemek için mantıksal uygulamanız, sabit bir yinelenme zamanlaması temelinde hizmeti veya uç noktayı tekrar tekrar çağıran bir *yoklama* tetikleyicisi kullanabilir. Hizmetin veya uç noktanın sağladığı veriler şu türlerden birini içerebilir:

* Her zaman okumak üzere kullanılabilen verileri açıklayan statik veriler
* Okumadan sonra artık kullanılamayan verileri açıklayan geçici veriler

Aynı verileri sürekli olarak okumayı önlemek için mantıksal uygulamanızın, istemci tarafında ya da sunucu, hizmet veya sistem tarafında durumu korumak için hangi verilerin daha önce okunduğunu hatırlamaları gerekir.

* İstemci tarafı durum kullanımı ile çalışan Logic Apps, durumu koruyabilirler.

  Örneğin, bir e-posta gelen kutusundan yeni bir ileti okuyan bir tetikleyici, tetikleyicinin en son okunan iletiyi hatırlayabilmesi gerekir. Bu şekilde, tetikleyici mantıksal uygulamayı yalnızca sonraki okunmamış ileti geldiğinde başlatır.

* Sunucu, hizmet veya sistem tarafı durumuyla çalışan Logic Apps, sunucu, hizmet veya sistem tarafında bulunan özellik değerlerini veya ayarları kullanır.

  Örneğin, bir veritabanından bir satırı okuyan sorgu tabanlı bir tetikleyici, satırda ayarlanmış bir sütun olmasını gerektirir `isRead` `FALSE` . Tetikleyici her bir satırı okuduğunda, mantıksal uygulama sütunu ' dan ' a değiştirerek bu satırı güncelleştirir `isRead` `FALSE` `TRUE` .

  Bu sunucu tarafı yaklaşımı, mantıksal uygulama iletiyi işlerken bir tetikleyicinin bir iletiyi okuyabileceği ve kilitleyebildiği sıraya alma semantiğine sahip Service Bus kuyrukları veya konuları için benzer şekilde çalışır. Mantıksal uygulama işlemeyi bitirdiğinde, tetikleyici iletiyi kuyruktan veya konudan siler.

Olağanüstü durum kurtarma perspektifinden mantıksal uygulamanızın birincil ve ikincil örneklerini ayarlarken, mantıksal uygulamanızın durumu istemci tarafında veya sunucu tarafında izleyip izlemediğinden emin olun:

* İstemci tarafı durumuyla birlikte çalışarak mantıksal uygulama için, mantıksal uygulamanızın aynı iletiyi birden çok kez okuduğunuzdan emin olun. Belirli bir zamanda yalnızca bir konum etkin bir Logic App örneğine sahip olabilir. Birincil örnek diğer konuma devredilene kadar alternatif konumdaki mantıksal uygulama örneğinin etkin olmadığından veya devre dışı bırakıldığından emin olun.

  Örneğin, Office 365 Outlook tetikleyicisi, istemci tarafı durumunu korur ve yinelenen okumayı önlemek için en son okunan e-posta için zaman damgasını izler.

* Sunucu tarafı durumuyla çalışan bir mantıksal uygulama için, mantıksal uygulama örneklerinizi, diğer örneğin birincil örnek diğer konuma yük devri yapana kadar beklediği bir rekabet tüketicileri veya [aktif-pasif roller](#roles) olarak çalıştıkları [etkin-etkin rolleri](#roles) çalmak üzere ayarlayabilirsiniz.

  Örneğin, Azure Service Bus kuyruğu gibi bir ileti kuyruğundan okuma, diğer istemcilerin aynı iletileri okumasını engellemek için iletileri kilitlediği için sunucu tarafı durumu kullanır.

  > [!NOTE]
  > Mantıksal uygulamanızın iletileri belirli bir sırada okuması gerekiyorsa (örneğin, bir Service Bus kuyruğundan), rekabet eden tüketici düzenini, ancak [ *sıralı konvoy* düzeni](/azure/architecture/patterns/sequential-convoy)olarak da bilinen Service Bus oturumlarıyla birleştirildiğinde kullanabilirsiniz. Aksi takdirde, mantıksal uygulama örneklerinizi aktif-pasif rollerle ayarlamanız gerekir.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>İstek tetikleyicisi

**İstek** tetikleyicisi, mantıksal uygulamanızı diğer uygulamalardan, hizmetlerden ve sistemlerden çağrılabilir hale getirir ve genellikle bu özellikleri sağlamak için kullanılır:

* Mantıksal uygulamanız için başkalarının çağıracağı doğrudan bir REST API

  Örneğin, mantıksal uygulamanızı başlatmak için Istek tetikleyicisi ' ni kullanın, böylece diğer Logic Apps, **iş akışı-Logic Apps çağrısı** eylemini kullanarak tetikleyiciyi çağırabilirler.

* Mantıksal uygulamanız için bir [Web kancası](#webhook-trigger) veya geri çağırma mekanizması

* Mantıksal uygulamanızı çağırmak için Kullanıcı işlemlerini veya yordamlarını el ile çalıştırabileceğiniz bir şekilde (örneğin, belirli bir görevi gerçekleştiren bir PowerShell betiği kullanarak)

Bir olağanüstü durum kurtarma açısından, mantıksal uygulama hiçbir iş gerçekleştirmediğinden ve başka bir hizmet veya sistem tetikleyiciyi açıkça çağırana kadar bekleyeceğinden, Istek tetikleyicisi pasif bir alıcıdır. Pasif bir uç nokta olarak, birincil ve ikincil örneklerinizi şu yollarla ayarlayabilirsiniz:

* [Etkin-etkin](#roles): her iki örnek de istekleri veya çağrıları etkin bir şekilde işler. Arayan veya yönlendirici bu örnekler arasında trafiği dengeler veya dağıtır.

* [Etkin-Pasif](#roles): yalnızca birincil örnek etkindir ve tüm işleri işler, ikincil örnek birincil deneyimle veya başarısız olana kadar bekler. Çağıran veya yönlendirici, ikincil örnek ne zaman çağrılacağını belirler.

Önerilen bir mimari olarak, Azure API Management, Istek Tetikleyicileri kullanan mantıksal uygulamalar için proxy olarak kullanabilirsiniz. API Management, [yerleşik çapraz bölgesel dayanıklılık ve trafiği birden çok uç nokta arasında yönlendirme yeteneği](../api-management/api-management-howto-deploy-multi-region.md)sağlar.

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Web kancası tetikleyicisi

*Web kancası* tetikleyicisi, bu hizmete bir *geri çağırma URL 'si* geçirerek mantıksal uygulamanızın bir hizmete abone olma özelliğini sağlar. Mantıksal uygulamanız daha sonra bu hizmet uç noktasında belirli bir olayın gerçekleşmesini dinleyebilir ve bekleyebilir. Olay gerçekleştiğinde hizmet, daha sonra mantıksal uygulamayı çalıştıran geri çağırma URL 'sini kullanarak Web kancası tetikleyicisini çağırır. Etkinleştirildiğinde, Web kancası tetikleyicisi hizmete abone olur. Devre dışı bırakıldığında, tetikleyici, hizmetten aboneliği kaldırır.

Bir olağanüstü durum kurtarma perspektifinden, yalnızca bir örnek, abone olunan uç noktadan olay veya ileti alması gerektiğinden, etkin-Pasif roller oynatmak için Web kancası Tetikleyicileri kullanan birincil ve ikincil örnekleri ayarlayın.

## <a name="assess-primary-instance-health"></a>Birincil örnek durumunu değerlendir

Olağanüstü durum kurtarma stratejinizin çalışması için, çözümünüzün bu görevleri gerçekleştirmek için bir yol olması gerekir:

* [Birincil örneğin kullanılabilirliğini denetle](#check-primary-availability)
* [Birincil örneğin sistem durumunu izleme](#monitor-primary-health)
* [İkincil örneği etkinleştirin](#activate-secondary)

Bu bölümde, kendi tasarımınız için bir temel olarak veya bir temel olarak kullanabileceğiniz bir çözüm açıklanmaktadır. Bu çözüm için üst düzey bir görsel genel bakış aşağıda verilmiştir:

![Birincil konumdaki bir sistem durumu denetimi mantıksal uygulamasını izleyen izleme mantıksal uygulaması oluşturma](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Birincil örnek kullanılabilirliğini denetle

Birincil örneğin kullanılabilir, çalışır durumda olup olmadığını ve çalışıp çalışamayacağını öğrenmek için, birincil örnekle aynı konumda olan "sistem durumu denetimi" mantıksal uygulaması oluşturabilirsiniz. Daha sonra bu durum denetimi uygulamasını alternatif bir konumdan çağırabilirsiniz. Durum denetimi uygulaması başarıyla yanıt verirse, bu bölgedeki Azure Logic Apps hizmeti için temel alınan altyapı kullanılabilir ve çalışır durumda olur. Durum denetimi uygulaması yanıt veremezse, konumun artık sağlıklı olmadığını varsayabilirsiniz.

Bu görev için, şu görevleri gerçekleştiren temel bir sistem durumu denetimi mantıksal uygulaması oluşturun:

1. Istek tetikleyicisini kullanarak izleme uygulamasından bir çağrı alır.

1. Denetlenen mantıksal uygulamanın yanıt eylemini kullanarak hala çalışıp çalışmadığını gösteren bir durumla yanıtlayın.

   > [!IMPORTANT]
   > Durum denetimi mantıksal uygulamasının, uygulamanın zaman uyumsuz değil zaman uyumlu olarak yanıt vermesi için bir yanıt eylemi kullanması gerekir.

1. İsteğe bağlı olarak, birincil konumun sağlıklı olup olmadığını daha fazla anlamak için, bu konumdaki hedef mantıksal uygulamayla etkileşime geçen diğer hizmetlerin sistem durumunu da çarpanlara atayabilirsiniz. Bu diğer hizmetlerin sistem durumunu değerlendirmek için sistem durumu denetimi mantıksal uygulamasını genişletmeniz yeterlidir.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>İzleme mantıksal uygulaması oluşturma

Birincil örneğin durumunu izlemek ve sistem durumu denetimi mantıksal uygulamasını çağırmak için *alternatif bir konumda*"izleme" mantıksal uygulaması oluşturun. Örneğin, sistem durumu denetimi mantığını çağırmak başarısız olursa, izleme işlemi, hatayı araştırmak ve birincil örneğin yanıt verememesi için işlem ekibinize bir uyarı gönderebilmesi için izleme mantıksal uygulamasını ayarlayabilirsiniz.

> [!IMPORTANT]
> İzleme mantıksal uygulamanızın *birincil konumdan farklı bir konumda* olduğundan emin olun. Birincil konumdaki Logic Apps hizmeti sorunlarla karşılaşırsa, izleme mantıksal uygulamanız çalışmayabilir.

Bu görev için, ikincil konumda, şu görevleri gerçekleştiren bir izleme mantıksal uygulaması oluşturun:

1. Yinelenme tetikleyicisini kullanarak sabit veya zamanlanmış bir tekrarya göre çalıştırın.

   Yinelenme süresini, kurtarma süresi hedefi (RTO) için tolerans düzeyinin altında olan bir değere ayarlayabilirsiniz.

1. HTTP eylemini kullanarak birincil konumdaki sistem durumu denetimi mantıksal uygulamasını çağırın, örneğin:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>İkincil örneğinizi etkinleştirin

İkincil örneği otomatik olarak etkinleştirmek için, [Azure Resource Manager Bağlayıcısı](/connectors/arm/) gıbı yönetim API 'sini çağıran bir mantıksal uygulama oluşturabilirsiniz. Bu, ikincil konumda uygun mantık uygulamalarını etkinleştirir. İzleme uygulamanızı, belirli bir sayıda hatadan sonra bu etkinleştirme mantıksal uygulamasını çağırmak için genişletebilirsiniz.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Tanılama verilerini topla

Mantıksal uygulama çalışmalarınız için günlük kaydı ayarlayabilir ve elde edilen tanılama verilerini daha fazla işleme ve işleme için Azure depolama, Azure Event Hubs ve Azure Log Analytics gibi hizmetlere gönderebilirsiniz.

* Bu verileri Azure Log Analytics ile kullanmak istiyorsanız, mantıksal uygulamanızın **tanılama ayarlarını** ayarlayarak ve verileri birden çok Log Analytics çalışma alanına göndererek hem birincil hem de ikincil konumlar için verileri kullanılabilir hale getirebilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici günlüklerini ayarlama ve Azure Logic Apps için tanılama verileri toplama](../logic-apps/monitor-logic-apps-log-analytics.md).

* Verileri Azure Storage veya Azure Event Hubs 'a göndermek istiyorsanız coğrafi yedeklilik ayarlayarak verileri hem birincil hem de ikincil konumlar için kullanılabilir hale getirebilirsiniz. Daha fazla bilgi için şu makalelere bakın:<p>

  * [Azure Blob depolama olağanüstü durum kurtarma ve hesap yük devretme](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs coğrafi olağanüstü durum kurtarma](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure için dayanıklılık genel bakışı](/azure/architecture/framework/resiliency/overview)
* [Belirli Azure hizmetleri için dayanıklılık denetim listesi](/azure/architecture/checklist/resiliency-per-service)
* [Azure 'da dayanıklılık için veri yönetimi](/azure/architecture/framework/resiliency/data-management)
* [Azure uygulamaları için yedekleme ve olağanüstü durum kurtarma](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Bölge genelinde hizmet kesintisinden kurtarma](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Azure hizmetleri için Microsoft hizmet düzeyi sözleşmeleri (SLA 'Lar)](https://azure.microsoft.com/support/legal/sla/)
