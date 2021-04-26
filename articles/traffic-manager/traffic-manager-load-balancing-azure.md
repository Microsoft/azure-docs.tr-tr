---
title: Azure 'da Yük Dengeleme hizmetlerini kullanma | Microsoft Docs
description: 'Bu öğreticide, Azure Yük Dengeleme portföyünü kullanarak nasıl senaryo oluşturacağınız gösterilmektedir: Traffic Manager, Application Gateway ve Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184382"
---
# <a name="using-load-balancing-services-in-azure"></a>Azure’daki yük dengeleme hizmetlerini kullanma

## <a name="introduction"></a>Giriş

Microsoft Azure, ağ trafiğinin nasıl dağıtıldığını ve yük dengeli şekilde yönetilmesine yönelik birden çok hizmet sağlar. Gereksinimlerinize bağlı olarak, en iyi çözümü oluşturmak için bu hizmetleri ayrı ayrı kullanabilir veya yöntemlerini birleştirebilirsiniz.

Bu öğreticide, ilk olarak bir müşteri kullanım örneği tanımlar ve aşağıdaki Azure Yük Dengeleme portföyünü kullanarak nasıl daha sağlam ve performans sunduğumuz hakkında bilgi alabilirsiniz: Traffic Manager, Application Gateway ve Load Balancer. Daha sonra coğrafi olarak yedekli bir dağıtım oluşturmaya, trafiği VM 'lere dağıtmanıza ve farklı istek türlerini yönetmenize yardımcı olacak adım adım yönergeler sağlıyoruz.

Kavramsal düzeyde, bu hizmetlerin her biri, Yük Dengeleme hiyerarşisinde ayrı bir rol oynar.

* **Traffic Manager** genel DNS yük dengelemesi sağlar. Müşterinin seçtiği yönlendirme ilkesine uygun olarak, gelen DNS isteklerine bakar ve sağlıklı bir uç nokta ile yanıt verir. Yönlendirme yöntemleri seçenekleri şunlardır:
  * İstek sahibinin gecikme süresi bakımından en yakın uç noktaya gönderilmesi için performans yönlendirme.
  * Tüm trafiği bir uç noktaya yönlendirmek için öncelik yönlendirme ve diğer uç noktalar yedekleme olarak.
  * Her bir uç noktaya atanan ağırlığa göre trafiği dağıtan ağırlıklı hepsini bir kez deneme yönlendirmesi.
  * Kullanıcının coğrafi konumunu temel alarak trafiği uygulama uç noktalarına dağıtmak için coğrafya tabanlı yönlendirme.
  * Kullanıcının alt ağına (IP adres aralığı) bağlı olarak trafiği uygulama uç noktalarınıza dağıtmak için alt ağa dayalı yönlendirme.
  * Tek bir DNS yanıtında birden fazla uygulama uç noktasına ait IP adresleri göndermenizi sağlayan çoklu değerli yönlendirme.

  İstemci, Traffic Manager tarafından döndürülen uç noktaya doğrudan bağlanır. Azure Traffic Manager, bir uç noktanın sağlıksız olduğunu algılar ve sonra istemcileri başka bir sağlıklı örneğe yönlendirir. Hizmet hakkında daha fazla bilgi edinmek için [Azure Traffic Manager belgelerine](traffic-manager-overview.md) bakın.
* **Application Gateway** , uygulamanız Için çeşitli katman 7 yük dengeleme özellikleri sunan bir hizmet olarak uygulama teslim DENETLEYICISI (ADC) sağlar. Müşterilerin, CPU yoğun TLS sonlandırmasını uygulama ağ geçidine devrederek, Web grubu üretkenliğini iyileştirmelerine olanak tanır. Diğer katman 7 yönlendirme özellikleri, gelen trafik, tanımlama bilgisi tabanlı oturum benzeşimi, URL yolu tabanlı Yönlendirme ve tek bir uygulama ağ geçidinin arkasında birden fazla Web sitesini barındırma olanağı içerir. Application Gateway, Internet 'e yönelik ağ geçidi, yalnızca dahili ağ geçidi veya her ikisinin bir birleşimi olarak yapılandırılabilir. Application Gateway tamamen Azure tarafından yönetilen, ölçeklenebilir ve yüksek oranda kullanılabilir. Daha iyi yönetilebilirlik için zengin tanılama ve günlüğe kaydetme özellikleri sağlar.
* **Load Balancer** , tüm UDP ve TCP protokolleri için yüksek performanslı ve düşük gecikmeli katman 4 Yük Dengeleme hizmetleri sunan Azure Sdn yığınının ayrılmaz bir parçasıdır. Gelen ve giden bağlantıları yönetir. Yük dengeleme özelliğine sahip genel ve şirket içi yük uç noktaları yapılandırıp TCP ve HTTP hizmet durumu yoklama seçeneklerini kullanarak, gelen bağlantıları arka uç havuz hedefleriyle eşleyebilir ve hizmet kullanılabilirliği sağlayabilirsiniz.

## <a name="scenario"></a>Senaryo

Bu örnek senaryoda, iki tür içerik sunan basit bir Web sitesi kullanıyoruz: görüntüler ve dinamik olarak işlenmiş Web sayfaları. Web sitesi coğrafi olarak yedekli olmalıdır ve kullanıcılara en yakın (en düşük gecikme süresi) konumundan sahip olması gerekir. Uygulama geliştiricisi,/images/* düzeniyle eşleşen tüm URL 'Lerin, Web grubunun geri kalanından farklı olan adanmış bir VM havuzundan sunulduğunu kararmıştır.

Ayrıca, dinamik içeriğe hizmet veren varsayılan VM havuzunun, yüksek kullanılabilirlik kümesinde barındırılan bir arka uç veritabanıyla iletişim sağlaması gerekir. Tüm dağıtım Azure Resource Manager ile ayarlanır.

Traffic Manager, Application Gateway ve Load Balancer kullanımı, bu Web sitesinin bu tasarım hedeflerini elde etmesine izin verir:

* **Çoklu coğrafi yedeklilik**: bir bölge kapalıysa, uygulama sahibinden herhangi bir müdahale olmadan trafiği en yakın bölgeye sorunsuzca yönlendirir Traffic Manager.
* **Azaltılan gecikme**: Traffic Manager, müşteriyi en yakın bölgeye otomatik olarak yönlendirtiğinden, müşteri, Web sayfası içeriğini istenirken daha düşük gecikme süresine sahiptir.
* **Bağımsız ölçeklenebilirlik**: Web uygulaması iş yükü içerik türüne göre ayrıldığından, uygulama sahibi, istek iş yüklerini birbirinden bağımsız olarak ölçeklendirebilirler. Application Gateway, trafiğin belirtilen kurallara ve uygulamanın sistem durumuna göre doğru havuzlara yönlendirilmesini sağlar.
* **İç Yük Dengeleme**: Load Balancer yüksek kullanılabilirlik kümesinin önünde olduğundan, yalnızca bir veritabanının etkin ve sağlıklı uç noktası uygulamaya sunulur. Ayrıca, bir veritabanı yöneticisi, etkin ve pasif çoğaltmaları küme genelinde ön uç uygulamadan bağımsız olarak dağıtarak iş yükünü iyileştirebilirler. Load Balancer, yüksek kullanılabilirlik kümesine bağlantı sağlar ve yalnızca sağlıklı veritabanlarının bağlantı isteklerini almasını sağlar.

Aşağıdaki diyagramda bu senaryonun mimarisi gösterilmektedir:

![Yük Dengeleme mimarisinin diyagramı](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Bu örnek, Azure tarafından sunulan Yük Dengeleme hizmetlerinin olası birçok yapılandırmasından yalnızca biridir. Traffic Manager, Application Gateway ve Load Balancer, Yük Dengeleme gereksinimlerinize en iyi şekilde karışabilir ve eşleştirilebilir. Örneğin, TLS yük boşaltma veya katman 7 işleme gerekli değilse, Load Balancer Application Gateway yerine kullanılabilir.

## <a name="setting-up-the-load-balancing-stack"></a>Yük Dengeleme yığınını ayarlama

### <a name="step-1-create-a-traffic-manager-profile"></a>1. Adım: Traffic Manager profili oluşturma

1. Azure Portal, **kaynak**  >  **ağı** oluştur  >  **Traffic Manager profil**  >  **Oluştur**' a tıklayın.
2. Aşağıdaki temel bilgileri girin:

   * **Ad**: Traffic Manager PROFILINIZE bir DNS ön eki adı verin.
   * **Yönlendirme yöntemi**: trafik yönlendirme yöntemi ilkesini seçin. Yöntemler hakkında daha fazla bilgi için bkz. [Traffic Manager trafik yönlendirme yöntemleri hakkında](traffic-manager-routing-methods.md).
   * **Abonelik**: profili içeren aboneliği seçin.
   * **Kaynak grubu**: profili içeren kaynak grubunu seçin. Yeni veya mevcut bir kaynak grubu olabilir.
   * **Kaynak grubu konumu**: Traffic Manager hizmeti geneldir ve bir konum ile bağlantılı değildir. Ancak, Traffic Manager profiliyle ilişkili meta verilerin bulunduğu grup için bir bölge belirtmeniz gerekir. Bu konum, profilin çalışma zamanı kullanılabilirliğini etkilemez.

3. Traffic Manager profili oluşturmak için **Oluştur** ' a tıklayın.

   !["Traffic Manager oluştur" dikey penceresi](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2. Adım: uygulama ağ geçitlerini oluşturma

1. Azure Portal, sol bölmedeki **kaynak**  >  **ağı** oluştur  >  **Application Gateway**' a tıklayın.
2. Uygulama ağ geçidi hakkında aşağıdaki temel bilgileri girin:

   * **Ad**: uygulama ağ geçidinin adı.
   * **SKU boyutu**: küçük, orta veya büyük olarak kullanılabilen uygulama ağ geçidinin boyutu.
   * **Örnek sayısı**: 2 ile 10 arasında bir değer olan örnek sayısı.
   * **Kaynak grubu**: Application Gateway 'i tutan kaynak grubu. Mevcut bir kaynak grubu veya yeni bir kaynak olabilir.
   * **Konum**: kaynak grubuyla aynı konum olan uygulama ağ geçidinin bölgesi. Sanal ağ ve genel IP ağ geçidiyle aynı konumda olması gerektiğinden konum önemlidir.
3. **Tamam**'a tıklayın.
4. Uygulama ağ geçidi için sanal ağ, alt ağ, ön uç IP ve dinleyici yapılandırmasını tanımlayın. Bu senaryoda, ön uç IP adresi **genel** hale gelir ve bu, daha sonra Traffic Manager profiline bir uç nokta olarak eklenmesine izin verir.
5. Dinleyiciyi aşağıdaki seçeneklerden biriyle yapılandırın:
    * HTTP kullanıyorsanız, yapılandırılacak bir şey yoktur. **Tamam**'a tıklayın.
    * HTTPS kullanıyorsanız, daha fazla yapılandırma gerekir. Adım 9 ' dan başlayarak [uygulama ağ geçidi oluşturma](../application-gateway/quick-create-portal.md)bölümüne bakın. Yapılandırmayı tamamladığınızda **Tamam**' a tıklayın.

#### <a name="configure-url-routing-for-application-gateways"></a>Uygulama ağ geçitleri için URL yönlendirmeyi yapılandırma

Bir arka uç havuzu seçtiğinizde, yol tabanlı bir kuralla yapılandırılmış bir uygulama ağ geçidi, hepsini bir kez deneme dağıtımına ek olarak istek URL 'sinin yol modelini alır. Bu senaryoda, "/images/" ile herhangi bir URL 'YI görüntü sunucusu havuzuna yönlendirmek için yol tabanlı bir kural ekliyoruz \* . Uygulama ağ geçidi için URL yolu tabanlı yönlendirmeyi yapılandırma hakkında daha fazla bilgi için, bkz. [uygulama ağ geçidi için yol tabanlı bir kural oluşturma](../application-gateway/create-url-route-portal.md).

![Application Gateway Web katmanı diyagramı](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Kaynak grubunuzda, önceki bölümde oluşturduğunuz uygulama ağ geçidinin örneğine gidin.
2. **Ayarlar** altında, **arka uç havuzları**' nı seçin ve ardından Web katmanı arka uç havuzlarıyla ilişkilendirmek istediğiniz VM 'leri eklemek için **Ekle** ' yi seçin.
3. Arka uç havuzunun adını ve havuzda bulunan makinelerin tüm IP adreslerini girin. Bu senaryoda, sanal makinelerin iki arka uç sunucu havuzunu bağlanıyoruz.

   !["Arka uç Havuzu Ekle" Application Gateway](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Uygulama ağ geçidinin **Ayarlar** bölümünde **kurallar**' ı seçin ve ardından bir kural eklemek için **yol tabanlı** düğmesine tıklayın.

   ![Application Gateway kuralları "yol tabanlı" düğmesi](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Aşağıdaki bilgileri sağlayarak kuralı yapılandırın.

   Temel ayarlar:

   + **Ad**: portalda erişilebilen kuralın kolay adı.
   + **Dinleyici**: kural için kullanılan dinleyici.
   + **Varsayılan arka uç havuzu**: varsayılan kuralla kullanılacak arka uç Havuzu.
   + **Varsayılan http ayarları**: varsayılan KURALLA kullanılacak http ayarları.

   Yol tabanlı kurallar:

   + **Ad**: yol tabanlı kuralın kolay adı.
   + **Yollar**: trafiği iletmek için kullanılan yol kuralı.
   + **Arka uç havuzu**: bu kuralla kullanılacak arka uç Havuzu.
   + **Http ayarı**: Bu KURALLA kullanılacak http ayarları.

   > [!IMPORTANT]
   > Yollar: geçerli yollar "/" ile başlamalıdır. "" Joker karakterine \* yalnızca sonda izin verilir. Geçerli örnekler;/xaçıklık, \* /x, veya/xyız/ \* .

   ![Application Gateway "yol tabanlı kural ekle" dikey penceresi](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3. Adım: Traffic Manager uç noktalarına uygulama ağ geçitleri ekleme

Bu senaryoda Traffic Manager, farklı bölgelerde bulunan uygulama ağ geçitlerine (önceki adımlarda yapılandırıldığı gibi) bağlıdır. Artık uygulama ağ geçitleri yapılandırıldıktan sonra, bir sonraki adım bunları Traffic Manager profilinize bağlayayöneliktir.

1. Traffic Manager profilinizi açın. Bunu yapmak için, kaynak grubunuza bakın veya **tüm kaynaklardaki** Traffic Manager profilinin adını arayın.
2. Sol bölmede **uç noktalar**' ı seçin ve sonra bir uç nokta eklemek için **Ekle** ' ye tıklayın.

   ![Traffic Manager uç noktaları "Ekle" düğmesi](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Aşağıdaki bilgileri girerek bir uç nokta oluşturun:

   * **Tür**: yük dengelemeye yönelik uç nokta türünü seçin. Bu senaryoda, daha önce yapılandırılmış uygulama ağ geçidi örneklerine bağlandığımız için **Azure uç noktası** ' nı seçin.
   * **Ad**: uç noktanın adını girin.
   * **Hedef kaynak türü**: **genel IP adresi** ' ni seçin ve ardından **hedef kaynak** altında, daha önce yapılandırılan uygulama ağ geçidinin genel IP 'sini seçin.

   !["Uç nokta Ekle" Traffic Manager](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Artık, Traffic Manager profilinizin DNS 'sini kullanarak kurulumunuzu test edebilirsiniz (Bu örnekte: TrafficManagerScenario.trafficmanager.net). İstekleri yeniden gönderebilirsiniz, farklı bölgelerde oluşturulan VM 'Leri ve Web sunucularını alabilir ve bu ayarları, ayarlarınızı test etmek için Traffic Manager profil ayarlarını değiştirebilirsiniz.

### <a name="step-4-create-a-load-balancer"></a>4. Adım: yük dengeleyici oluşturma

Bu senaryoda, Load Balancer Web katmanından yüksek kullanılabilirlik kümesi içindeki veritabanlarına bağlantı dağıtır.

Yüksek kullanılabilirlik veritabanı kümeniz SQL Server AlwaysOn kullanıyorsa, adım adım yönergeler için [bir veya daha fazla Always on kullanılabilirlik grubu dinleyicilerini yapılandırma](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) bölümüne bakın.

İç yük dengeleyiciyi yapılandırma hakkında daha fazla bilgi için, [Azure Portal iç yük dengeleyici oluşturma](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)bölümüne bakın.

1. Azure Portal, sol bölmedeki **kaynak**  >  **ağ**  >  **yükü dengeleyici** oluştur ' a tıklayın.
2. Yük dengeleyiciniz için bir ad seçin.
3. **Türü** **iç** olarak ayarlayın ve yük dengeleyicinin içinde bulunacağı uygun sanal ağı ve alt ağı seçin.
4. **IP adresi ataması** altında **dinamik** veya **statik**' ı seçin.
5. **Kaynak grubu** altında yük dengeleyici için kaynak grubunu seçin.
6. **Konum**' un altında, yük dengeleyici için uygun bölgeyi seçin.
7. Yük dengeleyiciyi oluşturmak için **Oluştur** ' a tıklayın.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Arka uç veritabanı katmanını yük dengeleyiciye bağlama

1. Kaynak grubunuzda, önceki adımlarda oluşturulan yük dengeleyiciyi bulun.
2. **Ayarlar** altında arka uç **havuzları**' na tıklayın ve ardından **Ekle** ' ye tıklayarak bir arka uç havuzu ekleyin.

   !["Arka uç Havuzu Ekle" Load Balancer](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Arka uç havuzunun adını girin.
4. Arka uç havuzuna ayrı makineler veya kullanılabilirlik kümesi ekleyin.

#### <a name="configure-a-probe"></a>Araştırma yapılandırma

1. Yük dengeleyicinizdeki **Ayarlar** altında, **yoklamalar**' ı seçin ve ardından **Ekle** ' ye tıklayarak bir araştırma ekleyin.

   !["Araştırma ekle" Load Balancer](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Araştırmanın adını girin.
3. Araştırmanın **protokolünü** seçin. Bir veritabanı için, HTTP araştırması yerine bir TCP araştırmasını isteyebilirsiniz. Yük dengeleyici araştırmaları hakkında daha fazla bilgi edinmek için [yük dengeleyici araştırmalarını anlama](../load-balancer/load-balancer-custom-probe-overview.md)bölümüne bakın.
4. Araştırmayla ilgili olarak kullanılacak veritabanınızın **bağlantı noktasını** girin.
5. **Aralık** altında, uygulamanın ne sıklıkta araştırılıp araştıralınacağını belirtin.
6. **Sağlıksız eşik** altında, arka uç VM 'sinin sağlıksız olarak kabul edilmesi için gerçekleşmesi gereken sürekli araştırma hatası sayısını belirtin.
7. Araştırmayı oluşturmak için **Tamam** ' ı tıklatın.

#### <a name="configure-the-load-balancing-rules"></a>Yük Dengeleme kurallarını yapılandırma

1. Yük dengeleyicinizin **ayarları** bölümünde **Yük Dengeleme kuralları**' nı seçin ve ardından **Ekle** ' ye tıklayarak bir kural oluşturun.
2. Yük Dengeleme kuralı için bir **ad** girin.
3. Yük dengeleyici, **protokol** ve **bağlantı NOKTASıNıN** **ön uç IP adresini** seçin.
4. **Arka uç bağlantı noktası** altında arka uç havuzunda kullanılacak bağlantı noktasını belirtin.
5. Kuralı uygulamak için önceki adımlarda oluşturulan **arka uç havuzunu** ve **araştırmayı** seçin.
6. **Oturum kalıcılığı** altında, oturumların nasıl kalıcı olmasını istediğinizi seçin.
7. **Boşta kalma zaman aşımları** altında, boşta kalma zaman aşımından önceki dakika sayısını belirtin.
8. **Kayan IP** altında **devre dışı** ya da **etkin**' i seçin.
9. Kuralı oluşturmak için **Tamam**'a tıklayın.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>5. Adım: Web katmanı VM 'lerini yük dengeleyiciye bağlama

Artık, herhangi bir veritabanı bağlantısı için Web katmanı sanal makinelerinizdeki çalışan uygulamalarda IP adresini ve yük dengeleyici ön uç bağlantı noktasını yapılandıracağız. Bu yapılandırma, bu VM 'lerde çalışan uygulamalara özeldir. Hedef IP adresini ve bağlantı noktasını yapılandırmak için uygulama belgelerine bakın. Ön ucun IP adresini bulmak için, Azure portal, **yük dengeleyici ayarlarındaki** ön uç IP havuzuna gidin.

!["Ön uç IP havuzu" gezinti bölmesi Load Balancer](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Traffic Manager'a Genel Bakış](traffic-manager-overview.md)
* [Application Gateway’e genel bakış](../application-gateway/overview.md)
* [Azure Load Balancer’a genel bakış](../load-balancer/load-balancer-overview.md)