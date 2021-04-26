---
title: Azure Kaynak kullanımını sınırlara karşı denetleyin | Microsoft Docs
description: Azure abonelik limitleriyle Azure kaynak kullanımınızı nasıl denetleyeceğinizi öğrenin.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: d629e65106145a4af364cd9dd489250c8910c25d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778580"
---
# <a name="check-resource-usage-against-limits"></a>Sınırlara göre kaynak kullanımını denetleme

Bu makalede, aboneliğinizde dağıttığınız her bir ağ kaynağı türünün sayısını ve [abonelik limitlerinizi](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) nasıl görebileceğinizi öğreneceksiniz. Sınırlara karşı kaynak kullanımını görüntüleyebilme özelliği, geçerli kullanımı izlemek ve ileride kullanılmak üzere plan yapmak için yararlıdır. Kullanımı izlemek için [Azure Portal](#azure-portal), [POWERSHELL](#powershell)veya [Azure CLI](#azure-cli) kullanabilirsiniz.

## <a name="azure-portal"></a>Azure Portalı

1. Azure [portalında](https://portal.azure.com)oturum açın.
2. Azure portal sol üst köşesinde, **tüm hizmetler**' i seçin.
3. **Filtre** kutusuna *abonelikleri* girin. Arama sonuçlarında **Abonelik** ifadesi göründüğünde bunu seçin.
4. Kullanım bilgilerini görüntülemek istediğiniz aboneliğin adını seçin.
5. **Ayarlar** altında **kullanım + kota**' i seçin.
6. Şu seçenekleri seçebilirsiniz:
   - **Kaynak türleri**: tüm kaynak türlerini seçebilir ya da görüntülemek istediğiniz belirli kaynak türlerini seçebilirsiniz.
   - **Sağlayıcılar**: tüm kaynak sağlayıcılarını seçebilir veya **işlem**, **ağ** veya **depolama**' yı seçebilirsiniz.
   - **Konumlar**: tüm Azure konumlarını seçebilir veya belirli konumları seçebilirsiniz.
   - Tüm kaynakları veya yalnızca en az bir adet dağıtıldığı kaynakları göstermeyi seçebilirsiniz.

     Aşağıdaki resimdeki örnekte, Doğu ABD dağıtılan en az bir kaynağa sahip tüm ağ kaynakları gösterilmektedir:

       ![Kullanım verilerini görüntüleme](./media/check-usage-against-limits/view-usage.png)

     Sütun başlığını seçerek sütunları sıralayabilirsiniz. Gösterilen sınırlar, aboneliğinizin sınırlarıdır. Varsayılan sınırı artırmanız gerekiyorsa, **Isteği artır**' ı seçin, ardından Tamam ve Destek isteğini Gönder ' i seçin. Tüm kaynakların Azure [sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)'nda listelenen bir üst sınırı vardır. Geçerli sınırınıza zaten en fazla sayı varsa, sınır arttırılamıyor.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, sürüm 1.0.0 veya sonraki bir sürümü gerekir. `Get-Module -ListAvailable Az`Yüklü sürümü bulmak için bilgisayarınızda çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Login-AzAccount` Azure 'da oturum açmak için de çalıştırmanız gerekir.

[Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage)ile kullanım sınırlarına göre kullanımınızı görüntüleyin. Aşağıdaki örnek, Doğu ABD konumunda en az bir kaynağın dağıtıldığı kaynakların kullanımını alır:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Aşağıdaki örnek çıktıda aynısını biçimlendirdiğiniz çıktıyı alırsınız:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu makale, Azure CLı sürüm 2.0.32 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız Azure 'da oturum açmak için de çalıştırmanız gerekir `az login` .

[Az Network List-Usage](/cli/azure/network#az_network_list_usages)komutuyla kullanımınızı sınırlara göre görüntüleyin. Aşağıdaki örnek Doğu ABD konumdaki kaynakların kullanımını alır:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Aşağıdaki örnek çıktıda aynısını biçimlendirdiğiniz çıktıyı alırsınız:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```