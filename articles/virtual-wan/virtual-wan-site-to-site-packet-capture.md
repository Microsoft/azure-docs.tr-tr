---
title: 'Öğretici: Azure sanal WAN siteden siteye bağlantılar üzerinde paket yakalama gerçekleştirme'
description: Bu öğreticide, sanal WAN siteden siteye VPN Gateway paket yakalama işlemini nasıl gerçekleştireceğinizi öğrenin.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879173"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Azure sanal WAN siteden siteye VPN Gateway paket yakalamayı gerçekleştirin 

Bağlantı ve performansla ilgili sorunlar genellikle karmaşıktır. Bu, sorunun nedenini azaltmak için önemli ölçüde zaman ve çaba sürebilirler. Paket yakalama, bir sorunun kapsamını ağın belirli bölümlerine daraltmanıza yardımcı olabilir. Sorunun, ağın müşteri tarafında, ağın Azure tarafında veya arasında bir yerde olup olmadığını belirlemenize yardımcı olabilir. Sorunu daraltdıktan sonra, hata ayıklama ve düzeltici eylem gerçekleştirmek daha etkilidir.

Aşağıdaki makalede, Azure sanal WAN siteden siteye VPN Gateway bir paket yakalamanın nasıl başlatılacağı ve durdurulacağı açıklanmaktadır. Şu anda bu işlev yalnızca PowerShell aracılığıyla kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları kullanmak için, ortamınızda aşağıdaki yapılandırmanın zaten ayarlanmış olması gerekir:

* Sanal hub 'da dağıtılan bir sanal WAN, sanal hub ve siteden siteye VPN Gateway. Ayrıca, VPN sitelerini siteden siteye VPN Gateway bağlayan bağlantılara de sahip olabilirsiniz.


### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Ortamı ayarlama

PowerShell 'de aşağıdaki betiği çalıştırarak doğru abonelik bağlamında olduğunuzdan emin olun. Bu, siteden siteye VPN Gateway paket yakalamayı gerçekleştirme izni olan bir kullanıcının oturum açmasını sağlar.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Depolama hesabı oluşturma

Paket yakalamalarınızın sonuçlarını depolamak için Azure aboneliğinizde bir depolama hesabı oluşturmanız gerekir. Depolama hesabı oluşturma yönergeleri için lütfen bu [belgeye](.././storage/common/storage-account-create.md) başvurun.

Hesabınızı oluşturduktan sonra, paylaşılan erişim imzası (SAS) URL 'SI oluşturmak için lütfen aşağıdaki komutları çalıştırın. Paket yakalamalarınızın sonuçları bu URL üzerinden depolanacak.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Paket yakalamayı Başlat

Paket yakalamayı başlatmak için iki seçeneğe sahip olursunuz, tek bir VPN bağlantısında veya tüm siteden siteye VPN Gateway olan paketleri yakalayabilirsiniz. VPN bağlantılarında yakalanan işlemleri yapmayı seçerseniz, yalnızca 6 bağlantı üzerinde yakalamaları aynı anda gerçekleştirebilirsiniz.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Siteden siteye VPN Gateway paket yakalama (tüm bağlantılar)

Lütfen aşağıdaki komutları çalıştırın. Siteden siteye VPN Gateway adı, bağlantı altında VPN (siteden siteye) öğesine tıklanarak sanal hub 'ınıza giderek bulunabilir.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Sanal WAN ağ geçidi adının görüntüsü." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Belirli siteden siteye VPN bağlantılarında paket yakalama

>[!NOTE]
> Lütfen aynı anda 5 VPN bağlantı üzerinde bir paket yakalama çalıştırabileceğinizi unutmayın.


Lütfen aşağıdaki komutları çalıştırın. Siteden siteye VPN bağlantılarının adı, sanal hub 'ınıza gidip bağlantı altında VPN (siteden siteye) öğesine tıklanarak bulunabilir. Ardından, paket yakalama işlemini gerçekleştirmek istediğiniz VPN sitesine gidin ve sağdaki üç noktaya tıklayın. Açılan menüdeki **VPN bağlantısını Düzenle** ' ye tıklayın.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="VPN bağlantı adlarını bulma görüntüsü." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Belirli bir VPN sitesine bağlı bağlantıların adı, önceki bölümden VPN sitesine tıklayıp **Bağlantılar** tablosuna bakarak bulunabilir. 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="VPN bağlantı adının nasıl bulunacağını gösteren resim." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>İsteğe bağlı: filtreleri belirtme

Yaygın olarak kullanılan bazı paket yakalama araçları vardır. Bu araçlarla ilgili paket yakalamalarını almak, özellikle de yüksek hacimli trafik senaryolarında çok daha fazla olabilir. Paket yakalamanızı basitleştirmek için, belirli davranışlara odaklanmak üzere paket yakalamanız üzerinde filtreler belirtebilirsiniz. Kullanılabilir parametreler şunlardır:

>[!NOTE]
> TracingFlags ve TCPFlags için, yakalamak istediğiniz protokollerin sayısal değerlerini ekleyerek birden çok protokol belirtebilirsiniz (mantıksal veya ile aynı). Örneğin, yalnızca ESP ve OPVN paketlerini yakalamak isterseniz, 8 + 1 = 9 ' un bir TracingFlag değeri belirtirsiniz.  

| Parametre | Açıklama | Varsayılan değerler | Kullanılabilir değerler|
|--- |--- | --- | ---|
| TracingFlags | Hangi tür paketlerin yakalandığını belirleyen tamsayı | 11 (ESP, ıKE, OVPN) | ESP = 1 ıKE = 2 OPVN = 8 |
| TCPFlags | Hangi tür TCP paketlerinin yakalandığını belirleyen tamsayı | 0 (yok) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16, URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Yakalanan bir paketin bayt cinsinden en büyük boyutu. Paketler, girilen değerden daha büyükse kesilir. |120|Herhangi biri|
| MaxFileSize |En büyük yakalama dosyası boyutu (MB). Yakalamalar döngüsel bir arabellekte depolanır, böylece taşma bir FıFO olarak işlenir (eski paketler önce kaldırılır)|100|Herhangi biri|
|Sourcesubneti|Belirtilen CıDR aralıklarından gelen paketler yakalanır. Bir dizi olarak belirtilir.|[] (tüm IPv4 adresleri)|Virgülle ayrılmış ıPV4 alt ağlarının dizisi|
| Hedef alt ağlar |Belirtilen CıDR aralıklarına gidecek paketler yakalanır. Bir dizi olarak belirtilir. |[] (tüm IPv4 adresleri)|Virgülle ayrılmış ıPV4 alt ağlarının dizisi|
|SourcePort |Belirtilen aralıklardaki kaynak içeren paketler yakalanır. Bir dizi olarak belirtilir.|[] (tüm bağlantı noktaları)|Virgülle ayrılmış bağlantı noktaları dizisi|
|DestinationPort |Belirtilen aralıklardaki hedefe sahip paketler yakalanır. Bir dizi olarak belirtilir.|[] (tüm bağlantı noktaları)|Virgülle ayrılmış bağlantı noktaları dizisi|
| CaptureSingleDirectionTrafficOnly |Doğru ise, paket yakalamada çift yönlü akışın yalnızca bir yönü görünür. Bu, tüm olası IP ve bağlantı noktalarını yakalar.|Doğru|Doğru, yanlış|
|Protokol|IANA protokollerine karşılık gelen tamsayılar dizisi. |[] (tüm protokoller)| [Burada](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) bulunan protokoller |

Bir filtre dizesi kullanan örnek bir paket yakalama aşağıda verilmiştir. Parametreleri yukarıdaki tabloya göre gereksinimlerinize uyacak şekilde değiştirebilirsiniz.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Paket yakalaması durduruluyor
Paket yakalamanın en az 600 saniye boyunca çalışmasına izin vermenizi öneririz. Yoldaki birden çok bileşen arasındaki eşitleme sorunları nedeniyle, daha kısa bir paket yakalama, verilerin tamamını sağlamayabilir. Paket yakalamayı durdurmaya hazırsanız, aşağıdaki komutu çalıştırın.

Parametreler, paket yakalama başlatma bölümünde olanlarla benzerdir. SAS URL 'SI [depolama hesabı oluşturma](#createstorage) bölümünde oluşturulmuştur.

### <a name="gateway-level-packet-capture"></a>Ağ Geçidi düzeyinde paket yakalama

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Bağlantı düzeyi paket yakalamaları

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Paket yakalamanızı görüntüleme

Azure portal ' de, "depolama hesabı oluşturma" bölümünde oluşturulan depolama hesabına gidin, kapsayıcınıza tıklayın ve dosyayı indirin. Paket yakalama veri dosyaları PCAP biçiminde oluşturulur. PCAP dosyalarını açmak için Wireshark veya başka bir sık kullanılan uygulama kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sonra, sanal WAN hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> * [Sanal WAN hakkında SSS](virtual-wan-faq.md)