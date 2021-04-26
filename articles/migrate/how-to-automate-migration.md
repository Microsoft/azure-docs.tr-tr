---
title: Azure geçişi 'nde aracısız VMware geçişlerini otomatikleştirin
description: Azure geçişi 'nde çok sayıda VMware VM 'Leri geçirmek için betiklerin nasıl kullanılacağını açıklar
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754275"
---
# <a name="scale-migration-of-vmware-vms"></a>VMware VM 'lerinin ölçeğini ölçeklendirme 

Bu makale, aracısız yöntemi kullanarak çok sayıda VMware sanal makinesini (VM) geçirmek için betikleri nasıl kullanacağınızı anlamanıza yardımcı olur. Geçişleri ölçeklendirmek için [Azure geçişi PowerShell modülünü](./tutorial-migrate-vmware-powershell.md)kullanırsınız. 

Azure geçişi VMware geçiş Otomasyonu betikleri, GitHub 'da [Azure PowerShell örnekleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) deposunda indirilebilir. Betikler, VMware VM 'lerini aracısız geçiş yöntemi kullanılarak Azure 'a geçirmek için kullanılabilir. Bu betikte kullanılan Azure geçişi PowerShell komutları [burada](./tutorial-migrate-vmware-powershell.md)belgelenmiştir.

## <a name="current-limitations"></a>Geçerli sınırlamalar
- Bu betikler, VMware VM 'lerinin tüm disklerle geçirilmesini destekler. Bir VMware VM 'sine eklenen diskleri seçmeli olarak çoğaltmak istiyorsanız betikleri güncelleştirebilirsiniz. 
- Betikler değerlendirme önerilerinin kullanımını destekler. Değerlendirme önerileri kullanılmıyorsa, VMware VM 'ye bağlı tüm diskler aynı yönetilen disk türüne geçirilir (Standart veya Premium). Aynı VM ile birden fazla yönetilen disk türü kullanmak istiyorsanız betikleri güncelleştirebilirsiniz

## <a name="prerequisites"></a>Önkoşullar

- Azure ve VMware 'yi geçiş için hazırlamak üzere [bulma öğreticisini doldurun](tutorial-discover-vmware.md) .
- [VMware VM](./tutorial-assess-vmware-azure-vm.md) 'lerini Azure 'a geçirmeden önce değerlendirmek için ikinci öğreticiyi tamamlamanızı öneririz.
- Azure PowerShell `Az` modüle sahipsiniz. Azure PowerShell yüklemeniz veya yükseltmeniz gerekiyorsa [Azure PowerShell yüklemek ve yapılandırmak için bu kılavuzu](/powershell/azure/install-az-ps) izleyin

## <a name="install-azure-migrate-powershell-module"></a>Azure geçişi PowerShell modülünü yükler

Azure geçişi PowerShell modülü önizlemede kullanılabilir. Aşağıdaki komutu kullanarak PowerShell modülünü yüklemeniz gerekir. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV giriş dosyası
Tüm önkoşulların tamamlanmasını tamamladıktan sonra, geçirmek istediğiniz her kaynak sanal makine için verileri içeren bir CSV dosyası oluşturmanız gerekir. Tüm betikler aynı CSV dosyasında çalışmak üzere tasarlanmıştır. Başvurunuz için betikler klasöründe örnek bir CSV şablonu bulunur. CSV dosyası, değerlendirme önerilerini kullanabilmeniz ve hatta belirli bir sanal makine için belirli işlemlerin tetiklenmeyeceğini belirtmek üzere yapılandırılabilir. 

> [!NOTE]
> Aynı CSV dosyası, birden çok Azure geçişi projesinde VM 'Leri geçirmek için kullanılabilir.

### <a name="csv-file-schema"></a>CSV dosya şeması

**Sütun Başlığı** | **Açıklama**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Azure geçişi proje abonelik KIMLIĞINI sağlayın.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Azure geçişi kaynak grubu adını sağlayın.
AZMIGRATEPROJECT_NAME | Sunucuları geçirmek istediğiniz Azure geçişi projesinin adını belirtin. 
SOURCE_MACHINE_NAME | Azure geçişi projesindeki bulunan VM için kolay adı (görünen ad) belirtin.
AZMIGRATEASSESSMENT_NAME | Geçiş için yararlanılabilir olması gereken değerlendirmenin adını sağlayın.
AZMIGRATEGROUP_NAME | Azure geçişi değerlendirmesi için kullanılan grubun adını belirtin.
TARGET_RESOURCE_GROUP_NAME | VM 'nin geçirilmesi gereken Azure Kaynak grubunun adını belirtin.
TARGET_VNET_NAME| Geçirilen VM 'nin kullanması gereken Azure sanal ağının adını belirtin.
TARGET_SUBNET_NAME | Hedef sanal ağdaki, geçirilen VM 'nin kullanması gereken alt ağın adını belirtin. Boş bırakılırsa, "varsayılan" alt ağı kullanılacaktır.
TARGET_MACHINE_NAME | Geçirilen VM 'nin Azure 'da kullanması gereken adı girin. Boş bırakılırsa, kaynak makine adı kullanılacaktır.  
TARGET_MACHINE_SIZE | VM 'nin Azure 'da kullanması gereken SKU 'YU sağlayın. Bir VM 'yi Azure 'da D2_v2 VM 'ye geçirmek için, bu alandaki değeri "Standard_D2_v2" olarak belirtin. Bir değerlendirme kullanıyorsanız, bu değer değerlendirme önerisine göre türetilir.
LICENSE_TYPE | Windows Server VM 'Leri için Azure Hibrit Avantajı kullanmak istediğinizi belirtin. Azure Hibrit Avantajı avantajlarından yararlanmak için "WindowsServer" değerini kullanın. Aksi halde boş bırakın veya "NoLicenseType" kullanın.
OS_DISK_ID | Geçirilecek VM için işletim sistemi disk KIMLIĞI sağlayın. Kullanılacak disk KIMLIĞI, Get-AzMigrateServer cmdlet 'i kullanılarak alınan diskin benzersiz tanımlayıcı (UUID) özelliğidir. Betik, hiçbir değer sağlanmazsa, işletim sistemi diski olarak VM 'nin ilk diskini kullanır.
TARGET_DISKTYPE | Azure 'da VM 'nin tüm diskleri için kullanılacak disk türünü sağlayın. Premium yönetilen diskler için ' Premium_LRS ', standart SSD diskleri için ' StandardSSD_LRS ' ve standart HDD disklerini kullanmak için ' Standard_LRS ' kullanın. Bir değerlendirme kullanmayı seçerseniz, komut dosyası, sanal makinenin her diski için önerilen disk türlerini kullanarak öncelik verir. Değerlendirme kullanmıyorsanız veya herhangi bir değer belirtmezseniz, komut dosyası standart HDD disklerini varsayılan olarak kullanır.    
AVAILABILITYZONE_NUMBER | Geçirilen VM için kullanılacak kullanılabilirlik bölgesi numarasını belirtin. Kullanılabilirlik bölgelerini kullanmak istemediğiniz durumlarda bu alanı boş bırakabilirsiniz. 
AVAILABILITYSET_NAME | Geçirilen VM için kullanılacak kullanılabilirlik kümesinin adını belirtin. Kullanılabilirlik kümesini kullanmak istemediğiniz durumlarda bu alanı boş bırakabilirsiniz.
TURNOFF_SOURCESERVER | Geçiş sırasında kaynak VM 'yi kapatmak istiyorsanız ' Y ' seçeneğini belirtin. Aksi takdirde ' N ' kullanın. Boş bırakılırsa, komut dosyası değeri ' N ' olarak varsayar.
TESTMIGRATE_VNET_NAME | Test geçişi için kullanılacak sanal ağın adını belirtin.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Kaynak grubunu Azure 'da geçirilen VM tarafından kullanılacak şekilde güncelleştirmek istiyorsanız, Azure Kaynak grubunun adını belirtin, yoksa boş bırakın. 
UPDATED_TARGET_VNET_NAME | Geçirilen VM tarafından Azure 'da kullanılacak sanal ağı güncelleştirmek istiyorsanız, Azure sanal ağının adını belirtin, yoksa boş bırakın.
UPDATED_TARGET_MACHINE_NAME | Geçirilen VM tarafından Azure 'da kullanılacak adı güncelleştirmek istiyorsanız, kullanılacak yeni adı belirtin, aksi durumda boş bırakın.
UPDATED_TARGET_MACHINE_SIZE | Azure 'da geçirilen VM tarafından kullanılacak SKU 'YU güncelleştirmek istiyorsanız, kullanılacak yeni SKU 'yu belirtin, aksi durumda boş bırakın.
UPDATED_AVAILABILITYZONE_NUMBER | Azure 'da geçirilmiş VM tarafından kullanılacak kullanılabilirlik bölgesini güncelleştirmek istiyorsanız, kullanılacak yeni kullanılabilirlik bölgesini belirtin, aksi durumda boş bırakın.
UPDATED_AVAILABILITYSET_NAME | Azure 'da geçirilen VM tarafından kullanılacak kullanılabilirlik kümesini güncelleştirmek istiyorsanız, kullanılacak yeni kullanılabilirlik kümesini belirtin, aksi durumda boş bırakın.
UPDATE_NIC1_ID | Güncelleştirileceği NIC 'nin KIMLIĞINI belirtin. Boş bırakılırsa, betik değeri bulunan VM 'nin ilk NIC 'i olacak şekilde varsayar. VM 'nin NIC 'sini güncelleştirmek istemiyorsanız, NIC adı içeren tüm alanları boş bırakın. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Bu NIC için kullanılacak değeri belirtin. Bu NIC 'in birincil veya ikincil olması gerekip gerekmediğini belirtmek için "birincil", "Ikincil" veya "DoNotCreate" kullanın veya geçirilen VM 'de oluşturulmamalıdır. VM için birincil NIC olarak yalnızca bir NIC belirtilebilir. Güncelleştirmek istemiyorsanız boş bırakın.
UPDATED_TARGET_NIC1_SUBNET_NAME | Geçirilen VM 'de NIC için kullanılacak alt ağın adını belirtin. Güncelleştirmek istemiyorsanız boş bırakın.
UPDATED_TARGET_NIC1_IP | Statik IP kullanmak istiyorsanız, geçirilen VM 'de NIC tarafından kullanılacak IPv4 adresini belirtin. IP 'yi otomatik olarak atamak istiyorsanız "Auto" kullanın. Güncelleştirmek istemiyorsanız boş bırakın.
UPDATE_NIC2_ID | Güncelleştirileceği NIC 'nin KIMLIĞINI belirtin. Boş bırakılırsa, betik değeri bulunan VM 'nin ikinci NIC 'i olacak şekilde varsayar. VM 'nin NIC 'sini güncelleştirmek istemiyorsanız, NIC adı içeren tüm alanları boş bırakın.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Bu NIC için kullanılacak değeri belirtin. Bu NIC 'in birincil veya ikincil olması gerekip gerekmediğini belirtmek için "birincil", "Ikincil" veya "DoNotCreate" kullanın veya geçirilen VM 'de oluşturulmamalıdır. VM için birincil NIC olarak yalnızca bir NIC belirtilebilir. Güncelleştirmek istemiyorsanız boş bırakın.
UPDATED_TARGET_NIC2_SUBNET_NAME | Geçirilen VM 'de NIC için kullanılacak alt ağın adını belirtin. Güncelleştirmek istemiyorsanız boş bırakın.
UPDATED_TARGET_NIC2_IP | Statik IP kullanmak istiyorsanız, geçirilen VM 'de NIC tarafından kullanılacak IPv4 adresini belirtin. IP 'yi otomatik olarak atamak istiyorsanız "Auto" kullanın. Güncelleştirmek istemiyorsanız boş bırakın.
OK_TO_UPDATE | AzMigrate_UpdateMachineProperties betiği çalıştırıldığında VM özelliklerinin güncelleştirilip etkinleştirilmeyeceğini belirtmek için ' Y ' kullanın. ' N ' kullanın veya başka bir şekilde boş bırakın.
OK_TO_MIGRATE | AzMigrate_StartMigration betiği çalıştırıldığında VM 'nin geçirilmesi gerekip gerekmediğini belirtmek için ' Y ' kullanın. VM 'yi geçirmek istemiyorsanız ' N ' kullanın veya boş bırakın. 
OK_TO_USE_ASSESSMENT | AzMigrate_StartReplication betiği çalıştırıldığında VM 'nin değerlendirme önerilerini kullanarak çoğaltma başlatması gerekip gerekmediğini belirtmek için ' Y ' kullanın. Bu, CSV dosyasındaki TARGET_MACHINE_SIZE ve TARGET_DISKTYPE değerlerini geçersiz kılar. Değerlendirme önerilerini kullanmak istemiyorsanız ' N ' kullanın veya boş bırakın.
OK_TO_TESTMIGRATE | AzMigrate_StartTestMigration betiği çalıştırıldığında VM 'nin test geçirilmesi gerekip gerekmediğini belirtmek için ' Y ' kullanın. VM geçişini test etmek istemiyorsanız ' N ' kullanın veya boş bırakın. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | AzMigrate_ReplicationStatus betiği çalıştırıldığında VM 'nin çoğaltma durumunun güncelleştirilip güncelleştirilmediğini belirtmek için ' Y ' kullanın. Çoğaltma durumunu güncelleştirmek istemiyorsanız ' N ' kullanın veya boş bırakın.
OK_TO_CLEANUP | AzMigrate_StopReplication betiği çalıştırıldığında VM çoğaltmasının temizlenip temizlenmeyeceğini belirtmek için ' Y ' kullanın. ' N ' kullanın veya başka bir şekilde boş bırakın.
OK_TO_TESTMIGRATE_CLEANUP | AzMigrate_CleanUpTestMigration betiği çalıştırıldığında VM 'ye yönelik test geçişinin temizlenip temizlenmeyeceğini belirtmek için ' Y ' kullanın. ' N ' kullanın veya başka bir şekilde boş bırakın.


## <a name="script-execution"></a>Betik yürütme

CSV 'ye hazırladıktan sonra şirket içi VMware VM 'lerinizi geçirmek için aşağıdaki adımları gerçekleştirebilirsiniz.

**Indan #** | **Betik adı** | **Açıklama**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | CSV 'de listelenen tüm VM 'Ler için çoğaltmayı etkinleştirme betiği, sorun giderme için bir CSV çıkışı ve bir günlük dosyası oluşturur.
2 | AzMigrate_ReplicationStatus.ps1 | Çoğaltma durumunu kontrol edin, komut dosyası her bir VM için durum ve sorun giderme için bir günlük dosyası içeren bir CSV çıkışı oluşturur.
3 | AzMigrate_UpdateMachineProperties.ps1 | VM 'Lerin ilk çoğaltmasını tamamladıktan sonra, sanal makinenin hedef özelliklerini güncelleştirmek için bu betiği kullanın (Işlem ve ağ özellikleri). Betik, her VM için iş ayrıntılarına sahip bir CSV çıkışı oluşturur.
4 | AzMigrate_StartTestMigration.ps1 |  Test geçişi için yapılandırılmış CSV 'de listelenen tüm VM 'Ler için yük devretme testini başlatın. Betik, her VM için iş ayrıntılarına sahip bir CSV çıkışı oluşturur.
5 | AzMigrate_CleanUpTestMigration.ps1 | Test yük devretmesi yapılan VM 'Leri el ile doğruladıktan sonra, test geçiş temizlemesi için yapılandırılmış CSV 'de listelenen tüm VM 'ler için test yük devretme VM 'lerini temizlemek üzere bu betiği kullanın. Betik, her VM için iş ayrıntılarına sahip bir CSV çıkışı oluşturur.
6 | AzMigrate_StartMigration.ps1 | Geçiş için yapılandırılmış CSV 'de listelenen tüm sanal makinelerin geçişini başlatın. Betik, her VM için iş ayrıntılarına sahip bir CSV çıkışı oluşturur.
7 | AzMigrate_StopReplication.ps1 | Başarılı bir şekilde geçirildikten sonra veya diğer nedenlerden dolayı çoğaltmayı iptal etmek istiyorsanız VM 'nin çoğaltmasını sonlandırır. Betik, her VM için iş ayrıntılarına sahip bir CSV çıkışı oluşturur.


Aşağıdaki betikler, çoğaltmayı etkinleştirme, test geçişini başlatma, VM özelliklerini güncelleştirme vb. gibi tüm Azure geçiş işlemlerine yönelik diğer betikler tarafından çağırılır. Tüm betiklerin aynı klasörde/yolda bulunduğundan emin olun. 

**Indan #** | **Betik adı** | **Açıklama**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Değerlendirme özelliklerini (API aracılığıyla) alma, bulunan VM 'Ler ve VM 'Leri çoğaltma işlevlerini içeren ortak betik. 
2 | AzMigrate_CSV_Processor.ps1 | Günlükleri yükleme, okuma ve yazdırma dahil olmak üzere CSV dosyası işlemleri için kullanılan işlevleri içeren ortak betik. 
3 | AzMigrate_Logger.ps1 | Azure geçiş Otomasyonu işlemlerine yönelik günlük dosyası oluşturmak için ortak betik çağrıldı. Günlük dosyası log.Scriptname.Datetime.txt biçiminde olacaktır.

Yukarıdaki ' a ek olarak, klasör farklı Azure geçiş işlemleri için özel betikler oluşturmak için iskelet çerçevesini içeren AzMigrate_Template.ps1 de içerir. 

### <a name="script-execution-syntax"></a>Betik yürütme söz dizimi

Betikleri indirdikten sonra betikler aşağıdaki gibi yürütülebilir.

Input.csv dosyasını kullanarak VM 'Ler için çoğaltma başlatmak üzere betiği yürütmek istiyorsanız aşağıdaki sözdizimini kullanın. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Azure geçişi ile VMware VM 'Leri geçirmek için Azure PowerShell kullanma hakkında daha fazla bilgi edinmek için [öğreticiyi](./tutorial-migrate-vmware-powershell.md)izleyin.