---
title: PowerShell ile uygulama kopyalama-Azure App Service
description: App Service uygulamanızı PowerShell kullanarak yeni bir uygulamaya nasıl klonleyeceğinizi öğrenin.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 47efcfc4bf2b0268d6720b659786300e751e861d
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983690"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>PowerShell kullanarak uygulama kopyalamayı Azure App Service

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Microsoft Azure PowerShell Version 1.1.0 sürümü ile, var olan bir App Service uygulamasını farklı bir bölgede veya `New-AzWebApp` aynı bölgede yeni oluşturulan bir uygulamaya kopyalamanızı sağlayan yeni bir seçenek eklenmiştir. Bu seçenek, müşterilerin farklı bölgelerde birkaç uygulamayı hızlı ve kolay bir şekilde dağıtmasını sağlar.

Uygulama kopyalama standart, Premium, Premium v2 ve yalıtılmış App Service planları için desteklenir. Yeni özellik App Service yedekleme özelliğiyle aynı sınırlamaları kullanır, bkz. [Azure App Service bir uygulamayı](manage-backup.md)yedekleme.

## <a name="cloning-an-existing-app"></a>Mevcut bir uygulamayı kopyalama
Senaryo: Orta Güney ABD bölgesindeki mevcut bir uygulama ve içerikleri Orta Kuzey ABD bölgesinde yeni bir uygulamaya kopyalamak istiyorsunuz. Bu, `-SourceWebApp` seçeneğiyle yeni bir uygulama oluşturmak için PowerShell cmdlet 'inin Azure Resource Manager sürümü kullanılarak gerçekleştirilebilir.

Kaynak uygulamanın bulunduğu kaynak grubu adını bilmenin ardından, kaynak uygulamanın bilgilerini (Bu durumda adlı `source-webapp`) almak için aşağıdaki PowerShell komutunu kullanabilirsiniz:

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Yeni bir App Service planı oluşturmak için aşağıdaki örnekte gösterildiği gibi `New-AzAppServicePlan` komutunu kullanabilirsiniz

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

`New-AzWebApp` Komutunu kullanarak yeni uygulamayı Orta Kuzey ABD bölgesinde oluşturup mevcut bir App Service planına bağlayabilirsiniz. Ayrıca, kaynak uygulamayla aynı kaynak grubunu kullanabilir veya aşağıdaki komutta gösterildiği gibi yeni bir kaynak grubu tanımlayabilirsiniz:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Tüm ilişkili dağıtım yuvaları dahil var olan bir uygulamayı kopyalamak için `IncludeSourceWebAppSlots` parametresini kullanmanız gerekir.  `IncludeSourceWebAppSlots` Parametresinin yalnızca tüm yuvaları dahil olmak üzere tüm bir uygulamayı kopyalamak için desteklendiğini unutmayın. Aşağıdaki PowerShell komutu, `New-AzWebApp` komutla birlikte bu parametrenin kullanımını gösterir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Mevcut bir uygulamayı aynı bölgede kopyalamak için, aynı bölgede yeni bir kaynak grubu ve yeni bir App Service planı oluşturmanız ve ardından uygulamayı kopyalamak için aşağıdaki PowerShell komutunu kullanmanız gerekir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Mevcut bir uygulamayı bir App Service Ortamı kopyalama
Senaryo: Orta Güney ABD bölgesindeki mevcut bir uygulama ve içerikleri yeni bir uygulamaya kopyalamak istiyorsanız mevcut bir App Service Ortamı (Ao).

Kaynak uygulamanın bulunduğu kaynak grubu adını bilmenin ardından, kaynak uygulamanın bilgilerini (Bu durumda adlı `source-webapp`) almak için aşağıdaki PowerShell komutunu kullanabilirsiniz:

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Ao 'nun adını ve Ao 'nun ait olduğu kaynak grubu adını bilmenin yanı sıra, aşağıdaki komutta gösterildiği gibi mevcut Ao 'da yeni uygulamayı oluşturabilirsiniz:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametre, eski bir nedenden dolayı gereklidir, ancak uygulamayı bir AO 'da oluşturduğunuzda yok sayılır. 

## <a name="cloning-an-existing-app-slot"></a>Var olan bir uygulama yuvasını kopyalama
Senaryo: Uygulamanın mevcut bir dağıtım yuvasını yeni bir uygulamaya veya yeni bir yuvaya kopyalamak istiyorsunuz. Yeni uygulama, özgün uygulama yuvası ile aynı bölgede veya farklı bir bölgede olabilir.

Kaynak uygulamanın bulunduğu kaynak grubu adını bilmenin ardından, kaynak uygulama yuvasının bilgilerini (Bu durumda adlı `source-appslot`) bağlı olarak `source-app`almak için aşağıdaki PowerShell komutunu kullanabilirsiniz:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Aşağıdaki komut, kaynak uygulamanın bir kopyasını yeni bir uygulamaya oluşturmayı gösterir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Uygulama kopyalanırken Traffic Manager yapılandırma
Çok bölgeli uygulamalar oluşturma ve bu uygulamalara giden trafiği yönlendirmek için Azure Traffic Manager yapılandırma, müşterilerin uygulamalarının yüksek oranda kullanılabilir olmasını sağlamak için önemli bir senaryodur. Mevcut bir uygulamayı kopyalarken, her iki uygulamayı da yeni bir Traffic Manager profiline veya var olan bir profile bağlama seçeneğiniz vardır. Yalnızca Traffic Manager sürümü Azure Resource Manager desteklenir.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Uygulama kopyalanırken yeni bir Traffic Manager profili oluşturma
Senaryo: Bir uygulamayı başka bir bölgeye kopyalamak istiyorsanız, her iki uygulamayı da içeren bir Azure Resource Manager Traffic Manager profili yapılandırın. Aşağıdaki komut, yeni bir Traffic Manager profili yapılandırılırken, kaynak uygulamanın bir kopyasını yeni bir uygulamaya oluşturmayı gösterir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Yeni kopyalanmış uygulama varolan bir Traffic Manager profiline ekleniyor
Senaryo: Zaten bir Azure Resource Manager Traffic Manager profiliniz var ve her iki uygulamayı uç nokta olarak eklemek istiyorsunuz. Bunu yapmak için, önce mevcut Traffic Manager profil KIMLIĞINI birleştirmek gerekir. Abonelik KIMLIĞI, kaynak grubu adı ve mevcut Traffic Manager profili adı gerekir.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Trafik Yöneticisi KIMLIĞINI aldıktan sonra aşağıdaki komut, mevcut bir Traffic Manager profiline eklenirken kaynak uygulamanın bir kopyasını yeni bir uygulamaya oluşturmayı gösterir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Geçerli kısıtlamalar
Uygulama kopyalama işleminin bilinen kısıtlamaları aşağıda verilmiştir:

* Otomatik ölçeklendirme ayarları kopyalanamadı
* Yedekleme zamanlaması ayarları kopyalanamadı
* VNET ayarları kopyalanamadı
* Uygulama öngörüleri hedef uygulamada otomatik olarak ayarlanmadı
* Kolay kimlik doğrulama ayarları klonlanmıyor
* Kudu uzantısı kopyalanamadı
* Ipucu kuralları klonlanmıyor
* Veritabanı içeriği kopyalanamadı
* Farklı bir ölçek birimine kopyalama yaptıysanız giden IP adresleri değişir
* Linux uygulamaları için kullanılamaz

### <a name="references"></a>Referanslar
* [App Service kopyalama](app-service-web-app-cloning.md)
* [Azure App Service bir uygulamayı yedekleme](manage-backup.md)
* [Azure Traffic Manager Preview için Azure Resource Manager desteği](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service Ortamı’na giriş](environment/intro.md)
* [Azure PowerShell’i Azure Resource Manager ile kullanma](../azure-resource-manager/manage-resources-powershell.md)

