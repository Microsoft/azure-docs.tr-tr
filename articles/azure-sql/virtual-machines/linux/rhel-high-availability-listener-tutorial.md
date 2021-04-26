---
title: Azure-Linux sanal makinelerinde RHEL sanal makinelerinde SQL Server için bir kullanılabilirlik grubu dinleyicisi yapılandırma | Microsoft Docs
description: Azure 'daki RHEL sanal makinelerinde SQL Server bir kullanılabilirlik grubu dinleyicisi ayarlama hakkında bilgi edinin
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 5f0b300be2f1cec4ee456065455832a2dc3598be
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449515"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Öğretici: Azure 'da RHEL sanal makinelerinde SQL Server için bir kullanılabilirlik grubu dinleyicisi yapılandırma
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Sunulan öğretici **genel önizlemede**. 
>
> Bu öğreticide RHEL 7,6 ile SQL Server 2017 kullanıyoruz, ancak yüksek kullanılabilirliği yapılandırmak için RHEL 7 veya RHEL 8 ' de SQL Server 2019 kullanmak mümkündür. Kullanılabilirlik grubu kaynaklarını yapılandırma komutları RHEL 8 ' de değişmiştir ve doğru komutlar hakkında daha fazla bilgi için [kullanılabilirlik grubu kaynağı](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) ve RHEL 8 kaynakları oluşturma makalesine bakmak isteyeceksiniz.

Bu öğreticide, Azure 'daki RHEL sanal makinelerinde (VM) bulunan SQL sunucularınız için bir kullanılabilirlik grubu dinleyicisi oluşturma adımları ele alınacaktır. Şunları öğrenirsiniz:

> [!div class="checklist"]
> - Azure portal yük dengeleyici oluşturma
> - Yük Dengeleyici için arka uç havuzunu yapılandırma
> - Yük Dengeleyici için bir araştırma oluşturun
> - Yük Dengeleme kurallarını ayarlama
> - Kümede Yük dengeleyici kaynağı oluşturma
> - Kullanılabilirlik grubu dinleyicisini oluşturma
> - Dinleyiciye bağlanan test
> - Yük devretmeyi test etme

## <a name="prerequisite"></a>Önkoşul

Tamamlanan [öğretici: Azure 'DA RHEL sanal makinelerinde SQL Server için kullanılabilirlik grupları yapılandırma](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure portal yük dengeleyiciyi oluşturun

Aşağıdaki yönergeler, yük [dengeleyici-Azure Portal](../windows/availability-group-load-balancer-portal-configure.md) makalesinin [Azure Portal bölümünde yük dengeleyiciyi oluşturma ve yapılandırma](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) üzerinden 1 ile 4 arasındaki adımları adım adım ele alır.

### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma

1. Azure portal, SQL Server sanal makinelerini içeren kaynak grubunu açın. 

2. Kaynak grubunda, **Ekle**' ye tıklayın.

3. **Yük dengeleyici** araması yapın ve ardından arama sonuçlarında **Microsoft** tarafından yayınlanan **Load Balancer** seçin.

4. **Load Balancer** dikey penceresinde **Oluştur**' a tıklayın.

5. **Yük dengeleyici oluştur** iletişim kutusunda yük dengeleyiciyi şu şekilde yapılandırın:

   | Ayar | Değer |
   | --- | --- |
   | **Ad** |Yük dengeleyiciyi temsil eden bir metin adı. Örneğin, **Sqllb**. |
   | **Tür** |**İç** |
   | **Sanal ağ** |Oluşturulan varsayılan sanal ağ **VM1VNET** olarak adlandırılmalıdır. |
   | **Alt ağ** |SQL Server örneklerinin bulunduğu alt ağı seçin. Varsayılan değer **VM1Subnet** olmalıdır.|
   | **IP adresi ataması** |**Static** |
   | **Özel IP adresi** |`virtualip`Kümede oluşturulan IP adresini kullanın. |
   | **Abonelik** |Kaynak grubunuz için kullanılan aboneliği kullanın. |
   | **Kaynak grubu** |SQL Server örneklerinin bulunduğu kaynak grubunu seçin. |
   | **Konum** |SQL Server örneklerinin bulunduğu Azure konumunu seçin. |

### <a name="configure-the-back-end-pool"></a>Arka uç havuzunu yapılandırma
Azure arka uç adres havuzu *arka uç havuzunu* çağırır. Bu durumda, arka uç havuzu, kullanılabilirlik grubunuzdaki üç SQL Server örneğinin adresleridir. 

1. Kaynak grubunuzda, oluşturduğunuz yük dengeleyiciye tıklayın. 

2. **Ayarlar**' da, **arka uç havuzları**' na tıklayın.

3. Arka uç **havuzlarında**, arka uç adres havuzu oluşturmak için **Ekle** ' ye tıklayın. 

4. **Arka uç Havuzu Ekle**' de, **ad**' ın altında, arka uç havuzu için bir ad yazın.

5. **İlişkili**' ın altında, **sanal makine**' yi seçin. 

6. Ortamdaki her bir sanal makineyi seçin ve uygun IP adresini her seçimle ilişkilendirin.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Arka uç Havuzu Ekle":::

7. **Ekle**'ye tıklayın. 

### <a name="create-a-probe"></a>Araştırma oluşturma

Araştırma, Azure 'un şu anda kullanılabilirlik grubu dinleyicisine sahip SQL Server örneklerinden hangisinin olduğunu nasıl doğrulayacağını tanımlar. Azure, araştırmayı oluştururken tanımladığınız bir bağlantı noktasındaki IP adresini temel alarak hizmeti yoklayın.

1. Yük dengeleyici **ayarları** dikey penceresinde **sistem durumu araştırmaları**' na tıklayın. 

2. **Sistem durumu araştırmaları** dikey penceresinde **Ekle**' ye tıklayın.

3. Araştırma **Ekle** dikey penceresinde araştırmayı yapılandırın. Araştırmayı yapılandırmak için aşağıdaki değerleri kullanın:

   | Ayar | Değer |
   | --- | --- |
   | **Ad** |Araştırmayı temsil eden bir metin adı. Örneğin, **Sqlalwaysonendpointaraştırması**. |
   | **Protokol** |**TCP** |
   | **Bağlantı noktası** |Kullanılabilir herhangi bir bağlantı noktasını kullanabilirsiniz. Örneğin, *59999*. |
   | **Aralık** |*5* |
   | **İyi durumda olmayan durum eşiği** |*2* |

4.  **Tamam**'a tıklayın. 

5. Tüm sanal makinelerinizde oturum açın ve aşağıdaki komutları kullanarak araştırma bağlantı noktasını açın:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure, araştırmayı oluşturur ve ardından onu kullanarak, kullanılabilirlik grubu için hangi SQL Server örneğinin dinleyicisi olduğunu test eder.

### <a name="set-the-load-balancing-rules"></a>Yük Dengeleme kurallarını ayarlama

Yük Dengeleme kuralları, yük dengeleyicinin trafiği SQL Server örneklerine nasıl yönlendirdiğini yapılandırır. Bu yük dengeleyici için, aynı anda yalnızca üç SQL Server örneklerinden biri kullanılabilirlik grubu dinleyicisi kaynağına sahip olduğu için doğrudan sunucu döndürmeyi etkinleştirirsiniz.

1. Yük dengeleyici **ayarları** dikey penceresinde **Yük Dengeleme kuralları**' na tıklayın. 

2. **Yük Dengeleme kuralları** dikey penceresinde **Ekle**' ye tıklayın.

3. **Yük Dengeleme kuralları Ekle** dikey penceresinde, Yük Dengeleme kuralını yapılandırın. Aşağıdaki ayarları kullanın: 

   | Ayar | Değer |
   | --- | --- |
   | **Ad** |Yük Dengeleme kurallarını temsil eden bir metin adı. Örneğin, **Sqlalwaysonendpointlistener**. |
   | **Protokol** |**TCP** |
   | **Bağlantı noktası** |*1433* |
   | **Arka uç bağlantı noktası** |*1433*. Bu kural **kayan IP (doğrudan sunucu dönüşü)** kullandığından bu değer yok sayılır. |
   | **Yoklama** |Bu yük dengeleyici için oluşturduğunuz araştırmanın adını kullanın. |
   | **Oturum kalıcılığı** |**Hiçbiri** |
   | **Boşta kalma zaman aşımı (dakika)** |*4* |
   | **Kayan IP (doğrudan sunucu dönüşü)** |**Etkin** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Yük Dengeleme kuralı ekle":::

4. **Tamam**'a tıklayın. 
5. Azure, Yük Dengeleme kuralını yapılandırır. Artık yük dengeleyici, trafiği kullanılabilirlik grubu için dinleyiciyi barındıran SQL Server örneğine yönlendirmek üzere yapılandırılmıştır. 

Bu noktada, kaynak grubunun tüm SQL Server makinelere bağlanan bir yük dengeleyici vardır. Yük dengeleyici Ayrıca, her makinenin kullanılabilirlik grupları için isteklere yanıt verebilmeleri için SQL Server Always on kullanılabilirlik grubu dinleyicisi için bir IP adresi de içerir.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Kümede Yük dengeleyici kaynağı oluşturma

1. Birincil sanal makinede oturum açın. Azure yük dengeleyici araştırma bağlantı noktasını etkinleştirmek için kaynak oluşturmanız gerekir (örneğimizde 59999 kullanılır). Şu komutu çalıştırın:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Ve kaynağını içeren bir grup oluşturun `virtualip` `azure_load_balancer` :

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Kısıtlama Ekle

1. Azure yük dengeleyici IP adresinin ve AG kaynağının aynı düğümde çalıştığından emin olmak için bir birlikte bulundurma kısıtlaması yapılandırılmalıdır. Şu komutu çalıştırın:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Azure yük dengeleyici IP adresinden önce AG kaynağının çalışır ve çalışıyor olduğundan emin olmak için bir sıralama kısıtlaması oluşturun. Birlikte bulundurma kısıtlaması bir sıralama kısıtlaması gösterdiği sürece bu uygulamayı zorlar.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Kısıtlamaları doğrulamak için şu komutu çalıştırın:

    ```bash
    sudo pcs constraint list --full
    ```

    Aşağıdaki çıkışı görmeniz gerekir:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisini oluşturma

1. Birincil düğümde, SQLCMD veya SSMS 'de aşağıdaki komutu çalıştırın:

    - Aşağıda kullanılan IP adresini `virtualip` IP adresiyle değiştirin.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Her VM düğümünde oturum açın. Konaklar dosyasını açmak ve her makinede için konak adı çözümlemesini ayarlamak için aşağıdaki komutu kullanın `ag1-listener` .

    ```
    sudo vi /etc/hosts
    ```

    **VI** düzenleyicisinde `i` metin eklemek için girin ve boş bir satıra, ' nin IP 'sini ekleyin `ag1-listener` . Ardından `ag1-listener` , IP 'nin yanındaki bir alandan sonra ekleyin.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    **VI** düzenleyicisinden çıkmak için, önce **ESC** tuşuna basın ve ardından `:wq` dosyayı yazıp çıkmak için komutunu girin. Bunu her düğümde yapın.

## <a name="test-the-listener-and-a-failover"></a>Dinleyiciyi ve yük devretmeyi test etme

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisini kullanarak SQL Server için test oturum açma

1. Kullanılabilirlik grubu dinleyicisi adını kullanarak SQL Server birincil düğümünde oturum açmak için SQLCMD kullanın:

    - Daha önce oluşturulmuş bir oturum açma kullanın ve `<YourPassword>` doğru parolayla değiştirin. Aşağıdaki örnek, `sa` SQL Server oluşturulan oturum açma bilgilerini kullanır.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Bağlı olduğunuz sunucunun adını denetleyin. SQLCMD 'de şu komutu çalıştırın:

    ```sql
    SELECT @@SERVERNAME
    ```

    Çıktın geçerli birincil düğümü göstermesi gerekir. Bu `VM1` , bir yük devretmeyi hiç test etmemelisiniz olmalıdır.

    Komutu yazarak SQL Server oturumundan çıkın `exit` .

### <a name="test-a-failover"></a>Yük devretmeyi test etme

1. Birincil çoğaltmayı `<VM2>` veya başka bir çoğaltmayı el ile devretmek için aşağıdaki komutu çalıştırın. `<VM2>`Sunucu adınızın değeriyle değiştirin.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Kısıtlamalarınızı denetederseniz, el ile yük devretme nedeniyle başka bir kısıtlamanın eklendiğini görürsünüz:

    ```bash
    sudo pcs constraint list --full
    ```

    KIMLIĞI olan bir kısıtlamanın `cli-prefer-ag_cluster-master` eklendiğini görürsünüz.

1. `cli-prefer-ag_cluster-master`Aşağıdaki komutu kullanarak kısıtlamayı kimliğiyle kaldırın:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Komutunu kullanarak küme kaynaklarınızı kontrol `sudo pcs resource` edin ve birincil Örneğin şu anda olduğunu görmeniz gerekir `<VM2>` .

    > [!NOTE]
    > Bu makale, Microsoft 'un artık kullandığı bir terim olan bağımlı dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Dinleyici adını kullanarak birincil yinelemenizdeki oturum açmak için SQLCMD kullanın:

    - Daha önce oluşturulmuş bir oturum açma kullanın ve `<YourPassword>` doğru parolayla değiştirin. Aşağıdaki örnek, `sa` SQL Server oluşturulan oturum açma bilgilerini kullanır.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Bağlı olduğunuz sunucuyu kontrol edin. SQLCMD 'de şu komutu çalıştırın:

    ```sql
    SELECT @@SERVERNAME
    ```

    Artık yük devredilen sanal makineye bağlı olduğunu görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'daki yük dengeleyiciler hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Azure VM 'lerinde SQL Server bir kullanılabilirlik grubu için yük dengelemesi yapılandırma](../windows/availability-group-load-balancer-portal-configure.md)
