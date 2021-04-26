---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: cf67ea58c4c13756fdc6e437883f12124ca47eb9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104725"
---
1. [Azure Portal](https://portal.azure.com/), Azure Stack Edge kaynağını seçin ve **genel bakış**' a gidin. Cihazınızın çevrimiçi olması gerekir. **Bulut depolama ağ geçidi > depolama hesaplarına** gidin.

2. Cihaz komut çubuğunda **+ depolama hesabı ekle** ' yi seçin. 

   ![Depolama hesabı ekleme](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. **Edge depolama hesabı ekle** bölmesinde, aşağıdaki ayarları belirtin:

    1. Cihazınızda Edge depolama hesabı için benzersiz bir ad sağlayın. Depolama hesabı adları, yalnızca küçük harf ve harf içerebilir. Özel karakterlere izin verilmez. Depolama hesabı adı, cihaz içinde (cihazlarda değil) benzersiz olmalıdır.

    2. Depolama hesabının tutulduğu veriler hakkındaki bilgilere yönelik isteğe bağlı bir açıklama sağlayın.  
    
    3. Varsayılan olarak, Edge depolama hesabı buluttaki bir Azure depolama hesabıyla eşlenir ve depolama hesabındaki veriler otomatik olarak buluta gönderilir. Edge depolama hesabınızın eşlendiği Azure Depolama hesabını belirtin.

    4. Yeni bir kapsayıcı oluşturun veya Azure depolama hesabındaki mevcut bir kapsayıcıdan seçin. Uç depolama hesabına yazılan cihazdaki tüm veriler, eşlenen Azure depolama hesabındaki seçili depolama kapsayıcısına otomatik olarak yüklenir.

    5. Tüm depolama hesabı seçenekleri belirtilmişse, kenar depolama hesabını oluşturmak için **Ekle** ' yi seçin. Sınır depolama hesabı başarıyla oluşturulduğunda size bildirilir. Yeni Edge depolama hesabı daha sonra Azure portal depolama hesapları listesinde görüntülenir.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. Bu yeni depolama hesabını seçer ve **erişim anahtarlarına** giderseniz, blob hizmeti uç noktasını ve karşılık gelen depolama hesabı adını bulabilirsiniz. Bu bilgileri erişim anahtarlarıyla birlikte, sınır depolama hesabına bağlanmanıza yardımcı olacak şekilde kopyalayın.

    ![Depolama hesabı ekleme 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    [Azure Resource Manager kullanarak, cihaz yerel API 'Lerine bağlanarak](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md)erişim anahtarlarını alırsınız.