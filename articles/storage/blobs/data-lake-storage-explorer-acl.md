---
title: "Depolama Gezgini: Azure Data Lake Storage 2. ACL 'Leri ayarlama"
description: Hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında erişim denetim listelerini (ACL 'Ler) yönetmek için Azure Depolama Gezgini kullanın.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654546"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ACL 'Leri yönetmek için Azure Depolama Gezgini kullanma

Bu makalede, hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında erişim denetim listelerini (ACL 'Ler) yönetmek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) nasıl kullanacağınız gösterilmektedir.

Dizinlerin ve dosyaların ACL 'Lerini görüntülemek ve güncelleştirmek için Depolama Gezgini kullanabilirsiniz. ACL devralma, bir üst dizin altında oluşturulan yeni alt öğeler için zaten kullanılabilir. Ancak, bu değişiklikleri her bir alt öğe için ayrı ayrı yapmak zorunda kalmadan, ACL ayarlarını bir üst dizinin var olan alt öğelerine özyinelemeli olarak da uygulayabilirsiniz. 

Bu makalede, dosya veya dizin ACL 'sinin nasıl değiştirileceği ve ACL ayarlarının yinelemeli olarak alt dizinlere nasıl uygulanacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı (HNS) etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](../common/storage-account-create.md) yönergeleri izleyin.

- Yerel bilgisayarınızda yüklü Azure Depolama Gezgini. Windows, Macintosh veya Linux işletim sisteminde Azure Depolama Gezgini’ni yüklemek için bkz. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Depolama Gezgini, Azure Data Lake Storage 2. ile çalışırken hem blob (blob) & Data Lake Storage 2. (DFS) [uç noktalarını](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) kullanır. Azure Data Lake Storage 2. erişimi özel uç noktalar kullanılarak yapılandırıldıysa, depolama hesabı için iki özel uç noktanın oluşturulduğundan emin olun: hedef alt kaynak `blob` ve diğeri hedef alt kaynakla `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Depolama Gezgini oturum açın

Depolama Gezgini’ni ilk kez başlattığınızda **Microsoft Azure Depolama Gezgini - Bağlan** penceresi görüntülenir. Depolama Gezgini depolama hesaplarına bağlanmak için çeşitli yollar sağladığından, ACL 'Leri yönetmek için şu anda yalnızca bir yol desteklenir.

|Görev|Amaç|
|---|---|
|Azure Hesabı ekleme | Sizi Azure 'da kimlik doğrulaması yapmak için kuruluşunuzun oturum açma sayfasına yönlendirir. ACL 'Leri yönetmek ve ayarlamak istiyorsanız şu anda desteklenen tek kimlik doğrulama yöntemidir.|
|Bağlantı dizesi veya paylaşılan erişim imzası URI'si kullanma | SAS belirteci veya paylaşılan bağlantı dizesiyle doğrudan bir kapsayıcıya veya depolama hesabına erişmek için kullanılabilir. |
|Depolama hesabı adını ve anahtarını kullanma| Azure depolama alanına bağlanmak için depolama hesabı adını ve anahtarını kullanın.|

**Azure hesabı ekle** ' yi seçin ve **oturum aç**' a tıklayın... Azure hesabınızda oturum açmak için ekrandaki istemleri izleyin.

![Microsoft Azure Depolama Gezgini gösteren ekran görüntüsü ve Azure hesabı ekle seçeneğini ve oturum aç düğmesini vurgular.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Bağlantı kurulduğunda Azure Depolama Gezgini yüklenir ve **Gezgin** sekmesi gösterilir. Bu görünüm, tüm Azure depolama hesaplarınızın yanı sıra [Azurıite depolama öykünücüsü](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) hesapları veya [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ortamları aracılığıyla yapılandırılan yerel depolama hakkında bilgi verir.

![Microsoft Azure Depolama Gezgini - Bağlan penceresi](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="manage-an-acl"></a>ACL yönetme

Kapsayıcıya, dizine veya dosyaya sağ tıklayın ve ardından **Access Control listelerini yönet**' e tıklayın.  Aşağıdaki ekran görüntüsünde, bir dizine sağ tıkladığınızda görüntülenen menü gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![Azure Depolama Gezgini bir dizine sağ tıklayın](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

**Erişimi yönet** iletişim kutusu, sahip ve sahipler grubu için izinleri yönetmenizi sağlar. Ayrıca, izinleri yönetebilmeniz için erişim denetim listesine yeni kullanıcılar ve gruplar eklemenize olanak tanır.

> [!div class="mx-imgBorder"]
> ![Erişimi Yönet iletişim kutusu](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Erişim denetim listesine yeni bir kullanıcı veya grup eklemek için **Ekle** düğmesini seçin. Ardından, listeye eklemek istediğiniz karşılık gelen Azure Active Directory (Azure AD) girişini girin ve **Ekle**' yi seçin.  Kullanıcı veya grup artık **Kullanıcılar ve gruplar:** alanında görünür ve bu da izinlerini yönetmeye başlamanızı sağlar.

> [!NOTE]
> Azure AD 'de bir güvenlik grubu oluşturmak ve tek kullanıcılar yerine grup üzerinde izinleri sürdürmek için en iyi uygulamadır ve önerilir. Bu öneriye ilişkin ayrıntılar ve diğer en iyi yöntemler için [Azure Data Lake Storage 2. ' de erişim denetimi modeli](data-lake-storage-explorer-acl.md)' ne bakın.

Erişim ve varsayılan ACL 'Leri ayarlamak için onay kutusu denetimlerini kullanın. Bu ACL türleri arasındaki fark hakkında daha fazla bilgi edinmek için bkz. [ACL türleri](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>ACL 'Leri yinelemeli olarak Uygula

Bu değişiklikleri her alt öğe için ayrı ayrı yapmak zorunda kalmadan, ACL girişlerini bir üst dizinin varolan alt öğelerine yinelemeli olarak uygulayabilirsiniz.

ACL girdilerini yinelemeli olarak uygulamak için kapsayıcıya veya bir dizine sağ tıklayın ve **Access Control listeleri yay**' e tıklayın.  Aşağıdaki ekran görüntüsünde, bir dizine sağ tıkladığınızda görüntülenen menü gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![Dizine sağ tıklayıp erişimi yay denetim ayarını seçme](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Sonraki adımlar

Data Lake Storage 2. izin modeli hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Data Lake Storage 2. 'de erişim denetimi modeli](./data-lake-storage-access-control-model.md)
