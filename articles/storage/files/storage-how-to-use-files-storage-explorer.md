---
title: Azure Depolama Gezgini kullanarak Azure dosya paylaşımlarını yönetme
description: Bu hızlı başlangıcı Azure dosyalarını yönetmek için Azure Depolama Gezgini kullanma hakkında bilgi edinmek için kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629793"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Hızlı Başlangıç: Azure Depolama Gezgini ile Azure dosya paylaşımları oluşturma ve yönetme
Bu kılavuzda, Azure Depolama Gezgini'ni kullanarak [Azure dosya paylaşımlarında](storage-files-introduction.md) çalışmanın temelleri gösterilir. Azure dosya paylaşımları diğer dosya paylaşımları gibidir, ancak bulutta depolanır ve Azure platformu tarafından desteklenir. Azure dosya paylaşımları endüstri standardı SMB protokolünü destekler ve birden çok makine, uygulama ve örnek arasında dosya paylaşmayı olanaklı kılar. 

Azure Depolama Gezgini Windows, macOS ve Linux için kullanılabilen popüler bir istemci aracıdır. Depolama Gezgini’ni kullanarak Azure dosya paylaşımlarını ve diğer depolama kaynaklarını yönetebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıç için Depolama Gezgini'nin yüklü olması gerekir. İndirip yüklemek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)’ne gidin.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Depolama Gezgini’ni yeni kaynaklar oluşturmak için kullanamazsınız. Bu tanıtım için [Azure portalında](https://portal.azure.com/) depolama hesabı oluşturun. 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Depolama Gezgini'ni Azure kaynaklarına bağlama
Depolama Gezgini’ni ilk kez başlattığınızda **Microsoft Azure Depolama Gezgini - Bağlan** penceresi görüntülenir. Depolama Gezgini depolama hesaplarına bağlamak için birçok yol sağlar: 

- **Azure hesabınızı kullanarak oturum açma**: Kuruluşunuzun kimlik bilgilerini veya Microsoft hesabınızı kullanarak oturum açabilirsiniz. 
- **Bir bağlantı dizesi veya SAS belirteci kullanarak belirli bir depolama hesabına bağlanma**: Bağlantı dizesi, depolama hesabı adını ve depolama hesabı anahtarını/SAS belirtecini içeren özel bir dizedir. Depolama Gezgini, belirteci kullanarak doğrudan depolama hesabına erişir (bir Azure hesabındaki tüm depolama hesaplarını görmek yerine). Bağlantı dizeleri hakkında daha fazla bilgi edinmek için bkz. [Azure depolama bağlantı dizelerini yapılandırma](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Depolama hesabı adı ve anahtarı kullanarak belirli bir depolama hesabına bağlanma**: Azure depolamaya bağlanmak için depolama hesabınızın depolama hesabı adını ve anahtarını kullanın.

Bu hızlı başlangıç için, Azure hesabınızı kullanarak oturum açın. **Azure Hesabı Ekle**’yi ve sonra **Oturum Aç**’ı seçin. Azure hesabınızda oturum açmak için yönergeleri izleyin.

![Microsoft Azure Depolama Gezgini - Bağlan penceresinin ekran görüntüsü](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma
`storageacct<random number>` depolama hesabı içinde ilk Azure dosya paylaşımınızı oluşturmak için:

1. Oluşturduğunuz depolama hesabını genişletin.
2. **Dosya Paylaşımları**'na sağ tıklayın ve sonra **Dosya Paylaşımı Oluştur**'u seçin.  
    ![Dosya paylaşımları klasörünün ve bağlama uygun bağlam menüsünün ekran görüntüsü](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Dosya paylaşımı için *myshare* yazın ve sonra Enter tuşuna basın.

Paylaşım adları yalnızca küçük harf, sayı ve tek kısa çizgi içerebilir (ancak kısa çizgiyle başlayamaz). Dosya paylaşımlarının ve dosyaların adlandırılması hakkında tüm ayrıntılara ulaşmak için bkz. [Paylaşım, dizin, dosya ve meta verileri adlandırma ve bunlara başvuruda bulunma](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Dosya paylaşımı oluşturulduktan sonra sağ bölmede dosya paylaşımınız için bir sekme açılır. 

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Artık Azure dosya paylaşımını oluşturduğunuza göre, [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) veya [macOS](storage-how-to-use-files-mac.md)'ta SMB ile dosya paylaşımını bağlayabilirsiniz. Alternatif olarak, Azure Depolama Gezgini’ni kullanarak Azure dosya paylaşımınızla çalışabilirsiniz. SMB kullanarak dosya paylaşımınızı bağlamak yerine Azure Depolama Gezgini kullanmanın avantajı, Azure Depolama Gezgini ile yapılan tüm isteklerin Dosya REST API’si kullanılarak yapılmasıdır. Dosya REST API’sini kullanarak, SMB erişimi olmayan istemciler üzerinde dosyalar ve dizinler oluşturabilir, değiştirebilir ve silebilirsiniz.

### <a name="create-a-directory"></a>Dizin oluşturma
Dizin eklemek, dosya paylaşımınızın yönetimi için hiyerarşik bir yapı sağlar. Dizininizde birden çok düze oluşturabilirsiniz. Ancak, alt dizinleri oluşturmadan önce üst dizinlerin var olduğundan emin olmanız gerekir. Örneğin, myDirectory/mySubDirectory yolu için öncelikle *myDirectory* dizinini oluşturmanız gerekir. Daha sonra *mySubDirectory* dizinini oluşturabilirsiniz. 

1. Dosya paylaşımının sekmesinde, en üstteki menüden **Yeni Klasör** düğmesini seçin. **Yeni Dizin Oluştur** bölmesi açılır.
    ![Bağlamda Yeni Klasör düğmesinin ekran görüntüsü](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Dizin adı olarak *myDirectory* yazın ve ardından **Tamam**’ı seçin. 

*myDirectory* dizini, *myshare* dosya paylaşımının sekmesinde listelenir.

### <a name="upload-a-file"></a>Dosyayı karşıya yükleme 
Yerel makinenizden bir dosyayı dosya paylaşımınızdaki yeni dizine yükleyebilirsiniz. Klasörün tamamını veya tek bir dosyayı karşıya yükleyebilirsiniz.

1. Üst menüden **Karşıya Yükle**’yi seçin. Bu işlemi kullanarak klasör veya dosyayı karşıya yükleyebilirsiniz.
2. **Dosyayı Karşıya Yükle**'yi seçin ve sonra yerel makinenizden karşıya yüklenecek dosyayı seçin.
3. **Dizine yükle** alanına *myDirectory* yazın ve sonra **Karşıya Yükle**'yi seçin. 

İşiniz bittiğinde, dosya *myDirectory* bölmesindeki listede görünür.

### <a name="download-a-file"></a>Dosya indirme
Dosya paylaşımınızdan bir dosyanın kopyasını indirmek için dosyaya sağ tıklayın ve ardından **İndir**’i seçin. Dosyanın yerel makinenizde nereye konmasını istediğinizi seçin ve ardından **Kaydet**'i seçin.

Pencerenin en altındaki **Etkinlikler** bölmesinde indirme işleminin ilerleme durumu görünür.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Depolama Gezgini’ni kaynakları kaldırmak için kullanamazsınız. Bu hızlı başlangıçtan temizlemek için [Azure portalını](https://portal.azure.com/) kullanabilirsiniz. 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Dosyalar nedir?](storage-files-introduction.md)