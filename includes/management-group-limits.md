---
title: include dosyası
description: include dosyası
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800163"
---
| Kaynak | Sınır |
| --- | --- |
| Azure AD kiracısı başına yönetim grupları | 10,000 |
| Yönetim grubu başına abonelikler | Sayısız. |
| Yönetim grubu hiyerarşisi düzeyleri | Kök düzeyi Plus 6 düzey<sup>1</sup> |
| Yönetim grubu başına doğrudan üst yönetim grubu | Bir |
| Konum başına [Yönetim grubu düzeyinde dağıtımlar](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup> 6 düzey abonelik düzeyini içermez.

<sup>2</sup> 800 dağıtım sınırına ulaşırsanız, artık gerekli olmayan geçmişten dağıtımları silin. Yönetim grubu düzeyi dağıtımlarını silmek için [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) veya [az Deployment mg Delete](/cli/azure/deployment/mg#az_deployment_mg_delete)komutunu kullanın.
