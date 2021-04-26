---
title: GPU ile Azure Stack Edge Pro 'da işlem iş yükünü dağıtmak için IoT Edge modülünü kullanma | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazınızda önceden oluşturulmuş bir IoT Edge modülünü kullanarak bir işlem iş yükünü çalıştırmayı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d10e27c80a9253de7482644debd19debce8f4e50
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055306"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Öğretici: Azure Stack Edge Pro GPU üzerinde IoT Edge modülüyle işlem yükünü çalıştırma

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Bu öğreticide, Azure Stack Edge Pro GPU cihazınızda bir IoT Edge modülünü kullanarak bir işlem iş yükünün nasıl çalıştırılacağı açıklanmaktadır. İşlem yapılandırıldıktan sonra cihaz, verileri Azure 'a göndermeden önce dönüştürür.

Bu yordamın tamamlanması yaklaşık 10 ila 15 dakika sürebilir.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Paylaşımlar Ekle
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

 
## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro GPU cihazınızda bir işlem rolü ayarlamadan önce şunları yaptığınızdan emin olun:

- Azure Stack Edge Pro cihazınızı, [Azure Stack Edge Pro 'Yu etkinleştirme](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.
- Verilerinizde çalıştırabileceğiniz bir IoT Edge modülünüzün olması gerekir. Bu öğreticide, verileri `filemove2` Azure depolama hesabına giden Edge paylaşımından kenar paylaşımıyla taşırken bir modül kullandık.


## <a name="configure-compute"></a>İşlem yapılandırma

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Paylaşımlar Ekle

Bu öğreticide basit dağıtım için iki paylaşım gerekir: bir kenar paylaşımı ve başka bir kenar yerel paylaşımı.

1. Cihaza bir Edge paylaşma eklemek için aşağıdaki adımları uygulayın:

    1. Azure Stack Edge kaynağında **bulut depolama ağ geçidi > paylaşımlar**' a gidin.
    2. Komut çubuğundan **+ paylaşma Ekle**' yi seçin.
    3. **Paylaşma Ekle** dikey penceresinde, paylaşma adını girip paylaşma türünü seçin.
    4. Edge payını bağlamak için, **kenar ile paylaşma kullanımını kullanın** onay kutusunu seçin.
    5. Mevcut bir kullanıcı olan **Depolama hesabını**, **Depolama hizmetini** seçin ve ardından **Oluştur**' u seçin.

        ![Kenar paylaşma ekleme](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > NFS paylaşımının işlem için bağlanması için, işlem ağının, NFS sanal IP adresi ile aynı alt ağda yapılandırılması gerekir. İşlem ağını yapılandırma hakkında daha fazla bilgi için, [Azure Stack Edge Pro 'da işlem ağını etkinleştirme](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)bölümüne gidin.

    Kenar paylaşma oluşturulur ve başarılı bir oluşturma bildirimi alırsınız. Paylaşma listesi güncelleştirilmiş olabilir, ancak paylaşımın oluşturulmasının tamamlanmasını beklemeniz gerekir.

2. Cihaza bir Edge Yerel eklentisi eklemek için önceki adımda bulunan tüm adımları yineleyin ve **Edge Yerel paylaşma olarak Yapılandır** onay kutusunu seçin. Yerel paylaşımdaki veriler cihazda kalır.

    ![Edge Yerel paylaşma ekleme](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Yerel bir NFS paylaşma oluşturduysanız, dosyaları paylaşıma kopyalamak için aşağıdaki uzaktan eşitleme (rsync) komut seçeneğini kullanın:

    `rsync <source file path> < destination file path>`

    Komutu hakkında daha fazla bilgi için `rsync` [ `Rsync` belgelere](https://www.computerhope.com/unix/rsync.htm)gidin.
 
3. Güncelleştirilmiş paylaşımların listesini görmek için **bulut depolama ağ geçidi > paylaşımlarına** gidin.

    ![Güncelleştirilmiş paylaşım listesi](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modül Ekle

Özel veya önceden oluşturulmuş bir modül ekleyebilirsiniz. Cihaz önceden oluşturulmuş veya özel modüller ile birlikte gelmez. Özel bir modül oluşturmayı öğrenmek için [Azure Stack Edge Pro cihazınız Için C# modülü geliştirme](./azure-stack-edge-gpu-create-iot-edge-module.md)bölümüne gidin.

Bu bölümde, [Azure Stack Edge Pro Için C# modülü geliştirme](./azure-stack-edge-gpu-create-iot-edge-module.md)bölümünde oluşturduğunuz IoT Edge cihazına özel bir modül eklersiniz. Bu özel modül, uç cihazdaki bir uç yerel paylaşımından dosya alır ve bunları cihazdaki bir kenar (bulut) paylaşımıyla taşımalıdır. Ardından bulut paylaşımından, dosyaları bulut paylaşımıyla ilişkili Azure depolama hesabına iter.

Modül eklemek için aşağıdaki adımları uygulayın:

1. **IoT Edge > modüllerine** gidin. Komut çubuğundan **+ Modül Ekle**' yi seçin. 

2. **Modül Ekle** dikey penceresinde aşağıdaki değerleri girin:

    
    |Alan  |Değer  |
    |---------|---------|
    |Ad     | Modül için benzersiz bir ad. Bu modül, Azure Stack Edge Pro ile ilişkili IoT Edge cihazına dağıtabileceğiniz bir Docker kapsayıcısıdır.        |
    |Görüntü URI 'SI     | Modülün karşılık gelen kapsayıcı görüntüsü için görüntü URI 'SI.        |
    |Kimlik bilgileri gerekli     | İşaretliyse, Kullanıcı adı ve parola, eşleşen bir URL ile modülleri almak için kullanılır.        |
    |Giriş paylaşma     | Bir giriş paylaşma seçin. Edge Yerel paylaşma, bu durumda giriş paylaşımıdır. Burada kullanılan modül, dosyaları uç yerel paylaşımından buluta yüklendikleri bir kenar paylaşımıyla taşıırlar.        |
    |Çıkış payı     | Bir çıkış payı seçin. Edge paylaşma, bu durumda çıkış paylaşımıdır.        |
    |Tetikleyici türü     | **Dosya** veya **zamanlama** arasından seçim yapın. Dosya tetikleyicisi, giriş paylaşımına dosya yazılması gibi bir dosya olayı gerçekleştiğinde tetiklenir. Zamanlanmış tetikleyici, sizin tanımladığınız zamanlamaya göre tetiklenir.         |
    |Tetikleyici adı     | Tetikleyiciniz için benzersiz bir ad.         |
    |Ortam değişkenleri| Modülünüzün çalışacağı ortamı tanımlamaya yardımcı olacak isteğe bağlı bilgiler.   |

    ![Modül ekleme ve yapılandırma](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. **Add (Ekle)** seçeneğini belirleyin. Modül eklendi. **IoT Edge > modülleri** sayfası, modülün dağıtıldığını belirtecek şekilde güncelleştirilir. Eklediğiniz modülün çalışma zamanı durumu *çalışıyor* olmalıdır.

    ![Modül dağıtıldı](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Veri dönüştürme işlemini doğrulama ve verileri aktarma

Son adım modülün çalıştığından ve verilerin beklenen şekilde işlenmesini sağlamaktır. Modülün çalışma zamanı durumu, IoT Hub kaynağında IoT Edge cihazınız için çalışıyor olmalıdır.

Modülün çalıştığını ve verileri beklenen şekilde işlemesini doğrulamak için aşağıdakileri yapın:


1. Dosya Gezgini 'nde, daha önce oluşturduğunuz uç yerel ve kenar paylaşımlarına bağlanın. Adımlara bakın 

    ![Uç yerel ve uç bulut paylaşımlarına bağlanma](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Yerel paylaşıma veri ekleyin.

    ![Dosya Edge Yerel paylaşımında kopyalanmış](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Veriler bulut paylaşımına taşınır.

    ![Dosya Edge bulut paylaşımıyla taşındı](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Veriler daha sonra bulut paylaşımından depolama hesabına gönderilir. Verileri görüntülemek için Portal 'da Depolama Gezgini veya Azure Storage ' ı kullanabilirsiniz.

    ![Depolama hesabındaki verileri doğrulama](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
Doğrulama işlemini tamamladınız.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Paylaşımlar Ekle
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

Azure Stack Edge Pro cihazınızı yönetme hakkında bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro 'Yu yönetmek için yerel Web Kullanıcı arabirimini kullanma](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)