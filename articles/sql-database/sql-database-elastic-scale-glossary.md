---
title: Elastik veritabanı araçları sözlüğü
description: Elastik veritabanı araçları için kullanılan koşulların açıklaması
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823621"
---
# <a name="elastic-database-tools-glossary"></a>Elastik veritabanı araçları sözlüğü

Azure SQL veritabanı 'nın bir özelliği olan [elastik veritabanı araçları](sql-database-elastic-scale-introduction.md)için aşağıdaki terimler tanımlanmıştır. Araçlar, parça [haritalarını](sql-database-elastic-scale-shard-map-management.md)yönetmek için kullanılır ve [istemci kitaplığı](sql-database-elastic-database-client-library.md), [bölünmüş birleştirme aracını](sql-database-elastic-scale-overview-split-and-merge.md), [elastik havuzları](sql-database-elastic-pool.md)ve [sorguları](sql-database-elastic-query-overview.md)dahil eder. 

Bu terimler, [elastik veritabanı araçlarını kullanarak parça ekleme](sql-database-elastic-scale-add-a-shard.md) ve [RecoveryManager sınıfını kullanarak parça eşleme sorunlarını çözme](sql-database-elastic-database-recovery-manager.md)bölümünde kullanılır.

![Elastik ölçek terimleri][1]

**Veritabanı**: BIR Azure SQL veritabanı. 

**Verilere bağımlı yönlendirme**: bir uygulamanın belirli bir parça anahtarı verilen bir parçaya bağlanmasını sağlayan işlevsellik. Bkz. [verilere bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md). **[Çok parçalı sorgu](sql-database-elastic-scale-multishard-querying.md)** ile karşılaştırın.

**Küresel parça Haritası**: parça **kümesi**içindeki parçalar ve ilgili parçalar arasındaki eşleme. Küresel parça eşleme, parça **eşleme Yöneticisi**'nde depolanır. Yerel parça **eşlemesiyle**karşılaştırın.

Parça **haritasını Listele**: parçalama anahtarlarının ayrı ayrı eşlendiği parça haritası. Aralık parça **Haritası**ile karşılaştırın.   

**Yerel parça Haritası**: bir parça üzerinde depolanan yerel parça Haritası, parça üzerinde yer alan parçalar için eşlemeler içerir.

**Çok parçalı sorgu**: birden çok parçaya karşı bir sorgu verme özelliği; sonuç kümeleri UNıON ALL semantiği ("fan arası sorgu" olarak da bilinir) kullanılarak döndürülür. **Verilere bağımlı yönlendirme**ile karşılaştırın.

**Çok kiracılı** ve **tek kiracılı**: Bu, tek kiracılı bir veritabanını ve çok kiracılı bir veritabanını gösterir:

![Tek ve çok kiracılı veritabanları](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Parçalı tek ve çok kiracılı veritabanlarının temsili **aşağıda verilmiştir.** 

![Tek ve çok kiracılı veritabanları](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Aralık parça Haritası**: parça dağıtım stratejisinin birden çok bitişik değer aralığını temel aldığı parça haritası. 

**Başvuru tabloları**: parçalı olmayan ancak parçalar arasında çoğaltılan tablolar. Örneğin, posta kodları bir başvuru tablosunda depolanabilir. 

Parça **: parçalı**bir veri kümesinden veri depolayan BIR Azure SQL veritabanı. 

Parça **esnekliği: hem** **yatay ölçeklendirmeyi** hem de **Dikey ölçeklendirmeyi**gerçekleştirme özelliği.

Parçalı **Tablolar**: parçalı, yani verileri parçalara ayırma anahtar değerlerine bağlı olarak parçalar arasında dağıtılan tablolar. 

Parçalı **anahtar**: verilerin parçalar arasında nasıl dağıtıldığını belirleyen bir sütun değeri. Değer türü şunlardan biri olabilir: **int**, **bigint**, **varbinary**veya **uniqueidentifier**. 

Parça **kümesi**: parça eşleme yöneticisinde aynı parça eşlemesine sahip olan parçaların koleksiyonu.  

**Shardlet**: parça üzerindeki bir parçalama anahtarının tek bir değeriyle ilişkili tüm veriler. Parçalara ayırma, parçalı tabloları yeniden dağıttığında mümkün olan en küçük veri hareketi birimidir. 

Parça **Haritası**: parçalı anahtarlar ve ilgili parçalar arasındaki eşlemeler kümesi.

Parça **eşleme Yöneticisi**: bir veya daha fazla parça kümesi için parça haritaları, parça konumları ve eşlemeler içeren bir yönetim nesnesi ve veri deposu.

![Eşlemeler][2]

## <a name="verbs"></a>Eylemlerinin
**Yatay ölçekleme**: aşağıda gösterildiği gibi, parça eşlemesine parçalar ekleyerek veya kaldırarak parçalar koleksiyonunu genişletme (veya içinde).

![Yatay ve dikey ölçekleme][3]

**Birleştir**: parçaları iki parçadan bir parça ile taşıma ve parça haritasını buna göre güncelleştirme eylemi.

**Parçama taşı**: tek bir parçayı farklı bir parçaya taşıma eylemi. 

Parça **: aynı**yapılandırılmış verileri, parçalı bir anahtara göre birden çok veritabanı arasında yatay olarak bölümlemenin görevi.

**Böl**: birden fazla parçayı bir parçadan diğerine (genellikle yeni) parça olarak taşıma eylemi. Parça anahtar, Kullanıcı tarafından bölünmüş nokta olarak sağlanır.

**Dikey ölçekleme**: tek bir parçanın işlem boyutunu artırma (veya azaltma) işlemi. Örneğin, bir parçayı standart iken Premium olarak değiştirme (daha fazla bilgi işlem kaynağı ile sonuçlanır). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

