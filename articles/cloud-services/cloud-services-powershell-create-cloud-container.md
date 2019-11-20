---
title: PowerShell ile bir bulut hizmeti kapsayıcısı oluşturma | Microsoft Docs
description: Bu makalede, PowerShell ile bir bulut hizmeti kapsayıcısının nasıl oluşturulacağı açıklanmaktadır. Kapsayıcı Web ve çalışan rollerini barındırır.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359496"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Boş bir bulut hizmeti kapsayıcısı oluşturmak için bir Azure PowerShell komutu kullanın

Bu makalede Azure PowerShell cmdlet 'lerini kullanarak hızlı bir şekilde Cloud Services kapsayıcının nasıl oluşturulacağı açıklanmaktadır. Lütfen aşağıdaki adımları izleyin:

1. [Azure PowerShell İndirmeleri](https://aka.ms/webpi-azps) sayfasından Microsoft Azure PowerShell cmdlet 'ini yükler.
2. PowerShell komut istemi ' ni açın.
3. Oturum açmak için [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) kullanın.

   > [!NOTE]
   > Azure PowerShell cmdlet 'ini yükleme ve Azure aboneliğinize bağlanma hakkında daha fazla yönerge için, [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview)konusuna bakın.
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

* Bulut hizmeti dağıtımını yönetmek için [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)ve [set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) komutlarına bakın. Daha fazla bilgi için [Cloud Services 'ı yapılandırma](cloud-services-how-to-configure-portal.md) konusuna da başvurabilirsiniz.
* Bulut hizmeti projenizi Azure 'da yayımlamak için, [arşivlenmiş bulut hizmetleri deposundaki](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery) **publishcloudservice. ps1** kod örneğine bakın.