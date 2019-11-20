---
title: Sanal ağda HBase kümeleri oluşturma-Azure
description: Azure HDInsight 'ta HBase 'i kullanmaya başlayın. Azure sanal ağında HDInsight HBase kümeleri oluşturmayı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 60a7afb6e610294ccaa535eaa7371ff8d5015db3
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077198"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Azure sanal ağı 'nda HDInsight 'ta Apache HBase kümeleri oluşturma
Azure [sanal ağında][1]Azure HDInsight Apache HBase kümeleri oluşturmayı öğrenin.

Sanal ağ tümleştirmesiyle, uygulamaların HBase ile doğrudan iletişim kurabilmesi için Apache HBase kümeleri, uygulamalarınızla aynı sanal ağa dağıtılabilir. Avantajları şunlardır:

* HBase Java uzak yordam çağrısı (RPC) API 'Leri aracılığıyla iletişim sağlayan HBase kümesinin düğümlerine doğrudan Web uygulamasının bağlantısı.
* Trafiğinizi birden çok ağ geçidi ve yük dengeleyicileri üzerinden hareket ederek iyileştirilmiş performans.
* Gizli bilgileri genel bir uç nokta görüntülenmeden daha güvenli bir şekilde işleme özelliği.

### <a name="prerequisites"></a>Önkoşullar
Bu makaleye başlamadan önce aşağıdaki öğelere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure PowerShell içeren bir iş istasyonu**. Bkz. [Azure PowerShell yükleyip kullanma](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Sanal ağa Apache HBase kümesi oluşturma
Bu bölümde, bir Azure sanal ağında [Azure Resource Manager şablonu](../../azure-resource-manager/resource-group-template-deploy.md)kullanarak, bağımlı Azure depolama hesabı ile Linux tabanlı bir Apache HBase kümesi oluşturacaksınız. Diğer küme oluşturma yöntemleri ve ayarları anlamak için bkz. [HDInsight kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md). HDInsight 'ta Apache Hadoop kümeleri oluşturmak için şablon kullanma hakkında daha fazla bilgi için, bkz. [Azure Resource Manager şablonları kullanarak HDInsight 'ta Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Bazı özellikler şablona sabit olarak kodlanmıştır. Örneğin:
>
> * **Konum**: Doğu ABD 2
> * **Küme sürümü**: 3.6
> * **Küme çalışan düğümü sayısı**: 2
> * **Varsayılan depolama hesabı**: benzersiz bir dize
> * **Sanal ağ adı**: &lt;Küme adı >-VNET
> * **Sanal ağ adres alanı**: 10.0.0.0/16
> * **Alt ağ adı**: subnet1
> * **Alt ağ adres aralığı**: 10.0.0.0/24
>
> &lt;> Küme adı, şablonu kullanırken sağladığınız küme adı ile değiştirilmiştir.

1. Azure Portal'da bir şablonu açmak için aşağıdaki görüntüye tıklayın. Şablon, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)bulunur.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. **Özel dağıtım** dikey penceresinde aşağıdaki özellikleri girin:

   * **Abonelik**: HDInsight kümesi, bağımlı depolama hesabı ve Azure sanal ağı oluşturmak için kullanılan bir Azure aboneliğini seçin.
   * **Kaynak grubu**: **Yeni oluştur**' u seçin ve yeni bir kaynak grubu adı belirtin.
   * **Konum**: Kaynak grubu için bir konum seçin.
   * **Clustername**: Oluşturulacak Hadoop kümesi için bir ad girin.
   * **Küme oturum açma adı ve parolası**: Varsayılan oturum açma adı **admin**’dir.
   * **SSH Kullanıcı adı ve parolası**: Varsayılan kullanıcı adı **sshuser** şeklindedir.  Bunu yeniden adlandırabilirsiniz.
   * **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**: Seçin
3. **Satın al**’a tıklayın. Bir küme oluşturmak yaklaşık 20 dakika sürer. Küme oluşturulduktan sonra, portalı açmak için portalda küme dikey penceresine tıklayabilirsiniz.

Makaleyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır. Bir kümeyi silme yönergeleri için [Azure Portal kullanarak HDInsight 'ta Apache Hadoop kümelerini yönetme](../hdinsight-administer-use-portal-linux.md#delete-clusters)konusuna bakın.

Yeni HBase kümeniz ile çalışmaya başlamak için [HDInsight 'ta Apache Hadoop Ile Apache HBase 'i kullanmaya başlama](./apache-hbase-tutorial-get-started-linux.md)bölümünde bulunan yordamları kullanabilirsiniz.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Apache HBase Java RPC API 'Lerini kullanarak Apache HBase kümesine bağlanma
1. Aynı Azure sanal ağına ve aynı alt ağa hizmet olarak altyapı (IaaS) sanal makinesi oluşturun. Yeni bir IaaS sanal makinesi oluşturma yönergeleri için bkz. [Windows Server çalıştıran bir sanal makine oluşturma](../../virtual-machines/windows/quick-create-portal.md). Bu belgedeki adımları izleyerek, ağ yapılandırması için aşağıdaki değerleri kullanmanız gerekir:

   * **Sanal ağ**: &lt;Küme adı >-VNET
   * **Alt ağ**: subnet1

   > [!IMPORTANT]  
   > Küme &lt;adı >, önceki adımlarda HDInsight kümesini oluştururken kullandığınız adla değiştirin.

   Bu değerleri kullanarak, sanal makine HDInsight kümesiyle aynı sanal ağa ve alt ağa yerleştirilir. Bu yapılandırma, bunların birbirleriyle doğrudan iletişim kurmasına olanak tanır. Boş bir Edge düğümüyle HDInsight kümesi oluşturmanın bir yolu vardır. Uç düğümü, kümeyi yönetmek için kullanılabilir.  Daha fazla bilgi için bkz. [HDInsight 'ta boş kenar düğümlerini kullanma](../hdinsight-apps-use-edge-node.md).

2. Bir Java uygulamasını kullanarak HBase 'e uzaktan bağlanmak için tam etki alanı adını (FQDN) kullanmanız gerekir. Bunu öğrenmek için HBase kümesinin bağlantıya özgü DNS sonekini almalısınız. Bunu yapmak için, aşağıdaki yöntemlerden birini kullanabilirsiniz:

   * Bir [Apache ambarı](https://ambari.apache.org/) çağrısı yapmak Için bir Web tarayıcısı kullanın:

     Https://&lt;clustername >. azurehdinsight. net/api/v1/kümeler/&lt;clustername >/hosts? minimal_response = true adresine gidin. Bir JSON dosyasını DNS son eklerine dönüştürür.
   * Ambarı Web sitesini kullanın:

     1. Https://&lt;clustername >. azurehdinsight. net konumuna gidin.
     2. Üstteki menüden **konaklar** ' a tıklayın.
   * REST çağrısı yapmak için kıvrımlı kullanın:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Döndürülen JavaScript Nesne Gösterimi (JSON) verilerinde "host_name" girişini bulun. Kümedeki düğümlerin FQDN 'sini içerir. Örneğin:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Etki alanı adının küme adı ile başlayan bölümü DNS son ekidir. Örneğin, mycluster.b1.cloudapp.net.
   * Azure PowerShell kullanma

     Aşağıdaki Azure PowerShell betiği kullanarak, DNS sonekini döndürmek için kullanılabilecek **Get-ClusterDetail** işlevini kaydedin:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Azure PowerShell betiğini çalıştırdıktan sonra, **Get-ClusterDetail** IŞLEVINI kullanarak DNS sonekini döndürmek için aşağıdaki komutu kullanın. Bu komutu kullanırken HDInsight HBase kümeniz adı, yönetici adı ve yönetici parolanızı belirtin.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Bu komut, DNS sonekini döndürür. Örneğin, **yourclustername.B4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Sanal makinenin HBase kümesiyle iletişim kurabildiğini doğrulamak için, sanal makinedeki komutunu `ping headnode0.<dns suffix>` kullanın. Örneğin, ping headnode0.mycluster.b1.cloudapp.net.

Bu bilgileri bir Java uygulamasında kullanmak için, bir uygulama oluşturmak üzere [HDInsight (Hadoop) Ile Apache HBase kullanan Java uygulamaları oluşturmak Için Apache Maven kullanma](./apache-hbase-build-java-maven-linux.md) bölümündeki adımları izleyebilirsiniz. Uygulamanın uzak bir HBase sunucusuna bağlanmasını sağlamak için bu örnekteki **HBase-site. xml** dosyasını değiştirerek Zookeeper için FQDN 'yi kullanın. Örneğin:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Azure sanal ağlarında ad çözümlemesi hakkında daha fazla bilgi için, kendi DNS sunucunuzu kullanma da dahil olmak üzere, bkz. [ad çözümlemesi (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Apache HBase kümesi oluşturmayı öğrendiniz. Daha fazla bilgi için bkz:

* [HDInsight 'ı kullanmaya başlama](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight 'ta boş kenar düğümlerini kullanma](../hdinsight-apps-use-edge-node.md)
* [HDInsight 'ta Apache HBase çoğaltmasını yapılandırma](apache-hbase-replication.md)
* [HDInsight 'ta Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight 'ta Apache Hadoop ile Apache HBase kullanmaya başlama](./apache-hbase-tutorial-get-started-linux.md)
* [Sanal Ağ’a Genel Bakış](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

