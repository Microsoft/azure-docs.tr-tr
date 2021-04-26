---
title: Azure Data Box Gateway bant genişliği zamanlamalarını yönetme | Microsoft Docs
description: Azure portalı kullanarak Azure Data Box Gateway bant genişliği zamanlamalarını yönetme adımları.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 55676128f482c8fb4dbdeab0523e4556f3957af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582558"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Azure Data Box Gateway bant genişliği zamanlamalarını yönetmek için Azure portalı kullanma  

Bu makalede Azure Data Box Gateway kullanıcılarını yönetme adımları açıklanmaktadır. Bant genişliği zamanlamaları, ağ bant genişliği kullanımını birden çok zamanlamaya göre yapılandırmanızı sağlar. Bu zamanlamalar, cihazınızla bulut arasında gerçekleştirilen yükleme ve indirme işlemlerine uygulanabilir.

Data Box Gateway cihazınız için bant genişliği zamanlaması ekleme, değiştirme veya silme işlemlerini Azure portaldan gerçekleştirebilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Zamanlama ekleme
> * Zamanlamayı değiştirme
> * Zamanlamayı silme

## <a name="add-a-schedule"></a>Zamanlama ekleme

Kullanıcı eklemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Data Box Gateway kaynağınızın Azure portal sayfasında **Bant genişliği** bölümüne gidin.
2. Sağdaki bölmede **+ Zamanlama ekle**'ye tıklayın.

    ![Kullanıcı ekle'ye tıklayın](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. **Zamanlama ekle** sayfasında: 

   1. **Başlangıç gününü**, **bitiş gününü**, **başlangıç saatini** ve zamanlamanın **bitiş saatini** belirtin. 
   2. Zamanlama gün boyu çalışıyorsa **Tüm gün** seçeneğini işaretleyebilirsiniz. 
   3. **Bant genişliği oranı** , bulut ile ilgili işlemlerde (her ikisi de karşıya yükleme ve indirme) kullanılan bit/sn (Mbps) cinsinden bant genişliğidir. Bu alana 1 ile 1000 arasında bir sayı girin. 
   4. Veri yükleme ve indirme işlemlerini kısıtlamak istemiyorsanız **Sınırsız** bant genişliğini seçin. 
   5. **Ekle**'ye tıklayın.

      ![Kullanıcı Ekle 2 ' ye tıklayın](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Belirtilen parametrelerle bir zamanlama oluşturulur. Bu zamanlama daha sonra portaldaki bant genişliği zamanlamaları listesinde görüntülenir.


## <a name="edit-schedule"></a>Zamanlamayı düzenleme

Bir bant genişliği zamanlamasını düzenlemek için aşağıdaki adımları gerçekleştirin. 

1. Data Box Gateway kaynağınızın Azure portal sayfasında Bant genişliği bölümüne gidin. 
2. Bant genişliği zamanlaması listesinden değiştirmek istediğiniz zamanlamayı seçin ve tıklayın.

    ![Kullanıcıyı değiştirme](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. İstediğiniz değişiklikleri yapın ve değişiklikleri kaydedin.

    ![Kullanıcı 2 ' i Değiştir](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Zamanlama değiştirildikten sonra zamanlama listesi değiştirilen zamanlamayı gösterecek şekilde güncelleştirilir.

    ![Kullanıcı 3 ' ü değiştirme](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Zamanlamayı silme

Data Box Gateway cihazınızla ilişkilendirilmiş bant genişliği zamanlamasını silmek için aşağıdaki adımları gerçekleştirin.

1. Data Box Gateway kaynağınızın Azure portal sayfasında **Bant genişliği** bölümüne gidin.  

2. Bant genişliği zamanlaması listesinden silmek istediğiniz zamanlamayı seçin. Sağ tıklayın ve açılan bağlam menüsünde **Sil**'e tıklayın. 

   ![Kullanıcı silme](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Zamanlama silindikten sonra zamanlama listesi güncelleştirilir.



## <a name="next-steps"></a>Sonraki adımlar

- [Bant genişliğini yönetmeyi](data-box-gateway-manage-bandwidth-schedules.md) öğrenin. 
