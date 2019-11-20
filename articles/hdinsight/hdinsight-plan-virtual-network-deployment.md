---
title: Azure HDInsight için bir sanal ağ planlayın
description: HDInsight 'ı diğer bulut kaynaklarına veya veri merkezinizdeki kaynaklara bağlamak için bir Azure sanal ağ dağıtımını nasıl planlayacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 61b929756cbc4cf13103faa67a667128eaffeec8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498177"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Azure HDInsight için bir sanal ağ planlayın

Bu makale, Azure HDInsight ile [Azure sanal ağlarını](../virtual-network/virtual-networks-overview.md) kullanma hakkında arka plan bilgileri sağlar. Ayrıca, HDInsight kümeniz için bir sanal ağ uygulayabilmeniz için yapılması gereken tasarım ve uygulama kararlarını da açıklar. Planlama aşaması tamamlandığında, [Azure HDInsight kümeleri için sanal ağlar oluşturmaya](hdinsight-create-virtual-network.md)devam edebilirsiniz. Ağ güvenlik gruplarını ve Kullanıcı tanımlı yolları düzgün şekilde yapılandırmak için gereken HDInsight yönetim IP adresleri hakkında daha fazla bilgi için bkz. [HDInsight YÖNETIM IP adresleri](hdinsight-management-ip-addresses.md).

Azure sanal ağı kullanmak aşağıdaki senaryolara izin vermez:

* HDInsight 'a doğrudan şirket içi ağdan bağlanma.
* HDInsight 'ı bir Azure sanal ağındaki veri depolarına bağlama.
* Internet üzerinden genel kullanıma açık olmayan [Apache Hadoop](https://hadoop.apache.org/) hizmetlere doğrudan erişme. Örneğin, [Apache Kafka](https://kafka.apache.org/) API 'Ler veya [Apache HBASE](https://hbase.apache.org/) Java API 'si.

> [!IMPORTANT]
> VNET 'te HDInsight kümesi oluşturmak, NIC 'ler ve yük dengeleyiciler gibi çeşitli ağ kaynakları oluşturur. Kümenizin VNET ile düzgün çalışması için gerektiğinden, bu ağ **kaynaklarını silmeyin.**
>
> 28 Şubat 2019 ' den sonra, VNET 'te oluşturulan yenı HDInsight kümeleri için ağ kaynakları (örneğin, NIC 'ler, LBs vb.), aynı HDInsight kümesi kaynak grubunda sağlanacak. Daha önce bu kaynaklar VNET kaynak grubunda sağlandı. Geçerli çalışan kümelerde ve VNET olmadan oluşturulan kümelerdeki hiçbir değişiklik yoktur.

## <a name="planning"></a>Planlama

Aşağıda, HDInsight 'ı bir sanal ağa yüklemeye planlarken yanıtlamanız gereken sorular verilmiştir:

* HDInsight 'ı mevcut bir sanal ağa yüklemeniz gerekiyor mu? Yoksa yeni bir ağ mi oluşturuyorsunuz?

    Var olan bir sanal ağı kullanıyorsanız, HDInsight 'ı yükleyebilmek için önce ağ yapılandırmasını değiştirmeniz gerekebilir. Daha fazla bilgi için, [var olan bir sanal ağa HDInsight ekleme](#existingvnet) bölümüne bakın.

* HDInsight içeren sanal ağı başka bir sanal ağa veya şirket içi ağınıza bağlamak istiyor musunuz?

    Ağlar arasındaki kaynaklarla kolayca çalışmak için özel bir DNS oluşturmanız ve DNS iletmeyi yapılandırmanız gerekebilir. Daha fazla bilgi için [birden çok ağı bağlama](#multinet) bölümüne bakın.

* Gelen veya giden trafiği HDInsight 'a kısıtlamak/yeniden yönlendirmek istiyor musunuz?

    HDInsight, Azure veri merkezi 'nde belirli IP adresleriyle Kısıtlanmamış iletişime sahip olmalıdır. İstemci iletişimine yönelik güvenlik duvarları aracılığıyla izin verilmesi gereken birkaç bağlantı noktası da vardır. Daha fazla bilgi için [ağ trafiğini denetleme](#networktraffic) bölümüne bakın.

## <a id="existingvnet"></a>Mevcut bir sanal ağa HDInsight ekleme

Mevcut bir Azure sanal ağına nasıl yeni HDInsight ekleneceğini saptamak için bu bölümdeki adımları kullanın.

> [!NOTE]  
> Mevcut bir HDInsight kümesini sanal bir ağa ekleyemezsiniz.

1. Sanal ağ için klasik veya Kaynak Yöneticisi dağıtım modeli mi kullanıyorsunuz?

    HDInsight 3,4 ve üzeri Kaynak Yöneticisi sanal ağ gerektirir. HDInsight 'ın önceki sürümlerinde klasik bir sanal ağ gerekiyordu.

    Var olan ağınız klasik bir sanal ağ ise, bir Kaynak Yöneticisi sanal ağı oluşturmanız ve ardından ikisini bağlamanız gerekir. [Klasik VNET 'leri yeni VNET 'Lere bağlama](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Katıldıktan sonra, Kaynak Yöneticisi ağı 'nda yüklenen HDInsight, klasik ağdaki kaynaklarla etkileşim kurabilir.

2. Trafiği sanal ağın içine veya dışına kısıtlamak için ağ güvenlik grupları, Kullanıcı tanımlı yollar veya sanal ağ gereçlerini kullanıyor musunuz?

    Yönetilen bir hizmet olarak, HDInsight 'ın Azure veri merkezi 'nde birkaç IP adresine sınırsız erişimi olması gerekir. Bu IP adresleriyle iletişime izin vermek için, var olan tüm ağ güvenlik gruplarını veya Kullanıcı tanımlı yolları güncelleştirin.
    
    HDInsight, çeşitli bağlantı noktaları kullanan birden çok hizmeti barındırır. Bu bağlantı noktalarıyla gelen trafiği engellemez. Sanal Gereç güvenlik duvarları aracılığıyla izin verilecek bağlantı noktalarının listesi için Güvenlik bölümüne bakın.
    
    Mevcut güvenlik yapılandırmanızı bulmak için aşağıdaki Azure PowerShell veya Azure CLı komutlarını kullanın:

    * Ağ güvenlik grupları

        `RESOURCEGROUP`, sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Daha fazla bilgi için bkz. [ağ güvenlik grupları Ile Ilgili sorun giderme](../virtual-network/diagnose-network-traffic-filter-problem.md) belgesi.

        > [!IMPORTANT]  
        > Ağ güvenlik grubu kuralları kural önceliğine göre sırayla uygulanır. Trafik düzeniyle eşleşen ilk kural uygulanır ve bu trafik için başka hiçbir uygulanmaz. En çok izin veren kuralların en az izin veren kuralları. Daha fazla bilgi için bkz. ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/security-overview.md) belgesi.

    * Kullanıcı tanımlı yollar

        `RESOURCEGROUP`, sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Daha fazla bilgi için bkz. [yol sorunlarını giderme](../virtual-network/diagnose-network-routing-problem.md) belgesi.

3. HDInsight kümesi oluşturun ve yapılandırma sırasında Azure sanal ağını seçin. Küme oluşturma işlemini anlamak için aşağıdaki belgelerdeki adımları kullanın:

    * [Azure portalını kullanarak HDInsight oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Azure PowerShell kullanarak HDInsight oluşturma](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Azure klasik CLı kullanarak HDInsight oluşturma](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Azure Resource Manager şablonu kullanarak HDInsight oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > HDInsight 'ı bir sanal ağa eklemek isteğe bağlı bir yapılandırma adımıdır. Kümeyi yapılandırırken sanal ağı seçtiğinizden emin olun.

## <a id="multinet"></a>Birden çok ağı bağlama

Birden çok ağ yapılandırması ile en büyük zorluk, ağlar arasında ad çözümlemesine sahiptir.

Azure, bir sanal ağda yüklü olan Azure hizmetleri için ad çözümlemesi sağlar. Bu yerleşik ad çözümlemesi, HDInsight 'ın tam etki alanı adı (FQDN) kullanarak aşağıdaki kaynaklara bağlanmasına izin verir:

* İnternet 'te kullanılabilir olan herhangi bir kaynak. Örneğin, microsoft.com, windowsupdate.com.

* Kaynağın __Iç DNS adını__ kullanarak aynı Azure sanal ağında olan herhangi bir kaynak. Örneğin, varsayılan ad çözümlemesi kullanıldığında, HDInsight çalışan düğümlerine atanan iç DNS adları örnekleri aşağıda verilmiştir:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Bu düğümler, iç DNS adları kullanarak, birbirleriyle ve HDInsight 'taki diğer düğümlerle doğrudan iletişim kurabilir.

Varsayılan ad çözümlemesi, HDInsight 'ın sanal ağa katılmış ağlardaki kaynakların adlarını çözümlemesine izin __vermez__ . Örneğin, şirket içi ağınızı sanal ağa katmak yaygındır. HDInsight yalnızca varsayılan ad çözümlemesi ile şirket içi ağdaki kaynaklara ada göre erişemez. Tersi de geçerlidir, şirket içi ağınızdaki kaynaklar sanal ağdaki kaynaklara ada göre erişemez.

> [!WARNING]  
> HDInsight kümesini oluşturmadan önce özel DNS sunucusunu oluşturmanız ve sanal ağı bunu kullanacak şekilde yapılandırmanız gerekir.

Birleşik ağlardaki sanal ağ ve kaynaklar arasında ad çözümlemesini etkinleştirmek için aşağıdaki eylemleri gerçekleştirmeniz gerekir:

1. HDInsight 'ı yüklemeyi planladığınız Azure sanal ağında özel bir DNS sunucusu oluşturun.

2. Sanal ağı özel DNS sunucusunu kullanacak şekilde yapılandırın.

3. Sanal ağınız için Azure tarafından atanan DNS sonekini bulun. Bu değer `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`benzerdir. DNS sonekini bulma hakkında daha fazla bilgi için bkz. [özel DNS](hdinsight-create-virtual-network.md#example-dns) bölümü.

4. DNS sunucuları arasında yönlendirmeyi yapılandırın. Yapılandırma, uzak ağın türüne bağlıdır.

   * Uzak ağ bir şirket içi ağ ise, DNS 'yi aşağıdaki gibi yapılandırın:
        
     * __Özel DNS__ (Sanal ağda):

         * Sanal ağın DNS son ekine yönelik istekleri Azure özyinelemeli çözümleyici 'ye (168.63.129.16) iletir. Azure, sanal ağdaki kaynak isteklerini işler

         * Diğer tüm istekleri şirket içi DNS sunucusuna ilet. Şirket içi DNS, diğer tüm ad çözümleme isteklerini, hatta Microsoft.com gibi internet kaynakları için istekleri işler.

     * __Şirket ıçı DNS__: sanal ağ DNS son eki için ISTEKLERI özel DNS sunucusuna iletir. Özel DNS sunucusu daha sonra Azure özyinelemeli çözümleyiciye iletilir.

       Bu yapılandırma, sanal ağın DNS sonekini özel DNS sunucusuna içeren tam etki alanı adları için istekleri yönlendirir. Diğer tüm istekler (genel internet adresleri için bile) Şirket içi DNS sunucusu tarafından işlenir.

   * Uzak ağ başka bir Azure sanal ağı ise, DNS 'yi aşağıdaki gibi yapılandırın:

     * __Özel DNS__ (her sanal ağda):

         * Sanal ağların DNS son ekine yönelik istekler özel DNS sunucularına iletilir. Her bir sanal ağdaki DNS, ağı içindeki kaynakları çözümlemeden sorumludur.

         * Diğer tüm istekleri Azure özyinelemeli çözümleyici 'ye iletin. Özyinelemeli çözümleyici, yerel ve internet kaynaklarını çözümlemeden sorumludur.

       Her ağın DNS sunucusu, DNS son ekine bağlı olarak istekleri birbirlerine iletir. Diğer istekler Azure özyinelemeli çözümleyici kullanılarak çözümlenir.

     Her yapılandırmanın bir örneği için bkz. [özel DNS](hdinsight-create-virtual-network.md#example-dns) bölümü.

Daha fazla bilgi için bkz. [VM 'ler ve rol örnekleri Için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) belgesi.

## <a name="directly-connect-to-apache-hadoop-services"></a>Apache Hadoop hizmetlerine doğrudan bağlanma

`https://CLUSTERNAME.azurehdinsight.net`adresinden kümeye bağlanabilirsiniz. Bu adres, internet 'ten gelen trafiği kısıtlamak için NSG 'ler kullandıysanız ulaşılamaz olabilecek genel bir IP kullanır. Ayrıca, kümeyi bir sanal ağa dağıttığınızda özel uç nokta `https://CLUSTERNAME-int.azurehdinsight.net`kullanarak erişebilirsiniz. Bu uç nokta, küme erişimi için VNet 'in içindeki özel bir IP 'ye çözümlenir.

Apache ambarı ve diğer Web sayfalarına sanal ağ üzerinden bağlanmak için aşağıdaki adımları kullanın:

1. HDInsight küme düğümlerinin iç tam etki alanı adlarını (FQDN) öğrenmek için aşağıdaki yöntemlerden birini kullanın:

    `RESOURCEGROUP`, sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Döndürülen düğüm listesinde, baş düğümlerin FQDN 'sini bulun ve bu, ambarı ve diğer Web hizmetlerine bağlanmak için FQDN 'leri kullanın. Örneğin, `http://<headnode-fqdn>:8080` kullanarak ambarı 'na erişin.

    > [!IMPORTANT]  
    > Baş düğümlerde barındırılan bazı hizmetler tek seferde yalnızca bir düğümde etkindir. Bir baş düğümde bir hizmete erişmeyi denerseniz ve 404 hatası döndürürse, diğer bir baş düğüme geçin.

2. Bir hizmetin kullanılabilir olduğu düğüm ve bağlantı noktasını öğrenmek için bkz. [HDInsight 'Ta Hadoop Hizmetleri tarafından kullanılan bağlantı noktaları](./hdinsight-hadoop-port-settings-for-services.md) .

## <a id="networktraffic"></a>Ağ trafiğini denetleme

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>HDInsight kümelerine gelen ve giden trafiği denetleme teknikleri

Bir Azure sanal ağlarındaki ağ trafiği aşağıdaki yöntemler kullanılarak denetlenebilir:

* **Ağ güvenlik grupları** (NSG) ağa gelen ve giden trafiği filtrelemenize izin verir. Daha fazla bilgi için bkz. ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/security-overview.md) belgesi.

* **Ağ sanal** gereçleri (NVA), yalnızca giden trafikle birlikte kullanılabilir. NVA 'lar güvenlik duvarları ve yönlendiriciler gibi cihazların işlevlerini çoğaltın. Daha fazla bilgi için bkz. [ağ araçları](https://azure.microsoft.com/solutions/network-appliances) belgesi.

Yönetilen bir hizmet olarak, HDInsight, VNET 'ten gelen ve giden trafik için HDInsight sistem durumu ve Yönetim Hizmetleri için sınırsız erişim gerektirir. NSG 'leri kullanırken, bu hizmetlerin HDInsight kümesiyle iletişim kurabildiğinden emin olmanız gerekir.

![Azure özel VNET 'te oluşturulan HDInsight varlıklarının diyagramı](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>Ağ güvenlik gruplarıyla HDInsight

Ağ trafiğini denetlemek için **ağ güvenlik grupları** kullanmayı planlıyorsanız, HDInsight 'ı yüklemeden önce aşağıdaki eylemleri gerçekleştirin:

1. HDInsight için kullanmayı planladığınız Azure bölgesini belirler.

2. HDInsight için gereken IP adreslerini belirler. Daha fazla bilgi için bkz. [HDInsight yönetimi IP adresleri](hdinsight-management-ip-addresses.md).

3. HDInsight 'ı yüklemeyi planladığınız alt ağ için ağ güvenlik grupları oluşturun veya değiştirin.

    * __Ağ güvenlik grupları__: ıp adreslerinden __443__ numaralı bağlantı noktasında __gelen__ trafiğe izin verin. Bu, HDInsight Yönetim Hizmetleri 'nin kümeye sanal ağ dışından ulaşmasını güvence altına alabilir.

Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [ağ güvenlik gruplarına genel bakış](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight kümelerinden giden trafiği denetleme

HDInsight kümelerinden giden trafiği denetleme hakkında daha fazla bilgi için bkz. [Azure HDInsight kümeleri için giden ağ trafiği kısıtlamasını yapılandırma](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premise"></a>Şirket içine zorlamalı tünel oluşturma

Zorlamalı tünel, bir alt ağdan gelen tüm trafiğin, şirket içi ağınız gibi belirli bir ağa veya konuma zorlanarak Kullanıcı tanımlı bir yönlendirme yapılandırmadır. HDInsight, __Şirket__ içi ağlarda trafiğin zorlamalı tünelini desteklemez. 

## <a id="hdinsight-ip"></a>Gerekli IP adresleri

Trafiği denetlemek için ağ güvenlik grupları veya Kullanıcı tanımlı yollar kullanırsanız, lütfen [HDInsight YÖNETIM IP adresleri](hdinsight-management-ip-addresses.md)' ne bakın.
    
## <a id="hdinsight-ports"></a>Gerekli bağlantı noktaları

Bir **güvenlik duvarı** kullanmayı ve kümeye belirli bağlantı noktalarında dışından erişmeyi planlıyorsanız senaryonuz için gereken bağlantı noktalarında trafiğe izin vermeniz gerekebilir. Varsayılan olarak, önceki bölümde açıklanan Azure Yönetim trafiğinin 443 numaralı bağlantı noktasında kümeye erişmesine izin verildiği sürece bağlantı noktalarının özel bir beyaz listesi gerekli değildir.

Belirli hizmetlere yönelik bağlantı noktalarının listesi için bkz. [HDInsight 'ta Apache Hadoop Services tarafından kullanılan bağlantı noktaları](hdinsight-hadoop-port-settings-for-services.md) .

Sanal gereçler güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [Sanal Gereç senaryo](../virtual-network/virtual-network-scenario-udr-gw-nva.md) belgesi.

## <a name="load-balancing"></a>Yük dengeleme

Bir HDInsight kümesi oluşturduğunuzda, bir yük dengeleyici de oluşturulur. Bu yük dengeleyicinin türü, belirli kısıtlamalara sahip olan [temel SKU düzeyinde](../load-balancer/load-balancer-overview.md#skus) bulunur. Bu kısıtlamalardan biri, farklı bölgelerde iki sanal ağınız varsa temel yük dengeleyicilere bağlanamazsınız. Daha fazla bilgi için bkz. [sanal ağlar SSS: genel VNET eşlemesi üzerindeki kısıtlamalar](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

## <a name="next-steps"></a>Sonraki adımlar

* Kod örnekleri ve Azure sanal ağları oluşturma örnekleri için bkz. [Azure HDInsight kümeleri için sanal ağlar oluşturma](hdinsight-create-virtual-network.md).
* HDInsight 'ı şirket içi ağa bağlanacak şekilde yapılandırmaya yönelik uçtan uca bir örnek için bkz. HDInsight 'ı Şirket [içi ağa bağlama](./connect-on-premises-network.md).
* Azure sanal ağlarında Apache HBase kümelerini yapılandırmak için bkz. [Azure sanal ağ 'Da HDInsight 'Ta Apache HBase kümeleri oluşturma](hbase/apache-hbase-provision-vnet.md).
* Apache HBase Coğrafi çoğaltmayı yapılandırmak için bkz. [Azure sanal ağlarında Apache HBase küme çoğaltmasını ayarlama](hbase/apache-hbase-replication.md).
* Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md).
* Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](../virtual-network/security-overview.md).
* Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı rotalar ve IP iletimi](../virtual-network/virtual-networks-udr-overview.md).
