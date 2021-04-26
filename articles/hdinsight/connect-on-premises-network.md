---
title: Azure HDInsight 'ı şirket içi ağınıza bağlama
description: Bir Azure sanal ağında HDInsight kümesi oluşturmayı ve ardından Şirket içi ağınıza bağlamayı öğrenin. Özel bir DNS sunucusu kullanarak HDInsight ve şirket içi ağınız arasında ad çözümlemesini nasıl yapılandıracağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: f26813176d4286a052772d2096427231759aacc2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863386"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>HDInsight’ı şirket içi ağınıza bağlama

Azure sanal ağları ve bir VPN ağ geçidi kullanarak HDInsight 'ı şirket içi ağınıza bağlamayı öğrenin. Bu belge, üzerine planlama bilgileri sağlar:

* HDInsight kullanarak şirket içi ağınıza bağlanan bir Azure sanal ağı.
* Sanal ağ ile şirket içi ağınız arasında DNS ad çözümlemesi yapılandırma.
* HDInsight 'a internet erişimini kısıtlamak için ağ güvenlik gruplarını yapılandırma.
* HDInsight tarafından sanal ağ üzerinde sunulan bağlantı noktaları.

## <a name="overview"></a>Genel Bakış

Birleştirilmiş ağdaki HDInsight ve kaynakların ada göre iletişim kurmasına izin vermek için aşağıdaki eylemleri gerçekleştirmeniz gerekir:

1. Azure sanal ağını oluşturun.
1. Azure sanal ağında özel bir DNS sunucusu oluşturun.
1. Sanal ağı varsayılan Azure özyinelemeli çözümleyici yerine özel DNS sunucusu kullanacak şekilde yapılandırın.
1. Özel DNS sunucusu ve şirket içi DNS sunucunuz arasında iletmeyi yapılandırın.

Bu yapılandırma aşağıdaki davranışı etkinleştirir:

* __Sanal ağ IÇIN__ DNS sonekine sahip olan tam etki alanı adları ISTEKLERI özel DNS sunucusuna iletilir. Özel DNS sunucusu daha sonra bu istekleri, IP adresini döndüren Azure özyinelemeli çözümleyici 'ye iletir.
* Diğer tüm istekler şirket içi DNS sunucusuna iletilir. Microsoft.com gibi genel internet kaynaklarına yönelik istekler bile ad çözümlemesi için şirket içi DNS sunucusuna iletilir.

Aşağıdaki diyagramda yeşil çizgiler, sanal ağın DNS son ekine biten kaynaklar için isteklerdir. Mavi çizgiler, şirket içi ağdaki veya genel İnternet 'teki kaynaklara yönelik isteklerdir.

:::image type="content" source="./media/connect-on-premises-network/on-premises-to-cloud-dns.png" alt-text="Yapılandırmada DNS isteklerinin nasıl çözümlendiğini gösteren diyagram" border="false":::

## <a name="prerequisites"></a>Önkoşullar

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](./hdinsight-hadoop-linux-use-ssh-unix.md).
* PowerShell kullanıyorsanız [az Module](/powershell/azure/)gerekecektir.
* Azure CLı 'yi kullanmak istiyorsanız ve henüz yüklemediyseniz, bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Sanal ağ yapılandırması oluştur

Şirket içi ağınıza bağlı bir Azure sanal ağı oluşturmayı öğrenmek için aşağıdaki belgeleri kullanın:

* [Azure portalını kullanma](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Azure PowerShell’i kullanma](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Azure CLI’yı kullanma](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Özel DNS sunucusu oluştur

> [!IMPORTANT]  
> HDInsight 'ı sanal ağa yüklemeden önce DNS sunucusunu oluşturmanız ve yapılandırmanız gerekir.

Bu adımlar, Azure sanal makinesi oluşturmak için [Azure Portal](https://portal.azure.com) kullanır. Sanal makine oluşturmanın diğer yolları için bkz. [VM oluşturma-Azure CLI](../virtual-machines/linux/quick-create-cli.md) ve [vm oluşturma-Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  [BIND](https://www.isc.org/downloads/bind/) DNS yazılımını kullanan bir Linux sanal makinesi oluşturmak için aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
  
1. Üstteki menüden **+ kaynak oluştur**' u seçin.

    :::image type="content" source="./media/connect-on-premises-network/azure-portal-create-resource.png" alt-text="Ubuntu sanal makinesi oluşturma":::

1.   >  **Sanal makine oluşturma** sayfasına gitmek için işlem **sanal makinesini** seçin.

1. __Temel bilgiler__ sekmesinde, aşağıdaki bilgileri girin:  
  
    | Alan | Değer |
    | --- | --- |
    |Abonelik |Uygun aboneliğinizi seçin.|
    |Kaynak grubu |Daha önce oluşturulan sanal ağı içeren kaynak grubunu seçin.|
    |Sanal makine adı | Bu sanal makineyi tanımlayan kolay bir ad girin. Bu örnek **dnsproxy** kullanır.|
    |Region | Daha önce oluşturulan sanal ağla aynı bölgeyi seçin.  Tüm bölgelerde tüm VM boyutları kullanılamaz.  |
    |Kullanılabilirlik seçenekleri |  İstediğiniz kullanılabilirlik düzeyini seçin.  Azure, uygulamalarınız için kullanılabilirliği ve dayanıklılığı yönetmeye yönelik çeşitli seçenekler sunar.  Uygulamalarınızı ve verilerinizi veri merkezi kesintilerine ve bakım olaylarına karşı korumak için Kullanılabilirlik Alanları veya kullanılabilirlik kümelerinde çoğaltılan VM 'Leri kullanmak için çözümünüzü mimarın. Bu örnek, **gerekli altyapı yedekliliği** kullanmaz. |
    |Görüntü | **Ubuntu Server 18,04 LTS** adresinden ayrılın. |
    |Kimlik doğrulaması türü | __Parola__ veya __SSH ortak anahtarı__: SSH hesabı için kimlik doğrulama yöntemi. Daha güvenli olduklarından ortak anahtarların kullanılmasını öneririz. Bu örnek, **parolayı** kullanır.  Daha fazla bilgi için bkz. [Linux VM 'leri IÇIN SSH anahtarları oluşturma ve kullanma](../virtual-machines/linux/mac-create-ssh-keys.md) belgesi.|
    |Kullanıcı adı |VM için yönetici kullanıcı adını girin.  Bu örnek **sshuser** kullanır.|
    |Parola veya SSH ortak anahtarı | Kullanılabilir alan, **kimlik doğrulama türü** için seçtiğiniz seçeneğe göre belirlenir.  Uygun değeri girin.|
    |Genel gelen bağlantı noktaları|**Seçili bağlantı noktalarına Izin ver**' i seçin. Ardından **gelen bağlantı noktaları seçin** açılır listesinden **SSH (22)** öğesini seçin.|

    :::image type="content" source="./media/connect-on-premises-network/virtual-machine-basics.png" alt-text="Sanal makine temel yapılandırması":::

    Diğer girişleri varsayılan değerlerde bırakın ve sonra **ağ** sekmesini seçin.

4. **Ağ** sekmesinde, aşağıdaki bilgileri girin:

    | Alan | Değer |
    | --- | --- |
    |Sanal ağ | Daha önce oluşturduğunuz sanal ağı seçin.|
    |Alt ağ | Daha önce oluşturduğunuz sanal ağ için varsayılan alt ağı seçin. VPN ağ geçidi tarafından kullanılan alt __ağı seçmeyin.__|
    |Genel IP | Oto doldurulmuş değeri kullanın.  |

    :::image type="content" source="./media/connect-on-premises-network/virtual-network-settings.png" alt-text="HDInsight sanal ağ ayarları":::

    Diğer girişleri varsayılan değerlerde bırakın ve ardından **gözden geçir + oluştur**' u seçin.

5. **Gözden geçir + oluştur** sekmesinden **Oluştur** ' u seçerek sanal makineyi oluşturun.

### <a name="review-ip-addresses"></a>IP adreslerini gözden geçirme

Sanal makine oluşturulduktan sonra **Kaynağa Git** düğmesine sahip bir **dağıtım başarılı** bildirimi alırsınız.  Yeni sanal makinenize gitmek için **Kaynağa Git** ' i seçin.  Yeni sanal makineniz için varsayılan görünümden, ilişkili IP adreslerini belirlemek için aşağıdaki adımları izleyin:

1. **Ayarlar**' dan **Özellikler**' i seçin.

2. Daha sonra kullanmak üzere **genel IP adresi/DNS ad etiketi** ve **özel IP adresi** değerlerini aklınızda yapın.

   :::image type="content" source="./media/connect-on-premises-network/virtual-machine-ip-addresses.png" alt-text="Ortak ve özel IP adresleri":::

### <a name="install-and-configure-bind-dns-software"></a>Bağlama (DNS yazılımı) yükleyip yapılandırma

1. Sanal makinenin __genel IP adresine__ bağlanmak için SSH kullanın. `sshuser`VM oluştururken BELIRTTIĞINIZ SSH kullanıcı hesabı ile değiştirin. Aşağıdaki örnek, 40.68.254.142 adresindeki bir sanal makineye bağlanır:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Bağlama yüklemek için SSH oturumunda aşağıdaki komutları kullanın:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ad çözümleme isteklerini şirket içi DNS sunucunuza iletecek şekilde bağlamayı yapılandırmak için aşağıdaki metni dosyanın içeriğiyle kullanın `/etc/bind/named.conf.options` :

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Bölümündeki değerleri, `goodclients` sanal ağın ve şirket içi AĞıN IP adresi aralığıyla değiştirin. Bu bölümde, bu DNS sunucusunun istekleri kabul ettiği adresler tanımlanmaktadır.
    >
    > `192.168.0.1`Bölümündeki girişi, Şirket `forwarders` içi DNS sunucunuzun IP adresi ile değiştirin. Bu giriş, çözümleme için DNS isteklerini şirket içi DNS sunucunuza yönlendirir.

    Bu dosyayı düzenlemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Dosyayı kaydetmek için __CTRL + X__, __Y__ kullanın ve ardından __girin__.

4. SSH oturumunda aşağıdaki komutu kullanın:

    ```bash
    hostname -f
    ```

    Bu komut aşağıdaki metne benzer bir değer döndürür:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Metin, bu sanal ağ Için __DNS son ekidir__ . Daha sonra kullanıldığından bu değeri kaydedin.

5. Sanal ağ içindeki kaynakların DNS adlarını çözümlemek üzere bağlamayı yapılandırmak için, dosyanın içeriği olarak aşağıdaki metni kullanın `/etc/bind/named.conf.local` :

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Öğesini `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` daha önce ALDıĞıNıZ DNS son eki ile değiştirmelisiniz.

    Bu dosyayı düzenlemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Dosyayı kaydetmek için __CTRL + X__, __Y__ kullanın ve ardından __girin__.

6. Bağlamayı başlatmak için aşağıdaki komutu kullanın:

    ```bash
    sudo service bind9 restart
    ```

7. Bağlama 'nın şirket içi ağınızdaki kaynakların adlarını çözümleyebildiğini doğrulamak için aşağıdaki komutları kullanın:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Şirket `dns.mynetwork.net` içi ağınızdaki bir kaynağın tam etki alanı adı (FQDN) ile değiştirin.
    >
    > `10.0.0.4`Sanal ağdaki özel DNS sunucunuzun __iç IP adresi__ ile değiştirin.

    Yanıt aşağıdaki metne benzer şekilde görünür:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Sanal ağı özel DNS sunucusunu kullanacak şekilde yapılandırma

Sanal ağı Azure özyinelemeli çözümleyici yerine özel DNS sunucusu kullanacak şekilde yapılandırmak için, [Azure Portal](https://portal.azure.com)aşağıdaki adımları kullanın:

1. Sol menüden **tüm hizmetler**  >  **ağ**  >  **sanal ağlarına** gidin.

2. Sanal ağınız için varsayılan görünümü açacak olan listeden Sanal ağınızı seçin.  

3. Varsayılan görünümden **Ayarlar** altında, **DNS sunucuları**' nı seçin.  

4. __Özel__' i seçin ve özel DNS sunucusunun **özel IP adresini** girin.

5. __Kaydet__’i seçin.  <br />  

    :::image type="content" source="./media/connect-on-premises-network/configure-custom-dns.png" alt-text="Ağ için özel DNS sunucusunu ayarlama":::

## <a name="configure-on-premises-dns-server"></a>Şirket içi DNS sunucusunu yapılandırma

Önceki bölümde, istekleri şirket içi DNS sunucusuna iletmek için özel DNS sunucusunu yapılandırdınız. Ardından, şirket içi DNS sunucusunu istekleri özel DNS sunucusuna iletecek şekilde yapılandırmanız gerekir.

DNS sunucunuzu yapılandırma hakkında belirli adımlar için, DNS sunucusu yazılımınız için belgelere başvurun. __Koşullu ileticinin__ nasıl yapılandırılacağı hakkında adımları arayın.

Koşullu iletme yalnızca belirli bir DNS son eki için istekleri iletir. Bu durumda, sanal ağın DNS son eki için bir iletici yapılandırmanız gerekir. Bu sonekin istekleri özel DNS sunucusunun IP adresine iletilmelidir. 

Aşağıdaki metin, **BIND** DNS yazılımının koşullu iletici yapılandırmasına bir örnektir:

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

**Windows Server 2016**' de DNS kullanma hakkında bilgi için bkz. [Add-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) belgeleri...

Şirket içi DNS sunucusunu yapılandırdıktan sonra, `nslookup` sanal ağdaki adları çözebildiğinizi doğrulamak için şirket içi ağdan ' ı kullanabilirsiniz. Aşağıdaki örnek 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Bu örnek, özel DNS sunucusunun adını çözümlemek için 196.168.0.4 adresindeki şirket içi DNS sunucusunu kullanır. IP adresini, şirket içi DNS sunucusu için bir ile değiştirin. `dnsproxy`Adresi özel DNS sunucusunun tam etki alanı adıyla değiştirin.

## <a name="optional-control-network-traffic"></a>İsteğe bağlı: ağ trafiğini denetleme

Ağ trafiğini denetlemek için ağ güvenlik grupları (NSG) veya Kullanıcı tanımlı yollar (UDR) kullanabilirsiniz. NSG 'ler gelen ve giden trafiği filtrelemenize ve trafiğe izin verebilir veya bu trafiği reddetmenize olanak tanır. UDRs, trafiğin sanal ağ, internet ve şirket içi ağ arasındaki kaynaklar arasında nasıl akacağını denetlemenize olanak tanır.

> [!WARNING]  
> HDInsight, Azure buluttaki belirli IP adreslerinden gelen erişimi ve sınırsız giden erişimi gerektirir. Trafiği denetlemek için NSG 'ler veya UDRs kullanırken aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Sanal ağınızı içeren konumun IP adreslerini bulun. Konuma göre gereken IP 'lerin bir listesi için, bkz. [gereklı IP adresleri](./hdinsight-management-ip-addresses.md).

2. 1. adımda belirtilen IP adresleri için IP adreslerinden gelen trafiğe izin verin.

   * __NSG__ kullanıyorsanız: bağlantı noktası __443__ üzerinde IP adresleri için __gelen__ trafiğe izin ver.
   * __UDR__ KULLANıYORSANıZ: IP adresleri Için yolun __sonraki atlama__ türünü __Internet__ 'e ayarlayın.

NSG 'ler oluşturmak için Azure PowerShell veya Azure CLı kullanma hakkında bir örnek için bkz. [HDInsight 'ı Azure sanal ağları Ile genişletme](hdinsight-create-virtual-network.md#hdinsight-nsg) belgesi.

## <a name="create-the-hdinsight-cluster"></a>HDInsight kümesi oluşturma

> [!WARNING]  
> HDInsight 'ı sanal ağa yüklemeden önce özel DNS sunucusunu yapılandırmanız gerekir.

HDInsight kümesi oluşturmak için [Azure Portal belgelerini kullanarak HDInsight kümesi oluşturma](./hdinsight-hadoop-create-linux-clusters-portal.md) bölümündeki adımları kullanın.

> [!WARNING]  
> * Küme oluşturma sırasında, Sanal ağınızı içeren konumu seçmeniz gerekir.
> * Yapılandırmanın __Gelişmiş ayarlar__ bölümünde, daha önce oluşturduğunuz sanal ağı ve alt ağı seçmeniz gerekir.

## <a name="connecting-to-hdinsight"></a>HDInsight 'a bağlanma

HDInsight üzerinde birçok belge, Internet üzerinden kümeye erişiminizin olduğunu varsayar. Örneğin, `https://CLUSTERNAME.azurehdinsight.net` konumundaki kümeye bağlanabildiğiniz kabul edilir. Bu adres, internet 'ten erişimi kısıtlamak için NSG 'ler veya UDRs kullandıysanız kullanılamayan genel ağ geçidini kullanır.

Bazı belgeler `headnodehost` , BIR SSH oturumundan kümeye bağlanırken de başvuru sağlar. Bu adres yalnızca bir küme içindeki düğümlerden kullanılabilir ve sanal ağ üzerinden bağlı istemcilerde kullanılamaz.

Sanal ağ aracılığıyla HDInsight 'a doğrudan bağlanmak için aşağıdaki adımları kullanın:

1. HDInsight küme düğümlerinin iç tam etki alanı adlarını öğrenmek için aşağıdaki yöntemlerden birini kullanın:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Bir hizmetin kullanılabilir olduğu bağlantı noktasını öğrenmek için [HDInsight belgesinde Apache Hadoop Hizmetleri tarafından kullanılan bağlantı noktalarına](./hdinsight-hadoop-port-settings-for-services.md) bakın.

    > [!IMPORTANT]  
    > Baş düğümlerde barındırılan bazı hizmetler tek seferde yalnızca bir düğümde etkindir. Bir baş düğümde bir hizmete erişmeyi denerseniz ve başarısız olursa, diğer baş düğüme geçin.
    >
    > Örneğin, Apache ambarı tek seferde yalnızca bir baş düğümde etkindir. Tek bir baş düğümde ambarı 'na erişmeyi denerseniz ve 404 hatası döndürürse, diğer baş düğümde çalışır.

## <a name="next-steps"></a>Sonraki adımlar

* Bir sanal ağda HDInsight kullanma hakkında daha fazla bilgi için bkz. [Azure HDInsight kümeleri için sanal ağ dağıtımı planlaması](./hdinsight-plan-virtual-network-deployment.md).

* Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md).

* Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md).

* Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı rotalar ve IP iletimi](../virtual-network/virtual-networks-udr-overview.md).