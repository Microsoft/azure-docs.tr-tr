---
author: amberz
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.author: amberz
ms.openlocfilehash: bf872feae9c3a7ca94e5252872adee2b653f5524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896172"
---
## <a name="creating-and-running-a-scan"></a>Tarama oluşturma ve çalıştırma

> [!Note] 
> Aşağıda gösterilen adımlar ve ekran görüntüleri, farklı veri kaynağı türlerinde taramaları yönetmeye yönelik genel işlemi gösterir. Seçenekleriniz, çalıştığınız veri kaynağı türlerine bağlı olarak biraz farklı olabilir.

Yeni bir tarama oluşturup çalıştırmak için aşağıdakileri yapın:

1. **Kaynaklara** gidin

1. Kaydettiğiniz veri kaynağını seçin.

1. **+ Yeni tarama** seçin

1. Veri kaynağınıza bağlanacak kimlik bilgisini seçin. 

   :::image type="content" source="media/manage-scans/set-up-scan-data-explorer.png" alt-text="Taramayı ayarla":::

1. Listeden uygun öğeleri denetleyerek, taramanızı klasörler, koleksiyonlar veya şemalar gibi veri kaynağının belirli bölümlerine atayabilirsiniz.

   :::image type="content" source="media/manage-scans/scope-your-scan-data-explorer.png" alt-text="Taramanızı kapsama":::

1. Tarama için ayarlanmış bir tarama kuralı seçin. Sistem varsayılanı, mevcut özel olanlar arasından seçim yapabilir veya yeni bir satır içi oluşturabilirsiniz.

   :::image type="content" source="media/manage-scans/scan-rule-set-data-explorer.png" alt-text="Tarama kuralı kümesi":::

1. Tarama tetikleyiciyi seçin. Bir zamanlama ayarlayabilir veya taramayı bir kez çalıştırabilirsiniz.

   :::image type="content" source="media/manage-scans/trigger-scan-data-explorer.png" alt-text="Tetikleyicinin":::

1. Taramanızı gözden geçirin ve **Kaydet ve Çalıştır '** ı seçin.

## <a name="viewing-your-scans-and-scan-runs"></a>Taramalarınızı ve tarama çalıştırmalarını görüntüleme

Mevcut taramaları görüntülemek için aşağıdakileri yapın:

1. Yönetim merkezine gidin. **Kaynaklar ve tarama** bölümünde **veri kaynakları** ' nı seçin. 

2. İstediğiniz veri kaynağını seçin. Bu veri kaynağındaki mevcut taramaların bir listesini görürsünüz.

3. Sonuçları görüntülemeye ilgilendiğiniz taramayı seçin.

4. Bu sayfada, her bir tarama çalışmasının ölçümlerinin ve durumlarının yanı sıra önceki tüm tarama çalışmalarının hepsi gösterilecektir. Ayrıca, taramalarınızın zamanlandığını veya el ile, kaç varlık sınıflandırdığını, kaç tane varlık olduğunu, taramanın başlangıç ve bitiş zamanını ve toplam tarama süresini de görüntüler.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Taramalarınızı yönetme-düzenleme, silme veya iptal etme

Bir taramayı yönetmek veya silmek için aşağıdakileri yapın:

1. Yönetim merkezine gidin. **Kaynaklar ve tarama** bölümünde **veri kaynakları** ' nı seçin ve ardından istediğiniz veri kaynağını seçin.

2. Yönetmek istediğiniz taramayı seçin. Taramayı **Düzenle** seçeneğini belirleyerek düzenleyebilirsiniz.

3. , **Sil**' i seçerek taramayı silebilirsiniz. 
