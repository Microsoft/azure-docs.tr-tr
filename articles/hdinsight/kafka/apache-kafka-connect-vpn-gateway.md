---
title: Sanal ağları kullanarak Kafka 'e bağlanma-Azure HDInsight
description: Azure sanal ağı aracılığıyla HDInsight 'ta Kafka 'e doğrudan bağlanmayı öğrenin. VPN Gateway kullanarak veya VPN Gateway cihazı kullanarak şirket içi ağınızdaki istemcilerden Kafka 'a nasıl bağlanacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 66bb054ab75c5a4e387995bc64dbc026c073413f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122618"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Azure sanal ağı aracılığıyla HDInsight 'ta Apache Kafka bağlanma

Azure sanal ağı aracılığıyla HDInsight 'ta Apache Kafka doğrudan bağlanmayı öğrenin. Bu belge, aşağıdaki yapılandırmalarda Kafka 'e bağlanma hakkında bilgi sağlar:

* Şirket içi bir ağdaki kaynaklardan. Bu bağlantı, yerel ağınızda bir VPN cihazı (yazılım veya donanım) kullanılarak oluşturulur.
* Bir VPN yazılım istemcisini kullanan bir geliştirme ortamından.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Mimari ve planlama

HDInsight, genel İnternet üzerinden Kafka 'e doğrudan bağlantıya izin vermez. Bunun yerine, Kafka istemcilerinin (üreticileri ve tüketiciler) aşağıdaki bağlantı yöntemlerinden birini kullanması gerekir:

* İstemcisini HDInsight üzerinde Kafka ile aynı sanal ağda çalıştırın. Bu yapılandırma, [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md) belgesinde kullanılır. İstemci doğrudan HDInsight küme düğümlerinde veya aynı ağdaki başka bir sanal makinede çalışır.

* Şirket içi ağınız gibi özel bir ağı sanal ağa bağlayın. Bu yapılandırma, şirket içi Ağınızdaki istemcilerin Kafka ile doğrudan çalışmasına izin verir. Bu yapılandırmayı etkinleştirmek için aşağıdaki görevleri gerçekleştirin:

  1. Sanal ağ oluşturun.
  2. Siteden siteye yapılandırma kullanan bir VPN Ağ Geçidi oluşturun. Bu belgede kullanılan yapılandırma, şirket içi ağınızdaki bir VPN Gateway cihazına bağlanır.
  3. Sanal ağda bir DNS sunucusu oluşturun.
  4. Her ağdaki DNS sunucusu arasında yönlendirmeyi yapılandırın.
  5. Sanal ağda HDInsight kümesi üzerinde bir Kafka oluşturun.

     Daha fazla bilgi için, Şirket [içi bir ağdan Apache Kafka bağlanma](#on-premises) bölümüne bakın. 

* VPN ağ geçidi ve VPN istemcisi kullanarak tek tek makineleri sanal ağa bağlayın. Bu yapılandırmayı etkinleştirmek için aşağıdaki görevleri gerçekleştirin:

  1. Sanal ağ oluşturun.
  2. Noktadan siteye yapılandırma kullanan bir VPN Ağ Geçidi oluşturun. Bu yapılandırma, hem Windows hem de MacOS istemcileri ile kullanılabilir.
  3. Sanal ağda HDInsight kümesi üzerinde bir Kafka oluşturun.
  4. IP tanıtımı için Kafka 'i yapılandırın. Bu yapılandırma, istemcinin etki alanı adları yerine aracı IP adreslerini kullanarak bağlanmasına izin verir.
  5. VPN istemcisini geliştirme sisteminde indirin ve kullanın.

     Daha fazla bilgi için [VPN istemcisiyle Apache Kafka bağlanma](#vpnclient) bölümüne bakın.

     > [!WARNING]  
     > Bu yapılandırma, aşağıdaki sınırlamalar nedeniyle yalnızca geliştirme amacıyla önerilir:
     >
     > * Her istemci bir VPN yazılım istemcisini kullanarak bağlanmalıdır.
     > * VPN istemcisi, ad çözümleme isteklerini sanal ağa geçirmez, bu nedenle Kafka ile iletişim kurmak için IP adresleme kullanmanız gerekir. IP iletişimi Kafka kümesinde ek yapılandırma gerektirir.

Bir sanal ağda HDInsight kullanma hakkında daha fazla bilgi için bkz. [Azure HDInsight kümeleri için bir sanal ağ planlayın](../hdinsight-plan-virtual-network-deployment.md).

## <a id="on-premises"></a>Şirket içi bir ağdan Apache Kafka bağlanma

Şirket içi ağınızla iletişim kuran bir Kafka kümesi oluşturmak için HDInsight 'ı Şirket [içi ağ belgenize bağlama](./../connect-on-premises-network.md) bölümündeki adımları izleyin.

> [!IMPORTANT]  
> HDInsight kümesini oluştururken, __Kafka__ küme türünü seçin.

Bu adımlar aşağıdaki yapılandırmayı oluşturur:

* Azure Sanal Ağı
* Siteden siteye VPN ağ geçidi
* Azure depolama hesabı (HDInsight tarafından kullanılır)
* HDInsight üzerinde Kafka

Bir Kafka istemcisinin Şirket içinden kümeye bağlanıp bağlanamadiğini doğrulamak için [örnekteki adımları kullanın: Python istemci](#python-client) bölümü.

## <a id="vpnclient"></a>VPN istemcisiyle Apache Kafka bağlanma

Aşağıdaki yapılandırmayı oluşturmak için bu bölümdeki adımları kullanın:

* Azure Sanal Ağı
* Noktadan siteye VPN ağ geçidi
* Azure depolama hesabı (HDInsight tarafından kullanılır)
* HDInsight üzerinde Kafka

1. [Noktadan siteye bağlantılar için otomatik olarak imzalanan sertifikalarla çalışma](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) bölümündeki adımları izleyin. Bu belge, ağ geçidi için gereken sertifikaları oluşturur.

2. Bir PowerShell istemi açın ve Azure aboneliğinizde oturum açmak için aşağıdaki kodu kullanın:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Yapılandırma bilgilerini içeren değişkenler oluşturmak için aşağıdaki kodu kullanın:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Azure Kaynak grubu ve sanal ağını oluşturmak için aşağıdaki kodu kullanın:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Bu işlemin tamamlanması birkaç dakika sürebilir.

5. Azure depolama hesabı ve BLOB kapsayıcısı oluşturmak için aşağıdaki kodu kullanın:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. HDInsight kümesini oluşturmak için aşağıdaki kodu kullanın:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Bu işlemin tamamlanabilmesi yaklaşık 15 dakika sürer.

### <a name="configure-kafka-for-ip-advertising"></a>IP tanıtımı için Kafka yapılandırma

Varsayılan olarak, Apache Zookeeper, istemcilere Kafka aracıların etki alanı adını döndürür. Bu yapılandırma, sanal ağdaki varlıklar için ad çözümlemesi kullanmadığından VPN yazılım istemcisiyle birlikte çalışmaz. Bu yapılandırma için, Kafka yapılandırmak için aşağıdaki adımları kullanarak etki alanı adları yerine IP adreslerini duyurmayı yapılandırın:

1. Bir Web tarayıcısı kullanarak adresine gidin `https://CLUSTERNAME.azurehdinsight.net`. HDInsight `CLUSTERNAME` kümesindeki Kafka adıyla değiştirin.

    İstendiğinde, küme için HTTPS Kullanıcı adı ve parolasını kullanın. Küme için ambarı Web Kullanıcı arabirimi görüntülenir.

2. Kafka hakkındaki bilgileri görüntülemek için soldaki listeden __Kafka__ ' yi seçin.

    ![Kafka vurgulanmış hizmet listesi](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Kafka yapılandırmasını görüntülemek için üst ortadaki __config__ 'ler ' i seçin.

    ![Apache ambarı Hizmetleri Yapılandırması](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. __Kafka-env__ yapılandırmasını bulmak için sağ üst köşedeki `kafka-env` __filtre__ alanına girin.

    ![Kafka-env için Kafka yapılandırması](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. IP adreslerini tanıtmak üzere Kafka yapılandırmak için, __Kafka-env-Template__ alanının altına aşağıdaki metni ekleyin:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka tarafından dinlediği arabirimi yapılandırmak için sağ üst köşedeki `listeners` __filtre__ alanına girin.

7. Kafka 'i tüm ağ arabirimlerini dinlemek üzere yapılandırmak için, __dinleyiciler__ alanındaki değeri olarak `PLAINTEXT://0.0.0.0:9092`değiştirin.

8. Yapılandırma değişikliklerini kaydetmek için __Kaydet__ düğmesini kullanın. Değişiklikleri açıklayan bir kısa mesaj girin. Değişiklikler kaydedildikten sonra __Tamam ' ı__ seçin.

    ![Apache ambarı kaydetme yapılandırması](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Kafka yeniden başlatırken hata oluşmasını engellemek için, __hizmet eylemleri__ düğmesini kullanın ve __bakım modunu aç__' ı seçin. Bu işlemi gerçekleştirmek için Tamam ' ı seçin.

    ![Bakım vurgulanmış şekilde hizmet eylemleri](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Kafka 'i yeniden başlatmak için __Yeniden Başlat__ düğmesini kullanın ve __etkilenen tümünü yeniden Başlat__' ı seçin. Yeniden başlatmayı onaylayın ve sonra işlem tamamlandıktan sonra __Tamam__ düğmesini kullanın.

    ![Yeniden Başlat düğmesi tüm etkilenen vurgulanmış vurgulanmış](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Bakım modunu devre dışı bırakmak için, __hizmet eylemleri__ düğmesini kullanın ve __bakım modunu__Kapat ' ı seçin. Bu işlemi gerçekleştirmek için **Tamam ' ı** seçin.

### <a name="connect-to-the-vpn-gateway"></a>VPN ağ geçidine bağlanma

VPN ağ geçidine bağlanmak için, [Noktadan siteye bağlantı yapılandırma](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) belgesi ' nin __Azure 'a Bağlan__ bölümünü kullanın.

## <a id="python-client"></a>Örneğinde Python istemcisi

Kafka bağlantısını doğrulamak için, aşağıdaki adımları kullanarak bir Python üreticisi ve tüketicisi oluşturun ve çalıştırın:

1. Kafka kümesinde düğümlerin tam etki alanı adını (FQDN) ve IP adreslerini almak için aşağıdaki yöntemlerden birini kullanın:

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

    Bu betik, sanal `$resourceGroupName` ağı içeren Azure Kaynak grubunun adı olduğunu varsayar.

    Sonraki adımlarda kullanılmak üzere döndürülen bilgileri kaydedin.

2. [Kafka-Python](https://kafka-python.readthedocs.io/) istemcisini yüklemek için aşağıdakileri kullanın:

    ```bash
    pip install kafka-python
    ```

3. Kafka 'e veri göndermek için aşağıdaki python kodunu kullanın:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    `'kafka_broker'` Girişleri bu bölümdeki 1. adımdan döndürülen adreslerle değiştirin:

   * __Yazılım VPN istemcisi__kullanıyorsanız, `kafka_broker` girdileri çalışan düğümlerinizin IP adresiyle değiştirin.

   * __Özel bir DNS sunucusu aracılığıyla ad çözümlemesini etkinleştirdiyseniz__, `kafka_broker` girdileri çalışan düğümlerinin FQDN 'siyle değiştirin.

     > [!NOTE]
     > Bu kod, dizeyi `test message` konuya `testtopic`gönderir. HDInsight üzerinde Kafka varsayılan yapılandırması, yoksa konuyu oluşturmaktır.

4. İletileri Kafka adresinden almak için aşağıdaki python kodunu kullanın:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    `'kafka_broker'` Girişleri bu bölümdeki 1. adımdan döndürülen adreslerle değiştirin:

    * __Yazılım VPN istemcisi__kullanıyorsanız, `kafka_broker` girdileri çalışan düğümlerinizin IP adresiyle değiştirin.

    * __Özel bir DNS sunucusu aracılığıyla ad çözümlemesini etkinleştirdiyseniz__, `kafka_broker` girdileri çalışan düğümlerinin FQDN 'siyle değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Bir sanal ağ ile HDInsight kullanma hakkında daha fazla bilgi için bkz. [Azure HDInsight kümeleri için sanal ağ dağıtımı planı](../hdinsight-plan-virtual-network-deployment.md) belgesi.

Noktadan siteye VPN ağ geçidi ile bir Azure sanal ağı oluşturma hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Azure portal kullanarak noktadan siteye bağlantı yapılandırma](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Azure PowerShell kullanarak noktadan siteye bağlantı yapılandırma](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

HDInsight üzerinde Apache Kafka ile çalışma hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md)
* [HDInsight üzerinde Apache Kafka yansıtma kullanma](apache-kafka-mirroring.md)
