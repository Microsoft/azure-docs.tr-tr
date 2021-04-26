---
title: Azure Izleyici kullanarak klasik ölçüm uyarıları oluşturma ve yönetme
description: Azure portal, CLı veya PowerShell kullanarak klasik ölçüm uyarı kuralları oluşturma, görüntüleme ve yönetme hakkında bilgi edinin.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 854867bb6f40bdd8f0fac28e69d936a4027760b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714383"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Azure Izleyici kullanarak klasik ölçüm uyarılarını oluşturma, görüntüleme ve yönetme

> [!WARNING]
> Bu makalede, klasik eski ölçüm uyarılarının nasıl oluşturulacağı açıklanır. Azure Izleyici artık [daha yeni bir neredeyse gerçek zamanlı ölçüm uyarılarını ve yeni bir uyarı deneyimini](./alerts-overview.md)desteklemektedir. Klasik uyarılar, genel bulut kullanıcıları için [devre](./monitoring-classic-retirement.md) dışı bırakılsa da hala **31 Mayıs 2021** tarihine kadar sınırlı kullanımda. Azure Kamu bulutu ve Azure Çin 21Vianet için klasik uyarılar, **29 şubat 2024** tarihinde devre dışı bırakılacak.
>

Azure Izleyici 'de klasik ölçüm uyarıları, ölçülerinizin biri eşiğe girdiğinde bildirim almanın bir yolunu sağlar. Klasik ölçüm uyarıları yalnızca boyutlu olmayan ölçümlerde uyarı vermeyi sağlayan eski bir işlevdir. Klasik ölçüm uyarıları üzerinde geliştirilmiş işlevlere sahip ölçüm uyarıları adlı yeni bir işlev vardır. [Ölçüm uyarılarına genel bakış](./alerts-metric-overview.md)bölümünde yeni ölçüm uyarıları işlevselliği hakkında daha fazla bilgi edinebilirsiniz. Bu makalede, Azure portal, Azure CLı ve PowerShell aracılığıyla klasik ölçüm uyarısı kurallarını oluşturma, görüntüleme ve yönetme hakkında açıklama göndereceğiz.

## <a name="with-azure-portal"></a>Azure portal ile

1. [Portalda](https://portal.azure.com/), izlemek istediğiniz kaynağı bulun ve seçin.

2. **İzleme** bölümünde Uyarılar ' ı **(klasik)** seçin. Metin ve simge farklı kaynaklar için biraz farklılık gösterebilir. Burada **Uyarılar (klasik)** bulamazsanız **uyarı veya** **uyarı kurallarında** bulabilirsiniz.

    ![İzleme](media/alerts-classic-portal/AlertRulesButton.png)

3. **Ölçüm uyarısı Ekle (klasik)** komutunu seçin ve ardından alanları girin.

    ![Uyarı Ekle](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. Uyarı kuralınızı **adlandırın** . Sonra da bildirim e-postalarında görüntülenen bir **Açıklama** seçin.

5. İzlemek istediğiniz **ölçümü** seçin. Ardından, ölçüm için bir **koşul** ve **eşik** değeri seçin. Ayrıca **, uyarı** tetikleyiciden önce ölçüm kuralının karşılanması gereken süreyi seçin. Örneğin, "son 5 dakika Içinde" dönemini kullanırsanız ve uyarınız %80 ' ün üzerinde bir CPU 'YU alıyorsa, uyarı, CPU sürekli olarak 5 dakika boyunca %80 ' den fazla olduğunda tetiklenir. İlk tetikleyici oluştuktan sonra, CPU 5 dakika boyunca %80 altında kaldığında yeniden tetiklenir. CPU Ölçümü ölçümü her dakika olur.

6. Yönetici ve ortak yöneticilerin, uyarı tetiklendiğinde e-posta bildirimleri almasını istiyorsanız **e-posta sahipleri** ' nı seçin...

7. Uyarı tetiklendiğinde ek e-posta adreslerine bildirim göndermek istiyorsanız, bunları **ek yönetici e-postası** alanına ekleyin. Birden çok e-postayı şu biçimde noktalı virgülle ayırın: *e-posta \@ contoso. com; Email2 \@ contoso.com*

8. Uyarı tetiklendiğinde çağrılabilmesi istiyorsanız **Web kancası** alanına GEÇERLI bir URI koyun.

9. Azure Otomasyonu kullanıyorsanız, uyarı tetiklendiğinde çalıştırılacak bir runbook seçebilirsiniz.

10. Uyarıyı oluşturmak için **Tamam ' ı** seçin.

Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

Bir uyarı oluşturduktan sonra, bunu seçebilir ve aşağıdaki görevlerden birini gerçekleştirebilirsiniz:

* Ölçüm eşiğini ve önceki günün gerçek değerlerini gösteren bir grafiği görüntüler.
* Düzenleyin veya silin.
* Bu uyarı için bildirimleri almayı geçici olarak durdurmak veya yeniden başlatmak istiyorsanız, **devre dışı bırakın** veya **etkinleştirin** .

## <a name="with-azure-cli"></a>Azure CLI ile

Önceki bölümlerde Azure portal kullanarak ölçüm uyarı kurallarının nasıl oluşturulacağı, görüntüleneceği ve yönetileceği açıklanmaktadır. Bu bölümde, platformlar arası [Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak nasıl yapılacağı açıklanır. Azure CLı 'yı kullanmaya başlamanın en hızlı yolu [Azure Cloud Shell](../../cloud-shell/overview.md)kullanmaktır.

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Bir kaynak grubundaki tüm klasik ölçüm uyarısı kurallarını al

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Belirli bir klasik ölçüm uyarısı kuralının ayrıntılarına bakın

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Klasik ölçüm uyarısı kuralı oluşturma

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Klasik ölçüm uyarısı kuralını silme

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>PowerShell ile

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu bölümlerde, klasik ölçüm uyarılarını oluşturma, görüntüleme ve yönetme PowerShell komutlarının nasıl kullanılacağı gösterilmektedir. Makalesindeki örneklerde, klasik ölçüm uyarıları için Azure Izleyici cmdlet 'lerini nasıl kullanabileceğiniz gösterilmektedir.

1. Henüz yapmadıysanız, PowerShell 'i bilgisayarınızda çalışacak şekilde ayarlayın. Daha fazla bilgi için bkz. [PowerShell 'ı yüklemek ve yapılandırmak](/powershell/azure/). Ayrıca Azure izleyici [(Öngörüler) cmdlet 'Lerinde](/powershell/module/az.applicationinsights)Azure izleyici PowerShell cmdlet 'lerinin tüm listesini inceleyebilirsiniz.

2. İlk olarak, Azure aboneliğinizde oturum açın.

    ```powershell
    Connect-AzAccount
    ```

3. Bir oturum açma ekranı görürsünüz. Hesabınızda oturum açtıktan sonra Tenantıd ve varsayılan abonelik KIMLIĞI görüntülenir. Tüm Azure cmdlet 'leri varsayılan aboneliğiniz bağlamında çalışır. Erişiminiz olan aboneliklerin listesini görüntülemek için aşağıdaki komutu kullanın:

    ```powershell
    Get-AzSubscription
    ```

4. Çalışma bağlamını farklı bir aboneliğe dönüştürmek için aşağıdaki komutu kullanın:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Bir kaynak grubundaki tüm klasik ölçüm uyarısı kurallarını alabilirsiniz:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Klasik ölçüm uyarısı kuralının ayrıntılarını görüntüleyebilirsiniz

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Bir hedef kaynak için ayarlanmış tüm uyarı kurallarını alabilirsiniz. Örneğin, bir VM 'de ayarlanan tüm uyarı kuralları.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klasik uyarı kuralları artık PowerShell aracılığıyla oluşturulamaz. Bir uyarı kuralı oluşturmak için yeni [' Add-AzMetricAlertRule '](/powershell/module/az.monitor/add-azmetricalertrule) komutunu kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Kaynak Yöneticisi şablonuyla klasik bir ölçüm uyarısı oluşturun](./alerts-enable-template.md).
- [Klasik bir ölçüm uyarısına sahip bir Web kancası kullanarak Azure olmayan bir sisteme bildirme](./alerts-webhooks.md).