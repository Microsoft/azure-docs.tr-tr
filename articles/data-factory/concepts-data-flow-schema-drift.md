---
title: Eşleme veri akışı 'nda şema kayması
description: Şema kayması ile Azure Data Factory dayanıklı veri akışları oluşturun
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027491"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Eşleme veri akışı 'nda şema kayması

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Şema kayması, kaynaklarınızın genellikle meta verileri değiştirme durumdur. Alanlar, sütunlar ve türler anında eklenebilir, kaldırılabilir veya değiştirilebilir. Şema kayması için işlem yapılmadan veri akışınız, yukarı akış veri kaynağı değişikliklerine karşı savunmasız hale gelir. Gelen sütunlar ve alanlar bu kaynak adlarına bağlı olduğundan, tipik ETL desenleri başarısız olur.

Şema driine karşı korunmak için bir veri akışı aracında tesislerin, bir veri mühendisi olarak şunları yapmasına olanak tanımak önemlidir:

* Değişebilir alan adlarına, veri türlerine, değerlere ve boyutlara sahip kaynakları tanımlama
* Sabit kodlanmış alanlar ve değerler yerine veri desenleriyle çalışkullanılabilecek dönüştürme parametreleri tanımlayın
* Adlandırılmış alanları kullanmak yerine gelen alanlarla eşleşecek desenleri anlayan ifadeler tanımlayın

Azure Data Factory, yürütme durumundan yürütmeye değişen esnek şemaları, veri akışlarınızı yeniden derlemenize gerek kalmadan genel veri dönüştürme mantığı oluşturabilmeniz için yerel olarak destekler.

Akış genelinde şema drını kabul etmek için veri akışınızda bir mimari karar vermeniz gerekir. Bunu yaptığınızda, kaynaklardaki şema değişikliklerine karşı koruma sağlayabilirsiniz. Ancak, veri akışınız boyunca sütunlarınızın ve türlerinizi erken bağlamayı kaybedersiniz. Azure Data Factory, şema kuruta akışlarını geç bağlama akışları olarak değerlendirir. bu nedenle, dönüştürmelerinizi oluştururken düzeltebilecekler sütun adları, akış genelindeki şema görünümlerinde sizin için kullanılamaz.

Bu videoda, veri akışının şema kayması özelliği ile ADF 'de kolayca oluşturabileceğiniz karmaşık çözümlerin bazılarına giriş sunulmaktadır. Bu örnekte, esnek veritabanı şemalarına göre yeniden kullanılabilir desenler oluşturacağız:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Kaynaktaki şema kayması

Kaynak tanımınızdan veri akışınıza gelen sütunlar, kaynak projeksiyonda mevcut olmadığında "düzeltebilecekler" olarak tanımlanır. Kaynak dönüşümünüzü yansıtma sekmesinden Kaynak projeksiyonunu görüntüleyebilirsiniz. Kaynağınız için bir veri kümesi seçtiğinizde, ADF otomatik olarak veri kümesinden şemayı alır ve bu veri kümesi şema tanımından bir yansıtma oluşturur.

Kaynak dönüşümünde, şema kayması, veri kümesi şemanız tanımlı olmayan okuma sütunları olarak tanımlanmıştır. Şema drmasını etkinleştirmek için, kaynak dönüşümünüze **şema Drçıkmasına Izin ver** ' i işaretleyin.

![Şema DRFT kaynağı](media/data-flow/schemadrift001.png "Şema DRFT kaynağı")

Şema drında, tüm gelen alanlar yürütme sırasında kaynağınızdan alınır ve tüm akış havuza gönderilir. Varsayılan olarak, *düzeltebilecekler sütunları* olarak bilinen tüm yeni algılanan sütunlar dize veri türü olarak gelir. Veri akışınız için düzeltebilecekler sütunların veri türlerini otomatik olarak çıkarması gerekirse, kaynak ayarlarınızda **düzeltebilecekler sütun türlerini** kontrol edin.

## <a name="schema-drift-in-sink"></a>Havuzda DRFT şeması

Bir havuz dönüşümünde şema kayması, havuz veri şemasında tanımlananla ilgili ek sütunlar yazdığınızda oluşur. Şema kayması 'nı etkinleştirmek için havuz dönüşümünüzün **şema Drçıkmasına Izin ver** ' i işaretleyin.

![Şema DRFT havuzu](media/data-flow/schemadrift002.png "Şema DRFT havuzu")

Şema kayması etkinleştirilirse, eşleme sekmesindeki **otomatik eşleme** kaydırıcısının açık olduğundan emin olun. Bu kaydırıcı üzerinde, tüm gelen sütunlar hedefe yazılır. Aksi takdirde, düzeltebilecekler sütunları yazmak için kural tabanlı eşleme kullanmanız gerekir.

![Havuz otomatik eşleme](media/data-flow/automap.png "Havuz otomatik eşleme")

## <a name="transforming-drifted-columns"></a>Düzeltebilecekler sütunlarını dönüştürme

Veri akışınız düzeltebilecekler sütunlara sahip olduğunda, bu dosyalara dönüşümlerinizi aşağıdaki yöntemlerle erişebilirsiniz:

* `byPosition` `byName` Bir sütuna ada veya konum numarasına göre açıkça başvuruda bulunmak için ve ifadelerini kullanın.
* Türetilmiş bir sütuna veya toplama dönüşümüne, ad, akış, konum, başlangıç veya tür birleşimlerinde eşleşecek bir sütun stili ekleyin
* Bir SELECT veya Sink dönüşümünde kural tabanlı eşlemeyi, bir model aracılığıyla düzeltebilecekler sütunları sütunlara göre diğer adlarla eşleşecek şekilde ekleyin

Sütun desenlerinin nasıl uygulanacağı hakkında daha fazla bilgi için bkz. [eşleme veri akışında sütun desenleri](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Map düzeltebilecekler sütunları hızlı eylemi

Düzeltebilecekler sütunlara açıkça başvurmak için, veri önizleme hızlı eylemi aracılığıyla bu sütunlara yönelik eşlemeleri hızlıca oluşturabilirsiniz. [Hata ayıklama modu](concepts-data-flow-debug-mode.md) açıldıktan sonra, veri önizleme sekmesine gidin ve **Yenile** ' ye tıklayarak bir veri önizlemesi getirin. Data Factory, düzeltebilecekler sütunlarının mevcut olduğunu algılarsa, **Map düzeltebilecekler** ' e tıklayabilir ve şema görünümlerinde tüm düzeltebilecekler sütunlarına başvurmanıza olanak tanıyan bir türetilmiş sütun oluşturabilirsiniz.

![Ekran görüntüsü, Map düzeltebilecekler ile çağrılan veri önizleme sekmesini gösterir.](media/data-flow/mapdrifted1.png "Düzeltebilecekler eşle")

Oluşturulan türetilmiş sütun dönüşümünde, her düzeltebilecekler sütunu algılanan ad ve veri türü ile eşlenir. Yukarıdaki veri önizlemede, ' Movieıd ' sütunu bir tamsayı olarak algılanır. **Map düzeltebilecekler** tıklatıldıktan sonra, Movieıd, türetilmiş sütunda olarak tanımlanır `toInteger(byName('movieId'))` ve aşağı akış dönüşümlerinde şema görünümlerine dahil edilir.

![Ekran görüntüsü türetilmiş sütunun ayarlar sekmesini gösterir.](media/data-flow/mapdrifted2.png "Düzeltebilecekler eşle")

## <a name="next-steps"></a>Sonraki adımlar
[Veri akışı Ifade dilinde](data-flow-expression-functions.md), sütun desenleri ve "byName" ve "byposition" gibi şema kayması için ek tesisler bulacaksınız.
