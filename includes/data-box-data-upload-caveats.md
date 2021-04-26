---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792458"
---
- Dosyaları, önceden yapılan paylaşımların hiçbirine doğrudan kopyalamayın. Paylaşımın altında bir klasör oluşturmanız ve ardından dosyaları bu klasöre kopyalamanız gerekir.
- *StorageAccount_BlockBlob* ve *StorageAccount_PageBlob* altındaki bir klasör bir kapsayıcıdır. Örneğin, kapsayıcılar *StorageAccount_BlockBlob/Container* ve *StorageAccount_PageBlob/Container* olarak oluşturulur.
- Doğrudan *StorageAccount_AzureFiles* altında oluşturulan her klasör bir Azure dosya paylaşımında çevrilir.
- Kopyalanmakta olan bir nesne, zaten bulutta olan bir blob veya dosya gibi Azure nesnesiyle aynı ada sahipse Data Box buluttaki dosyanın üzerine yazar.
- *StorageAccount_BlockBlob* ve *StorageAccount_PageBlob* paylaşımlarına yazılan her dosya sırasıyla Blok Blobu ve Sayfa Blobu olarak karşıya yüklenir.
- Azure Blob depolama, dizinleri desteklemez. *StorageAccount_BlockBlob* klasörü altında bir klasör oluşturursanız, sanal klasörler blob adında oluşturulur. Azure dosyaları için gerçek dizin yapısı korunur.
- *StorageAccount_BlockBlob* ve *StorageAccount_PageBlob* klasörlerinde oluşturulan boş dizin hiyerarşisi (herhangi bir dosya olmadan) karşıya yüklenmedi.
- Verileri Azure 'a yüklerken herhangi bir hata oluşursa, hedef depolama hesabında bir hata günlüğü oluşturulur. Bu hata günlüğü yolu, karşıya yükleme tamamlandığında kullanılabilir ve düzeltici eylem gerçekleştirmek için günlüğü gözden geçirebilirsiniz. Karşıya yüklenen verileri doğrulamadan verileri kaynaktan silmeyin.
- Dosya [ACL 'leri, öznitelikleri ve zaman damgalarını Azure Data Box koruma konusunda](../articles/databox/data-box-file-acls-preservation.md)rehberlik kullanılarak veriler Azure dosyalarına yüklendiğinde dosya meta VERILERI ve NTFS izinleri korunabilir.
