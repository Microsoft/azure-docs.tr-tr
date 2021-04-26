---
title: Hızlı başlangıç-PowerShell Azure Analysis Services kullanarak Azure Analysis Services oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta PowerShell kullanarak bir Azure Analysis Services sunucusu oluşturma açıklanmaktadır
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom: devx-track-azurepowershell - references_regions - devx-track-azurepowershell - mode-api
ms.openlocfilehash: f1902a7192fc2153725e2b1d1e76f23cbd1426f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769382"
---
# <a name="quickstart-create-a-server---powershell"></a>Hızlı başlangıç: Sunucu oluşturma - PowerShell

Bu hızlı başlangıç, Azure aboneliğinizde bir Azure Analysis Services sunucusu oluşturmak için komut satırından PowerShell kullanmayı anlatmaktadır.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**: hesap oluşturmak Için [Azure Ücretsiz deneme sürümünü](https://azure.microsoft.com/offers/ms-azr-0044p/) ziyaret edin.
- **Azure Active Directory**: Aboneliğinizin bir Azure Active Directory Kiracısı ile ilişkilendirilmiş olması ve ilgili dizinde bir hesabınızın olması gerekir. Daha fazla bilgi için bkz. [Kimlik doğrulaması ve izinler](analysis-services-manage-users.md).
- **Azure PowerShell**. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemek veya yükseltmek için bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Al az. AnalysisServices modülü

Aboneliğinizde bir sunucu oluşturmak için [az. AnalysisServices](/powershell/module/az.analysisservices) modülünü kullanın. PowerShell oturumunuza az. AnalysisServices modülünü yükleyin.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu kullanarak Azure aboneliğinizde oturum açın. Ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Azure kaynak grubu](../azure-resource-manager/management/overview.md), Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Sunucunuzu oluşturduğunuzda, aboneliğinizde bir kaynak grubu belirtmeniz gerekir. Henüz bir kaynak grubunuz yoksa, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak yeni bir tane oluşturabilirsiniz. Aşağıdaki örnekte Batı ABD bölgesinde `myResourceGroup` adında bir kaynak grubu oluşturulmaktadır.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Sunucu oluşturma

[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) komutunu kullanarak yeni bir sunucu oluşturun. Aşağıdaki örnek, Batı ABD bölgesinde D1 (ücretsiz) katmanında myResourceGroup’ta myServer adlı bir sunucu oluşturur ve sunucu yöneticisi olarak philipc@adventureworks.com adresini belirtir.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) komutunu kullanarak sunucuyu aboneliğinizden kaldırabilirsiniz. Bu koleksiyondaki diğer hızlı başlangıçları ve öğreticileri kullanacaksanız sunucunuzu kaldırmayın. Aşağıdaki örnekte önceki adımda oluşturduğunuz sunucu kaldırılır.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta PowerShell kullanarak Azure aboneliğinizde sunucu oluşturmayı öğrendiniz. Sunucuyu oluşturduğunuza göre bir sunucu güvenlik duvarı yapılandırarak (isteğe bağlı) güvenliğinin sağlanmasına yardımcı olacaksınız. Sunucunuza doğrudan portaldan örnek bir veri modeli de ekleyebilirsiniz. Örnek modelin olması, model veritabanı rollerini yapılandırma ve istemci bağlantılarını test etme işlemlerini öğrenme konusunda yardımcı olur. Daha fazla bilgi edinmek için, örnek model ekleme öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Sunucu güvenlik duvarını yapılandırma - Portal](analysis-services-qs-firewall.md)      
