---
title: Azure Izleyici çalışma kitapları-bölgeleri taşıma
description: Çalışma kitabını farklı bir bölgeye taşıma
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622843"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Bir Azure çalışma kitabını başka bir bölgeye taşıma

Bu makalede, Azure çalışma kitabı kaynaklarını farklı bir Azure bölgesine nasıl taşıyacağınız açıklanır. Birkaç nedenden dolayı kaynaklarınızı başka bir bölgeye taşıyabilirsiniz. Örneğin, yeni bir Azure bölgesinden yararlanmak için, yalnızca belirli bölgelerde bulunan özellikleri veya hizmetleri dağıtmak, iç ilke ve idare gereksinimlerini karşılamak veya kapasite planlama gereksinimlerine yanıt vermek için.

## <a name="prerequisites"></a>Önkoşullar

* Çalışma kitaplarının hedef bölgede desteklendiğinden emin olun.

* Bu yönergeler, `microsoft.insights/workbooks` `microsoft.insights/myworkbooks` Azure izleyici 'ye ve çoğu kaynak türüne kaydedilen paylaşılan çalışma kitapları () ve özel çalışma kitapları () için geçerlidir.

  Ancak, Application Insights kaynak türüne özel olarak bağlanan çalışma kitapları için, bu çalışma kitapları Application Insights kaynağının kaydedildiği Azure bölgesinde depolanır.

  Bu çalışma kitapları farklı bir bölgeye tek tek taşınamaz.

## <a name="move"></a>Taşı

Bir Azure çalışma kitabını taşımanın en kolay yolu, Portal Kullanıcı arabirimindeki çalışma kitapları aracında "farklı kaydet" i kullanmaktır:

1. Hedef çalışma kitabını çalışma kitabı görüntüleyicisinde açın.
2. Düzenleme modunu girmek için "Düzenle" araç çubuğu düğmesini kullanın.
3. "Farklı kaydet" araç çubuğu düğmesini kullanın.
4. Kaydet formunda, çalışma kitabı için bir ad ve istenen bölge seçin. Abonelik, kaynak grubu ve paylaşım için diğer alanların uygun olduğundan emin olun.

   > [!NOTE]
   > Yinelenen adlardan kaçınmak için çalışma kitabı için yeni bir ad kullanmanız gerekebilir.

5. Kaydedin. 

## <a name="verify"></a>Doğrulama

Yeni çalışma kitabını bulmak için Azure çalışma kitapları Kullanıcı arabirimine gözatıp kullanın. Konumun hedef konum olduğundan emin olun.

## <a name="clean-up"></a>Temizleme

Çalışma kitabınız yeni bölgede oluşturulduktan sonra, önceki bölgedeki özgün çalışma kitabını silin.
1. Çalışma kitabı görüntüleyicisinde özgün çalışma kitabını açın.
2. Düzenleme modunu girmek için "Düzenle" araç çubuğu düğmesini kullanın.
3. Araçlar açılan menüsünden (kurşun kalem simgesi) "çalışma kitabını Sil" i seçin.

Çalışma kitabınızı yeni bir bölgeye aktarmak üzere yeniden adlandırdıysanız, özgün çalışma kitabından sonra düzenleme araçları araç çubuğu "yeniden adlandır" öğesini kullanarak çalışma kitabını önceki adla yeniden adlandırabilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar

Çalışma kitabı yerine çalışma kitabı şablonunu taşımanız mı gerekiyor? Bkz. [Azure çalışma kitabı şablonunu başka bir bölgeye taşıma](./workbook-templates-move-region.md).

ARM şablonları aracılığıyla [çalışma kitaplarını ve çalışma kitabı şablonlarını dağıtma](../visualize/workbooks-automate.md) bölümüne bakın.
