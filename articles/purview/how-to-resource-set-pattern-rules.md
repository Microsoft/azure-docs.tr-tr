---
title: Kaynak kümesi model kuralları oluşturma
description: Varlıkların kaynak kümelerine nasıl gruplandığını üzerine yazmak için bir kaynak kümesi model kuralı oluşturmayı öğrenin
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 61de2cf2e3ad9175d97378234d62f72ab3517b51
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587842"
---
# <a name="create-resource-set-pattern-rules"></a>Kaynak kümesi model kuralları oluşturma

Ölçekli veri işleme sistemleri genellikle birden çok dosya olarak bir diskte tek bir tabloyu depolar. Bu kavram, kaynak kümeleri kullanılarak Azure purview 'da temsil edilir. Kaynak kümesi, veri kataloğunda, depolamada çok sayıda varlığı temsil eden tek bir nesnedir. Daha fazla bilgi edinmek için bkz. [kaynak kümelerini anlama](concept-resource-sets.md).

Bir depolama hesabı taranırken, Azure purview bir varlık grubunun bir kaynak kümesi olup olmadığını anlamak için tanımlı bir desenler kümesi kullanır. Azure purview 'ın kaynak kümesi gruplandırması, bazı durumlarda verilerinizi doğru şekilde yansıtmayabilir. Kaynak kümesi model kuralları, Azure purview 'ın hangi varlıkların kaynak kümesi olarak gruplandığını ve Katalog içinde nasıl görüntüleneceğini özelleştirmenizi veya geçersiz kılmanızı sağlar.

Şu anda aşağıdaki kaynak türlerinde model kuralları desteklenmektedir:
- Azure Data Lake Storage Gen2
- Azure Blob Depolama
- Azure Dosyaları


## <a name="how-to-create-a-resource-set-pattern-rule"></a>Kaynak kümesi model kuralı oluşturma

Yeni bir kaynak kümesi model kuralı oluşturmak için aşağıdaki adımları izleyin:

1. Yönetim merkezine gidin. Kaynak kümeleri başlığı altındaki menüden **model kuralları** ' nı seçin. Yeni bir kural kümesi oluşturmak için **+ Yeni** seçeneğini belirleyin.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="Yeni kaynak kümesi model kuralı oluştur" border="true":::

1. Kaynak kümesi model kuralınızın kapsamını girin. Depolama hesabı türünü ve bir kural kümesi oluşturmak istediğiniz depolama hesabının adını seçin. Her kural kümesi, **klasör yolu** alanında belirtilen bir klasör yolu kapsamına göre uygulanır.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="Kaynak kümesi model kuralları yapılandırması oluşturma" border="true":::

1. Yapılandırma kapsamının bir kuralını girmek için **+ Yeni kural**' ı seçin.

1. Bir kural oluşturmak için aşağıdaki alanlara girin:

   1. **Kural adı:** Yapılandırma kuralının adı. Bu alanın, kuralın uygulandığı varlıkları üzerinde hiçbir etkisi yoktur.

   1. **Tam ad:** Varlıkları yapılandırma kuralıyla eşleştirmek için metin, dinamik replacers ve statik replacers birleşimini kullanan nitelikli bir yol. Bu yol, yapılandırma kuralının kapsamına göredir. Nitelikli adları belirtme hakkında ayrıntılı yönergeler için aşağıdaki [söz dizimi](#syntax) bölümüne bakın.

   1. **Görünen ad:** Varlığın görünen adı. Bu alan isteğe bağlıdır. Bir varlığın katalogda nasıl görüntülendiğini özelleştirmek için düz metin ve statik replacers kullanın. Daha ayrıntılı yönergeler için aşağıdaki [söz dizimi](#syntax) bölümüne bakın.

   1. **Kaynak kümesi olarak gruplandırma:** Etkinleştirilirse, eşleşen kaynak bir kaynak kümesi olarak gruplandırılmaz.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="Yeni yapılandırma kuralı oluştur." border="true":::

1. **Ekle**' ye tıklayarak Kuralı kaydedin.

> [!NOTE]
> Bir model kuralı oluşturulduktan sonra, tüm yeni taramalar alma sırasında kuralı uygular. Veri kataloğunda bulunan varlıklar, birkaç saat sürebilen bir arka plan işlemi aracılığıyla güncelleştirilir. 

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> Model kuralı sözdizimi

Kaynak kümesi model kuralları oluştururken, hangi varlık kurallarının uygulanacağını belirtmek için aşağıdaki sözdizimini kullanın.

### <a name="dynamic-replacers-single-brackets"></a>Dinamik replacers (tek köşeli ayraç)

Tek ayraçlar, bir model kurallarında **dinamik replacers** olarak kullanılır. Biçim kullanarak tam adda dinamik bir değiştirici belirtin `{<replacerName:<replacerType>}` . Eşleşirse, dinamik replacers, varlıkların kaynak kümesi olarak temsil etmesi gerektiğini belirten bir gruplandırma koşulu olarak kullanılır. Varlıklar bir kaynak kümesi halinde gruplandırılmışsa, kaynak kümesi nitelikli yolu, yeniden `{replacerName}` cer 'nin belirtildiği yeri içerecektir.

Örneğin, iki varlık `folder1/file-1.csv` ve `folder2/file-2.csv` kuralla eşleşirse `{folder:string}/file-{NUM:int}.csv` , kaynak kümesi tek bir varlık olur `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Özel durum: dinamik replacers kaynak kümesine gruplandırılmadığınızda

Bir model kuralı için *kaynak kümesi etkinleştirilmiş olarak gruplandırdıysanız* , bulunan değiştirici adı isteğe bağlı bir alandır. `{:<replacerType>}` geçerli sözdizimidir. Örneğin, `file-{:int}.csv` ve için başarıyla eşleştirebilir `file-1.csv` `file-2.csv` ve kaynak kümesi yerine iki farklı varlık oluşturabilir.

### <a name="static-replacers-double-brackets"></a>Statik replacers (çift köşeli ayraç)

Çift köşeli ayraç, bir model kuralının nitelenmiş adında **statik replacers** olarak kullanılır. Biçim kullanarak tam adda bir statik değiştirici belirtin `{{<replacerName>:<replacerType>}}` . Bu eşleştirildiği her benzersiz statik değiştirici değeri kümesi farklı kaynak kümesi gruplandırmaları oluşturacaktır.

Örneğin, iki varlık `folder1/file-1.csv` ve `folder2/file-2.csv` kural ile eşleşirse `{{folder:string}}/file-{NUM:int}.csv` iki kaynak kümesi oluşturulur `folder1/file-{NUM}.csv` `folder2/file-{NUM}.csv` .

Statik replacers, bir model kuralına eşlenen bir varlığın görünen adını belirtmek için kullanılabilir. `{{<replacerName>}}`Bir kuralın görünen adında kullanılması, varlık adında eşleşen değeri kullanır.

### <a name="available-replacement-types"></a>Kullanılabilir değiştirme türleri

Statik ve dinamik replacers 'da kullanılabilecek kullanılabilir türler aşağıda verilmiştir:

| Tür | Yapı |
| ---- | --------- |
| string | Boşluk gibi sınırlayıcılar dahil olmak üzere 1 veya daha fazla Unicode karakter serisi. |
| int | 1 veya daha fazla 0-9 ASCII karakter serisi, 0 önekli (ör. 0001) olabilir. |
| guid | [RFC 4122](https://tools.ietf.org/html/rfc4122)' de defineddefa olarak bir UUID 'nin bir dizi 32 veya 8-4-4-4-12 dize temsili. |
| date | İsteğe bağlı ayırıcıları olan 6 veya 8 0-9 ASCII karakter serisi: YYYYMMDD, yyyy-aa-gg, YGG, yy-aa-gg, [RFC 3339](https://tools.ietf.org/html/rfc3339)' de belirtildi. |
| time | İsteğe bağlı ayırıcıları olan 4 veya 6 0-9 ASCII karakter serisi: ssmm, HH: mm, Ssmmss, HH: mm: ss, [RFC 3339](https://tools.ietf.org/html/rfc3339)' de belirtildi. |
| timestamp | İsteğe bağlı ayırıcıları olan 12 veya 14 0-9 ASCII karakter serisi: yyyy-mm-ddTHH: mm, yyyyMMddhhmm, yyyy-mm-ddTHH: mm: ss, yyyyaaggssddss, [RFC 3339](https://tools.ietf.org/html/rfc3339)' de belirtildi. |
| boolean | ' True ' veya ' false ' içerebilir, büyük/küçük harfe duyarsız olur. |
| sayı | 0 veya daha fazla 0-9 ASCII karakter serisi, 0 ön eki (ör. 0001) ve ardından isteğe bağlı olarak bir nokta '. ' ve bir dizi 1 veya daha fazla 0-9 ASCII karakteri olabilir, bu 0 postfixed (ör. 100) olabilir. |
| Onaltılık | 0-1 ve A-F kümesinden 1 veya daha fazla ASCII karakter serisi, değer 0 ön eki olabilir |
| locale | [RFC 5646](https://tools.ietf.org/html/rfc5646)' de belirtilen sözdizimiyle eşleşen bir dize. |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>Uygulanan kaynak kümesi model kuralları sırası

Aşağıda, düzen kurallarını uygulamak için işlemlerin sırası verilmiştir:

1. Bir varlık iki kuralla eşleşiyorsa, daha belirli kapsamlar öncelikli olur. Örneğin, bir kapsamdaki kurallar `container/folder` kapsamdaki kuralların önüne uygulanacaktır `container` .

1. Belirli bir kapsamdaki kuralların sırası. Bu, UX içinde düzenlenebilir.

1. Bir varlık belirtilen herhangi bir kuralla eşleşmezse varsayılan kaynak kümesi buluşsal yöntemler geçerlidir.

## <a name="examples"></a>Örnekler

### <a name="example-1"></a>Örnek 1

Tam ve Delta yüklere SAP veri ayıklama

#### <a name="inputs"></a>Girişler

Dosyalarý

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>Model kuralı

**Kapsam:**`https://myazureblob.blob.core.windows.net/bar/`

**Görünen ad:** ' Dış müşteri '

**Tam ad:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Kaynak kümesi:** doğru

#### <a name="output"></a>Çıktı

Bir kaynak kümesi varlığı

**Görünen ad:** Dış müşteri

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Örnek 2

Avro biçiminde IoT verileri

#### <a name="inputs"></a>Girişler

Dosyalarý

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>Model kuralları

**Kapsam:**`https://myazureblob.blob.core.windows.net/bar/`

Kural 1

**Görünen ad:** ' makine-89 '

**Tam ad:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Kaynak kümesi:** doğru

Kural 2

**Görünen ad:** ' Makine-90 '

**Tam ad:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**Kaynak kümesi:** doğru

#### <a name="outputs"></a>Çıkışlar

2 kaynak kümesi

Kaynak kümesi 1

**Görünen ad:** makine-89

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Kaynak kümesi 2

**Görünen ad:** makine-90

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Örnek 3

Avro biçiminde IoT verileri

#### <a name="inputs"></a>Girişler

Dosyalarý

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Model kuralı

**Kapsam:**`https://myazureblob.blob.core.windows.net/bar/`

**Görünen ad:** ' Makine-{{MachineID}} '

**Tam ad:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Kaynak kümesi:** doğru

#### <a name="outputs"></a>Çıkışlar

Kaynak kümesi 1

**Görünen ad:** makine-89

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Kaynak kümesi 2

**Görünen ad:** makine-90

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Örnek 4

Kaynak kümelerine gruplandırma

#### <a name="inputs"></a>Girişler

Dosyalarý

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Model kuralı

**Kapsam:**`https://myazureblob.blob.core.windows.net/bar/`

**Görünen ad:**`Machine-{{machineid}}`

**Tam ad:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Kaynak kümesi:** false

#### <a name="outputs"></a>Çıkışlar

4 bireysel varlık

Varlık 1

**Görünen ad:** makine-89

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Varlık 2

**Görünen ad:** makine-89

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Varlık 3

**Görünen ad:** makine-89

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Varlık 4

**Görünen ad:** makine-90

**Tam ad:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Sonraki adımlar

[Azure Data Lake Gen2 Storage hesabını kaydederek ve tarayarak](register-scan-adls-gen2.md)başlayın.