---
title: Windows sanal masaüstü yük dengelemesini Yapılandırma-Azure
description: Windows sanal masaüstü ortamı için yük dengeleme yöntemini yapılandırma.
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0e742a046d43fef6e8263f73fc2ca8460848ad40
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448126"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows Sanal Masaüstü yük dengeleme yöntemini yapılandırma

Bir konak havuzu için yük dengeleme yöntemini yapılandırmak, Windows sanal masaüstü ortamını gereksinimlerinize daha iyi uyacak şekilde ayarlamanıza olanak sağlar.

>[!NOTE]
> Bu, kullanıcıların konak havuzundaki bir oturum ana bilgisayarına her zaman bir 1:1 eşlemesi olduğundan, kalıcı bir masaüstü konak havuzu için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, PowerShell modülünü indirip yüklemek ve Azure hesabınızda oturum açmak için [Windows sanal masaüstü PowerShell modülünü ayarlama](powershell-module.md) bölümündeki yönergeleri izlediğiniz varsayılmaktadır.

## <a name="configure-breadth-first-load-balancing"></a>Enine ilk yük dengelemeyi yapılandırma

Enine ilk yük dengeleme, yeni kalıcı olmayan konak havuzları için varsayılan yapılandırmadır. Enine ilk yük dengeleme, yeni kullanıcı oturumlarını konak havuzundaki kullanılabilir tüm oturum konaklarına dağıtır. Enine ilk yük dengelemeyi yapılandırırken, konak havuzundaki oturum ana bilgisayarı başına en fazla oturum sınırı ayarlayabilirsiniz.

Bir konak havuzunu, en fazla oturum sınırını ayarlamadan önce enine yük dengelemesi gerçekleştirecek şekilde yapılandırmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Bundan sonra, birinci düzey yük dengeleme yöntemini ayarladığınızdan emin olmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Bir konak havuzunu, birinci düzey yük dengelemeyi gerçekleştirecek şekilde yapılandırmak ve yeni bir en fazla oturum sınırı kullanmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Derinlik-ilk yük dengelemeyi yapılandırma

Derinlik-ilk yük dengeleme, yeni kullanıcı oturumlarını en fazla bağlantı sayısına sahip kullanılabilir bir oturum ana bilgisayarına dağıtır ancak en fazla oturum sınırı eşiğine ulaşmaz.

>[!IMPORTANT]
>Derinlik-ilk yük dengelemeyi yapılandırırken, konak havuzundaki oturum ana bilgisayarı başına en fazla oturum sınırı ayarlamanız gerekir.

Bir konak havuzunu derinlik-ilk yük dengelemesi gerçekleştirecek şekilde yapılandırmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> Derinlik-ilk yük dengeleme algoritması, oturum ana bilgisayar sayısı üst sınırını () temel alarak oturumları oturum konaklarına dağıtır `-MaxSessionLimit` . Bu parametrenin varsayılan değeri `999999` , aynı zamanda bu değişkeni olarak ayarlayabileceğiniz mümkün olan en yüksek sayıdır. Derinlik-ilk yük dengeleme algoritmasını kullandığınızda bu parametre gereklidir. En iyi olası kullanıcı deneyimi için, en fazla oturum ana bilgisayar sınırı parametresini ortamınıza en uygun bir sayıyla değiştirdiğinizden emin olun.

Ayarın güncelleştirildiğinden emin olmak için şu cmdlet 'i çalıştırın:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Azure portal ile yük dengelemeyi yapılandırma

Yük dengelemeyi Azure portal de yapılandırabilirsiniz.

Yük dengelemeyi yapılandırmak için:

1. Azure portal ' de oturum açın https://portal.azure.com .
2. Hizmetler altında **Windows sanal masaüstü** araması yapın ve seçin.
3. Windows sanal masaüstü sayfasında **konak havuzları**' nı seçin.
4. Düzenlemek istediğiniz konak havuzunun adını seçin.
5. **Özellikler**’i seçin.
6. Alana **en fazla oturum sınırını** girin ve açılan menüde bu konak havuzu için istediğiniz **Yük Dengeleme algoritmasını** seçin.
7. **Kaydet**’i seçin. Bu, yeni yük dengeleme ayarlarını uygular.