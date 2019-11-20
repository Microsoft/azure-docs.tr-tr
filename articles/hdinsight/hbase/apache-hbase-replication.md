---
title: Sanal ağlarda HBase küme çoğaltması-Azure HDInsight
description: Yük Dengeleme, yüksek kullanılabilirlik, sıfır kesinti süresi geçişi ve güncelleştirmeleri ve olağanüstü durum kurtarma için bir HDInsight sürümünden diğerine HBase çoğaltmasını ayarlamayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: 18c7a06e656cbd5c16151381a76ec7725eb2785e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468418"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Azure sanal ağlarında Apache HBase küme çoğaltmasını ayarlama

Bir sanal ağ içinde veya Azure 'da iki sanal ağ arasında [Apache HBase](https://hbase.apache.org/) çoğaltmasını ayarlamayı öğrenin.

Küme çoğaltma, bir kaynak gönderim yöntemi kullanır. Bir HBase kümesi bir kaynak veya hedef olabilir ya da her iki rolü aynı anda yerine getirebilirler. Çoğaltma zaman uyumsuzdur. Çoğaltmanın hedefi nihai tutardır. Kaynak, çoğaltma etkinleştirildiğinde bir sütun ailesine düzenleme aldığında, düzenleme tüm hedef kümelerine yayılır. Veriler bir kümeden diğerine çoğaltıldığında, çoğaltma döngülerini engellemek için kaynak kümesi ve verileri zaten tüketilen tüm kümeler izlenir.

Bu makalede, kaynak hedefi bir çoğaltma ayarlarsınız. Diğer küme topolojileri için bkz. [Apache HBase başvuru kılavuzu](https://hbase.apache.org/book.html#_cluster_replication).

Tek bir sanal ağ için HBase çoğaltma kullanım durumları aşağıda verilmiştir:

* Yük dengeleme. Örneğin, hedef kümede taramalar veya MapReduce işleri çalıştırabilir ve kaynak kümedeki verileri içe aktarabilirsiniz.
* Yüksek kullanılabilirlik ekleme.
* Verileri bir HBase kümesinden diğerine geçirme.
* Bir Azure HDInsight kümesini bir sürümden diğerine yükseltme.

İki sanal ağ için HBase çoğaltma kullanım durumları aşağıda verilmiştir:

* Olağanüstü durum kurtarma ayarlanıyor.
* Yük Dengeleme ve uygulamayı bölümlendirme.
* Yüksek kullanılabilirlik ekleme.

[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication)'dan [betik eylemi](../hdinsight-hadoop-customize-cluster-linux.md) betikleri kullanarak kümeleri çoğaltabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makaleye başlamadan önce bir Azure aboneliğinizin olması gerekir. Bkz. [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Ortamları ayarlama

Üç yapılandırma seçeneğiniz vardır:

- Tek bir Azure sanal ağında iki Apache HBase kümesi.
- Aynı bölgedeki iki farklı sanal ağda bulunan iki Apache HBase kümesi.
- İki farklı bölgede iki farklı sanal ağda iki adet Apache HBase kümesi (coğrafi çoğaltma).

Bu makale, coğrafi çoğaltma senaryosunu ele alır.

Ortamları ayarlamanıza yardımcı olması için bazı [Azure Resource Manager şablonları](../../azure-resource-manager/resource-group-overview.md)oluşturduk. Ortamları diğer yöntemleri kullanarak ayarlamayı tercih ediyorsanız, bkz:

- [HDInsight 'ta Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md)
- [Azure sanal ağı 'nda Apache HBase kümeleri oluşturma](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>İki farklı bölgede iki sanal ağ ayarlama

İki farklı bölgede iki sanal ağ oluşturan bir şablon kullanmak için ve sanal ağlar arasındaki VPN bağlantısı ile, aşağıdaki **Azure 'A dağıt** düğmesini seçin. Şablon tanımı [genel bir BLOB depolama](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)alanında depolanır.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Şablondaki sabit kodlanmış bazı değerler:

**VNet 1**

| Özellik | Değer |
|----------|-------|
| Konum | Batı ABD |
| VNet adı | &lt;ClusterNamePrevix >-vnet1 |
| Adres alanı öneki | 10.1.0.0/16 |
| Alt ağ adı | alt ağ 1 |
| Alt ağ ön eki | 10.1.0.0/24 |
| Alt ağ (ağ geçidi) adı | GatewaySubnet (değiştirilemez) |
| Alt ağ (ağ geçidi) ön eki | 10.1.255.0/27 |
| Ağ geçidi adı | vnet1gw |
| Ağ Geçidi türü | VPN |
| Ağ Geçidi VPN türü | RouteBased |
| Ağ Geçidi SKU 'SU | Temel |
| Ağ geçidi IP 'si | vnet1gwip |

**VNet 2**

| Özellik | Değer |
|----------|-------|
| Konum | Doğu ABD |
| VNet adı | &lt;ClusterNamePrevix >-vnet2 |
| Adres alanı öneki | 10.2.0.0/16 |
| Alt ağ adı | alt ağ 1 |
| Alt ağ ön eki | 10.2.0.0/24 |
| Alt ağ (ağ geçidi) adı | GatewaySubnet (değiştirilemez) |
| Alt ağ (ağ geçidi) ön eki | 10.2.255.0/27 |
| Ağ geçidi adı | vnet2gw |
| Ağ Geçidi türü | VPN |
| Ağ Geçidi VPN türü | RouteBased |
| Ağ Geçidi SKU 'SU | Temel |
| Ağ geçidi IP 'si | vnet1gwip |

## <a name="setup-dns"></a>DNS kurulumu

Son bölümde, şablon iki sanal ağın her birinde bir Ubuntu sanal makinesi oluşturur.  Bu bölümde, iki DNS sanal makinesine bağlama yüklersiniz ve sonra iki sanal makinede DNS iletmeyi yapılandırırsınız.

Bağlama 'yı yüklemek için, iki DNS sanal makinenin genel IP adresini bulmak için yon gerekir.

1. [Azure portalı](https://portal.azure.com) açın.
2. Kaynak grupları ' nı seçerek DNS sanal makinesini açın **> [kaynak grubu adı] > [vnet1DNS]** .  Kaynak grubu adı, son yordamda oluşturduğunuz bir addır. Varsayılan DNS sanal makine adları *vnet1DNS* ve *vnet2NDS*' dir.
3. **Özellikler** ' i seçerek sanal ağın Özellikler sayfasını açın.
4. **Genel IP adresini**yazın ve ayrıca **özel IP adresini**doğrulayın.  Özel IP adresi, vnet2DNS için vnet1DNS ve **10.2.0.4** için **10.1.0.4** olacaktır.  
5. Aşağıdaki adımlarda, her iki sanal ağın DNS sunucularını varsayılan (Azure tarafından sağlanmış) DNS sunucularını kullanarak, paketleri yüklemek için paket ve giden erişime izin verecek şekilde değiştirin.

Bağlama yüklemek için aşağıdaki yordamı kullanın:

1. DNS sanal makinesinin __genel IP adresine__ bağlanmak için SSH kullanın. Aşağıdaki örnek, 40.68.254.142 adresindeki bir sanal makineye bağlanır:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    `sshuser`, DNS sanal makinesini oluştururken belirttiğiniz SSH kullanıcı hesabı ile değiştirin.

    > [!NOTE]  
    > `ssh` yardımcı programını almanın çeşitli yolları vardır. Linux, Unix ve macOS 'ta, işletim sisteminin bir parçası olarak sağlanır. Windows kullanıyorsanız, aşağıdaki seçeneklerden birini göz önünde bulundurun:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Windows 10 ' da Ubuntu 'da Bash](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Bağlama yüklemek için SSH oturumunda aşağıdaki komutları kullanın:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ad çözümleme isteklerini şirket içi DNS sunucunuza iletmek için bağlamayı yapılandırın. Bunu yapmak için, `/etc/bind/named.conf.options` dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > `goodclients` bölümündeki değerleri iki sanal ağın IP adresi aralığıyla değiştirin. Bu bölümde, bu DNS sunucusunun istekleri kabul ettiği adresler tanımlanmaktadır.

    Bu dosyayı düzenlemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Dosyayı kaydetmek için __CTRL + X__, __Y__kullanın ve ardından __girin__.

4. SSH oturumunda aşağıdaki komutu kullanın:

    ```bash
    hostname -f
    ```

    Bu komut aşağıdaki metne benzer bir değer döndürür:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` metin, bu sanal ağ için __DNS son ekidir__ . Bu değeri kaydedin çünkü daha sonra kullanılacaktır.

    DNS son ekini de diğer DNS sunucusundan bulmanız gerekir. Bunu bir sonraki adımda yapmanız gerekir.

5. Sanal ağ içindeki kaynakların DNS adlarını çözümlemek üzere bağlamayı yapılandırmak için, `/etc/bind/named.conf.local` dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net`, diğer sanal ağın DNS son ekine sahip olacak şekilde değiştirmelisiniz. Ve iletici IP 'si, diğer sanal ağdaki DNS sunucusunun özel IP adresidir.

    Bu dosyayı düzenlemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Dosyayı kaydetmek için __CTRL + X__, __Y__kullanın ve ardından __girin__.

6. Bağlamayı başlatmak için aşağıdaki komutu kullanın:

    ```bash
    sudo service bind9 restart
    ```

7. Bağlama, diğer sanal ağdaki kaynakların adlarını çözümleyebildiğini doğrulamak için aşağıdaki komutları kullanın:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net`, diğer ağdaki DNS sanal makinesinin tam etki alanı adı (FQDN) ile değiştirin.
    >
    > `10.2.0.4`, diğer sanal ağdaki özel DNS sunucunuzun __Iç IP adresi__ ile değiştirin.

    Yanıt aşağıdaki metne benzer şekilde görünür:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Bu aşamada, IP adresini, belirtilen DNS sunucusu IP adresi olmadan diğer ağdan arama yapılamaz.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Sanal ağı özel DNS sunucusunu kullanacak şekilde yapılandırma

Sanal ağı, Azure özyinelemeli çözümleyici yerine özel DNS sunucusu kullanacak şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com)sanal ağı seçin ve __DNS sunucuları__' nı seçin.

2. __Özel__' i seçin ve özel DNS sunucusunun __iç IP adresini__ girin. Son olarak __Kaydet__' i seçin.

6. Vnet1 ' de DNS sunucusu sanal makinesini açın ve **Yeniden Başlat**' a tıklayın.  DNS yapılandırmasının etkili olması için sanal ağdaki tüm sanal makineleri yeniden başlatmanız gerekir.
7. Vnet2 için özel DNS sunucusunu yapılandırma adımlarını yineleyin.

DNS yapılandırmasını test etmek için SSH kullanarak iki DNS sanal makinesine bağlanabilir ve diğer sanal ağın DNS sunucusuna ana bilgisayar adını kullanarak ping gönderebilirsiniz. İşe yaramazsa, DNS durumunu denetlemek için aşağıdaki komutu kullanın:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase kümeleri oluşturma

Aşağıdaki yapılandırmaya sahip iki sanal ağın her birinde bir [Apache HBase](https://hbase.apache.org/) kümesi oluşturun:

- **Kaynak grubu adı**: sanal ağları oluşturduğunuz kaynak grubu adını kullanın.
- **Küme türü**: HBase
- **Sürüm**: HBase 1.1.2 (hdı 3,6)
- **Konum**: sanal ağla aynı konumu kullanın.  Varsayılan olarak, vnet1 *Batı ABD*ve vnet2 *Doğu ABD*.
- **Depolama**: küme için yeni bir depolama hesabı oluşturun.
- **Sanal ağ** (portaldaki gelişmiş ayarlardan): son yordamda oluşturduğunuz vnet1 seçin.
- **Alt ağ**: şablonda kullanılan varsayılan ad **subnet1**' dir.

Ortamın doğru yapılandırıldığından emin olmak için, iki küme arasında headnode 'un FQDN 'sine ping atabilmelisiniz.

## <a name="load-test-data"></a>Test verilerini yükleme

Bir kümeyi çoğalttığınızda, çoğaltmak istediğiniz tabloları belirtmeniz gerekir. Bu bölümde, bazı verileri kaynak kümesine yüklersiniz. Sonraki bölümde, iki küme arasında çoğaltmayı etkinleştirecektir.

Bir **kişi** tablosu oluşturmak ve tabloya bazı veriler eklemek Için [Apache HBase öğreticisi: HDInsight 'Ta Apache HBase 'i kullanmaya başlama](apache-hbase-tutorial-get-started-linux.md)yönergelerini izleyin.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Aşağıdaki adımlarda Azure portal betik eylemi betiğinin nasıl çağrılacağını açıklamaktadır. Azure PowerShell ve Azure klasik CLı kullanarak betik eylemi çalıştırma hakkında bilgi için bkz. [betik eylemi kullanarak HDInsight kümelerini özelleştirme](../hdinsight-hadoop-customize-cluster-linux.md).

**Azure portal HBase çoğaltmasını etkinleştirmek için**

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Kaynak HBase kümesini açın.
3. Küme menüsünde **betik eylemleri**' ni seçin.
4. Sayfanın üst kısmında **Yeni Gönder**' i seçin.
5. Aşağıdaki bilgileri seçin veya girin:

   1. **Ad**: **çoğaltmayı etkinleştir**' i girin.
   2. **Bash betik URL 'si**: **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** girin.
   3. **Head**: bunun seçili olduğundan emin olun. Diğer düğüm türlerini temizleyin.
   4. **Parametreler**: Aşağıdaki örnek parametreler, tüm mevcut tablolar için çoğaltmayı etkinleştirir ve sonra kaynak kümeden tüm verileri hedef kümeye kopyalar:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Kaynak ve hedef küme DNS adı için FQDN yerine ana bilgisayar adı kullanın.

6. **Oluştur**'u seçin. Özellikle **-CopyData** bağımsız değişkenini kullandığınızda betiğin çalıştırılması biraz zaman alabilir.

Gerekli bağımsız değişkenler:

|Ad|Açıklama|
|----|-----------|
|-s,--src-Cluster | Kaynak HBase kümesinin DNS adını belirtir. Örneğin:-s hbsrccluster,--src-Cluster = hbsrccluster |
|-d,--DST-Cluster | Hedef (çoğaltma) HBase kümesinin DNS adını belirtir. Örneğin:-s dsthbcluster,--src-Cluster = dsthbcluster |
|-SP,--src-ambarı-parola | Kaynak HBase kümesindeki ambarı için yönetici parolasını belirtir. |
|-DP,--DST-ambarı-parola | Hedef HBase kümesindeki ambarı için yönetici parolasını belirtir.|

İsteğe bağlı bağımsız değişkenler:

|Ad|Açıklama|
|----|-----------|
|-su,--src-ambarı-Kullanıcı | Kaynak HBase kümesindeki ambarı için yönetici kullanıcı adını belirtir. Varsayılan değer **admin**' dir. |
|-du,--DST-ambarı-Kullanıcı | Hedef HBase kümesindeki ambarı için yönetici kullanıcı adını belirtir. Varsayılan değer **admin**' dir. |
|-t,--Table-List | Çoğaltılacak tabloları belirtir. Örneğin:--Table-List = "Table1; Table2; TABLE3". Tablo belirtmezseniz, var olan tüm HBase tabloları çoğaltılır.|
|-ı,--makine | Betik eyleminin çalıştığı baş düğümü belirtir. Değer **hn0** veya **hn1** olur ve etkin baş düğüm olan temel alınarak seçilmelidir. HDInsight portalından veya Azure PowerShell betik eylemi olarak $0 betiğini çalıştırırken bu seçeneği kullanın.|
|-CP,-CopyData | Çoğaltmanın etkinleştirildiği tablolardaki mevcut verilerin geçişine izin vermez. |
|-RPM,-Çoğalt-Phoenix-meta | Phoenix sistem tablolarında çoğaltmayı mümkün. <br><br>*Bu seçeneği dikkatli kullanın.* Bu betiği kullanmadan önce, çoğaltma kümelerinde Phoenix tablolarını yeniden oluşturmanızı öneririz. |
|-h,--yardım | Kullanım bilgilerini görüntüler. |

[Betiğin](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) `print_usage()` bölümünde parametrelerin ayrıntılı bir açıklaması vardır.

Betik eylemi başarıyla dağıtıldıktan sonra, hedef HBase kümesine bağlanmak için SSH 'yi kullanabilir ve sonra verilerin çoğaltıldığından emin olabilirsiniz.

### <a name="replication-scenarios"></a>Çoğaltma senaryoları

Aşağıdaki listede bazı genel kullanım durumları ve bunların parametre ayarları gösterilmektedir:

- **İki küme arasındaki tüm tablolarda çoğaltmayı etkinleştirin**. Bu senaryo, tablolardaki mevcut verilerin kopyalanmasını veya geçirilmesini gerektirmez ve Phoenix tabloları kullanmaz. Aşağıdaki parametreleri kullanın:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Belirli tablolarda çoğaltmayı etkinleştirin**. Table1, Table2 ve TABLE3 üzerinde çoğaltmayı etkinleştirmek için aşağıdaki parametreleri kullanın:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Belirli tablolarda çoğaltmayı etkinleştirin ve var olan verileri kopyalayın**. Table1, Table2 ve TABLE3 üzerinde çoğaltmayı etkinleştirmek için aşağıdaki parametreleri kullanın:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Tüm tablolarda çoğaltmayı etkinleştirin ve Phoenix meta verilerini kaynaktan hedefe çoğaltın**. Phoenix meta veri çoğaltması kusursuz değil. Dikkatli olun. Aşağıdaki parametreleri kullanın:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Verileri kopyalama ve geçirme

Çoğaltma etkinleştirildikten sonra verileri kopyalamak veya taşımak için kullanılabilecek iki ayrı betik eylem betiği vardır:

- [Küçük tablolar Için komut dosyası](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (boyut olarak birkaç gigabayt ve genel kopyanın bir saatten kısa bir süre içinde tamamlanmasının bekleniyor)

- [Büyük tablolar Için komut dosyası](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (kopyalamanın bir saatten uzun sürmemesi beklenen tabloların)

Betik eylemini çağırmak için [çoğaltmayı etkinleştirme](#enable-replication) bölümünde açıklanan yordamın aynısını izleyebilirsiniz. Aşağıdaki parametreleri kullanın:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[Betiğin](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) `print_usage()` bölümünde parametrelerin ayrıntılı bir açıklaması vardır.

### <a name="scenarios"></a>Senaryolar

- **Belirli tabloları (test1, test2 ve test3) Şu anda düzenlenen tüm satırlar Için Kopyala (geçerli zaman damgası)** :

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  veya

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Belirli bir zaman aralığına sahip belirli tabloları Kopyala**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Çoğaltmayı devre dışı bırakma

Çoğaltmayı devre dışı bırakmak için [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)'dan başka bir betik eylem betiği kullanın. Betik eylemini çağırmak için [çoğaltmayı etkinleştirme](#enable-replication) bölümünde açıklanan yordamın aynısını izleyebilirsiniz. Aşağıdaki parametreleri kullanın:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

[Betiğin](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) `print_usage()` bölümünde parametrelerin ayrıntılı bir açıklaması vardır.

### <a name="scenarios"></a>Senaryolar

- **Tüm tablolardaki çoğaltmayı devre dışı bırak**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  or

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Belirtilen tablolarda çoğaltmayı devre dışı bırak (Table1, Table2 ve TABLE3)** :

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir sanal ağ içinde veya iki sanal ağ arasında Apache HBase çoğaltmasını ayarlamayı öğrendiniz. HDInsight ve Apache HBase hakkında daha fazla bilgi edinmek için şu makalelere bakın:

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight Apache HBase genel bakış](./apache-hbase-overview.md)
* [Azure sanal ağı 'nda Apache HBase kümeleri oluşturma](./apache-hbase-provision-vnet.md)

