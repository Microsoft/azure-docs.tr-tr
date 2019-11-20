---
title: Azure DevTest Labs kişisel verileri silme ve dışarı aktarma | Microsoft Docs
description: Genel Veri Koruma Yönetmeliği (GDPR) kapsamında yükümlülüklerinizi desteklemek için Azure DevLast Labs hizmetinden kişisel verileri silme ve dışarı aktarma hakkında bilgi edinin.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 82ab8ef2e444b71f41fbbd87e4e9f8669e83e508
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371177"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Azure DevTest Labs kişisel verileri dışarı aktarma veya silme
Bu makalede, Azure DevTest Labs hizmetinden kişisel verileri silmek ve dışarı aktarmak için adımlar sağlanmaktadır. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs hangi kişisel verileri toplar?
DevTest Labs kullanıcıdan iki ana kişisel veri parçası toplar. Bunlar: Kullanıcı e-posta adresi ve Kullanıcı nesne KIMLIĞI. Bu bilgiler, Service 'in laboratuvar yöneticilerine ve laboratuvar kullanıcılarına hizmet içi Özellikler sağlaması açısından önemlidir.

### <a name="user-email-address"></a>Kullanıcı e-posta adresi
DevTest Labs, laboratuvar kullanıcılarına otomatik kapalı e-posta bildirimleri göndermek için kullanıcı e-posta adresini kullanır. E-posta, kullanıcılarının makine kapatmakta olduğunu bildirir. Kullanıcılar, bunu yapmak istediklerinde kapanmaya erteleyebilir ya da atlayabilir. E-posta adresini laboratuvar düzeyinde veya VM düzeyinde yapılandırırsınız.

**Laboratuvarda e-posta ayarlanıyor:**

![Laboratuvar düzeyinde e-posta ayarlanıyor](./media/personal-data-delete-export/lab-user-email.png)

**VM 'de e-posta ayarlanıyor:**

![VM düzeyinde e-posta ayarlanıyor](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Kullanıcı nesnesi KIMLIĞI
DevTest Labs Kullanıcı nesne KIMLIĞINI kullanarak aylık maliyet eğilimlerini ve kaynak bilgilerini laboratuvar yöneticilerine göre gösterir. Bunların laboratuvarlarına ait maliyetleri izlemesine ve eşiklerini yönetmesine olanak tanır. 

**Geçerli takvim ayı için tahmini maliyet eğilimi:** 
![geçerli takvim ayı için tahmini maliyet eğilimi](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Kaynağa göre tahmini aylık maliyet:** 
![kaynağa göre tahmini aylık maliyet](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Neden bu kişisel verilere ihtiyacımız var?
DevTest Labs hizmeti, çalışma amacıyla kişisel verileri kullanır. Bu veriler, hizmet için önemli özellikler sunmaları açısından önemlidir. Kullanıcı e-posta adresinde bir bekletme ilkesi ayarlarsanız, laboratuvar kullanıcıları e-posta adresleri sistemimizden silindikten sonra zamanında otomatik olarak kapanmadan e-posta bildirimleri almaz. Benzer şekilde, Laboratuvar Yöneticisi, Kullanıcı nesne kimlikleri bir bekletme ilkesine göre siliniyorsa, laboratuvardaki makineler için aya göre aylık maliyet eğilimlerini ve maliyeti görüntüleyemez. Bu nedenle, kullanıcının kaynağı laboratuvarda etkin olduğu sürece bu verilerin korunması gerekir.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Sisteme kişisel verilerimi nasıl unutabilirim?
Laboratuvar kullanıcısı olarak, bu kişisel verilerin silinmesini istiyorsanız laboratuvardaki ilgili kaynağı silerek bunu yapabilirsiniz. DevTest Labs hizmeti, silinen kişisel verileri Kullanıcı tarafından silindikten sonraki 30 gün sonra anonimleştirir.

Örneğin, VM 'nizi siler veya e-posta adresinizi kaldırdıysanız, DevTest Labs hizmeti kaynak silindikten sonraki 30 gün sonra bu verileri anonimleştirir. Silinmeden sonra 30 günlük bekletme ilkesi, laboratuvar yöneticisine doğru bir aylık maliyet projeksiyonu sağlamamız sağlamaktır.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Kişisel verilerim üzerinde nasıl dışarı aktarma isteyebilirim?
Azure portal veya PowerShell 'i kullanarak kişisel ve laboratuvar kullanım verilerini dışarı aktarabilirsiniz. Veriler iki farklı CSV dosyası olarak verilir:

- **diskler. csv** -farklı VM 'ler tarafından kullanılan disklerle ilgili bilgiler içerir
- **virtualmachines. csv** -laboratuvardaki VM 'ler hakkında bilgi içerir.

### <a name="azure-portal"></a>Azure portal
Laboratuvar kullanıcısı olarak, DevTest Labs hizmetinin depoladığı kişisel veriler üzerinde bir dışarı aktarma isteğinde bulunabilir. Bir dışarı aktarma istemek için laboratuvarınızın **genel bakış** sayfasında **kişisel veriler** seçeneğine gidin. Bir **dışarı aktarma isteği** seçin düğmesini laboratuvar yöneticinizin depolama hesabınızda indirilebilir bir Excel dosyası oluşturmayı devre dışı bırakın. Daha sonra bu verileri görüntülemek için laboratuvar yöneticinize başvurabilirsiniz.

1. Sol taraftaki menüden **kişisel veriler** ' i seçin. 

    ![Kişisel veri sayfası](./media/personal-data-delete-export/personal-data-page.png)
2. Laboratuvarı içeren **kaynak grubunu** seçin.

    ![Kaynak grubu seçin](./media/personal-data-delete-export/select-resource-group.png)
3. Kaynak grubundaki **Depolama hesabını** seçin.
4. **Depolama hesabı** sayfasında, Bloblar ' ı seçin.

    ![Bloblar kutucuğunu seçin](./media/personal-data-delete-export/select-blobs-tile.png)
5. Kapsayıcılar listesinde **labresourceusage** adlı kapsayıcıyı seçin.

    ![Blob kapsayıcısı seçin](./media/personal-data-delete-export/select-blob-container.png)
6. Laboratuvarınızın ardından adlı **klasörü** seçin. Laboratuvarınızda **diskler** ve **sanal makineler** için **CSV** dosyalarını bu klasörde bulabilirsiniz. Bu CSV dosyalarını indirebilir, erişim isteyen laboratuvar kullanıcısının içeriğini filtreleyebilir ve bunlarla paylaşabilirsiniz.

    ![CSV dosyasını indir](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Yukarıdaki örnekteki anahtar bileşenleri şunlardır:

- Invoke-AzureRmResourceAction komutu.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- İki eylem parametresi
    - **blobStorageAbsoluteSasUri** -paylaşılan erişim IMZASı (SAS) belirtecine sahip depolama hesabı URI 'si. PowerShell betiğinden, bu değer depolama anahtarı yerine ' de geçirilebilir.
    - **Usagestartdate** -bitiş tarihi, eylemin yürütüldüğü geçerli tarih olan verileri çekmek için başlangıç tarihi. Ayrıntı düzeyi gün düzeyindedir, bu nedenle saat bilgilerini eklemeseniz bile yok sayılır.

### <a name="exported-data---a-closer-look"></a>Aktarılmış veriler-daha yakından bakış
Şimdi, dışarıya aktarılmış verilere daha yakından bakalım. Daha önce belirtildiği gibi, veriler başarıyla verildiğinde, iki CSV dosyası olur. 

**Virtualmachines. csv** aşağıdaki veri sütunlarını içerir:

| Sütun adı | Açıklama |
| ----------- | ----------- | 
| SubscriptionId | Laboratuvarın bulunduğu abonelik tanımlayıcısı. |
| Labuıd | Laboratuvar için benzersiz GUID tanımlayıcısı. |
| LabName | Laboratuvarın adı. |
| Labresourceıd | Tam nitelikli laboratuvar kaynak KIMLIĞI. |
| ResourceGroupName | VM 'yi içeren kaynak grubunun adı | 
| RESOURCEID | Sanal makine için tam kaynak KIMLIĞI. |
| ResourceUId | VM için GUID |
| Ad | Sanal makine adı. |
| CreatedTime | VM 'nin oluşturulduğu tarih-saat. |
| DeletedDate | VM 'nin silindiği tarih-saat. Boşsa, silme işlemi henüz gerçekleşmemiştir. |
| ResourceOwner | VM sahibi. Değer boşsa, tek bir sanal makine olabilir veya bir hizmet sorumlusu tarafından oluşturulur. |
| PricingTier | VM 'nin Fiyatlandırma Katmanı |
| ResourceStatus | VM 'nin kullanılabilirlik durumu. Etkin, hala varsa veya etkin değilse, sanal makine silinmişse. |
| Computeresourceıd | Tam sanal makine işlem kaynağı tanımlayıcısı. |
| Talep edilebilir | VM, çakışan bir VM ise, true olarak ayarlayın | 
| EnvironmentID | Sanal makinenin içinde oluşturulduğu ortam kaynak tanımlayıcısı. VM, bir ortam kaynağının parçası olarak oluşturulmamışsa boştur. |
| ExpirationDate | VM için sona erme tarihi. Sona erme tarihi ayarlanmamışsa boş olarak ayarlanır.
| Gallerımagereferenceversion |  VM temel görüntüsünün sürümü. |
| Gallerimagereferenceteklif | VM temel görüntüsünü sunun. |
| Gallerimagereferenceyayımcısı | VM temel görüntüsünün yayımcısı. |
| Gallerimagereferencesku | VM temel görüntüsünün SKU 'su |
| Gallerimagereferenceostype | VM temel görüntüsünün işletim sistemi türü |
| Customımageıd | VM tabanlı özel görüntünün tam KIMLIĞI. |

**Diskler. csv** dosyasında yer alan veri sütunları aşağıda listelenmiştir:

| Sütun adı | Açıklama | 
| ----------- | ----------- | 
| SubscriptionId | Laboratuvarı içeren aboneliğin KIMLIĞI |
| Labuıd | Laboratuvar için GUID |
| LabName | Laboratuvarın adı | 
| Labresourceıd | Laboratuvar için tam kaynak KIMLIĞI | 
| ResourceGroupName | Laboratuvarın bulunduğu kaynak grubunun adı | 
| RESOURCEID | Sanal makine için tam kaynak KIMLIĞI. |
| ResourceUId | VM için GUID |
 |Ad | Eklenen diskin adı |
| CreatedTime |Veri diskinin oluşturulduğu tarih ve saat. |
| DeletedDate | Veri diskinin silindiği tarih ve saat. |
| ResourceStatus | Kaynağın durumu. Kaynak varsa etkin. Silindiğinde, etkin değil. |
| Diskblobadı | Veri diski için blob adı. |
| DiskSizeGB | Veri diskinin boyutu. |
| DiskType | Veri diskinin türü. Standart için 0, Premium için 1. |
| Leasedbyvmıd | Veri diskinin eklendiği sanal makinenin kaynak KIMLIĞI. |


> [!NOTE]
> Birden çok laboratuvarda çalışıyorsanız ve genel bilgi almak istiyorsanız, iki anahtar sütunu, abonelikler genelinde benzersiz kimlikler olan **Labuıd** ve **resourceuid**olur.

İçe aktarılmış veriler, SQL Server, Power BI vb. gibi araçlar kullanılarak işlenebilir ve görselleştirilebilir. Bu özellik özellikle, laboratuvarınızın kullanımını, yaptığınız şekilde aynı Azure aboneliğini kullanmayan Yönetim ekibinize raporlamak istediğinizde yararlıdır.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın: 

- [Laboratuvar ilkelerini ayarlama](devtest-lab-get-started-with-lab-policies.md)
- [Sık sorulan sorular](devtest-lab-faq.md)
