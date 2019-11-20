---
title: Azure Data Lake Storage 2. verileri yönetmek için Azure Depolama Gezgini kullanma
description: Bu hızlı başlangıçta, bir Azure Data Lake Storage 2. hesabında bir kapsayıcı oluşturmak için Azure Depolama Gezgini kullanmayı ve bir dizin ve bir dosyayı öğrenirsiniz. Ardından, dosyanın yerel bilgisayarınıza nasıl indirileceği ve tüm dosyanın bir dizinde nasıl görüntüleneceği hakkında bilgi edineceksiniz.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 95d7a58c8188e8c6633f6be50af608aed437edff
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991413"
---
# <a name="use-azure-storage-explorer-to-manage-data-in-an-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage 2. hesabındaki verileri yönetmek için Azure Depolama Gezgini kullanma

Bu hızlı başlangıçta, [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanarak dizin ve BLOB oluşturma hakkında bilgi edineceksiniz. Ardından, Blobun yerel bilgisayarınıza nasıl indirileceği ve bir dizindeki tüm Blobların nasıl görüntüleneceği hakkında bilgi edineceksiniz. Ayrıca, bir Blobun anlık görüntüsünü oluşturmayı, Dizin erişim ilkelerini yönetmeyi ve paylaşılan erişim imzası oluşturmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Bu hızlı başlangıç Azure Depolama Gezgini'ni yüklemenizi gerektirir. Windows, Macintosh veya Linux işletim sisteminde Azure Depolama Gezgini’ni yüklemek için bkz. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Depolama Gezgini oturum açın

Uygulamayı ilk kez başlattığınızda **Microsoft Azure Depolama Gezgini - Bağlan** penceresi görüntülenir. Depolama Gezgini depolama hesaplarına bağlanmak için çeşitli yollar sağladığından, ACL 'Leri yönetmek için şu anda yalnızca bir yol desteklenir.

|Görev|Amaç|
|---|---|
|Azure Hesabı ekleme | Azure'da kimlik doğrulaması gerçekleştirmek için kuruluşunuzun oturum açma sayfasını açar. ACL 'Leri yönetmek ve ayarlamak istiyorsanız şu anda desteklenen tek kimlik doğrulama yöntemidir. |

**Azure Hesabı Ekle**'yi seçip **Oturum açın**'a tıklayın. Azure hesabınızda oturum açmak için ekrandaki talimatları izleyin.

![Microsoft Azure Depolama Gezgini - Bağlan penceresi](media/storage-quickstart-blobs-storage-explorer/connect.png)

Bağlantı kurulduğunda Azure Depolama Gezgini yüklenir ve **Gezgin** sekmesi gösterilir. Bu görünümde tüm Azure depolama hesaplarınıza ek olarak [Azure Depolama Öykünücüsü](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) hesapları veya [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ortamları üzerinden yapılandırılan yerel depolama alanlarını görebilirsiniz.

![Microsoft Azure Depolama Gezgini - Bağlan penceresi](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Blob 'lar her zaman bir dizine yüklenir. Bu, blob gruplarını bilgisayarınızdaki dosyaları klasörler halinde düzenlediğiniz gibi düzenleyebilmenizi sağlar.

Bir dizin oluşturmak için, devam adımında oluşturduğunuz depolama hesabını genişletin. **BLOB kapsayıcısı**' nı seçin, sağ tıklayın ve **BLOB kapsayıcısı oluştur**' u seçin. Kapsayıcının adını girin. Tamamlandığında, kapsayıcıyı oluşturmak için **ENTER** tuşuna basın. Blob dizini başarıyla oluşturulduktan sonra, seçili depolama hesabı için **BLOB kapsayıcısı** klasörü altında görüntülenir.

![Microsoft Azure Depolama Gezgini-kapsayıcı oluşturma](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Blob 'ları dizine yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. IaaS VM’lerini yedeklemek için kullanılan VHD dosyaları sayfa bloblarıdır. Ekleme blobları, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetme için kullanılır. Blob depolamada depolanan çoğu dosya blok blobudur.

Dizin şeridinde **karşıya yükle**' yi seçin. Bu işlemi kullanarak klasör veya dosya yükleyebilirsiniz.

Yüklenecek dosyaları veya klasörü seçin. **Blob türü**'nü seçin. **Ekleme**, **Sayfa** veya **Blok** bloblarını seçebilirsiniz.

.vhd veya .vhdx dosyası yüklüyorsanız **.vhd/.vhdx dosyalarını sayfa blobları olarak yükle (önerilen)** seçeneğini belirleyin.

**Klasöre yükle (isteğe bağlı)** alanına, dosya veya klasörleri dizin altındaki bir klasöre depolamak için bir klasör adı. Klasör seçili değilse, dosyalar doğrudan dizinin altına yüklenir.

![Microsoft Azure Depolama Gezgini - blob yükleme](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

**Tamam**'ı seçtiğinizde dosyalar yüklenmek üzere kuyruğa alınır ve tüm dosyalar yüklenir. Yükleme işlemi tamamlandığında sonuçlar **Etkinlikler** penceresinde gösterilir.

## <a name="view-blobs-in-a-directory"></a>Blob 'ları bir dizinde görüntüleme

**Azure Depolama Gezgini** uygulamasında, depolama hesabı altında bir dizin seçin. Ana bölmede, seçili dizindeki Blobların bir listesi gösterilir.

![Bir dizindeki Microsoft Azure Depolama Gezgini Blobları listeleme](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Blob’ları indirme

**Azure Depolama Gezgini**'ni kullanarak blobları indirmek için istediğiniz blobu seçtikten sonra şeritteki **İndir**'e tıklayın. Açılan dosya iletişim kutusuna dosya adı girebilirsiniz. Blobu yerel konuma indirmeye başlamak için **Kaydet**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, dosyaları **Azure Depolama Gezgini** kullanarak yerel bir disk ve Azure Blob depolama arasında aktarmayı öğrendiniz. Dosyalarınızda ve dizinlerinizde ACL 'Lerin nasıl ayarlanacağı hakkında bilgi edinmek için, konudaki nasıl yapılır? konusundaki deneyimimize devam edin.

> [!div class="nextstepaction"]
> [Dosyalar ve dizinler üzerinde ACL 'Ler ayarlama](data-lake-storage-how-to-set-permissions-storage-explorer.md)
