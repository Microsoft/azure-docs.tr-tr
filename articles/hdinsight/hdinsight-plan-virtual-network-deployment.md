---
title: Azure HDInsight için bir sanal ağ planlayın
description: HDInsight 'ı diğer bulut kaynaklarına veya veri merkezinizdeki kaynaklara bağlamak için bir Azure sanal ağ dağıtımını nasıl planlayacağınızı öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: fe974a96b7f349c9d525d0cd0bb01a83ace57a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939280"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Azure HDInsight için bir sanal ağ planlayın

Bu makalede, Azure HDInsight ile [Azure sanal ağları](../virtual-network/virtual-networks-overview.md) (VNet) kullanmayla ilgili arka plan bilgileri sağlanmaktadır. Ayrıca, HDInsight kümeniz için bir sanal ağ uygulayabilmeniz için yapılması gereken tasarım ve uygulama kararlarını da açıklar. Planlama aşaması tamamlandığında, [Azure HDInsight kümeleri için sanal ağlar oluşturmaya](hdinsight-create-virtual-network.md)devam edebilirsiniz. Ağ güvenlik grupları (NSG 'ler) ve Kullanıcı tanımlı yolların düzgün şekilde yapılandırılması için gereken HDInsight yönetim IP adresleri hakkında daha fazla bilgi için bkz. [HDInsight YÖNETIM IP adresleri](hdinsight-management-ip-addresses.md).

Azure sanal ağı kullanmak aşağıdaki senaryolara izin vermez:

* HDInsight 'a doğrudan şirket içi ağdan bağlanma.
* HDInsight 'ı bir Azure sanal ağındaki veri depolarına bağlama.
* Internet üzerinden genel olarak kullanılamayan Apache Hadoop hizmetlere doğrudan erişme. Örneğin, Apache Kafka API 'Ler veya Apache HBase Java API 'SI.

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

    HDInsight, Azure veri merkezi 'nde belirli IP adresleriyle Kısıtlanmamış iletişime sahip olmalıdır. İstemci iletişimine yönelik güvenlik duvarları aracılığıyla izin verilmesi gereken birkaç bağlantı noktası da vardır. Daha fazla bilgi için bkz. [ağ trafiğini denetleme](./control-network-traffic.md).

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Mevcut bir sanal ağa HDInsight ekleme

Mevcut bir Azure sanal ağına nasıl yeni HDInsight ekleneceğini saptamak için bu bölümdeki adımları kullanın.

> [!NOTE]  
> - Mevcut bir HDInsight kümesini sanal bir ağa ekleyemezsiniz.
> - Oluşturulan VNET ve küme aynı abonelikte olmalıdır.

1. Sanal ağ için klasik veya Kaynak Yöneticisi dağıtım modeli mi kullanıyorsunuz?

    HDInsight 3,4 ve üzeri Kaynak Yöneticisi sanal ağ gerektirir. HDInsight 'ın önceki sürümlerinde klasik bir sanal ağ gerekiyordu.

    Var olan ağınız klasik bir sanal ağ ise, bir Kaynak Yöneticisi sanal ağı oluşturmanız ve ardından ikisini bağlamanız gerekir. [Klasik VNET 'leri yeni VNET 'Lere bağlama](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Katıldıktan sonra, Kaynak Yöneticisi ağı 'nda yüklenen HDInsight, klasik ağdaki kaynaklarla etkileşim kurabilir.

2. Trafiği sanal ağın içine veya dışına kısıtlamak için ağ güvenlik grupları, Kullanıcı tanımlı yollar veya sanal ağ gereçlerini kullanıyor musunuz?

    Yönetilen bir hizmet olarak, HDInsight 'ın Azure veri merkezi 'nde birkaç IP adresine sınırsız erişimi olması gerekir. Bu IP adresleriyle iletişime izin vermek için, var olan tüm ağ güvenlik gruplarını veya Kullanıcı tanımlı yolları güncelleştirin.

    HDInsight, çeşitli bağlantı noktaları kullanan birden çok hizmeti barındırır. Bu bağlantı noktalarına giden trafiği engellemez. Sanal Gereç güvenlik duvarları aracılığıyla izin verilecek bağlantı noktalarının listesi için Güvenlik bölümüne bakın.

    Mevcut güvenlik yapılandırmanızı bulmak için aşağıdaki Azure PowerShell veya Azure CLı komutlarını kullanın:

    * Ağ güvenlik grupları

        `RESOURCEGROUP`Öğesini sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Daha fazla bilgi için bkz. [ağ güvenlik grupları Ile Ilgili sorun giderme](../virtual-network/diagnose-network-traffic-filter-problem.md) belgesi.

        > [!IMPORTANT]  
        > Ağ güvenlik grubu kuralları kural önceliğine göre sırayla uygulanır. Trafik düzeniyle eşleşen ilk kural uygulanır ve bu trafik için başka hiçbir uygulanmaz. En çok izin veren kuralların en az izin veren kuralları. Daha fazla bilgi için bkz. ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/network-security-groups-overview.md) belgesi.

    * Kullanıcı tanımlı yollar

        `RESOURCEGROUP`Öğesini sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

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

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Birden çok ağı bağlama

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

3. Sanal ağınız için Azure tarafından atanan DNS sonekini bulun. Bu değer değerine benzerdir `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` . DNS sonekini bulma hakkında daha fazla bilgi için bkz. [özel DNS](hdinsight-create-virtual-network.md#example-dns) bölümü.

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

Üzerinde kümeye bağlanabilirsiniz `https://CLUSTERNAME.azurehdinsight.net` . Bu adres, internet 'ten gelen trafiği kısıtlamak için NSG 'ler kullandıysanız ulaşılamaz olabilecek genel bir IP kullanır. Ayrıca, kümeyi bir sanal ağda dağıttığınızda özel uç nokta kullanarak erişebilirsiniz `https://CLUSTERNAME-int.azurehdinsight.net` . Bu uç nokta, küme erişimi için VNet 'in içindeki özel bir IP 'ye çözümlenir.

Apache ambarı ve diğer Web sayfalarına sanal ağ üzerinden bağlanmak için aşağıdaki adımları kullanın:

1. HDInsight küme düğümlerinin iç tam etki alanı adlarını (FQDN) öğrenmek için aşağıdaki yöntemlerden birini kullanın:

    `RESOURCEGROUP`Öğesini sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

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

    Döndürülen düğüm listesinde, baş düğümlerin FQDN 'sini bulun ve bu, ambarı ve diğer Web hizmetlerine bağlanmak için FQDN 'leri kullanın. Örneğin, `http://<headnode-fqdn>:8080` ambarı 'na erişmek için kullanın.

    > [!IMPORTANT]  
    > Baş düğümlerde barındırılan bazı hizmetler tek seferde yalnızca bir düğümde etkindir. Bir baş düğümde bir hizmete erişmeyi denerseniz ve 404 hatası döndürürse, diğer bir baş düğüme geçin.

2. Bir hizmetin kullanılabilir olduğu düğüm ve bağlantı noktasını öğrenmek için bkz. [HDInsight 'Ta Hadoop Hizmetleri tarafından kullanılan bağlantı noktaları](./hdinsight-hadoop-port-settings-for-services.md) .

## <a name="load-balancing"></a>Yük dengeleme

Bir HDInsight kümesi oluşturduğunuzda, bir yük dengeleyici de oluşturulur. Bu yük dengeleyicinin türü, belirli kısıtlamalara sahip olan [temel SKU düzeyinde](../load-balancer/skus.md)bulunur. Bu kısıtlamalardan biri, farklı bölgelerde iki sanal ağınız varsa temel yük dengeleyicilere bağlanamazsınız. Daha fazla bilgi için bkz. [sanal ağlar SSS: genel VNET eşlemesi üzerindeki kısıtlamalar](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

## <a name="next-steps"></a>Sonraki adımlar

* Kod örnekleri ve Azure sanal ağları oluşturma örnekleri için bkz. [Azure HDInsight kümeleri için sanal ağlar oluşturma](hdinsight-create-virtual-network.md).
* HDInsight 'ı şirket içi ağa bağlanacak şekilde yapılandırmaya yönelik uçtan uca bir örnek için bkz. HDInsight 'ı Şirket [içi ağa bağlama](./connect-on-premises-network.md).
* Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md).
* Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md).
* Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı rotalar ve IP iletimi](../virtual-network/virtual-networks-udr-overview.md).
* Güvenlik Duvarı tümleştirmesi dahil trafiği denetleme hakkında daha fazla bilgi için bkz. [ağ trafiğini denetleme](./control-network-traffic.md).
