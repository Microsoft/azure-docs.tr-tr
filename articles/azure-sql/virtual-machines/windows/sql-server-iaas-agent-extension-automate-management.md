---
title: SQL Server IaaS Aracısı uzantısı nedir?
description: Bu makalede SQL Server IaaS aracı uzantısının, Azure VM 'lerinde SQL Server yönetim özel yönetim görevlerinin nasıl otomatikleştirilmesine yardımcı olduğu açıklanır. Bunlar otomatik yedekleme, otomatik düzeltme eki uygulama, Azure Key Vault tümleştirme, lisanslama yönetimi, depolama yapılandırması ve tüm SQL Server VM örneklerinin merkezi yönetimi gibi özellikler içerir.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225498"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Aracısı Uzantısı ile yönetimi otomatikleştirme
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS Aracısı uzantısı (Sqliaasextenma), yönetim ve yönetim görevlerini otomatikleştirmek için Azure sanal makinelerinde (VM) SQL Server çalışır. 

Bu makale, uzantıya genel bir bakış sağlar. SQL Server IaaS uzantısını Azure VM 'lerine SQL Server yüklemek için, [Otomatik yükleme](sql-agent-extension-automatic-registration-all-vms.md), [tek VM](sql-agent-extension-manually-register-single-vm.md)'ler veya [VM 'ler için toplu olarak](sql-agent-extension-manually-register-vms-bulk.md)makalelere bakın. 

## <a name="overview"></a>Genel Bakış

IaaS Aracısı uzantısı, Azure portal tümleştirilmesine olanak tanır ve yönetim moduna bağlı olarak, Azure VM 'lerinde SQL Server için birçok özellik avantajlarının kilidini açar: SQL Server 

- **Özellik avantajları**: uzantı, Portal yönetimi, lisans esnekliği, otomatik yedekleme, otomatik düzeltme eki uygulama ve daha fazlası gibi birçok Otomasyon özelliği avantajlarından yararlanır. Ayrıntılar için bu makalenin ilerleyen kısımlarında bulunan [özellik avantajları](#feature-benefits) bölümüne bakın. 

- **Uyumluluk**: uzantı, Microsoft 'a Azure hibrit avantajı, ürün koşullarında belirtilen şekilde etkinleştirildiğini bildirmek için gereksinimi karşılamakta olan basitleştirilmiş bir yöntem sunar. Bu işlem, her kaynak için lisanslama kayıt formlarını yönetmeye gerek duymasını geçersiz kılar.  

- **Ücretsiz**: tüm üç yönetilebilirlik modundaki uzantı tamamen ücretsizdir. Uzantı ile ilişkili ek maliyet yoktur veya değiştirme yönetimi modları vardır. 

- **Basitleştirilmiş lisans yönetimi**: uzantı SQL Server lisans yönetimini basitleştirir ve [Azure Portal](manage-sql-vm-portal.md), PowerShell veya Azure clı kullanarak etkinleştirilen Azure hibrit avantajı SQL Server VM 'leri hızlıca tanımlamanızı sağlar: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> SQL IaaS Aracısı uzantısı, Azure sanal makineler 'de SQL Server kullanırken müşterilere isteğe bağlı avantajlar vermek için veri toplar. Microsoft, bu verileri müşterinin öncelikli onayı olmadan lisanslama denetimleri için kullanmaz. Daha fazla bilgi için [SQL Server Gizlilik Eki](/sql/sql-server/sql-server-privacy#non-personal-data) ' ne bakın.


## <a name="feature-benefits"></a>Özellik avantajları 

SQL Server IaaS Aracısı uzantısı, SQL Server VM yönetmek için bir dizi özellik avantajlarından yararlanır. 

Aşağıdaki tabloda bu avantajlar ayrıntılı olarak verilmiştir: 


| Özellik | Açıklama |
| --- | --- |
| **Portal yönetimi** | [Portaldaki yönetimin](manage-sql-vm-portal.md)kilidini açarak SQL Server sanal makinelerinizi tek bir yerde görüntüleyebilir ve böylece SQL 'e özgü özellikleri doğrudan portaldan etkinleştirebilir ve devre dışı bırakabilmenizi sağlayabilirsiniz. <br/> Yönetim modu: hafif & dolu|  
| **Otomatik yedekleme** |Tüm veritabanları için, varsayılan örnek veya VM üzerinde [doğru şekilde yüklenmiş](frequently-asked-questions-faq.md#administration) bir SQL Server örneği için yedeklemelerin zamanlamasını otomatikleştirir. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için otomatik yedekleme (Kaynak Yöneticisi)](automated-backup-sql-2014.md). <br/> Yönetim modu: tam|
| **Otomatik düzeltme eki uygulama** |, Önemli Windows ve VM 'niz için SQL Server güvenlik güncelleştirmelerinin gerçekleşmesi sırasında bir bakım penceresi yapılandırır, bu sayede iş yükünüz için yoğun zamanlarda güncelleştirmelerden kaçınabilirsiniz. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için otomatik düzeltme eki uygulama (Kaynak Yöneticisi)](automated-patching.md). <br/> Yönetim modu: tam|
| **Azure Key Vault tümleştirme** |SQL Server VM Azure Key Vault otomatik olarak yüklemenize ve yapılandırmanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server için Azure Key Vault tümleştirmesini yapılandırma (Kaynak Yöneticisi)](azure-key-vault-integration-configure.md). <br/> Yönetim modu: tam|
| **Portalda disk kullanımını görüntüleme** | Azure portal SQL veri dosyalarınızın disk kullanımının grafik temsilini görüntülemenize olanak sağlar.  <br/> Yönetim modu: tam | 
| **Esnek lisanslama** | Kendi lisansını getir (Azure Hibrit Avantajı olarak da bilinir) ile Kullandıkça Öde lisanslama modeline [sorunsuz](licensing-model-azure-hybrid-benefit-ahb-change.md) bir şekilde geçiş yaparak maliyetten tasarruf edin. <br/> Yönetim modu: hafif & dolu| 
| **Esnek sürüm/sürüm** | SQL Server [sürümünü](change-sql-server-version.md) [veya sürümünü](change-sql-server-edition.md) değiştirmeye karar verirseniz, tüm SQL Server VM yeniden dağıtmak zorunda kalmadan, Azure Portal içindeki meta verileri güncelleştirebilirsiniz.  <br/> Yönetim modu: hafif & dolu| 


## <a name="management-modes"></a>Yönetim modları

SQL IaaS uzantınızı üç yönetim modunda kaydetmeyi seçebilirsiniz: 

- **Hafif** mod uzantı IKILILERINI sanal makineye kopyalar, ancak aracıyı yüklemez ve SQL Server hizmetini yeniden başlatmaz. Hafif mod yalnızca SQL Server lisans türünü ve sürümünü değiştirmeyi destekler ve sınırlı Portal yönetimi sağlar. Birden çok örneğe sahip veya bir yük devretme kümesi örneğine (FCı) katılan SQL Server VM 'Ler için bu seçeneği kullanın. Hafif mod, [otomatik kayıt](sql-agent-extension-automatic-registration-all-vms.md) özelliği kullanılırken veya el ile kayıt sırasında bir yönetim türü belirtilmediğinde varsayılan yönetim modudur. Hafif mod kullanılırken bellek veya CPU üzerinde hiçbir etkisi yoktur ve ilişkili bir maliyet yoktur. SQL Server VM önce basit modda kaydetmeniz önerilir, sonra zamanlanmış bakım penceresi sırasında tam moda yükseltilir. 

- **Tam** mod, tüm işlevleri sunmak Için SQL IaaS aracısını VM 'ye yükleyerek, ancak SQL Server hizmeti ve Sistem Yöneticisi izinlerinin yeniden başlatılmasını gerektirir. Tek bir örnekle SQL Server VM yönetmek için kullanın. Tam mod belleği ve CPU üzerinde en az etkisi olan iki Windows hizmeti yüklüyor. Bunlar, Görev Yöneticisi aracılığıyla izlenebilir. Tüm yönetilebilirlik modunu kullanmayla ilişkili bir maliyet yoktur. 

- **Noagent** modu, Windows Server 2008 ' de yüklü SQL Server 2008 ve SQL Server 2008 R2 için ayrılmıştır. NoAgent modu kullanılırken bellek veya CPU üzerinde bir etkisi yoktur. NoAgent yönetilebilirlik modunu kullanmayla ilişkili bir maliyet yoktur, SQL Server yeniden başlatılmaz ve bir aracı VM 'ye yüklenmez. 

SQL Server IaaS aracınızın geçerli modunu Azure PowerShell kullanarak görüntüleyebilirsiniz: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Yükleme

Aboneliğinizde, sanal makine kaynağından _ayrı_ bir kaynak olan **SQL sanal makine** _kaynağını_ oluşturmak için SQL Server IaaS aracı uzantısına SQL Server VM kaydedin. SQL Server VM uzantıdan kaldırmak **SQL sanal makine** _kaynağını_ kaldırır ancak gerçek sanal makineyi bırakamaz.

Azure portal üzerinden SQL Server VM Azure Marketi görüntüsünün dağıtımı, SQL Server VM Uzantısı ile otomatik olarak kaydeder. Ancak, bir Azure sanal makinesine SQL Server kendi kendine yüklemeyi veya özel bir VHD 'den bir Azure sanal makinesini sağlamayı seçerseniz, özellik avantajlarının kilidini açmak için SQL Server VM SQL IaaS uzantısına kaydetmeniz gerekir. 

Uzantıyı hafif modda kaydetmek ikilileri kopyalayacak, ancak aracıyı VM 'ye yüklemecektir. Uzantı tam yönetim moduna yükseltildiğinde aracı VM 'ye yüklenir. 

Uzantıya kaydolmak için üç yol vardır: 
- [Bir abonelikteki tüm geçerli ve gelecekteki VM 'Ler için otomatik olarak](sql-agent-extension-automatic-registration-all-vms.md)
- [Tek bir VM için el ile](sql-agent-extension-manually-register-single-vm.md)
- [Toplu olarak birden çok VM için el ile](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Adlandırılmış örnek desteği

SQL Server IaaS Aracısı uzantısı, sanal makinede kullanılabilen tek SQL Server örnek varsa SQL Server adlandırılmış bir örneğiyle birlikte çalışıyor. SANAL makinede varsayılan örnek yoksa, uzantı birden çok adlandırılmış SQL Server örneğe sahip VM 'Lere yüklenemez. 

SQL Server adlı bir örneğini kullanmak için bir Azure sanal makinesini dağıtın, bu örneğe tek bir adlandırılmış SQL Server örneği yükleyip [SQL IaaS uzantısına](sql-agent-extension-manually-register-single-vm.md)kaydedin.

Alternatif olarak, Azure Marketi SQL Server görüntüsü ile adlandırılmış bir örnek kullanmak için şu adımları izleyin: 

   1. Azure Marketi 'nden bir SQL Server VM dağıtın. 
   1. SQL IaaS Aracısı uzantısı SQL Server VM [kaydını silin](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) . 
   1. SQL Server tamamen SQL Server VM içinde kaldırın.
   1. SQL Server VM içindeki adlandırılmış bir örnekle SQL Server yükler. 
   1. [VM 'YI SQL IaaS Aracısı uzantısı Ile kaydedin](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Uzantının durumunu doğrulama

Uzantının durumunu denetlemek için Azure portal veya Azure PowerShell kullanın. 

### <a name="azure-portal"></a>Azure portalı

Azure portal uzantının yüklü olduğunu doğrulayın. 

Sanal makine bölmesinde **Tüm ayarlar** ' ı seçin ve ardından **Uzantılar**' ı seçin. **Sqliaasextenma** uzantısının listelendiğini görmeniz gerekir.

![Azure portal IaaS Aracısı uzantısının durumu SQL Server](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet 'ini de kullanabilirsiniz:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Önceki komut, aracının yüklendiğini onaylar ve genel durum bilgilerini sağlar. Aşağıdaki komutları kullanarak otomatik yedekleme ve düzeltme eki uygulama hakkında belirli durum bilgilerini alabilirsiniz:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Sınırlamalar

SQL IaaS Aracısı uzantısı yalnızca şunları destekler: 

- Azure Resource Manager aracılığıyla dağıtılan SQL Server VM 'Ler. Klasik model aracılığıyla dağıtılan SQL Server VM 'Ler desteklenmez. 
- Genel veya Azure Kamu bulutuna dağıtılan VM 'Ler SQL Server. Diğer özel veya kamu bulutlarına dağıtımlar desteklenmez. 


## <a name="in-region-data-residency"></a>Bölge içi veri yerleşimi
Azure SQL sanal makinesi ve SQL IaaS Aracısı uzantısı, müşteri verilerini dağıtıldığı bölge dışına taşımaz veya depolamaz.

## <a name="next-steps"></a>Sonraki adımlar

SQL Server IaaS uzantısını Azure VM 'lerine SQL Server yüklemek için, [Otomatik yükleme](sql-agent-extension-automatic-registration-all-vms.md), [tek VM](sql-agent-extension-manually-register-single-vm.md)'ler veya [VM 'ler için toplu olarak](sql-agent-extension-manually-register-vms-bulk.md)makalelere bakın.

Azure sanal makinelerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server nedir?](sql-server-on-azure-vm-iaas-what-is-overview.md).

Daha fazla bilgi için bkz. [sık sorulan sorular](frequently-asked-questions-faq.md). 
