---
title: SQL VM kaynak sağlayıcısı ile Azure 'da SQL sanal makinelerini toplu kaydetme | Microsoft Docs
description: Yönetilebilirlik geliştirmek için SQL VM kaynak sağlayıcısı ile VM 'Leri SQL Server toplu kaydettirin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 97541484501a3ecdd1bd5998314c1ee9e7a4e3a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489067"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM kaynak sağlayıcısı ile Azure 'da SQL sanal makinelerini toplu kaydetme

Bu makalede, `Register-SqlVMs` PowerShell cmdlet 'ini kullanarak SQL VM kaynak sağlayıcısı ile Azure 'da SQL Server sanal makinenizin (VM) toplu olarak nasıl kaydedileceği açıklanmaktadır.

`Register-SqlVMs` cmdlet 'i belirli bir abonelik, kaynak grubu veya belirli bir sanal makine listesi içindeki tüm sanal makineleri kaydetmek için kullanılabilir. Cmdlet 'i, sanal makineleri _hafif_ yönetim modunda kaydeder ve sonra hem [rapor hem de günlük dosyası](#output-description)oluşturur. 

Kayıt işlemi risk içermez, kapalı kalma süresi yoktur ve SQL Server veya sanal makineyi yeniden başlatmaz. 

Kaynak sağlayıcısı hakkında daha fazla bilgi için bkz. [SQL VM kaynak sağlayıcısı](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Önkoşullar

SQL Server VM kaynak sağlayıcısına kaydetmek için şunlar gerekir: 

- [Kaynak sağlayıcısına kayıtlı](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) ve kaydedilmemiş SQL Server sanal makineler Içeren bir [Azure aboneliği](https://azure.microsoft.com/free/) . 
- Sanal makineleri kaydetmek için kullanılan istemci kimlik bilgileri şu RBAC rollerinin hiçbirinde bulunuyor: **sanal makine katılımcısı**, **katkıda bulunan**veya **sahip**. 
- En son [az PowerShell](/powershell/azure/new-azureps-module-az)sürümü. 
- En son [az. SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)sürümü.

## <a name="getting-started"></a>Başlarken

Devam etmeden önce, önce betiğin yerel bir kopyasını oluşturmanız, PowerShell modülü olarak içeri aktarmanız ve Azure 'a bağlanmanız gerekir. 

### <a name="create-script"></a>Betik oluştur

Betiği oluşturmak için, bu makalenin sonundaki [tam betiği](#full-script) kopyalayın ve `RegisterSqlVMs.psm1`olarak yerel olarak kaydedin. 

### <a name="import-script"></a>Betiği içeri aktar

Betik oluşturulduktan sonra, PowerShell terminalinde bir modül olarak içeri aktarabilirsiniz. 

Bir yönetim PowerShell terminali açın ve `RegisterSqlVMs.psm1` dosyasını kaydettiğiniz konuma gidin. Ardından, komut dosyasını modül olarak içeri aktarmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Azure'a Bağlanma

Azure 'a bağlanmak için aşağıdaki PowerShell cmdlet 'ini kullanın:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Abonelikler listesindeki tüm VM 'Ler 

Tüm SQL Server sanal makinelerini bir abonelik listesine kaydetmek için aşağıdaki cmdlet 'i kullanın:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Örnek çıktı: 

```
Number of Subscriptions registration failed for 
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

## <a name="specific-vms-in-single-resource-group"></a>Tek kaynak grubundaki belirli VM 'Ler

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

## <a name="specific-vm"></a>Belirli VM

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

`Register-SqlVMs` cmdlet 'i her kullanıldığında hem rapor hem de günlük dosyası oluşturulur. 

### <a name="report"></a>Rapor

Rapor, `RegisterSqlVMScriptReport<Timestamp>.txt` adlı bir `.txt` dosyası olarak oluşturulur. burada zaman damgası, cmdlet 'in başlatıldığı zaman olur. Raporda aşağıdaki ayrıntılar listelenir:

| **Çıkış değeri** | **Açıklama** |
| :--------------  | :-------------- | 
| Erişiminiz olmadığından veya kimlik bilgileriniz yanlış olduğundan abonelik kaydı sayısı başarısız oldu | Bu, sağlanan kimlik doğrulamasıyla sorunlu olan aboneliklerin sayısını ve listesini sağlar. Ayrıntılı hata, abonelik KIMLIĞINI arayarak günlükte bulunabilir. | 
| RP 'ye kayıtlı olmadıklarından denenmemiş abonelik sayısı | Bu bölüm, SQL VM kaynak sağlayıcısına kayıtlı olmayan aboneliklerin sayısını ve listesini içerir. |
| Bulunan toplam VM sayısı | Cmdlet 'e geçirilen parametrelerin kapsamında bulunan sanal makine sayısı. | 
| VM 'Ler zaten kayıtlı | Kaynak sağlayıcısına zaten kayıtlı olduğu için Atlanan sanal makine sayısı. |
| Başarıyla kaydedilen VM sayısı | Cmdlet 'i çalıştırdıktan sonra başarıyla kaydedilen sanal makine sayısı. Kayıtlı sanal makineleri `SubscriptionID, Resource Group, Virtual Machine`biçimde listeler. | 
| Hata nedeniyle kayıt başarısız olan sanal makinelerin sayısı | Bir hata nedeniyle kaydettirilemedi sanal makine sayısı. Hatanın ayrıntıları günlük dosyasında bulunabilir. | 
| VM 'de sanal makine veya rüzgar Aracısı çalışmadığı için Atlanan VM sayısı | Sanal makine ya da sanal makinedeki Konuk Aracısı çalışmadığı için kaydedibir sanal makine sayısı ve listesi çalışmıyor. Bunlar, sanal makine veya Konuk Aracısı başlatıldıktan sonra yeniden denenebilirler. Ayrıntılar günlük dosyasında bulunabilir. |
| Windows üzerinde SQL Server çalıştırmayan için Atlanan VM sayısı | SQL Server çalışmadığı veya bir Windows sanal makinesi olmadığı için Atlanan sanal makine sayısı. Sanal makineler `SubscriptionID, Resource Group, Virtual Machine`biçiminde listelenir. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Günlük 

Hatalar, komut dosyasının başlatıldığı zaman damgasıdır `VMsNotRegisteredDueToError<Timestamp>.log` adlı günlük dosyasına kaydedilir. Hata abonelik düzeyindedir, günlük, virgülle ayrılmış SubscriptionID ve hata mesajını içerir. Hata sanal makine kaydıyla çalışıyorsa, günlük abonelik KIMLIĞI, kaynak grubu adı, sanal makine adı, hata kodu ve virgülle ayrılmış ileti içerir. 

## <a name="remarks"></a>Açıklamalar

Kaynak sağlayıcısına SQL Server VM 'Leri, belirtilen betiği kullanarak kaydettiğinizde aşağıdakileri göz önünde bulundurun:

- Kaynak sağlayıcısına kayıt, SQL Server VM çalışan bir Konuk Aracısı gerektirir. Windows Server 2008 görüntülerinin Konuk Aracısı yoktur, bu nedenle bu sanal makineler başarısız olur ve [Noagent yönetim modu](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)kullanılarak el ile kaydedilmelidir.
- Saydam hataları aşmak için yeniden deneme mantığı yerleşik olarak bulunur. Sanal makine başarıyla kaydedilmişse, hızlı bir işlemdir. Ancak kayıt başarısız olursa, her sanal makine yeniden denenir.  Bu nedenle, kayıt işlemini tamamlamaya yönelik önemli bir zamana izin vermeniz gerekir; ancak gerçek zamanlı gereksinim, hataların türüne ve sayısına bağlıdır. 

## <a name="full-script"></a>Tam betik

GitHub 'daki tam betik için bkz. [az PowerShell Ile SQL VM 'Leri toplu kaydetme](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Tam betiği kopyalayın ve `RegisterSqLVMs.psm1`olarak kaydedin.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]



## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [Windows VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)
