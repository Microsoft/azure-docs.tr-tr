---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185573"
---
Depolama Gezgini ilk kez kullanıyorsanız, aşağıdaki adımları gerçekleştirmeniz gerekir.

1. Üst komut çubuğundan **> hedef Azure Stack API 'Leri Düzenle**' ye gidin.

    ![Depolama Gezgini Yapılandır](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Değişikliklerin etkili olması için Depolama Gezgini yeniden başlatın.


Depolama hesabına bağlanmak ve bağlantıyı doğrulamak için aşağıdaki adımları izleyin.

1. Depolama Gezgini, depolama hesapları ' nı seçin. Sağ tıklayın ve **Azure depolama 'Ya Bağlan** seçeneğini belirleyin. 

    ![Yapılandırma Depolama Gezgini 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. **Azure depolama 'Ya Bağlan** iletişim kutusunda **depolama hesabı adı ve anahtarı kullan**' ı seçin.

    ![Depolama Gezgini 3 yapılandırma](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. **Ad ve anahtar Ile Bağlan** iletişim kutusunda aşağıdaki adımları uygulayın:

    1. Edge depolama hesabınız için bir görünen ad girin. 
    2. Sınır depolama hesabı adını belirtin.
    3. Cihaz yerel API 'Lerinden aldığınız erişim anahtarını Azure Resource Manager aracılığıyla yapıştırın.
    4. Depolama etki alanını başka bir şekilde seçin **(aşağıya girin)** ve ardından blob hizmeti uç noktasının sonekini şu biçimde girin: `<appliance name>.<DNSdomain>` . 
    5. Http **üzerinden** aktarım *http* seçeneğini işaretleyin. 
    6. **İleri**’yi seçin.

    ![Depolama Gezgini 4 yapılandırma](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. **Bağlantı Özeti** iletişim kutusunda, belirtilen bilgileri gözden geçirin. **Bağlan**’ı seçin.

    ![Depolama Gezgini 5 yapılandırma](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. Başarıyla eklediğiniz hesap, adının sonuna (dış, diğer) Depolama Gezgini sol bölmesinde görüntülenir. Kapsayıcıyı görüntülemek için **BLOB kapsayıcıları** ' nı seçin.

    ![Blob kapsayıcılarını görüntüle](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

Doğrulamak için bir sonraki adım, veri aktarımının bu bağlantı üzerinden doğru şekilde çalıştığı şeydir.

Cihazdaki uç depolama hesabınıza veri yüklemek için aşağıdaki adımları uygulayın ve otomatik olarak eşlenmiş Azure Storage hesabına katmanlanmalıdır.

1. Uç depolama hesabınızda verileri yüklemek istediğiniz kapsayıcıyı seçin. **Karşıya yükle** ' yi seçip **dosyaları karşıya yükle**' yi seçin.

    ![Veri aktarımını doğrulama](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. **Dosyaları karşıya yükle** iletişim kutusunda, sayfasına gidin ve karşıya yüklemek istediğiniz dosyaları seçin. **İleri**’yi seçin.

    ![Veri aktarımını doğrulama 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Dosyaların karşıya yüklendiğini doğrulayın. Karşıya yüklenen dosyalar kapsayıcıda gösterilir.

    ![Veri aktarımını doğrulama 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Ardından, bu Edge depolama hesabıyla eşlenen Azure depolama hesabına bağlanırsınız. Edge depolama hesabına yüklenen tüm veriler Azure depolama hesabına otomatik olarak katman olmalıdır. 
    
    Azure depolama hesabının bağlantı dizesini almak için, **erişim anahtarlarına > Azure depolama hesabına** gidin ve bağlantı dizesini kopyalayın.

    ![Veri aktarımını doğrulama 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Azure depolama hesabına eklemek için bağlantı dizesini kullanın.  

    ![Veri aktarımını doğrulama 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. **Bağlantı Özeti** iletişim kutusunda, belirtilen bilgileri gözden geçirin. **Bağlan**’ı seçin.

    ![Veri aktarımını doğrulama 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Edge depolama hesabına yüklediğiniz dosyaların Azure depolama hesabına aktarıldığını görürsünüz.

    ![Veri aktarımını doğrulama 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
