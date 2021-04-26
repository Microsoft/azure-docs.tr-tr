---
title: En iyi performans uygulamaları ve yapılandırma yönergeleri-Azure SQL Edge
description: Azure SQL Edge 'de performans en iyi uygulamaları ve yapılandırma yönergeleri hakkında bilgi edinin
keywords: SQL Edge, veri saklama
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392020"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>En iyi performans uygulamaları ve yapılandırma yönergeleri

Azure SQL Edge, SQL Edge dağıtımınızın performansını geliştirmek için kullanılabilecek çeşitli özellikler ve yetenekler sunmaktadır. Bu makalede performansı en üst düzeye çıkarmak için bazı en iyi yöntemler ve öneriler sunulmaktadır. 

## <a name="best-practices"></a>En iyi uygulamalar 

### <a name="configure-multiple-tempdb-data-files"></a>Birden çok tempdb veri dosyası yapılandırma

Azure SQL Edge varsayılan olarak kapsayıcı başlatma kapsamında yalnızca bir tempdb veri dosyası oluşturur. Birden çok tempdb veri dosyası oluşturmayı bir dağıtım sonrası yapmayı öneririz. Daha fazla bilgi için, [SQL Server tempdb veritabanındaki ayırma çekişmesini azaltmaya yönelik öneriler](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d)başlıklı makaledeki kılavuza bakın.

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Mümkün olduğunda kümelenmiş columnstore dizinlerini kullanın

IoT ve Edge cihazları, genellikle analiz için bazı zaman penceresinde toplanmış olan yüksek hacimli verileri oluşturma eğilimindedir. Tek tek veri satırları, tüm analizler için nadiren kullanılır. Columnstore dizinleri, bu tür büyük veri kümelerini depolamak ve sorgulamak için idealdir. Bu dizin, sütun tabanlı veri depolama ve sorgu işleme kullanarak geleneksel satır odaklı depolama üzerinden sorgu performansının 10 katına varan bir kazanç elde etmenizi sağlar. Ayrıca, sıkıştırılmamış veri boyutu üzerinde veri sıkıştırmasının 10 kata kadar kazanç elde edebilirsiniz. Daha fazla bilgi için bkz. [columnstore dizinleri](/sql/relational-databases/indexes/columnstore-indexes-overview)

Ayrıca, veri akışı ve veri saklama gibi diğer Azure SQL Edge özellikleri, veri ekleme ve veri kaldırma işlemlerinin bir yanındaki columnstore iyileştirmelerinden faydalanır. 

### <a name="simple-recovery-model"></a>Basit kurtarma modeli

Depolama alanı, uç cihazlarda kısıtlandığından, Azure SQL Edge 'deki tüm kullanıcı veritabanları varsayılan olarak basit kurtarma modelini kullanır. Basit kurtarma modeli, alan gereksinimlerinin küçük tutulması için günlük alanını otomatik olarak geri kazanır, aslında işlem günlüğü alanını yönetme ihtiyacını ortadan kaldırır. Sınırlı depolama alanı bulunan uç cihazlarda bu faydalı olabilir. Basit kurtarma modeli ve kullanılabilir diğer kurtarma modelleri hakkında daha fazla bilgi için bkz. [kurtarma modelleri](/sql/relational-databases/backup-restore/recovery-models-sql-server)

Günlük gönderme ve zaman Içinde geri yükleme gibi işlemler, işlem günlüğü yedeklemeleri gerektiren basit kurtarma modeli tarafından desteklenmez.  

## <a name="advanced-configuration"></a>Gelişmiş yapılandırma 

### <a name="setting-memory-limits"></a>Bellek sınırlarını ayarlama

Azure SQL Edge, arabellek havuzu için 64 GB 'a kadar belleği destekler, ancak ek bellek, SQL Edge kapsayıcısı içinde çalışan uydu işlemlerine gerek kalabilir. Sınırlı belleğe sahip daha küçük Edge cihazlarda, Docker konağının ve diğer kenar işlemlerinin veya modüllerinin düzgün şekilde çalışabilmesi için, SQL Edge kapsayıcılarıyla kullanılabilir belleği sınırlandırmamanız önerilir. SQL Edge için kullanılabilen toplam bellek aşağıdaki mekanizmalar kullanılarak denetlenebilir. 

- SQL Edge kapsayıcıları için kullanılabilir bellek sınırlandırma: SQL Edge kapsayıcısı için kullanılabilen toplam bellek, kapsayıcı çalışma zamanı yapılandırma seçeneği kullanılarak sınırlandırılabilir `--memory` . SQL Edge kapsayıcısı için kullanılabilir belleği sınırlama hakkında daha fazla bilgi için bkz. [bellek, CPU 'lar ve GPU 'lar Ile çalışma zamanı seçenekleri](https://docs.docker.com/config/containers/resource_constraints/).

- Kapsayıcı içindeki SQL işlemi için kullanılabilir belleği sınırlandırma: varsayılan olarak, kapsayıcı içindeki SQL işlemi, kullanılabilir fiziksel RAM 'in yalnızca %80 'ini kullanır. Dağıtımların çoğunluğu için varsayılan yapılandırma iyi olacaktır. Ancak, veri akışı için ek belleğin ve SQL Edge kapsayıcıları içinde çalışan ONNX işlemlerinin gerekli olabileceği senaryolar olabilir. Bu tür senaryolarda, SQL işlemi için kullanılabilir bellek, `memory.memorylimitmb` MSSQL-conf dosyasındaki ayarı kullanılarak denetlenebilir. Daha fazla bilgi için bkz. [MSSQL. conf dosyasını kullanarak yapılandırma](configure.md#configure-by-using-an-mssqlconf-file).

Bellek sınırlarını ayarlarken, bu değeri çok düşük ayarlamamaya dikkat edin. SQL işlemi için yeterli bellek ayarlanmamışsa SQL performansını ciddi bir şekilde etkileyebilir.

### <a name="delayed-durability"></a>Gecikmeli dayanıklılık

Azure SQL Edge 'deki işlemler tamamen dayanıklı, SQL Server varsayılan veya gecikmeli dayanıklı (yavaş işleme olarak da bilinir) olabilir.

Tamamen dayanıklı işlem yürütmeleri zaman uyumludur ve işleme başarılı olarak rapor verir ve yalnızca işlemin günlük kayıtları diske yazıldıktan sonra denetimi istemciye döndürür. Gecikmeli dayanıklı işlem yürütmeleri zaman uyumsuzdur ve işlemin günlük kayıtları diske yazılmadan önce başarılı olarak işleme rapor edilir. İşlemin sürekli olması için işlem günlüğü girdilerinin diske yazılması gerekir. İşlem günlüğü girdileri diske boşaltıldığınızda gecikmeli dayanıklı işlemler dayanıklı hale gelir. 

**Bazı veri kayıplarının** toleranslı veya yavaş depolama ile uç cihazlarda toleranslı olduğu dağıtımlarda, veri alımı ve veri bekletme tabanlı temizleme işlemini iyileştirmek için Gecikmeli dayanıklılık kullanılabilir. Daha fazla bilgi için bkz. [Denetim Işlemi dayanıklılığı](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Linux işletim sistemi yapılandırması 

Bir SQL yüklemesinde en iyi performansı yaşamak için aşağıdaki [Linux Işletim sistemi yapılandırma](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) ayarlarını kullanmayı göz önünde bulundurun.