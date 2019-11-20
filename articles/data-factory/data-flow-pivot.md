---
title: Azure Data Factory eşleme veri akışı Özet dönüştürmesi
description: Azure Data Factory eşleme veri akışı Özet dönüşümünü kullanarak satırlardan sütunlara kadar özet verileri özetleme
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e59f0623b898fedada63e51fabbaf88d8b17f59d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387788"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure Data Factory Pivot dönüşümü


Bir veya daha fazla gruplama sütununun ayrı sütunlara dönüştürülebileceği bir veya daha fazla gruplandırma sütununun bulunduğu bir toplama olarak ADF veri akışında Pivot kullanın. Temelde, satır değerlerini yeni sütunlara Özet olarak ekleyebilirsiniz (verileri meta verilere dönüştürebilirsiniz).

![Özet seçenekleri](media/data-flow/pivot1.png "Pivot 1")

## <a name="group-by"></a>Gruplandırma ölçütü

![Özet seçenekleri](media/data-flow/pivot2.png "Pivot 2")

İlk olarak, pivot toplamadır için gruplandırmak istediğiniz sütunları ayarlayın. Sütun listesinin yanındaki + işaretiyle birlikte 1 ' den fazla sütun ayarlayabilirsiniz.

## <a name="pivot-key"></a>Özet anahtar

![Özet seçenekleri](media/data-flow/pivot3.png "Pivot 3")

Pivot tuşu, ADF 'nin satırdan sütuna kadar Pivot olacağını belirten sütundur. Varsayılan olarak, bu alan için veri kümesindeki her benzersiz değer bir sütuna Özet olur. Ancak, isteğe bağlı olarak sütun değerlerine Pivot eklemek istediğiniz veri kümesinden değerleri girebilirsiniz. Bu, oluşturulacak yeni sütunları belirleyecek olan sütundur.

## <a name="pivoted-columns"></a>Özetleme sütunları

![Özet seçenekleri](media/data-flow/pivot4.png "Pivot 4")

Son olarak, özetleme değerleri için kullanmak istediğiniz toplamayı ve yeni çıkış projeksiyonundaki sütunları dönüşümden nasıl görüntülenmesini istediğinizi tercih edersiniz.

Seçim Satır değerlerinden her yeni sütun adına eklenmek üzere ön ek, orta ve son ek içeren bir adlandırma deseninin ayarlanabilir olmasını sağlayabilirsiniz.

Örneğin, "bölge" ile "Sales" özetleme, her satış değerinden yeni sütun değerlerinin oluşmasına neden olur, yani "25", "50", "1000" vb. Bununla birlikte, "Sales-" önek değerini ayarlarsanız her sütun değeri, değerin başına "Sales-" ekler.

![Özet seçenekleri](media/data-flow/pivot5.png "Özet 5")

Sütun düzenlemesini "normal" olarak ayarlamak, özetleme sütunlarının tümünü toplanmış değerlerle birlikte gruplandırır. Sütun düzenleme "yan yana" olarak ayarlandığında sütun ve değer arasında alternatif olur.

### <a name="aggregation"></a>Toplama

Özet değerler için kullanmak istediğiniz toplamayı ayarlamak için, özetleme sütunları bölmesinin altındaki alana tıklayın. Bir toplama ifadesi oluşturabileceğiniz ve yeni toplanmış değerlerinizle ilgili açıklayıcı bir diğer ad sağlayabileceğiniz ADF veri akışı ifade oluşturucusuna giriş yapmanız gerekir.

@No__t-0 Ifade Oluşturucusu 'ndaki Özet sütun dönüşümlerini anlatmak için ADF veri akışı Ifade dilini kullanın.

## <a name="pivot-metadata"></a>Özet meta verileri

Özet dönüştürme, gelen verilerinize göre dinamik olan yeni sütun adları oluşturacaktır. Pivot tuşu her yeni sütun adı için değerleri üretir. Tek tek değerler belirtmezseniz ve Pivot anahtarınıza her benzersiz değer için dinamik sütun adları oluşturmak istiyorsanız, Kullanıcı arabirimi Inceleme ' de meta verileri görüntülemez ve havuz dönüşümüne hiçbir sütun yayma olmaz. Özet anahtarın değerlerini ayarlarsanız, ADF yeni sütun adlarını belirleyebilir ve bu sütun adları Inceleme ve havuz eşlemesinde sizin için kullanılabilir olacaktır.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Dinamik sütunlardan yeni bir model oluştur

Pivot, satır değerlerine göre dinamik olarak yeni sütun adları oluşturur. Bu yeni sütunları, daha sonra veri akışınızda başvurulabilen meta verilere dönüştürebilirsiniz. Bunu yapmak için veri önizleme sekmesine tıklayın. Özet dönüşümünüzün oluşturduğu tüm yeni sütunlar, Tablo üstbilgisindeki bir "düzeltebilecekler" simgesiyle birlikte görüntülenir. Bu yeni sütunları meta verilere dönüştürmek için "Map düzeltebilecekler" düğmesine tıklayın ve bunları veri akışının modelinin bir parçası haline getirin.

![Özet sütunlar](media/data-flow/newpivot1.png "Map düzeltebilecekler Pivot sütunları")

### <a name="landing-new-columns-in-sink"></a>Havuzda yeni sütunlar giriş

Özet içindeki dinamik sütun adları da dahil olmak üzere, yeni sütun adlarınızı ve değerlerini hedef deponuza havuza almaya devam edebilirsiniz. Havuz ayarlarınızda "şemayı Drma Izin ver" ayarını Açık olarak ayarlamanız yeterlidir. Sütun meta verilerinizde yeni dinamik adlar görmezsiniz, ancak şema DRFT seçeneği, verileri Land kullanmanıza olanak sağlayacak.

### <a name="view-metadata-in-design-mode"></a>Tasarım modunda meta verileri görüntüleme

Yeni sütun adlarını Inceleme bölümünde meta veriler olarak görüntülemek istiyorsanız ve sütunları doğrudan havuz dönüşümüne yayar ve sonra Özet anahtarı sekmesinde açık değerler ayarlayın.

### <a name="how-to-rejoin-original-fields"></a>Özgün alanlara yeniden katıl
Özet dönüştürme yalnızca toplama, gruplama ve Özet eyleminde kullanılan sütunları projlecektir. Akıştaki önceki adımdan diğer sütunları eklemek istiyorsanız, önceki adımda yeni bir dal kullanın ve akışı özgün meta verilerle bağlamak için kendi kendine JOIN örüntüsünün kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Sütun değerlerini satır değerlerine dönüştürmek için [UNPIVOT dönüşümünü](data-flow-unpivot.md) deneyin. 
