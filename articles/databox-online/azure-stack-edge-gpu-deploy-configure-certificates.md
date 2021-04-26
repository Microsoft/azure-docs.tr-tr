---
title: Azure portal 'de GPU ile Azure Stack Edge Pro cihaz sertifikalarını yapılandırma öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro 'yu GPU ile dağıtma öğreticisi, fiziksel cihazınızda sertifika yapılandırmanızı sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 8b1203693b67bb1a8b9699b84dd3a437027e4c3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055323"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Öğretici: Azure Stack Edge Pro için sertifikaları GPU ile yapılandırma

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Pro cihazınıza yönelik sertifikaları, yerleşik bir GPU ile nasıl yapılandırabileceğiniz açıklanmaktadır.

Bu adım için geçen süre, seçtiğiniz belirli seçeneğe ve sertifika akışının ortamınızda nasıl Kuruladığına bağlı olarak değişebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel cihaz için sertifikaları yapılandırma

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızı GPU ile yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)' da ayrıntılı olarak yüklediniz.
* Kendi sertifikalarınızı getirmeyi planlıyorsanız:
    - İmza Zinciri sertifikası da dahil olmak üzere, sertifikalarınızın uygun biçimde hazırlanmalıdır. Sertifika hakkındaki ayrıntılar için, [sertifikaları Yönet](azure-stack-edge-gpu-manage-certificates.md) ' e gidin

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Cihaz için sertifikaları yapılandırma

1. **Sertifikalar** sayfasında, sertifikalarınızı yapılandıracaksınız. **Cihaz sayfasında cihaz** adını veya DNS etki alanını değiştirdiğinize bağlı olarak, sertifikalarınız için aşağıdaki seçeneklerden birini belirleyebilirsiniz.

    - Önceki adımda cihaz adını veya DNS etki alanını değiştirmediyseniz ve kendi sertifikalarınızı getirmek istemiyorsanız, bu adımı atlayabilir ve sonraki adıma geçebilirsiniz. Cihaz, ile başlamak için otomatik olarak imzalanan sertifikalar oluşturdu. 

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfası](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Cihaz adı veya DNS etki alanını değiştirdiyseniz, sertifikaların durumunun **geçerli değil** olarak gösterileceğini görürsünüz. 

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Durumun ayrıntılarını görüntülemek için bir sertifika seçin.

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Bunun nedeni, sertifikaların güncelleştirilmiş cihaz adı ve DNS etki alanını (konu adı ve konu diğer bölümünde kullanılan) yansıtmamaktadır. Cihazınızı başarıyla etkinleştirmek için aşağıdaki seçeneklerden birini belirleyin: 
    
        - **Tüm cihaz sertifikalarını oluştur**. Bu cihaz sertifikaları yalnızca test için kullanılmalıdır ve üretim iş yükleri ile kullanılmaz. Daha fazla bilgi için [Azure Stack Edge Pro 'unuzda cihaz sertifikaları oluşturma](#generate-device-certificates)bölümüne gidin.

        - **Kendi sertifikalarınızı getirin**. Kendi imzalı uç nokta sertifikalarınızı ve buna karşılık gelen imzalama zincirlerini getirebilirsiniz. Önce imzalama zincirini ekleyin ve ardından uç nokta sertifikalarını karşıya yükleyin. **Üretim iş yükleri için her zaman kendi sertifikalarınızı getirmenizi öneririz.** Daha fazla bilgi için [Azure Stack Edge Pro cihazınızda kendi sertifikalarınızı getirme](#bring-your-own-certificates)bölümüne gidin.
    
        - Kendi sertifikalarınızı getirip bazı cihaz sertifikaları oluşturabilirsiniz. **Sertifika oluştur** seçeneği yalnızca cihaz sertifikalarını yeniden oluşturur.

    - Cihaz adı veya DNS etki alanını değiştirdiyseniz ve sertifika oluşturmadıysanız veya kendi sertifikalarınızı getirmediyseniz **etkinleştirme engellenir**.


### <a name="generate-device-certificates"></a>Cihaz sertifikaları oluşturma

Cihaz sertifikaları oluşturmak için aşağıdaki adımları izleyin.

Azure Stack Edge Pro cihaz sertifikalarını yeniden oluşturmak ve indirmek için bu adımları kullanın:

1. Cihazınızın yerel kullanıcı arabiriminde **yapılandırma > sertifikaları**' na gidin. **Sertifika oluştur**' u seçin.

    ![Sertifika Oluştur ve indir 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. **Cihaz sertifikaları oluştur**' da **Oluştur**' u seçin. 

    ![Sertifika Oluştur ve indir 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Cihaz sertifikaları artık oluşturulup uygulanır. Sertifikaları oluşturmak ve uygulamak birkaç dakika sürer.
    
    > [!IMPORTANT]
    > Sertifika oluşturma işlemi devam ederken, kendi sertifikalarınızı getirmeyin ve **+ sertifika ekle** seçeneği aracılığıyla bunları eklemeyi deneyin.

    İşlem başarıyla tamamlandığında size bildirilir. **Olası önbellek sorunlarından kaçınmak için tarayıcınızı yeniden başlatın.**
    
    ![Sertifika Oluştur ve indir 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Sertifikalar oluşturulduktan sonra: 

    - Tüm sertifikaların durumu **geçerli** olarak gösterilir. 

        ![Sertifika Oluştur ve indir 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Belirli bir sertifika adı seçebilir ve sertifika ayrıntılarını görüntüleyebilirsiniz. 

        ![Sertifika Oluştur ve indir 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - **İndirme** sütunu artık doldurulmuştur. Bu sütunda yeniden üretilen sertifikaların indirileceği bağlantılar bulunur. 

        ![Sertifika oluşturma ve indirme 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Bir sertifika için indirme bağlantısını seçin ve istendiğinde sertifikayı kaydedin. 

    ![Sertifika Oluştur ve İndir 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. İndirmek istediğiniz tüm sertifikalar için bu işlemi tekrarlayın. 
    
    ![Sertifika Oluştur ve indir 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Cihaz tarafından oluşturulan sertifikalar aşağıdaki ad biçimiyle DER sertifikaları olarak kaydedilir: 

    `<Device name>_<Endpoint name>.cer`. Bu sertifikalar, cihazda yüklü karşılık gelen sertifikaların ortak anahtarını içerir. 

Bu sertifikaları Ao cihazında uç noktalara erişmek için kullandığınız istemci sistemine yüklemeniz gerekir. Bu sertifikalar istemciyle cihaz arasında güven kurar.

Bu sertifikaları cihaza erişmek için kullandığınız istemcide içeri ve dışarı aktarmak için, [Azure Stack Edge Pro cihazınıza erişen istemcilerde sertifikaları Içeri aktarma](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)' daki adımları izleyin. 

Azure Depolama Gezgini kullanıyorsanız, istemcinizi pek biçiminde yüklemeniz gerekir ve cihaz tarafından üretilen sertifikaları ped biçimine dönüştürmeniz gerekir. 

> [!IMPORTANT]
> - İndirme bağlantısı yalnızca cihaz tarafından oluşturulan sertifikalar için kullanılabilir ve kendi sertifikalarınızı getirdiğinizde değildir.
> - Diğer sertifika gereksinimleri karşılandığında, cihaz tarafından üretilen sertifikaların bir karışımına sahip olmak ve kendi sertifikalarınızı getirmek için karar verebilirsiniz. Daha fazla bilgi için [sertifika gereksinimleri](azure-stack-edge-gpu-certificate-requirements.md)' ne gidin.
    

### <a name="bring-your-own-certificates"></a>Kendi sertifikalarınızı getirme

İmzalama zinciri dahil kendi sertifikalarınızı eklemek için bu adımları izleyin.

1. Sertifikayı karşıya yüklemek için **sertifika** sayfasında **+ sertifika ekle**' yi seçin.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Önce imzalama zincirini karşıya yükleyin ve **& Ekle**' yi seçin.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Artık diğer sertifikaları karşıya yükleyebilirsiniz. Örneğin, Azure Resource Manager ve BLOB depolama uç noktası sertifikalarını karşıya yükleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Yerel Web Kullanıcı arabirimi sertifikasını da karşıya yükleyebilirsiniz. Bu sertifikayı karşıya yükledikten sonra tarayıcınızı başlatmanız ve Önbelleği temizlemeniz gerekir. Daha sonra cihazın yerel Web Kullanıcı arabirimine bağlanmanız gerekir.  

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Düğüm sertifikasını da karşıya yükleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Dilediğiniz zaman bir sertifika seçebilir ve bunların karşıya yüklediğiniz sertifikayla eşleştiğinden emin olmak için ayrıntıları görüntüleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    Sertifika sayfasının yeni eklenen sertifikaları yansıtacak şekilde güncelleştirilmesi gerekir.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Azure genel bulutu haricinde, imzalama zinciri sertifikalarının tüm bulut yapılandırmalarında (Azure Kamu veya Azure Stack) etkinleştirilmesinden önce getirilmesi gerekir.


Cihazınız artık etkinleştirilmeye hazırdır. Başlamak **için< geri 'yi** seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel cihaz için sertifikaları yapılandırma

Azure Stack Edge Pro cihazınızı nasıl etkinleştireceğinizi öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro cihazını etkinleştirin](./azure-stack-edge-gpu-deploy-activate.md)
