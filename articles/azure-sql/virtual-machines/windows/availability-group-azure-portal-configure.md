---
title: Kullanılabilirlik grubu yapılandırma (Azure portal)
description: Windows Yük devretme kümesi, kullanılabilirlik grubu dinleyicisi ve iç yük dengeleyiciyi Azure 'da bir SQL Server VM oluşturmak için Azure portal kullanın.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359515"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Azure VM 'de SQL Server için bir kullanılabilirlik grubu (Önizleme) yapılandırmak üzere Azure portal kullanma 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure VM 'lerinde SQL Server için bir kullanılabilirlik grubu yapılandırmak üzere [Azure Portal](https://portal.azure.com) nasıl kullanılacağı açıklanır. 

Yeni bir küme oluşturmak veya var olan bir kümeyi eklemek için Azure portal kullanın ve ardından kullanılabilirlik grubunu, dinleyiciyi ve iç yük dengeleyiciyi oluşturun. 

Bu özellik şu anda önizleme sürümündedir. 

Bu makalede kullanılabilirlik grubu ortamını yapılandırmak için Azure portal kullanılırken, [PowerShell veya Azure CLI](availability-group-az-commandline-configure.md), [Azure hızlı başlangıç şablonları](availability-group-quickstart-template-configure.md)veya [el ile](availability-group-manually-configure-tutorial.md) de yapılabilir. 


## <a name="prerequisites"></a>Önkoşullar

Azure portal kullanarak her zaman açık kullanılabilirlik grubu yapılandırmak için aşağıdaki önkoşullara sahip olmanız gerekir: 

- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- Etki alanı denetleyicisi olan bir kaynak grubu. 
- Azure 'daki bir veya daha fazla etki alanına katılmış [VM 'ler SQL Server 2016 (veya üzeri) Enterprise Edition](./create-sql-vm-portal.md) 'ı *,* [SQL IaaS aracı uzantısına tam yönetilebilirlik modunda kaydedilmiş](sql-agent-extension-manually-register-single-vm.md) ve her bir VM 'de SQL Server hizmeti için aynı etki alanı hesabını kullanan *farklı* kullanılabilirlik bölgelerinde.
- Kullanılabilir iki (herhangi bir varlık tarafından kullanılmayan) IP adresleri. Bunlardan biri iç yük dengeleyiciye yöneliktir. Diğeri, kullanılabilirlik grubu ile aynı alt ağda bulunan kullanılabilirlik grubu dinleyicisine yöneliktir. Var olan bir yük dengeleyiciyi kullanıyorsanız, kullanılabilirlik grubu dinleyicisi için yalnızca bir kullanılabilir IP adresine sahip olmanız gerekir. 

## <a name="permissions"></a>İzinler

Azure portal kullanarak kullanılabilirlik grubunu yapılandırmak için aşağıdaki hesap izinlerine sahip olmanız gerekir: 

- Etki alanında **bilgisayar nesnesi oluşturma** iznine sahip olan mevcut bir etki alanı kullanıcı hesabı. Örneğin, bir etki alanı yönetici hesabı genellikle yeterli izne sahiptir (örneğin: account@domain.com ). _Bu hesap, kümeyi oluşturmak için her VM 'de yerel yönetici grubunun da bir parçası olmalıdır._
- SQL Server denetleyen etki alanı kullanıcı hesabı. Bu, kullanılabilirlik grubuna eklemek istediğiniz her SQL Server VM için aynı hesap olmalıdır. 

## <a name="configure-cluster"></a>Kümeyi Yapılandır

Azure portal kullanarak kümeyi yapılandırın. Yeni bir küme oluşturabilirsiniz veya zaten var olan bir kümeniz varsa, bunu Portal yönetilebilirliği için SQL IaaS Aracısı uzantısına ekleyebilirsiniz.


### <a name="create-a-new-cluster"></a>Yeni küme oluşturma

Zaten bir kümeniz varsa, bu bölümü atlayın ve bunun yerine [var olan kümeyi](#onboard-existing-cluster) eklemek için taşıyın. 

Zaten mevcut bir kümeniz yoksa, aşağıdaki adımlarla Azure portal kullanarak oluşturun:

1. [Azure portal](https://portal.azure.com) oturum açın. 
1. [SQL sanal makineler](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) kaynağına gidin. 
1. **Ayarlar** altında **yüksek kullanılabilirlik** ' i seçin. 
1. **Windows Yük devretme kümesini Yapılandır** sayfasını açmak Için **+ Yeni Windows Server yük devretme kümesi ' ni** seçin.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Portalda + yeni kümeyi seçerek yeni küme oluştur":::

1. Kümenizi adlandırın ve bulut tanığı olarak kullanılacak bir depolama hesabı sağlayın. Mevcut bir depolama hesabını kullanın veya yeni bir depolama hesabı oluşturmak için **Yeni oluştur** ' u seçin. Depolama hesabı adı 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakam ve küçük harf kullanılmalıdır.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Küme için ad, depolama hesabı ve kimlik bilgilerini sağlayın":::

1. SQL Server hizmet hesabının [kimlik bilgilerini](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) ve SQL Server hizmeti için kullanılan hesaptan farklı olmaları durumunda küme işlecini ve önyükleme hesaplarını sağlamak Için **Windows Server yük devretme kümesi kimlik bilgilerini** genişletin. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="SQL hizmeti hesabı, küme operatörü hesabı ve küme önyükleme hesabı için kimlik bilgilerini belirtin":::

1. Kümeye eklemek istediğiniz SQL Server VM 'Leri seçin. Yeniden başlatma gerekip gerekmediğini ve dikkatli ilerlemeniz gerektiğini not edin. Yalnızca SQL IaaS Aracısı Uzantısı ile tam yönetilebilirlik modunda kayıtlı olan ve birincil SQL Server VM aynı konumda, etki alanında ve aynı sanal ağda bulunan VM 'Ler görünür olur. 
1. Kümeyi oluşturmak için **Uygula** ' yı seçin. Üst gezinti çubuğundaki zil simgesinden erişilebilen **etkinlik günlüğünde** dağıtımınızın durumunu kontrol edebilirsiniz. 
1. Bir yük devretme kümesinin Microsoft tarafından desteklenmesi için, küme doğrulamasını geçmesi gerekir. Tercih ettiğiniz yöntemi (Uzak Masaüstü Protokolü (RDP)) kullanarak VM 'ye bağlanın ve daha sonra devam etmeden önce kümenizin doğrulamayı geçirdiğini doğrulayın. Bunun başarısız olması, kümenizi desteklenmeyen bir durumda bırakır. Yük Devretme Kümesi Yöneticisi (FCM) kullanarak kümeyi veya aşağıdaki PowerShell komutunu kullanabilirsiniz:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Mevcut kümeyi ekleme

SQL Server VM ortamınızda yapılandırılmış bir kümeniz zaten varsa, bunu Azure portal ekleyebilirsiniz.

Bunu yapmak için aşağıdaki adımları izleyin:

1. [Azure portal](https://portal.azure.com) oturum açın. 
1. [SQL sanal makineler](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) kaynağına gidin. 
1. **Ayarlar** altında **yüksek kullanılabilirlik** ' i seçin. 
1. **Mevcut Windows Server yük devretme kümesini** ekleme ' yi seçerek **Windows Server yük devretme kümesi** ekleme sayfasını açın. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="SQL sanal makineler kaynağınızın yüksek kullanılabilirlik sayfasından var olan bir kümeyi ekleme":::

1. Kümenizin ayarlarını gözden geçirin. 
1. Kümenizi eklemek için **Uygula** ' yı seçin ve sonra devam etmek Için sorulduğunda **Evet** ' i seçin.

## <a name="create-availability-group"></a>Kullanılabilirlik grubu oluştur

Kümeniz oluşturulduktan veya eklendi olduktan sonra, Azure portal kullanarak kullanılabilirlik grubunu oluşturun. Bunu yapmak için aşağıdaki adımları izleyin:

1. [Azure portal](https://portal.azure.com) oturum açın. 
1. [SQL sanal makineler](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) kaynağına gidin. 
1. **Ayarlar** altında **yüksek kullanılabilirlik** ' i seçin. 
1. **Kullanılabilirlik grubu oluştur** sayfasını açmak Için **+ Yeni Always on kullanılabilirlik grubu '** nu seçin.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Kullanılabilirlik grubu oluştur sayfasını açmak için yeni Always on kullanılabilirlik grubu ' nu seçin.":::

1. Kullanılabilirlik grubu için bir ad girin. 
1. **Kullanılabilirlik grubu dinleyicisini Yapılandır** sayfasını açmak Için **dinleyiciyi Yapılandır** ' ı seçin. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Kullanılabilirlik grubu için bir ad girin ve bir dinleyici yapılandırın":::

1. Değerleri doldurun ve mevcut yük dengeleyiciyi kullanın veya yeni bir yük dengeleyici oluşturmak için **Yeni oluştur** ' u seçin.  Ayarlarınızı kaydetmek ve dinleyicinizi ve yük dengeleyiciyi oluşturmak için **Uygula** ' yı seçin. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Yeni dinleyicinizi ve yük dengeleyiciyi oluşturmak için formdaki değerleri doldurun":::

1. **Kullanılabilirlik Grubu çoğaltmalarını Yapılandır** sayfasını açmak için **+ çoğaltmayı Seç ' i** seçin.
1. Kullanılabilirlik grubuna eklemek istediğiniz sanal makineleri seçin ve iş ihtiyaçlarınıza en uygun kullanılabilirlik grubu ayarlarını seçin. Ayarlarınızı kaydetmek için **Uygula** ' yı seçin. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Kullanılabilirlik grubunuza eklemek ve işletmenize uygun ayarları yapılandırmak için VM 'Leri seçin":::

1. Kullanılabilirlik grubu ayarlarınızı doğrulayın ve sonra kullanılabilirlik grubunuzu oluşturmak için **Uygula** ' yı seçin. 

Üst gezinti çubuğundaki zil simgesinden erişilebilen **etkinlik günlüğünde** dağıtımınızın durumunu kontrol edebilirsiniz. 

  > [!NOTE]
  > Kullanılabilirlik grubunuza veritabanları ekleyene kadar, Azure portal **yüksek kullanılabilirlik** sayfasında **eşitleme sistem durumu** **iyi değil** olarak görünür. 


## <a name="add-database-to-availability-group"></a>Veritabanını kullanılabilirlik grubuna ekle

Dağıtım tamamlandıktan sonra veritabanlarınızı kullanılabilirlik grubunuza ekleyin. Aşağıdaki adımlar SQL Server Management Studio (SSMS) kullanır, ancak [Transact-SQL veya PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) de kullanabilirsiniz. 

SQL Server Management Studio kullanarak kullanılabilirlik grubunuza veritabanları eklemek için aşağıdaki adımları izleyin:

1. Uzak Masaüstü Bağlantısı (RDP) gibi tercih ettiğiniz yöntemi kullanarak SQL Server sanal makinelerinizdeki birine bağlanın. 
1. SQL Server Management Studio (SSMS) açın.
1. SQL Server örneğine bağlanın. 
1. **Nesne Gezgini**' de **her zaman yüksek kullanılabilirlik '** i genişletin.
1. **Kullanılabilirlik grupları**' nı genişletin, kullanılabilirlik grubunuza sağ tıklayıp **veritabanı eklemeyi seçin...**.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Nesne Gezgini 'nde kullanılabilirlik grubuna sağ tıklayın ve veritabanı Ekle ' yi seçin":::

1. Kullanılabilirlik grubunuza eklemek istediğiniz veritabanlarını seçmek için istemleri izleyin. 
1. Ayarlarınızı kaydetmek ve veritabanınızı kullanılabilirlik grubuna eklemek için **Tamam** ' ı seçin. 
1. Veritabanı eklendikten sonra, veritabanınızın durumunu olarak onaylamak için **Nesne Gezgini** yenileyin `synchronized` . 

Veritabanları eklendikten sonra, Azure portal kullanılabilirlik grubunuzun durumunu kontrol edebilirsiniz: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Veritabanları eşitlendikten sonra Azure portal yüksek kullanılabilirlik sayfasından kullanılabilirlik grubunuzun durumunu kontrol edin":::

## <a name="add-more-vms"></a>Daha fazla VM ekleyin

Kümeye daha fazla SQL Server VM eklemek için şu adımları izleyin: 

1. [Azure portal](https://portal.azure.com) oturum açın. 
1. [SQL sanal makineler](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) kaynağına gidin. 
1. **Ayarlar** altında **yüksek kullanılabilirlik** ' i seçin. 
1. Windows Server yük devretme kümesini **Yapılandır** sayfasını açmak Için **Windows Server yük devretme kümesini Yapılandır** ' ı seçin. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Kümenize VM 'Ler eklemek için Windows Server yük devretme kümesini Yapılandır ' ı seçin.":::

1. **Windows Server yük devretme kümesi kimlik bilgilerini** genişletin ve SQL Server hizmeti, küme operatörü ve küme önyükleme hesapları için kullanılan hesaplara girin. 
1. Kümeye eklemek istediğiniz SQL Server VM 'Leri seçin. 
1. **Uygula**’yı seçin. 

Üst gezinti çubuğundaki zil simgesinden erişilebilen **etkinlik günlüğünde** dağıtımınızın durumunu kontrol edebilirsiniz. 


## <a name="modify-availability-group"></a>Kullanılabilirlik grubunu değiştir 


Kullanılabilirlik grubuna **daha fazla çoğaltma ekleyebilir** , **dinleyiciyi yapılandırabilir** veya kullanılabilirlik grubunuzun yanındaki üç nokta (...) simgesini seçerek Azure Portal **yüksek kullanılabilirlik** sayfasından **dinleyiciyi silebilirsiniz** : 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Kullanılabilirlik grubunun yanındaki üç noktayı seçin ve ardından kullanılabilirlik grubuna daha fazla çoğaltmalar eklemek için çoğaltma Ekle ' yi seçin.":::

## <a name="remove-cluster"></a>Kümeyi kaldır

Tüm SQL Server VM 'Leri kümeden kaldırın ve ardından SQL IaaS Aracısı uzantısından küme meta verilerini kaldırın. [Azure CLI](/cli/azure/install-azure-cli) veya PowerShell 'in en son sürümünü kullanarak bunu yapabilirsiniz. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İlk olarak, SQL Server VM 'lerin tümünü kümeden kaldırın. Bu, düğümleri fiziksel olarak kümeden kaldırır ve kümeyi yok eder:  

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

az sql vm group delete --name <cluster name> --resource-group <resource group name>
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

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Sorun giderme

Sorunlarla karşılaşırsanız, dağıtım geçmişini denetleyebilir ve sık karşılaşılan hataları ve bunların çözümlerini gözden geçirebilirsiniz. 

### <a name="check-deployment-history"></a>Dağıtım geçmişini denetle

Portal aracılığıyla küme ve kullanılabilirlik grubundaki değişiklikler dağıtımlar aracılığıyla yapılır. Dağıtım geçmişi, küme oluşturma veya ekleme ile veya kullanılabilirlik grubu oluşturma ile ilgili sorunlar varsa daha ayrıntılı bir şekilde sağlayabilir.

Dağıtımın günlüklerini görüntülemek ve dağıtım geçmişini denetlemek için şu adımları izleyin:

1. [Azure portal](https://portal.azure.com) oturum açın.
1. Kaynak grubunuza gidin.
1. **Ayarlar** altında **dağıtımlar** ' ı seçin.
1. Dağıtım hakkında daha fazla bilgi edinmek için ilgilendiğiniz dağıtımı seçin. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Hakkında daha fazla bilgi edinmek için ilgilendiğiniz dağıtımı seçin." :::

### <a name="common-errors"></a>Sık karşılaşılan hatalar

Aşağıdaki yaygın hataları ve bunların çözümlerini gözden geçirin. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>SQL hizmetini başlatmak için kullanılan hesap bir etki alanı hesabı değil

Bu, kaynak sağlayıcının SQL Server hizmetine, belirtilen kimlik bilgileriyle erişemediğinin göstergesidir. Bazı yaygın çözümler:
- Etki alanı denetleyicinizin çalıştığından emin olun.
- Portalda belirtilen kimlik bilgilerinin SQL Server hizmetindekilerle eşleştiğini doğrulayın. 


## <a name="next-steps"></a>Sonraki adımlar


Kullanılabilirlik grupları hakkında daha fazla bilgi için bkz.

- [Kullanılabilirlik gruplarına genel bakış](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Kullanılabilirlik grubunun yönetimi](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Kullanılabilirlik gruplarının izlenmesi &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Kullanılabilirlik grubu Transact-SQL deyimleri ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Kullanılabilirlik grupları PowerShell komutları](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

SQL Server VM 'Ler hakkında daha fazla bilgi için bkz.: 

* [SQL Server VM 'lerine genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 'Ler için sürüm notları](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM 'Ler için SSS](frequently-asked-questions-faq.md)
