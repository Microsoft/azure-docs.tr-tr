---
title: Azure Trafik Analizi sık sorulan sorular | Microsoft Docs
description: Trafik Analizi hakkında en sık sorulan sorulardan bazılarına yanıt alın.
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
ms.date: 03/08/2018
ms.author: kumud
ms.openlocfilehash: 991bb91c5bc1f6d695d5b363cdb08268f1ee83df
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907104"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Trafik Analizi sık sorulan sorular

Bu makale, Azure ağ Izleyicisi 'nde trafik analizi hakkında en sık sorulan soruların çoğunu tek bir yerde toplar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Trafik analizini kullanma önkoşulları nelerdir?

Trafik Analizi aşağıdaki önkoşulları gerektirir:

- Ağ Izleyicisi etkin bir abonelik.
- İzlemek istediğiniz NSG 'ler için ağ güvenlik grubu (NSG) akış günlükleri etkinleştirildi.
- Ham akış günlüklerini depolamak için bir Azure depolama hesabı.
- Okuma ve yazma erişimiyle Azure Log Analytics çalışma alanı.

Trafik analizlerini etkinleştirmek için hesabınızın aşağıdakilerden birini karşılaması gerekir:

- Hesabınız abonelik kapsamında şu rol tabanlı erişim denetimi (RBAC) rollerinden birine sahip olmalıdır: sahip, katkıda bulunan, okuyucu veya ağ katılımcısı.
- Hesabınız daha önce listelenen rollerden birine atanmamışsa, abonelik düzeyinde aşağıdaki eylemlerin atandığı özel bir role atanması gerekir.
            
    - Microsoft. Network/Applicationgateway/Read
    - Microsoft. Network/Connections/Read
    - Microsoft. Network/loadBalancers/Read 
    - Microsoft. Network/Localnetworkgateway/Read 
    - Microsoft. Network/NetworkInterfaces/Read 
    - Microsoft. Network/networkSecurityGroups/Read 
    - Microsoft. Network/publicIPAddresses/Read
    - Microsoft. Network/routeTables/Read
    - Microsoft. Network/Virtualnetworkgateway/Read 
    - Microsoft. Network/virtualNetworks/Read
        
Bir abonelik için kullanıcıya atanan rolleri denetlemek için:

1. **Login-AzAccount**komutunu kullanarak Azure 'da oturum açın. 

2. **Select-AzSubscription**kullanarak gerekli aboneliği seçin. 

3. Belirtilen bir kullanıcıya atanan tüm rolleri listelemek için **Get-Azroleatama-Signınname [User email]-IncludeClassicAdministrators**komutunu kullanın. 

Herhangi bir çıkış görmüyorsanız, komutları çalıştırmak için erişim sağlamak üzere ilgili abonelik yöneticisine başvurun. Daha ayrıntılı bilgi için bkz. [Azure PowerShell ile rol tabanlı erişim denetimini yönetme](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Hangi Azure bölgeleri Trafik Analizi kullanılabilir?

NSG 'ler için trafik analizini aşağıdaki desteklenen bölgelerden herhangi birinde kullanabilirsiniz:
- Kanada Orta
- Batı Orta ABD
- Doğu ABD
- Doğu ABD 2
- Orta Kuzey ABD
- Orta Güney ABD
- Orta ABD
- Batı ABD
- Batı ABD 2
- Fransa Orta
- Batı Avrupa
- Kuzey Avrupa
- Güney Brezilya
- Birleşik Krallık Batı
- Birleşik Krallık Güney
- Avustralya Doğu
- Avustralya Güneydoğu 
- Doğu Asya
- Güneydoğu Asya
- Kore Orta
- Orta Hindistan
- Güney Hindistan
- Japonya Doğu
- Japonya Batı
- ABD Devleti Virginia
- Çin Doğu 2

Log Analytics çalışma alanı aşağıdaki bölgelerde bulunmalıdır:
- Kanada Orta
- Batı Orta ABD
- Doğu ABD
- Doğu ABD 2
- Orta Kuzey ABD
- Orta Güney ABD
- Orta ABD
- Batı ABD
- Batı ABD 2
- Fransa Orta
- Batı Avrupa
- Kuzey Avrupa
- Birleşik Krallık Batı
- Birleşik Krallık Güney
- Avustralya Doğu
- Avustralya Güneydoğu
- Doğu Asya
- Güneydoğu Asya 
- Kore Orta
- Orta Hindistan
- Japonya Doğu
- ABD Devleti Virginia
- Çin Doğu 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>NSG 'ler, akış günlüklerini çalışma alanım 'ın farklı bölgelerinde mi etkinleştirebilirim?

Evet, bu NSG 'ler Log Analytics çalışma alanınızdan farklı bölgelerde olabilir.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Tek bir çalışma alanı içinde birden çok NSG yapılandırılabilir mi?

Evet.

## <a name="can-i-use-an-existing-workspace"></a>Mevcut bir çalışma alanını kullanabilir miyim?

Evet. Mevcut bir çalışma alanını seçerseniz, yeni sorgu diline geçirilmiş olduğundan emin olun. Çalışma alanını yükseltmek istemiyorsanız yeni bir tane oluşturmanız gerekir. Yeni sorgu dili hakkında daha fazla bilgi için bkz. [Azure izleyici günlükleri yeni günlük aramasına yükseltme](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Azure depolama Hesabım bir abonelikte yer alabilir ve Log Analytics çalışma alanım farklı bir abonelikte olabilir mi?

Evet, Azure depolama hesabınız tek bir abonelikte olabilir ve Log Analytics çalışma alanınız farklı bir abonelikte olabilir.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Ham günlükleri farklı bir abonelikte depolayabilirim miyim?

Hayır. Ham günlükleri, bir NSG 'nin akış günlükleri için etkinleştirildiği tüm depolama hesaplarına kaydedebilirsiniz. Ancak, hem depolama hesabı hem de ham Günlükler aynı abonelikte ve bölgede olmalıdır.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>"Bulunamadı" hatası nedeniyle trafik analizi için NSG 'yi yapılandıramıyorum?

Desteklenen bir bölge seçin. Desteklenmeyen bir bölge seçerseniz, "bulunamadı" hatası alırsınız. Desteklenen bölgeler Bu makalenin önceki kısımlarında listelenmiştir.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>NSG akış günlükleri sayfası altında "yüklenemedi" durumunu alıyorsanız ne yapmalıyım?

Akış günlüğünün düzgün çalışması için Microsoft. Insights sağlayıcısı 'nın kayıtlı olması gerekir. Aboneliğiniz için Microsoft. Insights sağlayıcısı 'nın kayıtlı olup olmadığından emin değilseniz, aşağıdaki komutta *xxxxx-xxxxx-xxxxxx-xxxx* ' i değiştirin ve PowerShell 'den aşağıdaki komutları çalıştırın:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Çözümü yapılandırdım. Neden panoda hiçbir şey görmüyorum?

Panonun ilk kez görünmesi 30 dakika kadar sürebilir. Çözüm, öncelikle anlamlı Öngörüler türetmede yeterince veri toplamalıdır. Daha sonra raporlar oluşturur. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Bu iletiyi alırsam ne olursa yapın: "seçili zaman aralığı için bu çalışma alanında herhangi bir veri bulamadık. Zaman aralığını değiştirmeyi deneyin veya farklı bir çalışma alanı seçin. "?

Aşağıdaki seçenekleri deneyin:
- Üstteki çubukta zaman aralığını değiştirin.
- Üstteki çubukta farklı bir Log Analytics çalışma alanı seçin.
- Son zamanlarda etkinleştirilmişse, 30 dakika sonra trafik analizinden erişmeyi deneyin.
    
Sorun devam ederse, [Kullanıcı sesli forumundaki](https://feedback.azure.com/forums/217313-networking?category_id=195844)kaygıları yükseltin.

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Bu iletiyi aldım: "NSG akış günlüklerinizi ilk kez analiz etme. Bu işlemin tamamlanması 20-30 dakika sürebilir. Bir süre sonra yeniden kontrol edin. 2) yukarıdaki adım işe yaramazsa ve çalışma alanınız ücretsiz SKU 'nun altındaysa, kotayı doğrulamak için burada çalışma alanı kullanımınızı denetleyin, aksi takdirde daha fazla bilgi için SSS bölümüne bakın. "?

Şu nedenle şu iletiyi görebilirsiniz:
- Trafik Analizi son zamanlarda etkinleştirilmiştir ve anlamlı Öngörüler türeyebilmek için henüz yeterince veri toplanmayabilir.
- Log Analytics çalışma alanının ücretsiz sürümünü kullanıyorsunuz ve kota sınırlarını aşmış. Daha büyük bir kapasiteye sahip bir çalışma alanı kullanmanız gerekebilir.
    
Sorun devam ederse, [Kullanıcı sesli forumundaki](https://feedback.azure.com/forums/217313-networking?category_id=195844)kaygıları yükseltin.
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Bu iletiyi alırsam ne olursa olsun: "kaynak verileri (topoloji) ve akış bilgisi yok gibi görünüyor. Bu arada, kaynak verilerini görmek ve daha fazla bilgi için SSS 'ye başvurmak üzere buraya tıklayın. "?

Kaynak bilgilerini panoda görüyorsunuz. Ancak, akışla ilgili bir istatistik yoktur. Kaynaklar arasında hiçbir iletişim akışı olmadığından veriler bulunmayabilir. 60 dakika bekleyin ve durumu yeniden denetleyin. Sorun devam ederse ve kaynaklar arasındaki iletişim akışının mevcut olduğundan emin değilseniz, [Kullanıcı sesli forumundan](https://feedback.azure.com/forums/217313-networking?category_id=195844)kaygıları yükseltin.

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>PowerShell 'i veya bir Azure Resource Manager şablonu ya da istemciyi kullanarak trafik analizini yapılandırabilir miyim?

Trafik analizlerini Windows PowerShell 'i sürüm 6.2.1 sürümlerini kullanarak yapılandırabilirsiniz. Set cmdlet 'ini kullanarak belirli bir NSG için akış günlüğü ve trafik analizi yapılandırmak için bkz. [set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Belirli bir NSG için akış günlüğü ve trafik analizi durumunu almak için bkz. [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Şu anda trafik analizini yapılandırmak için bir Azure Resource Manager şablonu kullanamazsınız.

Trafik analizini Azure Resource Manager istemci kullanarak yapılandırmak için aşağıdaki örneklere bakın.

**Set cmdlet örneği:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Cmdlet örneği al:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Trafik Analizi nasıl fiyatlandırılır?

Trafik Analizi tarifeli. Ölçüm, hizmet tarafından akış günlüğü verilerinin işlenmesini ve elde edilen gelişmiş günlükleri bir Log Analytics çalışma alanında depolamayı temel alır. 

Örneğin, Orta Batı ABD bölgeyi ele alan [fiyatlandırma planına](https://azure.microsoft.com/pricing/details/network-watcher/)göre, trafik analizi tarafından işlenen bir depolama hesabında depolanan akış günlüğü VERILERI 10 gb ve Log Analytics çalışma alanında alınan gelişmiş Günlükler 1 GB ise geçerli ücretler şunlardır: 10 x $2,3 + 1 x $2,76 = $25,76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Ne sıklıkla veri Trafik Analizi?

Trafik Analizi şeması ve veri toplama belgesi içindeki [veri toplama bölümüne](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) bakın

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Trafik Analizi bir IP 'nin kötü amaçlı olduğuna nasıl karar veriyor? 

Trafik Analizi, bir IP 'yi kötü amaçlı olarak bozmasını sağlamak için Microsoft iç tehdit zekasını kullanır. Bu sistemler, Microsoft ürün ve Hizmetleri, Microsoft Digital Crimes birimi (DCU), Microsoft Güvenlik Yanıt Merkezi (MSRC) ve dış akışlar gibi farklı telemetri kaynaklarından yararlanır ve bunun üzerine çok fazla zeka oluşturur. Bu verilerden bazıları Microsoft Iç ' dır. Bilinen bir IP, kötü amaçlı olarak işaretlenmezse, ayrıntıları bilmemiz için lütfen bir destek bileti yükseltin.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Trafik Analizi verileri üzerinde uyarıları nasıl ayarlayabilirim?

Trafik Analizi, uyarılar için yerleşik olmayan desteğe sahip değil. Ancak Trafik Analizi veriler Log Analytics depolandığından, özel sorgular yazabilir ve bunlara uyarı ayarlayabilirsiniz. Olanları
- Trafik Analizi Log Analytics için shortlink kullanabilirsiniz. 
- Sorgularınızı yazmak için [burada belgelenen şemayı](traffic-analytics-schema.md) kullanın 
- Uyarıyı oluşturmak için "yeni uyarı kuralı" na tıklayın
- Uyarı oluşturmak için [günlük uyarıları belgelerine](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) başvurun

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Coğrafi Harita görünümündeki klavyeyi kullanarak nasıl gezinirim?

Coğrafi harita sayfası iki ana bölüm içerir:
    
- **Başlık**: coğrafi haritanın en üstündeki başlık, trafik dağıtım filtrelerini (örneğin, dağıtım, ülke/bölge veya kötü amaçlı trafik) seçmek için düğmeler sağlar. Bir düğmeyi seçtiğinizde ilgili filtre haritada uygulanır. Örneğin, etkin düğmesini seçerseniz, eşleme dağıtımınızdaki etkin veri merkezlerini vurgular.
- **Harita**: başlığın altında, harita bölümünde Azure veri merkezleri ve ülkeler/bölgeler arasında trafik dağıtımı gösterilmektedir.
    
### <a name="keyboard-navigation-on-the-banner"></a>Başlık üzerinde klavye gezintisi
    
- Varsayılan olarak, başlık için coğrafi harita sayfasında bulunan seçim, "Azure DCs" filtresidir.
- Başka bir filtreye geçmek için `Tab` ya da `Right arrow` anahtarını kullanın. Geriye doğru gitmek için `Shift+Tab` veya `Left arrow` anahtarını kullanın. İleri doğru gezinme, yukarıdan aşağıya doğru bir şekilde solýdir.
- Seçili filtreyi uygulamak için `Enter` veya `Down` ok tuşuna basın. Filtre seçimine ve dağıtımına dayalı olarak, harita bölümünün altındaki bir veya birden çok düğüm vurgulanır.
- Başlık ve eşleme arasında geçiş yapmak için `Ctrl+F6`tuşuna basın.
        
### <a name="keyboard-navigation-on-the-map"></a>Haritada klavye gezintisi
    
- Başlık üzerinde herhangi bir filtre seçtikten ve `Ctrl+F6`basılı olduktan sonra, odak harita görünümünde vurgulanan düğümlerden birine (**Azure veri merkezi** veya **ülke/bölge**) gider.
- Haritadaki diğer vurgulanan düğümlere geçmek için, ileri hareket için `Tab` ya da `Right arrow` anahtarını kullanın. Geriye doğru hareket için `Shift+Tab` ya da `Left arrow` anahtarını kullanın.
- Haritadaki vurgulanmış düğümleri seçmek için `Enter` veya `Down arrow` anahtarını kullanın.
- Bu tür düğümlerin seçimi üzerinde odak, düğüm için **bilgi araç kutusuna** taşınır. Varsayılan olarak, odak **bilgi araç kutusundaki**kapalı düğmesine taşınır. **Kutu** görünümü içinde daha fazla hareket etmek için `Right arrow` ve `Left arrow` tuşlarını kullanarak sırasıyla ileri ve geri hareket edin. `Enter` ' ye basmak, **bilgi araç kutusunda**odaklanmış düğmesini seçmekle aynı etkiye sahiptir.
- Odak **bilgi aracı kutusdayken**`Tab` ' a bastığınızda, odak seçili düğüm ile aynı kıta olan bitiş noktalarına taşınır. Bu uç noktalar arasında gezinmek için `Right arrow` ve `Left arrow` anahtarlarını kullanın.
- Diğer akış uç noktalarına veya kıta kümelerine geçmek için, ileri hareket için `Tab` ve geriye doğru hareket için `Shift+Tab` kullanın.
- Odak, **kıta kümeler**üzerinde olduğunda, kıta küme içindeki uç noktaları vurgulamak için `Enter` veya `Down` ok tuşlarını kullanın. Kıta kümesinin bilgi kutusunda uç noktalar ve Kapat düğmesi arasında ilerlemek için, sırasıyla ileri ve geri hareket için `Right arrow` veya `Left arrow` tuşunu kullanın. Herhangi bir uç noktada, seçili düğümdeki bağlantı satırına uç noktaya geçmek için `Shift+L` kullanabilirsiniz. Seçili uç noktaya taşımak için `Shift+L` yeniden basabilirsiniz.
        
### <a name="keyboard-navigation-at-any-stage"></a>Herhangi bir aşamada klavye gezintisi
    
- `Esc` genişletilmiş seçimi daraltır.
- `Up arrow` anahtar `Esc`aynı eylemi gerçekleştirir. `Down arrow` anahtar `Enter`aynı eylemi gerçekleştirir.
- Yakınlaştırmak için `Shift+Plus` kullanın ve uzaklaştırmak için `Shift+Minus`.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Sanal ağ topolojisi görünümündeki klavyeyi kullanarak nasıl gezinirim?

Sanal ağlar topolojisi sayfası iki ana bölüm içerir:
    
- **Başlık**: sanal ağlar topolojisinin en üstündeki başlık, trafik dağıtım filtrelerini (örneğin, bağlı sanal ağlar, bağlantısı kesilen sanal ağlar ve genel IP 'ler) seçmek için düğmeler sağlar. Bir düğmeyi seçtiğinizde ilgili filtre topolojiye uygulanır. Örneğin, etkin düğmesini seçerseniz, topoloji dağıtımınızdaki etkin sanal ağları vurgular.
- **Topoloji**: başlığın altında, topoloji bölümünde sanal ağlar arasındaki trafik dağılımı gösterilmektedir.
    
### <a name="keyboard-navigation-on-the-banner"></a>Başlık üzerinde klavye gezintisi
    
- Varsayılan olarak, başlık için sanal ağlar topolojisi sayfasında bulunan seçim, "bağlı VNET" filtreidir.
- Başka bir filtreye geçmek için `Tab` tuşunu kullanarak ileri git ' i kullanın. Geri gitmek için `Shift+Tab` anahtarını kullanın. İleri doğru gezinme, yukarıdan aşağıya doğru bir şekilde solýdir.
- Seçili filtreyi uygulamak için `Enter` tuşuna basın. Filtre seçimine ve dağıtımına dayalı olarak, topoloji bölümünün altındaki bir veya birden çok düğüm (sanal ağ) vurgulanır.
- Başlık ve topoloji arasında geçiş yapmak için `Ctrl+F6`tuşuna basın.
        
### <a name="keyboard-navigation-on-the-topology"></a>Topolojide klavye gezintisi
    
- Başlık üzerinde herhangi bir filtre seçtikten ve `Ctrl+F6`basılı olduktan sonra, odak, topoloji görünümündeki vurgulanan düğümlerden birine (**VNET**) gider.
- Topoloji görünümünde vurgulanan diğer düğümlere geçmek için, iletme hareketi için `Shift+Right arrow` anahtarını kullanın. 
- Vurgulanan düğümlerde, odak düğüm için **bilgi araç kutusuna** taşınır. Varsayılan olarak, odak **bilgi araç kutusundaki** **diğer ayrıntılar** düğmesine taşınır. **Kutu** görünümü içinde daha fazla hareket etmek için `Right arrow` ve `Left arrow` tuşlarını kullanarak sırasıyla ileri ve geri hareket edin. `Enter` ' ye basmak, **bilgi araç kutusunda**odaklanmış düğmesini seçmekle aynı etkiye sahiptir.
- Bu tür düğümlerin seçimi üzerinde, `Shift+Left arrow` tuşuna basarak tüm bağlantılarını tek tek ziyaret edebilirsiniz. Odak, bu bağlantının **bilgi araç kutusuna** taşınır. Herhangi bir noktada, odak `Shift+Right arrow` yeniden basılarak düğüme geri dönebilir.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Alt ağ topolojisi görünümündeki klavyeyi kullanarak nasıl gezinirim?

Sanal alt ağlar topolojisi sayfası iki ana bölüm içerir:
    
- **Başlık**: sanal alt ağlar topolojisinin en üstündeki başlık, trafik dağıtım filtrelerini (örneğin, etkin, orta ve ağ geçidi alt ağları) seçmek için düğmeler sağlar. Bir düğmeyi seçtiğinizde ilgili filtre topolojiye uygulanır. Örneğin, etkin düğmesini seçerseniz, topoloji dağıtımınızdaki etkin sanal alt ağı vurgular.
- **Topoloji**: başlığın altında, topoloji bölümünde sanal alt ağlar arasındaki trafik dağıtımı gösterilmektedir.
    
### <a name="keyboard-navigation-on-the-banner"></a>Başlık üzerinde klavye gezintisi
    
- Varsayılan olarak, başlık için sanal alt ağlar topolojisi sayfasında bulunan seçim, "alt ağlar" filtreidir.
- Başka bir filtreye geçmek için `Tab` tuşunu kullanarak ileri git ' i kullanın. Geri gitmek için `Shift+Tab` anahtarını kullanın. İleri doğru gezinme, yukarıdan aşağıya doğru bir şekilde solýdir.
- Seçili filtreyi uygulamak için `Enter` tuşuna basın. Filtre seçimine ve dağıtımına dayalı olarak, topoloji bölümünün altındaki bir veya birden çok düğüm (alt ağ) vurgulanır.
- Başlık ve topoloji arasında geçiş yapmak için `Ctrl+F6`tuşuna basın.
        
### <a name="keyboard-navigation-on-the-topology"></a>Topolojide klavye gezintisi
    
- Başlık üzerinde herhangi bir filtre seçtikten ve `Ctrl+F6`basılı olduktan sonra, odak, topoloji görünümündeki vurgulanan düğümlerden birine (**alt ağ**) gider.
- Topoloji görünümünde vurgulanan diğer düğümlere geçmek için, iletme hareketi için `Shift+Right arrow` anahtarını kullanın. 
- Vurgulanan düğümlerde, odak düğüm için **bilgi araç kutusuna** taşınır. Varsayılan olarak, odak **bilgi araç kutusundaki** **diğer ayrıntılar** düğmesine taşınır. **Kutu** görünümü içinde daha fazla hareket etmek için `Right arrow` ve `Left arrow` tuşlarını kullanarak sırasıyla ileri ve geri hareket edin. `Enter` ' ye basmak, **bilgi araç kutusunda**odaklanmış düğmesini seçmekle aynı etkiye sahiptir.
- Bu tür düğümlerin seçimi üzerinde, `Shift+Left arrow` tuşuna basarak tüm bağlantılarını tek tek ziyaret edebilirsiniz. Odak, bu bağlantının **bilgi araç kutusuna** taşınır. Herhangi bir noktada, odak `Shift+Right arrow` yeniden basılarak düğüme geri dönebilir.    

