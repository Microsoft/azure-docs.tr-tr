---
title: Key Vault klasik bir Azure ile tümleştirin SQL Server VM
description: Azure Key Vault ile kullanmak üzere SQL Server şifreleme yapılandırmasını nasıl otomatikleştirebileceğinizi öğrenin. Bu konu başlığı altında, klasik dağıtım modelinde SQL Server sanal makinelerle oluşturma Azure Key Vault tümleştirmenin nasıl kullanılacağı açıklanmaktadır.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: ddf23126154f5bc62c49f62ac4adf517d6987091
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033468"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure sanal makinelerinde SQL Server için Azure Key Vault tümleştirmesini yapılandırma (klasik)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasik](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Genel Bakış
[Saydam veri şifrelemesi (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [sütun DÜZEYINDE şifreleme (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)ve [yedekleme şifrelemesi](https://msdn.microsoft.com/library/dn449489.aspx)gibi birden çok SQL Server şifreleme özelliği vardır. Bu şifreleme biçimleri, şifreleme için kullandığınız şifreleme anahtarlarını yönetmenizi ve depolamanızı gerektirir. Azure Key Vault (AKV) hizmeti, güvenli ve yüksek oranda kullanılabilir bir konumda bu anahtarların güvenliğini ve yönetimini geliştirmek için tasarlanmıştır. [SQL Server Bağlayıcısı](https://www.microsoft.com/download/details.aspx?id=45344) , SQL Server Azure Key Vault bu anahtarları kullanmasına olanak sağlar.

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir.

Şirket içi makinelerle SQL Server çalıştırıyorsanız, Şirket [içi SQL Server makinenizden Azure Key Vault erişmek için izleyebileceğiniz adımlar](https://msdn.microsoft.com/library/dn198405.aspx)vardır. Ancak Azure VM 'lerinde SQL Server için *Azure Key Vault tümleştirme* özelliğini kullanarak zamandan tasarruf edebilirsiniz. Bu özelliği etkinleştirmek için birkaç Azure PowerShell cmdlet 'i sayesinde, anahtar kasanıza erişmek için bir SQL sanal makinesi için gereken yapılandırmayı otomatikleştirin.

Bu özellik etkinleştirildiğinde, SQL Server Bağlayıcısı otomatik olarak yüklenir, EKM sağlayıcısını Azure Key Vault erişim için yapılandırır ve kasanıza erişmenize izin veren kimlik bilgisini oluşturur. Daha önce bahsedilen şirket içi belgelerdeki adımlara bakarsanız, bu özelliğin 2 ve 3. adımları otomatikleştirdiği hakkında bilgi alabilirsiniz. Hala el ile yapmanız gereken tek şey, anahtar kasasını ve anahtarları oluşturmaktır. Buradan, SQL sanal makinenizin tüm kurulumu otomatikleştirilir. Bu özellik bu kurulumu tamamladıktan sonra, veritabanlarını veya yedeklemelerinizi normal şekilde şifrelemeye başlamak için T-SQL deyimlerini çalıştırabilirsiniz.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV tümleştirmesini yapılandırma
Azure Key Vault tümleştirmesini yapılandırmak için PowerShell 'i kullanın. Aşağıdaki bölümlerde, gerekli parametrelere ve sonra örnek bir PowerShell betiğine genel bir bakış sağlanmaktadır.

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS uzantısını yükler
İlk olarak, [SQL Server IaaS uzantısını yüklemeniz](../classic/sql-server-agent-extension.md)gerekir.

### <a name="understand-the-input-parameters"></a>Giriş parametrelerini anlayın
Aşağıdaki tabloda, sonraki bölümde PowerShell betiğini çalıştırmak için gereken parametreler listelenmektedir.

| Parametre | Açıklama | Örnek |
| --- | --- | --- |
| **$akvURL** |**Anahtar Kasası URL 'SI** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Hizmet sorumlusu adı** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Hizmet sorumlusu gizli anahtarı** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Kimlik bilgisi adı**: AKV Tümleştirme, VM’nin anahtar kasasına erişim sağlamasına izin vererek, SQL Server’da bir kimlik bilgisi oluşturur Bu kimlik bilgisi için bir ad seçin. |"mycred1" |
| **$vmName** |**Sanal makine adı**: daha önce oluşturulmuş BIR SQL VM 'nin adı. |"myvmname" |
| **$serviceName** |**Hizmet adı**: SQL VM Ile Ilişkili bulut hizmeti adı. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>PowerShell ile AKV tümleştirmesini etkinleştirme
**New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet 'ı Azure Key Vault tümleştirme özelliği için bir yapılandırma nesnesi oluşturur. **Set-AzureVMSqlServerExtension** Bu tümleştirmeyi **Keyvaultcredentialsettings** parametresiyle yapılandırır. Aşağıdaki adımlarda bu komutların nasıl kullanılacağı gösterilmektedir.

1. Azure PowerShell, ilk olarak giriş parametrelerini bu konunun önceki bölümlerinde açıklandığı gibi belirli değerlerinizle yapılandırın. Aşağıdaki betik bir örnektir.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Ardından, AKV tümleştirmesini yapılandırmak ve etkinleştirmek için aşağıdaki betiği kullanın.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS Aracısı uzantısı, SQL VM 'yi bu yeni yapılandırmayla güncelleştirecek.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

