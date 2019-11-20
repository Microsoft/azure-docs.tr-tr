---
title: Kota hataları
description: Kaynakları Azure Resource Manager ile dağıttığınızda kaynak kota hatalarının nasıl çözümleneceğini açıklar.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 8760b027d815dfc70000db5aea534bf10f60b759
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149442"
---
# <a name="resolve-errors-for-resource-quotas"></a>Kaynak kotaları hatalarını çözme

Bu makalede, kaynakları dağıttığınızda karşılaşabileceğiniz kota hataları açıklanır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Belirti

Azure kotalarınızı aşan kaynaklar oluşturan bir şablon dağıtırsanız, şöyle görünen bir dağıtım hatası alırsınız:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Ya da şunları görebilirsiniz:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Nedeni

Kotalar kaynak grubu başına, aboneliklere, hesaplara ve diğer kapsamlara uygulanır. Örneğin aboneliğiniz bir bölge için çekirdek sayısını sınırlandıracak şekilde yapılandırılmış olabilir. İzin verilen miktardan daha fazla çekirdeği olan bir sanal makine dağıtmaya çalışırsanız, kotanın aşıldığını belirten bir hata alırsınız.
Tüm kota bilgileri için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Sorun giderme

### <a name="azure-cli"></a>Azure CLI

Azure CLı için, sanal makine kotalarını bulmak için `az vm list-usage` komutunu kullanın.

```azurecli
az vm list-usage --location "South Central US"
```

Şunu döndürür:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

PowerShell için, sanal makine kotalarını bulmak için **Get-AzVMUsage** komutunu kullanın.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Şunu döndürür:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Çözüm

Kota artışı istemek için portala gidin ve bir destek sorunu verin. Destek sorunu içinde, dağıtmak istediğiniz bölge için kotasında bir artış isteyin.

> [!NOTE]
> Kaynak grupları için kota, aboneliğin tamamına değil, her bir bölgeye yöneliktir. Batı ABD 'de 30 çekirdek dağıtmanız gerekiyorsa, Batı ABD 'de 30 Kaynak Yöneticisi çekirdek sormanız gerekir. Erişiminiz olan herhangi bir bölgede 30 çekirdek dağıtmanız gerekiyorsa, tüm bölgelerde 30 Kaynak Yöneticisi çekirdek isteymelisiniz.
>
>

1. **Abonelikler**'i seçin.

   ![Abonelikler](./media/resource-manager-quota-errors/subscriptions.png)

2. Kotasını artırmanız gereken aboneliği seçin.

   ![Abonelik seçme](./media/resource-manager-quota-errors/select-subscription.png)

3. **Kullanım + kotalar** ' ı seçin

   ![Kullanım ve kotaları seçin](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. Sağ üst köşede **istek artışı**' nı seçin.

   ![İstek artışı](./media/resource-manager-quota-errors/request-increase.png)

5. Artırmanız gereken kota türünün formlarını doldurun.

   ![Formu doldur](./media/resource-manager-quota-errors/forms.png)