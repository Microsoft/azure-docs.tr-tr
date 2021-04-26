---
title: Azure Stack Edge Pro bant genişliği zamanlamalarını yönetme | Microsoft Docs
description: Azure Stack Edge Pro 'unuzda bant genişliği zamanlamalarını yönetmek için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: e73a02c93807072e30c8ce2a1a7feb30e9d3c8c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978977"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'unuzdaki bant genişliği zamanlamalarını yönetmek için Azure portal kullanma  

Bu makalede Azure Stack Edge Pro 'daki kullanıcıların nasıl yönetileceği açıklanır. Bant genişliği zamanlamaları, ağ bant genişliği kullanımını birden çok zamanlamaya göre yapılandırmanızı sağlar. Bu zamanlamalar, cihazınızla bulut arasında gerçekleştirilen yükleme ve indirme işlemlerine uygulanabilir.

Azure Stack Edge Pro için bant genişliği zamanlamalarını Azure portal aracılığıyla ekleyebilir, değiştirebilir veya silebilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Zamanlama ekleme
> * Zamanlamayı değiştirme
> * Zamanlamayı silme


## <a name="add-a-schedule"></a>Zamanlama ekleme

Bir zamanlama eklemek için Azure portal aşağıdaki adımları uygulayın.

1. Azure Stack Edge kaynağınız için Azure portal **bant genişliğine** gidin.
2. Sağ bölmede **+ zamanlama Ekle**' yi seçin.

    ![Bant genişliği seçin](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. **Zamanlama ekle** sayfasında: 

   1. **Başlangıç gününü**, **bitiş gününü**, **başlangıç saatini** ve zamanlamanın **bitiş saatini** belirtin.
   2. Bu zamanlamanın her gün çalışması gerekiyorsa, **tüm gün** seçeneğini işaretleyin.
   3. **Bant genişliği oranı** , bulut ile ilgili işlemlerde (her ikisi de karşıya yükleme ve indirme) kullanılan bit/sn (Mbps) cinsinden bant genişliğidir. Bu alana 20 ile 1.000.000.007 arasında bir sayı girin.
   4. Veri yükleme ve indirme işlemlerini kısıtlamak istemiyorsanız **Sınırsız** bant genişliğini seçin.
   5. **Add (Ekle)** seçeneğini belirleyin.

      ![Zamanlama Ekle](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Belirtilen parametrelerle bir zamanlama oluşturulur. Bu zamanlama daha sonra portaldaki bant genişliği zamanlamaları listesinde görüntülenir.

    ![Bant genişliği zamanlamalarının güncelleştirilmiş listesi](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Zamanlamayı düzenleme

Bir bant genişliği zamanlamasını düzenlemek için aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağına gidin ve ardından **bant genişliğine** gidin. 
2. Bant genişliği zamanlamaları listesinden, değiştirmek istediğiniz bir zamanlamayı seçin ve seçin.
    ![Bant genişliği zamanlamasını seçin](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. İstediğiniz değişiklikleri yapın ve değişiklikleri kaydedin.

    ![Kullanıcıyı değiştirme](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Zamanlama değiştirildikten sonra zamanlama listesi değiştirilen zamanlamayı gösterecek şekilde güncelleştirilir.

    ![Kullanıcı 2 ' i Değiştir](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Zamanlamayı silme

Azure Stack Edge Pro aygıtınızla ilişkili bir bant genişliği zamanlamasını silmek için aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidin ve ardından **bant genişliğine** gidin.  

2. Bant genişliği zamanlaması listesinden silmek istediğiniz zamanlamayı seçin. **Düzenleme zamanlamasında** **Sil**' i seçin. Onay istendiğinde **Evet**' i seçin.

   ![Kullanıcı silme](media/azure-stack-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Zamanlama silindikten sonra zamanlama listesi güncelleştirilir.


## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşımları yönetmeyi](azure-stack-edge-manage-shares.md)öğrenin.
