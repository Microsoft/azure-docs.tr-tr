---
title: Azure 'da Windows VM görüntülerini seçme
description: Market VM görüntüleri için yayımcı, teklif, SKU ve sürümü öğrenmek için Azure PowerShell kullanın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 2388b51c8103b6bcbae0c32d3c4d78a176caf282
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039530"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Azure Market 'te Azure PowerShell ile Windows VM görüntülerini bulma

Bu makalede, Azure Marketi 'nde VM görüntülerini bulmak için Azure PowerShell nasıl kullanılacağı açıklanır. Daha sonra PowerShell, Kaynak Yöneticisi şablonları veya diğer araçlarla bir VM 'yi programlama yoluyla oluşturduğunuzda bir market görüntüsü belirtebilirsiniz.

Ayrıca, [Azure Marketi](https://azuremarketplace.microsoft.com/) storefront, [Azure Portal](https://portal.azure.com)veya [Azure CLI](../linux/cli-ps-findimage.md)kullanarak kullanılabilir görüntülere ve tekliflere de gidebilirsiniz. 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Yaygın olarak kullanılan Windows görüntülerinin tablosu

Bu tablo, belirtilen Yayımcılar ve teklifler için kullanılabilir SKU 'ların bir alt kümesini gösterir.

| Yayımcı | Sunduğu | Sku |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-veri merkezi |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-veri merkezi-kapsayıcılar |
| MicrosoftWindowsServer |WindowsServer |2016-veri merkezi |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-veri merkezi-kapsayıcılar |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Görüntülerde gezin

Bir konumda görüntü bulmanın bir yolu [Get-Azvmımagepublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-Azvmımageteklifinin](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)ve [Get-Azvmımagesku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) cmdlet 'lerini sırasıyla çalıştırmak olur:

1. Görüntü yayımcılarını listeleyin.
2. Belirli bir yayımcı varsa yayımcının tekliflerini listeleyin.
3. Belirli bir teklif varsa SKU’larını listeleyin.

Daha sonra, seçilen bir SKU için, dağıtılacak sürümleri listelemek için [Get-Azvmımage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) komutunu çalıştırın.

1. Yayımcıları listeleyin:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Seçtiğiniz yayımcının adını girin ve teklifleri listeleyin:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Seçtiğiniz teklif adını girin ve SKU 'Ları listeleyin:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Seçtiğiniz SKU adınızı girin ve görüntü sürümünü alın:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
`Get-AzVMImage` komutunun çıktısından, yeni bir sanal makine dağıtmak için bir sürüm görüntüsü seçebilirsiniz.

Aşağıdaki örnek komutların ve çıktıların tam dizisini gösterir:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Kısmi çıkış:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

*Microsoftwindowsserver* yayımcısı için:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Çıktı:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

*Windowsserver* teklifi için:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Kısmi çıkış:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Ardından, *2019-Datacenter* SKU 'su için:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Artık seçili yayımcı, teklif, SKU ve sürümü bir URN (:) ile ayrılmış değerler) ile birleştirebilirsiniz. [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet 'ini kullanarak bir VM oluşturduğunuzda, bu URN 'yi `--image` parametresiyle geçirin. Görüntünün en son sürümünü almak için, isteğe bağlı olarak URN 'deki sürüm numarasını "en son" ile değiştirebilirsiniz.

Bir sanal makineyi Kaynak Yöneticisi şablonuyla dağıtırsanız, görüntü parametrelerini `imageReference` özelliklerinde ayrı ayrı ayarlarsınız. Bkz. [şablon başvurusu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Plan özelliklerini görüntüle

Bir görüntünün satın alma planı bilgilerini görüntülemek için `Get-AzVMImage` cmdlet 'ini çalıştırın. Çıktıda `PurchasePlan` özelliği `null`değilse, görüntü, programlı dağıtımdan önce kabul etmeniz gereken koşullara sahiptir.  

Örneğin, *Windows Server 2016 Datacenter* görüntüsünün ek terimleri yoktur, bu nedenle `PurchasePlan` bilgileri `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Çıktı:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Aşağıdaki örnekte, aşağıdaki `PurchasePlan` özelliklerine sahip olan *veri bilimi sanal makinesi-Windows 2016* görüntüsü için benzer bir komut gösterilmektedir: `name`, `product`ve `publisher`. Bazı görüntülerin `promotion code` özelliği de vardır. Bu görüntüyü dağıtmak için, koşulları kabul etmek ve programlı dağıtımı etkinleştirmek için aşağıdaki bölümlere bakın.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Çıktı:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Koşulları kabul edin

Lisans koşullarını görüntülemek için [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) cmdlet 'ini kullanın ve satın alma planı parametrelerini geçirin. Çıktı, Market görüntüsüne yönelik koşullara bir bağlantı sağlar ve koşulları önceden kabul edip etmeyeceğinizi gösterir. Parametre değerlerinde tüm küçük harfleri kullandığınızdan emin olun.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Çıktı:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Koşulları kabul etmek veya reddetmek için [set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) cmdlet 'ini kullanın. Her görüntü için abonelik başına koşulları kabul etmeniz yeterlidir. Parametre değerlerinde tüm küçük harfleri kullandığınızdan emin olun. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Çıktı:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Satın alma planı parametrelerini kullanarak dağıtın

Bir görüntü için koşulları kabul ettikten sonra bu abonelikte bir VM dağıtabilirsiniz. Aşağıdaki kod parçacığında gösterildiği gibi, VM nesnesinin Market planı bilgilerini ayarlamak için [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) cmdlet 'ini kullanın. VM için ağ ayarları oluşturmaya ve dağıtımı tamamlamaya yönelik bir komut dosyası için, [PowerShell betiği örneklerine](powershell-samples.md)bakın.

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Daha sonra, sanal makine yapılandırmasını ağ yapılandırma nesneleriyle birlikte `New-AzVM` cmdlet 'ine geçireceğiz.

## <a name="next-steps"></a>Sonraki adımlar

Temel görüntü bilgilerini kullanarak `New-AzVM` cmdlet ile hızlı bir şekilde sanal makine oluşturmak için bkz. [PowerShell Ile Windows sanal makinesi oluşturma](quick-create-powershell.md).


[Tam olarak yapılandırılmış bir sanal makine oluşturmak](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)Için bir PowerShell betiği örneğine bakın.


