---
title: Azure Stack Edge Pro GPU işlem yönetimi | Microsoft Docs
description: Tetikleyici, modüller, işlem yapılandırmasını görüntüleme gibi kenar işlem ayarlarının nasıl yönetileceğini, Azure Stack Edge Pro GPU 'unuzdaki Azure portal aracılığıyla yapılandırmayı kaldırmayı açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 057f80049b13a265925f2d6f24a008d1e1e04c96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102639016"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 'unuzda işlem yönetme

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Azure Stack Edge Pro GPU cihazınızda IoT Edge hizmeti aracılığıyla işlem yönetimi açıklanmaktadır. Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla işlem yönetebilirsiniz. Modül, Tetikleyiciler ve IoT Edge yapılandırmayı yönetmek için Azure portal kullanın ve işlem ağ ayarlarını yönetmek için yerel Web Kullanıcı arabirimini kullanın.



## <a name="manage-triggers"></a>Tetikleyicileri yönetme

Olaylar, bulut ortamınızda veya cihazınızda gerçekleşen ve üzerinde işlem yapmak isteyebileceğiniz şeylerdir. Örneğin paylaşımda bir dosya oluşturulduğunda, bu bir olaydır. Tetikleyiciler olayları başlatır. Azure Stack Edge Pro, Tetikleyiciler dosya olaylarına veya bir zamanlamaya yanıt verebilir.

- **Dosya**: Bu Tetikleyiciler, bir dosyanın oluşturulması, bir dosyanın değiştirilmesi gibi dosya olaylarına yanıt olarak yapılır.
- **Zamanlandı**: Bu Tetikleyiciler, bir başlangıç tarihi, başlangıç zamanı ve yineleme aralığı ile tanımlayabileceğiniz bir zamanlamaya yanıt olarak yapılır.


### <a name="add-a-trigger"></a>Tetikleyici ekleme

Bir tetikleyici oluşturmak için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidin ve **IoT Edge**' e gidin. **Tetikleyiciler** ' e gidin ve komut çubuğunda **+ tetikleyici Ekle** ' yi seçin.

    ![Tetikleyici Ekle 'yi seçin](media/azure-stack-edge-gpu-manage-compute/add-trigger-1-m.png)

2. **Tetikleyici Ekle** dikey penceresinde Tetikleyiciniz için benzersiz bir ad sağlayın.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Tetikleyici için bir **tür** seçin. Tetikleyici bir dosya olayına yanıt olarak olduğunda **Dosya** ' yı seçin. Tetikleyicinin tanımlı bir zamanda başlamasını istediğiniz zaman **zamanlanan** ' i seçin ve belirtilen bir yineleme aralığında çalıştırın. Seçiminize bağlı olarak, farklı bir seçenek kümesi sunulur.

    - **Dosya tetikleyicisi** -bağlı bir paylaşımdan açılan listeden seçim yapın. Bu paylaşımda bir dosya olayı tetiklendiğinde tetikleyici bir Azure Işlevi çağırır.

        ![SMB paylaşımı ekleme](media/azure-stack-edge-gpu-manage-compute/add-file-trigger.png)

    - **Zamanlanan tetikleyici** -başlangıç tarihini/saatini ve yineleme aralığını saat, dakika veya saniye cinsinden belirtin. Ayrıca, konu başlığı için bir ad girin. Bir konu, tetikleyiciyi cihaza dağıtılan bir modüle yönlendirme esnekliği sağlayacaktır.

        Örnek yol dizesi: `"route3&quot;: &quot;FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint(&quot;modules/modulename/inputs/input1")"` .

        ![NFS paylaşımı ekleme](media/azure-stack-edge-gpu-manage-compute/add-scheduled-trigger.png)

4. Tetikleyiciyi oluşturmak için **Ekle** ' yi seçin. Bir bildirim, tetikleyici oluşturma işleminin devam ettiğini gösterir. Tetikleyici oluşturulduktan sonra, dikey pencere yeni tetikleyiciyi yansıtacak şekilde güncelleştirilir.
 
    ![Güncelleştirilmiş tetikleyici listesi](media/azure-stack-edge-gpu-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Tetikleyiciyi silme

Bir tetikleyiciyi silmek için Azure portal aşağıdaki adımları uygulayın.

1. Tetikleyiciler listesinden silmek istediğiniz tetikleyiciyi seçin.

    ![Tetikleyiciyi seçin](media/azure-stack-edge-gpu-manage-compute/delete-trigger-1.png)

2. Sağ tıklayıp **Sil**' i seçin.

    ![Sil ' i seçin](media/azure-stack-edge-gpu-manage-compute/delete-trigger-2.png)

3. Onayınız istendiğinde **Evet**’e tıklayın.

    ![Silmeyi onayla](media/azure-stack-edge-gpu-manage-compute/delete-trigger-3.png)

Tetikleyicilerin listesi, silme işlemini yansıtacak şekilde güncelleştirilir.

## <a name="manage-iot-edge-configuration"></a>IoT Edge yapılandırmasını yönetme

İşlem yapılandırmasını görüntülemek, mevcut bir işlem yapılandırmasını kaldırmak veya Azure Stack Edge Pro için IoT cihazının ve IoT Edge cihazının erişim anahtarlarını eşitlemek üzere işlem yapılandırmasını yenilemek için Azure portal kullanın.

### <a name="view-iot-edge-configuration"></a>IoT Edge yapılandırmayı görüntüle

Cihazınızın IoT Edge yapılandırmasını görüntülemek için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidin ve **IoT Edge**' e gidin. Cihazınızda IoT Edge hizmeti etkinleştirildikten sonra genel bakış sayfası, IoT Edge hizmetinin sorunsuz çalıştığını gösterir.

    ![İşlem görüntüleme seçin](media/azure-stack-edge-gpu-manage-compute/view-compute-1.png)

2. Cihazınızda IoT Edge yapılandırmasını görüntülemek için **Özellikler** ' e gidin. İşlem yapılandırdığınızda, bir IoT Hub kaynağı oluşturdunuz. Bu IoT Hub kaynağı altında, bir IoT cihazı ve bir IoT Edge cihaz yapılandırılır. IoT Edge cihazda çalıştırmak için yalnızca Linux modülleri desteklenir.

    ![Yapılandırmayı görüntüle](media/azure-stack-edge-gpu-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>IoT Edge hizmeti 'ni kaldır

Cihazınızın mevcut IoT Edge yapılandırmasını kaldırmak için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidin ve **IoT Edge**' e gidin. **Genel Bakış ' a** gidin ve komut çubuğunda **Kaldır** ' ı seçin.

    ![İşlem kaldırma seçeneğini belirleyin](media/azure-stack-edge-gpu-manage-compute/remove-compute-1.png)

2. IoT Edge hizmetini kaldırırsanız, eylem geri alınamaz ve geri alınamaz. Oluşturduğunuz modüller ve Tetikleyiciler de silinir. IoT Edge tekrar kullanmanız gerekiyorsa cihazınızı yeniden yapılandırmanız gerekir. Onay istendiğinde **Tamam**' ı seçin.

    ![İşlem kaldırma 2 ' yi seçin](media/azure-stack-edge-gpu-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT cihazını ve IoT Edge cihaz erişim anahtarlarını eşitleyin

Azure Stack Edge Pro 'unuzda işlem yapılandırdığınızda, bir IoT cihazı ve bir IoT Edge cihaz oluşturulur. Bu cihazlara otomatik olarak simetrik erişim anahtarları atanır. En iyi güvenlik uygulaması olarak, bu anahtarlar IoT Hub hizmeti aracılığıyla düzenli olarak döndürülür.

Bu anahtarları döndürmek için, oluşturduğunuz IoT Hub hizmetine gidebilir ve IoT cihazını veya IoT Edge cihazını seçebilirsiniz. Her cihazda birincil erişim anahtarı ve ikincil erişim anahtarları bulunur. Birincil erişim anahtarını ikincil erişim anahtarına atayın ve ardından birincil erişim anahtarını yeniden oluşturun.

IoT cihazınız ve IoT Edge cihaz anahtarlarınız döndürülürse, en son erişim anahtarlarını almak için Azure Stack Edge Pro 'unuzda yapılandırmayı yenilemeniz gerekir. Eşitleme, cihazın IoT cihazınız ve IoT Edge cihazınız için en son anahtarları almasını sağlar. Azure Stack Edge Pro yalnızca birincil erişim anahtarlarını kullanır.

Cihazınızın erişim anahtarlarını eşitlemek için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağına gidin ve **IoT Edge işlem**' e gidin. **Genel Bakış ' a** gidin ve komut çubuğunda **yapılandırmayı Yenile** ' yi seçin.

    ![Yapılandırmayı Yenile seçeneğini belirleyin](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-1.png)

2. Onay sorulduğunda **Evet** ' i seçin.

    ![İstendiğinde Evet ' i seçin](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-2.png)

3. Eşitleme tamamlandıktan sonra iletişim kutusunu kapatın.

## <a name="change-external-service-ips-for-containers"></a>Kapsayıcılar için dış hizmet IP 'lerini değiştirme

Kubernetes dış hizmet IP 'Leri, Kubernetes kümesi dışında kullanıma sunulan hizmetlere ulaşmak için kullanılır. Cihazınız etkinleştirildikten sonra, yerel kullanıcı arabirimine erişerek cihazınızın Kapsayıcılı iş yükleri için dış hizmet IP 'lerini ayarlayabilir veya değiştirebilirsiniz.


1. Cihazın yerel kullanıcı arabiriminde, **işlem**' a gidin.
1. Ağı işlem için yapılandırılmış olan bağlantı noktasını seçin. Açılan dikey pencerede, Kubernetes dış hizmet IP 'lerini belirtin (yeni) veya değiştirin (varsa). Bu IP 'Ler, Kubernetes kümesi dışında açığa çıkarılması gereken hizmetler için kullanılır. 
    - Cihazınızda çalışan hizmet için en az 1 hizmet IP 'si gerekir `edgehub` ve IoT Edge modüller tarafından kullanılır. 
    - Dağıtımı planladığınız her ek IoT Edge modülü veya kapsayıcısı için bir IP gerekir. 
    - Bunlar statik, bitişik IP 'lardır.

    ![Kubernetes hizmet IP 'lerini değiştirme](media/azure-stack-edge-gpu-manage-compute/change-service-ips-1.png)

1. **Uygula**’yı seçin. IP 'Ler uygulandıktan sonra, cihazınızın yeniden başlatma veya yeniden başlatma gerekmez. Yeni IP 'Ler hemen devreye girer.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'yu nasıl giderebileceğinizi](azure-stack-edge-gpu-troubleshoot.md)öğrenin.
