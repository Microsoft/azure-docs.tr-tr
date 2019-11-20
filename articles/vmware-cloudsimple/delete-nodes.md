---
title: CloudSimple-Azure tarafından VMware çözümü için düğümleri silme
description: CloudSimple dağıtımıyla VMWare 'nizden düğümleri silmeyi öğrenin
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972828"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>CloudSimple tarafından Azure VMware çözümünden düğümleri silme

CloudSimple düğümleri oluşturulduktan sonra ölçümlerdir.  Düğümlerin ölçümünü durdurmak için düğümlerin silinmesi gerekir.  Azure portal kullanılmayan düğümleri silersiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Düğüm, yalnızca aşağıdaki koşullarda silinebilir:

* Düğümlerle oluşturulan özel bir bulut silinir.  Özel bir bulutu silmek için bkz. [CloudSimple özel bulutu tarafından Azure VMware çözümünü silme](delete-private-cloud.md).
* Özel bulutu daraltarak düğüm özel buluttan kaldırılmıştır.  Özel bulutu daraltmak için bkz. [CloudSimple özel bulutu tarafından Azure VMware çözümünü küçültme](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="delete-cloudsimple-node"></a>CloudSimple düğümünü Sil

1. **Tüm Hizmetler**’i seçin.

2. **Cloudsimple düğümleri**için arama yapın.

   ![CloudSimple düğümlerinde arama yapın](media/create-cloudsimple-node-search.png)

3. **Cloudsimple düğümlerini**seçin.

4. Silinecek özel buluta ait olmayan düğümleri seçin.  **Özel bulut adı** sütunu, bir düğümün ait olduğu özel bulut adını gösterir.  Bir düğüm özel bir bulut tarafından kullanılmıyorsa, değer boş olur. 

    ![CloudSimple düğümlerini seçin](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Yalnızca özel bulutun bir parçası olmayan düğümler silinebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bulut](cloudsimple-private-cloud.md) hakkında bilgi edinin
