---
title: Azure 'da bağlantı Izleyicisi | Microsoft Docs
description: Dağıtılmış bir ortamdaki ağ iletişimini izlemek için bağlantı Izleyicisini nasıl kullanacağınızı öğrenin.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 24c181c17e49fe5b7c3001c1cb2839bc957ef463
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490497"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Bağlantı Izleyicisi ile ağ bağlantısı Izleme

> [!IMPORTANT]
> 1 Temmuz 2021 ' den itibaren, mevcut bir çalışma alanına yeni testler ekleyemez veya Ağ Performansı İzleyicisi yeni bir çalışma alanı etkinleştiremeyeceksiniz. Ayrıca, bağlantı Izleyicisinde (klasik) yeni bağlantı izleyicileri ekleyemeyeceksiniz. 1 Temmuz 2021 ' den önce oluşturulan testleri ve bağlantı izleyicilerini kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, [testlerinizi ağ performansı İzleyicisi ](migrate-to-connection-monitor-from-network-performance-monitor.md) veya  [bağlantı izleyicisinden (klasik)](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure ağ Izleyicisi 'ndeki yeni bağlantı Izleyicisinden, 29 Şubat 2024 tarihinden önce geçirin.

Bağlantı Izleyicisi, Azure ağ Izleyicisi 'nde birleştirilmiş uçtan uca bağlantı izleme sağlar. Bağlantı Izleyicisi özelliği karma ve Azure bulut dağıtımlarını destekler. Ağ Izleyicisi, Azure dağıtımlarınız için bağlantı ile ilgili ölçümleri izlemek, tanılamak ve görüntülemek için araçlar sağlar.

Bağlantı Izleyicisi için bazı kullanım durumları aşağıda verilmiştir:

- Ön uç Web sunucusu sanal makinesi, çok katmanlı bir uygulamadaki bir veritabanı sunucusu VM 'si ile iletişim kurar. İki VM arasındaki ağ bağlantısını kontrol etmek istiyorsunuz.
- Doğu ABD bölgesindeki VM 'Lerin Orta ABD bölgesindeki sanal makinelere ping işlemi olmasını ve bölgeler arası ağ gecikmelerinin karşılaştırmasını istiyorsunuz.
- Seattle, Washington ve Ashyakma, Virginia 'de birden çok şirket içi Office siteniz var. Office siteleriniz Microsoft 365 URL 'Lerine bağlanır. Microsoft 365 URL kullanıcılarınız için Seattle ve Ashburn arasındaki gecikme sürelerini karşılaştırın.
- Karma uygulamanızın bir Azure depolama uç noktasına bağlanması gerekiyor. Şirket içi siteniz ve Azure uygulamanız aynı Azure depolama uç noktasına bağlanır. Şirket içi sitenin gecikme sürelerini Azure uygulamasının gecikme süreleriyle karşılaştırmak istiyorsunuz.
- Şirket içi kurulumlarınız ile bulut uygulamanızı barındıran Azure VM 'Ler arasındaki bağlantıyı denetlemek istiyorsunuz.

Bağlantı Izleyicisi iki özelliği en iyi şekilde birleştirir: ağ Izleyicisi [Bağlantı İzleyicisi (klasik)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) özelliği ve ağ performansı İzleyicisi (NPM) [hizmet bağlantısı İzleyicisi](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [ExpressRoute izleme](../expressroute/how-to-npm.md)ve [performans izleme](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) özelliği.

Bağlantı Izleyicisi 'nin bazı avantajları aşağıda verilmiştir:

* Azure ve karma izleme ihtiyaçlarına yönelik Birleşik, sezgisel deneyim
* Bölgeler arası, çapraz çalışma alanı bağlantısı izleme
* Daha yüksek yoklama sıklıkları ve ağ performansına daha iyi görünürlük
* Karma dağıtımlarınız için daha hızlı uyarı
* HTTP, TCP ve ıCMP tabanlı bağlantı denetimleri için destek 
* Hem Azure hem de Azure olmayan test kurulumları için ölçümler ve Log Analytics desteği

![Bağlantı Izleyicisinin Azure VM 'Leri, Azure dışı Konakları, uç noktaları ve veri depolama konumları ile nasıl etkileşime gireceğini gösteren diyagram](./media/connection-monitor-2-preview/hero-graphic.png)

İzleme için bağlantı Izleyicisi 'ni kullanmaya başlamak için şu adımları izleyin: 

1. İzleme aracılarını yükler.
1. Aboneliğinizde ağ Izleyicisi 'Ni etkinleştirin.
1. Bağlantı İzleyicisi oluşturun.
1. Veri analizini ve uyarıları ayarlayın.
1. Ağınızdaki sorunları tanılayın.

Aşağıdaki bölümlerde bu adımlarla ilgili ayrıntılar sağlanmaktadır.

## <a name="install-monitoring-agents"></a>İzleme aracılarını yükler

Bağlantı Izleyicisi, bağlantı denetimlerini çalıştırmak için hafif yürütülebilir dosyaları kullanır. Azure ortamlarından ve şirket içi ortamların bağlantı denetimlerini destekler. Kullandığınız yürütülebilir dosya, sanal makinenizin Azure 'da mı yoksa şirket içinde mi barındırıldığından bağlıdır.

### <a name="agents-for-azure-virtual-machines"></a>Azure sanal makineleri için aracılar

Bağlantı Izleyicisi 'nin Azure VM 'lerinizi izleme kaynakları olarak tanımasını sağlamak için, ağ Izleyicisi Aracısı sanal makine uzantısını bunlara yükler. Bu uzantı, *Ağ İzleyicisi uzantısı* olarak da bilinir. Azure sanal makineleri, uzantının uçtan uca izlemeyi ve diğer gelişmiş işlevleri tetiklemesine gerek duyar. 

[BIR VM oluşturduğunuzda](./connection-monitor.md#create-the-first-vm)ağ izleyicisi uzantısını yükleyebilirsiniz. Ayrıca, [Linux](../virtual-machines/extensions/network-watcher-linux.md) ve [Windows](../virtual-machines/extensions/network-watcher-windows.md)için ağ izleyicisi uzantısını ayrı olarak yükleyebilir, yapılandırabilir ve sorun giderebilirsiniz.

Bir ağ güvenlik grubu (NSG) veya güvenlik duvarı için kurallar, kaynak ve hedef arasındaki iletişimi engelleyebilirler. Bağlantı Izleyicisi bu sorunu algılar ve topolojide bir tanılama iletisi olarak gösterir. Bağlantı izlemeyi etkinleştirmek için NSG ve Güvenlik Duvarı kurallarının, kaynak ve hedef arasında TCP veya ıCMP üzerinden paketlere izin verildiğinden emin olun.

### <a name="agents-for-on-premises-machines"></a>Şirket içi makineler için aracılar

Bağlantı Izleyicisi 'ni izlemek için kaynak olarak şirket içi makinelerinizi tanımasını sağlamak için, makinelere Log Analytics aracısını yüklersiniz.  Ağ Performansı İzleyicisi çözümü etkinleştirin. Bu aracılar Log Analytics çalışma alanlarına bağlanır, bu nedenle aracıların izlemeye başlamadan önce çalışma alanı KIMLIĞI ve birincil anahtarı ayarlamanız gerekir.

Windows makineleri için Log Analytics Aracısı 'nı yüklemek için bkz. [Windows Için Azure izleyici sanal makine uzantısı](../virtual-machines/extensions/oms-windows.md).

Yol güvenlik duvarları veya ağ sanal gereçler (NVA 'lar) içeriyorsa, hedefin erişilebilir olduğundan emin olun.

Windows makineleri için, bağlantı noktasını açmak üzere PowerShell penceresinde yönetici ayrıcalıklarına sahip herhangi bir parametre olmadan [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell betiğini çalıştırın.

Linux makinelerinde kullanılacak bağlantı noktası numaralarının el ile değiştirilmesi gerekir. 
* Yola gidin:/var/seçenek/Microsoft/omsagent/npm_state. 
* Dosya Aç: npmdregistry
* Bağlantı noktası numarası için değeri değiştirin ```“PortNumber:<port of your choice>”```

 Kullanılmakta olan bağlantı noktası numaralarının, bir çalışma alanında kullanılan tüm aracılarda aynı olması gerektiğini unutmayın. 

Betik, çözüm için gereken kayıt defteri anahtarlarını oluşturur. Ayrıca aracıların birbirleriyle TCP bağlantıları oluşturmalarına izin vermek için Windows güvenlik duvarı kuralları oluşturur. Betiği tarafından oluşturulan kayıt defteri anahtarları, hata ayıklama günlüklerinin kaydedilip edilmeyeceğini ve Günlükler dosyasının yolunu belirtir. Betik Ayrıca iletişim için kullanılan aracı TCP bağlantı noktasını tanımlar. Bu anahtarların değerleri otomatik olarak komut dosyası tarafından ayarlanır. Bu anahtarları el ile değiştirmeyin. Varsayılan olarak açılan bağlantı noktası 8084 ' dir. Komut dosyasına portNumber parametresini girerek özel bir bağlantı noktası kullanabilirsiniz. Betiğin çalıştırıldığı tüm bilgisayarlarda aynı bağlantı noktasını kullanın. Log Analytics aracıları için ağ gereksinimleri hakkında [daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#network-requirements)

Betik yalnızca Windows güvenlik duvarını yerel olarak yapılandırır. Bir ağ güvenlik duvarınız varsa, Ağ Performansı İzleyicisi tarafından kullanılan TCP bağlantı noktasına giden trafiğe izin verdiğinden emin olun.

## <a name="enable-network-watcher-on-your-subscription"></a>Aboneliğinizde ağ Izleyicisi 'Ni etkinleştirin

Sanal ağı olan tüm abonelikler ağ Izleyicisi ile etkinleştirilir. Aboneliğinizde bir sanal ağ oluşturduğunuzda, sanal ağın bölgesinde ve aboneliğinde ağ Izleyicisi otomatik olarak etkinleştirilir. Bu otomatik etkinleştirme, kaynaklarınızı etkilemez veya ücretlendirir. Ağ Izleyicisi 'nin aboneliğinizde açık olarak devre dışı olmadığından emin olun. 

Daha fazla bilgi için bkz. [ağ Izleyicisini etkinleştirme](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Bağlantı izleyicisi oluşturma 

Bağlantı Izleyicisi, iletişimi düzenli aralıklarla izler. Ulaşılabilirlik ve gecikme süresi içindeki değişiklikleri bilgilendirir. Ayrıca, kaynak aracıları ile hedef uç noktalar arasındaki geçerli ve geçmiş ağ topolojisini kontrol edebilirsiniz.

Kaynaklar, yüklü bir izleme aracısına sahip Azure sanal makineleri veya şirket içi makineler olabilir. Hedef uç noktalar Microsoft 365 URL 'Ler, Dynamics 365 URL 'Leri, özel URL 'ler, Azure VM kaynak kimlikleri, IPv4, IPv6, FQDN veya herhangi bir etki alanı adı olabilir.

### <a name="access-connection-monitor"></a>Erişim bağlantısı Izleyicisi

1. Azure portal giriş sayfasında **Ağ İzleyicisi**' ne gidin.
1. Sol tarafta, **izleme** bölümünde **Bağlantı İzleyicisi**' ni seçin.
1. Bağlantı Izleyicisinde oluşturulan tüm bağlantı izleyicilerini görürsünüz. Klasik bağlantı Izleyici deneyiminde oluşturulan bağlantı izleyicilerini görmek için **Bağlantı İzleyicisi** sekmesine gidin.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Bağlantı Izleyicisinde oluşturulan bağlantı izleyicilerini gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Bağlantı izleyicisi oluşturma

Bağlantı Izleyicisinde oluşturduğunuz bağlantı izleyicilerinde, hem şirket içi makineleri hem de Azure VM 'lerini kaynak olarak ekleyebilirsiniz. Bu bağlantı izleyicileri, uç noktalara bağlantıyı da izleyebilir. Uç noktalar Azure veya başka bir URL veya IP üzerinde olabilir.

Bağlantı Izleyicisi aşağıdaki varlıkları içerir:

* **Bağlantı İzleyicisi kaynağı** : bölgeye özgü bir Azure kaynağı. Aşağıdaki varlıkların hepsi bir bağlantı İzleyicisi kaynağının özellikleridir.
* **Uç nokta** : bağlantı denetimlerine katılan kaynak veya hedef. Uç noktalara örnek olarak Azure VM 'Leri, şirket içi aracılar, URL 'Ler ve IP 'Ler verilebilir.
* **Test yapılandırması** – bir test için protokole özgü bir yapılandırma. Seçtiğiniz protokole bağlı olarak, bağlantı noktası, eşikler, sınama sıklığı ve diğer parametreleri tanımlayabilirsiniz.
* **Test grubu** : kaynak uç noktaları, hedef uç noktaları ve test yapılandırmalarının bulunduğu grup. Bir bağlantı İzleyicisi birden fazla test grubu içerebilir.
* **Test** : kaynak uç noktası, hedef uç noktası ve test yapılandırmasının birleşimi. Test, izleme verilerinin kullanılabildiği en ayrıntılı düzeydir. İzleme verileri, başarısız olan denetim yüzdesini ve gidiş dönüş süresini (RTT) içerir.

 ![Test grupları ve testler arasındaki ilişkiyi tanımlayan bir bağlantı izleyicisini gösteren diyagram](./media/connection-monitor-2-preview/cm-tg-2.png)

[Azure Portal](./connection-monitor-create-using-portal.md), [armclient](./connection-monitor-create-using-template.md) veya [PowerShell](connection-monitor-create-using-powershell.md) kullanarak bir bağlantı İzleyicisi oluşturabilirsiniz

Bir test grubuna eklediğiniz tüm kaynaklar, hedefler ve test yapılandırması, bireysel testlere bölünmüştür. Kaynak ve hedeflerin nasıl bölündüğü hakkında bir örnek aşağıda verilmiştir:

* Test grubu: TG1
* Kaynaklar: 3 (A, B, C)
* Hedefler: 2 (D, E)
* Test yapılandırması: 2 (yapılandırma 1, yapılandırma 2)
* Oluşturulan toplam test sayısı: 12

| Sınama numarası | Kaynak | Hedef | Test yapılandırması |
| --- | --- | --- | --- |
| 1 | A | D | Yapılandırma 1 |
| 2 | A | D | Yapılandırma 2 |
| 3 | A | E | Yapılandırma 1 |
| 4 | A | E | Yapılandırma 2 |
| 5 | B | D | Yapılandırma 1 |
| 6 | B | D | Yapılandırma 2 |
| 7 | B | E | Yapılandırma 1 |
| 8 | B | E | Yapılandırma 2 |
| 9 | C | D | Yapılandırma 1 |
| 10 | C | D | Yapılandırma 2 |
| 11 | C | E | Yapılandırma 1 |
| 12 | C | E | Yapılandırma 2 |

### <a name="scale-limits"></a>Ölçek sınırları

Bağlantı izleyicileri aşağıdaki ölçek sınırlarına sahiptir:

* Her bölge için abonelik başına en fazla bağlantı İzleyicisi: 100
* Bağlantı İzleyicisi başına en fazla test grubu: 20
* Bağlantı İzleyicisi başına en fazla kaynak ve hedef sayısı: 100
* Bağlantı İzleyicisi başına en fazla test yapılandırması: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>İzleme verilerini çözümleme ve uyarıları ayarlama

Bir bağlantı İzleyicisi oluşturduktan sonra, kaynaklar test yapılandırmanıza göre hedeflere bağlantıyı denetler.

### <a name="checks-in-a-test"></a>Bir testte denetimler

Bağlantı Izleyicisi, test yapılandırmasında seçtiğiniz protokolüne bağlı olarak, kaynak-hedef çifti için bir dizi denetim çalıştırır. Denetimler, seçtiğiniz test sıklığına göre çalışır.

HTTP kullanırsanız, hizmet geçerli bir yanıt kodu döndüren HTTP yanıtlarının sayısını hesaplar. Geçerli yanıt kodları PowerShell ve CLı kullanılarak ayarlanabilir. Sonuç, başarısız denetimlerin yüzdesini belirler. RTT 'yi hesaplamak için, hizmet bir HTTP çağrısıyla yanıt arasındaki süreyi ölçer.

TCP veya ıCMP kullanıyorsanız, hizmet başarısız denetimlerin yüzdesini öğrenmek için paket kaybı yüzdesini hesaplar. RTT 'yi hesaplamak için, hizmet gönderilen paketlerin onay (ACK) alınması için geçen süreyi ölçer. Ağ testleriniz için izleme yolu verilerini etkinleştirdiyseniz, şirket içi ağınız için atlama kaybını ve gecikme süresini görebilirsiniz.

### <a name="states-of-a-test"></a>Bir testin durumları

Denetimlerin döndürdüğü verilere bağlı olarak, testler aşağıdaki durumlara sahip olabilir:

* **Pass** : başarısız denetimlerin yüzdesi için gerçek değerler ve RTT belirtilen eşiklerin içindedir.
* **Başarısız** – başarısız DENETIMLERIN veya RTT yüzdesinin gerçek değerleri belirtilen eşikleri aştı. Eşik belirtilmemişse, başarısız denetimlerin yüzdesi 100 olduğunda bir test başarısız durumuna ulaşır.
* **Uyarı** – 
     * Eşik belirtilirse ve bağlantı Izleyicisi obhizmet denetimleri, eşiğin yüzde 80 ' sinden daha fazla başarısız olduysa, test uyarı olarak işaretlenir.
     * Belirtilen eşiklerin yokluğunda, bağlantı Izleyicisi otomatik olarak bir eşik atar. Bu eşik aşıldığında, test durumu uyarı olarak değişir.TCP veya ıCMP testlerinde gidiş dönüş süresi için eşik değeri 750ms 'dir. % Başarısız denetimler için eşik %10 ' dur. 
* **Belirsiz**   – Log Analytics çalışma alanında veri yok.Ölçümleri denetleyin. 
* **Çalışmıyor**   – Test grubunu devre dışı bırakarak devre dışı  

### <a name="data-collection-analysis-and-alerts"></a>Veri toplama, analiz ve uyarılar

Bağlantı Izleyicisinin topladığı veriler Log Analytics çalışma alanında depolanır. Bu çalışma alanını bağlantı izleyicisini oluştururken ayarlarsınız. 

Azure Izleyici ölçümlerinde izleme verileri de mevcuttur. İzleme verilerinizi istediğiniz uzunlukta tutmak için Log Analytics kullanabilirsiniz. Azure Izleyici, ölçümleri varsayılan olarak yalnızca 30 gün boyunca depolar. 

[Veride ölçüm tabanlı uyarılar ayarlayabilirsiniz](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Panoları izleme

İzleme Panoları ' nde, abonelikleriniz, bölgeler, zaman damgaları, kaynaklarınız ve hedef türleri için erişebileceğiniz bağlantı izleyicilerinin bir listesini görürsünüz.

Ağ Izleyicisinden bağlantı Izleyicisi 'ne gittiğinizde, verileri şu şekilde görüntüleyebilirsiniz:

* **Bağlantı İzleyicisi** – abonelikleriniz, bölgeler, zaman damgaları, kaynaklarınız ve hedef türleri için oluşturulan tüm bağlantı izleyicilerinin listesi. Bu görünüm varsayılandır.
* **Test grupları** – abonelikleriniz, bölgelerleriniz, zaman damgaları, kaynaklarınız ve hedef türleri için oluşturulan tüm test gruplarının listesidir. Bu test grupları bağlantı izleyicilerine göre filtrelenmez.
* **Test** – abonelikleriniz, bölgeler, zaman damgaları, kaynaklarınız ve hedef türleriniz için çalışan tüm testlerin listesi. Bu testler bağlantı izleyicilerine veya test gruplarına göre filtrelenmez.

Aşağıdaki görüntüde, üç veri görünümü, ok 1 ile belirtilmiştir.

Panoda, her bir bağlantı izleyicisini genişleterek test gruplarını görüntüleyebilirsiniz. Ardından, içinde çalışan testleri görmek için her bir test grubunu genişletebilirsiniz. 

Bir listeyi temelinde filtreleyebilirsiniz:

* **Üst düzey filtreler** – liste metne, varlık türüne (Bağlantı İzleyicisi, test grubu veya test) zaman damgası ve kapsam olarak arama. Kapsam, abonelikleri, bölgeleri, kaynakları ve hedef türlerini içerir. Aşağıdaki görüntüde Box 1 ' i inceleyin.
* **Durum tabanlı filtreler** : Bağlantı İzleyicisi, test grubu veya test durumuna göre filtreleyin. Aşağıdaki görüntüde Box 2 ' ye bakın.
* **Uyarı tabanlı filtre** -uyarılara göre filtrele Bağlantı İzleyicisi kaynağında tetiklendi. Aşağıdaki görüntüde Box 3 ' ü inceleyin.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Bağlantı izleyicilerinde bağlantı izleyicilerinin, test gruplarının ve testlerin görünümlerinin nasıl filtreleneceğini gösteren ekran görüntüsü " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Örneğin, kaynak IP 'nin 10.192.64.56 olduğu bağlantı Izleyicisinde tüm testlere bakmak için:
1. Görünümü **Test** olacak şekilde değiştirin.
1. Arama alanına *10.192.64.56* yazın.
1. En üst düzey filtre **kapsamında** , **kaynaklar**' ı seçin.

Kaynak IP 'nin 10.192.64.56 olduğu bağlantı Izleyicisinde yalnızca başarısız testleri göstermek için:
1. Görünümü **Test** olacak şekilde değiştirin.
1. Durum tabanlı filtre için **başarısız**' ı seçin.
1. Arama alanına *10.192.64.56* yazın.
1. En üst düzey filtre **kapsamında** , **kaynaklar**' ı seçin.

Yalnızca hedefin outlook.office365.com olduğu bağlantı Izleyicisinde başarısız testleri göstermek için:
1. Görünümü **Test** olarak değiştirin.
1. Durum tabanlı filtre için **başarısız**' ı seçin.
1. Arama alanına *Office.Live.com* girin.
1. En üst düzey filtre **kapsamında** , **hedefler**' i seçin.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Outlook.Office365.com hedefi için yalnızca başarısız testleri göstermek üzere filtrelenmiş bir görünümü gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Bir bağlantı izleyici veya test grubu ya da test hatasının nedenini bildirmek için, nedeni adlı sütuna tıklayın.  Bu, hangi eşiğin (başarısız olan% veya RTT) ihlal ettiğini ve ilgili tanılama iletilerini belirtir
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Bağlantı İzleyicisi, test veya test grubu için hata nedenini gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
RTT içindeki eğilimleri ve bir bağlantı izleyicisinin başarısız olan denetim yüzdesini görüntülemek için:
1. Araştırmak istediğiniz bağlantı izleyicisini seçin.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Test grubuna göre gösterilen bir bağlantı izleyicisinin ölçümlerini gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Aşağıdaki bölümler görüntülenir  
    1. Essentials-seçili bağlantı Izleyicisinin kaynağa özgü özellikleri 
    1. Özetleme 
        1. Bağlantı izleyicisinde tüm testler için RTT ve başarısız denetim yüzdesi için toplu eğilim çizgileri. Ayrıntıları görüntülemek için belirli bir zaman ayarlayabilirsiniz.
        1. Başarısız denetimlerin RTT veya yüzdesine göre test grupları, kaynaklar ve hedefler genelinde en iyi 5. 
    1. Test grupları, kaynaklar, hedefler ve test yapılandırmalarına yönelik sekmeler-bağlantı Izleyicisinde test gruplarını, kaynakları veya hedefleri listeler. Testler başarısız oldu, toplam RTT ve denetimler başarısız oldu% values.  Ayrıca, verileri görüntülemek için geri dönebilirsiniz. 
    1. Sorunlar-bağlantı Izleyicisinde her bir test için atlama düzeyi sorunları. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Test grubu Bölüm 2 tarafından gösterilen bir bağlantı izleyicisinin ölçümlerini gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. Şunları yapabilirsiniz
    * Bağlantı Izleyicisinde tüm testleri görüntülemek için tüm testleri görüntüle ' ye tıklayın.
    * Her birine özgü ayrıntıları görüntülemek için tüm test gruplarını, test yapılandırmasını, kaynakları ve hedefleri görüntüle ' ye tıklayın. 
    * Varlıktaki tüm testleri görüntülemek için bir test grubu, test yapılandırması, kaynak veya hedef seçin.

1. Tüm testleri görüntüle görünümünden şunları yapabilirsiniz:
    * Testler ' i seçin ve Karşılaştır ' a tıklayın.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="2 test karşılaştırmasını gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * VNET gibi bileşik kaynakları, alt ağları alt kaynaklarına genişletmek için küme kullanın
    * Topoloji ' ye tıklayarak herhangi bir test için topolojiyi görüntüleyin.

RTT içindeki eğilimleri ve bir test grubu için başarısız denetimlerin yüzdesini görüntülemek için:
1. Araştırmak istediğiniz test grubunu seçin. 
1. Bağlantı izleyicisine benzer, test grupları, kaynaklar, hedefler ve test yapılandırmalarına yönelik temel bilgiler, Özet, tablo gibi görüntülenir. Bir bağlantı İzleyicisi için yaptığınız gibi gezinin

RTT içindeki eğilimleri ve test için başarısız denetimlerin yüzdesini görüntülemek için:
1. Araştırmak istediğiniz testi seçin. Başarısız olan denetimler için ağ topolojisini ve uçtan uca eğilim grafiklerini ve gidiş dönüş süresini görürsünüz. Tanımlanan sorunları görmek için, topolojide, yoldaki herhangi bir atlama seçin. (Bu atlamalar Azure kaynaklarıdır.) Bu işlevsellik şu anda şirket içi ağlarda kullanılamıyor

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Testin topoloji görünümünü gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics 'de günlük sorguları

İzleme verilerinizin özel görünümlerini oluşturmak için Log Analytics kullanın. Kullanıcı arabiriminin gösterdiği tüm veriler Log Analytics. Depodaki verileri etkileşimli olarak analiz edebilirsiniz. Aracı Durumu veya Log Analytics tabanlı diğer çözümlerdeki verilerin ilişkilendirilmesi. Verileri Excel 'e aktarın veya Power BI veya paylaşılabilir bir bağlantı oluşturun.

#### <a name="metrics-in-azure-monitor"></a>Azure İzleyicisi'nde ölçümler

Bağlantı Izleyicisi deneyiminden önce oluşturulan bağlantı izleyicilerinde, dört ölçüm kullanılabilir:% araştırmaları başarısız, AverageRoundtripMs, ChecksFailedPercent (Önizleme) ve Roundüçlü MS (Önizleme). Bağlantı Izleyicisi deneyiminde oluşturulan bağlantı izleyicilerinde, veriler yalnızca ile etiketlenmiş ölçümler için kullanılabilir *(Önizleme)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Bağlantı Izleyicisinde ölçümleri gösteren ekran görüntüsü" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Ölçümleri kullandığınızda, kaynak türünü Microsoft. Network/networkWatchers/Connectionmonitörleri olarak ayarlayın

| Metric | Görünen ad | Birim | Toplama türü | Açıklama | Boyutlar |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (klasik) | % Yoklama başarısız oldu (klasik) | Yüzde | Ortalama | Bağlantı İzleme Araştırmaları yüzdesi başarısız oldu. | Boyut yok |
| AverageRoundtripMs (klasik) | Ort. gidiş dönüş süresi (MS) (klasik) | Mayacak | Ortalama | Kaynak ve hedef arasında gönderilen bağlantı izleme araştırmaları için Ortalama ağ RTT. |             Boyut yok |
| ChecksFailedPercent | % Denetim başarısız oldu | Yüzde | Ortalama | Bir test için başarısız denetimlerin yüzdesi. | Connectionmonitorresourceıd <br>SourceAddress <br>Kaynak <br>Sourceresourceıd <br>KaynakTürü <br>Protokol <br>Hedef adres <br>Hedef adı <br>Hedef RESOURCEID <br>Hedef türü <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Bölge |
| Roundroundtimems | Gidiş dönüş süresi (MS) | Mayacak | Ortalama | Kaynak ve hedef arasında gönderilen denetimler için RTT. Bu değer, ortalama değildir. | Connectionmonitorresourceıd <br>SourceAddress <br>Kaynak <br>Sourceresourceıd <br>KaynakTürü <br>Protokol <br>Hedef adres <br>Hedef adı <br>Hedef RESOURCEID <br>Hedef türü <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Bölge |
| TestResult | Test sonucu | Count | Ortalama | Bağlantı İzleyicisi test sonucu | SourceAddress <br>Kaynak <br>Sourceresourceıd <br>KaynakTürü <br>Protokol <br>Hedef adres <br>Hedef adı <br>Hedef RESOURCEID <br>Hedef türü <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>Hedef IP <br>SourceSubnet <br>Hedef alt ağ |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Bağlantı Izleyicisi için ölçüm tabanlı uyarılar

Aşağıdaki yöntemleri kullanarak bağlantı izleyicilerinde ölçüm uyarıları oluşturabilirsiniz 

1. Bağlantı Izleyicisi 'nden, [Azure Portal kullanarak](connection-monitor-preview-create-using-portal.md#) Bağlantı İzleyicisi oluşturma sırasında 
1. Bağlantı Izleyicisi 'nden, panoda "uyarıları Yapılandır" ı kullanma 
1. Azure Izleyici 'den-Azure Izleyici 'de uyarı oluşturmak Için: 
    1. Bağlantı Izleyicisi 'nde oluşturduğunuz Bağlantı İzleyicisi kaynağını seçin.
    1. **Ölçümün** Bağlantı İzleyicisi için sinyal türü olarak görüntülendiğinden emin olun.
    1. **Koşul** adı bölümünde, **sinyal adı** Için, **checksfailedpercent (Önizleme)** veya **roundüçlü MS (Önizleme)** öğesini seçin.
    1. **Sinyal türü** için **ölçümler**' i seçin. Örneğin, **Checksfailedpercent (Önizleme)** öğesini seçin.
    1. Ölçümün tüm boyutları listelenir. Boyut adı ve boyut değerini seçin. Örneğin, **kaynak adresi** ' ni seçin ve ardından bağlantı izleyicinizdeki herhangi BIR kaynağın IP adresini girin.
    1. **Uyarı mantığı**' nda aşağıdaki ayrıntıları girin:
        * **Koşul türü**: **statik**.
        * **Koşul** ve **eşik**.
        * **Toplama ayrıntı düzeyi ve değerlendirme sıklığı**: Bağlantı İzleyicisi her dakikada verileri güncelleştirir.
    1. **Eylemler**' de eylem grubunuzu seçin.
    1. Uyarı ayrıntılarını belirtin.
    1. Uyarı kuralını oluşturun.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Azure Izleyici 'de kural oluştur alanını gösteren ekran görüntüsü. Kaynak adresi ve kaynak uç noktası adı vurgulanır" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Ağınızdaki sorunları tanılayın

Bağlantı Izleyicisi, bağlantı izleyicinizdeki ve ağınızdaki sorunları tanılamanıza yardımcı olur. Karma ağınızdaki sorunlar, daha önce yüklediğiniz Log Analytics aracıları tarafından algılanır. Azure 'daki sorunlar ağ Izleyicisi uzantısı tarafından algılanır. 

Ağ topolojisinde Azure ağı 'ndaki sorunları görüntüleyebilirsiniz.

Kaynakları şirket içi VM 'Ler olan ağlarda aşağıdaki sorunlar tespit edilebilir:

* İstek zaman aşımına uğradı.
* Uç nokta DNS tarafından çözümlenmedi-geçici veya kalıcı. URL geçersiz.
* Hiçbir ana bilgisayar bulunamadı.
* Kaynak, hedefle bağlantı kuramıyor. Hedefe ıCMP üzerinden ulaşılamıyor.
* Sertifikayla ilgili sorunlar: 
    * Aracının kimliğini doğrulamak için istemci sertifikası gereklidir. 
    * Sertifika konum değiştirme listesine erişilemiyor. 
    * Uç noktanın ana bilgisayar adı, sertifikanın konu veya konu diğer adı ile eşleşmiyor. 
    * Kaynağın yerel bilgisayar güvenilen sertifika yetkilileri deposunda kök sertifika yok. 
    * SSL sertifikasının zaman aşımına uğradı, geçersiz, iptal edildi veya uyumsuz.

Kaynakları Azure VM 'Leri olan ağlarda aşağıdaki sorunlar tespit edilebilir:

* Aracı sorunları:
    * Aracı durdu.
    * DNS çözümlemesi başarısız oldu.
    * Hedef bağlantı noktasını dinleyen uygulama veya dinleyici yok.
    * Yuva açılamadı.
* VM durumu sorunları: 
    * Başlatılıyor
    * Durduruluyor
    * Durduruldu
    * Serbest bırakılıyor
    * Serbest bırakıldı
    * Yeniden başlatılıyor
    * Ayrılmadı
* ARP tablosu girdisi eksik.
* Yerel güvenlik duvarı sorunları veya NSG kuralları nedeniyle trafik engellendi.
* Sanal ağ geçidi sorunları: 
    * Eksik yollar.
    * İki ağ geçidi arasındaki tünelin bağlantısı kesildi veya yok.
    * İkinci ağ geçidi, tünel tarafından bulunamadı.
    * Eşleme bilgisi bulunamadı.
> [!NOTE]
> 2 bağlantılı ağ geçidi varsa ve bunlardan biri kaynak uç noktası ile aynı bölgede değilse, CM bunu topoloji görünümü için ' hiçbir yol öğrenmiş ' olarak tanımlar. Bağlantı etkilenmez. Bu bilinen bir sorundur ve onarımla devam etmektedir. 
* Microsoft Edge 'de yol eksikti.
* Sistem yolları veya UDR nedeniyle trafik durduruldu.
* Ağ Geçidi bağlantısında BGP etkin değil.
* DIP araştırması, yük dengeleyicide çalışıyor.

## <a name="next-steps"></a>Sonraki Adımlar
    
   * [Azure Portal kullanarak bağlantı İzleyicisi oluşturmayı](./connection-monitor-create-using-portal.md) öğrenin  
   * [ARMClient kullanarak bağlantı İzleyicisi oluşturmayı](./connection-monitor-create-using-template.md) öğrenin