---
title: Kullanılabilirlik grubu yapılandırma (PowerShell & az CLı)
description: Windows Yük devretme kümesi, kullanılabilirlik grubu dinleyicisi ve Azure 'daki bir SQL Server VM iç yük dengeleyici oluşturmak için PowerShell veya Azure CLı kullanın.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: ffd4ec6eff94589abbc8af70ecf9c0f7dc168962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766942"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Azure VM 'de SQL Server için bir kullanılabilirlik grubu yapılandırmak için PowerShell veya az CLı kullanın 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, [PowerShell](/powershell/scripting/install/installing-powershell) veya [Azure CLI](/cli/azure/sql/vm) kullanarak bir Windows Yük devretme kümesi dağıtma, kümeye SQL Server VM ekleme ve her zaman açık kullanılabilirlik grubu için iç yük dengeleyici ve dinleyici oluşturma açıklanır. 

Kullanılabilirlik grubunun dağıtımı SQL Server Management Studio (SSMS) veya Transact-SQL (T-SQL) aracılığıyla el ile yapılır. 

Bu makalede, kullanılabilirlik grubu ortamını yapılandırmak için PowerShell ve az CLı kullanılırken, ayrıca [Azure Portal](availability-group-azure-portal-configure.md), [Azure hızlı başlangıç şablonları](availability-group-quickstart-template-configure.md)veya [el ile](availability-group-manually-configure-tutorial.md) de yapılabilir. 

## <a name="prerequisites"></a>Önkoşullar

Her zaman açık kullanılabilirlik grubunu yapılandırmak için aşağıdaki önkoşullara sahip olmanız gerekir: 

- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- Etki alanı denetleyicisi olan bir kaynak grubu. 
- Azure 'daki bir veya daha fazla etki alanına katılmış VM, *aynı* kullanılabilirlik KÜMESINDE veya [SQL IaaS Aracısı Uzantısı ile kaydedilmiş](sql-agent-extension-manually-register-single-vm.md) *farklı* kullanılabilirlik bölgelerinde [SQL Server 2016 (veya üzeri) Enterprise Edition çalıştırıyor](./create-sql-vm-portal.md) .  
- [PowerShell](/powershell/scripting/install/installing-powershell) veya [Azure CLI](/cli/azure/install-azure-cli)'nin en son sürümü. 
- Kullanılabilir iki (herhangi bir varlık tarafından kullanılmayan) IP adresleri. Bunlardan biri iç yük dengeleyiciye yöneliktir. Diğeri, kullanılabilirlik grubu ile aynı alt ağda bulunan kullanılabilirlik grubu dinleyicisine yöneliktir. Var olan bir yük dengeleyiciyi kullanıyorsanız, kullanılabilirlik grubu dinleyicisi için yalnızca bir kullanılabilir IP adresine sahip olmanız gerekir. 

## <a name="permissions"></a>İzinler

Azure CLı kullanarak Always on kullanılabilirlik grubunu yapılandırmak için aşağıdaki hesap izinlerine sahip olmanız gerekir: 

- Etki alanında **bilgisayar nesnesi oluşturma** iznine sahip olan mevcut bir etki alanı kullanıcı hesabı. Örneğin, bir etki alanı yönetici hesabı genellikle yeterli izne sahiptir (örneğin: account@domain.com ). _Bu hesap, kümeyi oluşturmak için her VM 'de yerel yönetici grubunun da bir parçası olmalıdır._
- SQL Server denetleyen etki alanı kullanıcı hesabı. 
 
## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma 

Kümenin bulut tanığı olarak davranması için bir depolama hesabı olması gerekir. Mevcut bir depolama hesabını kullanabilir veya yeni bir depolama hesabı oluşturabilirsiniz. Mevcut bir depolama hesabını kullanmak istiyorsanız, sonraki bölüme atlayın. 

Aşağıdaki kod parçacığı depolama hesabını oluşturur: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> `az sql: 'vm' is not in the 'az sql' command group`Azure CLI 'nın eski bir sürümünü kullanıyorsanız bu hatayı görebilirsiniz. Bu hatayı almak için [Azure CLI 'nın en son sürümünü](/cli/azure/install-azure-cli-windows) indirin.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Küme meta verilerini tanımlama

Azure CLı [az SQL VM Group](/cli/azure/sql/vm/group) komut grubu, kullanılabilirlik grubunu barındıran Windows Server yük devretme KÜMESI (wsfc) hizmetinin meta verilerini yönetir. Küme meta verileri Active Directory etki alanını, küme hesaplarını, bulut tanığı olarak kullanılacak depolama hesaplarını ve SQL Server sürümünü içerir. İlk SQL Server VM eklendiğinde kümenin tanımlandığı şekilde oluşturulması için WSFC meta verilerini tanımlamak için [az SQL VM Group Create](/cli/azure/sql/vm/group#az_sql_vm_group_create) kullanın. 

Aşağıdaki kod parçacığı, küme için meta verileri tanımlar:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Kümeye VM ekleme

Kümeye ilk SQL Server VM eklendiğinde küme oluşturulur. [Az SQL VM Add-Group](/cli/azure/sql/vm#az_sql-vm_add_to_group) komutu, kümeyi daha önce verilen adla oluşturur, küme rolünü SQL Server VM 'lerine ekler ve kümeye ekler. Komutun sonraki kullanımları `az sql vm add-to-group` Yeni oluşturulan kümeye daha fazla SQL Server VM ekler. 

Aşağıdaki kod parçacığı kümeyi oluşturur ve ilk SQL Server VM buna ekler: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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
Kümeye diğer SQL Server VM 'Leri eklemek için bu komutu kullanın. Yalnızca `-n` SQL Server VM adı için parametreyi değiştirin. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Kümeyi Doğrula 

Bir yük devretme kümesinin Microsoft tarafından desteklenmesi için, küme doğrulamasını geçmesi gerekir. Uzak Masaüstü Protokolü (RDP) gibi tercih ettiğiniz yöntemi kullanarak VM 'ye bağlanın ve daha sonra devam etmeden önce kümenizin doğrulamayı geçirdiğini doğrulayın. Bunun başarısız olması, kümenizi desteklenmeyen bir durumda bırakır. 

Yük Devretme Kümesi Yöneticisi (FCM) kullanarak kümeyi veya aşağıdaki PowerShell komutunu kullanabilirsiniz:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Kullanılabilirlik grubu oluştur

Kullanılabilirlik grubunu, [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)veya [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)kullanarak normal şekilde yaptığınız şekilde el ile oluşturun. 

>[!IMPORTANT]
> Aşağıdaki bölümlerde Azure CLı üzerinden yapıldığından, şu anda bir *dinleyici oluşturmayın.*  

## <a name="create-internal-load-balancer"></a>İç yük dengeleyici oluştur

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Always on kullanılabilirlik grubu dinleyicisi bir Azure Load Balancer iç örneğini gerektirir. İç yük dengeleyici, daha hızlı yük devretme ve yeniden bağlantı sağlayan kullanılabilirlik grubu dinleyicisi için "kayan" bir IP adresi sağlar. Bir kullanılabilirlik grubundaki SQL Server VM 'Ler aynı Kullanılabilirlik kümesinin parçasıysa, temel bir yük dengeleyici kullanabilirsiniz. Aksi takdirde, standart yük dengeleyici kullanmanız gerekir.  

> [!NOTE]
> İç yük dengeleyici SQL Server VM örneklerle aynı sanal ağda olmalıdır. 

Aşağıdaki kod parçacığı iç yük dengeleyiciyi oluşturur:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> Her bir SQL Server VM genel IP kaynağının standart yük dengeleyiciye uyumlu olması için standart bir SKU 'SU olmalıdır. SANAL makinenizin genel IP kaynağının SKU 'sunu belirlemek için, **kaynak grubu**' na gidin, istediğiniz SQL Server VM IÇIN **genel IP adresi** kaynağınızı seçin ve **genel bakış** bölmesinde **SKU** altındaki değeri bulun.  

## <a name="create-listener"></a>Dinleyici oluştur

Kullanılabilirlik grubunu el ile oluşturduktan sonra [az SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create)kullanarak dinleyiciyi oluşturabilirsiniz. 

*Alt ağ kaynak kimliği* , `/subnets/<subnetname>` sanal ağ kaynağının kaynak kimliğine eklenen değerdir. Alt ağ kaynak KIMLIĞINI belirlemek için:
   1. [Azure Portal](https://portal.azure.com)kaynak grubunuza gidin. 
   1. Sanal ağ kaynağını seçin. 
   1. **Ayarlar** bölmesinde **Özellikler** ' i seçin. 
   1. Sanal ağın kaynak KIMLIĞINI belirleyip `/subnets/<subnetname>` alt ağ kaynak kimliği oluşturmak için sonuna ekleyin. Örnek:
      - Sanal ağ kaynak KIMLIĞINIZ: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Alt ağ adınız: `default`
      - Bu nedenle, alt ağ kaynak KIMLIĞINIZ: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Aşağıdaki kod parçacığı, kullanılabilirlik grubu dinleyicisini oluşturur:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Kopyaların sayısını değiştirme 
Azure 'da barındırılan SQL Server VM 'lere bir kullanılabilirlik grubu dağıttığınızda bir karmaşıklık katmanı daha vardır. Kaynak sağlayıcısı ve sanal makine grubu artık kaynakları yönetir. Bu nedenle, kullanılabilirlik grubunda çoğaltmalar eklerken veya kaldırırken, SQL Server VM 'Ler hakkında bilgi içeren dinleyici meta verilerini güncelleştirme hakkında ek bir adım vardır. Kullanılabilirlik grubundaki çoğaltmaların sayısını değiştirirken, dinleyiciyi SQL Server VM 'lerin meta verileriyle güncelleştirmek için [az SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) komutunu da kullanmanız gerekir. 


### <a name="add-a-replica"></a>Çoğaltma ekleme

Kullanılabilirlik grubuna yeni bir çoğaltma eklemek için:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. SQL Server VM küme grubuna ekleyin:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. SQL Server VM küme grubuna ekleyin:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. SQL Server örneğini kullanılabilirlik grubu içinde bir çoğaltma olarak eklemek için SQL Server Management Studio kullanın.
1. SQL Server VM meta verilerini dinleyiciye ekleyin:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Bir çoğaltmayı kaldırma

Bir çoğaltmayı kullanılabilirlik grubundan kaldırmak için:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. SQL Server Management Studio kullanarak çoğaltmayı kullanılabilirlik grubundan kaldırın. 
1. SQL Server VM meta verilerini dinleyicisinden kaldırın:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. SQL Server VM kümeden kaldırın:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Dinleyiciyi kaldır
Azure CLı ile yapılandırılmış kullanılabilirlik grubu dinleyicisini daha sonra kaldırmanız gerekirse, SQL IaaS Aracısı uzantısı ' na gitmeniz gerekir. Dinleyici SQL IaaS Aracısı uzantısı aracılığıyla kaydedildiğinden, SQL Server Management Studio aracılığıyla silmeniz yeterlidir. 

En iyi yöntem, Azure CLı 'de aşağıdaki kod parçacığını kullanarak SQL IaaS Aracısı uzantısı aracılığıyla silmektir. Bunun yapılması, kullanılabilirlik grubu dinleyicisi meta verilerini SQL IaaS Aracısı uzantısı 'ndan kaldırır. Ayrıca, dinleyiciyi kullanılabilirlik grubundan fiziksel olarak siler. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Kümeyi kaldır

Kümeyi yok etmek için kümeden tüm düğümleri kaldırın ve ardından SQL IaaS Aracısı uzantısından küme meta verilerini kaldırın. Azure CLı veya PowerShell kullanarak bunu yapabilirsiniz. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İlk olarak, SQL Server VM 'lerin tümünü kümeden kaldırın: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Bunlar kümedeki tek VM 'lardır, küme yok edilir. Kümeden kaldırılan SQL Server VM 'lerden ayrı başka VM 'Ler varsa, diğer VM 'Ler kaldırılmaz ve küme yok edilmez. 

Sonra, SQL IaaS Aracısı uzantısından küme meta verilerini kaldırın: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

İlk olarak, SQL Server VM 'lerin tümünü kümeden kaldırın. Bu, düğümleri fiziksel olarak kümeden kaldırır ve kümeyi yok eder: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Bunlar kümedeki tek VM 'lardır, küme yok edilir. Kümeden kaldırılan SQL Server VM 'lerden ayrı başka VM 'Ler varsa, diğer VM 'Ler kaldırılmaz ve küme yok edilmez. 

Sonra, SQL IaaS Aracısı uzantısından küme meta verilerini kaldırın: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri inceleyin: 

* [SQL Server VM 'lerine genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 'Ler için SSS](frequently-asked-questions-faq.md)
* [SQL Server VM 'Ler için sürüm notları](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM için lisans modellerini değiştirme](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [&#40;SQL Server her zaman açık kullanılabilirlik gruplarına genel bakış&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Her zaman açık kullanılabilirlik grupları için sunucu örneğinin yapılandırması &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Kullanılabilirlik grubu &#40;SQL Server Yönetimi&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Kullanılabilirlik gruplarının izlenmesi &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Her zaman açık kullanılabilirlik grupları için Transact-SQL deyimlerine genel bakış &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Her zaman açık kullanılabilirlik grupları için PowerShell cmdlet 'lerine genel bakış &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)