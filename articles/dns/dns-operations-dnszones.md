---
title: Azure DNS-PowerShell 'de DNS bölgelerini yönetme | Microsoft Docs
description: Azure PowerShell 'i kullanarak DNS bölgelerini yönetebilirsiniz. Bu makalede, Azure DNS üzerinde DNS bölgelerini güncelleştirme, silme ve oluşturma işlemlerinin nasıl yapılacağı açıklanmaktadır
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: dd238be6dbfcd14480b2c0cf03236902ef39e722
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959419"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell kullanarak DNS Bölgeleri Yönetme

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure klasik CLı](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLı](dns-operations-dnszones-cli.md)

Bu makalede, Azure PowerShell kullanarak DNS bölgelerinizi nasıl yöneteceğiniz gösterilmektedir. Ayrıca, platformlar arası [Azure CLI](dns-operations-dnszones-cli.md) veya Azure Portal kullanarak DNS bölgelerinizi yönetebilirsiniz.

Bu kılavuz özellikle ortak DNS bölgeleriyle ilgilidir. Azure DNS özel bölgeleri yönetmek için Azure PowerShell kullanma hakkında daha fazla bilgi için bkz. [Azure PowerShell kullanarak Azure DNS özel bölgeleri kullanmaya başlama](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

Bir DNS bölgesi `New-AzureRmDnsZone` cmdlet 'i kullanılarak oluşturulur.

Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *contoso.com* adlı bir DNS bölgesi oluşturur:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Aşağıdaki örnek, *Proje = demo* ve *env = test*olmak üzere ıkı [Azure Resource Manager etiketleriyle](dns-zones-records.md#tags)bir DNS bölgesinin nasıl oluşturulacağını gösterir:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Ayrıca, özel DNS bölgelerini de destekler Azure DNS.  Özel DNS bölgeleri hakkında daha fazla bilgi için bkz. [özel etki alanları için Azure DNS kullanma](private-dns-overview.md). Özel bir DNS bölgesinin nasıl oluşturulacağı hakkında bir örnek için bkz. [PowerShell kullanarak Azure DNS özel bölgelerini kullanmaya başlama](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>DNS bölgesi al

Bir DNS bölgesi almak için `Get-AzureRmDnsZone` cmdlet 'ini kullanın. Bu işlem, Azure DNS var olan bir bölgeye karşılık gelen bir DNS bölgesi nesnesi döndürür. Nesnesi, bölge hakkında (kayıt kümesi sayısı gibi) veriler içerir, ancak kayıt kümelerini içermez (bkz. `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>DNS bölgelerini listeleme

@No__t-0 ' dan bölge adını atlayarak, bir kaynak grubundaki tüm bölgeleri sıralayabilirsiniz. Bu işlem, bölge nesnelerinin bir dizisini döndürür.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Hem bölge adını hem de kaynak grubu adını `Get-AzureRmDnsZone` ' dan atlayarak Azure aboneliğindeki tüm bölgeleri sıralayabilirsiniz.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>DNS bölgesini güncelleştirme

DNS bölgesi kaynağına yapılan değişiklikler `Set-AzureRmDnsZone` kullanılarak yapılabilir. Bu cmdlet bölge içindeki DNS kayıt kümelerinden hiçbirini güncelleştirmez (bkz. [DNS kayıtlarını yönetme](dns-operations-recordsets.md)). Yalnızca bölge kaynağının özelliklerini güncelleştirmek için kullanılır. Yazılabilir bölge özellikleri şu anda [bölge kaynağı için Azure Resource Manager ' Tags '](dns-zones-records.md#tags)ile sınırlıdır.

DNS bölgesini güncelleştirmek için aşağıdaki iki yöntemi kullanın:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Bölge adını ve kaynak grubunu kullanarak bölgeyi belirtin

Bu yaklaşım, var olan bölge etiketlerinin yerine belirtilen değerleri koyar.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone nesnesi kullanarak bölgeyi belirtin

Bu yaklaşım, var olan bölge nesnesini alır, etiketleri değiştirir ve ardından değişiklikleri kaydeder. Bu şekilde, varolan Etiketler korunabilir.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

@No__t-0 ' ı bir $zone nesnesiyle kullanırken, eş zamanlı değişikliklerin üzerine yazılmadığından emin olmak için [ETag denetimleri](dns-zones-records.md#etags) kullanılır. Bu denetimleri bastırmak için isteğe bağlı `-Overwrite` anahtarını kullanabilirsiniz.

## <a name="delete-a-dns-zone"></a>DNS bölgesini silme

DNS bölgeleri `Remove-AzureRmDnsZone` cmdlet 'i kullanılarak silinebilir.

> [!NOTE]
> DNS bölgesini silme, bölge içindeki tüm DNS kayıtlarını da siler. Bu işlem geri alınamaz. DNS bölgesi kullanılıyorsa bölgeyi kullanan hizmetler, bölge silindiğinde başarısız olur.
>
>Yanlışlıkla bölge silmeye karşı korumak için bkz. [DNS bölgelerini ve kayıtlarını koruma](dns-protect-zones-recordsets.md).


DNS bölgesini silmek için aşağıdaki iki yöntemi kullanın:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Bölge adını ve kaynak grubu adını kullanarak bölgeyi belirtin

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone nesnesi kullanarak bölgeyi belirtin

@No__t-1 tarafından döndürülen `$zone` nesnesi kullanılarak silinecek bölgeyi belirtebilirsiniz.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Bölge nesnesi de bir parametre olarak geçirilmesi yerine, bir de olabilir:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

@No__t-0 ' da olduğu gibi, bir `$zone` nesnesi kullanılarak bölge belirtildiğinde, eş zamanlı değişikliklerin silinmediğinden emin olmak için ETag denetimleri etkinleştirilir. Bu denetimleri bastırmak için `-Overwrite` anahtarını kullanın.

## <a name="confirmation-prompts"></a>Onay istemleri

@No__t-0, `Set-AzureRmDnsZone` ve `Remove-AzureRmDnsZone` cmdlet 'leri her türlü onay istemini destekler.

@No__t-0 ve `Set-AzureRmDnsZone` `$ConfirmPreference` PowerShell tercih değişkeni `Medium` veya daha düşük bir değere sahipse onay için istem. Bir DNS bölgesini silmenin olası yüksek etkisi nedeniyle, `$ConfirmPreference` PowerShell değişkeni `None` dışında bir değer içeriyorsa `Remove-AzureRmDnsZone` cmdlet 'i onay ister.

@No__t-0 ' a ait varsayılan değer `High` olduğundan, yalnızca `Remove-AzureRmDnsZone` varsayılan olarak onay ister.

@No__t-1 parametresini kullanarak geçerli `$ConfirmPreference` ayarını geçersiz kılabilirsiniz. @No__t-0 veya `-Confirm:$True` belirtirseniz cmdlet 'i çalıştırmadan önce sizden onay ister. @No__t-0 ' ı belirtirseniz, cmdlet sizden onay istemez.

@No__t-0 ve `$ConfirmPreference` hakkında daha fazla bilgi için bkz. [tercih değişkenleri hakkında](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Sonraki adımlar

DNS bölgesindeki [kayıt kümelerini ve kayıtları yönetmeyi](dns-operations-recordsets.md) öğrenin.
<br>
[Azure DNS için etki alanınızı nasıl atayacağınızı](dns-domain-delegation.md)öğrenin.
<br>
[PowerShell başvuru belgelerini Azure DNS](/powershell/module/azurerm.dns)inceleyin.

