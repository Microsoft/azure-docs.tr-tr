---
title: Azure portal 'de GPU ile Azure Stack Edge Pro cihazını etkinleştirme öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro GPU 'SU dağıtma öğreticisi, fiziksel cihazınızı etkinleştirmenizi sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: alkohli
ms.openlocfilehash: 8e88fb2f6f2fc9ad50911bfda2245cd95ae33236
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058757"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Öğretici: Azure Stack Edge Pro 'YU GPU ile etkinleştirme

Bu öğretici, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Pro cihazınızı birlikte bulunan bir GPU ile nasıl etkinleştirebileceğinizi açıklar.

Etkinleştirme işleminin tamamlanması 5 dakika sürebilir.

Bu öğreticide hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Fiziksel cihazı etkinleştirin

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızı GPU ile yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazınız için: 
    
    - Fiziksel cihazı [yükleme Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)' da ayrıntılı olarak yüklediniz.
    - Ağı [, işlem ağını, Web proxy 'Sini yapılandırma](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) bölümünde ayrıntılı olarak ağ ve işlem ağ ayarlarını yapılandırdınız.
    - Cihaz adı veya DNS etki alanını **cihaz** sayfası aracılığıyla değiştirdiyseniz, kendi aygıtınızı karşıya yüklediniz veya cihazınızda cihaz sertifikaları oluşturmuş olursunuz. Bu adımı yapmadıysanız, cihaz etkinleştirme sırasında bir hata görürsünüz ve etkinleştirme engellenir. Daha fazla bilgi için [sertifikaları yapılandırma](azure-stack-edge-gpu-deploy-configure-certificates.md)bölümüne gidin.
    
* Azure Stack Edge Pro cihazını yönetmek için oluşturduğunuz Azure Stack Edge hizmetinden etkinleştirme anahtarınız vardır. Daha fazla bilgi için, [Azure Stack Edge Pro 'yu dağıtmaya hazırlanma](azure-stack-edge-gpu-deploy-prep.md)bölümüne gidin.


## <a name="activate-the-device"></a>Cihazı Etkinleştir

1. Cihazın yerel Web Kullanıcı arabiriminde, **Başlarken** sayfasına gidin.
2. **Etkinleştirme** kutucuğunda **Etkinleştir**' i seçin. 

    ![Yerel Web Kullanıcı arabirimi "bulut ayrıntıları" sayfası](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. **Etkinleştir** bölmesinde, [Azure Stack Edge Pro etkinleştirme anahtarını almak için](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)aldığınız **etkinleştirme anahtarını** girin.

4. **Uygula**’yı seçin.

    ![Yerel Web Kullanıcı arabirimi "bulut ayrıntıları" sayfa 2](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. İlk olarak cihaz etkinleştirilir. Daha sonra anahtar dosyasını indirmeniz istenir.
    
    ![Yerel Web Kullanıcı arabirimi "bulut ayrıntıları" sayfa 3](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    **İndir ve devam et** ' i seçin ve *device-serial-no.js* dosyayı cihazın dışında güvenli bir konuma kaydedin. **Bu anahtar dosya, cihazınızdaki işletim sistemi diski ve veri diskleri için kurtarma anahtarlarını içerir**. Gelecekteki sistem kurtarmasını kolaylaştırmak için bu anahtarlar gerekebilir.

    *JSON* dosyasının içeriği aşağıda verilmiştir:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    Aşağıdaki tabloda çeşitli anahtarlar açıklanmaktadır:
    
    |Alan  |Açıklama  |
    |---------|---------|
    |`Id`    | Bu, cihazın KIMLIĞIDIR.        |
    |`DataVolumeBitLockerExternalKeys`|Bunlar, veri diskleri için bit Lockers anahtarlardır ve cihazınızdaki yerel verileri kurtarmak için kullanılır.|
    |`SystemVolumeBitLockerRecoveryKey`| Bu, sistem birimi için BitLocker anahtarıdır. Bu anahtar, cihazınızın sistem yapılandırma ve Sistem verilerinin kurtarılmasına yardımcı olur. |
    |`ServiceEncryptionKey`| Bu anahtar, Azure hizmeti üzerinden akan verileri korur. Bu anahtar, Azure hizmeti güvenliğinin tehlikeye girmesi, depolanan bilgilerin güvenliğinin aşılmasına neden olmaz. |

6. **Genel bakış** sayfasına gidin. Cihaz durumu, **etkinleştirilmiş** olarak gösterilmelidir.

    ![Yerel Web Kullanıcı arabirimi "bulut ayrıntıları" sayfa 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Cihaz etkinleştirme işlemi tamamlanmıştır. Artık cihazınıza paylaşımlar ekleyebilirsiniz.

Etkinleştirme sırasında herhangi bir sorunla karşılaşırsanız, [etkinleştirme sorunlarını giderme ve Azure Key Vault hataları](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors)bölümüne gidin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Fiziksel cihazı etkinleştirin

Azure Stack Edge Pro cihazınızla veri aktarmayı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro ile veri aktarma](./azure-stack-edge-gpu-deploy-add-shares.md)