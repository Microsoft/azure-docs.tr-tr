---
title: Ticari Market programında Azure tablosu | Azure Marketi
description: Azure Blob için lider yönetimini yapılandırma
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: 5da4e0ab315b3f66a477b816f6fc5d27de7aa339
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812378"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure Blob için lider yönetimi yönergeleri

>[!Caution]
>Market teklifinizin müşteri adaylarını işleme için Azure Blob seçeneği kullanım dışı bırakılmıştır. Şu anda Azure Blob için lider yönetimi yapılandırmasıyla yayımlanmış bir teklifiniz varsa müşteri adaylarını artık almıyorsunuz. Lütfen müşteri adayı yönetim yapılandırmanızı diğer lider yönetimi seçeneklerinden birine güncelleştirin. [Müşteri adayı yönetimi giriş sayfasında](./commercial-marketplace-get-customer-leads.md)diğer seçenekler hakkında bilgi edinin. "

Müşteri Ilişkileri yönetimi (CRM) sisteminiz, Azure Marketi ve AppSource müşteri adaylarını almak üzere Iş Ortağı Merkezi 'nde açık bir şekilde desteklenmiyorsa, bu müşteri adaylarını işlemek için bir Azure Blob 'u kullanabilirsiniz. Sonra verileri dışarı aktarıp CRM sisteminize aktarabilirsiniz. Bu makaledeki yönergeler, Azure depolama hesabı oluşturma ve bu hesap altında bir Azure blobu oluşturma işlemi boyunca size kılavuzluk eder. Ayrıca, teklifiniz bir müşteri adayı aldığında e-posta bildirimi göndermek için Microsoft Flow kullanarak yeni bir akış oluşturabilirsiniz.


## <a name="how-to-configure-azure-blob"></a>Azure Blob 'u yapılandırma

1. Azure hesabınız yoksa [ücretsiz bir deneme hesabı oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).
1. Azure hesabınız etkin olduktan sonra [Azure Portal](https://portal.azure.com)oturum açın.
1. Azure portal, aşağıdaki yordamı kullanarak bir depolama hesabı oluşturun.  
    1. Sol menü çubuğunda **+ kaynak oluştur** ' u seçin.  **Yeni** bölme (dikey pencere) sağa görüntülenecektir.
    2. **Yeni** bölmesinde **depolama** ' yı seçin.  **Öne çıkan** bir liste sağ tarafta görüntülenir.
    3. Hesap oluşturmaya başlamak için **Depolama hesabını** seçin.  [Depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)makalesindeki yönergeleri izleyin.

    ![Azure depolama hesabı oluşturma adımları](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Depolama hesapları hakkında daha fazla bilgi için [Hızlı Başlangıç Öğreticisi](https://docs.microsoft.com/azure/storage/)' ni seçin.  Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

4. Depolama Hesabınız sağlanana kadar bekleyin, genellikle birkaç dakika sürer.  Ardından, **Tüm kaynaklarınızı gör** ' i seçerek veya Azure Portal sol gezinti menü çubuğundan **tüm kaynakları** seçerek depolama hesabınıza Azure Portal.

    ![Azure depolama hesabınıza erişin](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Depolama hesabı bölmesinizden **erişim anahtarları** ' nı seçin ve anahtar için *bağlantı dizesi* değerini kopyalayın. Bu değeri, Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *depolama hesabı bağlantı dizesi* değeridir.

     Bağlantı almaya bir örnek:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure depolama anahtarı](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Depolama hesabı sayfasından **Bloblar**' ı seçin.

   ![Azure depolama anahtarı](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Bloblar sayfasında, **+ Container** düğmesini seçin.

8. Yeni Kapsayıcınız için bir **ad** yazın. Kapsayıcı adı küçük harflerden oluşmalı ve bir harf veya rakamla başlamalıdır; yalnızca harf, rakam ve tire (-) karakterini içerebilir. Kapsayıcılar ve blob adları hakkında daha fazla bilgi için bkz. [Kapsayıcıları, blobları ve meta verileri adlandırma ve bunlara başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Bu değeri, Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *kapsayıcı adı* değeri olduğundan kaydedin.

9. Kapsayıcıya genel erişim düzeyini **özel (anonim erişim yok)** olarak ayarlayın.

10. Kapsayıcıyı oluşturmak için **Tamam**'ı seçin.

    ![Yeni kapsayıcı](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Teklifinizi Azure Blob 'a müşteri adayları gönderecek şekilde yapılandırma

Yayımlama portalında teklifiniz için müşteri adayı yönetim bilgilerini yapılandırmaya hazırsanız, aşağıdaki adımları izleyin:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
2. Müşteri adayı Yönetimi bölümünde **Bağlan** ' ı seçin.

    ![Teklif bağla](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Bağlantı ayrıntıları açılır penceresinde, müşteri adayı hedefi için **Azure Blob** ' u seçin.

    ![Bağlantı ayrıntısı](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Bu yönergeleri izleyerek aldığınız **kapsayıcı adı** ve **depolama hesabı bağlantı dizesini** belirtin.

    * Kapsayıcı adı örneği: `marketplaceleadcontainer`
    * Depolama hesabı bağlantı dizesi örneği: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![bağlantı ayrıntısı](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **Kaydet**’i seçin.

    > [!NOTE]
    > Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.


