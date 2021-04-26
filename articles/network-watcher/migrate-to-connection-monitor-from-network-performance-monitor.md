---
title: Ağ Performansı İzleyicisi bağlantı Izleyicisi 'ne geçir
titleSuffix: Azure Network Watcher
description: Ağ Performansı İzleyicisi 'den bağlantı Izleyicisine geçiş yapmayı öğrenin.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366360"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Ağ Performansı İzleyicisi bağlantı Izleyicisi 'ne geçir

> [!IMPORTANT]
> 1 Temmuz 2021 ' den itibaren, mevcut bir çalışma alanına yeni testler ekleyemez veya Ağ Performansı İzleyicisi yeni bir çalışma alanı etkinleştiremeyeceksiniz. 1 Temmuz 2021 ' den önce oluşturulan testleri kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, testlerinizi Ağ Performansı İzleyicisi 'den Azure ağ Izleyicisi 'ndeki yeni bağlantı Izleyicisinden, 29 Şubat 2024 tarihinden önce geçirin.

Ağ Performansı İzleyicisi (NPM) ' den yeni, iyileştirilmiş bağlantı Izleyicisine, tek tıklamayla ve sıfır kapalı kalma süresine sahip testleri geçirebilirsiniz. Avantajlar hakkında daha fazla bilgi edinmek için bkz. [Bağlantı İzleyicisi](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Önemli noktaları göz önünde

Geçiş, aşağıdaki sonuçları üretmenize yardımcı olur:

* Şirket içi aracılar ve güvenlik duvarı ayarları olduğu gibi çalışır. Değişiklik gerekmiyor. Azure sanal makinelerinde yüklü Log Analytics aracıların [Ağ İzleyicisi uzantısıyla](../virtual-machines/extensions/network-watcher-windows.md)değiştirilmeleri gerekir.
* Mevcut testler, > test grubu > test biçimiyle bağlantı Izleyicisine eşlenir. **Düzenle**' yi seçerek yeni bağlantı izleyicisinin özelliklerini görüntüleyip değiştirebilir, değişiklikler yapmak için bir şablon indirebilir ve Azure Resource Manager aracılığıyla şablonu gönderebilirsiniz.
* Aracılar Log Analytics çalışma alanına ve ölçümlere veri gönderir.
* Veri izleme:
   * **Log Analytics verileri**: geçişten önce, veriler NPM 'Nin networkmonitoring tablosunda yapılandırıldığı çalışma alanında kalır. Geçişten sonra, veriler NetworkMonitoring tablosuna gider. aynı çalışma alanındaki NWConnectionMonitorTestResult tablosu ve NWConnectionMonitorPathResult tablosu. Test NPM 'de devre dışı bırakıldıktan sonra, veriler yalnızca NWConnectionMonitorTestResult tablosu ve NWConnectionMonitorPathResult tablosunda depolanır.
   * **Günlük tabanlı uyarılar, panolar ve tümleştirmeler**: yeni NWConnectionMonitorTestResult tablosu ve NWConnectionMonitorPathResult tablosuna göre sorguları el ile düzenlemeniz gerekir. Uyarıları ölçümlerde yeniden oluşturmak için, bkz. [Bağlantı İzleyicisi Ile ağ bağlantısı izleme](./connection-monitor-overview.md#metrics-in-azure-monitor).
* ExpressRoute Izleme için:
    * **Uçtan uca kayıp ve gecikme**: Bağlantı İzleyicisi bunu güçlendirilecektir ve kullanıcılar, hangi devrelerin ve eşlerin izleneceğini yapılandırmalarına gerek kalmaz NPM 'den daha kolay olacaktır. Yoldaki devre'ler otomatik olarak keşfedilir, veriler ölçümlerde kullanılabilir (NPM 'den daha hızlı). Topoloji da olduğu gibi çalışacaktır.
    * **Bant genişliği ölçümleri**: bant genişliği ile ilgili ölçümlerin başlatılması sayesinde, NPM 'nin Log Analytics tabanlı yaklaşımı ExpressRoute müşterileri için bant genişliği izlemede etkili değildir. Bu özellik artık bağlantı Izleyicisi 'nde kullanılamaz.
    
## <a name="prerequisites"></a>Önkoşullar

* Aboneliğinizde ve Log Analytics çalışma alanının bölgesinde ağ izleyicisinin etkinleştirildiğinden emin olun. Bu yapılmazsa, "geçiş yapmayı denemeden önce" bir hata görürsünüz, lütfen seçim aboneliğinde ağ izleyicisi uzantısını etkinleştirin ve LA çalışma alanının konumunu seçili yapın. "
* Azure VM 'nin, Log Analytics çalışma alanı bir uç nokta olarak kullanıldığı farklı bir bölgeye/aboneliğe ait olması durumunda bu abonelik ve bölge için ağ Izleyicisi 'nin etkinleştirildiğinden emin olun.   
* Log Analytics aracıları yüklü Azure sanal makineleri, ağ Izleyicisi uzantısıyla etkinleştirilmelidir.

## <a name="migrate-the-tests"></a>Testleri geçirme

Testleri Ağ Performansı İzleyicisi bağlantı Izleyicisine geçirmek için aşağıdakileri yapın:

1. Ağ Izleyicisi 'nde **Bağlantı İzleyicisi**' ni seçin ve ardından **NPM 'den testleri geçir** sekmesini seçin. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Ağ Performansı İzleyicisi testleri bağlantı Izleyicisine geçirme" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Aşağı açılan listelerde, aboneliğinizi ve çalışma alanınızı seçin ve ardından geçirmek istediğiniz NPM özelliğini seçin. 
1. Testleri geçirmek için **Içeri aktar** ' ı seçin.
* Eğer NPM çalışma alanında etkinleştirilmemişse, "geçerli NPM yapılandırması bulunamadı" hatasını bildiren bir hata görürsünüz. 
* Step2 ' de seçtiğiniz özellikte bir test yoksa, "seçili çalışma alanı yapılandırma yok" hatasını bildiren bir hata görürsünüz <feature> .
* Geçerli bir test yoksa, "seçili çalışma alanı geçerli sınamalar yok" hatasını belirten bir hata görürsünüz.
* Testleriniz artık etkin olmayan, ancak geçmişte etkin olan aracılar içeriyor olabilir. "Az sayıda test artık etkin olmayan aracılar içerdiğini belirten bir hata görürsünüz. Etkin olmayan aracıların listesi- {0} . Bu aracılar geçmişte çalışıyor olabilir, ancak kapanmakta veya daha fazla çalışmıyor. Aracıları etkinleştirin ve bağlantı Izleyicisi 'ne geçiş yapın. Etkin olmayan aracılar içermeyen testleri geçirmek için devam ' a tıklayın. "

Geçiş başladıktan sonra aşağıdaki değişiklikler gerçekleşir: 
* Yeni bir bağlantı İzleyicisi kaynağı oluşturulur.
   * Her bölge ve abonelik için bir bağlantı İzleyicisi oluşturulur. Şirket içi aracılarla testler için yeni bağlantı izleyici adı olarak biçimlendirilir `<workspaceName>_"workspace_region_name"` . Azure aracılarıyla testler için yeni bağlantı izleyici adı olarak biçimlendirilir `<workspaceName>_<Azure_region_name>` .
   * İzleme verileri artık NPM 'nin etkinleştirildiği Log Analytics çalışma alanında, NWConnectionMonitorTestResult tablosu ve NWConnectionMonitorPathResult tablosu adlı yeni tablolarda depolanır. 
   * Test adı, test grubu adı olarak ileri taşınır. Test açıklaması geçirilmez.
   * Kaynak ve hedef uç noktaları yeni test grubunda oluşturulur ve kullanılır. Şirket içi aracılar için uç noktalar olarak biçimlendirilir `<workspaceName>_<FQDN of on-premises machine>` . Aracı açıklaması geçirilmez.
   * Hedef bağlantı noktası ve yoklama aralığı, ve adlı bir test yapılandırmasına `TC_<protocol>_<port>` taşınır `TC_<protocol>_<port>_AppThresholds` . Protokol, bağlantı noktası değerlerine göre ayarlanır. ICMP için, test yapılandırması ve olarak adlandırılır `TC_<protocol>` `TC_<protocol>_AppThresholds` . Küme geçirilirse başarı eşikleri ve diğer isteğe bağlı özellikler, aksi takdirde boş bırakılır.
   * Geçiş testleri çalıştırmayan aracıları içeriyorsa, aracıları etkinleştirmeniz ve yeniden geçirmeniz gerekir.
* NPM devre dışı bırakılmazsa, geçirilen testler NetworkMonitoring tablosuna, NWConnectionMonitorTestResult Table ve NWConnectionMonitorPathResult tablosuna veri gönderilmeye devam edebilir. Bu yaklaşım, mevcut günlük tabanlı uyarıların ve tümleştirmelerin etkilenmemesini sağlar.
* Yeni oluşturulan Bağlantı İzleyicisi, bağlantı Izleyicisi 'nde görünür.

Geçişten sonra şunları yaptığınızdan emin olun:
* NPM 'deki testleri el ile devre dışı bırakın. Bunu yapana kadar ücretlendirilmeye devam edersiniz. 
* NPM 'yi devre dışı bırakırken, uyarılarınızı NWConnectionMonitorTestResult ve NWConnectionMonitorPathResult tablolarında yeniden oluşturun veya ölçümleri kullanın. 
* Tüm dış tümleştirmeleri NWConnectionMonitorTestResult ve NWConnectionMonitorPathResult tablolarına geçirin. Dış tümleştirmelere örnek olarak Power BI ve Grafana ' deki panolar ve güvenlik bilgileri ve olay yönetimi (SıEM) sistemleriyle tümleştirmeler verilebilir.


## <a name="next-steps"></a>Sonraki adımlar

Bağlantı Izleyicisi hakkında daha fazla bilgi edinmek için bkz.:
* [Bağlantı Izleyicisi 'nden (klasik) bağlantı Izleyicisi 'ne geçiş](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Azure portal kullanarak bağlantı Izleyicisi oluşturma](./connection-monitor-create-using-portal.md)
