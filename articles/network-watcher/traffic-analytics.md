---
title: Azure Trafik Analizi | Microsoft Docs
description: Trafik Analizi ile Azure ağ güvenlik grubu akış günlüklerini çözümlemeyi öğrenin.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: kumud
ms.reviewer: vinigam
ms.openlocfilehash: 91fb4551f4651f44a1f7358951c5d4cc0ff70644
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907148"
---
# <a name="traffic-analytics"></a>Trafik Analizi

Trafik Analizi, bulut ağlarında Kullanıcı ve uygulama etkinliğine görünürlük sağlayan bulut tabanlı bir çözümdür. Trafik Analizi, Azure bulutunuzda trafik akışına ilişkin Öngörüler sağlamak için ağ Izleyicisi ağ güvenlik grubu (NSG) akış günlüklerini analiz eder. Trafik Analizi ile şunları yapabilirsiniz:

- Azure abonelikleriniz genelinde ağ etkinliğini görselleştirin ve etkin noktaları belirlersiniz.
- Açık bağlantı noktaları, internet erişimi yapılmaya çalışan uygulamalar ve standart dışı ağlara bağlanan sanal makineler (VM) gibi bilgilerle ağınızı güvenli hale getirin ve güvenliğini sağlayın.
- Performans ve kapasite için ağ dağıtımınızı iyileştirmek üzere Azure bölgeleri ve İnternet genelinde trafik akışı düzenlerini anlayın.
- Ağ yapılandırması hataları ağınızdaki başarısız bağlantıların başında.

> [!NOTE]
> Trafik Analizi artık NSG akış günlükleri verilerinin 10 dakikadan daha yüksek bir sıklıkta toplanmasını destekliyor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Neden Trafik Analizi?

Güvenliği ihlal edilmemiş güvenlik, uyumluluk ve performans için kendi ağınızı izlemek, yönetmek ve bilmek önemlidir. Kendi ortamınızı bilmeniz, korumak ve iyileştirmek için önemli öneme sahip olabilir. Genellikle ağın, bağlandığı, hangi bağlantı noktalarının internet 'e açık olduğu, ağ davranışı beklenen, düzensiz ağ davranışı ve trafikte olan ani RID 'lerin geçerli durumunu bilmeniz gerekir.

Bulut ağları, ağ arabirimine giren veya çıkış yaparken IP ağ trafiği toplama özelliği sağlayan, Netflow veya eşdeğer protokol özellikli yönlendiriciler ve anahtarlar içeren şirket içi kurumsal ağlardan farklıdır. Trafik akışı verilerini çözümleyerek, ağ trafiği akışı ve birimi analizini oluşturabilirsiniz.

Azure sanal ağları, tek tek ağ arabirimleri, VM 'Ler veya alt ağlar ile ilişkili bir ağ güvenlik grubu üzerinden giriş ve çıkış IP trafiği hakkında bilgi sağlayan NSG akış günlüklerine sahiptir. Ham NSG akış günlüklerini analiz ederek ve güvenlik, topoloji ve Coğrafya bilgilerini ekleyerek, trafik analizi ortamınızda trafik akışı hakkında öngörüler sağlar. Trafik Analizi, en çok iletişim kuran ana bilgisayar, çoğu iletişim uygulama protokolü, en çok kullanılan konak çiftleri, izin verilen/engellenen trafik, gelen/giden trafik, açık internet bağlantı noktaları, en çok engelleme kuralları, trafik gibi bilgiler sağlar Azure veri merkezi, sanal ağ, alt ağ veya standart dışı ağlar başına dağıtım.

## <a name="key-components"></a>Başlıca bileşenler

- **Ağ güvenlik grubu (NSG)** : bir Azure sanal ağına bağlı kaynaklara yönelik ağ trafiğine izin veren veya reddeden güvenlik kurallarının bir listesini içerir. Ağ güvenlik grupları (NSG’ler), alt ağlarla, ayrı ayrı VM’lerle (klasik) veya VM’lere bağlı ağ arabirimleri ile ilişkilendirilebilir (Resource Manager). Daha fazla bilgi için bkz. [ağ güvenlik grubuna genel bakış](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Ağ güvenlik grubu (NSG) akış günlükleri**: bir ağ güvenlik grubu üzerinden giriş ve çıkış IP trafiği hakkındaki bilgileri görüntülemenize izin verir. NSG akış günlükleri, JSON biçiminde yazılır ve bir kural temelinde giden ve gelen akışları gösterir, akış için geçerli olan NIC, Flow (kaynak/hedef IP adresi, kaynak/hedef bağlantı noktası ve protokol) ile ilgili beş demet bilgileri ve trafiğe izin veriliyorsa veya reddedildi. NSG akış günlükleri hakkında daha fazla bilgi için bkz. [NSG akış günlükleri](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: izleme verilerini toplayan ve verileri merkezi bir depoda depolayan Azure hizmeti. Bu veriler olayları, performans verilerini veya Azure API 'SI aracılığıyla sunulan özel verileri içerebilir. Toplanan veriler uyarı, analiz ve dışarı aktarma için kullanılabilir hale gelir. Ağ Performansı İzleyicisi ve trafik analizi gibi izleme uygulamaları, bir temel olarak Azure Izleyici günlükleri kullanılarak oluşturulmuştur. Daha fazla bilgi için bkz. [Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics çalışma alanı**: Azure hesabıyla ilgili verilerin depolandığı Azure izleyici günlüklerinin bir örneği. Log Analytics çalışma alanları hakkında daha fazla bilgi için bkz. [Log Analytics çalışma alanı oluşturma](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Ağ İzleyicisi**: Azure 'da bir ağ senaryosu düzeyinde koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Ağ Izleyicisi ile NSG akış günlüklerini açıp kapatabilirsiniz. Daha fazla bilgi için bkz. [Ağ İzleyicisi](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Trafik analizinin çalışması

Trafik Analizi, ham NSG akış günlüklerini inceler ve aynı kaynak IP adresi, hedef IP adresi, hedef bağlantı noktası ve protokol arasındaki ortak akışları toplayarak azaltılan günlükleri yakalar. Örneğin, ana bilgisayar 1 (IP adresi: 10.10.10.10), ana bilgisayar 2 ' ye (IP adresi: 10.10.20.10), bağlantı noktasını (örneğin, 80) ve Protokolü (örneğin, http) kullanarak 1 saat boyunca 100 kez iletişim kurarak. Azaltılan günlükte tek bir giriş bulunur. ana bilgisayar 1 & ana bilgisayar 2 bağlantı noktası *80* ve protokol 100 *http*kullanılarak 1 saat boyunca 100 kez iletidedir. Azaltılan Günlükler coğrafya, güvenlik ve topoloji bilgileriyle geliştirilmiştir ve sonra bir Log Analytics çalışma alanında depolanır. Aşağıdaki resimde veri akışı gösterilmektedir:

![NSG akış günlükleri işleme için veri akışı](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Desteklenen bölgeler: NSG 

NSG 'ler için trafik analizini aşağıdaki desteklenen bölgelerden herhangi birinde kullanabilirsiniz:

* Kanada Orta
* Batı Orta ABD
* Doğu ABD
* Doğu ABD 2
* Orta Kuzey ABD
* Orta Güney ABD
* Orta ABD
* Batı ABD
* Batı ABD 2
* Fransa Orta
* Batı Avrupa
* Kuzey Avrupa
* Güney Brezilya
* Birleşik Krallık Batı
* Birleşik Krallık Güney
* Avustralya Doğu
* Avustralya Güneydoğu
* Doğu Asya
* Güneydoğu Asya
* Kore Orta
* Orta Hindistan
* Güney Hindistan
* Japonya Doğu 
* Japonya Batı
* ABD Devleti Virginia
* Çin Doğu 2

## <a name="supported-regions-log-analytics-workspaces"></a>Desteklenen bölgeler: Log Analytics çalışma alanları

Log Analytics çalışma alanı aşağıdaki bölgelerde bulunmalıdır:
* Kanada Orta
* Batı Orta ABD
* Doğu ABD
* Doğu ABD 2
* Orta Kuzey ABD
* Orta Güney ABD
* Orta ABD
* Batı ABD
* Batı ABD 2
* Orta ABD
* Fransa Orta
* Batı Avrupa
* Kuzey Avrupa
* Güney Brezilya
* Birleşik Krallık Batı
* Birleşik Krallık Güney
* Avustralya Doğu
* Avustralya Güneydoğu
* Doğu Asya
* Güneydoğu Asya
* Kore Orta
* Orta Hindistan
* Japonya Doğu
* ABD Devleti Virginia
* Çin Doğu 2

## <a name="prerequisites"></a>Önkoşullar

### <a name="user-access-requirements"></a>Kullanıcı erişimi gereksinimleri

Hesabınız aşağıdaki Azure [yerleşik rollerinin](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)birine üye olmalıdır:

|Dağıtım modeli   | Rol                   |
|---------          |---------               |
|Resource Manager   | Sahip                  |
|                   | Katılımcı            |
|                   | Okuyucu                 |
|                   | Ağ katılımcısı    |

Hesabınız yerleşik rollerden birine atanmamışsa, abonelik düzeyinde aşağıdaki eylemler atanmış olan [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) atanmalıdır. şu şekilde hesaba atanmalıdır:

- "Microsoft. Network/Applicationgateway/Read"
- "Microsoft. Network/Connections/Read"
- "Microsoft. Network/loadBalancers/Read"
- "Microsoft. Network/Localnetworkgateway/Read"
- "Microsoft. Network/NetworkInterfaces/Read"
- "Microsoft. Network/networkSecurityGroups/Read"
- "Microsoft. Network/publicIPAddresses/Read"
- "Microsoft. Network/routeTables/Read"
- "Microsoft. Network/Virtualnetworkgateway/Read"
- "Microsoft. Network/virtualNetworks/Read"

Kullanıcı erişim izinlerini denetleme hakkında bilgi için bkz. [trafik analizi hakkında SSS](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Ağ İzleyicisini etkinleştirme

Trafiği çözümlemek için, var olan bir ağ izleyicisine sahip olmanız ya da NSG 'ler bulunan her bölgede, trafiğini çözümlemek istediğiniz her bölgede [bir ağ izleyicisi etkinleştirmeniz](network-watcher-create.md) gerekir. Trafik Analizi, [desteklenen bölgelerde](#supported-regions-nsg)barındırılan NSG 'ler için etkinleştirilebilir.

### <a name="select-a-network-security-group"></a>Bir ağ güvenlik grubu seçin

NSG akış günlüğünü etkinleştirmeden önce, akışları günlüğe kaydetmek için bir ağ güvenlik grubunuz olması gerekir. Ağ güvenlik grubunuz yoksa, oluşturmak için [ağ güvenlik grubu oluşturma](../virtual-network/manage-network-security-group.md#create-a-network-security-group) bölümüne bakın.

Azure portal sol tarafında **izleyici**' yi ve ardından **Ağ İzleyicisi**' ni seçin ve **NSG akış günlükleri**' ni seçin. Aşağıdaki resimde gösterildiği gibi NSG akış günlüğünü etkinleştirmek istediğiniz ağ güvenlik grubunu seçin:

![NSG akış günlüğünün etkinleştirilmesini gerektiren NSG 'ler seçimi](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

[Desteklenen bölgeler](#supported-regions-nsg)dışında herhangi bir bölgede barındırılan bir NSG için trafik analizini etkinleştirmeye çalışırsanız, "bulunamadı" hatası alırsınız.

## <a name="enable-flow-log-settings"></a>Akış günlüğü ayarlarını etkinleştir

Akış günlüğü ayarlarını etkinleştirmeden önce aşağıdaki görevleri gerçekleştirmeniz gerekir:

Aboneliğiniz için henüz kayıtlı değilse, Azure Insights sağlayıcısını kaydedin:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

NSG akış günlüklerini depolamak için henüz bir Azure depolama hesabınız yoksa bir depolama hesabı oluşturmanız gerekir. Aşağıdaki komutla bir depolama hesabı oluşturabilirsiniz. Komutu çalıştırmadan önce, `<replace-with-your-unique-storage-account-name>` tüm Azure konumlarında benzersiz olan bir adla değiştirin, yalnızca rakamlar ve küçük harfler kullanılarak 3-24 karakter uzunluğunda olacak şekilde değiştirin. Gerekirse, kaynak grubu adını da değiştirebilirsiniz.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Resimde gösterildiği gibi aşağıdaki seçenekleri belirleyin:

1. **Durum** için *Açık* seçeneğini belirleyin
2. **Akış günlükleri sürümü**için *sürüm 2* ' yi seçin. Sürüm 2, akış oturumu istatistikleri içerir (bayt ve paket)
3. Akış günlüklerini depolamak için mevcut bir depolama hesabı seçin. Verileri süresiz olarak depolamak istiyorsanız, değeri *0*olarak ayarlayın. Depolama hesabı için Azure depolama ücretine tabi olursunuz. Depolama alanınız "Data Lake Storage 2. hiyerarşik ad alanı etkin değil" özelliğinin true olarak ayarlandığından emin olun. Ayrıca, NSG akış Günlükleri güvenlik duvarı olan bir depolama hesabında depolanamaz. 
4. **Bekletmeyi** , verilerini depolamak istediğiniz gün sayısına ayarlayın.
> [!IMPORTANT]
> Şu anda ağ Izleyicisi için ağ [güvenlik grubu (NSG) akış günlüklerinin](network-watcher-nsg-flow-logging-overview.md) , bekletme ilkesi ayarlarına göre blob depolamadan otomatik olarak silinmediği bir sorun vardır. Sıfır olmayan bir bekletme ilkeniz varsa, herhangi bir ücret ödemeden kaçınmak için saklama süresini aşan depolama bloblarını düzenli aralıklarla silmenizi öneririz. NSG akış günlüğü depolama blogunu silme hakkında daha fazla bilgi için bkz. [NSG akış günlüğü depolama Bloblarını silme](network-watcher-delete-nsg-flow-log-blobs.md).

5. **Trafik Analizi durumu**Için *Açık '* ı seçin.
6. İşleme aralığını seçin. Seçim yapmanız temelinde, akış günlüklerinizi depolama hesabından toplanacak ve Trafik Analizi tarafından işlenir. Her 1 saatte bir veya 10 dakikada bir işleme aralığı seçebilirsiniz. 
7. Var olan bir Log Analytics (OMS) çalışma alanı seçin veya yeni bir **çalışma alanı** oluştur ' u seçerek yeni bir tane oluşturun. Log Analytics çalışma alanı, analiz oluşturmak için kullanılan toplanmış ve dizine alınmış verileri depolamak için Trafik Analizi tarafından kullanılır. Mevcut bir çalışma alanını seçerseniz, [desteklenen bölgelerden](#supported-regions-log-analytics-workspaces) birinde bulunmalıdır ve yeni sorgu diline yükseltilir. Mevcut bir çalışma alanını yükseltmek veya desteklenen bir bölgede çalışma alanınız yoksa yeni bir tane oluşturun. Sorgu dilleri hakkında daha fazla bilgi için bkz. [Azure Log Analytics yükseltme yeni günlük araması](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Trafik Analizi çözümünü barındıran Log Analytics çalışma alanının ve NSG 'lerin aynı bölgede olması gerekmez. Örneğin, Batı Avrupa bölgesindeki bir çalışma alanında trafik analizlerinin olması, Doğu ABD ve Batı ABD NSG 'lerinizin olması olabilir. Aynı çalışma alanında birden çok NSG yapılandırılabilir.

8. **Kaydet**’i seçin.

    ![Depolama hesabı, Log Analytics çalışma alanı ve Trafik Analizi etkinleştirme seçimi](./media/traffic-analytics/ta-customprocessinginterval.png)

İçin trafik analizini etkinleştirmek istediğiniz diğer NSG 'ler için önceki adımları tekrarlayın. Akış günlüklerinden veriler çalışma alanına gönderilir, böylece ülkenizde bulunan yerel yasaları ve yönetmelikler, çalışma alanının bulunduğu bölgede veri depolamaya izin verdiğinden emin olun. Farklı NSG 'ler için farklı işleme aralıkları ayarladıysanız, veriler farklı aralıklarla toplanır. Örneğin: kritik VNET 'ler için 10 dakikalık işleme aralığını ve kritik olmayan VNET 'ler için 1 saat etkinleştirmeyi seçebilirsiniz.

Ayrıca, Azure PowerShell içindeki [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell cmdlet 'ini kullanarak trafik analizini yapılandırabilirsiniz. Yüklü sürümünüzü bulmak için `Get-Module -ListAvailable Az` çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Trafik analizini görüntüleme

Portalın sol tarafında **tüm hizmetler**' i seçin ve ardından **filtre** kutusuna *izleyici* yazın. Arama sonuçlarında **izleyici** göründüğünde seçin. Trafik analizini ve yeteneklerini keşfetmeye başlamak için **Ağ İzleyicisi**' ni ve ardından **Trafik Analizi**.

![Trafik Analizi panosuna erişme](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Panonun, hiçbir rapor oluşturmadan önce anlamlı Öngörüler türetmesine yetecek kadar veri toplaması Trafik Analizi gerektiğinden, pano ilk kez görünmesi 30 dakika sürebilir.

## <a name="usage-scenarios"></a>Kullanım senaryoları

Trafik Analizi tam olarak yapılandırıldıktan sonra kazanmak isteyebileceğiniz öngörülerden bazıları şunlardır:

### <a name="find-traffic-hotspots"></a>Trafik etkin noktalarını bulma

**Aramak**

- Hangi ana bilgisayarlar, alt ağlar ve sanal ağlar en fazla trafiği gönderiyor veya alıyor, en fazla kötü amaçlı trafiğe geçiş yapma ve önemli akışları engelleme?
    - Konak, alt ağ ve sanal ağ için karşılaştırılma grafiğini denetleyin. En fazla trafiği hangi ana bilgisayarların, alt ağların ve sanal ağların gönderdiğini veya aldığını anlamak, en çok trafiği işleyen konakları ve trafik dağıtımının düzgün şekilde yapılıp yapılmadığını belirlemenize yardımcı olabilir.
    - Trafik hacminin bir ana bilgisayar için uygun olup olmadığını değerlendirebilirsiniz. Trafik normal davranış hacmidir veya daha fazla araştırma yapmak ister misiniz?
- Ne kadar gelen/giden trafik var?
    -   Konağın giden trafiği alından daha fazla gelen trafik alması bekleniyor mi yoksa tersi de geçerlidir mı?
- Engellenen trafik istatistikleri.
    - Neden bir ana bilgisayar, büyük bir zararsız trafik birimini engelliyor? Bu davranış daha fazla araştırma ve büyük olasılıkla yapılandırma iyileştirmesi gerektirir
- Kötü amaçlı izin verilen/engellenen trafik istatistikleri
  - Bir ana bilgisayar neden kötü amaçlı trafik alıyor ve kötü amaçlı kaynaklardan akışa neden izin veriliyor? Bu davranış daha fazla araştırma ve büyük olasılıkla yapılandırma iyileştirmesi gerektirir.

    Aşağıdaki resimde gösterildiği gibi, **ana bilgisayar**altında **Tümünü göster**' i seçin:

    ![En fazla trafik ayrıntılarının bulunduğu bir ana bilgisayar gösterilen Pano](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Aşağıdaki resimde, bir konak için ilk beş konuşana bilgisayarı ve Flow ile ilgili ayrıntılar (izin verilen – gelen/giden ve reddedilen-gelen/giden akışlar) için zaman eğilimi gösterilmektedir:

    ![En çok beş konuşuyor ana bilgisayar eğilimi](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Aramak**

- En çok söyme ana bilgisayar çiftleri nelerdir?
    - Ön uç veya arka uç iletişimi veya arka uç internet trafiği gibi düzensiz davranış gibi beklenen davranış.
- İzin verilen/engellenen trafik istatistikleri
    - Bir konağın önemli miktarda trafik için izin verme veya engelleme
- En sık kullanılan uygulama protokolü, en çok kullanılan konak çiftleri arasında:
    - Bu uygulamalar bu ağda izin veriyor mu?
    - Uygulamalar düzgün şekilde yapılandırılmış mı? İletişim için uygun protokolü kullanıyor mu? Aşağıdaki resimde gösterildiği gibi **sık kullanılan konuşma**altındaki **Tümünü** göster ' i seçin:

        ![En sık görülen konuşmayı görüntüleyen Pano](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Aşağıdaki resimde, bir konuşma çifti için izin verilen ve engellenen gelen ve giden akışlar gibi ilk beş görüşmenin ve Flow ile ilgili ayrıntıların zaman eğilimi gösterilmektedir:

    ![İlk beş geveze konuşma ayrıntıları ve eğilimi](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Aramak**

- Ortamınızda en çok kullanılan uygulama protokolü ve ana bilgisayar çiftleri en çok uygulama protokolünü kullanıyor mu?
    - Bu uygulamalar bu ağda izin veriyor mu?
    - Uygulamalar düzgün şekilde yapılandırılmış mı? İletişim için uygun protokolü kullanıyor mu? Beklenen davranış, 80 ve 443 gibi yaygın bağlantı noktalarıdır. Standart iletişim için, olağan dışı bağlantı noktaları görüntülenirse, bir yapılandırma değişikliği gerekebilir. Aşağıdaki resimde, **uygulama bağlantı noktası**altında **Tümünü göster** ' i seçin:

        ![Üst düzey uygulama protokollerini gösteren Pano](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Aşağıdaki resimlerde, bir L7 protokolü için ilk beş L7 protokolünün ve Flow ile ilgili ayrıntıların (örneğin, izin verilen ve reddedilen akışlar) zaman eğilimi gösterilmektedir:

    ![İlk beş katman 7 protokol ayrıntıları ve eğilimi](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Günlük aramasında uygulama protokolü için akış ayrıntıları](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Aramak**

- Ortamınızdaki bir VPN ağ geçidinin kapasite kullanımı eğilimleri.
    - Her VPN SKU 'SU belirli bir bant genişliği miktarına izin verir. VPN ağ geçitleri az kullanılıyor mu?
    - Ağ geçitleriniz kapasiteye ulaşıyor mu? Sonraki daha yüksek SKU 'ya yükseltmeniz mı gerekiyor?
- VPN ağ geçidi üzerinden hangi bağlantı noktası üzerinden en fazla söyleşen ana bilgisayar var?
    - Bu model normal midir? Aşağıdaki resimde gösterildiği gibi, **VPN Gateway**altında **Tümünü göster** ' i seçin:

        ![Büyük küçük harfe sahip Pano üst etkin VPN bağlantıları](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Aşağıdaki resimde bir Azure VPN Gateway kapasite kullanımı ve akışla ilgili ayrıntılar (izin verilen akışlar ve bağlantı noktaları gibi) için zaman eğilimi gösterilmektedir:

    ![VPN Gateway kullanım eğilimi ve akış ayrıntıları](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Coğrafya ile trafik dağıtımını görselleştirin

**Aramak**

- Veri merkezi 'ne yönelik en popüler trafik kaynakları, veri merkezi ile söyleşme ve uygulama protokollerini en iyi şekilde söyleşen en popüler ağlarla trafik dağıtımı.
  - Bir veri merkezinde daha fazla yük gözlemlerseniz, verimli trafik dağıtımı için plan yapabilirsiniz.
  - Veri merkezinde standart dışı ağlarda söyleşmek gerekirse, bunları engellemek için NSG kurallarını düzeltin.

    Aşağıdaki resimde gösterildiği gibi **ortamınızın**altındaki **Haritayı görüntüle** ' yi seçin:

    ![Büyük/küçük harf trafiği dağıtımını gösteren Pano](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Coğrafi eşleme, veri merkezleri (dağıtılan/olmayan dağıtım/etkin/devre dışı/Trafik Analizi etkin/Trafik Analizi etkin değil) ve etkin olan ve kötü amaçlı trafiğe katkıda bulunan ülkeler/bölgeler gibi parametrelerin seçimine yönelik üst şeridi gösterir dağıtmak

    ![Coğrafi harita görünümü göster etkin dağıtım](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Coğrafi harita, ülke/bölgelerdeki bir veri merkezine trafik dağılımını ve mavi (benign trafiği) ve kırmızı (kötü amaçlı trafik) renkli satırları ile iletişim kuran kıtları gösterir:

    ![Coğrafi harita görünümü ülke/bölgelere ve kıtalara trafik dağıtımını gösteren büyük/küçük harf göster](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Günlük aramasında trafik dağıtımı için akış ayrıntıları](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Sanal ağlar tarafından trafik dağıtımını görselleştirin

**Aramak**

- Sanal ağ başına trafik dağıtımı, topoloji, sanal ağa giden trafik, en iyi standart dışı ağlar, sanal ağa söyleşme ve en iyi uygulama protokollerini söyledi.
  - Hangi sanal ağın hangi sanal ağa dönüştürüleşdiğinin bilinmesi. Konuşma beklenmiyorsa, düzeltilebilir.
  - Standart dışı ağlarda bir sanal ağla iletişim varsa, standart dışı ağları engellemek için NSG kurallarını düzeltebilirsiniz.
 
    Aşağıdaki resimde gösterildiği gibi **ortamınız**altında **sanal ağları görüntüle** ' yi seçin:

    ![Sanal ağ dağıtımını gösteren Pano](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Sanal ağ topolojisi, sanal ağın (sanal ağ bağlantıları/etkin/etkin olmayan), dış bağlantıların, etkin akışların ve sanal ağın kötü amaçlı akışlarının gibi parametrelerin seçimine yönelik üst şeridi gösterir.
- Sanal ağ topolojisini abonelikler, çalışma alanları, kaynak grupları ve zaman aralığına göre filtreleyebilirsiniz. Akışı anlamanıza yardımcı olan ek filtreler şunlardır: akış türü (ıntervnet, ınvnet, vb.), akış yönü (gelen, giden), Flow durumu (Izin verilen, engellenen), sanal ağlar (hedeflenen ve bağlı), bağlantı türü (eşleme veya ağ geçidi-P2S ve S2S) ve NSG. Ayrıntılı incelemek istediğiniz sanal ağlara odaklanmak için bu filtreleri kullanın.
- Sanal ağ topolojisi, akışlar (Izin verilen/engellenen/gelen/giden/zararsız/kötü amaçlı), uygulama protokolü ve ağ güvenlik grupları gibi bir sanal ağa trafik dağılımını gösterir, örneğin:

    ![Trafik dağıtımı ve akış ayrıntılarını gösteren sanal ağ topolojisi](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Sanal ağ topolojisi Gösterim üst düzey ve daha fazla filtre](./media/traffic-analytics/virtual-network-filters.png)

    ![Günlük aramasında sanal ağ trafiği dağıtımı için akış ayrıntıları](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Aramak**

- Alt ağ, topoloji, alt ağa giden trafik kaynakları, en çok standart dışı ağlar alt ağa söyleşme ve uygulama protokollerini en iyi şekilde söyledi.
    - Hangi alt ağın hangi alt ağa dönüştürüleşdiğinin bilinmesi. Beklenmeyen konuşmalar görürseniz, yapılandırmanızı düzeltebilirsiniz.
    - Standart dışı ağlar bir alt ağ ile söylenebilir, standart dışı ağları engellemek için NSG kurallarını yapılandırarak bunu düzeltebilirsiniz.
- Alt ağlar topolojisi, etkin/etkin olmayan alt ağ, dış bağlantılar, etkin akışlar ve alt ağın kötü amaçlı akışları gibi parametrelerin seçimine yönelik üst şeridi gösterir.
- Alt ağ topolojisi, akış (Izin verilen/engellenen/gelen/giden/zararsız/kötü amaçlı), uygulama protokolü ve NSG 'ler gibi bir sanal ağa trafik dağılımını gösterir. Örneğin:

    ![Alt ağ topolojisi küçük harfe dağıtım trafik dağıtımı, akışlara göre bir sanal ağ alt ağı](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Aramak**

Uygulama ağ geçidi başına trafik dağıtımı & Load Balancer, topoloji, en üst trafik kaynakları, en çok standart dışı ağlar uygulama ağ geçidine & Load Balancer ve en üst düzey uygulama protokollerini söyleşme. 
    
 - Hangi alt ağın hangi uygulama ağ geçidine veya Load Balancer söylenediğinin bilinmesi. Beklenmedik konuşmaları gözlemlerseniz, yapılandırmanızı düzeltebilirsiniz.
 - Standart dışı ağlarda bir uygulama ağ geçidi veya Load Balancer söyleşiyor ise, standart dışı ağları engellemek için NSG kurallarını yapılandırarak bunu düzeltebilirsiniz. 

    ![alt ağ-topoloji-showp---to-to-to-Application-Gateway-subnet------------](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>İnternet 'ten trafik alan bağlantı noktalarını ve sanal makineleri görüntüleme

**Aramak**

- Internet üzerinden hangi açık bağlantı noktaları geçer?
  - Beklenmedik bağlantı noktaları açıksa, yapılandırmanızı düzeltebilirsiniz:

    ![İnternet 'e trafik alıp gönderen Pano, büyük/küçük harf noktalarını göster](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Azure hedef bağlantı noktaları ve konaklarının ayrıntıları](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Aramak**

Ortamınızda kötü amaçlı trafik var mı? Nereden kaynaklandığı? Nereye gidiyor?

![Günlük aramasında kötü amaçlı trafik akışları ayrıntısı](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>NSG/NSG kuralları isabetlerindeki eğilimleri görselleştirin

**Aramak**

- Hangi NSG/NSG kuralları, akış dağıtımı ile karşılaştırılma grafiğinde en fazla isabetliye sahiptir?
- NSG/NSG kuralları başına en üst kaynak ve hedef konuşma çiftleri nelerdir?

    ![Büyük/küçük harf NSG isabet istatistiklerini göster](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Aşağıdaki resimlerde, bir ağ güvenlik grubu için NSG kurallarının ve kaynak-hedef akış ayrıntılarının isabetleri için zaman eğilimi gösterilmektedir:

  - Hangi NSG 'lerin ve NSG kurallarının kötü amaçlı akışlara geçiş olduğunu ve bulut ortamınıza erişen en iyi kötü amaçlı IP adreslerini hızla tespit edin
  - Hangi NSG/NSG kurallarının önemli ağ trafiğine izin vermesini/engellenmesini belirler
  - NSG veya NSG kurallarının ayrıntılı incelemesi için en üst filtreleri seçin

    ![NSG kuralı isabetleri ve üst NSG kuralları için büyük/küçük harfe eğilim süresi](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Günlük aramasında üst NSG kuralları İstatistik ayrıntıları](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

Sık sorulan sorulara yanıt almak için bkz. [trafik analizi hakkında SSS](traffic-analytics-faq.md).

## <a name="next-steps"></a>Sonraki adımlar

- Akış günlüklerinin nasıl etkinleştirileceği hakkında bilgi edinmek için bkz. [NSG akış günlüğünü etkinleştirme](network-watcher-nsg-flow-logging-portal.md).
- Trafik Analizi şema ve işleme ayrıntılarını anlamak için bkz. [Trafik Analizi şeması](traffic-analytics-schema.md).
