---
title: Azure Analysis Services farklı bir bölgeye taşıma | Microsoft Docs
description: Bir Azure Analysis Services kaynağının farklı bir bölgeye nasıl taşınacağını açıklar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786360"
---
# <a name="move-analysis-services-to-a-different-region"></a>Analysis Services farklı bir bölgeye taşıma

Bu makalede, bir Analysis Services sunucusu kaynağını farklı bir Azure bölgesine nasıl taşıyacağınız açıklanır. Örneğin, kullanıcılara daha yakın bir Azure bölgesinden yararlanmak, yalnızca belirli bölgelerde desteklenen hizmet planlarını kullanmak veya iç ilke ve idare gereksinimlerini karşılamak üzere bir dizi nedenden dolayı sunucunuzu başka bir bölgeye taşıyabilirsiniz. 

Bu ve ilişkili bağlantılı makalelerde, şunları nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> 
> * [BLOB depolamaya](../storage/blobs/storage-blobs-introduction.md)kaynak sunucu modeli veritabanını yedekleyin.
> * Kaynak sunucu [kaynak şablonunu](../azure-resource-manager/templates/overview.md)dışarı aktarın.
> * Depolama [paylaşılan erişim imzası (SAS)](../storage/common/storage-sas-overview.md)alın.
> * Kaynak şablonunu değiştirin.
> * Yeni bir hedef sunucu oluşturmak için şablonu dağıtın.
> * Bir model veritabanını yeni hedef sunucuya geri yükleyin.
> * Yeni hedef sunucuyu ve veritabanını doğrulayın.
> * Kaynak sunucuyu silin.

Bu makalede, **temel bir yapılandırmaya** sahip tek bir Analysis Services sunucusunu aynı abonelikte farklı bir bölgeye *ve* kaynak grubuna geçirmek için bir kaynak şablonu kullanılması açıklanmaktadır. Bir şablon kullanmak, hedef sunucunun kaynak sunucu olarak bölge ve kaynak grubu dışında aynı özelliklerle yapılandırıldığından, yapılandırılmış sunucu özelliklerini korur. Bu makale, veri kaynağı, depolama ve ağ geçidi kaynakları gibi aynı kaynak grubunun parçası olabilecek ilişkili kaynakların taşınmasını tanımlamaz. 

Bir sunucuyu farklı bir bölgeye taşımadan önce ayrıntılı bir plan oluşturmanız önerilir. Ağ geçitleri ve depolama gibi taşınması gerekebilecek ek kaynakları göz önünde bulundurun. Herhangi bir plan ile, bir üretim sunucusunu taşımadan önce test sunucuları kullanarak bir veya daha fazla deneme taşıma işlemini tamamlaması önemlidir.

> [!IMPORTANT]
> İstemci uygulamaları ve bağlantı dizeleri, sunucunun bulunduğu bölgeyi içeren bir URI olan tam sunucu adını kullanarak Analysis Services bağlanır. Örneğin, `asazure://westcentralus.asazure.windows.net/advworks01`. Bir sunucuyu farklı bir bölgeye taşırken, sunucu adı URI 'sinde farklı bir bölgeye sahip olacak şekilde farklı bir bölgede yeni bir sunucu kaynağı oluşturursunuz. Betiklerdeki kullanılan istemci uygulamaları ve bağlantı dizeleri yeni sunucu adı URI 'sini kullanarak yeni sunucuya bağlanmalıdır. [Sunucu adı diğer](analysis-services-server-alias.md) adının kullanılması sunucu adı URI 'sinin değiştirilmesi gereken yer sayısını azaltır, ancak bölge taşıma işleminden önce uygulanmalıdır.

> [!IMPORTANT]
> Azure bölgeleri farklı IP adresi aralıkları kullanır. Sunucunuzun ve/veya depolama hesabınızın bulunduğu bölge için yapılandırılmış güvenlik duvarı özel durumlarınız varsa, farklı bir IP adresi aralığı yapılandırmak gerekebilir. Daha fazla bilgi edinmek için bkz. [ağ bağlantısı Analysis Services hakkında sık sorulan sorular](analysis-services-network-faq.md).

> [!NOTE]
> Bu makalede, bir veritabanı yedeklemesinin kaynak sunucunun bölgesindeki bir depolama kapsayıcısından bir hedef sunucuya geri yüklenmesi açıklanmaktadır. Bazı durumlarda, farklı bir bölgeden yedeklemelerin geri yüklenmesi, özellikle büyük veritabanları için düşük performansa sahip olabilir. Veritabanı geri yükleme sırasında en iyi performansı elde etmek için hedef sunucu bölgesinde yeni bir depolama kapsayıcısı geçirin veya oluşturun. Veritabanını hedef sunucuya geri yüklemeden önce. abf yedekleme dosyalarını kaynak bölgesi depolama kapsayıcısından hedef bölge depolama kapsayıcısına kopyalayın. Bu makalede kapsam dışında, bazı durumlarda özellikle çok büyük veritabanlarında, kaynak sunucunuzdaki bir veritabanını komut dosyası haline getirmek, yeniden oluşturmak ve ardından hedef sunucuda veritabanı verilerini yüklemek için işlemek, yedekleme/geri yükleme kullanmaktan daha uygun maliyetli olabilir.

> [!NOTE]
> Veri kaynaklarına bağlanmak için bir şirket içi veri ağ geçidi kullanıyorsanız, ağ geçidi kaynağını da hedef sunucu bölgesine taşımanız gerekir. Daha fazla bilgi edinmek için bkz. Şirket [içi veri ağ geçidini yükleyip yapılandırma](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Önkoşullar

- **Azure depolama hesabı**: bir. abf yedekleme dosyasını depolamak için gereklidir.
- **SQL Server Management Studio (SSMS)**: model veritabanlarını yedeklemek ve geri yüklemek için gereklidir.
- **Azure PowerShell**. Yalnızca PowerShell kullanarak bu görevi tamamlamayı tercih ederseniz gereklidir.

## <a name="prepare"></a>Hazırlama

### <a name="backup-model-databases"></a>Yedekleme modeli veritabanları

Kaynak sunucu için **depolama ayarları** zaten yapılandırılmamışsa, [depolama ayarlarını yapılandırma](analysis-services-backup.md#configure-storage-settings)bölümündeki adımları izleyin.

Depolama ayarları yapılandırıldığında, depolama kapsayıcıda bir model veritabanı. abf yedeklemesi oluşturmak için [yedekleme](analysis-services-backup.md#backup) bölümündeki adımları izleyin. Daha sonra. abf yedeklemesini yeni hedef sunucunuza geri yüklemeniz gerekir.


### <a name="export-template"></a>Şablonu dışarı aktarma

Şablon, kaynak sunucunun yapılandırma özelliklerini içerir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Şablonu Azure portalını kullanarak dışarı aktarmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Tüm kaynaklar**' ı seçin ve ardından Analysis Services sunucunuzu seçin.

3. > **ayarları**  >  **dışarı aktarma şablonu**' nu seçin.

4. **Şablonu dışarı aktar** dikey penceresinde **İndir** ' i seçin.

5. Portaldan indirdiğiniz. zip dosyasını bulun ve ardından bu dosyayı bir klasöre ayıklayın.

   ZIP dosyası, yeni bir sunucu dağıtmak için gereken şablonu ve parametreleri oluşturan. JSON dosyalarını içerir.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bir şablonu PowerShell kullanarak dışarı aktarmak için:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi taşımak istediğiniz sunucu kaynağının aboneliğine ayarlayın.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Kaynak sunucunuzun şablonunu dışarı aktarın. Bu komutlar, ResourceGroupName olarak bir JSON şablonunu geçerli dizininize dosya adı olarak kaydeder.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Depolama paylaşılan erişim imzasını (SAS) Al

Bir şablondan hedef sunucu dağıtıldığında, veritabanı yedeklemesini içeren depolama kapsayıcısını belirtmek için bir Kullanıcı temsili SAS belirteci (URI olarak) gereklidir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Portalı kullanarak paylaşılan erişim imzası almak için:

1. Portalda, sunucu veritabanınızı yedeklemek için kullanılan depolama hesabını seçin.

2. **Depolama Gezgini**' yi seçin ve ardından **BLOB kapsayıcıları**' nı genişletin. 

3. Depolama kapsayıcınızı sağ tıklatın ve ardından **paylaşılan erişim Imzasını al**' ı seçin.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS alma":::

4. **Paylaşılan erişim imzası**' nda **Oluştur**' u seçin. Varsayılan olarak, SAS süresi 24 saat içinde dolacak.

5. **URI**'yi kopyalayın ve kaydedin. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak paylaşılan erişim imzası almak için, [PowerShell ile bir kapsayıcı veya blob için Kullanıcı temsilcileri oluşturma SAS oluşturma](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)bölümündeki adımları izleyin.

---

### <a name="modify-the-template"></a>Şablonu değiştirme

Verdiğiniz dosyada template.jsdeğiştirmek için bir metin düzenleyicisi kullanın, bölge ve BLOB kapsayıcı özelliklerini değiştirin. 

Şablonu değiştirmek için:

1. Bir metin düzenleyicisinde, **Location** özelliğinde yeni hedef bölgeyi belirtin. **Backupblobcontaineruri** özelliğinde, depolama kapsayıcısı URI 'sini SAS anahtarıyla yapıştırın. 

    Aşağıdaki örnek, Server AdvWorks1 için hedef bölgeyi olarak ayarlar `South Central US` ve paylaşılan erişim imzası ile depolama kapsayıcısı URI 'sini belirtir: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Şablonu kaydedin.

#### <a name="regions"></a>Bölgeler

Azure bölgelerini almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/). PowerShell kullanarak bölgeleri almak için [Get-AzLocation](/powershell/module/az.resources/get-azlocation) komutunu çalıştırın.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Taşı

Farklı bir bölgeye yeni bir sunucu kaynağı dağıtmak için, önceki bölümlerde verdiğiniz ve değiştirdiğiniz dosyada **template.js** kullanacaksınız.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Portalda **kaynak oluştur**' u seçin.

2. **Market 'Te ara**' te, **şablon dağıtımı** yazın ve ardından **ENTER** tuşuna basın.

3. **Şablon dağıtımı** seçin.

4. **Oluştur**’u seçin.

5. **Düzenleyicide kendi şablonunuzu oluşturun öğesini** seçin.

6. **Dosya Yükle**' yi seçin ve ardından, verdiğiniz ve değiştirdiğiniz dosyaya **template.js** yüklemek için yönergeleri izleyin.

7. Şablon düzenleyicisinin yeni hedef sunucunuz için doğru özellikleri gösterdiğini doğrulayın.

8. **Kaydet**’i seçin.

9. Özellik değerlerini girin veya seçin:

    - **Abonelik**: Azure aboneliğini seçin.
    
    - **Kaynak grubu**: **Yeni oluştur**' u seçin ve ardından bir kaynak grubu adı girin. Zaten aynı ada sahip bir Analysis Services sunucusu bulundurmadığından, mevcut bir kaynak grubunu seçebilirsiniz.
    
    - **Konum**: şablonda belirttiğiniz bölgeyi seçin.

10. **Gözden geçir ve Oluştur '** u seçin.

11. Hüküm ve temel bilgileri gözden geçirin ve ardından **Oluştur**' u seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Şablonunuzu dağıtmak için şu komutları kullanın:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Hedef sunucu URI 'Sini al

SSMS 'deki yeni hedef sunucuya, model veritabanını geri yüklemek için bağlanmak üzere yeni hedef sunucu URI 'sini almanız gerekir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Portalda sunucu URI 'sini almak için:

1. Portalda yeni hedef sunucu kaynağına gidin.

2. **Genel bakış** sayfasında **sunucu adı** URI 'sini kopyalayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak sunucu URI 'sini almak için aşağıdaki komutları kullanın:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Sunucudan **Serverfullname** 'yi kopyalayın.

---

### <a name="restore-model-database"></a>Model veritabanını geri yükle

Model veritabanı. abf yedeklemesini yeni hedef sunucuya geri yüklemek için [geri yükleme](analysis-services-backup.md#restore) bölümünde açıklanan adımları izleyin.

İsteğe bağlı: model veritabanını geri yükledikten sonra, veri kaynaklarından verileri yenilemek için modeli ve tabloları işleyin. SSMS kullanarak modeli ve tabloyu işlemek için:

1. SSMS 'de model veritabanına > **Işlem veritabanı**' na sağ tıklayın.

2. **Tablolar**' ı genişletin, bir tabloya sağ tıklayın. **İşlem** tabloları ' nda tüm tablolar ' ı seçin ve ardından **Tamam**' ı seçin.

## <a name="verify"></a>Doğrulama

1. Portalda yeni hedef sunucuya gidin.

2. Genel Bakış sayfasında, **Analysis Services sunucusundaki modeller**' da, geri yüklenen modellerin göründüğünü doğrulayın.

3. Yeni sunucudaki modele bağlanmak için Power BI veya Excel gibi bir istemci uygulaması kullanın. Tablolar, ölçüler, hiyerarşiler gibi model nesnelerinin göründüğünü doğrulayın. 

4. Tüm otomasyon betiklerini çalıştırın. Bunların başarıyla yürütüldüğünden emin olun.

İsteğe bağlı: [ALM araç seti](http://alm-toolkit.com/) , Power BI veri kümelerini karşılaştırmak ve yönetmek için *açık kaynak* bir araçtır *ve* tablosal model veritabanları Analysis Services. Hem kaynak hem de hedef sunucu veritabanlarına bağlanmak ve karşılaştırmak için araç setini kullanın. Veritabanı geçişiniz başarılı olursa model nesneleri aynı tanıma sahip olur. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Araç Seti":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

İstemci uygulamalarının yeni sunucuya bağlanabilme ve tüm otomasyon betikleri doğru şekilde yürütüldüğü doğrulandıktan sonra, kaynak sunucunuzu silin. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Kaynak sunucuyu portaldan silmek için:

Kaynak sunucunuzun **genel bakış** sayfasında **Sil**' i seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak kaynak sunucuyu silmek için Remove-AzAnalysisServicesServer komutunu kullanın.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Bölge taşımayı tamamladıktan sonra, yeni hedef sunucunuzun kaynak sunucu bölgesindeki depolama kapsayıcısı yerine yedeklemeler için aynı bölgedeki bir depolama kapsayıcısını kullanması önerilir.
