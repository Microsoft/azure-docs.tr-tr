---
title: Invalidnetworkconfigurationerrorcode hatası-Azure HDInsight
description: Azure HDInsight 'ta ınvalidnetworkconfigurationerrorcode ile başarısız küme oluşturmaları için çeşitli nedenler
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 5b8d031af9dbe6019d71e2a1caa3d3f25d4024ea
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044461"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight 'ta küme oluşturma işlemi ınvalidnetworkconfigurationerrorcode ile başarısız oluyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

"Sanal ağ yapılandırması HDInsight gereksinimi ile uyumlu değil" açıklamasıyla hata kodu `InvalidNetworkConfigurationErrorCode` görürseniz, genellikle kümenizin [sanal ağ yapılandırmasıyla](../hdinsight-plan-virtual-network-deployment.md) ilgili bir sorun olduğunu gösterir. Hata açıklamasının geri kalanı temel alınarak, sorununuzu çözmek için aşağıdaki bölümleri izleyin.

## <a name="hostname-resolution-failed"></a>"Konak adı çözümlemesi başarısız oldu"

### <a name="issue"></a>Sorun

Hata açıklaması "konak adı çözümlemesi başarısız oldu" öğesini içeriyor.

### <a name="cause"></a>Nedeni

Bu hata, özel DNS yapılandırmasıyla ilgili bir sorunu işaret eder. Bir sanal ağ içindeki DNS sunucuları DNS sorgularını, bu sanal ağdaki konak adlarını çözümlemek için Azure 'un özyinelemeli çözümleyicilerine iletebilir (Ayrıntılar için bkz. [sanal ağlardaki ad çözümlemesi](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ). Azure 'un özyinelemeli çözümleyicilerine erişimi, sanal IP 168.63.129.16 aracılığıyla sağlanır. Bu IP 'ye yalnızca Azure VM 'lerinden erişilebilir. Bu nedenle, bir Onpred DNS sunucusu kullanıyorsanız veya DNS sunucunuz, kümenin vNet 'in parçası olmayan bir Azure VM 'se çalışmaz.

### <a name="resolution"></a>Çözünürlük

1. Kümenin parçası olan VM 'ye SSH ve `hostname -f`komutunu çalıştırın. Bu, konağın tam etki alanı adını (aşağıdaki yönergelerde `<host_fqdn>` olarak adlandırılır) döndürür.

1. Sonra, komut `nslookup <host_fqdn>` çalıştırın (örneğin, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Bu komut, adı bir IP adresi olarak çözümlerse, DNS sunucunuzun doğru şekilde çalıştığı anlamına gelir. Bu durumda, HDInsight ile bir destek talebi oluşturup sorununuzu araştıracağız. Destek durumda, yürüttüğünüz sorun giderme adımlarını ekleyin. Bu, sorunu daha hızlı çözmemize yardımcı olur.

1. Yukarıdaki komut bir IP adresi döndürmezse `nslookup <host_fqdn> 168.63.129.16` çalıştırın (örneğin, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Bu komut IP 'yi çözümleyebilse, DNS sunucunuz sorguyu Azure 'un DNS 'sine iletmediği ya da kümeyle aynı vNet 'in parçası olan bir VM olmadığı anlamına gelir.

1. Kümenin vNet 'inde özel bir DNS sunucusu işlevi görecek bir Azure VM 'iniz yoksa, önce bunu eklemeniz gerekir. VNet 'te, DNS ileticisi olarak yapılandırılacak bir VM oluşturun.

1. VNet 'iniz üzerinde dağıtılmış bir VM 'niz olduktan sonra, bu VM 'de DNS iletme kurallarını yapılandırın. Tüm IDNs ad çözümleme isteklerini 168.63.129.16 'e ve REST 'i DNS sunucunuza iletin. Özel bir DNS sunucusu için bu kuruluma bir örnek [aşağıda](../hdinsight-plan-virtual-network-deployment.md) verilmiştir.

1. Bu VM 'nin IP adresini, sanal ağ DNS yapılandırması için ilk DNS girişi olarak ekleyin.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Azure Storage hesabıyla bağlantı kurulamadı"

### <a name="issue"></a>Sorun

Hata açıklaması "Azure depolama hesabına bağlanılamadı" veya "Azure SQL 'e bağlanılamadı" içeriyor.

### <a name="cause"></a>Nedeni

Azure Storage ve SQL 'in sabit IP adresleri yok, bu nedenle bu hizmetlere erişime izin vermek için tüm IP 'Lere giden bağlantılara izin vermemiz gerekiyor. Tam çözümleme adımları, bir ağ güvenlik grubu (NSG) veya Kullanıcı tanımlı kurallar (UDR) ayarlamanıza bağlı olarak değişir. Bu yapılandırmalara ilişkin ayrıntılar için [ağ güvenlik grupları ve Kullanıcı tanımlı rotalar Ile HDInsight ile ağ trafiğini denetleme](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) bölümüne bakın.

### <a name="resolution"></a>Çözünürlük

* Kümeniz bir [ağ güvenlik grubu (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)kullanıyorsa.

    Azure portal gidin ve kümenin dağıtıldığı alt ağla ilişkili NSG 'yi tanımla. **Giden güvenlik kuralları** bölümünde, kısıtlama olmadan internet 'e giden erişime izin ver (burada daha küçük bir **Öncelik** sayısının daha yüksek öncelikli olduğunu unutmayın). Ayrıca, **alt ağlar** bölümünde, bu NSG 'nin küme alt ağına uygulandığını doğrulayın.

* Kümeniz [Kullanıcı tanımlı yollar (UDR)](../../virtual-network/virtual-networks-udr-overview.md)kullanıyorsa.

    Azure portal gidin ve kümenin dağıtıldığı alt ağla ilişkili yol tablosunu tanımla. Alt ağ için yol tablosunu bulduktan sonra, içindeki **rotalar** bölümünü inceleyin.

    Tanımlı yollar varsa, kümenin dağıtıldığı bölgenin IP adresleri için yolların bulunduğundan emin olun ve her bir rotanın **Nexthoptype** 'ı **Internet**'dir. Belirtilen makalede belgelenen her bir gerekli IP adresi için bir rota tanımlanmış olmalıdır.

---

### <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
