---
title: Visual Studio 'nun şablon dağıtım betiğini az PowerShell kullanacak şekilde Güncelleştir
description: Azurerd 'den az PowerShell 'e Visual Studio şablon dağıtım betiğini güncelleştirme
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149055"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Visual Studio şablon dağıtım betiği az PowerShell kullanmak üzere güncelleştiriliyor

Visual Studio 16,4, şablon dağıtım betiğinde az PowerShell kullanılmasını destekler. Visual Studio az PowerShell yüklemez veya kaynak grubu projenizde dağıtım betiğini otomatik olarak güncelleştirmez. Daha yeni az PowerShell kullanmak için şu dört şeyi yapmanız gerekir:
1. Azurerd PowerShell 'i kaldır
1. Yüklemeyi az PowerShell
1. Visual Studio 16,4 'ye güncelleştirme
1. Projenizdeki dağıtım betiğini güncelleştirin.

## <a name="uninstall-azurerm-powershell"></a>Azurerd PowerShell 'i kaldır
Azurerd PowerShell 'i kaldırmak için bu [yönergeleri](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) izleyin.

## <a name="install-az-powershell"></a>Yüklemeyi az PowerShell
Az PowerShell 'i yüklemek için bu [yönergeleri](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) izleyin.

## <a name="update-visual-studio-to-164"></a>Visual Studio 'Yu 16,4 olarak güncelleştirme
Kullanılabilir olduğunda Visual Studio 16,4 ' e güncelleştirin. Yükseltme sırasında, Azure PowerShell bileşeninin işaretli olmadığından emin olun. Galeri aracılığıyla az PowerShell 'i yükletiğinden, PowerShell 'in Visual Studio ile yüklenen Azurerd sürümünü istemezsiniz.

Zaten 16,4 sürümüne yükselttiyseniz ve Azure PowerShell bileşeni işaretliyse, Azure Iş yükünde veya tek tek bileşenler sayfasında Azure PowerShell bileşenini Visual Studio Yükleyicisi çalıştırarak veya denetimini kaldırarak kaldırabilirsiniz.

## <a name="update-the-deployment-script-in-your-project"></a>Projenizdeki dağıtım betiğini güncelleştirme
' Azurerd ' dizesinin tüm oluşumlarını dağıtım betiğinizdeki ' az ' ile değiştirin. Değişiklikleri görmek için bu [GİST](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) 'teki düzeltmelere başvurabilirsiniz. Betikleri az PowerShell 'e yükseltme hakkında daha fazla bilgi için bu [belgeye](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) başvurun.


