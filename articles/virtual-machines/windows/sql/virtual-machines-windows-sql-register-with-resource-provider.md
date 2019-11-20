---
title: Azure 'da SQL Server sanal makinesini SQL VM kaynak sağlayıcısıyla kaydetme | Microsoft Docs
description: Yönetilebilirlik arttırmak için SQL Server VM SQL VM kaynak sağlayıcısı ile kaydedin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 433480b4a587b3a085c3b1c0ba4122ae98eb4508
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897711"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Azure 'da SQL Server sanal makinesini SQL VM kaynak sağlayıcısıyla kaydetme

Bu makalede, SQL VM kaynak sağlayıcısı ile Azure 'da SQL Server sanal makinenizin (VM) nasıl kaydedileceği açıklanmaktadır. 

Azure portal üzerinden SQL Server VM Azure Marketi görüntüsünün dağıtımı, SQL Server VM kaynak sağlayıcısıyla otomatik olarak kaydeder. Azure Market ' ten bir görüntü seçmek yerine bir Azure sanal makinesine SQL Server kendi kendine yüklemeyi tercih ediyorsanız veya SQL Server olan özel bir VHD 'den bir Azure VM sağladıysanız, SQL Server VM için kaynak sağlayıcısına kaydolmanız gerekir :

- **Lisans yönetimini basitleştirme**: Microsoft Ürün koşullarına göre, müşteriler [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullandıklarında Microsoft 'a başvurmalıdır. SQL VM kaynak sağlayıcısı 'na kaydolmak, lisans yönetimini SQL Server basitleştirir ve [portalda](virtual-machines-windows-sql-manage-portal.md) Azure Hibrit Avantajı kullanarak SQL Server VM 'leri hızlı bir şekilde tanımlamanızı sağlar veya az CLI: 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **Özellik avantajları**: SQL Server VM kaynak sağlayıcıyla kaydetme, [otomatik düzeltme eki uygulama](virtual-machines-windows-sql-automated-patching.md), [otomatik yedekleme](virtual-machines-windows-sql-automated-backup-v2.md)ve izleme ve yönetilebilirlik yeteneklerini kaldırır. Ayrıca [lisanslama](virtual-machines-windows-sql-ahb.md) ve [Sürüm](virtual-machines-windows-sql-change-edition.md) esnekliğini de kaldırır. Daha önce, bu özellikler yalnızca Azure Marketi 'ndeki SQL Server VM görüntülerle sunulmaktadır.

- **Ücretsiz yönetim**: SQL VM kaynak sağlayıcısına kaydolma ve tüm yönetilebilirlik modları tamamen ücretsizdir. Kaynak sağlayıcısıyla veya değişen yönetim modlarıyla ilişkili ek bir maliyet yoktur. 

SQL VM kaynak sağlayıcısını kullanmak için, aboneliğiniz ile SQL VM kaynak sağlayıcısını da kaydetmeniz gerekir. Azure portal, Azure CLı veya PowerShell kullanarak bunu yapabilirsiniz. 

  > [!NOTE]
  > Kaynak sağlayıcısıyla kayıt ile ilişkili ek lisanslama gereksinimi yoktur. SQL VM kaynak sağlayıcısına kaydolmak, Microsoft 'un her kaynak için lisanslama kayıt formlarını yönetme yerinde Azure Hibrit Avantajı etkinleştirilmiş olduğunu bildirmek için basitleştirilmiş bir yöntem sunar. 

SQL VM kaynak sağlayıcısını kullanmanın avantajları hakkında daha fazla bilgi için aşağıdaki [Channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) videosunu inceleyin: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Azure 'da SQL Server kendi kendine yüklerken SQL VM kaynak sağlayıcısından avantaj-Microsoft Channel 9 videosu"></iframe>


## <a name="prerequisites"></a>Önkoşullar

SQL Server VM kaynak sağlayıcısına kaydetmek için şunlar gerekir: 

- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- Bir [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) veya [PowerShell](/powershell/azure/new-azureps-module-az)'in en son sürümü. 


## <a name="register-with-sql-vm-resource-provider"></a>SQL VM kaynak sağlayıcısına kaydolun
[SQL Server IaaS Aracısı UZANTıSı](virtual-machines-windows-sql-server-agent-extension.md) VM 'de yüklü değilse, basit SQL yönetim modunu BELIRTEREK SQL VM kaynak sağlayıcısına kaydolabilirsiniz. 

Kayıt işlemi sırasında hafif belirtildiğinde, SQL VM kaynak sağlayıcısı SQL IaaS uzantısını [hafif modda](#change-management-modes) otomatik olarak yükleyecek ve SQL Server örnek meta verilerini doğrulayacaktır; Bu, SQL Server hizmeti 'ni yeniden başlatmaz. SQL VM kaynak sağlayıcısı 'nı ' PAYG ' veya ' AHUB ' olarak kaydederken istediğiniz SQL Server Lisansı türünü sağlamanız gerekir.

SQL VM kaynak sağlayıcısı ile hafif modda kaydolmak uyumluluk sağlar ve esnek lisansın yanı sıra yerinde SQL Server Edition güncelleştirmelerini etkinleştirir. Yük devretme kümesi örnekleri ve çok örnekli dağıtımlar, yalnızca hafif modda SQL VM kaynak sağlayıcısı ile kaydedilebilir. Dilediğiniz zaman tam yönetim moduna [yükseltebilirsiniz](#change-management-modes) , ancak bunu yapmak SQL Server hizmetini yeniden başlatır. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
SQL Server IaaS uzantısı VM 'de zaten yüklüyse SQL VM kaynak sağlayıcısına kaydolmak için aşağıdaki kod parçacığını kullanın. SQL VM kaynak sağlayıcısı ile kaydolurken istediğiniz SQL Server Lisansı türünü sağlamanız gerekir: Kullandıkça Öde (`PAYG`) veya Azure Hibrit Avantajı (`AHUB`). 

Aşağıdaki PowerShell kod parçacığını kullanarak SQL Server VM kaydedin:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLı](#tab/bash)

Ücretli sürümler için (kurumsal veya standart):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

Ücretsiz sürümler için (Geliştirici, Web veya Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

SQL IaaS uzantısı VM 'ye el ile yüklenmişse, yalnızca Microsoft. SqlVirtualMachine/SqlVirtualMachines türünde bir meta veri kaynağı oluşturarak SQL VM kaynak sağlayıcısı 'nı tam modda kaydedebilirsiniz. SQL IaaS uzantısı VM 'de zaten yüklüyse SQL VM kaynak sağlayıcısı 'na kaydolmak için kod parçacığı aşağıda verilmiştir. ' PAYG ' veya ' AHUB ' olarak istenen SQL Server Lisansı türünü sağlamanız gerekir. Tam yönetim modunda kaydolmak için aşağıdaki PowerShell komutunu kullanın:

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Windows Server 2008 VM 'lerine SQL Server 2008 veya 2008 R2 kaydetme

Windows Server 2008 ' de yüklü olan SQL Server 2008 ve 2008 R2, [Aracı olmayan modda](#change-management-modes)SQL VM kaynak sağlayıcısı ile kaydedilebilir. Bu seçenek uyumluluk sağlar ve Azure portal sınırlı işlevlerle birlikte SQL Server VM izlenmesini sağlar.

Aşağıdaki tabloda, kayıt sırasında belirtilen parametreler için kabul edilebilir değerlerin ayrıntıları verilmiştir:

| Parametre | Kabul edilebilir değerler                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` veya `PAYG`                    |
| **Sqlimageteklifini**  | `SQL2008-WS2008` veya `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


SQL Server 2008 veya 2008 R2 örneğinizi Windows Server 2008 örneğine kaydetmek için aşağıdaki PowerShell veya az CLı kod parçacığını kullanın:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLı](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Kayıt durumunu doğrula
SQL Server VM Azure portal, Azure CLı veya PowerShell kullanarak SQL VM kaynak sağlayıcısı ile zaten kaydedilmiş olup olmadığını doğrulayabilirsiniz. 

### <a name="azure-portal"></a>Azure portalı 

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. 
1. [SQL Server sanal makinelerinize](virtual-machines-windows-sql-manage-portal.md)gidin.
1. Listeden SQL Server VM seçin. SQL Server VM burada listelenmiyorsa, büyük olasılıkla SQL VM kaynak sağlayıcısına kayıtlı değildir. 
1. **Durum**altındaki değeri görüntüleyin. **Durum** **başarılı**olursa, SQL Server VM SQL VM kaynak sağlayıcısına başarıyla kaydedildi. 

![SQL RP kaydıyla durumu doğrulama](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Komut satırı

Az CLı veya PowerShell kullanarak geçerli SQL Server VM kayıt durumunu doğrulayın. `ProvisioningState`, kayıt başarılı olursa `Succeeded` gösterir. 

# <a name="az-clitabbash"></a>[AZ CLı](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Bir hata, SQL Server VM kaynak sağlayıcıya kaydedilmemiş olduğunu gösterir. 

## <a name="change-management-modes"></a>Değişiklik yönetimi modları

SQL Server IaaS uzantısı için üç ücretsiz yönetilebilirlik modu vardır: 

- **Tam** mod tüm işlevleri sunar, ancak SQL Server ve Sistem Yöneticisi izinlerinin yeniden başlatılmasını gerektirir. Bu, varsayılan olarak yüklenen seçenektir. Tek bir örnekle SQL Server VM yönetmek için kullanın. Tam mod belleği ve CPU üzerinde en az etkisi olan iki Windows hizmeti yüklüyor. Bunlar, Görev Yöneticisi aracılığıyla izlenebilir. Tüm yönetilebilirlik modunu kullanmayla ilişkili bir maliyet yoktur. 

- **Hafif** SQL Server yeniden başlatılmasını gerektirmez, ancak yalnızca SQL Server lisans türünü ve sürümünü değiştirmeyi destekler. Birden çok örneğe sahip SQL Server VM 'Ler veya bir yük devretme kümesi örneğine (FCı) katılmak için bu seçeneği kullanın. Hafif mod kullanılırken bellek veya CPU üzerinde bir etkisi yoktur. Hafif yönetilebilirlik modunun kullanılmasıyla ilişkili bir maliyet yoktur. 

- **Noagent** , Windows Server 2008 ' de yüklü SQL Server 2008 ve SQL Server 2008 R2 için ayrılmıştır. NoAgent modu kullanılırken bellek veya CPU üzerinde bir etkisi yoktur. NoAgent yönetilebilirlik modunu kullanmayla ilişkili bir maliyet yoktur. 

PowerShell kullanarak SQL Server IaaS aracınızın geçerli modunu görüntüleyebilirsiniz: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

*Hafif* IaaS uzantısının yüklü olduğu SQL Server VM 'ler, Azure Portal kullanarak modu _tam_ olarak yükseltebilir. _Aracı olmayan_ bir modda SQL Server VM 'ler, Işletim sistemi Windows 2008 R2 ve üzeri sürümlere yükseltildikten sonra _tam_ olarak yükseltilebilir. Bunun için düşürme yapılamaz, SQL VM kaynağını silerek SQL Server VM SQL VM kaynak sağlayıcısı 'ndan [kaydını](#unregister-vm-from-resource-provider) KALDıRMANıZ ve SQL VM kaynak sağlayıcısına yeniden kaydolmanız gerekir. 

Aracı modunu tam olarak yükseltmek için: 


### <a name="azure-portal"></a>Azure portalı

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. [SQL sanal makineler](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) kaynağına gidin. 
1. SQL Server sanal makinenizi seçin ve **genel bakış**' ı seçin. 
1. NoAgent veya Lightweight IaaS modundaki sanal makineler SQL Server için, **SQL IaaS uzantı Iletisiyle tek lisans türünü seçin ve sürüm güncelleştirmelerini** seçin.

   ![Portalın modunu değiştirme seçimleri](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. **Sanal makinede SQL Server hizmetini yeniden başlatmayı kabul** ediyorum onay kutusunu seçin ve ardından IaaS modumu tam olarak yükseltmek için **Onayla** ' yı seçin. 

    ![Sanal makinede SQL Server hizmetini yeniden başlatmak için kabul etmiş onay kutusu](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Komut satırı

# <a name="az-clitabbash"></a>[AZ CLı](#tab/bash)

Şu az CLı kod parçacığını çalıştırın:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki PowerShell kod parçacığını çalıştırın:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-subscription-with-rp"></a>Aboneliği RP ile kaydetme

SQL Server VM SQL VM kaynak sağlayıcısına kaydetmek için öncelikle aboneliğinizi kaynak sağlayıcısına kaydetmeniz gerekir.  Azure portal, Azure CLı veya PowerShell kullanarak bunu yapabilirsiniz.

### <a name="azure-portal"></a>Azure portalı

1. Azure portal açın ve **tüm hizmetlere**gidin. 
1. **Abonelikler** ' e gidin ve ilgilendiğiniz aboneliği seçin.  
1. **Abonelikler** sayfasında, **kaynak sağlayıcıları**' na gidin. 
1. SQL ile ilgili kaynak sağlayıcılarını getirmek için filtreye **SQL** girin. 
1. İstediğiniz eyleme bağlı olarak **Microsoft. SqlVirtualMachine** sağlayıcısı için **Kaydet**, **yeniden kaydet**veya **kaydı kaldır** ' ı seçin. 

![Sağlayıcıyı değiştirme](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Komut satırı

SQL VM kaynak sağlayıcınızı az CLı veya PowerShell kullanarak Azure aboneliğinize kaydedin. 

# <a name="az-clitabbash"></a>[AZ CLı](#tab/bash)
Aşağıdaki kod parçacığı, SQL VM kaynak sağlayıcısını Azure aboneliğinize kaydeder. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="unregister-vm-from-resource-provider"></a>Kaynak sağlayıcıdan VM kaydını sil 

SQL Server VM SQL VM kaynak sağlayıcısı ile kaydını silmek için, Azure portal veya Azure CLı kullanarak SQL sanal makine *kaynağını* silin. SQL sanal makine *kaynağını* silme, SQL Server VM silmez. Bununla birlikte, *kaynağı*kaldırmaya çalışırken yanlışlıkla sanal makineyi silmek mümkün olduğundan dikkatli olun ve adımları dikkatle izleyin. 

Yönetim modunun tam olarak indirgenmesini sağlamak için SQL VM kaynak sağlayıcısı ile SQL VM kaydının kaydı gereklidir. 

### <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak SQL Server VM kaynak sağlayıcıyla kaydını silmek için şu adımları izleyin:

1. [Azure portal](https://portal.azure.com) oturum açın.
1. SQL Server VM kaynağına gidin. 
  
   ![SQL sanal makineler kaynağı](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. **Sil**’i seçin. 

   ![SQL VM kaynak sağlayıcısını Sil](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. SQL sanal makinesinin adını yazın ve **sanal makinenin yanındaki onay kutusunu temizleyin**.

   ![SQL VM kaynak sağlayıcısını Sil](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Sanal makine adının yanındaki onay kutusunun temizlenmemesi, sanal makineyi tamamen *siler* . Kaynak sağlayıcıdan SQL Server VM kaydını kaldırmak, ancak *gerçek sanal makineyi silmek*için onay kutusunu temizleyin. 

1. SQL Server sanal makinesini değil, SQL sanal makine *kaynağının*silinmesini onaylamak için **Sil** ' i seçin. 


### <a name="azure-cli"></a>Azure CLI 

Azure CLı ile kaynak sağlayıcıdan SQL Server sanal makinenizin kaydını silmek için [az SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) komutunu kullanın. Bu, SQL Server sanal makine *kaynağını* kaldırır ancak sanal makineyi silmez. 


```azurecli-interactive
   az sql vm delete 
     --name <SQL VM resource name> |
     --resource-group <Resource group name> |
     --yes 
```



## <a name="remarks"></a>Açıklamalar

- SQL VM kaynak sağlayıcısı yalnızca Azure Resource Manager aracılığıyla dağıtılan SQL Server VM 'Leri destekler. Klasik model aracılığıyla dağıtılan SQL Server VM 'Ler desteklenmez. 
- SQL VM kaynak sağlayıcısı yalnızca ortak buluta dağıtılan SQL Server VM 'Leri destekler. Özel veya kamu bulutuna dağıtımlar desteklenmez. 
 

## <a name="frequently-asked-questions"></a>Sık sorulan sorular 

**Azure Marketi 'ndeki bir SQL Server görüntüsünden sağlanan SQL Server VM kaydetmem gerekir mi?**

Hayır. Microsoft, Azure Marketi 'ndeki SQL Server görüntülerden sağlanan VM 'Leri otomatik olarak kaydeder. SQL VM kaynak sağlayıcısına kaydolmak yalnızca VM 'nin Azure Marketi 'ndeki SQL Server görüntülerden *sağlanmaması* ve SQL Server kendi kendine yüklenmesi durumunda gereklidir.

**SQL VM kaynak sağlayıcısını tüm müşteriler kullanabilir mi?** 

Evet. Müşteriler, Azure Marketi 'nden bir SQL Server görüntüsü kullanmayan ve bunun yerine kendi kendine yüklenmiş SQL Server veya özel VHD 'leri getirdiklerinde SQL Server sanal makinelerini SQL VM kaynak sağlayıcısına kaydetmelidir. Tüm abonelik türlerinin (doğrudan, Kurumsal Anlaşma ve bulut çözümü sağlayıcısı) sahip olduğu VM 'Ler SQL VM kaynak sağlayıcısına kaydoabilir.

**SQL Server VM SQL Server IaaS uzantısı zaten yüklüyse SQL VM kaynak sağlayıcısına kaydolmalı mı?**

SQL Server VM kendi kendine yüklenmiş ve Azure Marketi 'ndeki SQL Server görüntülerden sağlanmamışsa, SQL Server IaaS uzantısını yüklemiş olsanız bile SQL VM kaynak sağlayıcısına kaydolmanız gerekir. SQL VM kaynak sağlayıcısına kaydolmak, Microsoft. SqlVirtualMachines türünde yeni bir kaynak oluşturur. SQL Server IaaS uzantısının yüklenmesi bu kaynağı oluşturmaz.

**SQL VM kaynak sağlayıcısına kaydolurken varsayılan yönetim modu nedir?**

SQL VM kaynak sağlayıcısına kayıt yaptığınızda varsayılan yönetim modu *dolu*' dır. SQL VM kaynak sağlayıcısına kaydoldığınızda SQL Server Management özelliği ayarlanmamışsa, mod tam yönetilebilirlik olarak ayarlanır. SQL Server IaaS uzantısının VM 'de yüklü olması, SQL VM kaynak sağlayıcısına tam yönetilebilirlik modunda kaydolma önkoşuludur.

**SQL VM kaynak sağlayıcısına kaydolma önkoşulları nelerdir?**

Basit modda veya aracı olmayan bir modda SQL VM kaynak sağlayıcısı 'na kaydolmak için herhangi bir önkoşul yoktur. SQL VM kaynak sağlayıcısına tam modda kaydolma önkoşulu, VM 'de SQL Server IaaS uzantısının yüklü olmasını sağlar.

**VM 'ye SQL Server IaaS uzantısı yüklü değilse SQL VM kaynak sağlayıcısı ile kayıt yapabilir miyim?**

Evet, VM 'ye SQL Server IaaS uzantısı yüklü değilse, basit yönetim modunda SQL VM kaynak sağlayıcısına kaydolabilirsiniz. Hafif modda SQL VM kaynak sağlayıcısı, SQL Server örneğinin sürümünü ve sürümünü doğrulamak için bir konsol uygulaması kullanacaktır. 

SQL VM kaynak sağlayıcısına kaydolurken varsayılan SQL yönetim modu _dolu_. SQL yönetim özelliği SQL VM kaynak sağlayıcısı ile kaydolurken ayarlanmamışsa, mod tam yönetilebilirlik olarak ayarlanır. VM 'de SQL IaaS uzantısının yüklü olması, SQL VM kaynak sağlayıcısı 'na tam yönetilebilirlik modunda kaydolma önkoşuludur.

**SQL VM kaynak sağlayıcısı ile kaydolacaktır, VM 'imde bir aracı yükler mi?**

Hayır. SQL VM kaynak sağlayıcısına kaydolmak yalnızca yeni bir meta veri kaynağı oluşturur. VM 'ye bir aracı yüklemez.

SQL Server IaaS uzantısı yalnızca tam yönetilebilirlik etkinleştirmek için gereklidir. Yönetilebilirlik modunu hafif 'dan tam 'e yükseltmek SQL Server IaaS uzantısını yükler ve SQL Server yeniden başlatır.

**SQL Server VM kaynak sağlayıcısı, VM 'imde SQL Server kayıt yaptıracaktır mi?**

Hayır. SQL VM kaynak sağlayıcısına kaydolmak yalnızca yeni bir meta veri kaynağı oluşturur. VM 'de SQL Server yeniden başlatmaz. 

SQL Server yeniden başlatma yalnızca SQL Server IaaS uzantısını yüklemek için gereklidir. Tam yönetilebilirliği etkinleştirmek için SQL Server IaaS uzantısı gereklidir. Yönetilebilirlik modunu hafif 'dan tam 'e yükseltmek SQL Server IaaS uzantısını yükler ve SQL Server yeniden başlatır.

**SQL VM kaynak sağlayıcısı ile kaydolurken basit ve olmayan aracı yönetimi modları arasındaki fark nedir?** 

Hiçbir aracı yönetim modu, Windows Server 2008 üzerinde yalnızca SQL Server 2008 ve SQL Server 2008 R2 için kullanılabilir. Bu sürümler için kullanılabilen tek yönetim modu vardır. Tüm SQL Server diğer sürümleri için, kullanılabilir iki yönetilebilirlik modu hafif ve tamamludur. 

Aracı modu, SQL Server sürüm ve sürüm özelliklerinin müşteri tarafından ayarlanmasını gerektirir. Hafif mod, SQL Server örneğinin sürümünü ve sürümünü bulmak için VM 'yi sorgular.

**Azure CLı kullanarak basit veya aracı olmayan modda SQL VM kaynak sağlayıcısı ile kayıt yapabilir miyim?**

Hayır. Yönetim modu özelliği yalnızca Azure PowerShell kullanarak SQL VM kaynak sağlayıcısı ile kayıt yaparken kullanılabilir. Azure CLı, SQL Server yönetilebilirlik özelliğinin ayarlanmasını desteklemez. Her zaman SQL VM kaynak sağlayıcısı 'nı varsayılan modda, tam yönetilebilirlik olarak kaydeder.

**SQL Server lisans türünü belirtmeden SQL VM kaynak sağlayıcısı ile kayıt yapabilir miyim?**

Hayır. SQL Server lisans türü, SQL VM kaynak sağlayıcısına kaydolurken isteğe bağlı bir özellik değildir. Azure CLı ve PowerShell kullanarak tüm yönetilebilirlik modlarında (Hayır-Agent, hafif ve tam) SQL VM kaynak sağlayıcısı 'na kaydolurken, SQL Server lisans türünü Kullandıkça Öde veya Azure Hibrit Avantajı olarak ayarlamanız gerekir.

**SQL Server IaaS uzantısını aracı olmayan moddan tam moda yükseltebilir miyim?**

Hayır. Yönetilebilirlik modunu tam veya hafif olarak yükseltmek, aracı olmayan mod için kullanılamaz. Bu, Windows Server 2008 ' in teknik sınırlamasıdır.

**SQL Server IaaS uzantısını hafif moddan tam moda yükseltebilir miyim?**

Evet. Yönetilebilirlik modunu hafif ' den Full ' a yükseltmek PowerShell veya Azure portal ile desteklenir. SQL Server yeniden başlatılmasını gerektirir.

**SQL Server IaaS uzantısını tam moddan aracı olmayan veya hafif yönetim moduna indirgeyebilirim miyim?**

Hayır. SQL Server IaaS uzantısının yönetilebilirlik modunu eski sürüme düşürme desteklenmez. Yönetilebilirlik modu, tam moddan hafif veya aracı olmayan moda düşürülemez ve hafif moddan aracı olmayan moda düşürülemez. 

Yönetilebilirlik modunu tam yönetilebilirlik olarak değiştirmek için, SQL Server *kaynağını* bırakarak ve SQL Server VM SQL VM kaynak sağlayıcısı ile yeniden kaydolarak SQL Server sanal makinesinin SQL Server kaynak sağlayıcısından [kaydını kaldırın](#unregister-vm-from-resource-provider) farklı bir yönetim modunda bir kez daha.

**Azure portal SQL VM kaynak sağlayıcısı ile kayıt yapabilir miyim?**

Hayır. SQL VM kaynak sağlayıcısına kaydolma Azure portal kullanılamıyor. SQL VM kaynak sağlayıcısı ile tam yönetilebilirlik modunda kaydolmak, Azure CLı veya PowerShell ile desteklenir. Basit veya aracı olmayan yönetilebilirlik modundaki SQL VM kaynak sağlayıcısına kaydolmak yalnızca Azure PowerShell API 'Leri tarafından desteklenir.

**SQL Server yüklenmeden önce SQL VM kaynak sağlayıcısı ile bir VM 'yi kaydedebilir miyim?**

Hayır. VM, SQL VM kaynak sağlayıcısına başarıyla kaydolmak için en az bir SQL Server örneğine sahip olmalıdır. VM üzerinde SQL Server örneği yoksa, yeni Microsoft. SqlVirtualMachine kaynağı başarısız durumda olur.

**Birden çok SQL Server örneği varsa SQL VM kaynak sağlayıcısı ile bir VM 'yi kaydedebilir miyim?**

Evet. SQL VM kaynak sağlayıcısı yalnızca bir SQL Server örneğini kaydeder. SQL VM kaynak sağlayıcısı, birden çok örnek olması durumunda varsayılan SQL Server örneğini kaydeder. Varsayılan örnek yoksa, yalnızca hafif modda kaydolma desteklenir. Hafif ve tam yönetilebilirlik moduna yükseltmek için, varsayılan SQL Server örneği var olmalıdır veya VM yalnızca bir adlandırılmış SQL Server örneğine sahip olmalıdır.

**SQL Server yük devretme kümesi örneğini SQL VM kaynak sağlayıcısı ile kaydedebilir miyim?**

Evet. Azure VM 'de yük devretme kümesi örnekleri SQL Server, SQL VM kaynak sağlayıcısına hafif modda kaydedilebilir. Ancak, SQL Server yük devretme kümesi örnekleri tam yönetilebilirlik moduna yükseltilemez.

**Her zaman açık kullanılabilirlik grubu yapılandırılmışsa VM 'imi SQL VM kaynak sağlayıcısı ile kaydedebilir miyim?**

Evet. Her zaman açık kullanılabilirlik grubu yapılandırmasına katılıyorsanız, SQL VM kaynak sağlayıcısı ile bir Azure VM 'de SQL Server örneğini kaydetme kısıtlaması yoktur.

**SQL VM kaynak sağlayıcısına kaydolma veya tam yönetilebilirlik moduna yükseltme maliyeti nedir?**
Hiçbiri. SQL VM kaynak sağlayıcısı ile veya üç yönetilebilirlik modundan birini kullanarak kayıt ile ilişkili bir ücret alınmaz. SQL Server VM kaynak sağlayıcıyla yönetme tamamen ücretsizdir. 

**Farklı yönetilebilirlik modlarını kullanmanın performans etkisi nedir?**
*Noagent* ve *hafif* yönetilebilirlik modları kullanılırken hiçbir etkisi yoktur. İşletim sistemine yüklenen iki hizmetten *tam* yönetilebilirlik modunu kullanırken en az etkisi vardır. Bunlar, Görev Yöneticisi ile izlenebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [Windows VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)
