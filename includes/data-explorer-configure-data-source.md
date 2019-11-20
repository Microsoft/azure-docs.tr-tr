---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581938"
---
## <a name="configure-the-data-source"></a>Veri kaynağını yapılandırma

Pano aracınız için bir veri kaynağı olarak Azure Veri Gezgini yapılandırmak için aşağıdaki adımları gerçekleştirirsiniz. Bu bölümde bu adımları daha ayrıntılı bir şekilde ele alacağız:

1. Azure Active Directory (Azure AD) hizmet sorumlusu oluşturun. Hizmet sorumlusu, pano aracınız tarafından Azure Veri Gezgini hizmetine erişmek için kullanılır.

1. Azure AD hizmet sorumlusunu Azure Veri Gezgini veritabanındaki *görüntüleyiciler* rolüne ekleyin.

1. Azure AD hizmet sorumlusunun bilgilerine göre Pano aracı bağlantı özelliklerinizi belirtin, ardından bağlantıyı test edin.

### <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Hizmet sorumlusunu [Azure Portal](#azure-portal) veya [Azure CLI](#azure-cli) komut satırı deneyimini kullanarak oluşturabilirsiniz. Kullandığınız yöntemden bağımsız olarak, oluşturulduktan sonra sonraki adımlarda kullanacağınız dört bağlantı özelliği için değerler alırsınız.

#### <a name="azure-portal"></a>Azure portal

1. Hizmet sorumlusu oluşturmak için [Azure Portal belgelerindeki](/azure/active-directory/develop/howto-create-service-principal-portal)yönergeleri izleyin.

    1. [Uygulamayı bir role ata](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) bölümünde, Azure Veri Gezgini kümenize bir **okuyucu** rol türü atayın.

    1. [Oturum açmak için değerleri Al](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) bölümünde, adımlarda kapsanan üç özellik değerini kopyalayın: **Dizin KIMLIĞI** (kiracı KIMLIĞI), **uygulama kimliği**ve **parola**.

1. Azure portal, **abonelikler** ' i seçin ve ardından hizmet sorumlusunu oluşturduğunuz aboneliğin kimliğini kopyalayın.

    ![Abonelik KIMLIĞI-Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Hizmet sorumlusu oluşturun. Uygun bir kapsamı ve `reader`rol türünü ayarlayın.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Daha fazla bilgi için bkz. [Azure CLI Ile Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Komut aşağıdaki gibi bir sonuç kümesi döndürür. Üç özellik değerini kopyalayın: **AppID**, **parola**ve **kiracı**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Aboneliklerinizin bir listesini alın.

    ```azurecli
    az account list --output table
    ```

    Uygun abonelik KIMLIĞINI kopyalayın.

    ![Abonelik KIMLIĞI-CLı](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Hizmet sorumlusunu görüntüleyiciler rolüne ekleyin

Artık bir hizmet sorumlusu olduğuna göre, Azure Veri Gezgini veritabanındaki *görüntüleyiciler* rolüne eklersiniz. Bu görevi Azure portal **izinler** altında veya bir yönetim komutu kullanarak **sorgu** altında gerçekleştirebilirsiniz.

#### <a name="azure-portal---permissions"></a>Azure portal Izinleri

1. Azure portal Azure Veri Gezgini kümenize gidin.

1. **Genel bakış** bölümünde StormEvents örnek verilerinin bulunduğu veritabanını seçin.

    ![Veritabanı Seç](media/data-explorer-configure-data-source/select-database.png)

1. **İzinler** ' i seçin ve ardından **ekleyin**.

    ![Veritabanı izinleri](media/data-explorer-configure-data-source/database-permissions.png)

1. **Veritabanı Izinleri Ekle**altında, **Görüntüleyici** rolünü seçip **sorumlular**' ı seçin.

    ![Veritabanı izinleri ekleme](media/data-explorer-configure-data-source/add-permission.png)

1. Oluşturduğunuz hizmet sorumlusu için arama yapın. Sorumluyu seçip öğesini **seçin**.

    ![Azure portal izinleri yönetme](media/data-explorer-configure-data-source/new-principals.png)

1. **Kaydet**’i seçin.

    ![Azure portal izinleri yönetme](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Yönetim komutu-sorgu

1. Azure portal Azure Veri Gezgini kümenize gidin ve **sorgu**' yı seçin.

    ![Sorgu](media/data-explorer-configure-data-source/query.png)

1. Sorgu penceresinde aşağıdaki komutu çalıştırın. Azure portal veya CLı 'den uygulama KIMLIĞI ve kiracı KIMLIĞINI kullanın.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Komut aşağıdaki gibi bir sonuç kümesi döndürür. Bu örnekte, ilk satır veritabanında var olan bir kullanıcıya yöneliktir ve ikinci satır, yeni eklenen hizmet sorumlusu içindir.

    ![Sonuç kümesi](media/data-explorer-configure-data-source/result-set.png)
