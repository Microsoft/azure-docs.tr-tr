---
title: Azure Data Lake Storage 2. ile Azure Depolama Gezgini kullanma
description: Hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında dizinleri ve dosya ve Dizin erişim denetim listelerini (ACL) yönetmek için Azure Depolama Gezgini kullanın.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652284"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. dizinleri ve dosyaları yönetmek için Azure Depolama Gezgini kullanın

Bu makalede, hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında dizin ve dosya oluşturmak ve yönetmek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) nasıl kullanılacağı gösterilmektedir.

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

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Kapsayıcı dizinleri ve dosyaları barındırır. Bir tane oluşturmak için, devam adımında oluşturduğunuz depolama hesabını genişletin. **BLOB kapsayıcıları**' nı seçin, sağ tıklayın ve **BLOB kapsayıcısı oluştur**' u seçin. Kapsayıcının adını girin. Adlandırma kapsayıcılarındaki kuralların ve kısıtlamaların listesi için [kapsayıcı oluşturma](storage-quickstart-blobs-dotnet.md#create-a-container) bölümüne bakın. Tamamlandığında, kapsayıcıyı oluşturmak için **ENTER** tuşuna basın. Kapsayıcı başarıyla oluşturulduktan sonra, seçili depolama hesabı için **BLOB kapsayıcıları** klasörü altında görüntülenir.

![Microsoft Azure Depolama Gezgini-kapsayıcı oluşturma](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Dizin oluşturma

Bir dizin oluşturmak için, devam adımında oluşturduğunuz kapsayıcıyı seçin. Kapsayıcı şeridinde **Yeni klasör** düğmesini seçin. Dizininizin adını girin. Tamamlandığında, dizini oluşturmak için **ENTER** tuşuna basın. Dizin başarıyla oluşturulduktan sonra düzenleyici penceresinde görünür.

![Microsoft Azure Depolama Gezgini-dizin oluşturma](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blob 'ları dizine yükleme

Dizin şeridinde **karşıya yükle** düğmesini seçin. Bu işlemi kullanarak klasör veya dosya yükleyebilirsiniz.

Yüklenecek dosyaları veya klasörü seçin.

![Microsoft Azure Depolama Gezgini - blob yükleme](media/data-lake-storage-explorer/upload-file.png)

**Tamam**'ı seçtiğinizde dosyalar yüklenmek üzere kuyruğa alınır ve tüm dosyalar yüklenir. Yükleme işlemi tamamlandığında sonuçlar **Etkinlikler** penceresinde gösterilir.

## <a name="view-blobs-in-a-directory"></a>Blob 'ları bir dizinde görüntüleme

**Azure Depolama Gezgini** uygulamasında, depolama hesabı altında bir dizin seçin. Ana bölmede, seçili dizindeki Blobların bir listesi gösterilir.

![Bir dizindeki Microsoft Azure Depolama Gezgini Blobları listeleme](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blob’ları indirme

**Azure Depolama Gezgini** kullanarak dosyaları indirmek için, bir dosya seçiliyken şeritten **İndir** ' i seçin. Açılan dosya iletişim kutusuna dosya adı girebilirsiniz. Yerel konuma bir dosyanın indirilmesini başlatmak için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Erişim denetim listeleri (ACL 'Ler) ayarlayarak dosya ve Dizin iznini yönetmeyi öğrenin

> [!div class="nextstepaction"]
> [Azure Data Lake Storage 2. ACL 'Leri yönetmek için Azure Depolama Gezgini kullanma](./data-lake-storage-explorer-acl.md)
