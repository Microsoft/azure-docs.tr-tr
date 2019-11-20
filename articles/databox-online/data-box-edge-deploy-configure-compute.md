---
title: Azure Data Box Edge işlem ile verileri filtreleme, analiz etme öğreticisi | Microsoft Docs
description: Data Box Edge'deki işlem rolünü yapılandırmayı ve verileri Azure'a göndermeden önce dönüştürmek için kullanmayı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277254"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Öğretici: Azure Data Box Edge ile veri dönüştürme

Bu öğreticide, Azure Data Box Edge cihazınızda bir işlem rolünün nasıl yapılandırılacağı açıklanmaktadır. İşlem rolünü yapılandırdıktan sonra Data Box Edge, verileri Azure 'a göndermeden önce dönüştürebilir.

Bu yordamın tamamlanması yaklaşık 10 ila 15 dakika sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İşlemi yapılandırma
> * Paylaşımlar Ekle
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

 
## <a name="prerequisites"></a>Önkoşullar

Data Box Edge cihazınızda bir işlem rolü ayarlamadan önce şunları yaptığınızdan emin olun:

- Data Box Edge cihazınızı [, bağlanma, ayarlama ve etkinleştirme Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)açıklandığı şekilde etkinleştirdiniz.


## <a name="configure-compute"></a>İşlemi yapılandırma

Data Box Edge işlem yapılandırmak için bir IoT Hub kaynağı oluşturacaksınız.

1. Data Box Edge kaynağınızın Azure portal, genel bakış ' a gidin. Sağ bölmede, **işlem** kutucuğunda **başlayın**' ı seçin.

    ![İşlem ile çalışmaya başlama](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. **Uç Işlem yapılandırma** kutucuğunda, **işlem Yapılandır**' ı seçin.
3. **Uç Işlem yapılandırma** dikey penceresinde aşağıdakileri girin:

   
    |Alan  |Value  |
    |---------|---------|
    |IoT Hub     | **Yeni** veya **mevcut**seçeneklerinden birini belirleyin. <br> Varsayılan olarak, bir IoT kaynağı oluşturmak için standart bir katman (S1) kullanılır. Ücretsiz bir IoT kaynağı kullanmak için, bir tane oluşturun ve ardından mevcut kaynağı seçin. <br> Her durumda IoT Hub kaynak, Data Box Edge kaynak tarafından kullanılan aynı abonelik ve kaynak grubunu kullanır.     |
    |Name     |IoT Hub kaynağınız için bir ad girin.         |

    ![İşlem ile çalışmaya başlama](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. **Oluştur**’u seçin. IoT Hub kaynak oluşturma birkaç dakika sürer. IoT Hub kaynağı oluşturulduktan sonra, işlem yapılandırmasını göstermek için işlem kutucuğunu **Yapılandır** ' ı güncelleştirir. Edge işlem rolünün yapılandırıldığını doğrulamak için, **Işlem yapılandırma** kutucuğunda işlemi **görüntüle** ' yi seçin.
    
    ![İşlem ile çalışmaya başlama](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > IoT Hub Data Box Edge cihazla ilişkilendirilmeden önce **Işlem yapılandırma** iletişim kutusu kapatılırsa IoT Hub oluşturulur ancak işlem yapılandırmasında gösterilmez. 
    
    Edge cihazında Edge hesaplama rolü ayarlandığında, iki cihaz oluşturur: bir IoT cihazı ve bir IoT Edge cihaz. Her iki cihaz de IoT Hub kaynağında görüntülenebilir. Bu IoT Edge cihazında aynı zamanda bir IoT Edge çalışma zamanı çalışıyor. Bu noktada, IoT Edge cihazınız için yalnızca Linux platformu kullanılabilir.


## <a name="add-shares"></a>Paylaşımlar Ekle

Bu öğreticide basit dağıtım için iki paylaşım gerekir: bir kenar paylaşımı ve başka bir kenar yerel paylaşımı.

1. Aşağıdaki adımları uygulayarak cihaza bir Edge paylaşma ekleyin:

    1. Data Box Edge kaynağınız, **uç işlem >** başlayın ' a gidin.
    2. **Paylaşma Ekle** kutucuğunda **Ekle**' yi seçin.
    3. **Paylaşma Ekle** dikey penceresinde, paylaşma adını girip paylaşma türünü seçin.
    4. Edge payını bağlamak için, **kenar ile paylaşma kullanımını kullanın**onay kutusunu seçin.
    5. Mevcut bir kullanıcı olan **Depolama hesabını**, **Depolama hizmetini**seçin ve ardından **Oluştur**' u seçin.

        ![Kenar paylaşma ekleme](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Yerel bir NFS paylaşma oluşturduysanız, dosyaları paylaşıma kopyalamak için aşağıdaki uzaktan eşitleme (rsync) komut seçeneğini kullanın:

    `rsync <source file path> < destination file path>`

    Rsync komutu hakkında daha fazla bilgi için, [rsync belgelerine](https://www.computerhope.com/unix/rsync.htm)gidin.

    Kenar paylaşma oluşturulur ve başarılı bir oluşturma bildirimi alırsınız. Paylaşma listesi güncelleştirilmiş olabilir, ancak paylaşımın oluşturulmasının tamamlanmasını beklemeniz gerekir.

2. Önceki adımda yer alan tüm adımları tekrarlayarak ve **Edge Yerel paylaşma olarak Yapılandır**onay kutusunu seçerek Edge cihazında Edge Yerel bir paylaşımından bir sınır ekleyin. Yerel paylaşımdaki veriler cihazda kalır.

    ![Edge Yerel paylaşma ekleme](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Güncelleştirilmiş paylaşımların listesini görmek için **Paylaşım Ekle** ' yi seçin.

    ![Güncelleştirilmiş paylaşım listesi](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modül Ekle

Özel veya önceden oluşturulmuş bir modül ekleyebilirsiniz. Bu Edge cihazında özel modül yok. Özel bir modül oluşturmayı öğrenmek için [Data Box Edge cihazınız için C# modül geliştirme](data-box-edge-create-iot-edge-module.md)bölümüne gidin.

Bu bölümde, [Data Box Edge için C# modül geliştirme](data-box-edge-create-iot-edge-module.md)bölümünde oluşturduğunuz IoT Edge cihazına özel bir modül eklersiniz. Bu özel modül, uç cihazdaki bir uç yerel paylaşımından dosya alır ve bunları cihazdaki bir kenar (bulut) paylaşımıyla taşımalıdır. Ardından bulut paylaşımından, dosyaları bulut paylaşımıyla ilişkili Azure depolama hesabına iter.

1. **Edge compute >** başlayın ' a gidin. **Modül Ekle** kutucuğunda senaryo türünü **basit**olarak seçin. **Add (Ekle)** seçeneğini belirleyin.
2. **Modül yapılandırma ve ekleme** dikey penceresinde aşağıdaki değerleri girin:

    
    |Alan  |Değer  |
    |---------|---------|
    |Name     | Modül için benzersiz bir ad. Bu modül, Data Box Edge ilişkili IoT Edge cihaza dağıtabileceğiniz bir Docker kapsayıcısıdır.        |
    |Görüntü URI'si     | Modülün karşılık gelen kapsayıcı görüntüsü için görüntü URI 'SI.        |
    |Kimlik bilgileri gerekli     | İşaretliyse, Kullanıcı adı ve parola, eşleşen bir URL ile modülleri almak için kullanılır.        |
    |Giriş paylaşma     | Bir giriş paylaşma seçin. Edge Yerel paylaşma, bu durumda giriş paylaşımıdır. Burada kullanılan modül, dosyaları uç yerel paylaşımından buluta yüklendikleri bir kenar paylaşımıyla taşıırlar.        |
    |Çıkış payı     | Bir çıkış payı seçin. Edge paylaşma, bu durumda çıkış paylaşımıdır.        |
    |Tetikleyici türü     | **Dosya** veya **zamanlama**arasından seçim yapın. Dosya tetikleyicisi, giriş paylaşımında bir dosya gibi bir dosya olayı gerçekleştiğinde tetiklenir. Zamanlanan bir tetikleyici, sizin tarafınızdan tanımlanan bir zamanlamaya göre ateşlenir.         |
    |Tetikleyici adı     | Tetikleyiciniz için benzersiz bir ad.         |
    |Ortam değişkenleri| Modülünüzün çalışacağı ortamı tanımlamaya yardımcı olacak isteğe bağlı bilgiler.   |

    ![Modül ekleme ve yapılandırma](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. **Add (Ekle)** seçeneğini belirleyin. Modül eklendi. Modülün dağıtıldığını belirtmek için **Modül Ekle** kutucuk güncelleştirmeleri. 

    ![Modül dağıtıldı](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Veri dönüştürme işlemini doğrulama ve verileri aktarma

Son adım modülün bağlı olduğundan ve beklendiği gibi çalıştığından emin olmak. Modülün çalışma zamanı durumu, IoT Hub kaynağında IoT Edge cihazınız için çalışıyor olmalıdır.

Modülün çalıştığını doğrulamak için aşağıdakileri yapın:

1. **Modül Ekle** kutucuğunu seçin. Bu sizi **modüller** dikey penceresine götürür. Modüller listesinde, dağıttığınız modülü ayırt edin. Eklediğiniz modülün çalışma zamanı durumu *çalışıyor*olmalıdır.

    ![Veri dönüştürmeyi doğrulama](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  Dosya Gezgini 'nde, daha önce oluşturduğunuz uç yerel ve kenar paylaşımlarına bağlanın.

    ![Veri dönüştürmeyi doğrulama](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Yerel paylaşıma veri ekleyin.

    ![Veri dönüştürmeyi doğrulama](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Veriler bulut paylaşımına taşınır.

    ![Veri dönüştürmeyi doğrulama](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Veriler daha sonra bulut paylaşımından depolama hesabına gönderilir. Verileri görüntülemek için Depolama Gezgini gidin.

    ![Veri dönüştürmeyi doğrulama](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Doğrulama işlemini tamamladınız.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İşlemi yapılandırma
> * Paylaşımlar Ekle
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

Data Box Edge cihazınızı nasıl yöneteceğiniz hakkında bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Data Box Edge yönetimi için yerel web arabirimi kullanma](data-box-edge-manage-access-power-connectivity-mode.md)
