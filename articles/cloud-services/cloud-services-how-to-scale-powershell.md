---
title: Windows PowerShell 'de Azure bulut hizmetini ölçeklendirme | Microsoft Docs
description: Klasik Azure 'da bir Web rolünü veya çalışan rolünü ölçeklendirmek için PowerShell 'in nasıl kullanılacağını öğrenin.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359044"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>PowerShell 'de bir bulut hizmetini ölçeklendirme

Örnek ekleyerek veya kaldırarak bir Web rolü veya çalışan rolünü ölçeklendirmek için Windows PowerShell kullanabilirsiniz.  

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Aboneliğiniz üzerinde PowerShell aracılığıyla herhangi bir işlem gerçekleştirebilmek için oturum açmanız gerekir:

```powershell
Add-AzureAccount
```

Hesabınızla ilişkili birden fazla aboneliğiniz varsa, bulut hizmetinizin bulunduğu yere bağlı olarak geçerli aboneliği değiştirmeniz gerekebilir. Geçerli aboneliği denetlemek için şunu çalıştırın:

```powershell
Get-AzureSubscription -Current
```

Geçerli aboneliği değiştirmeniz gerekiyorsa şunu çalıştırın:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Rolünüzün geçerli örnek sayısını denetleyin

Rolünüzün geçerli durumunu denetlemek için şunu çalıştırın:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Rol hakkında, geçerli işletim sistemi sürümü ve örnek sayısı dahil olmak üzere bilgileri geri almalısınız. Bu durumda, rolün tek bir örneği vardır.

![Rol hakkında bilgi](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Daha fazla örnek ekleyerek rolü ölçeklendirin

Rolünüzün ölçeğini genişletmek için, istenen sayıda örneği **set-AzureRole** cmdlet 'ine **Count** parametresi olarak geçirin:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Yeni örnekler sağlandığında ve başlatıldığında cmdlet, anlık olarak engeller. Bu süre boyunca, yeni bir PowerShell penceresi açıp daha önce gösterildiği gibi **Get-AzureRole** ' i çağırırsanız, yeni hedef örnek sayısını görürsünüz. Portalda rol durumunu incelüyorsunuz, yeni örneğin başlangıcını görmeniz gerekir:

![Portal 'dan başlayan VM örneği](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Yeni örnekler başlatıldıktan sonra cmdlet başarıyla döndürülür:

![Rol örneği başarı artışı](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Örnekleri kaldırarak rolü ölçeklendirin

Aynı şekilde örnekleri kaldırarak bir rolü ölçeklendirebilirsiniz. **Set-AzureRole** üzerindeki **Count** parametresini, işlem sırasında ölçeklendirme işlemi tamamlandıktan sonra olmasını istediğiniz örnek sayısına ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bulut hizmetleri için otomatik ölçeklendirmeyi PowerShell 'den yapılandırmak mümkün değildir. Bunu yapmak için bkz. [bulut hizmetini otomatik ölçeklendirme](cloud-services-how-to-scale-portal.md).
