---
title: Kullanılabilirlik grubu yapılandırma (Azure CLı)
description: Windows Yük devretme kümesi, kullanılabilirlik grubu dinleyicisi ve Azure 'daki bir SQL Server VM iç yük dengeleyici oluşturmak için Azure CLı 'yi kullanın.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022360"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure sanal makinesinde SQL Server için her zaman açık kullanılabilirlik grubu yapılandırmak üzere Azure CLı 'yi kullanma
Bu makalede, [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) kullanarak bir Windows Yük devretme kümesi dağıtma, kümeye SQL Server VM ekleme, her zaman açık kullanılabilirlik grubu için iç yük dengeleyici ve dinleyici oluşturma işlemlerinin nasıl yapılacağı açıklanır. Always on kullanılabilirlik grubunun dağıtımı SQL Server Management Studio (SSMS) üzerinden el ile yapılır. 

## <a name="prerequisites"></a>Önkoşullar
Azure CLı kullanarak her zaman açık kullanılabilirlik grubunun kurulumunu otomatik hale getirmek için aşağıdaki önkoşullara sahip olmanız gerekir: 
- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- Etki alanı denetleyicisi olan bir kaynak grubu. 
- Azure 'daki bir veya daha fazla etki alanına katılmış VM, *aynı Kullanılabilirlik kümesinde veya* [SQL VM kaynak sağlayıcısına kaydedilmiş](virtual-machines-windows-sql-register-with-resource-provider.md)farklı kullanılabilirlik bölgelerinde [SQL Server 2016 (veya üzeri) Enterprise Edition çalıştırıyor](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) .  
- [Azure CLI](/cli/azure/install-azure-cli). 
- Kullanılabilir iki (herhangi bir varlık tarafından kullanılmayan) IP adresleri. Bunlardan biri iç yük dengeleyiciye yöneliktir. Diğeri, kullanılabilirlik grubu ile aynı alt ağda bulunan kullanılabilirlik grubu dinleyicisine yöneliktir. Var olan bir yük dengeleyiciyi kullanıyorsanız, kullanılabilirlik grubu dinleyicisi için yalnızca bir kullanılabilir IP adresine sahip olmanız gerekir. 

## <a name="permissions"></a>İzinler
Azure CLı kullanarak Always on kullanılabilirlik grubunu yapılandırmak için aşağıdaki hesap izinlerine sahip olmanız gerekir: 

- Etki alanında **bilgisayar nesnesi oluşturma** iznine sahip olan mevcut bir etki alanı kullanıcı hesabı. Örneğin, bir etki alanı yönetici hesabı genellikle yeterli izne sahiptir (örneğin: account@domain.com). _Bu hesap, kümeyi oluşturmak için her VM 'de yerel yönetici grubunun da bir parçası olmalıdır._
- SQL Server hizmetini denetleyen etki alanı kullanıcı hesabı. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>1\. Adım: bulut tanığı olarak depolama hesabı oluşturma
Kümenin bulut tanığı olarak davranması için bir depolama hesabı olması gerekir. Mevcut bir depolama hesabını kullanabilir veya yeni bir depolama hesabı oluşturabilirsiniz. Mevcut bir depolama hesabını kullanmak istiyorsanız, sonraki bölüme atlayın. 

Aşağıdaki kod parçacığı depolama hesabını oluşturur: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Azure CLı 'nın eski bir sürümünü kullanıyorsanız hata `az sql: 'vm' is not in the 'az sql' command group` görebilirsiniz. Bu hatayı almak için [Azure CLI 'nın en son sürümünü](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) indirin.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>2\. Adım: Windows Yük devretme kümesi meta verilerini tanımlama
Azure CLı [az SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) komut grubu, kullanılabilirlik grubunu barındıran Windows Server yük devretme KÜMESI (wsfc) hizmetinin meta verilerini yönetir. Küme meta verileri Active Directory etki alanını, küme hesaplarını, bulut tanığı olarak kullanılacak depolama hesaplarını ve SQL Server sürümünü içerir. İlk SQL Server VM eklendiğinde kümenin tanımlandığı şekilde oluşturulması için WSFC meta verilerini tanımlamak için [az SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) kullanın. 

Aşağıdaki kod parçacığı, küme için meta verileri tanımlar:
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>3\. Adım: kümeye SQL Server VM ekleme
Kümeye ilk SQL Server VM eklendiğinde küme oluşturulur. [Az SQL VM Add-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) komutu, kümeyi daha önce verilen adla oluşturur, küme rolünü SQL Server VM 'lerine ekler ve kümeye ekler. `az sql vm add-to-group` komutunun sonraki kullanımları yeni oluşturulan kümeye daha fazla SQL Server VM ekler. 

Aşağıdaki kod parçacığı kümeyi oluşturur ve ilk SQL Server VM buna ekler: 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Kümeye diğer SQL Server VM 'Leri eklemek için bu komutu kullanın. Yalnızca SQL Server VM adı için `-n` parametresini değiştirin. 

## <a name="step-4-create-the-availability-group"></a>4\. Adım: kullanılabilirlik grubunu oluşturma
Kullanılabilirlik grubunu, [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)veya [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)kullanarak normal şekilde yaptığınız şekilde el ile oluşturun. 

>[!IMPORTANT]
> Aşağıdaki bölümlerde Azure CLı üzerinden yapıldığından, şu anda bir *dinleyici oluşturmayın.*  

## <a name="step-5-create-the-internal-load-balancer"></a>5\. Adım: iç yük dengeleyici oluşturma

Always on kullanılabilirlik grubu dinleyicisi bir Azure Load Balancer iç örneğini gerektirir. İç yük dengeleyici, daha hızlı yük devretme ve yeniden bağlantı sağlayan kullanılabilirlik grubu dinleyicisi için "kayan" bir IP adresi sağlar. Bir kullanılabilirlik grubundaki SQL Server VM 'Ler aynı Kullanılabilirlik kümesinin parçasıysa, temel bir yük dengeleyici kullanabilirsiniz. Aksi takdirde, standart yük dengeleyici kullanmanız gerekir.  

> [!NOTE]
> İç yük dengeleyici SQL Server VM örneklerle aynı sanal ağda olmalıdır. 

Aşağıdaki kod parçacığı iç yük dengeleyiciyi oluşturur:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Her bir SQL Server VM genel IP kaynağının standart yük dengeleyiciye uyumlu olması için standart bir SKU 'SU olmalıdır. SANAL makinenizin genel IP kaynağının SKU 'sunu belirlemek için, **kaynak grubu**' na gidin, istediğiniz SQL Server VM IÇIN **genel IP adresi** kaynağınızı seçin ve **genel bakış** bölmesinde **SKU** altındaki değeri bulun.  

## <a name="step-6-create-the-availability-group-listener"></a>6\. Adım: kullanılabilirlik grubu dinleyicisini oluşturma
Kullanılabilirlik grubunu el ile oluşturduktan sonra [az SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)kullanarak dinleyiciyi oluşturabilirsiniz. 

*Alt ağ kaynak kimliği* , sanal ağ KAYNAĞıNıN kaynak kimliğine eklenen `/subnets/<subnetname>` değeridir. Alt ağ kaynak KIMLIĞINI belirlemek için:
   1. [Azure Portal](https://portal.azure.com)kaynak grubunuza gidin. 
   1. Sanal ağ kaynağını seçin. 
   1. **Ayarlar** bölmesinde **Özellikler** ' i seçin. 
   1. Sanal ağın kaynak KIMLIĞINI belirleyip alt ağ kaynak KIMLIĞINI oluşturmak için `/subnets/<subnetname>` sonuna ekleyin. Örneğin:
      - Sanal ağ kaynak KIMLIĞINIZ: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Alt ağ adınız: `default`
      - Bu nedenle, alt ağ kaynak KIMLIĞINIZ: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Aşağıdaki kod parçacığı, kullanılabilirlik grubu dinleyicisini oluşturur:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Bir kullanılabilirlik grubundaki çoğaltmaların sayısını değiştirme
Azure 'da barındırılan SQL Server VM 'lere bir kullanılabilirlik grubu dağıttığınızda bir karmaşıklık katmanı daha vardır. Kaynak sağlayıcısı ve sanal makine grubu artık kaynakları yönetir. Bu nedenle, kullanılabilirlik grubunda çoğaltmalar eklerken veya kaldırırken, SQL Server VM 'Ler hakkında bilgi içeren dinleyici meta verilerini güncelleştirme hakkında ek bir adım vardır. Kullanılabilirlik grubundaki çoğaltmaların sayısını değiştirirken, dinleyiciyi SQL Server VM 'lerin meta verileriyle güncelleştirmek için [az SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) komutunu da kullanmanız gerekir. 


### <a name="add-a-replica"></a>Çoğaltma ekleme

Kullanılabilirlik grubuna yeni bir çoğaltma eklemek için:

1. Kümeye SQL Server VM ekleyin:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. SQL Server örneğini kullanılabilirlik grubu içinde bir çoğaltma olarak eklemek için SQL Server Management Studio kullanın.
1. SQL Server VM meta verilerini dinleyiciye ekleyin:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Bir çoğaltmayı kaldırma

Bir çoğaltmayı kullanılabilirlik grubundan kaldırmak için:

1. SQL Server Management Studio kullanarak çoğaltmayı kullanılabilirlik grubundan kaldırın. 
1. SQL Server VM meta verilerini dinleyicisinden kaldırın:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. SQL Server VM kümeden kaldırın:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisini kaldır
Azure CLı ile yapılandırılmış kullanılabilirlik grubu dinleyicisini daha sonra kaldırmanız gerekirse, SQL VM kaynak sağlayıcısı ' na gitmeniz gerekir. Dinleyici SQL VM kaynak sağlayıcısı aracılığıyla kaydedildiğinden, SQL Server Management Studio aracılığıyla silmeniz yeterlidir. 

En iyi yöntem, Azure CLı 'de aşağıdaki kod parçacığını kullanarak SQL VM kaynak sağlayıcısı aracılığıyla bunu silmektir. Bunun yapılması, kullanılabilirlik grubu dinleyicisi meta verilerini SQL VM kaynak sağlayıcısından kaldırır. Ayrıca, dinleyiciyi kullanılabilirlik grubundan fiziksel olarak siler. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [SQL Server VM 'lerine genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM 'Ler için SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM 'Ler için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM için lisans modellerini değiştirme](virtual-machines-windows-sql-ahb.md)
* [Always on kullanılabilirlik gruplarına &#40;genel bakış SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always on kullanılabilirlik grupları &#40;için bir sunucu örneği yapılandırması SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Bir kullanılabilirlik grubunun &#40;yönetimi SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Kullanılabilirlik gruplarının &#40;izlenmesi SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always on kullanılabilirlik grupları &#40;için Transact-SQL deyimlerine genel bakış SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always on kullanılabilirlik grupları &#40;için PowerShell cmdlet 'lerine genel bakış SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
