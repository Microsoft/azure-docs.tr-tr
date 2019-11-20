---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800178"
---
- Dosyaları, önceden yapılan paylaşımların hiçbirine doğrudan kopyalamayın. Paylaşımın altında bir klasör oluşturmanız ve ardından dosyaları bu klasöre kopyalamanız gerekir.
- *StorageAccount_BlockBlob* ve *StorageAccount_PageBlob* altındaki bir klasör bir kapsayıcıdır. Örneğin, kapsayıcılar *StorageAccount_BlockBlob/Container* ve *StorageAccount_PageBlob/Container*olarak oluşturulur.
- Doğrudan *StorageAccount_AzureFiles* altında oluşturulan her klasör bir Azure dosya paylaşımında çevrilir.
- Bulutta, Kopyalanmakta olan nesneyle aynı ada sahip mevcut bir Azure nesneniz (blob veya bir dosya gibi) varsa Data Box buluttaki dosyanın üzerine yazar.
- *StorageAccount_BlockBlob* ve *StorageAccount_PageBlob* paylaşımlarına yazılan her dosya sırasıyla Blok Blobu ve Sayfa Blobu olarak karşıya yüklenir.
- Azure Blob depolama, dizinleri desteklemez. *StorageAccount_BlockBlob* klasörü altında bir klasör oluşturursanız, sanal klasörler blob adında oluşturulur. Azure dosyaları için gerçek dizin yapısı korunur.
- *StorageAccount_BlockBlob* ve *StorageAccount_PageBlob* klasörlerinde oluşturulan boş dizin hiyerarşisi (herhangi bir dosya olmadan) karşıya yüklenmedi.
- Verileri Azure 'a yüklerken herhangi bir hata oluşursa, hedef depolama hesabında bir hata günlüğü oluşturulur. Karşıya yükleme tamamlandığında bu hata günlüğüne yönelik yol kullanılabilir ve düzeltici eylem gerçekleştirmek için günlüğü gözden geçirebilirsiniz. Karşıya yüklenen verileri doğrulamadan verileri kaynaktan silmeyin.
- Veriler Azure dosyalarına yüklendiğinde dosya meta verileri ve NTFS izinleri korunmaz. Örneğin, veriler kopyalanırken dosyaların *son değiştirilme özniteliği* tutulmaz.
