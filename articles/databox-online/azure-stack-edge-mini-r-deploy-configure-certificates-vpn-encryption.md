---
title: Azure portal 'de Azure Stack Edge Mini R cihazının sertifikalarını yapılandırma öğreticisi | Microsoft Docs
description: Azure Stack Edge Mini 'i dağıtma öğreticisi, fiziksel cihazınızda sertifika yapılandırmanızı sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 42863c942314d3cb76c7536edc79b08e132f8fa2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063007"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Öğretici: Azure Stack Edge Mini R 'niz için sertifikaları, VPN, şifrelemeyi yapılandırma

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Mini R cihazınız için sertifikaları, VPN ve gerideki şifrelemeyi nasıl yapılandırabileceğinizi açıklanmaktadır.

Bu adım için geçen süre, seçtiğiniz belirli seçeneğe ve sertifika akışının ortamınızda nasıl Kuruladığına bağlı olarak değişebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel cihaz için sertifikaları yapılandırma
> * VPN’i yapılandırma
> * Bekleyen şifrelemeyi yapılandırma

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Mini R cihazınızı yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md)' de ayrıntılı olarak yüklediniz.

* Kendi sertifikalarınızı getirmeyi planlıyorsanız:
    - İmza Zinciri sertifikası da dahil olmak üzere, sertifikalarınızın uygun biçimde hazırlanmalıdır. Sertifika hakkındaki ayrıntılar için, [sertifikaları Yönet](azure-stack-edge-gpu-manage-certificates.md) ' e gidin

    - Cihazınız Azure Kamu veya Azure Kamu Gizli Bilgi ya da Azure Kamu üst gizli bulutuna dağıtılmışsa, cihazınızı etkinleştirebilmeniz için bir imzalama zinciri sertifikası gerekir. 
    Sertifika hakkındaki ayrıntılar için [sertifikaları Yönet](azure-stack-edge-gpu-manage-certificates.md)' e gidin.


## <a name="configure-certificates-for-device"></a>Cihaz için sertifikaları yapılandırma

1. **Sertifikalar** sayfasında, sertifikalarınızı yapılandıracaksınız. **Cihaz sayfasında cihaz** adını veya DNS etki alanını değiştirdiğinize bağlı olarak, sertifikalarınız için aşağıdaki seçeneklerden birini belirleyebilirsiniz.

    - Önceki adımda varsayılan cihaz adını veya varsayılan DNS etki alanını değiştirmediyseniz ve kendi sertifikalarınızı getirmek istemiyorsanız, bu adımı atlayabilir ve sonraki adıma geçebilirsiniz. Cihaz, ile başlamak için otomatik olarak imzalanan sertifikalar oluşturdu. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Cihaz adı veya DNS etki alanını değiştirdiyseniz, sertifikaların durumunun **geçerli değil** olarak gösterileceğini görürsünüz. 

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Durumun ayrıntılarını görüntülemek için bir sertifika seçin.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        Sertifikalar güncelleştirilmiş cihaz adı ve DNS etki alanını (konu adı ve konu alternatifi bölümünde kullanılan) yansıtmadığından sertifika durumu **geçerli değil** . Cihazınızı başarıyla etkinleştirmek için, kendi imzalı uç nokta sertifikalarınızı ve ilgili imzalama zincirlerinizi getirebilirsiniz. Önce imzalama zincirini ekleyin ve ardından uç nokta sertifikalarını karşıya yükleyin. Daha fazla bilgi için [Azure Stack Edge Mini cihazınızda kendi sertifikalarınızı getirme](#bring-your-own-certificates)bölümüne gidin.


    - Cihaz adı veya DNS etki alanını değiştirdiyseniz ve kendi sertifikalarınızı getirmediyseniz **etkinleştirme engellenir**.


#### <a name="bring-your-own-certificates"></a>Kendi sertifikalarınızı getirme

İmzalama zincirini bu cihazda daha önceki bir adımla zaten eklediniz. Artık uç nokta sertifikaları, düğüm sertifikası, yerel kullanıcı arabirimi sertifikası ve VPN sertifikası yükleyebilirsiniz. Kendi sertifikalarınızı eklemek için aşağıdaki adımları izleyin.

1. Sertifikayı karşıya yüklemek için **sertifika** sayfasında **+ sertifika ekle**' yi seçin.

    [![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Diğer sertifikaları karşıya yükleyebilirsiniz. Örneğin, Azure Resource Manager ve BLOB depolama uç noktası sertifikalarını karşıya yükleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Yerel Web Kullanıcı arabirimi sertifikasını da karşıya yükleyebilirsiniz. Bu sertifikayı karşıya yükledikten sonra tarayıcınızı başlatmanız ve Önbelleği temizlemeniz gerekir. Daha sonra cihazın yerel Web Kullanıcı arabirimine bağlanmanız gerekir.  

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Düğüm sertifikasını da karşıya yükleyebilirsiniz.


    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Son olarak, VPN sertifikasını karşıya yükleyebilirsiniz.
        
    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfası](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. Dilediğiniz zaman bir sertifika seçebilir ve bunların karşıya yüklediğiniz sertifikayla eşleştiğinden emin olmak için ayrıntıları görüntüleyebilirsiniz.

    [![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    Sertifika sayfasının yeni eklenen sertifikaları yansıtacak şekilde güncelleştirilmesi gerekir.

    [![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Azure genel bulutu haricinde, imzalama zinciri sertifikalarının tüm bulut yapılandırmalarında (Azure Kamu veya Azure Stack hub) etkinleştirilmesinden önce getirilmesi gerekir.


## <a name="configure-vpn"></a>VPN’i yapılandırma

1. **Güvenlik** KUTUCUĞUNDA VPN için **Yapılandır** ' ı seçin. 

    VPN 'yi yapılandırmak için öncelikle Azure 'da gerekli tüm yapılandırmanın yapıldığından emin olmanız gerekir. Ayrıntılar için bkz. [Azure Stack Edge Mini R cihazınız Için PowerShell aracılığıyla VPN yapılandırma](azure-stack-edge-placeholder.md). Bu işlemi tamamladıktan sonra, yerel kullanıcı arabiriminde yapılandırmayı yapabilirsiniz.
    
    1. VPN sayfasında **Yapılandır**' ı seçin.
        ![VPN yerel kullanıcı arabirimini yapılandırma 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. **VPN Yapılandır** dikey penceresinde:

        1. Telefon rehberini giriş olarak girin.
        2. Azure veri merkezi IP aralığı JSON dosyasını girdi olarak sağlayın. Bu dosyayı şuradan indir: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
        3. Bölge olarak **eastus** öğesini seçin.
        4. **Uygula**’yı seçin.

        ![VPN yerel kullanıcı arabirimini yapılandırma 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. IP adresi aralıklarını yalnızca VPN kullanarak erişilecek şekilde yapılandırın. 
    
        - **Yalnızca VPN kullanılarak erişilecek IP adresi aralıkları** altında **Yapılandır**' ı seçin.
        - Azure sanal ağınız için seçtiğiniz VNET IPv4 aralığını girin.
        - **Uygula**’yı seçin.
    
        ![VPN yerel ARABIRIMINI yapılandırma 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Cihazınız artık şifreli olmaya hazırdır. Bekleyen şifrelemeyi yapılandırma.


## <a name="enable-encryption"></a>Şifrelemeyi etkinleştir

1. **Güvenlik** kutucuğunda, bekleyen şifreleme için **Yapılandır** ' ı seçin. Bu gerekli bir ayardır ve bu, başarılı bir şekilde yapılandırılana kadar cihazı etkinleştiremezsiniz. 

    ![Yerel Web Kullanıcı arabirimi "bekleyen şifreleme" sayfa 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    Fabrika sırasında, cihazlar yansıma alındıktan sonra birim düzeyi BitLocker şifrelemesi etkinleştirilir. Cihazı aldıktan sonra, Rest şifrelemesini yapılandırmanız gerekir. Depolama havuzu ve birimleri yeniden oluşturulur ve REST 'ten şifrelemeyi etkinleştirmek için BitLocker anahtarları sağlayabilir ve bu sayede bekleyen verileriniz için ikinci bir şifreleme katmanı oluşturabilirsiniz.

1. **Bekleyen şifreleme** bölmesinde, 32 karakter uzunluğunda bir Long (AES-256 bit) Base-64 kodlamalı anahtar girin. Bu tek seferlik bir yapılandırmadır ve bu anahtar, gerçek şifreleme anahtarını korumak için kullanılır. 

    ![Yerel Web Kullanıcı arabirimi "bekleyen şifreleme" sayfa 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Bu anahtarı otomatik olarak oluşturmayı da tercih edebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "bekleyen şifreleme" sayfa 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. **Uygula**’yı seçin. Bu işlem birkaç dakika sürer ve işlemin durumu **güvenlik** kutucuğunda görüntülenir.

    ![Yerel Web Kullanıcı arabirimi "bekleyen şifreleme" sayfa 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Durum **tamamlandı** olarak görüntülendikten sonra, **kullanmaya** başlamak için geri dönün.

Cihazınız artık etkinleştirilmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel cihaz için sertifikaları yapılandırma
> * VPN’i yapılandırma
> * Bekleyen şifrelemeyi yapılandırma

Azure Stack Edge Mini R cihazınızı nasıl etkinleştireceğinizi öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Mini R cihazını etkinleştirin](./azure-stack-edge-mini-r-deploy-activate.md)
