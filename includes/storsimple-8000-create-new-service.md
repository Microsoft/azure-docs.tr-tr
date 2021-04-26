---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 746198f87e23cd7aca2a3177c23974917cb4b12a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026353"
---
#### <a name="to-create-a-new-service"></a>Yeni hizmet oluşturmak için

1. [Azure portalda](https://portal.azure.com/) oturum açmak için Microsoft hesabınızın kimlik bilgilerini kullanın.

2. Azure portalında **Kaynak oluştur** seçeneğine tıklayın ve markette **Tümünü gör**’e tıklayın.

    ![StorSimple Cihaz Yöneticisi oluşturma](./media/storsimple-8000-create-new-service/createssdevman1.png)

    _StorSimple Fiziksel_ ifadesini aratın. **StorSimple Fiziksel Cihaz Serisi**‘ni seçip tıklayın ve **Oluştur**’a tıklayın. Alternatif olarak, Azure portal **+** ve ardından **depolama** altında **StorSimple fiziksel cihaz Serisi**' ne tıklayın.

    ![StorSimple Device Manager 2 oluşturma](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. **StorSimple Cihaz Yöneticisi** dikey penceresinde aşağıdaki adımları gerçekleştirin:

   1. Hizmetiniz için benzersiz bir **Kaynak adı** sağlayın. Bu ad, hizmetinizi tanımlamak için kullanılabilecek kolay bir addır. Ad harf, rakam ve tirelerden oluşan 2-50 karakter arası uzunlukta olabilir. Ad bir harf veya sayıyla başlamalı ve bitmelidir.

   2. Açılan listeden bir **Abonelik** seçin. Abonelik fatura hesabınıza bağlıdır. Bu alan bir aboneliğiniz olmadığı sürece yoktur.

   3. **Kaynak Grubu** için **Mevcut grubu kullanın** veya **Yeni grup oluşturun**. Daha fazla bilgi edinmek için bkz. [Azure kaynak grupları](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Hizmetiniz için bir **Konum** sağlayın. Genel olarak, cihazınızı dağıtmak istediğiniz coğrafi bölgeye yakın bir konum seçmek istersiniz. Aşağıdaki konuları da hesaba katmak isteyebilirsiniz:

      * Azure’da, StorSimple cihazınızla dağıtmak istediğiniz var olan iş yükleriniz varsa o veri merkezini kullanmanız gerekir.
      * StorSimple Cihaz Yöneticisi hizmeti ve Azure Storage iki ayrı konumda olabilir. Böyle bir durumda, StorSimple Cihaz Yöneticisi ve Azure Storage hesabını ayrı ayrı oluşturmanız gerekir. Azure Depolama hesabı oluşturmak için Azure portalındaki Azure Depolama hizmetine gidin ve [Azure Depolama hesabı oluşturma](../articles/storage/common/storage-account-create.md) konusundaki adımları uygulayın. Bu hesabı oluşturduktan sonra, [Hizmet için yeni bir depolama hesabı yapılandırma](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service) konusundaki adımları uygulayarak bunu StorSimple Cihaz Yöneticisi hizmetine ekleyin.

   5. Otomatik olarak hizmeti olan bir depolama hesabı oluşturmak için **Yeni depolama hesabı oluştur**’u seçin. Bu depolama hesabı için bir ad belirtin. Verilerinizin farklı bir konumda olması gerekiyorsa bu kutunun işaretini kaldırın.

   6. Panonuzda bu hizmetin hızlı bağlantısının olmasını istiyorsanız **Panoya sabitle** seçeneğini işaretleyin.

   7. StorSimple Cihaz Yöneticisi’ni oluşturmak için **Oluştur**’a tıklayın.

       ![StorSimple Device Manager oluşturun 3](./media/storsimple-8000-create-new-service/createssdevman2.png)

Hizmetin oluşturulması birkaç dakika sürer. Hizmet başarıyla oluşturulduktan sonra bir bildirim görürsünüz ve yeni hizmet dikey penceresi açılır.

![StorSimple Device Manager 4 oluşturma](./media/storsimple-8000-create-new-service/createssdevman5.png)