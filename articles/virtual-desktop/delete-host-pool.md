---
title: Windows sanal masaüstü ana bilgisayar havuzunu silme-Azure
description: Windows sanal masaüstündeki bir konak havuzunu silme.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3693a82a0d9c9544ed6a14d4596d544725125bd3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447055"
---
# <a name="delete-a-host-pool"></a>Konak havuzunu silme

Windows sanal masaüstü 'nde oluşturulan tüm konak havuzları, oturum konaklarına ve uygulama gruplarına eklenir. Bir konak havuzunu silmek için, ilişkili uygulama gruplarını ve oturum konaklarınızı silmeniz gerekir. Bir uygulama grubunun silinmesi oldukça basittir, ancak bir oturum konağını silmek daha karmaşıktır. Bir oturum konağını sildiğinizde, bunun etkin bir Kullanıcı oturumu olmadığından emin olmanız gerekir. Kullanıcıların veri kaybetmesini engellemek için oturum ana bilgisayarındaki tüm kullanıcı oturumlarının oturumu kapatması gerekir.

## <a name="delete-a-host-pool-with-powershell"></a>PowerShell ile bir konak havuzunu silme

PowerShell kullanarak bir konak havuzunu silmek için, önce konak havuzundaki tüm uygulama gruplarını silmeniz gerekir. Tüm uygulama gruplarını silmek için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Sonra, konak havuzunu silmek için bu cmdlet 'i çalıştırın:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Bu cmdlet, konak havuzunun oturum ana bilgisayarındaki tüm mevcut kullanıcı oturumlarını kaldırır. Ayrıca, konak havuzundan oturum ana bilgisayarının kaydını siler. Aboneliğiniz dahilinde ilgili tüm sanal makineler (VM) hala mevcut olacaktır.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Azure portal bir konak havuzunu silme

Azure portal bir konak havuzunu silmek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **Windows sanal masaüstü** araması yapın ve seçin.

3. Sayfanın sol tarafındaki menüde **konak havuzları** ' nı seçin, sonra silmek istediğiniz konak havuzunun adını seçin.

4. Sayfanın sol tarafındaki menüde **uygulama grupları**' nı seçin.

5. Silinecek konak havuzundaki tüm uygulama gruplarını seçin ve ardından **Kaldır**' ı seçin.

6. Uygulama gruplarını kaldırdıktan sonra, sayfanın sol tarafındaki menüye gidin ve **genel bakış**' ı seçin.

7. **Kaldır**' ı seçin.

8. Silmekte olduğunuz ana bilgisayar havuzunda oturum Konakları varsa, izninizin devam etmesini isteyen bir ileti görürsünüz. **Evet**’i seçin.

9. Azure portal artık tüm oturum konaklarına kaldırılacak ve konak havuzunu silecek. Oturum ana bilgisayarıyla ilgili VM 'Ler silinmez ve aboneliğinizde kalır.

## <a name="next-steps"></a>Sonraki adımlar

Bir konak havuzu oluşturmayı öğrenmek için şu makalelere göz atın:

- [Azure portalıyla yeni bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md)
- [PowerShell ile ana bilgisayar havuzu oluşturma](create-host-pools-powershell.md)

Konak havuzu ayarlarını yapılandırma hakkında bilgi edinmek için şu makalelere göz atın:

- [Bir konak havuzu için Uzak Masaüstü Protokolü özelliklerini özelleştirme](customize-rdp-properties.md)
- [Windows Sanal Masaüstü yük dengeleme yöntemini yapılandırma](configure-host-pool-load-balancing.md)
- [Kişisel masaüstü konak havuzu atama türünü yapılandırma](configure-host-pool-personal-desktop-assignment-type.md)