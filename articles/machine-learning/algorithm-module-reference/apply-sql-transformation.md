---
title: SQL dönüşümünü Uygula
titleSuffix: Azure Machine Learning service
description: Verileri dönüştürmek üzere giriş veri kümelerinde bir SQLite sorgusu çalıştırmak için Azure Machine Learning hizmetinde SQL dönüştürme modülünü uygulama ' yı nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 62c61d589324fe8364fe4630b3cf2cc64e1860b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493924"
---
# <a name="apply-sql-transformation"></a>SQL dönüşümünü Uygula

Bu makalede bir Azure Machine Learning Designer (Önizleme) modülü açıklanır.

SQL dönüştürme modülünü Uygula ' yı kullanarak şunları yapabilirsiniz:
  
-   Sonuçlar için tablo oluşturma ve veri kümelerini taşınabilir bir veritabanına kaydetme.  
  
-   Veri türlerinde özel dönüşümler gerçekleştirin veya toplamalar oluşturun.  
  
-   Verileri filtrelemek veya değiştirmek ve sorgu sonuçlarını veri tablosu olarak döndürmek için SQL sorgu deyimlerini yürütün.  

> [!IMPORTANT]
> Bu modülde kullanılan SQL altyapısı **SQLite**' dur. SQLite sözdizimi hakkında daha fazla bilgi için daha fazla bilgi için bkz. [SQLite tarafından anlaşıldığı SQL](https://www.sqlite.org/index.html) .  

## <a name="how-to-configure-apply-sql-transformation"></a>SQL dönüşümünü Uygula ' yı yapılandırma  

Modülün giriş olarak üç veri kümesi olabilir. Her giriş bağlantı noktasına bağlı veri kümelerine başvurduğunuzda, `t1`, `t2`ve `t3`adlarını kullanmanız gerekir. Tablo numarası, giriş bağlantı noktasının dizinini gösterir.  
  
Kalan parametre, SQLite sözdizimini kullanan bir SQL sorgusudur. **SQL betiği** metin kutusuna birden çok satır yazarken her bir ifadeyi sonlandırmak için noktalı virgül kullanın. Aksi takdirde, satır sonları boşluklara dönüştürülür.  

Bu modül, SQLite sözdiziminin tüm standart deyimlerini destekler. Desteklenmeyen deyimlerin bir listesi için [Teknik notlar](#technical-notes) bölümüne bakın.

##  <a name="technical-notes"></a>Teknik notlar  

Bu bölümde, sık sorulan soruların uygulama ayrıntıları, ipuçları ve yanıtları yer almaktadır.

-   Bağlantı noktası 1 üzerinde her zaman bir giriş gereklidir.  
  
-   Boşluk veya diğer özel karakterler içeren sütun tanımlayıcıları için, `SELECT` veya `WHERE` yan tümcelerinde bulunan sütuna başvuru yaparken her zaman köşeli ayraç veya çift tırnak işareti içinde sütun tanımlayıcısını çevreler.  
  
### <a name="unsupported-statements"></a>Desteklenmeyen deyimler  

SQLite, ANSI SQL standardının çoğunu desteklese de, ticari ilişkisel veritabanı sistemleri tarafından desteklenen birçok özellik içermez. Daha fazla bilgi için bkz. [SQLite tarafından anlaşıldığı gibi SQL](http://www.sqlite.org/lang.html). Ayrıca, SQL deyimleri oluştururken aşağıdaki kısıtlamalara dikkat edin:  
  
- SQLite, en ilişkisel veritabanı sistemlerinde olduğu gibi bir sütuna bir tür atamak yerine, değerler için dinamik yazma kullanır. Kesin olarak yazılmış ve örtük tür dönüştürmeye izin veren.  
  
- `LEFT OUTER JOIN` uygulanmış, ancak `RIGHT OUTER JOIN` veya `FULL OUTER JOIN`değil.  

- `ALTER TABLE` komutuyla `RENAME TABLE` ve `ADD COLUMN` deyimlerini kullanabilirsiniz, ancak `DROP COLUMN`, `ALTER COLUMN`ve `ADD CONSTRAINT`dahil diğer yan tümceler desteklenmez.  
  
- SQLite içinde bir görünüm oluşturabilirsiniz, ancak bundan sonra görünümler salt okunurdur. Bir görünümde `DELETE`, `INSERT`veya `UPDATE` ifadesini yürütemezsiniz. Ancak, bir görünüm üzerinde `DELETE`, `INSERT`veya `UPDATE` ve Tetikleyicinin gövdesinde diğer işlemleri gerçekleştirmek için bir girişim tetikleyen bir tetikleyici oluşturabilirsiniz.  
  

Resmi SQLite sitesinde sağlanan desteklenmeyen işlevlerin listesine ek olarak, aşağıdaki wiki desteklenmeyen diğer özelliklerin bir listesini sağlar: [SQLite-desteklenmeyen SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
