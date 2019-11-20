---
title: Azure Blob depolama 'ya bağlanma-Azure Logic Apps
description: Azure Logic Apps kullanarak Azure Storage hesaplarında blob oluşturma ve yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/28/2019
tags: connectors
ms.openlocfilehash: c431f917f6fc1ac080b13184bd9ce205a20afbaa
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199571"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Azure Blob depolamada Bloblar oluşturma ve yönetme

Bu makalede, Azure Blob depolama Bağlayıcısı ile bir mantık uygulamasının içinden Azure depolama hesabınızda blob olarak depolanan dosyaları nasıl erişebileceğiniz ve yönetebileceğiniz gösterilmektedir. Bu şekilde, dosyalarınızı yönetmek için görevleri ve iş akışlarını otomatikleştiren mantıksal uygulamalar oluşturabilirsiniz. Örneğin, depolama hesabınızda dosya oluşturan, alan güncelleştiren, güncelleştiren ve silen mantıksal uygulamalar oluşturabilirsiniz.

Bir Azure Web sitesinde güncelleştirilmiş bir aracınız olduğunu varsayalım. mantıksal uygulamanız için tetikleyici işlevi görür. Bu olay gerçekleştiğinde, mantıksal uygulamanızdaki bir işlem olan BLOB depolama kapsayıcısında bir dosya olan bir dosya, mantıksal uygulamanızın güncelleştirilmesini sağlayabilirsiniz.

Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin. Bağlayıcıya özgü teknik bilgiler için bkz. [Azure Blob depolama Bağlayıcısı başvurusu](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Güvenlik duvarlarının arkasındaki depolama hesaplarına Azure Logic Apps erişimi etkinleştirmek için, bu konunun devamındaki [güvenlik duvarları sonrasındaki depolama hesaplarına erişin](#storage-firewalls) bölümüne bakın.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Sınırlar

* Varsayılan olarak, Azure Blob depolama eylemleri *50 MB veya daha küçük*olan dosyaları okuyabilir veya yazabilir. 50 MB 'den büyük ancak 1024 MB 'a kadar olan dosyaları işlemek için Azure Blob depolama eylemleri [ileti parçalama](../logic-apps/logic-apps-handle-large-messages.md)desteği sağlar. **BLOB Içeriğini al** eylemi, örtük olarak parçalama kullanır.

* Azure Blob depolama Tetikleyicileri parçalama desteği vermez. Dosya içeriği istenirken Tetikleyiciler yalnızca 50 MB veya daha küçük olan dosyaları seçer. 50 MB 'tan büyük dosyaları almak için şu modele uyun:

  * **BLOB eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)** dosya özellikleri döndüren bir Azure Blob depolama tetikleyicisi kullanın.

  * Tam dosyayı okuyan ve örtük olarak parçalama kullanan Azure Blob depolama **BLOB Içeriğini al** eylemini kullanarak tetikleyiciyi izleyin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Bir [Azure depolama hesabı ve depolama kapsayıcısı](../storage/blobs/storage-quickstart-blobs-portal.md)

* Azure Blob depolama hesabınıza erişmeniz gereken mantıksal uygulama. Mantıksal uygulamanızı bir Azure Blob depolama tetikleyicisi ile başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>BLOB depolama tetikleyicisi Ekle

Azure Logic Apps, her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında tetiklenen bir [tetikleyiciyle](../logic-apps/logic-apps-overview.md#logic-app-concepts)başlamalıdır. Tetikleyici her tetiklendiğinde Logic Apps altyapısı bir mantıksal uygulama örneği oluşturur ve uygulamanızın iş akışını çalıştırmaya başlar.

Bu örnek, depolama kapsayıcıınızda bir blob 'un özellikleri eklendiğinde veya güncelleştirilirken bir **BLOB eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)** tetikleyicisiyle bir mantıksal uygulama iş akışını nasıl başlatakullanabileceğinizi gösterir.

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama Tasarımcısı ' nı açan boş bir mantıksal uygulama oluşturun. Bu örnek Azure portal kullanır.

2. Arama kutusuna filtreniz olarak "Azure Blob" yazın. Tetikleyiciler listesinden istediğiniz tetikleyiciyi seçin.

   Bu örnek, bu tetikleyiciyi kullanır: **bir blob eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)**

   ![Azure Blob depolama tetikleyicisi seçin](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Bağlantı ayrıntıları istenirse, [BLOB depolama bağlantınızı şimdi oluşturun](#create-connection). Ya da bağlantınız zaten varsa, tetikleyici için gerekli bilgileri sağlayın.

   Bu örnek için, izlemek istediğiniz kapsayıcıyı ve klasörü seçin.

   1. **Kapsayıcı** kutusunda klasör simgesini seçin.

   2. Klasör listesinde, sağ açılı köşeli ayracı ( **>** ) seçin ve istediğiniz klasörü bulup seçinceye kadar tarayın.

      ![Tetikleyici ile kullanılacak depolama klasörünü seçin](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Tetikleyicinin klasör değişikliklerini ne sıklıkta denetlemesini istediğinizi belirlemek için aralığı ve sıklığı seçin.

4. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

5. Şimdi, tetikleyici sonuçlarıyla gerçekleştirmek istediğiniz görevler için mantıksal uygulamanıza bir veya daha fazla eylem eklemeye devam edin.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB depolama eylemi ekleme

Azure Logic Apps bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) , iş akışınızda bir tetikleyiciyi veya başka bir eylemi izleyen bir adımdır. Bu örnekte, mantıksal uygulama [yinelenme tetikleyicisiyle](../connectors/connectors-native-recurrence.md)başlar.

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın. Bu örnek Azure portal kullanır.

2. Mantıksal uygulama Tasarımcısı ' nda, tetikleyici veya eylem altında **yeni adım**' ı seçin.

   ![Logic App iş akışına yeni adım Ekle](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Varolan adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve **Eylem Ekle**' yi seçin.

3. Arama kutusuna filtreniz olarak "Azure Blob" yazın. Eylemler listesinden istediğiniz eylemi seçin.

   Bu örnek şu eylemi kullanır: **BLOB Içeriğini al**

   ![Azure Blob depolama eylemini seçin](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Bağlantı ayrıntıları istenirse, [Azure Blob depolama bağlantınızı hemen oluşturun](#create-connection).
Ya da bağlantınız zaten varsa, eylem için gerekli bilgileri sağlayın.

   Bu örnek için istediğiniz dosyayı seçin.

   1. **BLOB** kutusundan klasör simgesini seçin.
  
      ![Eylemde kullanılacak depolama klasörünü seçin](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Blob 'un **kimlik** numarasına göre istediğiniz dosyayı bulun ve seçin. Bu **kimlik** numarasını, daha önce açıklanan BLOB depolama tetikleyicisi tarafından döndürülen Blobun meta verilerinde bulabilirsiniz.

5. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.
Mantıksal uygulamanızı test etmek için, seçili klasörde bir blob bulunduğundan emin olun.

Bu örnek yalnızca bir Blobun içeriğini alır. İçeriği görüntülemek için başka bir bağlayıcı kullanarak blob ile dosya oluşturan başka bir eylem ekleyin. Örneğin, blob içeriklerine dayalı bir dosya oluşturan OneDrive eylemi ekleyin.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Depolama hesabına Bağlan

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Bağlantıyı oluşturmanız istendiğinde, şu bilgileri sağlayın:

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Bağlantı Adı** | Yes | <*bağlantı-adı*> | Bağlantınız için oluşturulacak ad |
   | **Depolama Hesabı** | Yes | <*depolama hesabı*> | Listeden depolama hesabınızı seçin. |
   ||||

   Örnek:

   ![Azure Blob depolama hesabı bağlantısı oluşturma](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png)  

1. Hazırsanız **Oluştur** ' u seçin.

1. Bağlantınızı oluşturduktan sonra, [BLOB depolama tetikleyicisi ekleme](#add-trigger) veya [BLOB depolama ekleme eylemi](#add-action)ile devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Open API (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](https://docs.microsoft.com/connectors/azureblobconnector/)bakın.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Güvenlik duvarlarının arkasındaki depolama hesaplarına erişin

Bir [güvenlik duvarı ve güvenlik duvarı kurallarıyla](../storage/common/storage-network-security.md)erişimi kısıtlayarak, bir Azure depolama hesabına ağ güvenliği ekleyebilirsiniz. Ancak bu kurulum, depolama hesabına erişmesi gereken Azure ve diğer Microsoft Hizmetleri için bir zorluk oluşturur. Veri merkezindeki yerel iletişim iç IP adreslerini soyutlar; bu nedenle, IP kısıtlamalarına sahip güvenlik duvarı kuralları ayarlayamazsınız. Daha fazla bilgi için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](../storage/common/storage-network-security.md).

Azure Blob Storage bağlayıcısını veya diğer çözümleri kullanarak Azure Logic Apps güvenlik duvarlarının arkasındaki depolama hesaplarına erişmek için çeşitli seçenekler şunlardır:

* Azure Depolama Blobu bağlayıcısı

  * [Diğer bölgelerdeki depolama hesaplarına erişin](#access-other-regions)
  * [Güvenilen bir sanal ağ üzerinden depolama hesaplarına erişme](#access-trusted-virtual-network)

* Diğer çözümler

  * [Yönetilen kimliklerle bir güvenilen hizmet olarak depolama hesaplarına erişin](#access-trusted-service)
  * [Azure API Management aracılığıyla depolama hesaplarına erişin](#access-api-management)

<a name="access-other-regions"></a>

### <a name="access-to-storage-accounts-in-other-regions"></a>Diğer bölgelerdeki depolama hesaplarına erişim

Logic Apps, güvenlik duvarı kuralları olan ve aynı bölgede yer alan depolama hesaplarına doğrudan erişemez. Ancak, [bölgeniz içindeki yönetilen bağlayıcılar için gıden IP adreslerine](../logic-apps/logic-apps-limits-and-config.md#outbound)erişime izin verirseniz, mantıksal uygulamalarınız Azure Tablo depolama bağlayıcısını veya Azure kuyruk depolama bağlayıcısını kullandığınız durumlar dışında farklı bir bölgedeki depolama hesaplarına erişebilir. Tablo depolama veya kuyruk depolamaya erişmek için, yerleşik HTTP tetikleyicisini ve eylemlerini kullanmaya devam edebilirsiniz.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Güvenilen bir sanal ağ üzerinden depolama hesaplarına erişme

Yönettiğiniz bir Azure sanal ağına depolama hesabı koyabilirsiniz ve ardından bu sanal ağı güvenilen sanal ağlar listesine ekleyebilirsiniz. Mantıksal uygulamanızın, [Güvenilen bir sanal ağ](../virtual-network/virtual-networks-overview.md)üzerinden depolama hesabına erişmesini sağlamak için, bu mantıksal uygulamayı bir [tümleştirme hizmeti ortamına (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)dağıtmanız gerekir ve bu, bir sanal ağdaki kaynaklara bağlanabilir. Daha sonra bu ıSE alt ağlarını güvenilen listeye ekleyebilirsiniz. Blob Storage Bağlayıcısı gibi Azure depolama bağlayıcıları, depolama kapsayıcısına doğrudan erişebilir. Bu kurulum, bir ıSE 'deki hizmet uç noktalarını kullanmakla aynı deneyimdir.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Yönetilen kimliklerle bir güvenilen hizmet olarak depolama hesaplarına erişin

Microsoft 'a güvenilen hizmetlere bir güvenlik duvarı aracılığıyla bir depolama hesabına erişim vermek için bu depolama hesabında bu hizmetler için bir özel durum oluşturabilirsiniz. Bu çözüm, [kimlik doğrulaması için yönetilen kimlikleri](../active-directory/managed-identities-azure-resources/overview.md) destekleyen Azure hizmetlerinin, güvenlik duvarlarının arkasındaki depolama hesaplarına Güvenilen hizmet olarak erişmesini sağlar. Özellikle, genel çok kiracılı Azure 'da bu depolama hesaplarına erişim için bir mantıksal uygulama için, mantıksal uygulamada [yönetilen kimlik desteğini etkinleştirmeniz](../logic-apps/create-managed-service-identity.md) gerekir. Daha sonra, mantıksal uygulamanızda HTTP eylemini veya tetikleyiciyi kullanır ve [mantıksal uygulamanızın yönetilen kimliğini kullanmak için kimlik doğrulama türlerini ayarlarsınız](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Bu senaryo için *yalnızca* http eylemini veya tetikleyiciyi kullanabilirsiniz.

Özel durum ve yönetilen kimlik desteğini ayarlamak için aşağıdaki genel adımları izleyin:

1. Depolama hesabınızda, **Ayarlar**altında **güvenlik duvarları ve sanal ağlar**' ı seçin. **Erişime Izin ver**' in altında, ilgili ayarların görünmesi için **Seçili ağlar** seçeneğini belirleyin.

1. **Özel durumlar**altında, **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver**' i seçin ve ardından **Kaydet**' i seçin.

   ![Microsoft güvenilen hizmetlerine izin veren özel durum seçin](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Mantıksal uygulamanızın ayarlarında, [yönetilen kimlik desteğini etkinleştirin](../logic-apps/create-managed-service-identity.md).

1. Mantıksal uygulamanızın iş akışında, depolama hesabına veya varlığına erişmek için HTTP eylemini veya tetikleyiciyi ekleyin ve ayarlayın.

   > [!IMPORTANT]
   > Giden HTTP eylemi veya Azure Storage hesaplarına yapılan çağrıları tetiklemek için, istek üstbilgisinin, depolama hesabında çalıştırmak istediğiniz işlem için `x-ms-version` özelliğini ve API sürümünü içerdiğinden emin olun. Daha fazla bilgi için bkz. [Azure depolama hizmetleri için](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)yönetilen kimliğe ve sürüm oluşturmaya [erişim kimlik doğrulaması](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) .

1. Bu eylemde, kimlik doğrulaması için kullanılacak [yönetilen kimliği seçin](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) .

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Azure API Management aracılığıyla depolama hesaplarına erişin

[API Management](../api-management/api-management-key-concepts.md)için adanmış bir katman kullanıyorsanız, API Management kullanarak depolama API 'sini önyükleyebilir ve güvenlik duvarı ÜZERINDEN ikinci IP adreslerine izin verebilirsiniz. Temel olarak, API Management tarafından kullanılan Azure sanal ağını depolama hesabının güvenlik duvarı ayarına ekleyin. Daha sonra Azure depolama API 'Lerini çağırmak için API Management eylemini veya HTTP eylemini kullanabilirsiniz. Ancak, bu seçeneği belirlerseniz, kimlik doğrulama işlemini kendiniz işlemeniz gerekir. Daha fazla bilgi için bkz. [basit kurumsal tümleştirme mimarisi](https://aka.ms/aisarch).

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
