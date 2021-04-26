---
title: Öğretici - Azure Maliyet Yönetimi'nden dışarı aktarılan verileri oluşturma ve yönetme
description: Bu makalede, dış sistemlerde kullanabilmeniz için dışarı aktarılan Azure Maliyet Yönetimi verilerini nasıl oluşturup yönetebileceğiniz gösterilir.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 08bf165254c8759a978b406b3e9a990a41ab7a30
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873223"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Öğretici: Dışarı aktarılan verileri oluşturma ve yönetme

Maliyet Analizi öğreticisini okuduysanız, Maliyet Yönetimi verilerinizi el ile indirme konusunda bilgi sahibisiniz. Ancak günlük, haftalık veya aylık düzende otomatik olarak Maliyet Yönetimi verilerinizi Azure depolama alanına aktaran, yinelenen bir görev oluşturabilirsiniz. Dışarı aktarılan veriler CSV biçimindedir ve Maliyet Yönetimi tarafından toplanan tüm bilgileri içerir. Daha sonra Azure depolama alanındaki dışarı aktarılan verileri dış sistemlerle kullanabilir ve kendi özel verilerinizle birleştirebilirsiniz. Ayrıca dışarı aktarılan verilerinizi pano gibi bir dış sistemde veya diğer mali sistemde kullanabilirsiniz.

Azure maliyet verilerinizi Azure Depolama alanına aktarmak için zamanlama oluşturma hakkındaki [Azure Maliyet Yönetimi ile depolama alanı dışarı aktarma işlemlerini zamanlama](https://www.youtube.com/watch?v=rWa_xI1aRzo) videosunu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Bu öğreticideki örnek, maliyet yönetimi verilerinizi dışarı aktarmada ve ardından verilerin başarılı bir şekilde dışarı aktarıldığını doğrulamada size yol gösterir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Günlük bir dışarı aktarma oluşturma
> * Verilerin toplandığını doğrulama

## <a name="prerequisites"></a>Ön koşullar
[Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ve [Microsoft Müşteri Sözleşmesi](get-started-partners.md) müşterileri dahil olmak üzere birçok farklı Azure hesap türünde veriler dışarı aktarılabilir. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](understand-cost-mgt-data.md). Aşağıdaki Azure izinleri veya kapsamları abonelik başına kullanıcı ve grup tarafından yapılan veri dışarı aktarımı için desteklenir. Kapsamlar hakkında daha fazla bilgi için bkz. [Kapsamları anlama ve birlikte çalışma](understand-work-scopes.md).

- Sahip â € "bir abonelik için zamanlanmış dışarı aktarmaları oluşturabilir, değiştirebilir veya silebilir.
- Katkıda bulunan â € "kendi zamanlanmış dışarı aktarmaları oluşturabilir, değiştirebilir veya silebilir. Başkaları tarafından oluşturulan zamanlanmış dışarı aktarmaların adını değiştirebilir.
- Okuyucu â € ", iznine sahip oldukları dışarı aktarmaları zamanlayabilir.

Azure Depolama hesapları için:
- Yazma izinlerinin, dışarı aktarma üzerindeki izinlerden bağımsız olarak yapılandırılmış depolama hesabını değiştirmesi gerekir.
- Azure depolama hesabınızın blob veya dosya depolama için yapılandırılmış olması gerekir.

Yeni bir aboneliğiniz varsa Maliyet Yönetimi özelliklerini hemen kullanamazsınız. Maliyet Yönetimi özelliklerini kullanabilmek için 48 saat kadar beklemeniz gerekebilir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma
[https://portal.azure.com](https://portal.azure.com/) adresinden Azure portalında oturum açın.

## <a name="create-a-daily-export"></a>Günlük bir dışarı aktarma oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Dışarı aktarma oluşturmak veya görüntülemek ya da zamanlamak için Azure portalında istediğiniz kapsamı açıp menüden **Maliyet analizi**'ni seçin. Örneğin **Abonelikler**'e gidin, listeden bir abonelik belirleyin ve menüden **Maliyet analizi**'ni seçin. Maliyet analizi sayfasının üstünde **Ayarlar**’ı ve sonra **Dışarı Aktarmalar**’ı seçin.

> [!NOTE]
> - Dışarı aktarmayı aboneliklerin yanı sıra, kaynak grupları, yönetim grupları, departmanlar ve kayıtlar özelinde de oluşturabilirsiniz. Kapsamlar hakkında daha fazla bilgi için bkz. [Kapsamları anlama ve birlikte çalışma](understand-work-scopes.md).
>- Ödeme hesabı kapsamında veya müşteri kiracısında iş ortağı olarak oturum açtığınızda verileri iş ortağı depolama hesabıyla ilişkilendirilmiş olan Azure Depolama hesabına aktarabilirsiniz. Ancak CSP kiracınızda etkin bir aboneliğe sahip olmanız gerekir.

1. **Ekle**’yi seçin ve dışarı aktarma için bir ad yazın.
1. **Ölçüm** için bir seçim yapın:
    - **Gerçek maliyet (Kullanım ve Satın Almalar)** : Standart kullanımı ve satın almaları dışarı aktarmak için seçin
    - **İtfa edilen maliyet (Kullanım ve Satın Almalar)** : Azure rezervasyonları gibi satın almaların itfa edilen maliyetlerini dışarı aktarmak için seçin
1. **Dışarı aktarma türü** için bir seçim yapın:
    - **Aylık maliyetlerin güncel maliyetlerinin günlük dışa aktarılması,** aylık maliyetleriniz için günlük olarak yeni bir dışarı aktarma dosyası sağlar. En son veriler önceki günlük dışarı aktarmalardan toplanır.
    - **Son yedi gün için maliyet dışa aktarma** â € ", dışarı aktarmanın seçili başlangıç tarihinden itibaren son yedi güne ait maliyetlerinizin haftalık olarak dışa aktarılmasını oluşturur.
    - **Son ayın maliyetlerinin aylık dışa aktarılması** "â €", dışarı aktarmayı oluşturduğunuz geçerli aya kıyasla geçen ayın maliyetlerinin dışa aktarılmasını sağlar. Bundan itibaren zamanlama, önceki aylardaki maliyetlerinizle her yeni ayın beşinci gününde bir dışarı aktarma dosyası çalıştırır.
    - **Bir kerelik dışarı aktarma** â € ", geçmiş verilerin Azure Blob depolama alanına aktarılması için bir tarih aralığı seçmenize olanak sağlar. Seçtiğiniz günden itibaren en fazla 90 günlük geçmiş maliyetleri içeren bir dosyayı dışarı aktarabilirsiniz. Bu dışarı aktarma hemen çalışır ve iki saat içinde depolama hesabınızda kullanıma sunulur.
        Dışarı aktarma türünüze bağlı olarak bir başlangıç tarihi veya **Başlangıç** ve **Bitiş** tarihi seçin.
1. Azure depolama hesabınıza ait aboneliği belirttikten sonra bir kaynak grubu seçin veya yeni bir tane oluşturun.
1. Depolama hesabı adını seçin veya yeni bir tane oluşturun.
1. Konumu (Azure bölgesini) seçin.
1. Dışarı aktarma dosyasının gitmesini istediğiniz depolama kapsayıcısını ve dizin yolunu belirtin.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Yeni dışarı aktarma örneği" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Dışarı aktarma ayrıntılarınızı gözden geçirin ve **Oluştur**'u seçin.

Yeni dışarı aktarmanız, dışarı aktarma listesinde görünür. Yeni dışarı aktarmalar varsayılan olarak etkinleştirilir. Zamanlanmış bir dışarı aktarmayı devre dışı bırakmak veya silmek istiyorsanız, listedeki herhangi bir öğeye ve ardından **Devre dışı bırak** veya **Sil**'i seçin.

Başlangıçta, dışarı aktarmanın çalışmaya başlaması 12 ila 24 saat arası sürebilir. Ancak, verilerin dışarı aktarılan dosyalarda gösterilmesi daha uzun sürebilir.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Programlı olarak bir dışarı aktarma oluşturduğunuzda, `Microsoft.CostManagementExports` kaynak sağlayıcıyı depolama hesabının bulunduğu abonelikle el ile kaydetmeniz gerekir. Azure portal kullanarak dışarı aktarma oluşturduğunuzda kayıt otomatik olarak gerçekleşir. Kaynak sağlayıcılarının nasıl kaydedileceği hakkında daha fazla bilgi için bkz. [kayıt kaynak sağlayıcısı](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

Başlangıç olarak ortamınızı Azure CLI için hazırlayın:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Oturum açtıktan sonra geçerli dışarı aktarmaları görmek için [az costmanagement export list](/cli/azure/costmanagement/export#az_costmanagement_export_list) komutunu kullanın:

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Aboneliklerin yanı sıra kaynak grupları ve yönetim grupları için de dışarı aktarma oluşturabilirsiniz. Kapsamlar hakkında daha fazla bilgi için bkz. [Kapsamları anlama ve birlikte çalışma](understand-work-scopes.md).
   >* Ödeme hesabı kapsamında veya müşteri kiracısında iş ortağı olarak oturum açtığınızda verileri iş ortağı depolama hesabıyla ilişkilendirilmiş olan Azure Depolama hesabına aktarabilirsiniz. Ancak CSP kiracınızda etkin bir aboneliğe sahip olmanız gerekir.

1. Bir kaynak grubu oluşturun veya mevcut kaynak grubunu kullanın. Kaynak grubu oluşturmak için [az group create](/cli/azure/group#az_group_create) komutunu kullanın:

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Dışarı aktarmaları almak için bir depolama hesabı oluşturun veya mevcut depolama hesabını kullanın. Depolama hesabı oluşturmak için [az storage account create](/cli/azure/storage/account#az_storage_account_create) komutunu kullanın:

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Dışarı aktarmayı oluşturmak için [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) komutunu kullanın:

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   **--type** parametresi için `ActualCost`, `AmortizedCost` veya `Usage` değerini seçebilirsiniz.

   Bu örnekte `MonthToDate` kullanılmıştır. Dışarı aktarma işlemi ay başından bugüne kadarki maliyetler için her gün bir dışarı aktarma dosyası oluşturur. Bu ayın önceki günlük dışarı aktarmalarından alınan en güncel veriler toplanır.

1. Dışarı aktarma işleminizin ayrıntılarını görmek için [az costmanagement export show](/cli/azure/costmanagement/export#az_costmanagement_export_show) komutunu kullanın:

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Dışarı aktarmayı güncelleştirmek için [az costmanagement export update](/cli/azure/costmanagement/export#az_costmanagement_export_update) komutunu kullanın:

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Bu örnek çıkış dizinini değiştirir.

>[!NOTE]
>Başlangıçta, dışarı aktarmanın çalışmaya başlaması 12 ila 24 saat arası sürebilir. Öte yandan verilerin dışarı aktarılan dosyalarda gösterilmesi daha uzun sürebilir.

Dışarı aktarmayı silmek için [az costmanagement export delete](/cli/azure/costmanagement/export#az_costmanagement_export_delete) komutunu kullanın:

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Programlı olarak bir dışarı aktarma oluşturduğunuzda, `Microsoft.CostManagementExports` kaynak sağlayıcıyı depolama hesabının bulunduğu abonelikle el ile kaydetmeniz gerekir. Azure portal kullanarak dışarı aktarma oluşturduğunuzda kayıt otomatik olarak gerçekleşir. Kaynak sağlayıcılarının nasıl kaydedileceği hakkında daha fazla bilgi için bkz. [kayıt kaynak sağlayıcısı](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

Başlangıç olarak ortamınızı Azure PowerShell için hazırlayın:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.CostManagement** PowerShell modülü önizleme aşamasındayken `Install-Module` cmdlet'ini kullanarak modülü ayrı olarak yüklemeniz gerekir. Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. Oturum açtıktan sonra geçerli dışarı aktarmalarınızı görmek için [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) cmdlet'ini kullanın:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Aboneliklerin yanı sıra kaynak grupları ve yönetim grupları için de dışarı aktarma oluşturabilirsiniz. Kapsamlar hakkında daha fazla bilgi için bkz. [Kapsamları anlama ve birlikte çalışma](understand-work-scopes.md).
   >* Ödeme hesabı kapsamında veya müşteri kiracısında iş ortağı olarak oturum açtığınızda verileri iş ortağı depolama hesabıyla ilişkilendirilmiş olan Azure Depolama hesabına aktarabilirsiniz. Ancak CSP kiracınızda etkin bir aboneliğe sahip olmanız gerekir.

1. Bir kaynak grubu oluşturun veya mevcut kaynak grubunu kullanın. Kaynak grubu oluşturmak için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet'ini kullanın:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Dışarı aktarmaları almak için bir depolama hesabı oluşturun veya mevcut depolama hesabını kullanın. Depolama hesabı oluşturmak için [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet'ini kullanın:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) cmdlet'ini çalıştırarak dışarı aktarmayı oluşturun:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   **DefinitionType** parametresi için `ActualCost`, `AmortizedCost` veya `Usage` değerini seçebilirsiniz.

   Bu örnekte `MonthToDate` kullanılmıştır. Dışarı aktarma işlemi ay başından bugüne kadarki maliyetler için her gün bir dışarı aktarma dosyası oluşturur. Bu ayın önceki günlük dışarı aktarmalarından alınan en güncel veriler toplanır.

1. Dışarı aktarma işleminizin ayrıntılarını görmek için `Get-AzCostManagementExport` cmdlet'ini kullanın:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) cmdlet'ini kullanarak dışarı aktarmayı güncelleştirin:

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   Bu örnek çıkış dizinini değiştirir.

>[!NOTE]
>Başlangıçta, dışarı aktarmanın çalışmaya başlaması 12 ila 24 saat arası sürebilir. Öte yandan verilerin dışarı aktarılan dosyalarda gösterilmesi daha uzun sürebilir.

[Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) cmdlet'ini kullanarak dışarı aktarmayı silebilirsiniz:

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Dışarı aktarma zamanlaması

Zamanlanan dışarı aktarmalar, ilk oluşturduğunuz andaki saatten ve haftanın gününden etkilenir. Zamanlanan dışarı aktarma oluşturduğunuzda dışarı aktarma, sonraki her bir çalışma için aynı aralığı korur. Örneğin günlük çalışma sıklığına sahip olan ve ay başından bugüne kadarki aralıktaki maliyetleri kapsayan dışarı aktarma işlemi günlük olarak çalışır. Benzer şekilde haftalık dışarı aktarma da zamanlandığı gibi her hafta aynı günde çalışır. Dışarı aktarmanın tam teslim süresi garanti edilmez ve dışarı aktarılan veriler dört saatlik çalışma süresi içinde kullanılabilir.

Her dışarı aktarma işlemi yeni bir dosya oluşturacağından, eski dışarı aktarmaların üzerine yazılmaz.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Birden çok abonelik için dışarı aktarma oluşturma

Kurumsal Anlaşmanız varsa abonelik maliyeti bilgilerini tek bir kapsayıcıda toplamak için bir yönetim grubu kullanabilirsiniz. Daha sonra, yönetim grubu için maliyet yönetimi verilerini dışarı aktarabilirsiniz.

Diğer abonelik türlerinin yönetim gruplarına yönelik dışarı aktarmalar desteklenmez.

1. Henüz yönetim grubu oluşturmadıysanız bir grup oluşturun ve bu gruba abonelikler atayın.
1. Maliyet analizinde, kapsamı yönetim grubunuz olarak ayarlayıp **Bu yönetim grubunu seç**’i belirleyin.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Bu yönetim grubunu seç tercihini gösteren örnek" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Yönetim grubundaki aboneliklere yönelik maliyet yönetimi verilerini almak için kapsama uygun bir dışarı aktarma işlemi oluşturun.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Yönetim grubu kapsamında yeni dışarı aktarma oluşturma seçeneğini gösteren örnek":::

## <a name="verify-that-data-is-collected"></a>Verilerin toplandığını doğrulama

Maliyet Yönetimi verilerinizin toplandığını kolaylıkla doğrulayabilir ve Azure Depolama Gezgini’ni kullanarak dışarı aktarılan CSV dosyasını görüntüleyebilirsiniz.

Dışarı aktarma listesinde depolama hesabı adını seçin. Depolama hesabı sayfasında Gezgin'de Aç'ı seçin. Bir onay kutusu görürseniz, dosyayı Azure Depolama Gezgini’nde açmak için **Evet**’i seçin.

![Örnek bilgilerini ve Gezgin'de Aç bağlantısını gösteren depolama hesabı sayfası](./media/tutorial-export-acm-data/storage-account-page.png)

Depolama Gezgini'nde, açmak istediğiniz kapsayıcıya gidin ve bulunduğunuz aya karşılık gelen klasörü seçin. CSV dosyaları listesi gösterilir. Birini belirleyin ve **Aç**’ı seçin.

![Depolama Gezgini'nde gösterilen örnek bilgiler](./media/tutorial-export-acm-data/storage-explorer.png)

Dosya, CSV dosyası uzantılarını açmak üzere ayarlanmış program veya uygulama ile açılır. Bir örneği Excel’de verilmiştir.

![Excel'de gösterilen dışarı aktarılmış örnek CSV verileri](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Dışarı aktarılmış bir CSV veri dosyasını indirme

Dışarı aktarılmış CSV dosyasını Azure portalından da indirebilirsiniz. Aşağıdaki adımlarda, maliyet analizinden bu dosyayı nasıl bulacağınız açıklanmıştır.

1. Maliyet analizinde **Ayarlar**’ı ve sonra **Dışarı Aktarmalar**’ı seçin.
1. Dışarı aktarmalar listesinde, dışarı aktarma işlemine ait depolama hesabını seçin.
1. Depolama hesabınızda **Kapsayıcılar**’ı seçin.
1. Kapsayıcılar listesinden kapsayıcıyı seçin.
1. Dizinlerde ve depolama bloblarında istediğiniz tarihe doğru gidin.
1. CSV dosyasını seçip **İndir** seçeneğini belirleyin.

[![Örnek dışarı aktarma indirmesi](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Dışarı aktarma çalıştırma geçmişini görüntüleme

Dışarı aktarmalar listesi sayfasındaki tek bir dışarı aktarmayı seçerek, zamanlanmış dışarı aktarmanızın çalıştırma geçmişini görüntüleyebilirsiniz. Dışarı aktarmalar listesi sayfası, önceki dışarı aktarmalarınızın çalıştırma zamanını ve bir dışarı aktarmanın çalıştırılacağı sonraki zamanı görüntülemenize yönelik hızlı erişim olanağı da verir. Çalıştırma geçmişini gösteren bir örnek aşağıda verilmiştir.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Ekran görüntüsü Dışarı Aktarmalar bölmesini gösterir.":::

Çalıştırma geçmişini görüntülemek için bir dışarı aktarma seçin.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Ekran görüntüsü bir dışarı aktarmanın çalıştırma geçmişini gösterir.":::

## <a name="access-exported-data-from-other-systems"></a>Dışarı aktarılan verilere diğer sistemlerden erişme

Maliyet Yönetimi verilerinizi dışarı aktarma amaçlarından biri, verilere dış sistemlerden erişmektir. Bir pano sistemi veya diğer finansal sistemi kullanabilirsiniz. Bu sistemler büyük ölçüde farklılık gösterdiğinden bir örnek vermek uygun olmaz.  Bununla birlikte, [Azure Depolama'ya giriş](../../storage/common/storage-introduction.md) bölümünde uygulamalarınızdan verilerinize erişmeye başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Günlük bir dışarı aktarma oluşturma
> * Verilerin toplandığını doğrulama

Boşta olan ve az kullanılan kaynakları belirleyerek verimliliği iyileştirmek ve geliştirmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Önerileri gözden geçirme ve gerekli eylemleri gerçekleştirme](tutorial-acm-opt-recommendations.md)
