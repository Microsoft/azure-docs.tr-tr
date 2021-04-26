---
title: Azure Marketi 'nden Microsoft Azure Stack Edge Pro cihazınızda GPU modülünü dağıtma | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazınızda GPU özellikli IoT modülünün nasıl dağıtılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568444"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazında Azure Marketi 'nden GPU özellikli bir IoT modülü dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Bu makalede, bir grafik Işleme birimi (GPU) etkin IoT Edge modülünün Azure Stack Edge Pro cihazınızda Azure Marketi 'nden nasıl dağıtılacağı açıklanır. 

Bu makalede şunları öğreneceksiniz:
  - Azure Stack Edge Pro 'YU bir GPU modülünü çalıştırmaya hazırlayın.
  - Azure Marketi 'nden GPU özellikli IoT modülünü indirin ve dağıtın.
  - Modül çıkışını izleyin.

## <a name="about-sample-module"></a>Örnek modül hakkında

Bu makaledeki GPU örnek modülü,, GPU 'ya karşı CPU için örnek kodu PyTorch ve TensorFlow benchişaretlemesini içerir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- GPU etkin 1 düğümlü Azure Stack Edge cihazına erişirsiniz. Bu cihaz, Azure'da bir kaynakla birlikte etkinleştirilir. 
- Bu cihazda işlem yapılandırdınız. Öğreticideki adımları izleyin [: Azure Stack Edge cihazınızda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md).
- Bir Windows istemcisinde aşağıdaki geliştirme kaynakları:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code için Azure IoT Edge uzantısı](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).   


## <a name="get-module-from-azure-marketplace"></a>Azure Marketi 'nden modül al

1. [Azure Marketi 'ndeki tüm uygulamalara](https://azuremarketplace.microsoft.com/marketplace/apps)gözatamazsınız.

    ![Azure Market 'te uygulamalara gözatamazsınız](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. GPU 'lara **Başlarken**' i arayın.

    ![GPU örnek modülünde ara](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. **Şimdi al**' ı seçin.

    ![Örnek modül al](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Sağlayıcının kullanım koşullarını ve gizlilik ilkesini onaylamak için **devam** ' ı seçin. 

    ![Örnek modül 2 al](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Azure Stack Edge Pro cihazınızı dağıtmak için kullandığınız aboneliği seçin.

    ![Abonelik seçme](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Azure Stack Edge Pro cihazınızı yapılandırdığınızda oluşturduğunuz IoT Hub hizmetinin adını girin. Bu IoT Hub hizmet adını bulmak için Azure portal cihazındaki aygıtınızla ilişkili Azure Stack Edge kaynağına gidin. 

    1. Sol bölmedeki menü seçeneklerinde, **Edge hizmetleri > IoT Edge** gidin. 

        ![İşlem yapılandırmasını görüntüle](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. **Özellikler**' e gidin. 

        1. Azure Stack Edge Pro cihazınızda işlem yapılandırdığınızda oluşturulan IoT Hub hizmetini bir yere göz önüne alın.
        2. İşlem yapılandırdığınızda oluşturulan IoT Edge cihazının adını unutmayın. Sonraki adımda bu adı kullanacaksınız.

        ![Edge işlem yapılandırması](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. **Cihaza dağıt**' ı seçin.

11. IoT Edge cihazının adını girin veya hub 'a kayıtlı cihazlar arasında gezinmek için **Cihazı bul** ' u seçin.

    ![Cihaz bul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. İsterseniz, başka modüller ekleme dahil olmak üzere bir dağıtım bildirimini yapılandırmaya yönelik standart işleme devam etmek için **Oluştur** ' u seçin. Görüntü URI 'SI, oluşturma seçenekleri ve istenen özellikler gibi yeni modülün ayrıntıları önceden tanımlanmıştır ancak değiştirilebilir.

    ![Oluştur’u seçin](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Modülün Azure portal IoT Hub dağıtıldığını doğrulayın. Cihazınızı seçin, **modülleri ayarla** ' yı seçin ve modülün **IoT Edge modüller** bölümünde listelenmesi gerekir.

    ![2 oluştur seçeneğini belirleyin](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Modülü izleme  

1. VS Code komut paletinde **Azure IoT Hub: Select IoT Hub** komutunu çalıştırın.

2. Yapılandırmak istediğiniz IoT Edge cihazını barındıran aboneliği ve IoT hub'ını seçin. Bu durumda, Azure Stack Edge Pro cihazını dağıtmak için kullanılan aboneliği seçin ve Azure Stack Edge Pro cihazınız için oluşturulan IoT Edge cihazı seçin. Bu, önceki adımlarda Azure portal aracılığıyla işlem yapılandırdığınızda oluşur.

3. VS Code Gezgini ' nde Azure IoT Hub bölümünü genişletin. **Cihazlar**' ın altında, Azure Stack Edge Pro cihazınıza karşılık gelen IoT Edge cihazını görmeniz gerekir. 

    1. Bu cihazı seçin, sağ tıklayın ve **yerleşik olay uç noktasını Izlemeye başla**' yı seçin.
  
        ![İzlemeyi Başlat](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **Cihazlar > modüller** ' e gidin ve **GPU modülünün** çalıştığını görmeniz gerekir.

    3. VS Code Terminal, Azure Stack Edge Pro cihazınız için izleme çıktısı olarak IoT Hub olaylarını da göstermelidir.

        ![İzleme çıktısı](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        GPU 'ya göre aynı işlem kümesini (şekil dönüştürme 5000 yinelemesi) yürütmek için geçen sürenin, CPU 'nun çok daha küçük olduğunu görebilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar

- [GPU 'yu bir modül kullanacak şekilde yapılandırma](./azure-stack-edge-gpu-configure-gpu-modules.md)hakkında daha fazla bilgi edinin.