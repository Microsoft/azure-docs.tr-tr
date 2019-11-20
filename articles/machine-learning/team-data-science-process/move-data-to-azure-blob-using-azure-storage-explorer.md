---
title: BLOB depolama verilerini Azure Depolama Gezgini Team Data Science Işlemiyle taşıma
description: Azure Blob Storage 'dan verileri karşıya yüklemek ve indirmek için Azure Depolama Gezgini kullanmayı öğrenin.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053950"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Azure Depolama Gezgini kullanarak Azure Blob depolama alanına veya buradan veri taşıma
Azure Depolama Gezgini, Microsoft 'un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle çalışmanıza olanak sağlayan ücretsiz bir araçtır. Bu konu başlığı altında, Azure Blob depolama alanından verileri karşıya yüklemek ve indirmek için nasıl kullanılacağı açıklanmaktadır. Araç [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)adresinden indirilebilir.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure 'Daki veri bilimi sanal makineleri](virtual-machines.md)tarafından belirtilen BETIKLERLE ayarlanmış VM kullanıyorsanız, Azure Depolama Gezgini VM 'de zaten yüklüdür.
> 
> [!NOTE]
> Azure Blob depolama 'ya yönelik kapsamlı bir giriş için [Azure Blob temelleri](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) ve [Azure Blob hizmeti](https://msdn.microsoft.com/library/azure/dd179376.aspx)' ne bakın.   
> 
> 

## <a name="prerequisites"></a>Önkoşullar
Bu belgede, bu hesap için bir Azure aboneliğiniz, bir depolama hesabınız ve karşılık gelen depolama anahtarı olduğu varsayılır. Verileri karşıya yüklemeden/indirmeden önce Azure depolama hesabı adınızı ve hesap anahtarınızı bilmeniz gerekir. 

* Bir Azure aboneliği ayarlamak için bkz. [ücretsiz bir aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/).
* Depolama hesabı oluşturma ve hesap ve anahtar bilgilerini alma hakkında yönergeler için bkz. [Azure Storage hesapları hakkında](../../storage/common/storage-create-storage-account.md). Azure Depolama Gezgini aracı ile hesaba bağlanmak için bu anahtarın gerekli olduğu şekilde, depolama hesabınızın erişim anahtarını aklınızda olun.
* Azure Depolama Gezgini araç [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)indirilebilir. Yüklemesi sırasında varsayılanları kabul edin.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Depolama Gezgini kullan
Aşağıdaki adımlar Azure Depolama Gezgini kullanarak verileri karşıya yüklemeyi/indirmeyi belgeleyin. 

1. Microsoft Azure Depolama Gezgini başlatın.
2. **Hesabınızda oturum açın...** sihirbazında **Azure hesap ayarları** simgesini seçin, sonra **bir hesap ekleyin** ve kimlik bilgilerinizi girin. Azure Storage hesabı eklemek ![](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. **Azure depolama 'Ya bağlanma** Sihirbazı 'nı açmak için **Azure depolama 'ya Bağlan** simgesini seçin. !["Azure depolama 'ya Bağlan" seçeneğine tıklayın](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Azure Depolama hesabınızdaki erişim anahtarını **Azure Storage 'A Bağlan** sihirbazına girin ve ardından **İleri**' ye gidin. ![Azure Storage hesabından erişim anahtarı girin](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. **Hesap adı** kutusuna depolama hesabı adını girin ve ardından **İleri**' yi seçin. dış depolama](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png) eklemek ![
6. Eklenen depolama hesabı artık listelenmelidir. Bir depolama hesabında blob kapsayıcısı oluşturmak için, bu hesaptaki **BLOB kapsayıcıları** düğümüne sağ tıklayın, **BLOB kapsayıcısı oluştur**' u seçin ve bir ad girin.
7. Bir kapsayıcıya veri yüklemek için hedef kapsayıcıyı seçin ve **karşıya yükle** düğmesine tıklayın.![depolama hesapları](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. **Dosyalar** kutusunun sağ tarafındaki **..** . öğesine tıklayın, dosya sisteminden karşıya yüklenecek bir veya birden çok dosya seçin ve dosyaları karşıya yüklemeye başlamak için **karşıya yükle** ' ye tıklayın. Karşıya dosya yükleme![](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Verileri indirmek için, karşılık gelen kapsayıcıda bir blobu **seçip İndir ' e tıklayın.** dosyaları Indirmek ![](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

