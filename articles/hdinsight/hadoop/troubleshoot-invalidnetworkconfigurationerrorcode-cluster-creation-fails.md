---
title: Invalidnetworkconfigurationerrorcode hatası-Azure HDInsight
description: Azure HDInsight 'ta ınvalidnetworkconfigurationerrorcode ile başarısız küme oluşturmaları için çeşitli nedenler
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 83d4819ecb1da91bda5fb4f1cb445bbc34fd007f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98927003"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight 'ta küme oluşturma işlemi ınvalidnetworkconfigurationerrorcode ile başarısız oluyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

`InvalidNetworkConfigurationErrorCode`"Sanal ağ yapılandırması HDInsight gereksinimi ile uyumlu değil" açıklamasıyla hata kodu görürseniz, genellikle kümenizin [sanal ağ yapılandırmasıyla](../hdinsight-plan-virtual-network-deployment.md) ilgili bir sorun olduğunu gösterir. Hata açıklamasının geri kalanı temel alınarak, sorununuzu çözmek için aşağıdaki bölümleri izleyin.

## <a name="hostname-resolution-failed"></a>"Konak adı çözümlemesi başarısız oldu"

### <a name="issue"></a>Sorun

Hata açıklaması "konak adı çözümlemesi başarısız oldu" öğesini içeriyor.

### <a name="cause"></a>Nedeni

Bu hata, özel DNS yapılandırmasıyla ilgili bir sorunu işaret eder. Bir sanal ağ içindeki DNS sunucuları DNS sorgularını, bu sanal ağdaki konak adlarını çözümlemek için Azure 'un özyinelemeli çözümleyicilerine iletebilir (Ayrıntılar için bkz. [sanal ağlardaki ad çözümlemesi](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ). Azure 'un özyinelemeli çözümleyicilerine erişimi, sanal IP 168.63.129.16 aracılığıyla sağlanır. Bu IP 'ye yalnızca Azure VM 'lerinden erişilebilir. Bu nedenle, bir Onpred DNS sunucusu kullanıyorsanız veya DNS sunucunuz, kümenin sanal ağının bir parçası olmayan bir Azure VM 'si ise çalışmaz.

### <a name="resolution"></a>Çözüm

1. Kümenin parçası olan VM 'ye SSH ekleyin ve komutunu çalıştırın `hostname -f` . Bu, konağın tam etki alanı adını (aşağıdaki yönergelerde olduğu şekilde adlandırılır `<host_fqdn>` ) döndürür.

1. Sonra, komutunu çalıştırın `nslookup <host_fqdn>` (örneğin, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Bu komut, adı bir IP adresi olarak çözümlerse, DNS sunucunuzun doğru şekilde çalıştığı anlamına gelir. Bu durumda, HDInsight ile bir destek talebi oluşturup sorununuzu araştıracağız. Destek durumda, yürüttüğünüz sorun giderme adımlarını ekleyin. Bu, sorunu daha hızlı çözmemize yardımcı olur.

1. Yukarıdaki komut bir IP adresi döndürmezse `nslookup <host_fqdn> 168.63.129.16` (örneğin,) öğesini çalıştırın `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` . Bu komut IP 'yi çözümleyemezse, DNS sunucunuz sorguyu Azure 'un DNS 'sine iletmediği veya kümeyle aynı sanal ağın parçası olan bir VM olmadığı anlamına gelir.

1. Kümenin sanal ağında özel bir DNS sunucusu işlevi görecek bir Azure VM 'si yoksa, önce bunu eklemeniz gerekir. Sanal ağda, DNS ileticisi olarak yapılandırılacak bir VM oluşturun.

1. Sanal ağınızda dağıtılan bir VM 'niz olduktan sonra, bu VM 'de DNS iletme kurallarını yapılandırın. Tüm IDNs ad çözümleme isteklerini 168.63.129.16 'e ve REST 'i DNS sunucunuza iletin. Özel bir DNS sunucusu için bu kuruluma bir örnek [aşağıda](../hdinsight-plan-virtual-network-deployment.md) verilmiştir.

1. Bu VM 'nin IP adresini, sanal ağ DNS yapılandırması için ilk DNS girişi olarak ekleyin.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Azure Storage hesabıyla bağlantı kurulamadı"

### <a name="issue"></a>Sorun

Hata açıklaması "Azure depolama hesabına bağlanılamadı" veya "Azure SQL 'e bağlanılamadı" içeriyor.

### <a name="cause"></a>Nedeni

Azure Storage ve SQL 'in sabit IP adresleri yok, bu nedenle bu hizmetlere erişime izin vermek için tüm IP 'Lere giden bağlantılara izin vermemiz gerekiyor. Tam çözümleme adımları, bir ağ güvenlik grubu (NSG) veya User-Defined kuralları (UDR) ayarlayıp ayarladığınıza bağlıdır. Bu yapılandırmalara ilişkin ayrıntılar için [ağ güvenlik grupları ve Kullanıcı tanımlı rotalar Ile HDInsight ile ağ trafiğini denetleme](../control-network-traffic.md) bölümüne bakın.

### <a name="resolution"></a>Çözüm

* Kümeniz bir [ağ güvenlik grubu (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)kullanıyorsa.

    Azure portal gidin ve kümenin dağıtıldığı alt ağla ilişkili NSG 'yi tanımla. **Giden güvenlik kuralları** bölümünde, kısıtlama olmadan internet 'e giden erişime izin ver (burada daha küçük bir **Öncelik** sayısının daha yüksek öncelikli olduğunu unutmayın). Ayrıca, **alt ağlar** bölümünde, bu NSG 'nin küme alt ağına uygulandığını doğrulayın.

* Kümeniz [Kullanıcı tanımlı yollar (UDR)](../../virtual-network/virtual-networks-udr-overview.md)kullanıyorsa.

    Azure portal gidin ve kümenin dağıtıldığı alt ağla ilişkili yol tablosunu tanımla. Alt ağ için yol tablosunu bulduktan sonra, içindeki **rotalar** bölümünü inceleyin.

    Tanımlı yollar varsa, kümenin dağıtıldığı bölgenin IP adresleri için yolların bulunduğundan emin olun ve her bir rotanın **Nexthoptype** 'ı **Internet**'dir. Belirtilen makalede belgelenen her bir gerekli IP adresi için bir rota tanımlanmış olmalıdır.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"HDInsight kaynak sağlayıcısıyla iletişim için kümeden giden bir bağlantı kurulamadı. Lütfen giden bağlantıya izin verildiğinden emin olun. "

### <a name="issue"></a>Sorun

Hata açıklaması içerir "HDInsight kaynak sağlayıcısıyla iletişim için kümeden giden bağlantı kurulamadı. Lütfen giden bağlantıya izin verildiğinden emin olun. "

### <a name="cause"></a>Nedeni

Özel bağlantılı HDInsight kümeleri kullanırken, kümeden giden erişim, HDInsight kaynak sağlayıcısına bağlantı kurulmasına izin verecek şekilde yapılandırılmalıdır.

### <a name="resolution"></a>Çözüm

* Bu sorunu çözmek için [özel bağlantı kurulumu](../hdinsight-private-link.md) 'Nda HDInsight özel bağlantı kurulum adımlarına bakın
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Sanal ağ yapılandırması HDInsight gereksinimi ile uyumlu değil"

### <a name="issue"></a>Sorun

Hata açıklamaları aşağıdaki gibi iletileri içerir:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Nedeni

Özel DNS kurulumuyla ilgili bir sorun olabilir.

### <a name="resolution"></a>Çözüm

168.63.129.16 'in özel DNS zincirinde olduğunu doğrulayın. Bir sanal ağ içindeki DNS sunucuları DNS sorgularını, bu sanal ağdaki konak adlarını çözümlemek için Azure 'un özyinelemeli çözümleyicilerine iletebilir. Daha fazla bilgi için bkz. [sanal ağlarda ad çözümleme](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Azure 'un özyinelemeli çözümleyicilerine erişimi, sanal IP 168.63.129.16 aracılığıyla sağlanır.

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aşağıdaki komutu yürütün:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Şuna benzer bir şey görmeniz gerekir:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Sonuca bağlı olarak aşağıdaki adımlardan birini seçin:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 bu listede değil

**1\. Seçenek**  
[Azure HDInsight için bir sanal ağ planlayın](../hdinsight-plan-virtual-network-deployment.md)bölümünde açıklanan adımları kullanarak sanal ağın Ilk özel DNS olarak 168.63.129.16 ekleyin. Bu adımlar yalnızca özel DNS sunucunuz Linux üzerinde çalışıyorsa geçerlidir.

**2\. Seçenek**  
Sanal ağ için bir DNS sunucusu VM 'si dağıtın. Bu, aşağıdaki adımları içerir:

* Sanal ağda, DNS ileticisi (Linux veya Windows VM olabilir) olarak yapılandırılacak bir VM oluşturun.
* Bu VM 'de DNS iletme kurallarını yapılandırın (tüm IDNs ad çözümleme isteklerini 168.63.129.16 'ye ve DNS sunucunuza Rest 'e ilet).
* Sanal ağ DNS yapılandırması için bu VM 'nin IP adresini ilk DNS girişi olarak ekleyin.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 listede

Bu durumda, lütfen HDInsight ile bir destek durumu oluşturun ve sorununuzu araştıracağız. Aşağıdaki komutların sonucunu destek çalışmanıza ekleyin. Bu, sorunu daha hızlı araştırıp çözmemize yardımcı olur.

Baş düğümdeki bir SSH oturumunda aşağıdakileri düzenleyin ve çalıştırın:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Nedeni

Bu hata kodunun başka bir nedeni, `InvalidNetworkConfigurationErrorCode` `EnableVmProtection` PowerShell veya bir Azure runbook 'ta kullanım dışı bırakılan parametrenin kullanılması olabilir.

### <a name="resolution"></a>Çözüm

`Get-AzVirtualNetwork` [Az PowerShell SDK](/powershell/module/az.network/get-azvirtualnetwork) ' da belgelendiği gibi geçerli parametreleri kullanın

---

### <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
