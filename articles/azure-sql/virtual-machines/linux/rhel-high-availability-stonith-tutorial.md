---
title: Azure-Linux sanal makinelerinde RHEL sanal makinelerinde SQL Server için kullanılabilirlik grupları yapılandırma | Microsoft Docs
description: Bir RHEL küme ortamında yüksek kullanılabilirlik ayarlama ve STONITH ayarlama hakkında bilgi edinin
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 889df16191104ca5b335d067abe0f3d1311c4603
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450115"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Öğretici: Azure 'da RHEL sanal makinelerinde SQL Server için kullanılabilirlik grupları yapılandırma 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Bu öğreticide RHEL 7,6 ile SQL Server 2017 kullanıyoruz, ancak yüksek kullanılabilirliği yapılandırmak için RHEL 7 veya RHEL 8 ' de SQL Server 2019 kullanmak mümkündür. Pacemake kümesini ve kullanılabilirlik grubu kaynaklarını yapılandırma komutları RHEL 8 ' de değişmiştir ve doğru komutlar hakkında daha fazla bilgi için [kullanılabilirlik grubu kaynağı](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) ve RHEL 8 kaynakları oluşturma makalesine bakmak isteyeceksiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Yeni bir kaynak grubu, kullanılabilirlik kümesi ve Linux sanal makineleri (VM 'Ler) oluşturma
> - Yüksek kullanılabilirliği etkinleştir (HA)
> - Paceoluşturucu kümesi oluşturma
> - Bir TNITH cihazı oluşturarak bir uçum Aracısı yapılandırma
> - RHEL üzerinde SQL Server ve MSSQL araçları 'nı yükler
> - SQL Server Always on kullanılabilirlik grubu yapılandırma
> - Paceoluşturucu kümesindeki kullanılabilirlik grubu (AG) kaynaklarını yapılandırma
> - Yük devretme ve sınırlama aracısını test etme

Bu öğreticide Azure 'da kaynak dağıtmak için Azure CLı kullanılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.30 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Birden fazla aboneliğiniz varsa, bu kaynakları dağıtmak istediğiniz [aboneliği ayarlayın](/cli/azure/manage-azure-subscriptions-azure-cli) .

Bir bölgede kaynak grubu oluşturmak için aşağıdaki komutu kullanın `<resourceGroupName>` . `<resourceGroupName>`Seçtiğiniz bir adla değiştirin. `East US 2`Bu öğretici için kullanıyoruz. Daha fazla bilgi için aşağıdaki [hızlı](../../../application-gateway/quick-create-cli.md)başlangıca bakın.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma

Bir sonraki adım, bir kullanılabilirlik kümesi oluşturmaktır. Azure Cloud Shell ' de aşağıdaki komutu çalıştırın ve `<resourceGroupName>` kaynak grubu adınızla değiştirin. İçin bir ad seçin `<availabilitySetName>` .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Komut tamamlandıktan sonra aşağıdaki sonuçları almanız gerekir:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Kullanılabilirlik kümesi içinde RHEL VM 'Leri oluşturma

> [!WARNING]
> Kullandıkça Öde (PAYG) RHEL görüntüsünü seçerseniz ve yüksek kullanılabilirliği (HA) yapılandırırsanız, aboneliğinizi kaydetmeniz gerekebilir. Bu, sanal makine için Microsoft Azure RHEL aboneliği ve Red Hat aboneliği için ücretlendirildiğiniz için abonelik için iki kez ödeme yapılmasına neden olabilir. Daha fazla bilgi için bkz. https://access.redhat.com/solutions/2458541.
>
> "Çift faturalandırılan" olmaması için, Azure VM oluştururken bir RHEL HA görüntüsü kullanın. RHEL-HA görüntüleri olarak sunulan görüntüler Ayrıca, HA deposu önceden etkin olan PAYG görüntüleridir.

1. RHEL 'yi HA ile sunan sanal makine görüntülerinin listesini alın:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Aşağıdaki sonuçları görmeniz gerekir:

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    Bu öğreticide, `RedHat:RHEL-HA:7.6:7.6.2019062019` RHEL 7 örneği için görüntü seçiyoruz ve `RedHat:RHEL-HA:8.1:8.1.2020021914` RHEL 8 örneği için seçim yaptık.
    
    RHEL8-HA görüntülerinde önceden yüklenmiş SQL Server 2019 ' i de seçebilirsiniz. Bu görüntülerin listesini almak için aşağıdaki komutu çalıştırın:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    Aşağıdaki sonuçları görmeniz gerekir:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    Sanal makineleri oluşturmak için yukarıdaki görüntülerden birini kullanırsanız, SQL Server 2019 önceden yüklenmiş olur. Bu makalede açıklandığı gibi [ınstall SQL Server ve MSSQL-Tools](#install-sql-server-and-mssql-tools) bölümünü atlayın.
    
    
    > [!IMPORTANT]
    > Kullanılabilirlik grubu ayarlamak için makine adları 15 karakterden az olmalıdır. Kullanıcı adı büyük harf karakterleri içeremez ve parolalar 12 karakterden uzun olmalıdır.

1. Kullanılabilirlik kümesinde 3 VM oluşturmak istiyoruz. Aşağıdaki komutta aşağıdaki komutu değiştirin:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` -Bir örnek "Standard_D16_v3" olabilir
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Yukarıdaki komut, VM 'Leri oluşturur ve bu VM 'Ler için varsayılan bir sanal ağ oluşturur. Farklı yapılandırmalara ilişkin daha fazla bilgi için, [az VM Create](/cli/azure/vm) makalesine bakın.

Her VM için komut tamamlandıktan sonra aşağıdaki gibi sonuçlar almanız gerekir:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Yukarıdaki komutla oluşturulan varsayılan görüntü, varsayılan olarak bir 32 işletim sistemi diski oluşturur. Bu varsayılan yükleme ile muhtemelen boş alan tükeniyor olabilirsiniz. `az vm create`Örnek olarak 128gb ile bir işletim sistemi diski oluşturmak için yukarıdaki komutuna eklenen aşağıdaki parametreyi kullanabilirsiniz: `--os-disk-size-gb 128` .
>
> Daha sonra, yüklemenize uyum sağlamak için uygun klasör birimlerini genişletmeniz gerekiyorsa [mantıksal birim Yöneticisi 'ni (LVM) yapılandırabilirsiniz](/previous-versions/azure/virtual-machines/linux/configure-lvm) .

### <a name="test-connection-to-the-created-vms"></a>Oluşturulan VM 'Lerle bağlantıyı test etme

Azure Cloud Shell ' de aşağıdaki komutu kullanarak VM1 veya diğer VM 'lere bağlanın. VM IP 'larınızı bulamıyorsanız [Azure Cloud Shell bu hızlı](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm)başlangıcı izleyin.

```azurecli-interactive
ssh <username>@publicipaddress
```

Bağlantı başarılı olursa, Linux terminalini temsil eden aşağıdaki çıktıyı görmeniz gerekir:

```output
[<username>@<VM1> ~]$
```

`exit`SSH oturumundan çıkmak için yazın.

## <a name="enable-high-availability"></a>Yüksek kullanılabilirliği etkinleştir

> [!IMPORTANT]
> Öğreticinin bu bölümünü tamamlamaya yönelik bir RHEL ve yüksek kullanılabilirlik eklentisi aboneliğine sahip olmanız gerekir. Önceki bölümde önerilen bir görüntü kullanıyorsanız, başka bir abonelik kaydetmeniz gerekmez.
 
Her bir VM düğümüne bağlanın ve HA 'yi etkinleştirmek için aşağıdaki kılavuzu izleyin. Daha fazla bilgi için bkz. [RHEL için yüksek kullanılabilirlik aboneliğini etkinleştirme](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Aynı komutların her birine aynı anda bir SSH oturumu açarsanız, makale genelinde her bir VM 'de çalıştırılması gerekir.
>
> Birden çok komutu kopyalayıp yapıştırıyorsanız `sudo` ve parola istenirse, ek komutlar çalıştırılmaz. Her komutu ayrı ayrı çalıştırın.


1. Pacemaker güvenlik duvarı bağlantı noktalarını açmak için her bir VM 'de aşağıdaki komutları çalıştırın:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Aşağıdaki komutları kullanarak pacemaker paketlerini tüm düğümlerde güncelleştirin ve bu paketleri yüklersiniz:

    > [!NOTE]
    > **Nmap** , AĞıNıZDA kullanılabilir IP adreslerini bulmak için bir araç olarak bu komut bloğunun bir parçası olarak yüklenir. **Nmap**'i yüklemek zorunda değilsiniz, ancak bu öğreticide daha sonra yararlı olacak.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Paceoluşturucu paketleri yüklenirken oluşturulan varsayılan kullanıcının parolasını ayarlayın. Tüm düğümlerde aynı parolayı kullanın.

    ```bash
    sudo passwd hacluster
    ```

1. Hosts dosyasını açmak ve konak adı çözümlemesini ayarlamak için aşağıdaki komutu kullanın. Daha fazla bilgi için bkz. Hosts dosyasını yapılandırırken [AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) 'yi yapılandırma.

    ```
    sudo vi /etc/hosts
    ```

    **VI** düzenleyicisinde `i` metin eklemek için girin ve boş bir satıra karşılık gelen VM 'nin **özel IP** 'sini ekleyin. Ardından, IP 'nin yanındaki bir alandan sonra VM adını ekleyin. Her satırın ayrı bir girişi olmalıdır.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Yukarıdaki **özel IP** adresinizi kullanmanızı öneririz. Bu yapılandırmadaki genel IP adresinin kullanılması Kurulumun başarısız olmasına neden olur ve sanal makinenizin dış ağlara sunulmasını önermiyoruz.

    **VI** düzenleyicisinden çıkmak için, önce **ESC** tuşuna basın ve ardından `:wq` dosyayı yazıp çıkmak için komutunu girin.

## <a name="create-the-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma

Bu bölümde, pcsd hizmetini etkinleştirip başlatacak ve sonra kümeyi yapılandıracağız. Linux üzerinde SQL Server için, küme kaynakları otomatik olarak oluşturulmaz. Pacemaker kaynaklarını el ile etkinleştirip oluşturmanız gerekecektir. Daha fazla bilgi için, [RHEL için yük devretme kümesi örneğini yapılandırma](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node) makalesine bakın

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Pcsd hizmeti ve Paceyapıcısı etkinleştirme ve başlatma

1. Komutları tüm düğümlerde çalıştırın. Bu komutlar, yeniden başlatmadan sonra düğümlerin kümeye yeniden eklenmesine izin verir.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Tüm düğümlerde var olan tüm küme yapılandırmalarını kaldırın. Şu komutu çalıştırın:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Birincil düğümde, kümeyi ayarlamak için aşağıdaki komutları çalıştırın.

    - `pcs cluster auth`Küme düğümlerinin kimliğini doğrulamak için komutunu çalıştırırken sizden bir parola girmeniz istenir. Daha önce oluşturulan **hacluster** kullanıcısının parolasını girin.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    RHEL 8 için düğümlerin kimliğini ayrı olarak doğrulamanız gerekecektir. İstendiğinde, **hacluster** için Kullanıcı adını ve parolayı el ile girin.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Tüm düğümlerin çevrimiçi olup olmadığını denetlemek için aşağıdaki komutu çalıştırın.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Tüm düğümler çevrimiçiyse aşağıdakine benzer bir çıktı görürsünüz:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Canlı kümede beklenen oyları 3 olarak ayarlayın. Bu komut yalnızca canlı kümeyi etkiler ve yapılandırma dosyalarını değiştirmez.

    Tüm düğümlerde beklenen oyları aşağıdaki komutla ayarlayın:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Sınırlama aracısını yapılandırma

Bir STONITH cihazı, bir çevre Aracısı sağlar. Aşağıdaki yönergeler bu öğreticide değiştirilmiştir. Daha fazla bilgi için bkz. [BIR STONITH cihazı oluşturma](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Güncelleştirildiğinden emin olmak Için Azure çit aracısının sürümünü denetleyin](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Aşağıdaki komutu kullanın:

```bash
sudo yum info fence-agents-azure-arm
```

Aşağıdaki örneğe benzer bir çıktı görmeniz gerekir.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Azure Active Directory yeni bir uygulama kaydetme
 
 1. Şuraya gidin: https://portal.azure.com
 2. [Azure Active Directory dikey penceresini](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)açın. Özellikler ' e gidin ve Dizin KIMLIĞINI yazın. Bu, `tenant ID`
 3. [ **Uygulama kayıtları** tıklayın](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. **Yeni kayıt** öğesine tıklayın
 5. Gibi bir **ad** girin `<resourceGroupName>-app` , **yalnızca bu kuruluş dizinindeki hesapları** seçin
 6. Uygulama türü **Web**' i seçin, bir oturum açma URL 'si girin (örneğin, http://localhost) Ekle ' ye tıklayın. Oturum açma URL 'SI kullanılmaz ve geçerli bir URL olabilir. İşiniz bittiğinde **Kaydet** ' e tıklayın.
 7. Yeni uygulama kaydınız için **sertifikaları ve gizli** dizileri seçip **yeni istemci parolası** ' na tıklayın.
 8. Yeni anahtar için bir açıklama girin (istemci gizli anahtarı), **hiçbir zaman süre sonu** seçeneğini belirleyip **Ekle** ' ye tıklayın
 9. Gizli dizi değerini yazın. Hizmet sorumlusu için parola olarak kullanılır
10. **Genel bakış**'ı seçin. Uygulama KIMLIĞINI yazın. Hizmet sorumlusunun Kullanıcı adı (aşağıdaki adımlarda oturum açma KIMLIĞI) olarak kullanılır
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Çit Aracısı için özel bir rol oluşturma

[Azure CLI kullanarak Azure özel rolü oluşturma](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role)öğreticisini izleyin.

JSON dosyanız aşağıdakine benzer şekilde görünmelidir:

- `<username>`İstediğiniz adla değiştirin. Bu rol tanımını oluştururken herhangi bir tekrardan kaçınmaktır.
- `<subscriptionId>`Azure ABONELIK Kimliğinizle değiştirin.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Rolü eklemek için aşağıdaki komutu çalıştırın:

- `<filename>`Dosyanın adıyla değiştirin.
- Komutu, dosyanın kaydedildiği klasörden farklı bir yoldan yürütüyorsunuz, dosyanın klasör yolunu komuta ekleyin.

```bash
az role definition create --role-definition "<filename>.json"
```

Aşağıdaki çıkışı görmeniz gerekir:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Hizmet sorumlusuna özel rol atama

`Linux Fence Agent Role-<username>`Son adımda oluşturulan özel rolü hizmet sorumlusuna atayın. Sahip rolünü artık kullanmayın!
 
1. Şuraya gidin: https://portal.azure.com
2. [Tüm kaynaklar dikey penceresini](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll) açın
3. İlk küme düğümünün sanal makinesini seçin
4. **Erişim denetimi (IAM)** öğesine tıklayın
5. **Rol ataması Ekle** ' ye tıklayın
6. `Linux Fence Agent Role-<username>` **Rol** listesinden rolü seçin
7. **Seç** listesinde, yukarıda oluşturduğunuz uygulamanın adını girin.`<resourceGroupName>-app`
8. **Kaydet**’e tıklayın
9. Tüm küme düğümü için yukarıdaki adımları tekrarlayın.

### <a name="create-the-stonith-devices"></a>STONITH cihazlarını oluşturma

Düğüm 1 ' de aşağıdaki komutları çalıştırın:

- Öğesini `<ApplicationID>` uygulama KAYDıNıZDAN kimlik değeri ile değiştirin.
- Değerini, `<servicePrincipalPassword>` istemci gizli anahtarı ile değiştirin.
- `<resourceGroupName>`Bu öğretici için kullanılan aboneliğinizdeki kaynak grubuyla değiştirin.
- `<tenantID>`Ve ' i `<subscriptionId>` Azure aboneliğinizden değiştirin.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

HA hizmetine () izin vermek için güvenlik duvarımızı zaten bir kural eklediğimiz için `--add-service=high-availability` , tüm düğümlerde şu güvenlik duvarı bağlantı noktalarını açmaya gerek yoktur: 2224, 3121, 21064, 5405. Ancak, HA ile herhangi bir türde bağlantı sorunuyla karşılaşıyorsanız, HA ile ilişkili bu bağlantı noktalarını açmak için aşağıdaki komutu kullanın.

> [!TIP]
> İsterseniz bu öğreticideki tüm bağlantı noktalarını bir kez daha ekleyebilirsiniz. Açılması gereken bağlantı noktaları aşağıdaki ilgili bölümlerinde açıklanmıştır. Tüm bağlantı noktalarını şimdi eklemek istiyorsanız, ek bağlantı noktalarını ekleyin: 1433 ve 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>SQL Server ve MSSQL araçları 'nı yükler

> [!NOTE]
> RHEL8-HA üzerinde 2019 ön yüklenmiş SQL Server VM 'Leri oluşturduysanız, tüm VM 'lerde komutunu çalıştırarak tüm VM 'lerde sa parolasını ayarladıktan sonra, SQL Server ve MSSQL-araçları yüklemek için aşağıdaki adımları atlayabilirsiniz ve **bir kullanılabilirlik grubu Yapılandır** bölümünü başlatabilirsiniz `sudo /opt/mssql/bin/mssql-conf set-sa-password` .

VM 'Lere SQL Server ve MSSQL araçları yüklemek için aşağıdaki bölümü kullanın. Aşağıdaki örneklerden birini seçerek RHEL 7 üzerine 2017 SQL Server veya RHEL 8 ' SQL Server 2019 ' ü yükleyebilirsiniz. Tüm düğümlerde bu eylemlerin her birini gerçekleştirin. Daha fazla bilgi için bkz. [Red Hat VM 'sine SQL Server](/sql/linux/quickstart-install-connect-red-hat).


### <a name="installing-sql-server-on-the-vms"></a>VM 'Lere SQL Server yükleme

Aşağıdaki komutlar SQL Server yüklemek için kullanılır:

**SQL Server 2017 ile RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**SQL Server 2019 ile RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Uzaktan bağlantılar için güvenlik duvarı bağlantı noktası 1433 ' i açın

Uzaktan bağlanmak için VM 'de bağlantı noktası 1433 ' i açmanız gerekir. Her VM 'nin güvenlik duvarında 1433 numaralı bağlantı noktasını açmak için aşağıdaki komutları kullanın:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>SQL Server komut satırı araçlarını yükleme

Aşağıdaki komutlar SQL Server komut satırı araçlarını yüklemek için kullanılır. Daha fazla bilgi için [SQL Server komut satırı araçlarını yüklemeyi](/sql/linux/quickstart-install-connect-red-hat#tools)inceleyin.

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Kolaylık sağlaması için PATH ortam değişkenine/seçenek/MSSQL-Tools/bin/ekleyin. Bu, tüm yolu belirtmeden araçları çalıştırmanızı sağlar. PATH ortam değişkenini hem oturum açma bilgileriyle başlatılan oturumları hem de etkileşimli/oturum açma bilgisi olmadan başlatılan oturumları için değiştirmek üzere aşağıdaki komutları çalıştırın:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>SQL Server durumunu kontrol edin

Yapılandırma ile işiniz bittiğinde, SQL Server durumunu denetleyebilir ve çalıştığını doğrulayabilirsiniz:

```bash
systemctl status mssql-server --no-pager
```

Aşağıdaki çıkışı görmeniz gerekir:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>Kullanılabilirlik grubunu yapılandırma

Sanal makinelerinize yönelik SQL Server Always on kullanılabilirlik grubunu yapılandırmak için aşağıdaki adımları kullanın. Daha fazla bilgi için bkz. [Linux üzerinde yüksek kullanılabilirlik için SQL Server Always on kullanılabilirlik grupları yapılandırma](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>Always on kullanılabilirlik grupları 'Nı etkinleştirin ve MSSQL-Server 'ı yeniden başlatın

SQL Server örneğini barındıran her düğümde Always on kullanılabilirlik grupları 'Nı etkinleştirin. Sonra MSSQL-Server ' ı yeniden başlatın. Şu betiği çalıştırın:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Sertifika oluşturma

Şu anda AG uç noktasında AD kimlik doğrulamasını desteklemiyoruz. Bu nedenle, AG uç noktası şifrelemesi için bir sertifika kullanılmalıdır.

1. SQL Server Management Studio (SSMS) veya SQL CMD kullanarak **tüm düğümlere** bağlanın. Bir AlwaysOn_health oturumu etkinleştirmek ve bir ana anahtar oluşturmak için aşağıdaki komutları çalıştırın:

    > [!IMPORTANT]
    > SQL Server örneğinize uzaktan bağlanıyorsanız, güvenlik duvarınız üzerinde 1433 numaralı bağlantı noktasını açmanız gerekir. Ayrıca, her VM için NSG bağlantı noktası 1433 ' e gelen bağlantılara izin vermeniz gerekir. Daha fazla bilgi için bkz. gelen güvenlik kuralı oluşturmak için [güvenlik kuralı oluşturma](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .

    - Yerine `<Master_Key_Password>` kendi parolanızı koyun.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. SSMS veya SQL CMD kullanarak birincil çoğaltmaya bağlanın. Aşağıdaki komutlar, `/var/opt/mssql/data/dbm_certificate.cer` `var/opt/mssql/data/dbm_certificate.pvk` birincil SQL Server çoğaltmaınızla ilgili olarak bir sertifika ve bir özel anahtar oluşturacaktır:

    - Yerine `<Private_Key_Password>` kendi parolanızı koyun.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Komutunu çalıştırarak SQL CMD oturumundan çıkın `exit` ve SSH oturumunuza geri dönün.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Sertifikayı ikincil çoğaltmalara kopyalayın ve sunucuda sertifikaları oluşturun

1. Oluşturulan iki dosyayı, kullanılabilirlik çoğaltmalarını barındıracak tüm sunucularda aynı konuma kopyalayın.
 
    Birincil sunucuda, `scp` sertifikayı hedef sunuculara kopyalamak için aşağıdaki komutu çalıştırın:

    - `<username>`Ve `<VM2>` kullandığınız Kullanıcı adı ve hedef VM adıyla değiştirin.
    - Tüm ikincil çoğaltmalar için bu komutu çalıştırın.

    > [!NOTE]
    > `sudo -i`Size kök ortam sağlayan, çalıştırmanız gerekmez. `sudo`Bu öğreticide daha önce yaptığımız gibi komutu yalnızca her komutun önünde çalıştırabilirsiniz.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Hedef sunucuda aşağıdaki komutu çalıştırın:

    - `<username>`Kullanıcı adınızla değiştirin.
    - `mv`Komut dosyaları veya dizini bir konumdan diğerine taşıdıkça.
    - `chown`Komut, dosya, dizin veya bağlantı sahibini ve grubunu değiştirmek için kullanılır.
    - Tüm ikincil çoğaltmalar için bu komutları çalıştırın.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Aşağıdaki Transact-SQL betiği, birincil SQL Server çoğaltmasında oluşturduğunuz yedekten bir sertifika oluşturur. Betiği güçlü parolalarla güncelleştirin. Şifre çözme parolası, önceki adımda. PVK dosyasını oluşturmak için kullandığınız paroladır. Sertifikayı oluşturmak için, tüm ikincil sunuculardaki SQL CMD veya SSMS kullanarak aşağıdaki betiği çalıştırın:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Tüm çoğaltmalarda veritabanı yansıtma uç noktalarını oluşturma

SQL CMD veya SSMS kullanarak tüm SQL Server örneklerinde aşağıdaki betiği çalıştırın:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Kullanılabilirlik grubunu oluşturma

SQL CMD veya SSMS kullanarak birincil çoğaltmayı barındıran SQL Server örneğine bağlanın. Kullanılabilirlik grubunu oluşturmak için aşağıdaki komutu çalıştırın:

- `ag1`Istediğiniz kullanılabilirlik grubu adıyla değiştirin.
- `<VM1>`, `<VM2>` Ve `<VM3>` değerlerini, çoğaltmaları barındıran SQL Server örneklerinin adlarıyla değiştirin.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Pacemaker için SQL Server oturum açma oluşturma

Tüm SQL Server örneklerinde, pacemaker için SQL Server bir oturum açma oluşturun. Aşağıdaki Transact-SQL bir oturum açma oluşturur.

- `<password>`Kendi karmaşık parolanızla değiştirin.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Tüm SQL Server örneklerinde, SQL Server oturum açmak için kullanılan kimlik bilgilerini kaydedin. 

1. Dosyayı oluşturun:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Aşağıdaki 2 satırı dosyaya ekleyin:

    ```bash
    pacemakerLogin
    <password>
    ```

    **VI** düzenleyicisinden çıkmak için, önce **ESC** tuşuna basın ve ardından `:wq` dosyayı yazıp çıkmak için komutunu girin.

1. Dosyayı yalnızca köke göre okunabilir hale getirme:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>İkincil çoğaltmaları kullanılabilirlik grubuna katın

1. İkincil çoğaltmaları AG 'ye katmak için, tüm sunucular için güvenlik duvarında 5022 numaralı bağlantı noktasını açmanız gerekir. SSH oturumunda aşağıdaki komutu çalıştırın:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. İkincil çoğaltmalarda, bu komutları AG 'ye katmak için aşağıdaki komutları çalıştırın:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Birincil çoğaltma ve her ikincil çoğaltma üzerinde aşağıdaki Transact-SQL betiğini çalıştırın:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. İkincil çoğaltmalar birleştirildikten sonra, **her zaman yüksek kullanılabilirlik** düğümünü genişleterek onları ssms Nesne Gezgini görebilirsiniz:

    ![Ekran görüntüsü, birincil ve ikincil kullanılabilirlik çoğaltmalarını gösterir.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Kullanılabilirlik grubuna bir veritabanı ekleme

[Veritabanı ekleme hakkında kullanılabilirlik grubunu yapılandır makalesini](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)takip edeceğiz.

Bu adımda aşağıdaki Transact-SQL komutları kullanılır. Bu komutları birincil çoğaltmada Çalıştır:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Veritabanının ikincil sunucularda oluşturulduğunu doğrulama

Her bir ikincil SQL Server çoğaltmasında, DB1 veritabanının oluşturulup oluşturulmamasından ve EŞITLENMIŞ durumda olup olmadığını görmek için aşağıdaki sorguyu çalıştırın:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

`synchronization_state_desc`Listeleri IÇIN eşitlendiğinde `db1` , çoğaltmaların eşitlendiği anlamına gelir. İkincil öğeler `db1` birincil çoğaltmada gösteriliyor.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Pacemaker kümesinde kullanılabilirlik grubu kaynakları oluşturma

[Pacemaker kümesinde kullanılabilirlik grubu kaynakları oluşturma](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)kılavuzunu takip edeceğiz.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan bağımlı dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

### <a name="create-the-ag-cluster-resource"></a>AG kümesi kaynağı oluşturma

1. Kaynak kullanılabilirlik grubunda oluşturmak için daha önce seçilen ortama göre aşağıdaki komutlardan birini kullanın `ag_cluster` `ag1` .

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Aşağıdaki komutu kullanmaya devam etmeden önce kaynağı denetleyip çevrimiçi olduklarından emin olun:

    ```bash
    sudo pcs resource
    ```

    Aşağıdaki çıkışı görmeniz gerekir:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Sanal IP kaynağı oluşturma

1. Sanal IP kaynağı oluşturmak için ağınızdan kullanılabilir bir statik IP adresi kullanın. Komut aracını kullanarak bir tane bulabilirsiniz `nmap` .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. **Stonith-Enabled** özelliğini false olarak ayarlayın

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Aşağıdaki komutu kullanarak sanal IP kaynağını oluşturun:

    - `<availableIP>`Aşağıdaki değeri kullanılmayan BIR IP adresi ile değiştirin.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Kısıtlama Ekle

1. IP adresinin ve AG kaynağının aynı düğümde çalıştığından emin olmak için, bir birlikte bulundurma kısıtlamasının yapılandırılması gerekir. Şu komutu çalıştırın:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. AĞ kaynağının IP adresinden önce çalışır ve çalışır olmasını sağlamak için bir sıralama kısıtlaması oluşturun. Birlikte bulundurma kısıtlaması bir sıralama kısıtlaması gösterdiği sürece bu uygulamayı zorlar.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Kısıtlamaları doğrulamak için şu komutu çalıştırın:

    ```bash
    sudo pcs constraint list --full
    ```

    Aşağıdaki çıkışı görmeniz gerekir:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Stonıth 'ı yeniden etkinleştirin

Test için hazırız. Düğüm 1 ' de aşağıdaki komutu çalıştırarak kümede stonıth 'yi yeniden etkinleştirin:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Küme durumunu denetleme

Aşağıdaki komutu kullanarak küme kaynaklarınızın durumunu kontrol edebilirsiniz:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Yük devretme testi

Yapılandırmanın şimdiye kadar başarılı olduğundan emin olmak için bir yük devretmeyi test edeceğiz. Daha fazla bilgi için bkz. [Linux 'Ta Always on kullanılabilirlik grubu yük devretmesi](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Birincil çoğaltmanın üzerinde el ile yük devretmek için aşağıdaki komutu çalıştırın `<VM2>` . `<VM2>`Sunucu adınızın değeriyle değiştirin.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   Ayrıca, kaynağı istenen bir düğüme taşımak için oluşturulan geçici kısıtlamanın otomatik olarak devre dışı bırakılması için ek bir seçenek belirtebilir ve aşağıdaki adım 2 ve 3 ' ü gerçekleştirmeniz gerekmez.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   Aşağıdaki 2 ve 3. adımları otomatik hale getirmek için, kaynak taşıma komutunun kendisindeki geçici kısıtlamayı temizlemek için başka bir alternatif de tek bir satırda birden çok komut birleştirilerek kullanılır. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. Kısıtlamaları yeniden denetederseniz, el ile yük devretme nedeniyle başka bir kısıtlamanın eklendiğini görürsünüz:
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. `cli-prefer-ag_cluster-master`Aşağıdaki komutu kullanarak kısıtlamayı kimliğiyle kaldırın:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Komutunu kullanarak küme kaynaklarınızı kontrol `sudo pcs resource` edin ve birincil Örneğin şu anda olduğunu görmeniz gerekir `<VM2>` .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Test sınırlama

Aşağıdaki komutu çalıştırarak STONITH 'ı test edebilirsiniz. İçin aşağıdaki komutu çalıştırmayı deneyin `<VM1>` `<VM3>` .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Varsayılan olarak, çit eylemi düğümü kapalı ve sonra açık duruma getirir. Düğümü yalnızca çevrimdışı duruma getirmek istiyorsanız, komutundaki seçeneğini kullanın `--off` .

Aşağıdaki çıkışı almalısınız:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Bir çit cihazını test etme hakkında daha fazla bilgi için aşağıdaki [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

SQL Server örneklerinizin bir kullanılabilirlik grubu dinleyicisine yararlanmak için bir yük dengeleyici oluşturmanız ve yapılandırmanız gerekir.

> [!div class="nextstepaction"]
> [Öğretici: Azure 'da RHEL sanal makinelerinde SQL Server için bir kullanılabilirlik grubu dinleyicisi yapılandırma](rhel-high-availability-listener-tutorial.md)
