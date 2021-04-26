---
title: 'Dışarı aktarma verileri: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Sonuçları ve ara verileri Azure Machine Learning dışına kaydetmek için Azure Machine Learning tasarımcısında verileri dışarı aktar modülünü kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 90755aef66fa51084d83d036722187b61449a6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656915"
---
# <a name="export-data-module"></a>Veri modülünü dışarı aktarma

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

İşlem hatlarınızdan bulut depolama hedeflerine sonuçları, ara verileri ve çalışma verilerini kaydetmek için bu modülü kullanın. 

Bu modül verilerinizi aşağıdaki bulut veri hizmetlerine aktarmayı destekler:

- Azure Blob kapsayıcısı
- Azure Dosya Paylaşımı
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL veritabanı

Verilerinizi dışarı aktarmadan önce, önce Azure Machine Learning çalışma alanınıza bir veri deposu kaydetmeniz gerekir. Daha fazla bilgi için bkz. [Azure Storage hizmetlerindeki verilere erişme](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Dışarı aktarma verilerini yapılandırma

1. **Veri dışa aktarma** modülünü tasarımcıda işlem hattınızı ekleyin. Bu modülü **giriş ve çıkış** kategorisinde bulabilirsiniz.

1. Dışarı aktarma **verilerini** , dışarı aktarmak istediğiniz verileri içeren modüle bağlayın.

1. **Özellikler** bölmesini açmak Için **verileri dışarı aktar** ' ı seçin.

1. **Veri deposu** için, açılan listeden var olan bir veri deposunu seçin. Yeni bir veri deposu da oluşturabilirsiniz. [Azure depolama hizmetleri 'Nde erişim verilerini](../how-to-access-data.md)ziyaret ederek nasıl olduğunu denetleyin.

    > [!NOTE]
    > Belirli bir veri türünün verilerinin başka bir veri türü olarak belirtilen bir SQL veritabanı sütununa aktarılması desteklenmez. Hedef tablonun önce mevcut olması gerekmez.

1. Onay kutusu, **çıktıyı yeniden oluştur**, çalışma zamanında çıktıyı yeniden oluşturmak için modülün yürütülüp yürütülmeyeceğine karar verir. 

    Bu, varsayılan olarak seçili değildir, bu da modülün aynı parametrelerle daha önce yürütülmesi durumunda sistemin, çalışma süresini azaltmak için son çalıştırışınızda çıktıyı yeniden kullanacağı anlamına gelir. 

    Seçilirse, sistem çıktıyı yeniden oluşturmak için modülü yeniden yürütür.

1. Verilerin bulunduğu veri deposundaki yolu tanımlayın. Yol, göreli bir yoldur. `data/testoutput` Örnek olarak, **dışarı aktarma verilerinin** giriş verilerinin, `data/testoutput` modülün **Çıkış ayarlarında** ayarladığınız veri deposunda dışarı aktarılacağı anlamına gelir.

    > [!NOTE]
    > Boş yollar veya **URL yollarına** izin verilmez.


1. **Dosya biçimi** için verilerin depolanacağı biçimi seçin.
 
1. İşlem hattını gönderme.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
