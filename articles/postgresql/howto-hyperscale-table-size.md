---
title: Tablo boyutunu belirleme-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Dağıtılmış tabloların bir hiper ölçek (Citus) sunucu grubunda gerçek boyutunu bulma
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937677"
---
# <a name="determine-table-and-relation-size"></a>Tablo ve ilişki boyutunu belirleme

PostgreSQL içinde tablo boyutlarını bulmanın olağan yolu, büyük ölçüde `pg_total_relation_size` altında, dağıtılmış tabloların boyutunu Hiperscale (Citus) olarak raporlar.
Bu işlevin hepsi bir hiper ölçek (Citus) sunucu grubunda bulunur. Bu, düzenleyici düğümündeki tabloların boyutunu açığa çıkarır.  Gerçekte, dağıtılmış tablolardaki veriler, düzenleyicinin üzerinde değil, çalışan düğümlerinde (parçalılar) bulunur. Dağıtılmış tablo boyutunun gerçek bir ölçüsü, parça boyutlarının toplamı olarak elde edilir. Hiper ölçek (Citus), bu bilgileri sorgulamak için yardımcı işlevler sağlar.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>İşlev</th>
<th>Döndürülenler</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Tablodaki gerçek verilerin boyutu ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">ana çatal</a>").</li>
<li>İlişki, bir tablonun veya dizinin adı olabilir.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size Plus:</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">boş alan eşlemesinin</a> boyutu</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">görünürlük eşlemesinin</a> boyutu</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size Plus:</p>
<blockquote>
<ul>
<li>Dizin boyutu</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Bu işlevler, bir düğüme bağlanamazlar dışında, standart PostgreSQL [nesne boyutu işlevlerinin](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)üçüne benzer.

## <a name="example"></a>Örnek

Tüm dağıtılmış tabloların boyutlarını listelemek için şu adımları uygulayın:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Çıkış:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Sonraki adımlar

* Sunucu grubunu daha fazla veri tutacak şekilde [ölçeklendirmeye](howto-hyperscale-scale-grow.md) öğrenin.
* Bir Hyperscale (Citus) sunucu grubundaki [tablo türlerini](concepts-hyperscale-nodes.md) ayırt edin.
