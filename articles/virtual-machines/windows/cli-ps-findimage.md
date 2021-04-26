---
title: PowerShell kullanarak Market satın alma planı bilgilerini bulma ve kullanma
description: Market VM görüntüleri için yayımcı, teklif, SKU ve sürüm gibi görüntü URNs ve satın alma planı parametrelerini bulmak için Azure PowerShell kullanın.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 282eaa6e966ff458dfb1dfdd32ef6ec1f2cdc151
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443755"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Azure PowerShell ile Azure Market VM görüntülerini bulma ve kullanma

Bu makalede, Azure Marketi 'nde VM görüntülerini bulmak için Azure PowerShell nasıl kullanılacağı açıklanır. Daha sonra bir sanal makine oluştururken Market görüntüsü ve plan bilgileri belirtebilirsiniz.

Ayrıca, [Azure Marketi](https://azuremarketplace.microsoft.com/) veya [Azure CLI](../linux/cli-ps-findimage.md)kullanarak kullanılabilir görüntülere ve tekliflere de gidebilirsiniz. 

## <a name="terminology"></a>Terminoloji

Azure 'da Market görüntüsü aşağıdaki özniteliklere sahiptir:

* **Yayımcı**: görüntüyü oluşturan kuruluş. Örnekler: Canonical, MicrosoftWindowsServer
* **Teklif**: Yayımcı tarafından oluşturulan ilgili görüntü grubunun adı. Örnekler: UbuntuServer, WindowsServer
* **SKU**: bir dağıtımın ana sürümü gibi bir teklifin örneği. Örnekler: 18,04-LTS, 2019-Datacenter
* **Sürüm**: BIR görüntü SKU 'sunun sürüm numarası. 

Bu değerler ayrı ayrı veya bir görüntü *urn*'si olarak geçirilebilir ve iki nokta üst üste (:) ile ayrılmış değerler birleştiren. Örneğin: *Yayımcı*:*teklif*:*SKU*:*Sürüm*. URN 'deki sürüm numarasını `latest` görüntünün en son sürümünü kullanacak şekilde değiştirebilirsiniz. 

Görüntü yayımcısı ek lisans ve satın alma koşulları sağlıyorsa, görüntüyü kullanabilmeniz için önce bunları kabul etmelisiniz.  Daha fazla bilgi için bkz. [satın alma planı şartlarını kabul etme](#accept-purchase-plan-terms).

## <a name="list-images"></a>Resimleri Listele

Bir görüntü listesini daraltmak için PowerShell kullanabilirsiniz. Değişkenlerin değerlerini gereksinimlerinizi karşılayacak şekilde değiştirin.

1. [Get-Azvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher)kullanarak görüntü yayımcılarını listeleyin.
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Belirli bir yayımcı için [Get-Azvmımageteklifini](/powershell/module/az.compute/get-azvmimageoffer)kullanarak tekliflerini listeleyin.
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Belirli bir yayımcı ve teklif için [Get-Azvmımagesku](/powershell/module/az.compute/get-azvmimagesku)kullanarak kullanılabilir SKU 'ları listeleyin.
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. SKU için [Get-Azvmımage](/powershell/module/az.compute/get-azvmimage)' ı kullanarak görüntünün sürümlerini listeleyin.

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    Ayrıca, `latest` belirli bir eski sürümü değil en son görüntüyü kullanmak isterseniz de kullanabilirsiniz.


Artık seçili yayımcı, teklif, SKU ve sürümü bir URN (:) ile ayrılmış değerler) ile birleştirebilirsiniz. `-Image` [New-azvm](/powershell/module/az.compute/new-azvm) cmdlet 'INI kullanarak bir VM oluşturduğunuzda bu urn parametresini parametresiyle geçirin. `latest`Görüntünün en son sürümünü almak IÇIN urn 'deki sürüm numarasını da değiştirebilirsiniz.

Bir sanal makineyi Kaynak Yöneticisi şablonuyla dağıtırsanız, Özellikler içinde görüntü parametrelerini ayrı olarak ayarlarsınız `imageReference` . Bkz. [şablon başvurusu](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Satın alma planı özelliklerini görüntüle

Azure Marketi 'ndeki bazı sanal makine görüntülerinin, programlama yoluyla dağıtmadan önce kabul etmeniz gereken ek lisans ve satın alma koşulları vardır. Her abonelik için görüntünün koşullarını kabul etmeniz gerekir.

Bir görüntünün satın alma planı bilgilerini görüntülemek için `Get-AzVMImage` cmdlet 'ini çalıştırın. `PurchasePlan`Çıktıda özelliği yoksa `null` , görüntü, programlı dağıtımdan önce kabul etmeniz gereken koşulları içerir.  

Örneğin, *Windows Server 2016 Datacenter* görüntüsünün ek terimleri yoktur, bu nedenle `PurchasePlan` bilgiler şu şekilde olur `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Çıktı aşağıdakine benzer şekilde görünür:

```output
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

Aşağıdaki örnekte, aşağıdaki özelliklere sahip *veri bilimi sanal makinesi-Windows 2016* görüntüsü için benzer bir komut gösterilmektedir `PurchasePlan` : `name` , `product` , ve `publisher` . Bazı görüntülerin de bir `promotion code` özelliği vardır. Bu görüntüyü dağıtmak için, koşulları kabul etmek ve programlı dağıtımı etkinleştirmek için aşağıdaki bölümlere bakın.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Çıktı aşağıdakine benzer şekilde görünür:

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

Lisans koşullarını görüntülemek için [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) cmdlet 'ini kullanın ve satın alma planı parametrelerini geçirin. Çıktı, Market görüntüsüne yönelik koşullara bir bağlantı sağlar ve koşulları önceden kabul edip etmeyeceğinizi gösterir. Parametre değerlerinde tüm küçük harfleri kullandığınızdan emin olun.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Çıktı aşağıdakine benzer şekilde görünür:

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Satın alma planı şartlarını kabul et

Koşulları kabul etmek veya reddetmek için [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) cmdlet 'ini kullanın. Her görüntü için abonelik başına koşulları kabul etmeniz yeterlidir. Parametre değerlerinde tüm küçük harfleri kullandığınızdan emin olun. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
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


## <a name="create-a-new-vm-from-a-marketplace-image"></a>Market görüntüsünden yeni bir VM oluşturma

Kullanmak istediğiniz görüntü hakkında daha fazla bilgi zaten varsa, VM yapılandırmasına görüntü bilgilerini eklemek için bu bilgileri [set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage) cmdlet 'ine geçirebilirsiniz. Market 'te bulunan görüntüleri aramak ve listelemek için bir sonraki bölüme bakın.

Ayrıca, bazı ücretli görüntüler, [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan)kullanarak satın alma planı bilgilerini sağlamanızı gerektirir. 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Daha sonra VM yapılandırmasını diğer yapılandırma nesneleriyle birlikte cmdlet 'e geçireceğiz `New-AzVM` . PowerShell ile VM yapılandırması kullanmayla ilgili ayrıntılı bir örnek için, bu [komut dosyasına](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)bakın.

Görüntü koşullarını kabul etme hakkında bir ileti alırsanız, önceki bölüm [satın alma planı koşullarını kabul](#accept-purchase-plan-terms)edin.

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Satın alma planı bilgileriyle bir VHD 'den yeni bir VM oluşturma

Azure Marketi görüntüsü kullanılarak oluşturulmuş bir VHD varsa, bu VHD 'den yeni bir VM oluşturduğunuzda satın alma planı bilgilerini sağlamanız gerekebilir.

Hala orijinal VM veya aynı görüntüden oluşturulmuş başka bir VM varsa, Get-AzVM kullanarak bu bilgisayardan plan adı, yayımcı ve ürün bilgilerini alabilirsiniz. Bu örnek *Myresourcegroup* kaynak grubundaki *myvm* adlı bir VM 'yi alır ve ardından satın alma planı bilgilerini görüntüler.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Özgün VM silinmeden önce plan bilgilerini almadıysanız, bir [destek isteği](https://ms.portal.azure.com/#create/Microsoft.Support)dosyası gönderebilirsiniz. Bu, VM adı, abonelik KIMLIĞI ve silme işleminin zaman damgasına ihtiyaç duyar.

Bir VHD kullanarak bir VM oluşturmak için, [Özel BIR VHD 'den VM oluşturma](create-vm-specialized.md) ve bir satıra ekleme ' yi, [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) ' i kullanarak VM yapılandırmasına plan bilgilerini eklemek için aşağıdaki makaleye bakın:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>Sonraki adımlar

Temel görüntü bilgilerini kullanarak cmdlet 'le hızlı bir şekilde sanal makine oluşturmak için `New-AzVM` bkz. [PowerShell ile Windows sanal makinesi oluşturma](quick-create-powershell.md).

Paylaşılan görüntü galerisinde özel görüntüler oluşturmak üzere Azure Marketi görüntülerini kullanma hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](../marketplace-images.md).
