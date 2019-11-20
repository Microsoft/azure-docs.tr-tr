---
title: Kişisel veriler
description: Azure Resource Manager işlemleriyle ilişkili kişisel verileri yönetmeyi öğrenin.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 2b1f97fa4f831235fe04c18b1ed33e729ed2326b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149509"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Azure Resource Manager ilişkili kişisel verileri yönetme

Hassas bilgileri açığa çıkarmaktan kaçınmak için dağıtımlar, kaynak grupları veya Etiketler ' de verdiğiniz tüm kişisel bilgileri silin. Azure Resource Manager, dağıtımlar, kaynak grupları veya Etiketler içinde sağlanmış olan kişisel verileri yönetmenize olanak sağlayan işlemler sağlar.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Dağıtım geçmişindeki kişisel verileri silme

Dağıtımlar için Kaynak Yöneticisi, dağıtım geçmişinde parametre değerlerini ve durum iletilerini korur. Bu değerler, siz dağıtımı geçmişten silene kadar kalır. Bu değerlerde kişisel veriler sağladıysanız, dağıtımları listeleyin. Kişisel veriler bulursanız, verileri geçmişten silin.

Geçmişteki **dağıtımları** listelemek için şunu kullanın:

* [Kaynak grubuna göre Listele](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az Group Deployment List](/cli/azure/group/deployment#az-group-deployment-list)

**Dağıtım** geçmişinden silmek için şunu kullanın:

* [Delete](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Kaynak grubu adlarındaki kişisel verileri silme

Kaynak grubunun adı, kaynak grubunu silene kadar devam ettirir. Adlarında kişisel veriler sağlandığını görmek için kaynak gruplarını listeleyin. Kişisel veriler bulursanız, kaynakları yeni bir kaynak grubuna [taşıyın](resource-group-move-resources.md) ve ad içindeki kişisel verilerle kaynak grubunu silin.

**Kaynak gruplarını**listelemek için şunu kullanın:

* [Liste](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az Group List](/cli/azure/group#az-group-list)

**Kaynak gruplarını**silmek için şunu kullanın:

* [Delete](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Etiketlerin içindeki kişisel verileri silme

Etiket adları ve değerleri, etiketi silene veya değiştirene kadar kalır. Etiketlerdeki kişisel verileri sağladıysanız, etiketleri listeleyin. Kişisel verileri bulursanız, etiketleri silin.

**Etiketleri**listelemek için şunu kullanın:

* [Liste](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az Tag List](/cli/azure/tag#az-tag-list)

**Etiketleri**silmek için şunu kullanın:

* [Delete](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az Tag Delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Sonraki adımlar
* Azure Resource Manager genel bir bakış için bkz. [Kaynak Yöneticisi nedir?](resource-group-overview.md)