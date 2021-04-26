---
title: Azure Stack Edge Pro işlem yönetimi | Microsoft Docs
description: Tetikleyici, modüller, işlem yapılandırmasını görüntüleme gibi kenar işlem ayarlarının nasıl yönetileceğini, Azure Stack Edge Pro 'daki Azure portal aracılığıyla yapılandırmayı kaldırmayı açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: ebf967fe02ab6424b9952bb0315b70fadb547fe3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97967920"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'unuzda işlem yönetme

Bu makalede Azure Stack Edge Pro 'da işlem yönetimi açıklanmaktadır. Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla işlem yönetebilirsiniz. Modül, Tetikleyiciler ve işlem yapılandırmasını yönetmek için Azure portal kullanın ve işlem ayarlarını yönetmek için yerel Web Kullanıcı arabirimini kullanın.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Tetikleyicileri yönetme
> * İşlem yapılandırmasını yönetme


## <a name="manage-triggers"></a>Tetikleyicileri yönetme

Olaylar, bulut ortamınızda veya cihazınızda gerçekleşen ve üzerinde işlem yapmak isteyebileceğiniz şeylerdir. Örneğin paylaşımda bir dosya oluşturulduğunda, bu bir olaydır. Tetikleyiciler olayları başlatır. Azure Stack Edge Pro, Tetikleyiciler dosya olaylarına veya bir zamanlamaya yanıt verebilir.

- **Dosya**: Bu Tetikleyiciler, bir dosyanın oluşturulması, bir dosyanın değiştirilmesi gibi dosya olaylarına yanıt olarak yapılır.
- **Zamanlandı**: Bu Tetikleyiciler, bir başlangıç tarihi, başlangıç zamanı ve yineleme aralığı ile tanımlayabileceğiniz bir zamanlamaya yanıt olarak yapılır.


### <a name="add-a-trigger"></a>Tetikleyici ekleme

Bir tetikleyici oluşturmak için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidip **IoT Edge > tetikleyicilerine** gidin. Komut çubuğunda **+ tetikleyici Ekle** ' yi seçin.

    ![Tetikleyici Ekle 'yi seçin](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. **Tetikleyici Ekle** dikey penceresinde Tetikleyiciniz için benzersiz bir ad sağlayın.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Tetikleyici için bir **tür** seçin. Tetikleyici bir dosya olayına yanıt olarak olduğunda **Dosya** ' yı seçin. Tetikleyicinin tanımlı bir zamanda başlamasını istediğiniz zaman **zamanlanan** ' i seçin ve belirtilen bir yineleme aralığında çalıştırın. Seçiminize bağlı olarak, farklı bir seçenek kümesi sunulur.

    - **Dosya tetikleyicisi** -bağlı bir paylaşımdan açılan listeden seçim yapın. Bu paylaşımda bir dosya olayı tetiklendiğinde tetikleyici bir Azure Işlevi çağırır.

        ![SMB paylaşımı ekleme](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **Zamanlanan tetikleyici** -başlangıç tarihini/saatini ve yineleme aralığını saat, dakika veya saniye cinsinden belirtin. Ayrıca, konu başlığı için bir ad girin. Bir konu, tetikleyiciyi cihaza dağıtılan bir modüle yönlendirme esnekliği sağlayacaktır.

        Örnek yol dizesi: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![NFS paylaşımı ekleme](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. Tetikleyiciyi oluşturmak için **Ekle** ' yi seçin. Bir bildirim, tetikleyici oluşturma işleminin devam ettiğini gösterir. Tetikleyici oluşturulduktan sonra, dikey pencere yeni tetikleyiciyi yansıtacak şekilde güncelleştirilir.
 
    ![Güncelleştirilmiş tetikleyici listesi](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Tetikleyiciyi silme

Bir tetikleyiciyi silmek için Azure portal aşağıdaki adımları uygulayın.

1. Tetikleyiciler listesinden silmek istediğiniz tetikleyiciyi seçin.

    ![Tetikleyiciyi seçin](media/azure-stack-edge-manage-compute/delete-trigger-1.png)

2. Sağ tıklayıp **Sil**' i seçin.

    ![Sil ' i seçin](media/azure-stack-edge-manage-compute/delete-trigger-2.png)

3. Onayınız istendiğinde **Evet**’e tıklayın.

    ![Silmeyi onayla](media/azure-stack-edge-manage-compute/delete-trigger-3.png)

Tetikleyicilerin listesi, silme işlemini yansıtacak şekilde güncelleştirilir.

## <a name="manage-compute-configuration"></a>İşlem yapılandırmasını yönetme

İşlem yapılandırmasını görüntülemek, mevcut bir işlem yapılandırmasını kaldırmak veya Azure Stack Edge Pro için IoT cihazının ve IoT Edge cihazının erişim anahtarlarını eşitlemek üzere işlem yapılandırmasını yenilemek için Azure portal kullanın.

### <a name="view-compute-configuration"></a>İşlem yapılandırmasını görüntüle

Cihazınızın işlem yapılandırmasını görüntülemek için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidip **IoT Edge > genel bakış**' a gidin. 

    ![İşlem görüntüleme seçin](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. **Özellikler** sayfasına gidin. Cihazınızda işlem yapılandırmasını bir yere getirin. İşlem yapılandırdığınızda, bir IoT Hub kaynağı oluşturdunuz. Bu IoT Hub kaynağı altında, bir IoT cihazı ve bir IoT Edge cihaz yapılandırılır. IoT Edge cihazda çalıştırmak için yalnızca Linux modülleri desteklenir.

    ![Yapılandırmayı görüntüle](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>İşlem yapılandırmasını kaldır

Cihazınızın mevcut Edge işlem yapılandırmasını kaldırmak için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidip **IoT Edge > genel bakış**' a gidin. Komut çubuğunda **Kaldır** ' ı seçin.

    ![İşlem kaldırma seçeneğini belirleyin](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. İşlem yapılandırmasını kaldırırsanız, işlem yeniden kullanmanız gerektiğinde cihazınızı yeniden yapılandırmanız gerekir. Onay istendiğinde **Evet**' i seçin.

    ![İşlem kaldırma 2 ' yi seçin](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT cihazını ve IoT Edge cihaz erişim anahtarlarını eşitleyin

Azure Stack Edge Pro 'unuzda işlem yapılandırdığınızda, bir IoT cihazı ve bir IoT Edge cihaz oluşturulur. Bu cihazlara otomatik olarak simetrik erişim anahtarları atanır. En iyi güvenlik uygulaması olarak, bu anahtarlar IoT Hub hizmeti aracılığıyla düzenli olarak döndürülür.

Bu anahtarları döndürmek için, oluşturduğunuz IoT Hub hizmetine gidebilir ve IoT cihazını veya IoT Edge cihazını seçebilirsiniz. Her cihazda birincil erişim anahtarı ve ikincil erişim anahtarları bulunur. Birincil erişim anahtarını ikincil erişim anahtarına atayın ve ardından birincil erişim anahtarını yeniden oluşturun.

IoT cihazınız ve IoT Edge cihaz anahtarlarınız döndürülürse, en son erişim anahtarlarını almak için Azure Stack Edge Pro 'unuzda yapılandırmayı yenilemeniz gerekir. Eşitleme, cihazın IoT cihazınız ve IoT Edge cihazınız için en son anahtarları almasını sağlar. Azure Stack Edge Pro yalnızca birincil erişim anahtarlarını kullanır.

Cihazınızın erişim anahtarlarını eşitlemek için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidip **IoT Edge > genel bakış**' a gidin. Komut çubuğunda **yapılandırmayı Yenile** ' yi seçin.

    ![Yapılandırmayı Yenile seçeneğini belirleyin](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. Onay sorulduğunda **Evet** ' i seçin.

     ![İstendiğinde Evet ' i seçin](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. Eşitleme tamamlandıktan sonra iletişim kutusunu kapatın.

## <a name="next-steps"></a>Sonraki adımlar

- [Uç işlem ağını Azure Portal aracılığıyla yönetmeyi](azure-stack-edge-extend-compute-access-modules.md)öğrenin.
