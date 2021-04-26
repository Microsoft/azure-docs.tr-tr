---
title: Azure Izleyici çalışma kitabı şablonları-bölgeleri taşıma
description: Çalışma kitabı şablonunu farklı bir bölgeye taşıma
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 9787118b1912a5697c02e116be99b1f396748937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100621536"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Bir Azure çalışma kitabı şablonunu başka bir bölgeye taşıma

Bu makalede, Azure çalışma kitabı şablon kaynaklarının farklı bir Azure bölgesine nasıl taşınacağı açıklanır. Birkaç nedenden dolayı kaynaklarınızı başka bir bölgeye taşıyabilirsiniz. Örneğin, yeni bir Azure bölgesinden yararlanmak için, yalnızca belirli bölgelerde bulunan özellikleri veya hizmetleri dağıtmak, iç ilke ve idare gereksinimlerini karşılamak veya kapasite planlama gereksinimlerine yanıt vermek için.

Şu anda çalışma kitabı şablonu kaynakları oluşturmak için bir Portal Kullanıcı arabirimi yok, bunları oluşturmanın tek geçerli yolu [Azure Resource Manager şablonu (ARM şablonu) dağıtımlarını](../visualize/workbooks-automate.md)kullanmaktır. Bu nedenle, bir şablonu taşımanın en kolay yolu, önceki ARM şablonunu yeniden kullanmak ve yeni bölgeye dağıtılması için güncelleştirmenizdir.

## <a name="prerequisites"></a>Önkoşullar

* Çalışma kitabı şablonlarının hedef bölgede desteklendiğinden emin olun.

## <a name="prepare"></a>Hazırlama

* Çalışma kitabı şablonu için önceden kullanılmış ARM şablonunu tanımla.

## <a name="move"></a>Taşı

1. Daha önce kullanılan şablonu yeni bölgeye başvuracak şekilde güncelleştirin.

   > [!NOTE]
   > Yinelenen adlardan kaçınmak için çalışma kitabı şablonu için yeni bir ad kullanmanız gerekebilir.

2. İstenen bölgede yeni bir çalışma kitabı şablonu oluşturmak için ARM şablon dağıtımı aracılığıyla güncelleştirilmiş şablonu dağıtın.

## <a name="verify"></a>Doğrulama

Yeni dağıtılan çalışma kitabı şablonunu bulmak için Azure çalışma kitapları Kullanıcı arabirimine gözatıp kullanın. Konumun hedef konum olduğundan emin olun.

## <a name="clean-up"></a>Temizleme

Çalışma kitabı şablonunuz yeni bölgede oluşturulduktan sonra, önceki bölgedeki özgün çalışma kitabı şablonunu silin.
1. Azure çalışma kitapları tarayıcı kullanıcı arabiriminde çalışma kitabı şablonunu bulun.
2. Silinecek çalışma kitabı şablonunu seçin.
3. "Sil" komutunu seçin.

Çalışma kitabı şablonunuzu yeni bir bölgeye aktarmak üzere yeniden adlandırdıysanız, özgün öğe, Azure çalışma kitabı şablonu kaynak görünümündeki "yeniden adlandır" komutu kullanılarak silindikten sonra çalışma kitabı şablonunu önceki adla yeniden adlandırabilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar

Şablon yerine çalışma kitabını taşımak mı gerekiyor? Bkz. [bir Azure çalışma kitabını başka bir bölgeye taşıma](./workbooks-move-region.md).

