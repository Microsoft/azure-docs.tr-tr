---
title: Azure portal 'de Azure Stack Edge Pro R cihazının sertifikalarını yapılandırma öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro R dağıtma öğreticisi, fiziksel cihazınızda sertifika yapılandırmanızı sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 99a0f93b73e3aa3d1cb031301150ec528870ab38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059981"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Öğretici: Azure Stack Edge Pro R 'niz için sertifikaları yapılandırma

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Pro R cihazınız için sertifikaları nasıl yapılandırabileceğiniz açıklanmaktadır.

Bu adım için geçen süre, seçtiğiniz belirli seçeneğe ve sertifika akışının ortamınızda nasıl Kuruladığına bağlı olarak değişebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel cihaz için sertifikaları yapılandırma
> * VPN’i yapılandırma
> * Bekleyen şifreleme yapılandırma

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro R cihazınızı yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md)' de ayrıntılı olarak yüklediniz.
* Kendi sertifikalarınızı getirmeyi planlıyorsanız:
    - İmza Zinciri sertifikası da dahil olmak üzere, sertifikalarınızın uygun biçimde hazırlanmalıdır. Sertifika hakkındaki ayrıntılar için, [sertifikaları Yönet](azure-stack-edge-gpu-manage-certificates.md) ' e gidin



## <a name="configure-certificates-for-device"></a>Cihaz için sertifikaları yapılandırma

1. **Sertifikalar** sayfasında, sertifikalarınızı yapılandıracaksınız. **Cihaz sayfasında cihaz** adını veya DNS etki alanını değiştirdiğinize bağlı olarak, sertifikalarınız için aşağıdaki seçeneklerden birini belirleyebilirsiniz.

    - Önceki adımda cihaz adını veya DNS etki alanını değiştirmediyseniz, bu adımı atlayabilir ve sonraki adıma geçebilirsiniz. Cihaz, ile başlamak için otomatik olarak imzalanan sertifikalar oluşturdu. 

    - Cihaz adı veya DNS etki alanını değiştirdiyseniz, sertifikaların durumunun **geçerli değil** olarak gösterileceğini görürsünüz. 

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Durumun ayrıntılarını görüntülemek için bir sertifika seçin.

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Bunun nedeni, sertifikaların güncelleştirilmiş cihaz adı ve DNS etki alanını (konu adı ve konu diğer bölümünde kullanılan) yansıtmamaktadır. Cihazınızı başarıyla etkinleştirmek için, kendi imzalı uç nokta sertifikalarınızı ve ilgili imzalama zincirlerinizi getirebilirsiniz. Önce imzalama zincirini ekleyin ve ardından uç nokta sertifikalarını karşıya yükleyin. Daha fazla bilgi için [Azure Stack Edge Pro R cihazınızda kendi sertifikalarınızı getirme](#bring-your-own-certificates)bölümüne gidin.

    - Cihaz adı veya DNS etki alanını değiştirdiyseniz ve kendi sertifikalarınızı getirmediyseniz **etkinleştirme engellenir**.

    
#### <a name="bring-your-own-certificates"></a>Kendi sertifikalarınızı getirme

İmzalama zinciri dahil kendi sertifikalarınızı eklemek için bu adımları izleyin.

1. Sertifikayı karşıya yüklemek için **sertifika** sayfasında **+ sertifika ekle**' yi seçin.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Önce imzalama zincirini karşıya yükleyin ve **& Ekle**' yi seçin.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Artık diğer sertifikaları karşıya yükleyebilirsiniz. Örneğin, Azure Resource Manager ve BLOB depolama uç noktası sertifikalarını karşıya yükleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Yerel Web Kullanıcı arabirimi sertifikasını da karşıya yükleyebilirsiniz. Bu sertifikayı karşıya yükledikten sonra tarayıcınızı başlatmanız ve Önbelleği temizlemeniz gerekir. Daha sonra cihazın yerel Web Kullanıcı arabirimine bağlanmanız gerekir.  

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Düğüm sertifikasını da karşıya yükleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Son olarak, VPN sertifikasını karşıya yükleyebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Dilediğiniz zaman bir sertifika seçebilir ve bunların karşıya yüklediğiniz sertifikayla eşleştiğinden emin olmak için ayrıntıları görüntüleyebilirsiniz.

    Sertifika sayfasının yeni eklenen sertifikaları yansıtacak şekilde güncelleştirilmesi gerekir.

    ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Azure genel bulutu haricinde, imzalama zinciri sertifikalarının tüm bulut yapılandırmalarında (Azure Kamu veya Azure Stack) etkinleştirilmesinden önce getirilmesi gerekir.

1. Başlamak **için< geri 'yi** seçin.

## <a name="configure-vpn"></a>VPN’i yapılandırma

1. **Güvenlik** KUTUCUĞUNDA VPN için **Yapılandır** ' ı seçin.

    ![Yerel Web Kullanıcı arabirimi "VPN" sayfası](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    VPN 'yi yapılandırmak için öncelikle Azure 'da gerekli tüm yapılandırmanın yapıldığından emin olmanız gerekir. Ayrıntılar için bkz. [önkoşulları yapılandırma](azure-stack-edge-placeholder.md) ve [VPN Için Azure kaynaklarını yapılandırma](azure-stack-edge-placeholder.md). Bu işlemi tamamladıktan sonra, yerel kullanıcı arabiriminde yapılandırmayı yapabilirsiniz.
    
    1. VPN sayfasında **Yapılandır**' ı seçin.
    2. **VPN Yapılandır** dikey penceresinde:

    - **VPN ayarlarını** etkinleştirin.
    - **VPN paylaşılan gizliliğini** sağlayın. Bu, Azure VPN bağlantı nesnesini oluştururken verdiğiniz paylaşılan anahtardır.
    - **VPN ağ GEÇIDI IP** adresini sağlayın. Bu, Azure yerel ağ geçidi IP adresidir.
    - **PFS Grubu** için **hiçbiri**' ni seçin. 
    - **DH grubu** için **grup2**' yi seçin.
    - **IPSec bütünlüğü yöntemi** için **SHA256** öğesini seçin.
    - **Ipseccipher dönüştürme sabitleri** için **GCMAES256** öğesini seçin.
    - **IPSec kimlik doğrulaması dönüştürme sabitleri** için **GCMAES256** öğesini seçin.
    - **Ike şifreleme yöntemi** için **AES256** öğesini seçin.
    - **Uygula**’yı seçin.

        ![Yerel Kullanıcı arabirimini yapılandırma 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. VPN yolu yapılandırma dosyasını karşıya yüklemek için **karşıya yükle**' yi seçin. 
    
        ![Yerel Kullanıcı arabirimini yapılandırma 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Önceki adımda yerel sisteminizde indirdiğiniz VPN yapılandırması *JSON* dosyasına gidin.
        - Cihaz, sanal ağ ve ağ geçitleri ile ilişkili Azure bölgesi olarak bölgeyi seçin.
        - **Uygula**’yı seçin.
    
            ![Yerel Kullanıcı arabirimini yapılandırma 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. İstemciye özgü yollar eklemek için IP adresi aralıklarını yalnızca VPN kullanarak erişilecek şekilde yapılandırın. 
    
        - **Yalnızca VPN kullanılarak erişilecek IP adresi aralıkları** altında **Yapılandır**' ı seçin.
        - Geçerli bir IPv4 aralığı sağlayın ve **Ekle**' yi seçin. Diğer aralıkları eklemek için adımları tekrarlayın.
        - **Uygula**’yı seçin.
    
            ![Yerel Kullanıcı arabirimi 5 ' i yapılandırma](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Başlamak **için< geri 'yi** seçin.

## <a name="configure-encryption-at-rest"></a>Bekleyen şifreleme yapılandırma

1. **Güvenlik** kutucuğunda, bekleyen şifreleme için **Yapılandır** ' ı seçin. Bu gerekli bir ayardır ve bu, başarılı bir şekilde yapılandırılana kadar cihazı etkinleştiremezsiniz. 

    Fabrika sırasında, cihazlar yansıma alındıktan sonra birim düzeyi BitLocker şifrelemesi etkinleştirilir. Cihazı aldıktan sonra, Rest şifrelemesini yapılandırmanız gerekir. Depolama havuzu ve birimleri yeniden oluşturulur ve REST 'ten şifrelemeyi etkinleştirmek için BitLocker anahtarları sağlayabilir ve bu sayede bekleyen verileriniz için ikinci bir şifreleme katmanı oluşturabilirsiniz.

1. **Bekleyen şifreleme** bölmesinde, bir 32 karakter uzunluğunda bir key-64 kodlamalı anahtar sağlayın. Bu tek seferlik bir yapılandırmadır ve bu anahtar, gerçek şifreleme anahtarını korumak için kullanılır. Bu anahtarı otomatik olarak oluşturmayı veya bir tane girmeyi seçebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "bekleyen şifreleme" bölmesi](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    Anahtar, cihaz etkinleştirildikten sonra **bulut ayrıntıları** sayfasındaki bir anahtar dosyasına kaydedilir.

1. **Uygula**’yı seçin. Bu işlem birkaç dakika sürer ve işlemin durumu **güvenlik** kutucuğunda görüntülenir.

    ![Yerel Web Kullanıcı arabirimi "bekleyen şifreleme" sayfası](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Durum **tamamlandı** olarak görüntülendikten sonra, başlamak **için< geri**' yi seçin.

Cihazınız artık etkinleştirilmeye hazırdır. 


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Fiziksel cihaz için sertifikaları yapılandırma
> * VPN’i yapılandırma
> * Bekleyen şifreleme yapılandırma

Azure Stack Edge Pro R cihazınızı nasıl etkinleştireceğinizi öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R cihazını etkinleştirin](./azure-stack-edge-pro-r-deploy-activate.md)
