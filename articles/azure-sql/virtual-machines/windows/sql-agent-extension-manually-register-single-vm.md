---
title: SQL IaaS Aracısı Uzantısı ile kaydolun
description: Azure Market 'in dışında dağıtılan SQL Server sanal makinelere yönelik özelliklerin yanı sıra uyumluluk ve gelişmiş yönetilebilirlik özelliklerine olanak tanımak için Azure SQL Server sanal makinenizi SQL IaaS Aracısı Uzantısı ile kaydedin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: e34876c76259b8274e0b0ef9059659802eb55cf1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765458"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>SQL IaaS Aracısı Uzantısı ile SQL Server VM kaydetme
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure VM 'de SQL Server için çok sayıda özellik avantajlarının kilidini açmak üzere SQL Server VM [SQL IaaS Aracısı uzantısıyla](sql-server-iaas-agent-extension-automate-management.md) kaydetme. 

Bu makale, SQL IaaS Aracısı Uzantısı ile tek bir SQL Server VM kaydetmenizi öğretir. Alternatif olarak, tüm SQL Server sanal makinelerini [otomatik olarak](sql-agent-extension-automatic-registration-all-vms.md) veya [birden çok VM 'yi toplu](sql-agent-extension-manually-register-vms-bulk.md)olarak kaydedebilirsiniz.


## <a name="overview"></a>Genel Bakış

[SQL Server IaaS Aracısı uzantısına](sql-server-iaas-agent-extension-automate-management.md) kaydolmak, aboneliğinizdeki **SQL sanal makine** _kaynağını_ , sanal makine kaynağından _ayrı_ bir kaynak oluşturur. SQL Server VM uzantıdan kaldırmak **SQL sanal makine** _kaynağını_ kaldırır ancak gerçek sanal makineyi bırakamaz.

Azure portal üzerinden SQL Server VM Azure Marketi görüntüsünün dağıtımı, SQL Server VM Uzantısı ile otomatik olarak kaydeder. Ancak, bir Azure sanal makinesine SQL Server kendi kendine yüklemeyi veya özel bir VHD 'den bir Azure sanal makinesini sağlamayı seçerseniz, tam özellik avantajları ve yönetilebilirliği açmak için SQL Server VM SQL IaaS Aracısı Uzantısı ile kaydetmeniz gerekir. 

SQL IaaS Aracısı uzantısını kullanmak için öncelikle [aboneliğinizi **Microsoft. SqlVirtualMachine** sağlayıcısıyla kaydetmeniz](#register-subscription-with-resource-provider)gerekır, bu da SQL IaaS uzantısına bu belirli abonelikte kaynak oluşturma yeteneği verir.

> [!IMPORTANT]
> SQL IaaS Aracısı uzantısı, Azure sanal makineler 'de SQL Server kullanırken müşterilere isteğe bağlı avantajlar vermek için veri toplar. Microsoft, bu verileri müşterinin öncelikli onayı olmadan lisanslama denetimleri için kullanmaz. Daha fazla bilgi için [SQL Server Gizlilik Eki](/sql/sql-server/sql-server-privacy#non-personal-data) ' ne bakın.

## <a name="prerequisites"></a>Önkoşullar

SQL Server VM uzantıya kaydetmek için şunlar gerekir: 

- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- Azure kaynak modeli [Windows Server 2008 (veya üzeri)](../../../virtual-machines/windows/quick-create-portal.md) , genel veya Azure Kamu bulutuna dağıtılan [SQL Server 2008 (veya daha büyük](https://www.microsoft.com/sql-server/sql-server-downloads) ) bir sanal makineye sahiptir. 
- [Azure CLI](/cli/azure/install-azure-cli) veya Azure PowerShell 'nin en son sürümü [(5,0 en az)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-resource-provider"></a>Aboneliği kaynak sağlayıcısına Kaydet

SQL Server VM SQL IaaS Aracısı uzantısına kaydetmek için öncelikle aboneliğinizi **Microsoft. SqlVirtualMachine** kaynak sağlayıcısı ile kaydetmeniz gerekir. Bu, SQL IaaS Aracısı uzantısının aboneliğinizdeki kaynakları oluşturma yeteneğini sağlar.  Azure portal, Azure CLı veya Azure PowerShell kullanarak bunu yapabilirsiniz.

### <a name="azure-portal"></a>Azure portalı

1. Azure portal açın ve **tüm hizmetlere** gidin. 
1. **Abonelikler** ' e gidin ve ilgilendiğiniz aboneliği seçin.  
1. **Abonelikler** sayfasında, **Uzantılar**' a gidin. 
1. SQL ile ilgili uzantıları getirmek için filtreye **SQL** girin. 
1. İstediğiniz eyleme bağlı olarak **Microsoft. SqlVirtualMachine** sağlayıcısı için **Kaydet**, **yeniden kaydet** veya **kaydı kaldır** ' ı seçin. 

   ![Sağlayıcıyı değiştirme](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Komut satırı

Azure aboneliğinizi **Microsoft. SqlVirtualMachine** SAĞLAYıCıSıYLA Azure clı veya Azure PowerShell kullanarak kaydedin. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Uzantıya kaydolun

[SQL Server IaaS Aracısı uzantısı](sql-server-iaas-agent-extension-automate-management.md#management-modes)için üç yönetim modu vardır. 

Uzantının tam yönetim modunda kaydedilmesi SQL Server hizmetini yeniden başlatır. böylece, uzantıyı önce basit modda kaydetmeniz önerilir, sonra bir bakım penceresi sırasında [tam olarak yükseltin](#upgrade-to-full) . 

### <a name="lightweight-management-mode"></a>Hafif yönetim modu

SQL Server VM basit modda uzantınızı kaydetmek için Azure CLı veya Azure PowerShell kullanın. Bu işlem SQL Server hizmetini yeniden başlatmaz. Dilediğiniz zaman tam moda yükseltebilirsiniz, ancak bunu yapmak SQL Server hizmeti 'ni yeniden başlatarak zamanlanan bir bakım penceresine kadar beklemeniz önerilir. 

Kullanım başına ödeme yapmak için SQL Server lisans türünü Kullandıkça Öde ( `PAYG` ), `AHUB` kendi lisansınızı kullanmak için Azure hibrit avantajı () veya `DR` [ücretsiz Dr çoğaltma lisansını](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)etkinleştirmek için olağanüstü durum kurtarma () sağlayın.

Yük devretme kümesi örnekleri ve çok örnekli dağıtımlar yalnızca hafif modda SQL IaaS Aracısı Uzantısı ile kaydedilebilir. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLı ile hafif modda bir SQL Server VM kaydetme: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

SQL Server VM Azure PowerShell basit modda kaydetme:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Tam yönetim modu

SQL Server VM tam modda kaydetmek SQL Server hizmetini yeniden başlatır. Lütfen dikkatli ilerleyin. 

SQL Server VM doğrudan tam modda kaydetmek için (ve SQL Server hizmetinizi büyük olasılıkla yeniden başlatmanız), aşağıdaki Azure PowerShell komutunu kullanın: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent yönetim modu 

Windows Server 2008 (_R2 değil_) üzerinde yüklü olan SQL Server 2008 ve 2008 R2, [NOAGENT modunda](sql-server-iaas-agent-extension-automate-management.md#management-modes)SQL IaaS Aracısı Uzantısı ile kaydedilebilir. Bu seçenek uyumluluk sağlar ve Azure portal sınırlı işlevlerle birlikte SQL Server VM izlenmesini sağlar.


**Lisans türü** için şunlardan birini belirtin: `AHUB` , `PAYG` , veya `DR` . **Görüntü teklifi** için ya da şunu belirtin `SQL2008-WS2008``SQL2008R2-WS2008`

`SQL2008-WS2008`Windows Server 2008 örneğine SQL Server 2008 () veya 2008 R2 () kaydetmek için `SQL2008R2-WS2008` AŞAĞıDAKI Azure clı veya Azure PowerShell kod parçacığını kullanın: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLı ile SQL Server sanal makinenizi NoAgent moduna kaydedin: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


SQL Server sanal makinenizi Azure PowerShell ile NoAgent moduna kaydedin: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Doğrulama modu

SQL Server IaaS aracınızın geçerli modunu Azure PowerShell kullanarak görüntüleyebilirsiniz: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Tam sürüme yükselt  

Uzantıyı *hafif* modda kaydetmiş SQL Server VM 'ler, Azure Portal, Azure clı veya Azure PowerShell kullanarak _tam_ olarak yükseltebilir. _Noagent_ modundaki SQL Server VM 'ler, Işletim sistemi Windows 2008 R2 ve üzeri sürümlere yükseltildikten sonra _tam_ sürümüne yükseltilebilir. Bunun için düşürme yapılamaz, bunun için SQL IaaS Aracısı uzantısından SQL Server VM [kaydını](#unregister-from-extension) kaldırmanız gerekir. Bunun yapılması **SQL sanal makine** _kaynağını_ kaldıracak, ancak gerçek sanal makineyi silmeyecektir. 

> [!NOTE]
> SQL IaaS uzantısının yönetim modunu tam olarak yükselttiğinizde, SQL Server hizmeti başlatılır. Bazı durumlarda, yeniden başlatma SQL Server hizmetiyle ilişkili hizmet asıl adlarının (SPN 'Ler) yanlış Kullanıcı hesabına değişmesine neden olabilir. Yönetim modunu tam olarak yükselttikten sonra bağlantı sorunlarınız varsa, [SPN 'leri silin ve yeniden kaydettirin](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections).


### <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak uzantıyı tam moda yükseltmek için aşağıdaki adımları izleyin: 

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. [SQL sanal makineler](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) kaynağına gidin. 
1. SQL Server VM seçin ve **genel bakış**' ı seçin. 
1. NoAgent veya Lightweight IaaS modundaki sanal makineler SQL Server için, **SQL IaaS uzantı Iletisiyle tek lisans türünü seçin ve sürüm güncelleştirmelerini** seçin.

   ![Portalın modunu değiştirme seçimleri](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. **Sanal makinede SQL Server hizmetini yeniden başlatmayı kabul** ediyorum onay kutusunu seçin ve ardından IaaS modumu tam olarak yükseltmek için **Onayla** ' yı seçin. 

    ![Sanal makinede SQL Server hizmetini yeniden başlatmak için kabul etmiş onay kutusu](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Komut satırı

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Uzantıyı tam moda yükseltmek için aşağıdaki Azure CLı kod parçacığını çalıştırın:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uzantıyı tam moda yükseltmek için aşağıdaki Azure PowerShell kod parçacığını çalıştırın:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Kayıt durumunu doğrula
SQL Server VM, Azure portal, Azure CLı veya Azure PowerShell kullanarak SQL IaaS Aracısı Uzantısı ile zaten kaydedilmiş olduğunu doğrulayabilirsiniz. 

### <a name="azure-portal"></a>Azure portalı 

Azure portal kullanarak kayıt durumunu doğrulamak için aşağıdaki adımları izleyin: 

1. [Azure portalında](https://portal.azure.com) oturum açın. 
1. [SQL Server sanal](manage-sql-vm-portal.md)makinelerinize gidin.
1. Listeden SQL Server VM seçin. SQL Server VM burada listelenmiyorsa, büyük olasılıkla SQL IaaS Aracısı uzantısına kayıtlı değildir. 
1. **Durum** altındaki değeri görüntüleyin. **Durum** **başarılı** olursa SQL Server VM SQL IaaS Aracısı uzantısı başarıyla kaydedildi. 

   ![SQL RP kaydıyla durumu doğrulama](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Komut satırı

Azure CLı veya Azure PowerShell kullanarak geçerli SQL Server VM kayıt durumunu doğrulayın. `ProvisioningState` , `Succeeded` kaydın başarılı olup olmadığını gösterir. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLı kullanarak kayıt durumunu doğrulamak için aşağıdaki kod parçacığını çalıştırın:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell kullanarak kayıt durumunu doğrulamak için aşağıdaki kod parçacığını çalıştırın:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Bir hata SQL Server VM uzantıya kaydedilmemiş olduğunu gösterir. 


## <a name="unregister-from-extension"></a>Uzantıdan kaydını sil

SQL IaaS Aracısı Uzantısı ile SQL Server VM kaydını silmek için, Azure portal veya Azure CLı kullanarak SQL sanal makine *kaynağını* silin. SQL sanal makine *kaynağını* silme, SQL Server VM silmez. Bununla birlikte, *kaynağı* kaldırmaya çalışırken yanlışlıkla sanal makineyi silmek mümkün olduğundan dikkatli olun ve adımları dikkatle izleyin. 

Yönetim modunun tam olarak indirgenmesini sağlamak için SQL sanal makinesinin SQL IaaS Aracısı Uzantısı ile kaydının kaydı gereklidir. 

### <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak SQL Server VM uzantıdan kaydını silmek için şu adımları izleyin:

1. [Azure portal](https://portal.azure.com) oturum açın.
1. SQL VM kaynağına gidin. 
  
   ![SQL sanal makineler kaynağı](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. **Sil**’i seçin. 

   ![Üstteki gezinmede Sil ' i seçin](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. SQL sanal makinesinin adını yazın ve **sanal makinenin yanındaki onay kutusunu temizleyin**.

   ![Gerçek sanal makinenin silinmesini engellemek için VM 'nin işaretini kaldırın ve ardından SQL VM kaynağını silmeye devam etmek için Sil 'i seçin](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Sanal makine adının yanındaki onay kutusunun temizlenmemesi, sanal makineyi tamamen *siler* . SQL Server VM uzantının kaydını silmek için onay kutusunu temizleyin ancak *gerçek sanal makineyi silmez*. 

1. SQL Server VM değil, SQL sanal makine *kaynağını* silmeyi onaylamak için **Sil** ' i seçin. 

### <a name="command-line"></a>Komut satırı

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Uzantınızın Azure CLı ile SQL Server VM kaydını silmek için [az SQL VM Delete](/cli/azure/sql/vm#az_sql_vm_delete) komutunu kullanın. Bu işlem SQL Server VM *kaynağını* kaldırır ancak sanal makineyi silmez. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

SQL Server VM uzantıdan Azure PowerShell kaydını silmek için [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)komutunu kullanın. Bu işlem SQL Server VM *kaynağını* kaldırır ancak sanal makineyi silmez. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri inceleyin: 

* [Windows VM 'de SQL Server genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](frequently-asked-questions-faq.md)  
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](../../database/doc-changes-updates-release-notes.md)
