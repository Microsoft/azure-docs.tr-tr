---
title: Kullanılabilirlik grubu dinleyicilerini & yük dengeleyiciyi yapılandırma (Azure portal)
description: Azure sanal makinelerinde SQL Server için her zaman açık kullanılabilirlik grubu için dinleyici oluşturmaya yönelik adım adım yönergeler
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039658"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Azure SQL Server VM 'lerde bir kullanılabilirlik grubu için yük dengeleyici yapılandırma
Bu makalede, Azure Resource Manager çalıştıran Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu için yük dengeleyici oluşturma açıklanır. Bir kullanılabilirlik grubu, SQL Server örnekleri Azure sanal makinelerinde olduğunda yük dengeleyici gerektirir. Yük dengeleyici, kullanılabilirlik grubu dinleyicisinin IP adresini depolar. Bir kullanılabilirlik grubu birden çok bölgeye yayılırsa, her bölgenin bir yük dengeleyici olması gerekir.

Bu görevi gerçekleştirmek için, Kaynak Yöneticisi çalıştıran Azure sanal makinelerinde dağıtılmış bir SQL Server kullanılabilirlik grubunuz olması gerekir. SQL Server sanal makinelerin her ikisi de aynı Kullanılabilirlik kümesine ait olmalıdır. Kaynak Yöneticisi ' de kullanılabilirlik grubunu otomatik olarak oluşturmak için [Microsoft şablonunu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) kullanabilirsiniz. Bu şablon sizin için otomatik olarak bir iç yük dengeleyici oluşturur. 

İsterseniz, [bir kullanılabilirlik grubunu el ile yapılandırabilirsiniz](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Bu makale, kullanılabilirlik gruplarınızın zaten yapılandırılmış olmasını gerektirir.  

İlgili konular şunları içerir:

* [Azure VM 'de Always on kullanılabilirlik grupları yapılandırma (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Azure Resource Manager ve PowerShell kullanarak VNet-VNet bağlantısı yapılandırma](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Bu makalede yürüyerek Azure portal bir yük dengeleyici oluşturup yapılandırırsınız. İşlem tamamlandıktan sonra, küme, kullanılabilirlik grubu dinleyicisi için yük dengeleyiciden IP adresini kullanacak şekilde yapılandırılır.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Azure portal yük dengeleyici oluşturma ve yapılandırma
Görevin bu bölümünde şunları yapın:

1. Azure portal, yük dengeleyiciyi oluşturun ve IP adresini yapılandırın.
2. Arka uç havuzunu yapılandırın.
3. Araştırması oluşturun. 
4. Yük Dengeleme kurallarını ayarlayın.

> [!NOTE]
> SQL Server örnekleri birden çok kaynak grubunda ve bölgede yer alıyorsa her bir kaynak grubunda bir kez olmak üzere her adımı iki kez gerçekleştirin.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>1\. Adım: yük dengeleyiciyi oluşturma ve IP adresini yapılandırma
İlk olarak, yük dengeleyiciyi oluşturun. 

1. Azure portal, SQL Server sanal makinelerini içeren kaynak grubunu açın. 

2. Kaynak grubunda, **Ekle**' ye tıklayın.

3. **Yük dengeleyici** araması yapın ve ardından arama sonuçlarında **Microsoft**tarafından yayınlanan **Load Balancer**seçin.

4. **Load Balancer** dikey penceresinde **Oluştur**' a tıklayın.

5. **Yük dengeleyici oluştur** iletişim kutusunda yük dengeleyiciyi şu şekilde yapılandırın:

   | Ayar | Değer |
   | --- | --- |
   | **Adı** |Yük dengeleyiciyi temsil eden bir metin adı. Örneğin, **Sqllb**. |
   | **Tür** |**İç**: çoğu uygulama, aynı sanal ağ içindeki uygulamaların kullanılabilirlik grubuna bağlanmasına izin veren bir iç yük dengeleyici kullanır.  </br> **Harici**: uygulamaların genel bir Internet bağlantısı aracılığıyla kullanılabilirlik grubuna bağlanmasına izin verir. |
   | **Sanal ağ** |SQL Server örneklerinin bulunduğu sanal ağı seçin. |
   | **Alt ağ** |SQL Server örneklerinin bulunduğu alt ağı seçin. |
   | **IP adresi ataması** |**Se** |
   | **Özel IP adresi** |Alt ağdan kullanılabilir bir IP adresi belirtin. Kümede bir dinleyici oluşturduğunuzda bu IP adresini kullanın. Bir PowerShell betiğindeki Bu makalenin ilerleyen kısımlarında `$ILBIP` değişkeni için bu adresi kullanın. |
   | **Abonelik** |Birden çok aboneliğiniz varsa, bu alan görünebilir. Bu kaynakla ilişkilendirmek istediğiniz aboneliği seçin. Normalde kullanılabilirlik grubu için tüm kaynaklarla aynı abonelikte olur. |
   | **Kaynak grubu** |SQL Server örneklerinin bulunduğu kaynak grubunu seçin. |
   | **Konum** |SQL Server örneklerinin bulunduğu Azure konumunu seçin. |

6. **Oluştur**’ tıklayın. 

Azure, yük dengeleyici oluşturur. Yük dengeleyici belirli bir ağa, alt ağa, kaynak grubuna ve konuma aittir. Azure, görevi tamamladıktan sonra yük dengeleyici ayarlarını Azure 'da doğrulayın. 

### <a name="step-2-configure-the-back-end-pool"></a>2\. Adım: arka uç havuzunu yapılandırma
Azure arka uç adres havuzu *arka uç havuzunu*çağırır. Bu durumda, arka uç havuzu kullanılabilirlik grubunuzda iki SQL Server örneğinin adresleridir. 

1. Kaynak grubunuzda, oluşturduğunuz yük dengeleyiciye tıklayın. 

2. **Ayarlar**' da, **arka uç havuzları**' na tıklayın.

3. Arka uç **havuzlarında**, arka uç adres havuzu oluşturmak için **Ekle** ' ye tıklayın. 

4. **Arka uç Havuzu Ekle**' de, **ad**' ın altında, arka uç havuzu için bir ad yazın.

5. **Sanal makineler**altında **sanal makine Ekle**' ye tıklayın. 

6. **Sanal makineler seçin**altında, **bir kullanılabilirlik kümesi Seç**' e tıklayın ve ardından SQL Server sanal makinelerinin ait olduğu kullanılabilirlik kümesini belirtin.

7. Kullanılabilirlik kümesini seçtikten sonra, **sanal makineleri Seç**' e tıklayın, kullanılabilirlik grubundaki SQL Server örneklerini barındıran iki sanal makineyi seçin ve ardından **Seç**' e tıklayın. 

8. **Sanal makineler Seç**ve **arka uç Havuzu Ekle**dikey penceresini kapatmak için **Tamam** ' a tıklayın. 

Azure, arka uç adres havuzunun ayarlarını güncelleştirir. Artık kullanılabilirlik kümesinde iki SQL Server örneği havuzu bulunur.

### <a name="step-3-create-a-probe"></a>3\. Adım: araştırma oluşturma
Araştırma, Azure 'un şu anda kullanılabilirlik grubu dinleyicisine sahip SQL Server örneklerinden hangisinin olduğunu nasıl doğrulayacağını tanımlar. Azure, araştırmayı oluştururken tanımladığınız bir bağlantı noktasındaki IP adresini temel alarak hizmeti yoklayın.

1. Yük dengeleyici **ayarları** dikey penceresinde **sistem durumu araştırmaları**' na tıklayın. 

2. **Sistem durumu araştırmaları** dikey penceresinde **Ekle**' ye tıklayın.

3. Araştırma **Ekle** dikey penceresinde araştırmayı yapılandırın. Araştırmayı yapılandırmak için aşağıdaki değerleri kullanın:

   | Ayar | Değer |
   | --- | --- |
   | **Adı** |Araştırmayı temsil eden bir metin adı. Örneğin, **Sqlalwaysonendpointaraştırması**. |
   | **Protokolü** |**TCP** |
   | **Bağlantı Noktası** |Kullanılabilir herhangi bir bağlantı noktasını kullanabilirsiniz. Örneğin, *59999*. |
   | **Aralık** |*5* |
   | **Sağlıksız eşik** |*2* |

4.  **OK (Tamam)** düğmesine tıklayın. 

> [!NOTE]
> Belirttiğiniz bağlantı noktasının her iki SQL Server örneğinin güvenlik duvarında açık olduğundan emin olun. Her iki örnek de kullandığınız TCP bağlantı noktası için bir gelen kuralı gerektirir. Daha fazla bilgi için bkz. [güvenlik duvarı kuralı ekleme veya düzenleme](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure, araştırmayı oluşturur ve ardından onu kullanarak, kullanılabilirlik grubu için hangi SQL Server örneğinin dinleyicisi olduğunu test eder.

### <a name="step-4-set-the-load-balancing-rules"></a>4\. Adım: Yük Dengeleme kurallarını ayarlama
Yük Dengeleme kuralları, yük dengeleyicinin trafiği SQL Server örneklerine nasıl yönlendirdiğini yapılandırır. Bu yük dengeleyici için, yalnızca iki SQL Server örneklerinden biri aynı anda kullanılabilirlik grubu dinleyicisi kaynağına sahip olduğu için doğrudan sunucu döndürmeyi etkinleştirirsiniz.

1. Yük dengeleyici **ayarları** dikey penceresinde **Yük Dengeleme kuralları**' na tıklayın. 

2. **Yük Dengeleme kuralları** dikey penceresinde **Ekle**' ye tıklayın.

3. **Yük Dengeleme kuralları Ekle** dikey penceresinde, Yük Dengeleme kuralını yapılandırın. Aşağıdaki ayarları kullanın: 

   | Ayar | Değer |
   | --- | --- |
   | **Adı** |Yük Dengeleme kurallarını temsil eden bir metin adı. Örneğin, **Sqlalwaysonendpointlistener**. |
   | **Protokolü** |**TCP** |
   | **Bağlantı Noktası** |*1433* |
   | **Arka uç bağlantı noktası** |*1433*. Bu kural **kayan IP (doğrudan sunucu dönüşü)** kullandığından bu değer yok sayılır. |
   | **Yokla** |Bu yük dengeleyici için oluşturduğunuz araştırmanın adını kullanın. |
   | **Oturum kalıcılığı** |**Yok.** |
   | **Boşta kalma zaman aşımı (dakika)** |*4* |
   | **Kayan IP (doğrudan sunucu dönüşü)** |**Etkin** |

   > [!NOTE]
   > Tüm ayarları görüntülemek için dikey pencerenin aşağı kaydırmanız gerekebilir.
   > 

4. **OK (Tamam)** düğmesine tıklayın. 
5. Azure, Yük Dengeleme kuralını yapılandırır. Artık yük dengeleyici, trafiği kullanılabilirlik grubu için dinleyiciyi barındıran SQL Server örneğine yönlendirmek üzere yapılandırılmıştır. 

Bu noktada, kaynak grubu SQL Server makinelere bağlanan bir yük dengeleyiciye sahiptir. Yük dengeleyici Ayrıca, makine kullanılabilirlik grupları için isteklere yanıt verebilmeleri için SQL Server Always on kullanılabilirlik grubu dinleyicisi için bir IP adresi de içerir.

> [!NOTE]
> SQL Server örneklerinizin iki ayrı bölgede olması durumunda diğer bölgedeki adımları yineleyin. Her bölge için bir yük dengeleyici gerekir. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Kümeyi, yük dengeleyici IP adresini kullanacak şekilde yapılandırma
Sonraki adım, kümede dinleyiciyi yapılandırmak ve dinleyiciyi çevrimiçi hale getirmaktır. Şunları yapın: 

1. Yük devretme kümesinde kullanılabilirlik grubu dinleyicisini oluşturun. 

2. Dinleyiciyi çevrimiçi duruma getirin.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>5\. Adım: yük devretme kümesinde kullanılabilirlik grubu dinleyicisini oluşturma
Bu adımda, Yük Devretme Kümesi Yöneticisi ve SQL Server Management Studio ' de kullanılabilirlik grubu dinleyicisini el ile oluşturursunuz.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Dinleyicinin yapılandırmasını doğrulama

Küme kaynakları ve Bağımlılıklar doğru yapılandırılmışsa, SQL Server Management Studio dinleyiciyi görüntüleyebilmeniz gerekir. Dinleyici bağlantı noktasını ayarlamak için şunları yapın:

1. SQL Server Management Studio başlatın ve ardından birincil çoğaltmaya bağlanın.

2. **Kullanılabilirlik grubu dinleyicilerine** > **AlwaysOn yüksek kullanılabilirlik** > **kullanılabilirlik gruplarına** gidin.  
    Artık Yük Devretme Kümesi Yöneticisi oluşturduğunuz dinleyici adını görmeniz gerekir. 

3. Dinleyici adına sağ tıklayın ve ardından **Özellikler**' e tıklayın.

4. **Bağlantı noktası** kutusunda, daha önce kullandığınız $EndpointPort kullanarak kullanılabilirlik grubu dinleyicisinin bağlantı noktası numarasını belirtin (1433 varsayılandır) ve ardından **Tamam**' a tıklayın.

Artık Kaynak Yöneticisi modunda çalışan Azure sanal makinelerinde bir kullanılabilirlik grubunuz var. 

## <a name="test-the-connection-to-the-listener"></a>Dinleyiciyle bağlantıyı test etme
Aşağıdakileri yaparak bağlantıyı test edin:

1. Aynı sanal ağdaki bir SQL Server örneğine RDP ile, ancak çoğaltmaya sahip değildir. Bu sunucu, kümedeki diğer SQL Server örneği olabilir.

2. Bağlantıyı sınamak için **sqlcmd** yardımcı programını kullanın. Örneğin, aşağıdaki komut dosyası, Windows kimlik doğrulaması ile dinleyici aracılığıyla birincil çoğaltmaya bir **sqlcmd** bağlantısı kurar:
   
        sqlcmd -S <listenerName> -E

SQLCMD bağlantısı, birincil çoğaltmayı barındıran SQL Server örneğine otomatik olarak bağlanır. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Ek kullanılabilirlik grubu için bir IP adresi oluşturma

Her kullanılabilirlik grubu ayrı bir dinleyici kullanır. Her dinleyici kendi IP adresine sahiptir. Ek dinleyiciler için IP adresini tutmak üzere aynı yük dengeleyiciyi kullanın. Bir kullanılabilirlik grubu oluşturduktan sonra, IP adresini yük dengeleyiciye ekleyin ve ardından dinleyiciyi yapılandırın.

Azure portal bir yük dengeleyicisine bir IP adresi eklemek için aşağıdakileri yapın:

1. Azure portal, yük dengeleyiciyi içeren kaynak grubunu açın ve yük dengeleyiciye tıklayın. 

2. **Ayarlar**altında, **ön uç IP havuzu**' na ve ardından **Ekle**' ye tıklayın. 

3. Ön uç **IP adresi ekle**' nin altında, ön uç için bir ad atayın. 

4. **Sanal ağın** ve **alt ağın** SQL Server örneklerle aynı olduğunu doğrulayın.

5. Dinleyicinin IP adresini ayarlayın. 
   
   >[!TIP]
   >IP adresini statik olarak ayarlayabilir ve alt ağda Şu anda kullanılmayan bir adresi yazabilirsiniz. Alternatif olarak, IP adresini dinamik olarak ayarlayabilir ve yeni ön uç IP havuzunu kaydedebilirsiniz. Bunu yaptığınızda, Azure portal havuza otomatik olarak kullanılabilir bir IP adresi atar. Ardından ön uç IP havuzunu yeniden açabilir ve atamayı statik olarak değiştirebilirsiniz. 

6. Dinleyicinin IP adresini kaydedin. 

7. Aşağıdaki ayarları kullanarak bir sistem durumu araştırması ekleyin:

   |Ayar |Değer
   |:-----|:----
   |**Adı** |Araştırmayı tanımlayacak bir ad.
   |**Protokolü** |TCP
   |**Bağlantı Noktası** |Tüm sanal makinelerde kullanılabilir olması gereken kullanılmamış bir TCP bağlantı noktası. Başka herhangi bir amaçla kullanılamaz. İki dinleyici aynı araştırma bağlantı noktasını kullanamaz. 
   |**Aralık** |Araştırma denemeleri arasındaki süre miktarı. Varsayılanı kullanın (5).
   |**Sağlıksız eşik** |Bir sanal makinenin sağlıksız olduğu kabul edilmeden önce başarısız olması gereken birbirini izleyen eşiklerin sayısı.

8. Araştırmayı kaydetmek için **Tamam** ' ı tıklatın. 

9. Yük Dengeleme kuralı oluşturma. **Yük Dengeleme kuralları**' na ve ardından **Ekle**' ye tıklayın.

10. Aşağıdaki ayarları kullanarak yeni yük dengeleme kuralını yapılandırın:

    |Ayar |Değer
    |:-----|:----
    |**Adı** |Yük Dengeleme kuralını tanımlamak için bir ad. 
    |**Ön uç IP adresi** |Oluşturduğunuz IP adresini seçin. 
    |**Protokolü** |TCP
    |**Bağlantı Noktası** |SQL Server örneklerinin kullandığı bağlantı noktasını kullanın. Varsayılan bir örnek, değiştirmediğiniz müddetçe 1433 numaralı bağlantı noktasını kullanır. 
    |**Arka uç bağlantı noktası** |**Bağlantı noktası**ile aynı değeri kullanın.
    |**Arka uç havuzu** |SQL Server örneklerine sahip sanal makineleri içeren havuz. 
    |**Durum araştırması** |Oluşturduğunuz araştırmayı seçin.
    |**Oturum kalıcılığı** |None
    |**Boşta kalma zaman aşımı (dakika)** |Varsayılan (4)
    |**Kayan IP (doğrudan sunucu dönüşü)** | Etkin

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Kullanılabilirlik grubunu yeni IP adresini kullanacak şekilde yapılandırın

Kümeyi yapılandırmayı bitirdiğinizde, ilk kullanılabilirlik grubunu yaptığınızda izlediğiniz adımları yineleyin. Diğer bir deyişle, [kümeyi yenı IP adresini kullanacak şekilde](#configure-the-cluster-to-use-the-load-balancer-ip-address)yapılandırın. 

Dinleyici için bir IP adresi ekledikten sonra, aşağıdakileri yaparak ek kullanılabilirlik grubunu yapılandırın: 

1. Yeni IP adresi için araştırma bağlantı noktasının, SQL Server sanal makinelerde açık olduğunu doğrulayın. 

2. [Küme Yöneticisi ' nde, istemci erişim noktasını ekleyin](#addcap).

3. [Kullanılabilirlik grubu IÇIN IP kaynağını yapılandırın](#congroup).

   >[!IMPORTANT]
   >IP adresini oluştururken, yük dengeleyicisine eklediğiniz IP adresini kullanın.  

4. [SQL Server kullanılabilirlik grubu kaynağını istemci erişim noktasına bağımlı hale getirin](#dependencyGroup).

5. [İstemci erişim noktası KAYNAĞıNı IP adresine bağımlı hale getirin](#listname).
 
6. [PowerShell 'de küme parametrelerini ayarlayın](#setparam).

Kullanılabilirlik grubunu yeni IP adresini kullanacak şekilde yapılandırdıktan sonra, dinleyiciye bağlantıyı yapılandırın. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Dağıtılmış kullanılabilirlik grubu için Yük Dengeleme kuralı ekle

Bir kullanılabilirlik grubu Dağıtılmış kullanılabilirlik grubuna katılıyorsa, yük dengeleyici ek bir kurala ihtiyaç duyuyor. Bu kural, dağıtılmış kullanılabilirlik grubu dinleyicisi tarafından kullanılan bağlantı noktasını depolar.

>[!IMPORTANT]
>Bu adım yalnızca kullanılabilirlik grubu [Dağıtılmış bir kullanılabilirlik grubuna](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)katılıyorsa geçerlidir. 

1. Dağıtılmış kullanılabilirlik grubuna katılan her bir sunucuda, dağıtılmış kullanılabilirlik grubu dinleyicisi TCP bağlantı noktasında bir gelen kuralı oluşturun. Birçok örnekte, belgeler 5022 ' i kullanır. 

1. Azure portal yük dengeleyiciye, **Yük Dengeleme kuralları**' na ve ardından **+ Ekle**' ye tıklayın. 

1. Aşağıdaki ayarlarla Yük Dengeleme kuralını oluşturun:

   |Ayar |Değer
   |:-----|:----
   |**Adı** |Dağıtılmış kullanılabilirlik grubu için Yük Dengeleme kuralını tanımlamak için bir ad. 
   |**Ön uç IP adresi** |Kullanılabilirlik grubuyla aynı ön uç IP adresini kullanın.
   |**Protokolü** |TCP
   |**Bağlantı Noktası** |5022- [Dağıtılmış kullanılabilirlik grubu uç nokta dinleyicisi](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)için bağlantı noktası.</br> Kullanılabilir herhangi bir bağlantı noktası olabilir.  
   |**Arka uç bağlantı noktası** | 5022- **bağlantı noktası**ile aynı değeri kullanın.
   |**Arka uç havuzu** |SQL Server örneklerine sahip sanal makineleri içeren havuz. 
   |**Durum araştırması** |Oluşturduğunuz araştırmayı seçin.
   |**Oturum kalıcılığı** |None
   |**Boşta kalma zaman aşımı (dakika)** |Varsayılan (4)
   |**Kayan IP (doğrudan sunucu dönüşü)** | Etkin

Dağıtılmış kullanılabilirlik gruplarına katılan diğer kullanılabilirlik gruplarındaki yük dengeleyici için bu adımları tekrarlayın.

Azure ağ güvenlik grubuyla erişimi kısıtladığınız takdirde, izin ver kurallarının arka uç SQL Server VM IP adreslerini ve ağ dinleyicisi için yük dengeleyici kayan IP adreslerini ve varsa küme çekirdek IP adresini içerdiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Farklı bölgelerde Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grubu yapılandırma](virtual-machines-windows-portal-sql-availability-group-dr.md)
