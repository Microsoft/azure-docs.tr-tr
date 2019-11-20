---
title: Azure ağ Izleyicisi | Microsoft Docs
description: Azure ağ Izleyicisi 'nin bir sanal ağdaki kaynaklara yönelik izleme, tanılama, ölçümler ve günlüğe kaydetme özellikleri hakkında bilgi edinin.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
Customer intent: As someone with basic Azure network experience, I want to understand how Azure Network Watcher can help me resolve some of the network-related problems I've encountered and provide insight into how I use Azure networking.
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: e6ff3e6798fadca89455790efa28891c02e9e459
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996961"
---
# <a name="what-is-azure-network-watcher"></a>Azure ağ Izleyicisi nedir?

Azure ağ Izleyicisi, bir Azure sanal ağındaki kaynakların günlüklerini izlemek, tanılamak, görüntülemek ve etkinleştirmek ya da devre dışı bırakmak için araçlar sağlar. Ağ Izleyicisi, sanal makineler, sanal ağlar, uygulama ağ geçitleri, yük dengeleyiciler, vb. dahil olmak üzere IaaS (hizmet olarak altyapı) ürünlerinin ağ durumunu izlemek ve onarmak için tasarlanmıştır. Not: için tasarlanmamıştır ve PaaS izleme veya web analizi için çalışmayacak. 

## <a name="monitoring"></a>İzleme

### <a name = "connection-monitor"></a>Bir sanal makine ve uç nokta arasındaki iletişimi izleme

Uç noktalar başka bir sanal makine (VM), tam etki alanı adı (FQDN), bir Tekdüzen Kaynak tanımlayıcısı (URI) veya IPv4 adresi olabilir. *Bağlantı İzleyicisi* özelliği, iletişimi düzenli bir aralıkla Izler ve VM ile uç nokta arasındaki ulaşılabilirlik, gecikme süresi ve ağ topolojisi değişiklikleri hakkında bilgilendirir. Örneğin, bir veritabanı sunucusu VM 'si ile iletişim kuran bir Web sunucusu sanal makinesine sahip olabilirsiniz. Kuruluşunuzdaki birisi sizin için bilinmiyor, Web sunucusuna veya veritabanı sunucusu VM 'sine veya alt ağına özel bir yol veya ağ güvenlik kuralı uygulayabilirsiniz.

Bir uç nokta ulaşılamaz hale gelirse, bağlantı sorunlarını giderme nedeni sizi bilgilendirir. Olası nedenler, bir sanal makinenin işletim sistemi içindeki bir DNS ad çözümleme sorunu, CPU, bellek veya güvenlik duvarı ya da özel bir yolun atlama türü ya da giden bağlantının VM veya alt ağı için güvenlik kuralı olabilir. Azure 'da [güvenlik kuralları](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#security-rules) ve [yol atlama türleri](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) hakkında daha fazla bilgi edinin.

Bağlantı İzleyicisi, zaman içinde gözlenen en düşük, ortalama ve en yüksek gecikme süresini de sağlar. Bir bağlantı gecikmesini öğrendikten sonra, Azure kaynaklarınızı farklı Azure bölgelerine taşıyarak gecikmeyi azaldığınızı fark edebilirsiniz. [Azure bölgeleri ve internet hizmet sağlayıcıları arasındaki göreli gecikme](#determine-relative-latencies-between-azure-regions-and-internet-service-providers) sürelerini belirleme ve [bağlantı IZLEYICISI](connection-monitor.md)ile bir VM ile uç nokta arasındaki iletişimin nasıl izleneceği hakkında daha fazla bilgi edinin. Bağlantıyı zaman içinde izlemek yerine bir zaman noktasında test etmek istiyorsanız, Bağlantı İzleyicisi ile yaptığınız gibi bağlantı [sorunlarını giderme](#connection-troubleshoot) özelliğini kullanın.

Ağ Performansı İzleyicisi, ağ altyapınızın çeşitli noktaları arasındaki ağ performansını izlemenize yardımcı olan bulut tabanlı bir karma ağ izleme çözümüdür. Ayrıca hizmet ve uygulama uç noktalarına ağ bağlantısını izlemenize ve Azure ExpressRoute 'un performansını izlemenize yardımcı olur. Ağ Performansı İzleyicisi trafik kara gizleme, yönlendirme hataları ve geleneksel ağ izleme yöntemlerinin algılayamadığı sorunlar gibi ağ sorunlarını algılar. Çözüm, uyarılar oluşturur ve bir ağ bağlantısı için ihlal edildiğinde size bildirir. Ayrıca, ağ performans sorunlarının güncel olarak algılanmasını ve sorunun kaynağını belirli bir ağ kesimine veya cihazla yerelleştirir. [Ağ performansı İzleyicisi](../azure-monitor/insights/network-performance-monitor.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)hakkında daha fazla bilgi edinin.

### <a name="view-resources-in-a-virtual-network-and-their-relationships"></a>Bir sanal ağdaki kaynakları ve bunların ilişkilerini görüntüleme

Kaynak bir sanal ağa eklendikçe, bir sanal ağda hangi kaynakların olduğunu ve birbirleriyle ilişkilerini anlamak zor olabilir. *Topoloji* özelliği, bir sanal ağdaki kaynakların görsel bir diyagramını ve kaynaklar arasındaki ilişkileri oluşturmanıza olanak sağlar. Aşağıdaki resimde, üç alt ağ, iki VM, ağ arabirimi, genel IP adresi, ağ güvenlik grupları, yol tabloları ve kaynaklar arasındaki ilişkiler içeren bir sanal ağ için örnek bir topoloji diyagramı gösterilmektedir:

![Topoloji görünümü](./media/network-watcher-monitoring-overview/topology.png)

Resmin düzenlenebilir bir sürümünü SVG biçiminde indirebilirsiniz. [Topoloji görünümü](view-network-topology.md)hakkında daha fazla bilgi edinin.

## <a name="diagnostics"></a>Tanılamalar

### <a name="diagnose-network-traffic-filtering-problems-to-or-from-a-vm"></a>VM 'ye veya VM 'ye yönelik ağ trafiği Filtreleme sorunlarını tanılama

Bir VM dağıtırken, Azure VM 'ye veya VM 'ye giden trafiğe izin veren ya da reddeden çeşitli varsayılan güvenlik kuralları uygular. Azure 'un varsayılan kurallarını geçersiz kılabilir veya ek kurallar oluşturabilirsiniz. Bir noktada, bir sanal makine, bir güvenlik kuralı nedeniyle diğer kaynaklarla iletişim kuramaabilir. *IP akışı doğrulama* özelliği, bir kaynak ve hedef IPv4 adresi, bağlantı noktası, protokol (TCP veya UDP) ve trafik yönü (gelen veya giden) belirtmenize olanak sağlar. IP akışı doğrulama daha sonra iletişimi sınar ve bağlantı başarılı veya başarısız olduğunda sizi bilgilendirir. Bağlantı başarısız olursa, IP akışı doğrulama, sorunu çözebilmeniz için hangi güvenlik kuralına izin verileceğini veya iletişimin reddedildiğini söyler. [Bir sanal makineyi Tanıla ağ trafiği filtresi sorun](diagnose-vm-network-traffic-filtering-problem.md) öğreticisini tamamlayarak IP akışı doğrulama hakkında daha fazla bilgi edinin.

### <a name="diagnose-network-routing-problems-from-a-vm"></a>VM 'den ağ yönlendirme sorunlarını tanılama

Bir sanal ağ oluşturduğunuzda, Azure ağ trafiği için birkaç varsayılan giden yol oluşturur. Sanal bir ağda dağıtılan VM 'Ler gibi tüm kaynaklardaki giden trafik, Azure 'un varsayılan yollarına göre yönlendirilir. Azure 'un varsayılan yollarını geçersiz kılabilir veya ek yollar oluşturabilirsiniz. Belirli bir yol nedeniyle bir VM 'nin artık diğer kaynaklarla iletişim kuramaabileceğini fark edebilirsiniz. *Sonraki atlama* özelliği bir kaynak ve hedef IPv4 adresi belirtmenizi sağlar. Ardından, sonraki atlama, iletişimi sınar ve trafiği yönlendirmek için ne tür bir sonraki atlama kullanıldığını size bildirir. Daha sonra bir yönlendirme sorununu çözmek için bir rota kaldırabilir, değiştirebilir veya ekleyebilirsiniz. [Sonraki atlama](diagnose-vm-network-routing-problem.md) özelliği hakkında daha fazla bilgi edinin.

### <a name="connection-troubleshoot"></a>Bir VM 'den giden bağlantıları tanılama

*Bağlantı sorunlarını giderme* özelliği, bir VM ile başka bir VM, bir FQDN, URI veya IPv4 adresi arasındaki bağlantıyı test etmenize olanak sağlar. Sınama [, bağlantı izleyici özelliği kullanılırken](#connection-monitor) döndürülen benzer bilgileri döndürür, ancak bağlantı izleyici yaptığı sırada bağlantıyı zaman içinde izlemek yerine zaman içinde test eder. [Bağlantı sorunlarını giderme](network-watcher-connectivity-overview.md)kullanarak bağlantı sorunlarını giderme hakkında daha fazla bilgi edinin.

### <a name="capture-packets-to-and-from-a-vm"></a>Bir VM 'ye/sanal makineye paket yakalama

Gelişmiş filtreleme seçenekleri ve zaman ve boyut sınırlamalarını ayarlama yeteneği gibi ince ayarlanmış denetimler, çok yönlülük sağlar. Yakalama, Azure depolama 'da, VM 'nin diskinde veya her ikisinde depolanabilir. Daha sonra yakalama dosyasını birkaç standart ağ yakalama çözümleme aracı kullanarak çözümleyebilirsiniz. [Paket yakalama](network-watcher-packet-capture-overview.md)hakkında daha fazla bilgi edinin.

### <a name="diagnose-problems-with-an-azure-virtual-network-gateway-and-connections"></a>Azure sanal ağ geçidi ve bağlantılarıyla ilgili sorunları tanılama

Sanal ağ geçitleri, şirket içi kaynaklar ile Azure sanal ağları arasında bağlantı sağlar. Ağ geçitlerini ve bağlantılarını izlemek, iletişimin kopuk olmamasını sağlamak için önemlidir. *VPN tanılama* özelliği, ağ geçitlerini ve bağlantıları tanılamanıza olanak sağlar. VPN tanılama, ağ geçidinin veya Ağ Geçidi bağlantısının sistem durumunu tanılar ve bir ağ geçidi ve ağ geçidi bağlantısı olup olmadığını bildirir. Ağ geçidi veya bağlantı kullanılamıyorsa, VPN tanılaması size nedenini söyler. bu sayede sorunu çözebilirsiniz. [Ağlarla ilgili bir iletişim sorununu tanılama](diagnose-communication-problem-between-networks.md) öğreticisini tamamlayarak VPN tanılama hakkında daha fazla bilgi edinin.

### <a name="determine-relative-latencies-between-azure-regions-and-internet-service-providers"></a>Azure bölgeleri ve Internet hizmet sağlayıcıları arasındaki göreli gecikmeleri belirleme

Azure bölgeleri ve internet hizmet sağlayıcıları arasında gecikme bilgileri için ağ Izleyicisi 'Ni sorgulayabilirsiniz. Azure bölgeleri ve Internet hizmet sağlayıcıları arasında gecikme sürelerini bildiğiniz zaman, ağ yanıt süresini iyileştirmek için Azure kaynakları dağıtabilirsiniz. [Göreli gecikme](view-relative-latencies.md)süreleri hakkında daha fazla bilgi edinin.

### <a name="view-security-rules-for-a-network-interface"></a>Ağ arabirimi için güvenlik kurallarını görüntüleme

Ağ arabirimi için geçerli güvenlik kuralları, ağ arabirimine uygulanan tüm güvenlik kurallarının ve ağ arabiriminin içinde bulunduğu alt ağın bir birleşimidir.  *Güvenlik grubu görünümü* özelliği, ağ arabirimine uygulanan tüm güvenlik kurallarını, ağ arabiriminin içinde bulunduğu alt ağı ve ikisinin toplamasını gösterir. Bir ağ arabirimine hangi kuralların uygulandığını anlamak için, değiştirmek istediğiniz trafiğe izin vermek veya erişimi reddetmek için kuralları ekleyebilir, kaldırabilir veya değiştirebilirsiniz. [Güvenlik grubu görünümü](network-watcher-security-group-view-overview.md)hakkında daha fazla bilgi edinin.

## <a name="metrics"></a>Ölçümler

Bir Azure aboneliği ve bölgesi içinde oluşturabileceğiniz ağ kaynaklarının sayısı için [sınırlar](../azure-subscription-service-limits.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#azure-resource-manager-virtual-networking-limits) vardır. Limitleri karşıladıysanız, abonelik veya bölge dahilinde daha fazla kaynak oluşturamazsınız. *Ağ aboneliği sınırı* özelliği, her bir ağ kaynağının kaç tane bir abonelikte ve bölgede dağıttığınıza ve sınırın kaynak için ne olduğunu gösteren bir Özet sağlar. Aşağıdaki resimde, örnek bir abonelik için Doğu ABD bölgesinde dağıtılan ağ kaynakları için kısmi çıkış gösterilmektedir:

![Abonelik limitleri](./media/network-watcher-monitoring-overview/subscription-limit.png)

Gelecekteki kaynak dağıtımlarını planlarken bilgiler yararlı olur.

## <a name="logs"></a>Açıldığında

### <a name="analyze-traffic-to-or-from-a-network-security-group"></a>Ağ güvenlik grubundan gelen veya giden trafiği çözümleme

Ağ güvenlik grupları (NSG), bir VM 'deki bir ağ arabirimine gelen veya giden trafiğe izin verir veya reddeder. *NSG akış günlüğü* özelliği, kaynak ve hedef IP adresini, bağlantı noktasını, protokolü ve bır NSG tarafından trafiğe izin verilip verilmeyeceğini ve reddedildiğini sağlar. PowerBI ve *Trafik Analizi* özelliği gibi çeşitli araçları kullanarak günlükleri analiz edebilirsiniz. Trafik Analizi, NSG akış günlüklerine yazılan verilerin zengin görselleştirmelerini sağlar. Aşağıdaki resimde, trafik analizinin NSG akış günlüğü verilerinden sunduğu bazı bilgiler ve görselleştirmeler gösterilmektedir:

![Trafik Analizi](./media/network-watcher-monitoring-overview/traffic-analytics.png)

[Bir sanal makine öğreticisine gelen ve giden günlük ağ trafiğini](network-watcher-nsg-flow-logging-portal.md) tamamlayarak ve [trafik analizinin](traffic-analytics.md)nasıl uygulanacağı NSG akış günlükleri hakkında daha fazla bilgi edinin.

### <a name="view-diagnostic-logs-for-network-resources"></a>Ağ kaynakları için tanılama günlüklerini görüntüle

Ağ güvenlik grupları, genel IP adresleri, yük dengeleyiciler, sanal ağ geçitleri ve uygulama ağ geçitleri gibi Azure ağ kaynakları için tanılama günlük kaydını etkinleştirebilirsiniz. *Tanılama günlükleri* özelliği, tanılama günlüğü oluşturan mevcut ağ kaynakları için ağ kaynak tanılama günlüklerini etkinleştirmek ve devre dışı bırakmak üzere tek bir arabirim sağlar. Tanılama günlüklerini, Microsoft Power BI ve Azure Izleyici günlükleri gibi araçları kullanarak görüntüleyebilirsiniz. Azure ağ tanılama günlüklerini çözümleme hakkında daha fazla bilgi edinmek için bkz. Azure [izleyici günlüklerinde Azure ağ çözümleri](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="network-watcher-automatic-enablement"></a>Ağ Izleyicisi otomatik etkinleştirme
Aboneliğinizde bir sanal ağ oluşturduğunuzda veya güncelleştirdiğinizde, sanal ağınızın bölgesinde ağ Izleyicisi otomatik olarak etkinleştirilir. Ağ İzleyicisini otomatik olarak etkinleştirmek için kaynaklarınız veya ilişkili ücretler üzerinde bir etkisi yoktur. Daha fazla bilgi için bkz. [Ağ İzleyicisi oluşturma](network-watcher-create.md).

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure ağ Izleyicisi 'ne bir genel bakış sunulmaktadır. Ağ Izleyicisi 'ni kullanmaya başlamak için IP akışı doğrulama kullanarak bir sanal makine ile ve arasında ortak bir iletişim sorunu tanılayın. Nasıl yapılacağını öğrenmek için bkz. [sanal makine ağ trafiği filtresini tanılama](diagnose-vm-network-traffic-filtering-problem.md) hızlı başlangıç.
