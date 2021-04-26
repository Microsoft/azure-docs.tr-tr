---
title: Azure Blob depolama ile lider Yönetimi-Microsoft ticari Market
description: Azure Blob 'u kullanarak Microsoft AppSource ve Azure Marketi için müşteri adaylarını nasıl yapılandıracağınızı öğrenin
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491112"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Ticari Market müşteri adaylarını yönetmek için Azure Blob depolamayı kullanma

>[!Caution]
>Azure Blob depolama için ticari Market desteği kullanımdan kaldırılmıştır ve artık teklifinizden gelen müşteri adaylarını işleme seçeneği yoktur. Şu anda Azure Blob için yapılandırılmış lider yönetimi ile ticari bir market teklifiniz varsa müşteri adaylarını artık almayacaksınız. Lütfen müşteri adayı yönetim yapılandırmanızı diğer lider yönetimi seçeneklerinden birine güncelleştirin. [Müşteri adayı yönetimi giriş sayfasında](./commercial-marketplace-get-customer-leads.md)diğer seçenekler hakkında bilgi edinin. "

 Müşteri Ilişkileri yönetimi (CRM) sisteminiz, Microsoft AppSource ve Azure Market 'te müşteri adaylarını almak üzere Iş Ortağı Merkezi 'nde açıkça desteklenmiyorsa Azure Blob depolamayı kullanabilirsiniz. Sonra verileri dışarı aktarıp CRM sisteminize aktarabilirsiniz. Bu makaledeki yönergeler, Azure depolama hesabı oluşturma ve bu hesap altında bir blob oluşturma işlemi boyunca size kılavuzluk eder. Ayrıca, teklifiniz bir müşteri adayı aldığında e-posta bildirimi göndermek için Power otomatikleştirir kullanarak yeni bir akış oluşturabilirsiniz.

>[!NOTE]
>Bu yönergelerde kullanılan güç otomatikleştirme Bağlayıcısı, Power otomatikleştirmek için ücretli bir abonelik gerektirir. Bu makaledeki yönergeleri izlemeden önce bunun için hesap yaptığınızdan emin olun.

## <a name="configure-azure-blob-storage"></a>Azure Blob depolamayı yapılandırma

1. Azure hesabınız yoksa [ücretsiz bir deneme hesabı oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).

2. Azure hesabınız etkin olduktan sonra [Azure Portal](https://portal.azure.com)oturum açın.

3. Azure portal, aşağıdaki yordamı kullanarak bir depolama hesabı oluşturun.  
    1. Sol menü çubuğunda **+ kaynak oluştur** ' u seçin.  **Yeni** bölme (dikey pencere) sağa görüntülenecektir.
    2. **Yeni** bölmesinde **depolama** ' yı seçin.  **Öne çıkan** bir liste sağ tarafta görüntülenir.
    3. Hesap oluşturmaya başlamak için **Depolama hesabını** seçin.  [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md?tabs=azure-portal)makalesindeki yönergeleri izleyin.

    ![Azure depolama hesabı oluşturma adımları](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Depolama hesapları hakkında daha fazla bilgi için bu [hızlı](../../storage/blobs/storage-quickstart-blobs-portal.md)başlangıca bakın.  Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

4. Depolama Hesabınız sağlanana kadar bekleyin, genellikle birkaç dakika sürer.  Ardından, **Tüm kaynaklarınızı gör** ' i  seçerek veya Azure Portal sol gezinti menü çubuğundan **tüm kaynakları** seçerek depolama hesabınıza Azure Portal.

    ![Azure depolama hesabınıza erişin](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Depolama hesabı bölmesinizden **erişim anahtarları** ' nı seçin ve anahtar için *bağlantı dizesi* değerini kopyalayın. Bu değeri, Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *depolama hesabı bağlantı dizesi* değeridir.

     Bağlantı dizesine bir örnek:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    !["Bağlantı dizesi" metin kutusu vurgulanmış "erişim tuşları" sayfasını gösteren ekran görüntüsü.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Depolama hesabı sayfasından **Bloblar**' ı seçin.

   ![Blob 'ların seçildiği Azure depolama hesabı sayfasının ekran görüntüsü](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Bloblar sayfasında, **+ Container** düğmesini seçin.

8. Yeni Kapsayıcınız için bir **ad** yazın. Kapsayıcı adı küçük harflerden oluşmalı ve bir harf veya rakamla başlamalıdır; yalnızca harf, rakam ve tire (-) karakterini içerebilir. Kapsayıcı ve BLOB adları hakkında daha fazla bilgi için bkz. [kapsayıcıları, Blobları ve meta verileri adlandırma ve başvuru](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Bu değeri, Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *kapsayıcı adı* değeri olduğundan kaydedin.

9. Kapsayıcıya genel erişim düzeyini **özel (anonim erişim yok)** olarak ayarlayın.

10. Kapsayıcıyı oluşturmak için **Tamam**'ı seçin.

    ![Yeni kapsayıcı](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Teklifinizi Azure Blob depolamaya göndermek için teklifinizi yapılandırma

Yayımlama portalında teklifiniz için müşteri adayı yönetim bilgilerini yapılandırmaya hazırsanız, aşağıdaki adımları izleyin:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
2. **Müşteri müşteri adayları** bölümünde **Bağlan**' ı seçin.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Müşteri liderleri":::

3. Bağlantı ayrıntıları açılır penceresinde, müşteri adayı hedefi için **Azure Blob** ' u seçin.

    ![Bağlantı ayrıntısı](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Bu yönergeleri izleyerek aldığınız **kapsayıcı adı** ve **depolama hesabı bağlantı dizesini** belirtin.

    * Kapsayıcı adı örneği: `marketplaceleadcontainer`
    * Depolama hesabı bağlantı dizesi örneği: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ bağlantı ayrıntısı](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **Kaydet**’i seçin.

    > [!NOTE]
    > Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.