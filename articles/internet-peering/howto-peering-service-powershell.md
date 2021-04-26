---
title: PowerShell kullanarak doğrudan eşleme üzerinde Azure eşleme hizmeti 'ni etkinleştirme
titleSuffix: Azure
description: PowerShell kullanarak doğrudan eşleme üzerinde Azure eşleme hizmeti 'ni etkinleştirme
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079056"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>PowerShell kullanarak doğrudan eşleme üzerinde Azure eşleme hizmeti 'ni etkinleştirme

Bu makalede, PowerShell cmdlet 'lerini ve Azure Resource Manager dağıtım modelini kullanarak doğrudan eşleme üzerinde Azure [eşleme hizmeti](overview-peering-service.md) 'nin nasıl etkinleştirileceği açıklanır.

İsterseniz, Azure [portalını](howto-peering-service-portal.md)kullanarak bu kılavuzu tamamlayabilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce
* Yapılandırmaya başlamadan önce [önkoşulları](prerequisites.md) gözden geçirin.
* Aboneliğinizde, eşleme hizmetini etkinleştirmek istediğiniz bir doğrudan eşleme seçin. Bir tane yoksa, eski bir doğrudan eşlemeyi dönüştürün veya yeni bir doğrudan eşleme oluşturun:
    * Eski bir doğrudan eşlemeyi dönüştürmek için [PowerShell 'i kullanarak eski bir doğrudan eşlemeyi Azure kaynağına dönüştürme](howto-legacy-direct-powershell.md)bölümündeki yönergeleri izleyin.
    * Yeni bir doğrudan eşleme oluşturmak için [PowerShell kullanarak doğrudan eşleme oluşturma veya değiştirme](howto-direct-powershell.md)bölümündeki yönergeleri izleyin.

### <a name="work-with-azure-powershell"></a>Azure PowerShell çalışın
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Doğrudan eşleme üzerinde Eşleme Hizmeti’ni etkinleştir

### <a name="view-direct-peering"></a><a name= get></a>Doğrudan eşlemeyi görüntüle
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Eşleme hizmeti için doğrudan eşlemeyi etkinleştir

Önceki adımda doğrudan eşleme aldıktan sonra, bunu eşleme hizmeti için etkinleştirin.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Doğrudan eşleme bağlantısını değiştirme

Bağlantı ayarlarını değiştirmeniz gerekiyorsa, [PowerShell kullanarak doğrudan eşleme oluşturma veya değiştirme](howto-direct-powershell.md)konusundaki "doğrudan eşlemeyi değiştirme" bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [PowerShell kullanarak Exchange eşlemesi oluşturma veya değiştirme](howto-exchange-powershell.md)
* [PowerShell kullanarak eski bir Exchange eşlemesini Azure kaynağına dönüştürme](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ek kaynaklar
Aşağıdaki komutu çalıştırarak tüm parametrelerin ayrıntılı açıklamalarını alabilirsiniz:

```powershell
Get-Help Get-AzPeering -detailed
```

Sık sorulan sorular için bkz. [eşleme hizmeti hakkında SSS](service-faqs.md).
