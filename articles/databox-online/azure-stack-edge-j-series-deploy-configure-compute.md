---
title: GPU ile Azure Stack Edge Pro 'da işlem ile verileri filtreleme, analiz etme öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazında işlem rolünü yapılandırmayı ve Azure 'a göndermeden önce verileri dönüştürmek için kullanmayı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 1dcf429ddc697dfbfdac721cb8de137b9382b234
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058791"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Öğretici: Azure Stack Edge Pro ile veri dönüştürme

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Bu öğreticide, Azure Stack Edge Pro cihazınızda bir işlem rolünün nasıl yapılandırılacağı açıklanmaktadır. İşlem rolünü yapılandırdıktan sonra Azure Stack Edge Pro, verileri Azure 'a göndermeden önce dönüştürebilir.

Bu yordamın tamamlanması yaklaşık 10 ila 15 dakika sürebilir.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Paylaşımlar Ekle
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

 
## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızda bir işlem rolü ayarlamadan önce şunları yaptığınızdan emin olun:
- Azure Stack Edge Pro cihazınızı, [Azure Stack Edge Pro 'Yu etkinleştirme](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.


## <a name="configure-compute"></a>İşlem yapılandırma

Azure Stack Edge Pro ortamınızda işlem yapılandırmak için bir IoT Hub kaynağı oluşturacaksınız.

1. Azure Stack Edge kaynağınızın Azure portal, **Genel Bakış ' a** gidin ve **IoT Edge**' yı seçin.

   ![İşlem ile çalışmaya başlama](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. **IoT Edge hizmeti 'Ni etkinleştir** bölümünde **Ekle**' yi seçin.

   ![İşlem yapılandırma](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. **IoT Edge hizmeti oluştur**' da, IoT Hub kaynağınızın ayarlarını girin:

   |Alan   |Değer    |
   |--------|---------|
   |Abonelik      | Azure Stack Edge kaynağı tarafından kullanılan abonelik. |
   |Kaynak grubu    | Azure Stack Edge kaynağı tarafından kullanılan kaynak grubu. |
   |IoT Hub           | **Yeni oluştur** veya **var olanı kullan** seçeneklerinden birini belirleyin. <br> Varsayılan olarak IoT kaynağı oluşturulurken Standart katmanı (S1) kullanılır. Bir ücretsiz katman IoT kaynağı kullanmak için kaynağı oluşturun ve sonra da mevcut kaynağı seçin. <br> Her durumda IoT Hub kaynağı, Azure Stack Edge kaynağıyla aynı aboneliği ve kaynak grubunu kullanır.     |
   |Name              | Yeni bir IoT Hub kaynağı için belirtilen varsayılan adı kullanmak istemiyorsanız, farklı bir ad girin. |

    ![İşlem 2 ile çalışmaya başlama](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Ayarları tamamladığınızda, **gözden geçir + oluştur**' u seçin. IoT Hub kaynağınız için ayarları gözden geçirin ve **Oluştur**' u seçin.

   Bir IoT Hub kaynağı için kaynak oluşturma birkaç dakika sürer. Kaynak oluşturulduktan sonra **genel bakış** IoT Edge hizmetin çalıştığını gösterir.

    ![İşlem 3 ' ü kullanmaya başlama](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Uç işlem rolünün yapılandırıldığını onaylamak için **Özellikler**' i seçin.

   ![İşlem 4 ' ü kullanmaya başlama](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

   Edge cihazında Edge hesaplama rolü ayarlandığında, iki cihaz oluşturur: bir IoT cihazı ve bir IoT Edge cihaz. Her iki cihaz de IoT Hub kaynağında görüntülenebilir. Bu IoT Edge cihazında aynı zamanda bir IoT Edge çalışma zamanı çalışıyor. Bu noktada, IoT Edge cihazınız için yalnızca Linux platformu kullanılabilir.


## <a name="add-shares"></a>Paylaşımlar Ekle

Bu öğreticide basit dağıtım için iki paylaşım gerekir: bir kenar paylaşımı ve başka bir kenar yerel paylaşımı.

1. Aşağıdaki adımları uygulayarak cihaza bir Edge paylaşma ekleyin:

    1. Azure Stack Edge kaynağında, **uç işlem >** başlayın ' a gidin.
    2. **Paylaşma Ekle** kutucuğunda **Ekle**' yi seçin.

        ![Paylaşma kutucuğu Ekle](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. **Paylaşma Ekle** dikey penceresinde, paylaşma adını girip paylaşma türünü seçin.
    4. Edge payını bağlamak için, **kenar ile paylaşma kullanımını kullanın** onay kutusunu seçin.
    5. Mevcut bir kullanıcı olan **Depolama hesabını**, **Depolama hizmetini** seçin ve ardından **Oluştur**' u seçin.

        ![Kenar paylaşma ekleme](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Yerel bir NFS paylaşma oluşturduysanız, dosyaları paylaşıma kopyalamak için aşağıdaki uzaktan eşitleme ( `rsync` ) komut seçeneğini kullanın:

    `rsync <source file path> < destination file path>`

    Komutu hakkında daha fazla bilgi için `rsync` [ `Rsync` belgelere](https://www.computerhope.com/unix/rsync.htm)gidin.

    > [!NOTE]
    > NFS paylaşımının işlem için bağlanması için, işlem ağının, NFS sanal IP adresi ile aynı alt ağda yapılandırılması gerekir. İşlem ağını yapılandırma hakkında daha fazla bilgi için, [Azure Stack Edge Pro 'da işlem ağını etkinleştirme](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)bölümüne gidin.

    Kenar paylaşma oluşturulur ve başarılı bir oluşturma bildirimi alırsınız. Paylaşma listesi güncelleştirilmiş olabilir, ancak paylaşımın oluşturulmasının tamamlanmasını beklemeniz gerekir.

2. Önceki adımda yer alan tüm adımları tekrarlayarak ve **Edge Yerel paylaşma olarak Yapılandır** onay kutusunu seçerek Edge cihazında Edge Yerel bir paylaşımından bir sınır ekleyin. Yerel paylaşımdaki veriler cihazda kalır.

    ![Edge Yerel paylaşma ekleme](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. Güncelleştirilmiş paylaşımların listesini görmek için **Paylaşım Ekle** ' yi seçin.

    ![Güncelleştirilmiş paylaşım listesi](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modül Ekle

Özel veya önceden oluşturulmuş bir modül ekleyebilirsiniz. Bu Edge cihazında özel modül yok. Özel bir modül oluşturmayı öğrenmek için [Azure Stack Edge Pro cihazınız Için C# modülü geliştirme](./azure-stack-edge-gpu-create-iot-edge-module.md)bölümüne gidin.

Bu bölümde, [Azure Stack Edge Pro Için C# modülü geliştirme](./azure-stack-edge-gpu-create-iot-edge-module.md)bölümünde oluşturduğunuz IoT Edge cihazına özel bir modül eklersiniz. Bu özel modül, uç cihazdaki bir uç yerel paylaşımından dosya alır ve bunları cihazdaki bir kenar (bulut) paylaşımıyla taşımalıdır. Ardından bulut paylaşımından, dosyaları bulut paylaşımıyla ilişkili Azure depolama hesabına iter.

1. **Edge compute >** başlayın ' a gidin. **Modül Ekle** kutucuğunda senaryo türünü **basit** olarak seçin. **Add (Ekle)** seçeneğini belirleyin.
2. **Modül yapılandırma ve ekleme** dikey penceresinde aşağıdaki değerleri girin:

    
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

    ![Modül ekleme ve yapılandırma](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. **Add (Ekle)** seçeneğini belirleyin. Modül eklendi. **Genel bakış** sayfasına gidin. **Modüller** , modülün dağıtıldığını belirtecek şekilde güncelleştirilir. 

    ![Modül dağıtıldı](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Veri dönüştürme işlemini doğrulama ve verileri aktarma

Son adım modülün bağlı olduğundan ve beklendiği gibi çalıştığından emin olmak. Modülün çalışma zamanı durumu, IoT Hub kaynağında IoT Edge cihazınız için çalışıyor olmalıdır.

Modülün çalıştığını doğrulamak için aşağıdakileri yapın:

1. **Modül Ekle** kutucuğunu seçin. Bu sizi **modüller** dikey penceresine götürür. Modüller listesinde, dağıttığınız modülü ayırt edin. Eklediğiniz modülün çalışma zamanı durumu *çalışıyor* olmalıdır.

    ![Dağıtılan modülü görüntüle](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. Dosya Gezgini 'nde, daha önce oluşturduğunuz uç yerel ve kenar paylaşımlarına bağlanın.

    ![Veri dönüşümünü doğrulama-1](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Yerel paylaşıma veri ekleyin.

    ![Veri dönüştürmeyi doğrulama-2](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   Veriler bulut paylaşımına taşınır.

    ![Veri dönüştürmeyi doğrula-3](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   Veriler daha sonra bulut paylaşımından depolama hesabına gönderilir. Verileri görüntülemek için Depolama Gezgini kullanabilirsiniz.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
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
> [Azure Stack Edge Pro 'Yu yönetmek için yerel Web Kullanıcı arabirimini kullanma](azure-stack-edge-manage-access-power-connectivity-mode.md)