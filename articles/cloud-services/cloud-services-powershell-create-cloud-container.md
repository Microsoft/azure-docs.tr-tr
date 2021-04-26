---
title: PowerShell ile bir bulut hizmeti (klasik) kapsayıcısı oluşturma | Microsoft Docs
description: Bu makalede, PowerShell ile bir bulut hizmeti kapsayıcısının nasıl oluşturulacağı açıklanmaktadır. Kapsayıcı Web ve çalışan rollerini barındırır.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610425"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Boş bir bulut hizmeti (klasik) kapsayıcısı oluşturmak için bir Azure PowerShell komutu kullanın

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bu makalede Azure PowerShell cmdlet 'lerini kullanarak hızlı bir şekilde Cloud Services kapsayıcının nasıl oluşturulacağı açıklanmaktadır. Lütfen aşağıdaki adımları izleyin:

1. [Azure PowerShell İndirmeleri](https://aka.ms/webpi-azps) sayfasından Microsoft Azure PowerShell cmdlet 'ini yükler.
2. PowerShell komut istemi ' ni açın.
3. Oturum açmak için [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) kullanın.

   > [!NOTE]
   > Azure PowerShell cmdlet 'ini yükleme ve Azure aboneliğinize bağlanma hakkında daha fazla yönerge için, [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/)konusuna bakın.
   >
   >
4. Boş bir Azure bulut hizmeti kapsayıcısı oluşturmak için **New-AzureService** cmdlet 'ini kullanın.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Cmdlet 'ini çağırmak için bu örneği izleyin:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Azure bulut hizmetini oluşturma hakkında daha fazla bilgi için şunu çalıştırın:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Sonraki adımlar

* Bulut hizmeti dağıtımını yönetmek için [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)ve [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) komutlarına bakın. Daha fazla bilgi için [Cloud Services 'ı yapılandırma](cloud-services-how-to-configure-portal.md) konusuna da başvurabilirsiniz.
* Bulut hizmeti projenizi Azure 'da yayımlamak için [arşivlenmiş bulut hizmetleri deposundaki](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery) **PublishCloudService.ps1** kod örneğine bakın.