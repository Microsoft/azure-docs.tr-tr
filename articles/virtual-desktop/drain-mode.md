---
title: Windows sanal masaüstü boşaltma modunu ayarlama-Azure
description: Windows sanal masaüstü 'nde boşaltma modunu yapılandırma ve kullanma.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509166"
---
# <a name="set-drain-mode"></a>Boşaltma modunu ayarla

Boşaltma modu, kullanıcı oturumlarını kesintiye uğratmadan düzeltme ekleri uygulamak ve bakım yapmak istediğinizde bir oturum konağını yalıtır. Yalıtılmış olduğunda, oturum ana bilgisayarı yeni kullanıcı oturumlarını kabul etmez. Yeni bağlantılar, bir sonraki kullanılabilir oturum konağına yönlendirilir. Oturum ana bilgisayarındaki mevcut bağlantılar, Kullanıcı oturumu kapatıncaya veya yönetici oturumu sona erdirene kadar çalışmaya devam edecektir. Oturum Ana bilgisayarı boşaltma modundayken, yöneticiler Windows sanal masaüstü hizmetinden geçmeden sunucuya uzaktan de bağlanabilir. Bu ayarı, hem havuza alınan hem de kişisel masaüstlerine uygulayabilirsiniz.

## <a name="set-drain-mode-using-the-azure-portal"></a>Azure portal kullanarak boşaltma modunu ayarlama

Azure portal boşaltma modunu açmak için:

1. Azure portal açın ve yalıtmak istediğiniz konak havuzuna gidin.

2. Gezinti menüsünde **oturum Konakları**' nı seçin.

3. Sonra, boşaltma modunu açmak istediğiniz konakları seçin, sonra **boşaltma modunu aç**' ı seçin.

4. Boşaltma modunu devre dışı bırakmak için, boşaltma modunun etkinleştirildiği konak havuzlarını seçin, sonra **boşalt modunu** Kapat ' ı seçin.

## <a name="set-drain-mode-using-powershell"></a>PowerShell kullanarak boşaltma modunu ayarlama

PowerShell 'de boşalt modunu [Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true) komutunun bir parçası olan *allownewsessions* parametresiyle ayarlayabilirsiniz.

Boşaltma modunu etkinleştirmek için bu cmdlet 'i çalıştırın:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Boşaltma modunu devre dışı bırakmak için bu cmdlet 'i çalıştırın:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Bu komutu, ayarı uyguladığınız her oturum ana bilgisayarı için çalıştırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Windows sanal masaüstü Azure portal hakkında daha fazla bilgi edinmek istiyorsanız [öğreticilerimize](create-host-pools-azure-marketplace.md)göz atın. Temel bilgileri zaten biliyorsanız, Azure portal ( [Msix uygulama iliştirme](app-attach-azure-portal.md) ve [Azure Advisor](azure-advisor.md)gibi) kullanabileceğiniz diğer özelliklerden bazılarını inceleyin.

PowerShell yöntemini kullanıyorsanız ve modülün ne yapabileceklerini görmek isterseniz, [Windows sanal masaüstü Için PowerShell modülünü](powershell-module.md) ve [PowerShell başvurunuz](/powershell/module/az.desktopvirtualization/)' ı ayarlamayı inceleyin.