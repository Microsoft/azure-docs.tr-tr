---
title: Bağlantı İzleyicisi oluşturma-PowerShell
titleSuffix: Azure Network Watcher
description: PowerShell kullanarak bir bağlantı İzleyicisi oluşturmayı öğrenin.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833040"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>PowerShell kullanarak bağlantı İzleyicisi oluşturma

> [!IMPORTANT]
> 1 Temmuz 2021 ' den itibaren, mevcut bir çalışma alanına yeni testler ekleyemez veya Ağ Performansı İzleyicisi yeni bir çalışma alanı etkinleştiremeyeceksiniz. Ayrıca, bağlantı Izleyicisinde (klasik) yeni bağlantı izleyicileri ekleyemeyeceksiniz. 1 Temmuz 2021 ' den önce oluşturulan testleri ve bağlantı izleyicilerini kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, [testlerinizi ağ performansı İzleyicisi ](migrate-to-connection-monitor-from-network-performance-monitor.md) veya  [bağlantı izleyicisinden (klasik)](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure ağ Izleyicisi 'ndeki yeni bağlantı Izleyicisinden, 29 Şubat 2024 tarihinden önce geçirin.


Kaynaklarınız arasındaki iletişimi izlemek için Azure ağ Izleyicisi 'nin bağlantı Izleyicisi özelliğini nasıl kullanacağınızı öğrenin.


## <a name="before-you-begin"></a>Başlamadan önce

Bağlantı Izleyicisi ile oluşturduğunuz bağlantı izleyicilerinde, hem şirket içi makineleri hem de Azure sanal makinelerini (VM 'Ler) kaynak olarak ekleyebilirsiniz. Bu bağlantı izleyicileri, uç noktalara bağlantıyı da izleyebilir. Uç noktalar Azure veya başka bir URL veya IP üzerinde olabilir.

Bir bağlantı İzleyicisi aşağıdaki varlıkları içerir:

* **Bağlantı İzleyicisi kaynağı**: bölgeye özgü bir Azure kaynağı. Aşağıdaki varlıkların hepsi Bağlantı İzleyicisi kaynağının özellikleridir.
* **Uç nokta**: bağlantı denetimlerine katılan kaynak veya hedef. Uç noktalara örnek olarak Azure VM 'Leri, şirket içi aracılar, URL 'Ler ve IP 'Ler verilebilir.
* **Test yapılandırması**: bir test için protokole özgü bir yapılandırma. Seçtiğiniz protokole bağlı olarak, bağlantı noktası, eşikler, sınama sıklığı ve diğer parametreleri tanımlayabilirsiniz.
* **Test grubu**: kaynak uç noktaları, hedef uç noktaları ve test yapılandırmalarının bulunduğu grup. Bir bağlantı İzleyicisi birden fazla test grubu içerebilir.
* **Test**: kaynak uç noktası, hedef uç noktası ve test yapılandırmasının birleşimi. Test, izleme verilerinin kullanılabildiği en ayrıntılı düzeydir. İzleme verileri, başarısız olan denetim yüzdesini ve gidiş dönüş süresini (RTT) içerir.

    ![Test grupları ve testler arasındaki ilişkiyi tanımlayan bir bağlantı izleyicisini gösteren diyagram.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Bağlantı İzleyicisi oluşturma adımları

PowerShell kullanarak bir bağlantı İzleyicisi oluşturmak için aşağıdaki komutları kullanın.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Özelliklerin açıklaması

* **Connectionmonitorname**: Bağlantı İzleyicisi kaynağının adı.

* **Sub**: Bağlantı İzleyicisi oluşturmak istediğiniz ABONELIĞIN abonelik kimliği.

* **NW**: bir bağlantı izleyicisinin oluşturulduğu ağ IZLEYICISI kaynak kimliği.

* **Konum**: bağlantı izleyicisinin oluşturulduğu bölge.

* **Uç Noktalar**
    * **Ad**: her bir uç nokta için benzersiz ad.
    * **Kaynak kimliği**: Azure uç noktaları IÇIN kaynak kimliği, VM 'ler için Azure Resource Manager kaynak kimliğine başvurur. Azure dışı uç noktalar için kaynak KIMLIĞI, Azure olmayan aracılara bağlı Log Analytics çalışma alanının Azure Resource Manager kaynak KIMLIĞINE başvurur.
    * **Adres**: yalnızca kaynak kimliği belirtilmediğinde veya kaynak kimliği Log Analytics çalışma alanında ise geçerlidir. Kaynak KIMLIĞI olmadan kullanılırsa bu, herhangi bir genel bitiş noktasının URL veya IP 'SI olabilir. Bir Log Analytics kaynak KIMLIĞIYLE birlikte kullanılırsa, bu, izleme aracısının FQDN 'sine başvurur.
    * **Filtre**: Azure dışı uç noktalar için, bağlantı izleyicisi kaynağında Log Analytics çalışma alanından izleme aracıları ' nı seçmek için filtreleri kullanın. Filtreler ayarlanmamışsa, Log Analytics çalışma alanına ait olan tüm aracılar izleme için kullanılabilir.
        * **Tür**: **Aracı adresi** olarak ayarlayın.
        * **Adres**: şirket içi ARACıNıZıN FQDN 'si olarak ayarlayın.

* **Test grupları**
    * **Ad**: test grubunuzu adlandırın.
    * **Kaynaklar**: daha önce oluşturduğunuz uç noktalardan seçin. Azure tabanlı kaynak uç noktalarında Azure ağ Izleyicisi uzantısının yüklü olması gerekir; Azure tabanlı olmayan kaynak uç noktalarında bir Azure Log Analytics aracısının yüklü olması gerekir. Kaynağınıza yönelik bir aracı yüklemek için bkz. [izleme aracılarını yüklemek](./connection-monitor-overview.md#install-monitoring-agents).
    * **Hedefler**: daha önce oluşturduğunuz uç noktalardan seçin. Azure VM 'lerine veya herhangi bir uç noktaya (genel IP, URL veya FQDN) yönelik bağlantıyı, bunları hedefler olarak belirterek izleyebilirsiniz. Tek bir test grubunda Azure VM 'leri, Office 365 URL 'Leri, Dynamics 365 URL 'Leri ve özel uç noktaları ekleyebilirsiniz.
    * **Devre dışı bırak**: test grubunun belirttiği tüm kaynaklar ve hedefler için izlemeyi devre dışı bırakın.

* **Test yapılandırması**
    * **Ad**: test yapılandırmasını adlandırın.
    * **TestFrequencySec**: hangi kaynakların hangi sıklıkla, belirttiğiniz protokolde ve bağlantı noktasında ne sıklıkta ping alınacağını belirtin. 30 saniye, 1 dakika, 5 dakika, 15 dakika veya 30 dakika seçebilirsiniz. Kaynaklar, seçtiğiniz değere göre hedeflere yönelik bağlantıyı test edin. Örneğin, 30 saniye seçerseniz, kaynaklar en az 30 saniyelik bir dönemde hedefe bağlantıyı kontrol edin.
    * **Protokol**: TCP, ıCMP, http veya https seçin. Protokolüne bağlı olarak, aşağıdaki protokole özgü yapılandırmalara de seçim yapabilirsiniz:
        * **preferhttps**: http üzerinden https kullanıp kullanmayacağınızı belirtin.
        * **bağlantı noktası**: tercih ettiğiniz hedef bağlantı noktasını belirtin.
        * **Disableizleme Oute**: topoloji ve atlama-atlama RTT bulma kaynaklarını durdurun. Bu, TCP veya ıCMP ile test grupları için geçerlidir.
        * **Yöntem**: http istek YÖNTEMINI (Get veya post) seçin. Bu, HTTP ile test yapılandırmalarına yöneliktir.
        * **yol**: URL 'ye eklenecek yol parametrelerini belirtin.
        * **Validstatuscodes**: geçerli durum kodlarını seçin. Yanıt kodu eşleşmezse, bir tanılama iletisi görüntülenir.
        * **RequestHeaders**: hedefe geçirilecek özel istek üst bilgisi dizelerini belirtin.
    * **Başarı eşiği**: aşağıdaki ağ parametrelerinde eşikleri ayarlayın:
        * **Checksfailedpercent**: kaynaklar, belirttiğiniz ölçütleri kullanarak hedeflere bağlantı denetlediğinde başarısız olan denetimlerin yüzdesini ayarlayın. TCP veya ıCMP protokolü için, başarısız denetimlerin yüzdesi, paket kaybı yüzdesine eşit olabilir. HTTP protokolü için, bu alan yanıt olmadan alınan HTTP isteklerinin yüzdesini temsil eder.
        * **Roundroundtimems**: milisaniye cinsinden test yapılandırması üzerinden hedefe bağlanmak için kaynakların ne kadar süreceğini belirleyin.

## <a name="scale-limits"></a>Ölçek sınırları

Bağlantı izleyicileri aşağıdaki ölçek sınırlarına sahiptir:

* Her bölge için abonelik başına en fazla bağlantı İzleyicisi: 100
* Bağlantı İzleyicisi başına en fazla test grubu: 20
* Bağlantı İzleyicisi başına en fazla kaynak ve hedef sayısı: 100
* Bağlantı İzleyicisi başına en fazla test yapılandırması: 20

## <a name="next-steps"></a>Sonraki adımlar

* [İzleme verilerini çözümlemeyi ve uyarıları ayarlamayı](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)öğrenin.
* [Ağınızdaki sorunları tanılamayı](./connection-monitor-overview.md#diagnose-issues-in-your-network)öğrenin.
