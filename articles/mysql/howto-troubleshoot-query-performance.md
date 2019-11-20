---
title: MySQL için Azure veritabanı 'nda sorgu performansı sorunlarını giderme
description: MySQL için Azure veritabanı 'nda sorgu performansı sorunlarını gidermek için AÇıKLA ' yı kullanmayı öğrenin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 02/28/2018
ms.openlocfilehash: d58721f1fedd234b3c400a82035048d8e70f8c32
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972862"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda sorgu performansını profil oluşturma hakkında açıklama kullanma
**Açıkla** sorguları iyileştirmek için kullanışlı bir araçtır. AÇıKLA deyimi, SQL deyimlerinin nasıl yürütüldüğü hakkında bilgi almak için kullanılabilir. Aşağıdaki çıktıda bir açıklama ifadesinin yürütülmesi örneği gösterilmektedir.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Bu örnekte görünebilirler, *anahtarın* değeri null olur. Bu çıktı, MySQL sorgu için iyileştirilmiş bir dizin bulamadığı ve tam tablo taraması gerçekleştirmediği anlamına gelir. **Kimlik** sütununa bir dizin ekleyerek bu sorguyu iyileştirelim.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Yeni AÇıKLA, MySQL 'in artık satır sayısını 1 olarak sınırlandırmak için bir dizin kullandığını gösterir; bu da arama süresini önemli ölçüde kısaltır.
 
## <a name="covering-index"></a>Kapsayan Dizin
Kapsayan Dizin, veri tablolarından değer alımını azaltmak için dizindeki bir sorgunun tüm sütunlarından oluşur. Aşağıdaki **Group By** deyimindeki bir çizim aşağıda verilmiştir.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Çıktıda görünebileceğinden, MySQL hiçbir dizin kullanmaz çünkü uygun dizin yok. Ayrıca, *geçici kullanmayı da gösterir; Dosya sıralamayı kullanarak*MySQL, **Group By** yan tümcesini karşılamak için geçici bir tablo oluşturur.
 
Yalnızca **C2** sütununda bir dizin oluşturmak farklılık yapmaz ve MySQL 'in yine de geçici bir tablo oluşturması gerekir:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Bu durumda, daha fazla veri aramasını ortadan kaldırmak için, **C1** ve **C2** ' de **Kapsanan bir dizin** oluşturulabilir ve bu değerin **C2**"değeri doğrudan dizinde eklenmesi

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Yukarıdaki konularda gösterildiği gibi MySQL artık kapsanan dizini kullanır ve geçici bir tablo oluşturmaktan kaçınmaz. 

## <a name="combined-index"></a>Birleşik Dizin
Birleşik bir dizin birden fazla sütundan değerler içerir ve dizine alınmış sütunların değerlerini birleştirerek sıralanan bir satır dizisi olarak kabul edilebilir. Bu yöntem **Group By** ifadesinde yararlı olabilir.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL, özellikle de birçok satırı sıralamak gerektiğinde oldukça yavaş olan bir *Dosya sıralama* işlemi gerçekleştirir. Bu sorguyu iyileştirmek için, sıralanan her iki sütunda da birleştirilmiş bir dizin oluşturulabilir.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

Şimdi AÇıKLA, MySQL 'in, dizin zaten sıralandığında bu yana ek sıralamayı önlemek için Birleşik dizini kullanabildiğini gösterir.
 
## <a name="conclusion"></a>Sonuç
 
AÇıKLA ve farklı türde dizinlerin kullanılması performansı önemli ölçüde artırabilir. Tabloda bir dizin olması, MySQL 'in sorgular için kullanabilmesi anlamına gelmez. Her zaman varsayımlarınızı doğrulayın ve dizinleri kullanarak sorgularınızı iyileştirin.


## <a name="next-steps"></a>Sonraki adımlar
- En çok ilgili sorularınızın eş yanıtlarını bulmak veya yeni bir soru/cevap göndermek için [MSDN Forumu](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) veya [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)sitesini ziyaret edin.
