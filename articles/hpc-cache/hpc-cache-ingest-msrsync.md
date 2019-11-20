---
title: Azure HPC önbellek verileri alma-msrsync
description: Azure HPC önbelleğinde bir BLOB depolama hedefine veri taşımak için msrsync kullanma
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168432"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC Cache Data ınest-msrsync yöntemi

Bu makalede, Azure HPC Cache ile kullanmak üzere verileri bir Azure Blob depolama kapsayıcısına kopyalamak için ``msrsync`` yardımcı programının kullanılmasıyla ilgili ayrıntılı yönergeler sunulmaktadır.

Azure HPC önbelleğiniz için verileri blob depolamaya taşıma hakkında daha fazla bilgi edinmek için [Azure Blob depolama 'ya veri taşıma](hpc-cache-ingest.md)makalesini okuyun.

``msrsync`` Aracı, verileri Azure HPC önbelleği için bir arka uç depolama hedefine taşımak üzere kullanılabilir. Bu araç birden çok paralel ``rsync`` işlemi çalıştırarak bant genişliği kullanımını iyileştirmek için tasarlanmıştır. https://github.com/jbd/msrsync'de GitHub 'dan kullanılabilir.

``msrsync``, kaynak dizinini ayrı "demetlere" ayırır ve sonra her bir Bucket üzerinde bireysel ``rsync`` süreçlerini çalıştırır.

Dört çekirdekli bir VM kullanan ön test, 64 işlemleri kullanırken en iyi verimliliği gösteriyordu. İşlem sayısını 64 olarak ayarlamak için ``-p`` ``msrsync`` seçeneğini kullanın.

``msrsync`` yalnızca yerel birimlerden ve bu birimlere yazabildiğini unutmayın. Kaynak ve hedefin, komutu vermek için kullanılan iş istasyonunda yerel takmaları olarak erişilebilir olması gerekir.

Azure Blob depolamayı Azure HPC Cache ile doldurmak için ``msrsync`` kullanmak için şu yönergeleri izleyin:

1. ``msrsync`` ve önkoşullarını (``rsync`` ve Python 2,6 veya üzeri) yükleyip
1. Kopyalanacak toplam dosya ve dizin sayısını belirleme.

   Örneğin, ```prime.py --directory /path/to/some/directory``` bağımsız değişkenlerle ``prime.py`` (<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>indirerek kullanılabilir) yardımcı programı kullanın.

   ``prime.py``kullanmıyorsanız, GNU ``find`` aracıyla birlikte öğe sayısını aşağıdaki şekilde hesaplayabilirsiniz:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. İşlem başına öğe sayısını öğrenmek için öğe sayısını 64 göre bölün. Komutu çalıştırdığınızda demetlerin boyutunu ayarlamak için bu sayıyı ``-f`` seçeneğiyle kullanın.

1. Dosyaları kopyalamak için ``msrsync`` komutunu verme:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Örneğin, bu komut, 11.000 64 dosyalarını/test/source-Repository 'den/mnt/hpccache/Repository dizinine taşımak için tasarlanmıştır:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
