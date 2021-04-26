---
title: Veri aktarımı için bir Azure çözümü seçin | Microsoft Docs
description: Ortamınızdaki veri boyutlarına ve kullanılabilir ağ bant genişliğine bağlı olarak veri aktarımı için bir Azure çözümü seçme hakkında bilgi edinin.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 11ea9c759bdb4bb2b837028407ce6e83f6e25a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92784057"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Veri aktarımı için bir Azure çözümü seçme

Bu makalede, bazı yaygın Azure veri aktarımı çözümlerine genel bakış sunulmaktadır. Makale Ayrıca ortamınızdaki ağ bant genişliğine ve aktarmak istediğiniz verilerin boyutuna bağlı olarak önerilen seçeneklere bağlantı sağlar.

## <a name="types-of-data-movement"></a>Veri taşıma türleri

Veri aktarımı çevrimdışı veya ağ bağlantısı üzerinden olabilir. Çözümünüze bağlı olarak çözümünüzü seçin:

- **Veri boyutu** -aktarım için tasarlanan verilerin boyutu,
- **Aktarım sıklığı** -bir kerelik veya düzenli veri alımı ve
- **Ağ** – ortamınızda veri aktarımı için kullanılabilir bant genişliği.

Veri taşıma aşağıdaki türlerde olabilir:

- **Sevk özellikli cihazlar kullanılarak çevrimdışı aktarma** -çevrimdışı bir kerelik toplu veri aktarımı yapmak istediğinizde fiziksel olarak sevk özellikli cihazları kullanın. Microsoft size bir disk veya güvenli bir özel cihaz gönderir. Alternatif olarak, kendi disklerinizi satın alabilir ve gönderebilirsiniz. Verileri cihaza kopyalar ve ardından verilerin karşıya yüklendiği Azure 'a gönderilir.  Bu durum için kullanılabilen seçenekler Data Box Disk, Data Box, Data Box Heavy ve Içeri/dışarı aktarma (kendi disklerinizi kullanın).

- **Ağ aktarımı** -verilerinizi ağ bağlantınız üzerinden Azure 'a aktarırsınız. Bu, birçok şekilde yapılabilir.

    - **Grafik arabirim** -zaman zaman yalnızca birkaç dosya aktarırsanız ve veri aktarımını otomatikleştirmeniz gerekmiyorsa, Azure Portal Azure Depolama Gezgini veya Web tabanlı bir araştırma aracı gibi bir grafik arabirim aracı seçebilirsiniz.
    - **Betikleştirilmiş veya programlı aktarma** -REST API 'Leri/SDK 'lerimizi doğrudan sağladığımız veya arayduğumuz iyileştirilmiş yazılım araçlarını kullanabilirsiniz. Kullanılabilir komut dosyalı araçlar AzCopy, Azure PowerShell ve Azure CLı 'larıdır. Programlama arabirimi için, .NET, Java, Python, Node/JS, C++, Go, PHP veya Ruby için SDK 'Lardan birini kullanın.
    - **Şirket içi cihazlarda** , veri merkezinizde bulunan fiziksel veya sanal bir cihaz sağlıyoruz ve ağ üzerinden veri aktarımını en iyi duruma getirir. Bu cihazlar, sık kullanılan dosyaların yerel bir önbelleğini de sağlar. Fiziksel cihaz Azure Stack Edge ve sanal cihaz Data Box Gateway. Her ikisi de şirket içinde kalıcı olarak çalışır ve ağ üzerinden Azure 'a bağlanır.
    - **Yönetilen veri işlem hattı** -çeşitli Azure Hizmetleri, şirket içi veya ikisinin birleşimi arasında düzenli olarak dosya aktarmak için bir bulut işlem hattı ayarlayabilirsiniz. Veri işlem hatlarını ayarlamak ve yönetmek için Azure Data Factory kullanın, verileri analiz için taşıma ve dönüştürme.

Aşağıdaki görselde aktarım için kullanılabilir ağ bant genişliğine, aktarım için tasarlanan veri boyutuna ve aktarım sıklığının sıklığına bağlı olarak çeşitli Azure veri aktarım araçları 'nı seçme yönergeleri gösterilmektedir.

![Azure veri aktarımı araçları](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Çevrimdışı aktarım cihazlarının üst sınırları-Data Box Disk, Data Box ve Data Box Heavy, bir cihaz türünün birden çok siparişi yerleştirilerek genişletilebilir.*

## <a name="selecting-a-data-transfer-solution"></a>Veri aktarımı çözümü seçme

Bir veri aktarımı çözümü seçmenize yardımcı olması için aşağıdaki soruları yanıtlayın:

- Kullanılabilir ağ bant genişliğiniz sınırlı veya mevcut değil ve büyük veri kümelerini aktarmak istiyor musunuz?
  
    Yanıt Evet ise, bkz. [Senaryo 1: büyük veri kümelerini veya düşük ağ bant genişliğiyle aktarma](storage-solution-large-dataset-low-network.md).
- Büyük veri kümelerini ağ üzerinden aktarmak istiyor musunuz ve orta düzeyde yüksek ağ bant genişliğine sahip olabilirsiniz misiniz?

    Yanıt Evet ise, bkz. [Senaryo 2: orta ile büyük veri kümelerini yüksek ağ bant genişliğine aktarma](storage-solution-large-dataset-moderate-high-network.md).
- Tek zaman ağ üzerinden yalnızca birkaç dosya aktarmak istiyor musunuz?

    Yanıt Evet ise, bkz. [Senaryo 3: ağ bant genişliğine sınırlı olan küçük veri kümelerini aktarma](storage-solution-small-dataset-low-moderate-network.md).
- Düzenli aralıklarla belirli bir noktaya veri aktarımı mı arıyorsunuz?

    Yanıt Evet ise, [Senaryo 4: düzenli veri aktarımları](storage-solution-periodic-data-transfer.md)bölümünde özetlenen komut dosyalı/programlı seçenekleri kullanın.
- Devam eden, sürekli veri aktarımı mi arıyorsunuz?

    Yanıt Evet ise, [Senaryo 4: düzenli veri aktarımları](storage-solution-periodic-data-transfer.md)içindeki seçenekleri kullanın.

## <a name="data-transfer-feature-in-azure-portal"></a>Azure portal veri aktarımı özelliği

Ayrıca, Azure portal 'de Azure depolama hesabınıza gidebilir ve **veri aktarımı** özelliğini seçebilirsiniz. Ortamınızda ağ bant genişliğini, aktarmak istediğiniz verilerin boyutunu ve veri aktarımı sıklığını belirtin. Verdiğiniz bilgilere karşılık gelen en iyi veri aktarımı çözümlerini görürsünüz. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Depolama Gezgini bir giriş alın](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [AzCopy 'e genel bakış konusunu okuyun](./storage-use-azcopy-v10.md).
- [Hızlı başlangıç: PowerShell ile Blobları karşıya yükleme, indirme ve listeleme](../blobs/storage-quickstart-blobs-powershell.md)
- [Hızlı başlangıç: Azure CLı ile Bloblar oluşturma, indirme ve listeleme](../blobs/storage-quickstart-blobs-cli.md)
- Şunları öğrenin:

    - [Çevrimdışı aktarımlar için Azure Data Box, Azure Data Box disk ve Azure Data Box Heavy](../../databox/index.yml).
    - [Çevrimiçi aktarımlar için Azure Data Box Gateway ve Azure Stack Edge](../../databox-online/index.yml).
- [Azure Data Factory ne olduğunu öğrenin](../../data-factory/copy-activity-overview.md).
- Veri aktarmak için REST API 'Lerini kullanma

    - [.NET 'te](/dotnet/api/overview/azure/storage)
    - [Java üzerinde](/java/api/overview/azure/storage)