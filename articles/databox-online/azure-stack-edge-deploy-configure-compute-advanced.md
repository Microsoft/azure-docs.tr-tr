---
title: Azure Stack Edge Pro 'da işlem ile gelişmiş dağıtıma yönelik verileri filtreleme, analiz etme öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro 'da işlem rolünü yapılandırmayı ve Azure 'a göndermeden önce gelişmiş dağıtım akışına yönelik verileri dönüştürmek için kullanmayı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: 81953f573c47d229fcaccd7c11f62155acd2f119
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063653"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Öğretici: gelişmiş dağıtım akışı için Azure Stack Edge Pro ile veri dönüştürme

Bu öğreticide, Azure Stack Edge Pro cihazınızda gelişmiş bir dağıtım akışı için bir işlem rolünün nasıl yapılandırılacağı açıklanmaktadır. İşlem rolünü yapılandırdıktan sonra Azure Stack Edge Pro, verileri Azure 'a göndermeden önce dönüştürebilir.

İşlem, cihazınızda basit veya gelişmiş dağıtım akışı için yapılandırılabilir.

| Ölçütler | Basit Dağıtım                                | Gelişmiş dağıtım                   |
|------------------|--------------------------------------------------|---------------------------------------|
| İçin tasarlanan     | BT yöneticileri                                | Geliştiriciler                            |
| Tür             | Modül dağıtmak için Azure Stack Edge hizmetini kullanma      | Modül dağıtmak için IoT Hub hizmetini kullanma |
| Dağıtılan modüller | Tek                                           | Zincirleme veya birden çok modül           |


Bu yordamın tamamlanması 20 ila 30 dakika kadar sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Paylaşımlar Ekle
> * Tetikleyici ekleme
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

 
## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızda bir işlem rolü ayarlamadan önce şunları yaptığınızdan emin olun:

- Azure Stack Edge Pro cihazınızı [, Azure Stack Edge Pro 'Yu bağlama, ayarlama ve etkinleştirme](azure-stack-edge-deploy-connect-setup-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.


## <a name="configure-compute"></a>İşlem yapılandırma

Azure Stack Edge Pro ortamınızda işlem yapılandırmak için bir IoT Hub kaynağı oluşturacaksınız.

1. Azure Stack Edge kaynağınızın Azure portal **Genel Bakış ' a** gidin. Sağ bölmede **IoT Edge** kutucuğunu seçin.

    ![İşlem ile çalışmaya başlama](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. **IoT Edge hizmeti etkinleştir** kutucuğunda **Ekle**' yi seçin. Bu eylem, IoT Edge modüllerini cihazınıza yerel olarak dağıtmanıza olanak sağlayan IoT Edge hizmeti sağlar.

    ![İşlem 2 ile çalışmaya başlama](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. **IoT Edge oluştur hizmetinde**, aşağıdakileri girin:

   
    |Alan  |Değer  |
    |---------|---------|
    |Abonelik     |IoT Hub kaynağınız için bir abonelik seçin. Azure Stack Edge kaynağı tarafından kullanılan aboneliğin aynısını seçebilirsiniz.        |
    |Kaynak grubu     |IoT Hub kaynağınız için kaynak grubu için bir ad girin. Azure Stack Edge kaynağı tarafından kullanılan kaynak grubunu seçebilirsiniz.         |
    |IoT Hub     | **Yeni** veya **mevcut** seçeneklerinden birini belirleyin. <br> Varsayılan olarak IoT kaynağı oluşturulurken Standart katmanı (S1) kullanılır. Bir ücretsiz katman IoT kaynağı kullanmak için kaynağı oluşturun ve sonra da mevcut kaynağı seçin.      |
    |Name     |Varsayılan adı kabul edin veya IoT Hub kaynağınız için bir ad girin.         |

    ![İşlem 3 ' ü kullanmaya başlama](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. **Gözden geçir + Oluştur**’u seçin. IoT Hub kaynak oluşturma birkaç dakika sürer. IoT Hub kaynağı oluşturulduktan sonra, **genel bakış** , IoT Edge hizmetinin çalıştığını göstermek için güncelleştirilir. 

    Sınır cihazında IoT Edge hizmeti yapılandırıldığında, iki cihaz oluşturur: bir IoT cihazı ve bir IoT Edge cihaz. Her iki cihaz de IoT Hub kaynağında görüntülenebilir. Bu IoT Edge cihazında aynı zamanda bir IoT Edge çalışma zamanı çalışıyor. Bu noktada, IoT Edge cihazınız için yalnızca Linux platformu kullanılabilir.

    Edge işlem rolünün yapılandırıldığını onaylamak için **IoT Edge hizmeti > Özellikler** ' i seçin ve IoT cihazını ve IoT Edge cihazını görüntüleyin. 

    ![İşlem 4 ' ü kullanmaya başlama](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>Paylaşımlar Ekle

Bu öğreticide gelişmiş dağıtım için iki paylaşım gerekir: bir kenar paylaşımı ve başka bir kenar yerel paylaşımı.

1. Aşağıdaki adımları uygulayarak cihaza bir Edge paylaşma ekleyin:

    1. Azure Stack Edge kaynağınız **IoT Edge > paylaşımlarına** gidin.
    2. **Paylaşımlar** sayfasında, komut çubuğundan **+ Paylaşım Ekle**' yi seçin.
    3. **Paylaşma Ekle** dikey penceresinde, paylaşma adını girip paylaşma türünü seçin.
    4. Edge payını bağlamak için, **kenar ile paylaşma kullanımını kullanın** onay kutusunu seçin.
    5. Mevcut bir kullanıcı olan **Depolama hesabını**, **Depolama hizmetini** seçin ve ardından **Oluştur**' u seçin.

        ![Kenar paylaşma ekleme](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Kenar paylaşma oluşturulduktan sonra, başarılı bir oluşturma bildirimi alırsınız. Paylaşma listesi, yeni paylaşımın yansıtacak şekilde güncelleştirilir.

2. Önceki adımda yer alan tüm adımları tekrarlayarak ve **Edge Yerel paylaşma olarak Yapılandır** onay kutusunu seçerek Edge cihazında Edge Yerel bir paylaşımından bir sınır ekleyin. Yerel paylaşımdaki veriler cihazda kalır.

    ![Edge Yerel paylaşma ekleme](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. **Paylaşımlar** dikey penceresinde, güncelleştirilmiş paylaşımların listesini görürsünüz.

    ![Güncelleştirilmiş paylaşım listesi](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Yeni oluşturulan yerel paylaşımın özelliklerini görüntülemek için listeden paylaşma ' yı seçin. **Edge işlem modülleri Için yerel bağlama noktası** kutusunda, bu paylaşıma karşılık gelen değeri kopyalayın.

    Modülü dağıtırken bu yerel bağlama noktasını kullanacaksınız.

    !["Edge işlem modülleri için yerel bağlama noktası" kutusu](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Oluşturduğunuz kenar paylaşımının özelliklerini görüntülemek için listeden paylaşma ' yı seçin. **Edge işlem modülleri Için yerel bağlama noktası** kutusunda, bu paylaşıma karşılık gelen değeri kopyalayın.

    Modülü dağıtırken bu yerel bağlama noktasını kullanacaksınız.

    ![Özel modül ekleme](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Tetikleyici ekleme

1. Azure Stack Edge kaynağına gidip **IoT Edge > tetikleyicilerine** gidin. **+ Tetikleyici Ekle**' yi seçin.

    ![Tetikleyici ekleme](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. **Tetikleyici Ekle** dikey penceresinde aşağıdaki değerleri girin.

    |Alan  |Değer  |
    |---------|---------|
    |Tetikleyici adı     | Tetikleyiciniz için benzersiz bir ad.         |
    |Tetikleyici türü     | **Dosya** tetikleyicisi ' ni seçin. Dosya tetikleyicisi, giriş paylaşımına dosya yazılması gibi bir dosya olayı gerçekleştiğinde tetiklenir. Diğer yandan zamanlanan bir tetikleyici sizin tarafınızdan tanımlanan bir zamanlamaya göre ateşlenir. Bu örnekte, bir dosya tetikleyicisine ihtiyacımız var.    |
    |Giriş paylaşma     | Bir giriş paylaşma seçin. Edge Yerel paylaşma, bu durumda giriş paylaşımıdır. Burada kullanılan modül, dosyaları uç yerel paylaşımından buluta yüklendikleri bir kenar paylaşımıyla taşıırlar.        |

    ![Tetikleyici Ekle 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Tetikleyici oluşturulduktan sonra bilgilendirilirsiniz. Tetikleyiciler listesi, yeni oluşturulan tetikleyiciyi görüntüleyecek şekilde güncelleştirilir. Yeni oluşturduğunuz tetikleyiciyi seçin.

    ![Tetikleyici Ekle 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Örnek yolu kopyalayın ve kaydedin. Bu örnek yolu değiştirecek ve daha sonra IoT Hub kullanacaksınız.

    `"sampleroute&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Tetikleyici Ekle 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Modül Ekle

Bu Edge cihazında özel modül yok. Özel veya önceden oluşturulmuş bir modül ekleyebilirsiniz. Özel bir modül oluşturmayı öğrenmek için [Azure Stack Edge Pro cihazınız Için C# modülü geliştirme](azure-stack-edge-create-iot-edge-module.md)bölümüne gidin.

Bu bölümde, [Azure Stack Edge Pro Için C# modülü geliştirme](azure-stack-edge-create-iot-edge-module.md)bölümünde oluşturduğunuz IoT Edge cihazına özel bir modül eklersiniz. Bu özel modül, uç cihazdaki bir uç yerel paylaşımından dosya alır ve bunları cihazdaki bir kenar (bulut) paylaşımıyla taşımalıdır. Ardından bulut paylaşımından, dosyaları bulut paylaşımıyla ilişkili Azure depolama hesabına iter.

1. Azure Stack Edge kaynağına gidip **IoT Edge > genel bakış**' a gidin. **Modüller** kutucuğunda **Azure IoT Hub git**' i seçin.

    ![Gelişmiş dağıtım seçin](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. IoT Hub kaynağınız **IoT Edge cihaz** ' a gidin ve IoT Edge cihazınızı seçin.

    ![IoT Hub IoT Edge cihaza git](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. **Cihaz ayrıntıları**' nın **Modül ayarla**' yı seçin.

    ![Modül ayarla bağlantısı](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. **Modül Ekle**' nin altında aşağıdakileri yapın:

    1. Özel modülün kapsayıcı kayıt defteri ayarları için ad, adres, Kullanıcı adı ve parola girin.
    Ad, adres ve listelenen kimlik bilgileri, eşleşen bir URL 'ye sahip modülleri almak için kullanılır. Bu modülü dağıtmak için **Dağıtım modülleri** sayfasında **IoT Edge modülü**'nü seçin. Bu IoT Edge modülü, Azure Stack Edge Pro cihazınızda ilişkili IoT Edge cihazına dağıtabileceğiniz bir Docker kapsayıcısıdır.

        ![Modülleri ayarla sayfası](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. IoT Edge özel modül ayarlarını belirtin. Aşağıdaki değerleri girin.
     
        |Alan  |Değer  |
        |---------|---------|
        |Ad     | Modül için benzersiz bir ad. Bu modül, Azure Stack Edge Pro ile ilişkili IoT Edge cihazına dağıtabileceğiniz bir Docker kapsayıcısıdır.        |
        |Görüntü URI 'SI     | Modülün karşılık gelen kapsayıcı görüntüsü için görüntü URI 'SI.        |
        |Kimlik bilgileri gerekli     | İşaretliyse, Kullanıcı adı ve parola, eşleşen bir URL ile modülleri almak için kullanılır.        |
    
        **Kapsayıcı oluşturma seçenekleri** kutusunda, önceki adımlarda kenar paylaşma ve uç yerel paylaşma Için kopyaladığınız kenar modüllerinin yerel bağlama noktalarını girin.

        > [!IMPORTANT]
        > Burada kullanılan yollar, kapsayıcınıza bağlanır, bu nedenle kapsayıcınızda işlevselliğin beklediği değerlere uymalıdır. Eğer aşağıdaki [özel bir modül oluşturuyorsanız](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code), bu modülde belirtilen kod kopyalanmış yolları bekler. Bu yolları değiştirmeyin.
    
        **Kapsayıcı oluşturma seçenekleri** kutusunda, aşağıdaki örneği yapıştırabilirsiniz:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Modülünüzün kullandığı ortam değişkenlerini belirtin. Çevresel değişkenler, modülünüzün çalıştığı ortamı tanımlamaya yardımcı olacak isteğe bağlı bilgiler sağlar.

        ![Kapsayıcı oluşturma seçenekleri kutusu](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Gerekirse, gelişmiş Edge çalışma zamanı ayarlarını yapılandırın ve ardından **İleri**' ye tıklayın.

        ![Özel Modül Ekle 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. **Rotaları belirtin** altında, modüller arasında rotalar ayarlayın.  
   
   ![Yolları belirt](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    *Rotayı* , daha önce kopyaladığınız yol dizesi ile değiştirebilirsiniz. Bu örnekte, bulut paylaşımında veri alacak yerel paylaşımın adını girin. Öğesini `modulename` modülün adıyla değiştirin. **İleri**’yi seçin.
        
    ```
    "route&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Rotaları belirt bölümü](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. **Dağıtımı gözden geçir** altında tüm ayarları gözden geçirin ve ardından, modülü dağıtıma göndermek için **Gönder** ' i seçin.

   ![Modül ayarla sayfası 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Bu eylem modül dağıtımını başlatır. Dağıtım tamamlandıktan sonra, modülün **çalışma zamanı durumu** **çalışıyor** olur.

    ![Özel Modül Ekle 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Veri dönüştürmeyi doğrulama, aktarma

Son adım modülün bağlı olduğundan ve beklendiği gibi çalıştığından emin olmak. Modülün çalışma zamanı durumu, IoT Hub kaynağında IoT Edge cihazınız için çalışıyor olmalıdır.

Veri dönüştürmeyi doğrulamak ve Azure 'a aktarmak için aşağıdaki adımları uygulayın.
 
1. Dosya Gezgini 'nde, daha önce oluşturduğunuz uç yerel ve kenar paylaşımlarına bağlanın.

   ![Veri dönüştürmeyi doğrulama](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Yerel paylaşıma veri ekleyin.

   ![Veri dönüştürmeyi doğrulama 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Veriler bulut paylaşımına taşınır.

    ![Veri dönüştürmeyi doğrulama 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Veriler daha sonra bulut paylaşımından depolama hesabına gönderilir. Verileri görüntülemek için depolama hesabınıza gidin ve **Depolama Gezgini**' yi seçin. Karşıya yüklenen verileri depolama hesabınızda görüntüleyebilirsiniz.

    ![Veri dönüştürmeyi doğrulama 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Doğrulama işlemini tamamladınız.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İşlem yapılandırma
> * Paylaşımlar Ekle
> * Tetikleyici ekleme
> * İşlem modülü ekleme
> * Veri dönüştürme işlemini doğrulama ve verileri aktarma

Azure Stack Edge Pro cihazınızı yönetme hakkında bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro 'Yu yönetmek için yerel Web Kullanıcı arabirimini kullanma](azure-stack-edge-manage-access-power-connectivity-mode.md)
