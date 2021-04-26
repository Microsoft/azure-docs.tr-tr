---
title: SQL IaaS Aracısı Uzantısı ile birden çok SQL VM 'yi Azure 'da kaydetme
description: Yönetilebilirlik geliştirmek için SQL IaaS Aracısı uzantısına sahip VM 'Leri SQL Server toplu kaydetme.
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
ms.openlocfilehash: ebf835cad79f8c011be2fec91f6f4644ecd0941f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284133"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>SQL IaaS Aracısı Uzantısı ile birden çok SQL VM 'yi Azure 'da kaydetme
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure PowerShell cmdlet 'ini kullanarak [SQL IaaS Aracısı uzantısı](sql-server-iaas-agent-extension-automate-management.md) ile SQL Server sanal makinelerinizin (VM) Azure 'da toplu olarak nasıl kaydedileceği açıklanmaktadır `Register-SqlVMs` . 


Bu makale, SQL Server VM 'Leri toplu olarak el ile kaydetmenizi öğretir. Alternatif olarak, [tüm SQL Server sanal makinelerini otomatik olarak](sql-agent-extension-automatic-registration-all-vms.md) veya [bireysel SQL Server VM 'leri el ile](sql-agent-extension-manually-register-single-vm.md)kaydedebilirsiniz. 

## <a name="overview"></a>Genel Bakış

`Register-SqlVMs`Cmdlet 'i belirli bir abonelik, kaynak grubu veya belirli bir sanal makine listesi listesindeki tüm sanal makineleri kaydetmek için kullanılabilir. Cmdlet 'i, sanal makineleri [lightweight_ yönetim modunda](sql-server-iaas-agent-extension-automate-management.md#management-modes)kaydeder ve sonra hem [rapor hem de günlük dosyası](#output-description)oluşturur. 

Kayıt işlemi risk içermez, kapalı kalma süresi yoktur ve SQL Server hizmeti ya da sanal makineyi yeniden başlatmaz. 

## <a name="prerequisites"></a>Önkoşullar

SQL Server VM uzantıya kaydetmek için şunlar gerekir: 

- [ **Microsoft. sqlvirtualmachine** sağlayıcısına kayıtlı](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-resource-provider) ve kaydedilmemiş SQL Server sanal makineler içeren bir [Azure aboneliği](https://azure.microsoft.com/free/) . 
- Sanal makineleri kaydetmek için kullanılan istemci kimlik bilgileri şu Azure rollerinin hiçbirinde bulunuyor: **sanal makine katılımcısı**, **katkıda bulunan** veya **sahip**. 
- En son [az PowerShell sürümü (en az 5,0)](/powershell/azure/new-azureps-module-az). 


## <a name="get-started"></a>başlarken

Devam etmeden önce, önce betiğin yerel bir kopyasını oluşturmanız, PowerShell modülü olarak içeri aktarmanız ve Azure 'a bağlanmanız gerekir. 

### <a name="create-the-script"></a>Betiği oluşturma

Betiği oluşturmak için, bu makalenin sonundaki [tam betiği](#full-script) kopyalayın ve yerel olarak kaydedin `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>Betiği içeri aktar

Betik oluşturulduktan sonra, PowerShell terminalinde bir modül olarak içeri aktarabilirsiniz. 

Bir yönetim PowerShell terminali açın ve dosyayı kaydettiğiniz yere gidin `RegisterSqlVMs.psm1` . Ardından, komut dosyasını modül olarak içeri aktarmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Azure'a Bağlanma

Azure 'a bağlanmak için aşağıdaki PowerShell cmdlet 'ini kullanın:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>Abonelikler listesindeki tüm VM 'Ler 

Tüm SQL Server sanal makinelerini bir abonelik listesine kaydetmek için aşağıdaki cmdlet 'i kullanın:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Örnek çıktı: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Tek bir abonelikteki tüm VM 'Ler

Tüm SQL Server sanal makinelerini tek bir aboneliğe kaydetmek için aşağıdaki cmdlet 'i kullanın: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Örnek çıktı:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Birden çok kaynak grubunda bulunan tüm VM 'Ler

Tüm SQL Server sanal makinelerini tek bir abonelik içindeki birden çok kaynak grubuna kaydetmek için aşağıdaki cmdlet 'i kullanın:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Örnek çıktı:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Bir kaynak grubundaki tüm VM 'Ler

Tüm SQL Server sanal makinelerini tek bir kaynak grubuna kaydetmek için aşağıdaki cmdlet 'i kullanın: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Örnek çıktı:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>Tek bir kaynak grubundaki belirli VM 'Ler

Belirli SQL Server sanal makinelerini tek bir kaynak grubuna kaydetmek için aşağıdaki cmdlet 'i kullanın:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Örnek çıktı:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>Belirli bir VM

Belirli bir SQL Server sanal makinesini kaydetmek için aşağıdaki cmdlet 'i kullanın: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Örnek çıktı:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Çıkış açıklaması

Cmdlet her kullanıldığında hem rapor hem de günlük dosyası oluşturulur `Register-SqlVMs` . 

### <a name="report"></a>Rapor

Rapor, bir dosya olarak oluşturulur `.txt` `RegisterSqlVMScriptReport<Timestamp>.txt` . burada zaman damgası, cmdlet 'in başlatıldığı zaman. Raporda aşağıdaki ayrıntılar listelenir:

| **Çıkış değeri** | **Açıklama** |
| :--------------  | :-------------- | 
| Erişiminiz olmadığından veya kimlik bilgileriniz yanlış olduğundan abonelik kaydı sayısı başarısız oldu | Bu, sağlanan kimlik doğrulamasıyla sorunlu olan aboneliklerin sayısını ve listesini sağlar. Ayrıntılı hata, abonelik KIMLIĞINI arayarak günlükte bulunabilir. | 
| Kaynak sağlayıcısına kayıtlı olmadıklarından denenmemiş abonelik sayısı | Bu bölüm, SQL IaaS Aracısı uzantısına kayıtlı olmayan aboneliklerin sayısını ve listesini içerir. |
| Bulunan toplam VM sayısı | Cmdlet 'e geçirilen parametrelerin kapsamında bulunan sanal makine sayısı. | 
| VM 'Ler zaten kayıtlı | Zaten uzantıya kayıtlı olduklarından atlanan sanal makine sayısı. |
| Başarıyla kaydedilen VM sayısı | Cmdlet 'i çalıştırdıktan sonra başarıyla kaydedilen sanal makine sayısı. Kayıtlı sanal makineleri biçimde listeler `SubscriptionID, Resource Group, Virtual Machine` . | 
| Hata nedeniyle kayıt başarısız olan sanal makinelerin sayısı | Bir hata nedeniyle kaydettirilemedi sanal makine sayısı. Hatanın ayrıntıları günlük dosyasında bulunabilir. | 
| VM 'de sanal makine veya rüzgar Aracısı çalışmadığı için Atlanan VM sayısı | Sanal makine ya da sanal makinedeki Konuk Aracısı çalışmadığı için kaydedibir sanal makine sayısı ve listesi çalışmıyor. Bunlar, sanal makine veya Konuk Aracısı başlatıldıktan sonra yeniden denenebilirler. Ayrıntılar günlük dosyasında bulunabilir. |
| Windows üzerinde SQL Server çalıştırmayan için Atlanan VM sayısı | SQL Server çalışmadığı veya bir Windows sanal makinesi olmadığı için Atlanan sanal makine sayısı. Sanal makineler biçiminde listelenir `SubscriptionID, Resource Group, Virtual Machine` . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Günlük 

Hatalar `VMsNotRegisteredDueToError<Timestamp>.log` , komut dosyasının başlatıldığı zaman damgasıdır adlı günlük dosyasına kaydedilir. Hata abonelik düzeyindedir, günlük, virgülle ayrılmış abonelik KIMLIĞINI ve hata iletisini içerir. Hata sanal makine kaydıyla çalışıyorsa, günlük abonelik KIMLIĞINI, kaynak grubu adını, sanal makine adını, hata kodunu ve virgülle ayrılmış iletiyi içerir. 

## <a name="remarks"></a>Açıklamalar

Belirtilen betiği kullanarak uzantıya sahip SQL Server VM 'Leri kaydettiğinizde, aşağıdakileri göz önünde bulundurun:

- Uzantıya kaydolma, SQL Server VM çalışan bir konuk aracısının olmasını gerektirir. Windows Server 2008 görüntülerinin Konuk Aracısı yoktur, bu nedenle bu sanal makineler başarısız olur ve [Noagent yönetim modu](sql-server-iaas-agent-extension-automate-management.md#management-modes)kullanılarak el ile kaydedilmelidir.
- Saydam hataları aşmak için yeniden deneme mantığı yerleşik olarak bulunur. Sanal makine başarıyla kaydedilmişse, hızlı bir işlemdir. Ancak kayıt başarısız olursa, her sanal makine yeniden denenir.  Bu nedenle, kayıt işlemini tamamlamaya yönelik önemli bir zamana izin vermeniz gerekir; ancak gerçek zamanlı gereksinim, hataların türüne ve sayısına bağlıdır. 

## <a name="full-script"></a>Tam betik

GitHub 'daki tam betik için bkz. [az PowerShell ile SQL Server VM 'Leri toplu kaydetme](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Tam betiği kopyalayın ve olarak kaydedin `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri inceleyin: 

* [Windows VM 'de SQL Server genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](frequently-asked-questions-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](../../database/doc-changes-updates-release-notes.md)
