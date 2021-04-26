---
title: 'Hızlı başlangıç: Azure Veri Gezgini Azure SYNAPSE Analytics çalışma alanına bağlama'
description: Azure SYNAPSE Analytics için Apache Spark kullanarak bir Azure Veri Gezgini kümesini Azure SYNAPSE Analytics çalışma alanına bağlayın.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92172280"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Azure Synapse Analytics için Apache Spark kullanarak Azure Veri Gezgini’ne bağlanma

Bu makalede, SYNAPSE Studio 'dan Azure SYNAPSE Analytics için Apache Spark bir Azure Veri Gezgini veritabanına nasıl erişebileceğiniz açıklanır.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Veri Gezgini kümesi ve veritabanı oluşturun](/azure/data-explorer/create-cluster-database-portal).
* Mevcut bir Azure SYNAPSE Analytics çalışma alanına sahip veya [hızlı başlangıç: Azure SYNAPSE çalışma alanı oluşturma](./quickstart-create-workspace.md)bölümündeki adımları izleyerek yeni bir çalışma alanı oluşturun.
* Mevcut bir Apache Spark havuzuna sahip veya [hızlı başlangıç: Azure Portal kullanarak Apache Spark havuzu oluşturma](./quickstart-create-apache-spark-pool-portal.md)bölümündeki adımları izleyerek yeni bir havuz oluşturun.
* [Bir Azure AD uygulaması sağlayarak Azure Active Directory (Azure AD) uygulaması oluşturun](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Azure [Veri Gezgini veritabanı Izinlerini yönetme](/azure/data-explorer/manage-database-permissions)bölümündeki adımları IZLEYEREK Azure AD uygulamanıza veritabanınıza erişim izni verin.

## <a name="go-to-synapse-studio"></a>SYNAPSE Studio 'ya git

Azure SYNAPSE çalışma alanından **Launch SYNAPSE Studio 'yu** seçin. SYNAPSE Studio giriş sayfasında veri **Nesne Gezgini** gitmek için **veri** ' yi seçin.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Azure Veri Gezgini veritabanını Azure SYNAPSE çalışma alanına bağlama

Bir Azure Veri Gezgini veritabanını bir çalışma alanına bağlamak, bağlı bir hizmet aracılığıyla yapılır. Azure Veri Gezgini bağlantılı bir hizmetle, Azure SYNAPSE için Apache Spark verilere göz atabilir ve bu verileri okuyabilir ve yazabilirsiniz. Ayrıca, tümleştirme işlerini bir ardışık düzen içinde de çalıştırabilirsiniz.

Veri Nesne Gezgini, bir Azure Veri Gezgini kümesini doğrudan bağlamak için aşağıdaki adımları izleyin:

1. **+** **Verilerin** yakınında bulunan simgeyi seçin.
1. Dış verilere bağlanmak için **Bağlan** ' ı seçin.
1. **Azure Veri Gezgini (kusto)** seçeneğini belirleyin.
1. **Devam**’ı seçin.
1. Bağlı hizmeti adlandırmak için kolay bir ad kullanın. Ad, veri Nesne Gezgini görünür ve Azure SYNAPSE çalışma zamanları tarafından veritabanına bağlanmak için kullanılır.
1. Aboneliğinizden Azure Veri Gezgini kümesini seçin veya URI 'yi girin.
1. **Hizmet sorumlusu kimliği** ve **hizmet sorumlusu anahtarını** girin. Bu hizmet sorumlusunun veritabanına okuma işlemi için görünüm erişiminin ve verilerin içe alımı için erişim izni olduğundan emin olun.
1. Azure Veri Gezgini veritabanı adını girin.
1. Doğru izinlere sahip olduğunuzdan emin olmak için **Bağlantıyı Sına** ' yı seçin.
1. **Oluştur**’u seçin.

    ![Yeni bir bağlı hizmet gösteren ekran görüntüsü.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Seçim **Sınama bağlantısı** yazma erişimini doğrulamaz. Hizmet sorumlusu KIMLIĞINIZIN Azure Veri Gezgini veritabanına yazma erişimi olduğundan emin olun.

1. Azure Veri Gezgini kümeleri ve veritabanları, **azure Veri Gezgini** bölümünün altındaki **bağlı** sekmede görüntülenir.

    ![Kümelere göz atmayı gösteren ekran görüntüsü.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > Geçerli sürümde, veritabanı nesneleri Azure Veri Gezgini veritabanlarında Azure AD hesabınızın izinlerine göre doldurulur. Apache Spark not defterlerini veya tümleştirme işlerini çalıştırdığınızda, bağlantı hizmetindeki kimlik bilgileri kullanılır (örneğin, hizmet sorumlusu).

## <a name="quickly-interact-with-code-generated-actions"></a>Kod tarafından oluşturulan eylemlerle hızlıca etkileşim kurun

Bir veritabanına veya tabloya sağ tıkladığınızda, örnek Spark Not Defterleri listesi görüntülenir. Azure Veri Gezgini 'de verileri okumak, yazmak veya akışa almak için bir seçenek belirleyin.

[![Yeni örnek not defterlerini gösteren ekran görüntüsü.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Veri okuma hakkında bir örnek aşağıda verilmiştir. Not defterini Spark havuzunuza ekleyin ve hücreyi çalıştırın.

[![Yeni bir okuma Not defteri gösteren ekran görüntüsü.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > İlk çalıştırma, Spark oturumunun başlatılması üç dakikadan uzun sürebilir. Sonraki yürütmeler önemli ölçüde daha hızlı olacaktır.

## <a name="limitations"></a>Sınırlamalar

Azure Veri Gezgini Bağlayıcısı Şu anda Azure SYNAPSE yönetilen sanal ağları ile desteklenmemektedir.

## <a name="next-steps"></a>Sonraki adımlar

* [Gelişmiş seçeneklere sahip örnek kod](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Veri Gezgini (kusto) Spark Bağlayıcısı](https://github.com/Azure/azure-kusto-spark)