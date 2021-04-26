---
title: Azure DNS ve Traffic Manager kullanarak olağanüstü durum kurtarma | Microsoft Docs
description: Azure DNS ve Traffic Manager kullanarak olağanüstü durum kurtarma çözümlerine genel bakış.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: kumud
ms.openlocfilehash: 077e92b67f0cf6dac673cc870b7ff8c86fbe60dd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551297"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Azure DNS ve Traffic Manager kullanarak olağanüstü durum kurtarma

Olağanüstü durum kurtarma, uygulama işlevselliğinin önemli bir kaybından kurtarılmasına odaklanır. Bir olağanüstü durum kurtarma çözümü seçmek için, iş ve teknoloji sahipleri öncelikle, bir olağanüstü durum (örneğin, azaltılmış işlevsellik veya geciktirilen kullanılabilirlik ya da tam olarak kullanılabilir) için gereken işlevsellik düzeyini belirlememelidir.
Çoğu kurumsal müşteri, uygulama veya altyapı düzeyinde yük devretmeye karşı dayanıklılık için çok bölgeli bir mimari seçmektir. Müşteriler, ek mimari aracılığıyla yük devretme ve yüksek kullanılabilirlik elde etmek için, bu mühendisde çeşitli yaklaşımlar seçebilirler. Popüler yaklaşımlardan bazıları şunlardır:

- **Etkin-Pasif bekleme**: Bu yük devretme çözümünde, yük devretme için gerekli olana kadar VM 'ler ve bekleme bölgesinde çalışan diğer gereçler etkin değildir. Ancak, üretim ortamı yedekler, VM görüntüleri veya Kaynak Yöneticisi şablonları şeklinde farklı bir bölgeye çoğaltılır. Bu yük devretme mekanizması düşük maliyetli, ancak tam yük devretme işleminin daha uzun sürmesine neden olur.
 
    ![Soğuk bekleme ile etkin/Pasif](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Şekil-soğuk bekleme olağanüstü durum kurtarma yapılandırması ile etkin/Pasif*

- **Etkin/Pasif ve pilot ışığı**: Bu yük devretme çözümünde, bekleme ortamı en düşük yapılandırmayla ayarlanır. Kurulum yalnızca en az ve kritik bir uygulama kümesini desteklemek için çalışan gerekli hizmetleri içerir. Bu senaryo, yerel biçiminde yalnızca minimum işlevselliği yürütebilir, ancak bir yük devretme gerçekleşirse üretim yükünün toplu olması için daha fazla hizmet oluşturabilir ve oluşturabilir.
    
    ![Pilot hafif etkin/Pasif](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Şekil: pilot hafif olağanüstü durum kurtarma yapılandırması ile etkin/Pasif*

- **Etkin/Pasif Ile etkin/Pasif**: Bu yük devretme çözümünde, bekleme bölgesi önceden çarpımış olur ve temel yükü almaya hazır hale gelir, otomatik ölçeklendirme açıktır ve tüm örnekler çalışır durumdadır. Bu çözüm, tam üretim yükünü alacak, ancak işlevsel ve tüm hizmetler çalışır durumda olacak şekilde ölçeklendirilmez. Bu çözüm, pilot ışığı yaklaşımının genişletilmiş bir sürümüdür.
    
    ![Yarı etkin bekleme ile etkin/Pasif](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Şekil: etkin/Pasif olağanüstü durum kurtarma yapılandırması*
    
Yük devretme ve yüksek kullanılabilirlik hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Olağanüstü durum kurtarma mimarinizi planlama

Olağanüstü durum kurtarma mimarinizi ayarlamanın iki teknik yönü vardır:
-  Birincil ve bekleme ortamları arasında örnekleri, verileri ve konfigürasyonları çoğaltmak için bir dağıtım mekanizması kullanma. Bu tür olağanüstü durum kurtarma, Azure Site-Recovery aracılığıyla VERITAS veya NetApp gibi Microsoft Azure iş ortağı gereçlerine göre yerel olarak yapılabilir. 
- Birincil siteden bekleme sitesine ağ/web trafiği için bir çözüm geliştirme. Bu tür olağanüstü durum kurtarma Azure DNS, Azure Traffic Manager (DNS) veya üçüncü taraf küresel yük dengeleyiciler aracılığıyla elde edilebilir.

Bu makale ağ ve web trafiği yeniden yönlendirme aracılığıyla yaklaşımlar ile sınırlıdır. Azure Site Recovery ayarlama yönergeleri için bkz. [Azure Site Recovery belgeleri](../site-recovery/index.yml).
DNS genellikle genel ve veri merkezine ait olduğundan ve bölge veya kullanılabilirlik bölgesi (AZ) düzeyindeki hatalardan ayrı olduğundan, ağ trafiğini incelemek için en verimli mekanizmalardan biridir. Bunlardan biri DNS tabanlı yük devretme mekanizmasını kullanabilir ve Azure 'da iki DNS hizmeti, bazı moda Azure DNS (yetkili DNS) ve Azure Traffic Manager (DNS tabanlı akıllı trafik yönlendirme) ile aynı işlemi gerçekleştirebilir. 

Bu makalede sunulan çözümleri tartışmak için yaygın olarak kullanılan DNS 'in bazı kavramlarını anlamak önemlidir:
- **DNS A kaydı** – bir kayıt, bir etki alanını IPv4 adresine işaret eden işaretçilerdir. 
- **CNAME veya kurallı ad** -bu kayıt türü, başka bir DNS kaydına işaret etmek için kullanılır. CNAME bir IP adresi ile yanıt vermez, bunun yerine IP adresini içeren kayda yönelik işaretçi. 
- **Ağırlıklı yönlendirme** – bir ağırlığı hizmet uç noktalarına ilişkilendirmeyi ve ardından, atanan ağırlıklara göre trafiği dağıtmayı seçebilir. Bu yönlendirme yöntemi, Traffic Manager içinde kullanılabilen dört trafik yönlendirme mekanizmalarından biridir. Daha fazla bilgi için bkz. [ağırlıklı yönlendirme yöntemi](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Öncelik yönlendirme** – öncelikli yönlendirme, uç noktaların durum denetimlerini temel alır. Varsayılan olarak, Azure Traffic Manager tüm trafiği en yüksek öncelikli uç noktaya gönderir ve bir hata ya da olağanüstü durum Traffic Manager, trafiği ikincil uç noktaya yönlendirir. Daha fazla bilgi için bkz. [Öncelik yönlendirme yöntemi](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Azure DNS kullanarak el ile yük devretme
Olağanüstü durum kurtarma için Azure DNS el ile yük devretme çözümü, yedekleme sitesine yük devretmek için standart DNS mekanizmasını kullanır. Azure DNS aracılığıyla el ile seçeneği, soğuk bekleme veya pilot ışığı yaklaşımıyla birlikte kullanıldığında en iyi şekilde çalışmaktadır. 

![Azure DNS kullanarak el ile yük devretme](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Şekil-Azure DNS kullanarak el Ile yük devretme*

Çözüm için yapılan varsayımlar şunlardır:
- Birincil ve ikincil uç noktalarında, genellikle değişmeyen statik IP 'Ler vardır. Birincil site için IP 'nin 100.168.124.44 ve ikincil sitenin IP 'si 100.168.124.43.
- Birincil ve ikincil site için bir Azure DNS bölgesi bulunur. Birincil site için bitiş noktası prod.contoso.com ve yedekleme sitesi için dr.contoso.com. Www contoso.com olarak bilinen ana uygulama için bir DNS kaydı \. da mevcuttur.   
- TTL, kuruluşta ayarlanan RTO SLA 'nın altında veya altında. Örneğin, bir kuruluş uygulama olağanüstü durum yanıtının RTO 'ı 60 dakika olarak ayarladığında, TTL en az 60 dakika olmalıdır, tercihen daha iyi olur. 
  El ile yük devretme için Azure DNS aşağıdaki gibi ayarlayabilirsiniz:
- DNS bölgesi oluşturma
- DNS bölge kayıtları oluşturma
- CNAME kaydını Güncelleştir

### <a name="step-1-create-a-dns"></a>1. Adım: DNS oluşturma
Aşağıda gösterildiği gibi bir DNS bölgesi (örneğin, www \. contoso.com) oluşturun:

![Azure 'da bir DNS bölgesi oluşturma](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Şekil-Azure 'da bir DNS bölgesi oluşturma*

### <a name="step-2-create-dns-zone-records"></a>2. Adım: DNS bölge kayıtları oluşturma

Bu bölge içinde, aşağıda göster olarak üç kayıt (örneğin \. , www contoso.com, prod.contoso.com ve Dr.consoto.com) oluşturun.

![DNS bölge kayıtları oluşturma](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Şekil-Azure 'da DNS bölge kayıtları oluşturma*

Bu senaryoda www \. contoso.com, 30 dakikalık BIR TTL 'e sahiptir ve bu, belirtilen RTO 'nın altında iyi bir değer olan ve Prod.contoso.com üretim sitesini işaret ediyor. Bu yapılandırma normal iş işlemleri sırasında yapılır. Prod.contoso.com ve dr.contoso.com TTL değeri 300 saniye veya 5 dakika olarak ayarlanmıştır. Azure Izleyici veya Azure Application Insights gibi bir Azure izleme hizmetini veya dynaTrace gibi iş ortağı izleme çözümlerini kullanabilirsiniz. Aynı şekilde, uygulama veya sanal altyapı düzeyindeki sorunları izleyebildiği veya algılayan ev büyüme çözümlerini de kullanabilirsiniz.

### <a name="step-3-update-the-cname-record"></a>3. Adım: CNAME kaydını güncelleştirme

Hata algılandıktan sonra, aşağıda gösterildiği gibi, kayıt değerini dr.contoso.com işaret etmek üzere değiştirin:
       
![CNAME kaydını Güncelleştir](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Şekil-Azure 'da CNAME kaydını güncelleştirme*

30 dakika içinde, çoğu çözümleyiciler önbelleğe alınmış bölge dosyasını yenilediğinde, tüm www \. contoso.com sorgusu Dr.contoso.com 'e yönlendirilir.
CNAME değerini değiştirmek için aşağıdaki Azure CLı komutunu da çalıştırabilirsiniz:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Bu adım el ile veya Otomasyon aracılığıyla çalıştırılabilir. Konsol veya Azure CLı aracılığıyla el ile yapılabilir. Azure SDK ve API, el ile müdahale gerekmeden CNAME güncelleştirmesini otomatikleştirmek için kullanılabilir. Otomasyon, Azure işlevleri aracılığıyla veya bir üçüncü taraf izleme uygulamasında veya Şirket içinden bile oluşturulabilir.

### <a name="how-manual-failover-works-using-azure-dns"></a>Azure DNS kullanarak el ile yük devretme nasıl kullanılır
DNS sunucusu yük devretme veya olağanüstü durum bölgesinin dışında olduğundan, herhangi bir kapalı kalma süresine karşı ayrı olur. Bu, kullanıcının uygun maliyetli olan basit bir yük devretme senaryosunu mimarmasına olanak sağlar ve işlecin olağanüstü durum sırasında ağ bağlantısına sahip olduğunu ve ters çevirmeyi yapıp yapacağını kabul eden tüm zamanı işler. Çözüm komut dosyasıyla karşılaşırsanız, bunlardan biri, betiği çalıştıran sunucu veya hizmetin, üretim ortamını etkileyen soruna karşı yalıtılmış olmasını sağlamalıdır. Ayrıca, dünyanın her yerindeki bir çözümleyici bitiş noktasının uzun süre önbelleğe alınmasını ve müşterilerin RTO içindeki siteye erişmesini sağlamak için bölgeye göre ayarlanan düşük TTL 'yi aklınızda bulundurun. Bir soğuk bekleme ve pilot ışığı için, bir veya daha fazla yönetim etkinliği gerekli olabileceğinden, tek yapmanız gereken bir süre sonra da geçiş yapmadan önce yeterli zaman vermelidir.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Azure Traffic Manager kullanarak otomatik yük devretme
Karmaşık mimarilerin ve aynı işlevi gerçekleştirebilen birden çok kaynak kümesi olduğunda, kaynaklarınızın sistem durumunu denetlemek ve trafiği sağlıklı olmayan kaynaktan sağlıklı kaynağa yönlendirmek için Azure Traffic Manager (DNS tabanlı) yapılandırabilirsiniz. Aşağıdaki örnekte, hem birincil bölgenin hem de ikincil bölgenin tam dağıtımı vardır. Bu dağıtım, bulut hizmetlerini ve eşitlenmiş bir veritabanını içerir. 

![Azure Traffic Manager kullanarak otomatik yük devretme](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Şekil-Azure Traffic Manager kullanarak otomatik yük devretme*

Ancak, yalnızca birincil bölge, kullanıcılardan gelen ağ isteklerini etkin bir şekilde işliyor. İkincil bölge yalnızca birincil bölge bir hizmet kesintisi yaşıyorsa etkin hale gelir. Bu durumda, tüm yeni ağ istekleri ikincil bölgeye yönlendirir. Veritabanının yedeklemesi anlık bir yerde olduğundan, her iki yük dengeleyicinin sistem durumu denetimli bir IP 'si vardır ve örnekler her zaman çalışır durumda olur ve bu topoloji, el ile müdahale olmadan düşük RTO ve yük devretme için bir seçenek sunar. Birincil bölgenin hatasından sonra, ikincil yük devretme bölgesinin hemen ardından etkin olması gerekir.
Bu senaryo, http/https ve TCP gibi çeşitli türlerde sistem durumu denetimleri için yerleşik yoklamalar içeren Azure Traffic Manager kullanımı için idealdir. Azure Traffic Manager 'da, aşağıda açıklandığı gibi bir hata oluştuğunda yük devretmek için yapılandırılabilecek bir kural altyapısı da vardır. Traffic Manager kullanarak aşağıdaki çözümü ele alalım:
- Müşteri, prod.contoso.com olarak bilinen ve statik IP 'si 100.168.124.44 olarak bilinen bir #2 bölge #1 uç noktası olan bölgeye sahiptir. 
-   Bu ortamların her biri, yük dengeleyici gibi genel kullanıma yönelik bir özellik aracılığıyla alınmıştır. Yük dengeleyici, yukarıda gösterildiği gibi, DNS tabanlı bir uç noktaya veya tam etki alanı adına (FQDN) sahip olacak şekilde yapılandırılabilir.
-   Bölge 2 ' deki tüm örnekler, bölge 1 ' de neredeyse gerçek zamanlı çoğaltmada bulunur. Ayrıca, makine görüntüleri güncel olur ve tüm yazılım/yapılandırma verileri düzeltme eki uygulanır ve bölge 1 ' de yer alan bir satır içinde bulunur.  
-   Otomatik ölçeklendirme önceden yapılandırılmıştır. 

Azure Traffic Manager ile yük devretmeyi yapılandırmak için uygulanan adımlar şunlardır:
1. Yeni bir Azure Traffic Manager profili oluşturma
2. Traffic Manager profili içinde uç noktalar oluşturma
3. Sistem durumu denetimi ve yük devretme yapılandırmasını ayarlama

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>1. Adım: yeni bir Azure Traffic Manager profili oluşturma
Contoso123 adlı yeni bir Azure Traffic Manager profili oluşturun ve yönlendirme yöntemini öncelik olarak seçin. İlişkilendirmek istediğiniz önceden var olan bir kaynak grubunuz varsa, var olan bir kaynak grubunu seçebilirsiniz, aksi takdirde yeni bir kaynak grubu oluşturabilirsiniz.

![Traffic Manager profili oluşturma](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Şekil-Traffic Manager profili oluşturma*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>2. Adım: Traffic Manager profili içinde uç noktalar oluşturma

Bu adımda, üretim ve olağanüstü durum kurtarma sitelerine işaret eden uç noktalar oluşturursunuz. Burada, bir dış uç nokta olarak **türü** seçin, ancak kaynak Azure 'Da barındırılıyorsa **Azure uç noktası** ' nı da seçebilirsiniz. **Azure uç noktası**' nı seçerseniz, Azure tarafından ayrılan **App SERVICE** veya **genel IP** olan bir **hedef kaynak** seçin. Öncelik 1, bölge 1 ' in birincil hizmeti olduğundan **1** olarak ayarlanır.
Benzer şekilde, Traffic Manager de olağanüstü durum kurtarma uç noktası oluşturun.

![Olağanüstü durum kurtarma uç noktaları oluşturma](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Şekil-olağanüstü durum kurtarma uç noktaları oluşturma*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>3. Adım: sistem durumu denetimi ve yük devretme yapılandırmasını ayarlama

Bu adımda, DNS TTL 'sini, internet 'e yönelik özyinelemeli çözümleyicilerleyenler tarafından kabul edilecek şekilde 10 saniye olarak ayarlarsınız. Bu yapılandırma, hiçbir DNS Çözümleyicisinin 10 saniyeden uzun süre içinde bilgi önbelleğe alacağını belirtir. Uç nokta izleyici ayarları için, yol/veya root geçerli kümesidir, ancak bir yolu değerlendirmek için uç nokta ayarlarını özelleştirebilirsiniz, örneğin, prod.contoso.com/index. Aşağıdaki örnekte, yoklama protokolü olarak **https** gösterilmektedir. Ancak, **http** veya **TCP** de seçebilirsiniz. Protokol seçimi, son uygulamaya bağlıdır. Yoklama aralığı, hızlı yoklama sağlayan 10 saniye olarak ayarlanır ve yeniden deneme 3 olarak ayarlanır. Sonuç olarak, art arda üç Aralık bir hata kaydı olursa Traffic Manager ikinci uç noktaya yük devreder. Aşağıdaki formül otomatik yük devretme için toplam süreyi tanımlar: yük devretme için süre = TTL + yeniden dene * yoklama aralığı ve bu durumda, değer 10 + 3 * 10 = 40 saniye (max) olur.
Yeniden deneme 1 olarak ayarlanmışsa ve TTL 10 saniye olarak ayarlanırsa, yük devretme 10 + 1 * 10 = 20 saniye için zaman. Hatalı pozitif sonuçlar veya herhangi bir küçük ağ hüşileri nedeniyle yük devretme olasılığını ortadan kaldırmak için yeniden denemeyi **1** ' den büyük bir değere ayarlayın. 


![Sistem durumu denetimini ayarlama](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Şekil-sistem durumu denetimi ve yük devretme yapılandırmasını ayarlama*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Otomatik yük devretme Traffic Manager kullanarak nasıl kullanılır

Bir olağanüstü durum sırasında, birincil uç nokta denetlenir ve durum **düşürülmüş** olarak değişir ve olağanüstü durum kurtarma sitesi **çevrimiçi** kalır. Traffic Manager varsayılan olarak tüm trafiği birincil (en yüksek öncelikli) uç noktaya gönderir. Birincil uç nokta düzeyi düşürülmüş görünüyorsa, Traffic Manager sağlıklı kaldığı sürece trafiği ikinci uç noktaya yönlendirir. Bunlardan biri, ek yük devretme uç noktaları olarak kullanılabilecek Traffic Manager içinde daha fazla uç nokta yapılandırabilir veya yük dengeleyiciler uç noktalar arasında paylaşımı paylaşıyor.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)hakkında daha fazla bilgi edinin.
- [Azure DNS](../dns/dns-overview.md)hakkında daha fazla bilgi edinin.