---
title: Hızlı başlangıç-Azure Depolama Gezgini blob oluşturma
titleSuffix: Azure Storage
description: Bir kapsayıcı ve BLOB oluşturmak, blobu yerel bilgisayarınıza indirmek ve kapsayıcıdaki tüm Blobları görüntülemek için Azure Depolama Gezgini kullanmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 2477107105b6dbcab96db8d44ac982554dc0c48d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543179"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>Hızlı başlangıç: blob oluşturmak için Azure Depolama Gezgini kullanma

Bu hızlı başlangıçta, bir kapsayıcı ve BLOB oluşturmak için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanmayı öğreneceksiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobları görüntülemeyi öğreneceksiniz. Ayrıca bir blobun anlık görüntüsünü oluşturma, kapsayıcı erişim ilkelerini yönetme ve paylaşılan erişim imzası oluşturma hakkında bilgi edineceksiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Bu hızlı başlangıç Azure Depolama Gezgini'ni yüklemenizi gerektirir. Windows, Macintosh veya Linux işletim sisteminde Azure Depolama Gezgini’ni yüklemek için bkz. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Depolama Gezgini oturumu açma

Uygulamayı ilk kez başlattığınızda **Microsoft Azure Depolama Gezgini - Bağlan** penceresi görüntülenir. Depolama Gezgini depolama hesaplarına bağlamak için birçok yol sağlar. Aşağıdaki tabloda bağlantı için kullanabileceğiniz yöntemlere yer verilmiştir:

|Görev|Amaç|
|---|---|
|Azure Hesabı ekleme | Sizi Azure 'da kimlik doğrulaması yapmak için kuruluşunuzun oturum açma sayfasına yönlendirir. |
|Bağlantı dizesi veya paylaşılan erişim imzası URI'si kullanma | SAS belirteci veya paylaşılan bağlantı dizesiyle doğrudan bir kapsayıcıya veya depolama hesabına erişmek için kullanılabilir. |
|Depolama hesabı adını ve anahtarını kullanma| Azure depolama alanına bağlanmak için depolama hesabı adını ve anahtarını kullanın.|

**Azure hesabı ekle** ' yi seçin ve **oturum aç**' a tıklayın... Azure hesabınızda oturum açmak için ekrandaki istemleri izleyin.

![Microsoft Azure Depolama Gezgini-Bağlan penceresini gösteren ekran görüntüsü.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Bağlantı kurulduğunda Azure Depolama Gezgini yüklenir ve **Gezgin** sekmesi gösterilir. Bu görünüm, tüm Azure depolama hesaplarınızın yanı sıra [Azurıite depolama öykünücüsü](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) hesapları veya [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ortamları aracılığıyla yapılandırılan yerel depolama hakkında bilgi verir.

![Microsoft Azure Depolama Gezgini - Bağlan penceresi](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bloblar her zaman bir kapsayıcıya yüklenir. Bu, blob gruplarını bilgisayarınızdaki dosyaları klasörler halinde düzenlediğiniz gibi düzenleyebilmenizi sağlar.

Kapsayıcı oluşturmak için önceki adımda oluşturduğunuz depolama hesabını genişletin. **Blob Kapsayıcıları**'nı ve ardından **Blob Kapsayıcısı Oluştur**'u seçin. Blob kapsayıcınızın adını girin. Blob kapsayıcıları adlandırmayla ilgili kural ve kısıtlamaların listesi için [kapsayıcı oluşturma](storage-quickstart-blobs-dotnet.md#create-a-container) bölümüne bakın. Girişleri tamamladığınızda blob kapsayıcısını oluşturmak için **Enter**'a basın. Blob kapsayıcısı başarıyla oluşturulduktan sonra, seçili depolama hesabının **Blob Kapsayıcıları** klasörü altında gösterilir.

## <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. IaaS VM’lerini yedeklemek için kullanılan VHD dosyaları sayfa bloblarıdır. Ekleme blobları, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetme için kullanılır. Blob depolamada depolanan çoğu dosya blok blobudur.

Kapsayıcı şeridinde **Yükle**'yi seçin. Bu işlemi kullanarak klasör veya dosya yükleyebilirsiniz.

Yüklenecek dosyaları veya klasörü seçin. **Blob türü**'nü seçin. **Ekleme**, **Sayfa** veya **Blok** bloblarını seçebilirsiniz.

.vhd veya .vhdx dosyası yüklüyorsanız **.vhd/.vhdx dosyalarını sayfa blobları olarak yükle (önerilen)** seçeneğini belirleyin.

**Klasöre yükle (isteğe bağlı)** alanına dosyaların veya klasörlerin depolanması için kapsayıcıdaki bir klasörün adını girebilirsiniz. Klasör seçilmezse dosyalar doğrudan kapsayıcı altına yüklenir.

![Microsoft Azure Depolama Gezgini - blob yükleme](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

**Tamam**'ı seçtiğinizde dosyalar yüklenmek üzere kuyruğa alınır ve tüm dosyalar yüklenir. Yükleme işlemi tamamlandığında sonuçlar **Etkinlikler** penceresinde gösterilir.

## <a name="view-blobs-in-a-container"></a>Blobları bir kapsayıcıda görüntüleme

**Azure Depolama Gezgini** uygulamasında depolama hesabının altındaki bir kapsayıcıyı seçin. Ana bölmede seçilen kapsayıcı içindeki blobların listesi gösterilir.

![Microsoft Azure Depolama Gezgini bir kapsayıcıyı seçtiğiniz yeri gösteren ekran görüntüsü.](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Blob’ları indirme

**Azure Depolama Gezgini**'ni kullanarak blobları indirmek için istediğiniz blobu seçtikten sonra şeritteki **İndir**'e tıklayın. Açılan dosya iletişim kutusuna dosya adı girebilirsiniz. Blobu yerel konuma indirmeye başlamak için **Kaydet**'i seçin.

## <a name="manage-snapshots"></a>Anlık görüntüleri yönetme

Azure Depolama Gezgini, bloblarınızın [anlık görüntülerini](./snapshots-overview.md) almanızı ve görüntülemenizi sağlar. Bir blobun anlık görüntüsünü almak için bloba sağ tıklayıp **Anlık Görüntü Oluştur**'u seçin. Bir blobun anlık görüntülerini görüntülemek için bloba sağ tıklayıp **Ekran Görüntülerini Yönet**'i seçin. Geçerli sekmede blobun ekran görüntülerinin listesi gösterilir.

![Microsoft Azure Depolama Gezgini blob 'ların listesini gösteren ekran görüntüsü.](media/storage-quickstart-blobs-storage-explorer/snapshots.png)

## <a name="manage-access-policies"></a>Erişim ilkelerini yönetme

Depolama Gezgini, kullanıcı arabiriminde kapsayıcılar için erişim ilkelerini yönetme imkanı sunar. Hizmet düzeyi ve hesap düzeyi olmak üzere iki güvenlik erişim ilkesi (SAS) türü vardır. Hesap düzeyi SAS, depolama hesabını hedefler ve birden fazla hizmete ve kaynağa uygulanabilir. Hizmet düzeyi SAS, belirli bir hizmet altındaki kaynak için tanımlanır. Hizmet düzeyi SAS oluşturmak için herhangi bir kapsayıcıya sağ tıklayın ve **erişim Ilkelerini Yönet...** seçeneğini belirleyin. Hesap düzeyi SAS oluşturmak için depolama hesabına sağ tıklayın.

Yeni bir erişim ilkesi eklemek ve ilkenin izinlerini tanımlamak için **Ekle**'yi seçin. İşlemi tamamladığınızda erişim ilkesini kaydetmek için **Kaydet**'i seçin. Bu ilkeyi Paylaşılan Erişim İmzası yapılandırma sırasında kullanabilirsiniz.

## <a name="work-with-shared-access-signatures"></a>Paylaşılan Erişim İmzaları ile çalışma

Paylaşılan Erişim İmzaları (SAS) Depolama Gezgini'nden alınabilir. Depolama hesabı, kapsayıcı veya blobu sağ tıklatın ve **paylaşılan erişim Imzası al...** seçeneğini belirleyin. Başlangıç ve sona erme saati ' ni ve SAS URL 'SI için izinleri seçin ve **Oluştur**' u seçin. Sonraki ekranda sorgu dizesini içeren tam URL'nin yanı sıra sorgu dizesinin kendisi sağlanır ve bu değerler kopyalanabilir.

![Microsoft Azure Depolama Gezgini - kapsayıcı içindeki blobları listeleme](media/storage-quickstart-blobs-storage-explorer/sharedaccesssignature.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, dosyaları **Azure Depolama Gezgini** kullanarak yerel bir disk ve Azure Blob depolama arasında aktarmayı öğrendiniz. Blob depolamayla çalışma hakkında daha fazla bilgi edinmek için, Blob depolama nasıl yapılır öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Blob Depolama İşlemleri Nasıl Yapılır](./storage-quickstart-blobs-powershell.md)