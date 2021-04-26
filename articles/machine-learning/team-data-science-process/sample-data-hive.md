---
title: Azure HDInsight Hive tablolarında örnek veriler-ekip veri bilimi Işlemi
description: Azure HDInsight Hive tablolarında depolanan ve verileri, analiz için daha yönetilebilir bir boyuta azaltmak üzere Hive sorguları kullanan, örnek veri verileri.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321893"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Azure HDInsight Hive tablolarındaki örnek veriler
Bu makalede, Azure HDInsight Hive tablolarında depolanan verileri analiz için daha yönetilebilir bir boyuta düşürmek üzere Hive sorguları kullanılarak nasıl azaltalacağı açıklanır. Üç adet popuya kullanılan örnekleme yöntemini içerir:

* Tekdüzen rastgele örnekleme
* Gruplara göre rastgele örnekleme
* Bağlı örnekleme

**Verileriniz neden örnekleyebilirsiniz?**
Çözümlemeyi planladığınız veri kümesi büyükse, daha küçük ancak temsili ve daha yönetilebilir bir boyutla azaltmak için verileri düşürmek genellikle iyi bir fikirdir. Aşağı örnekleme, veri anlama, araştırma ve özellik mühendisliğini kolaylaştırır. Ekip veri bilimi Işlemindeki rolü, veri işleme işlevlerinin ve makine öğrenimi modellerinin hızlı prototipini etkinleştirmektir.

Bu örnekleme görevi, [ekip veri bilimi işlemindeki (TDSP)](./index.yml)bir adımdır.

## <a name="how-to-submit-hive-queries"></a>Hive sorguları gönderme
Hive sorguları Hadoop kümesinin baş düğümündeki Hadoop Command-Line konsolundan gönderilebilir.  Hadoop kümesinin baş düğümünde oturum açın, Hadoop Command-Line konsolunu açın ve Hive sorgularını buradan gönderebilirsiniz. Hadoop Command-Line konsolunda Hive sorguları gönderme yönergeleri için bkz. [Hive sorguları gönderme](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Tekdüzen rastgele örnekleme
Tek biçimli rastgele örnekleme, veri kümesindeki her bir satırın örneklenme olasılığını eşit olduğu anlamına gelir. Bu, iç "Seç" sorgusunda veri kümesine bir ek alan S_SAYI_ÜRET () eklenerek ve "Seç" dıştaki bu rasgele alanda bu koşulu sorgulayarak uygulanabilir.

Örnek bir sorgu aşağıda verilmiştir:

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

Burada, `<sample rate, 0-1>` kullanıcıların örneklemek istediği kayıt oranını belirtir.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Gruplara göre rastgele örnekleme
Kategorik verileri örnekleme sırasında, kategorik değişkenin bir değeri için tüm örnekleri dahil etmek veya hariç tutmak isteyebilirsiniz. Bu örnekleme sıralaması "gruba göre örnekleme" olarak adlandırılır. Örneğin, NY, MA, CA, NJ ve PA gibi değerler içeren kategorik bir değişkeniniz "*durum*" ise, örneklenip örneklenmeseler her bir durum için kayıtların birlikte olmasını istersiniz.

Gruba göre örnekleyerek örnek bir sorgu aşağıda verilmiştir:

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Bağlı örnekleme
Elde edilen örneklerin üst nüfusununde aynı oranda mevcut olan kategorik değerleri varsa rastgele örnekleme kategorik bir değişkene göre belirlenir. Yukarıdaki gibi aynı örneği kullanarak, verilerinizin durumlara göre şu gözlemleri olduğunu varsayalım: NJ 100 gözlemlere sahiptir, NY, 60 gözlemlere sahiptir ve WA, 300 gözlemlerdir. 0,5 olacak şekilde yapılan örnekleme oranını belirtirseniz, elde edilen örnek sırasıyla yaklaşık 50, 30 ve 150 olarak NJ, NY ve WA 'da gözlemlenebilir.

Örnek bir sorgu aşağıda verilmiştir:

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

Hive 'de kullanılabilen daha gelişmiş örnekleme yöntemleri hakkında daha fazla bilgi için bkz. [Languagemanual örnekleme](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).