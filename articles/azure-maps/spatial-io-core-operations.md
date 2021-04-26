---
title: Çekirdek GÇ işlemleri | Microsoft Azure haritaları
description: Uzamsal GÇ modülündeki temel kitaplıkları kullanarak XML ve sınırlandırılmış verileri verimli bir şekilde okumayı ve yazmayı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891385"
---
# <a name="core-io-operations"></a>Çekirdek GÇ işlemleri

Uzamsal GÇ modülü, uzamsal veri dosyalarını okumak için Araçlar sağlamaya ek olarak, temel temel kitaplıkların XML ve sınırlandırılmış verileri hızlı ve verimli bir şekilde okuyup yazabilmesini sağlar.

`atlas.io.core`Ad alanı, CSV ve XML verilerini hızlı bir şekilde okuyabilen ve yazabilmesi için iki alt düzey sınıf içerir. Bu temel sınıflar, uzamsal GÇ modülündeki uzamsal veri okuyucularını ve yazarları güçlendirin. Bunları kullanarak CSV veya XML dosyaları için ek okuma ve yazma desteği ekleyebilirsiniz.
 
## <a name="read-delimited-files"></a>Ayrılmış dosyaları oku

`atlas.io.core.CsvReader`Sınıfı, sınırlandırılmış veri kümeleri içeren dizeleri okur. Bu sınıf, verileri okumak için iki yöntem sunar:

- `read`İşlevi, tam veri kümesini okur ve ayrılmış veri kümesinin tüm hücrelerini temsil eden iki boyutlu bir dize dizisi döndürür.
- `getNextRow`İşlevi, sınırlandırılmış bir veri kümesindeki her metin satırını okur ve bu veri kümesi satırındaki tüm hücreleri temsil eden bir dize dizisi döndürür. Kullanıcı, sonraki satırı işlemeden önce satırı işleyebilir ve bu satırdan gereksiz belleği atabilirsiniz. Bu nedenle, işlev daha fazla bellek verimlidir.

Varsayılan olarak, okuyucu ayırıcı olarak virgül karakterini kullanır. Ancak, sınırlayıcı herhangi bir tek karakter olarak değiştirilebilir veya olarak ayarlanabilir `'auto'` . Olarak ayarlandığında `'auto'` , okuyucu dizedeki metnin ilk satırını analiz eder. Daha sonra, sınırlayıcı olarak kullanmak için aşağıdaki tablodan en sık kullanılan karakteri seçer.

| Sınırlayıcı | Karakter |
| :-- | :-- |
| Virgül | `,` |
| Tab | `\t` |
| Kapatıldığı | `|` |

Bu okuyucu Ayrıca sınırlayıcı karakteri içeren hücreleri işlemek için kullanılan metin niteleyicilerini destekler. Quote ( `'"'` ) karakteri varsayılan metin niteleyicidir, ancak herhangi bir tek karakterle değiştirilebilir.

## <a name="write-delimited-files"></a>Ayrılmış dosyaları yaz

Bir `atlas.io.core.CsvWriter` nesne dizisini ayrılmış bir dize olarak yazar. Herhangi bir tek karakter, sınırlayıcı veya metin niteleyicisi olarak kullanılabilir. Varsayılan sınırlayıcı virgül (), `','` varsayılan metin niteleyicisi ise quote ( `'"'` ) karakteridir.

Bu sınıfı kullanmak için aşağıdaki adımları izleyin:

- Sınıfının bir örneğini oluşturun ve isteğe bağlı olarak özel bir sınırlayıcı veya metin niteleyicisi ayarlayın.
- İşlevini veya işlevini kullanarak sınıfa veri yazma `write` `writeRow` . İşlevi için `write` birden çok satır ve hücreyi temsil eden iki boyutlu bir nesne dizisi geçirin. İşlevini kullanmak için `writeRow` , birden çok sütunlu bir veri satırını temsil eden bir nesne dizisi geçirin.
- `toString`Ayrılmış dizeyi almak için işlevi çağırın. 
- İsteğe bağlı olarak, `clear` yazıcıyı yeniden kullanılabilir hale getirmek ve kaynak ayırmayı azaltmak için yöntemini çağırın veya `delete` Yazıcı örneğini atmak için yöntemini çağırın.

> [!Note]
> Yazılan sütunların sayısı, yazıcıya geçirilen verilerin ilk satırındaki hücre sayısıyla sınırlandıralınacaktır.

## <a name="read-xml-files"></a>XML dosyalarını oku

`atlas.io.core.SimpleXmlReader`Sınıfı, XML dosyaları ayrıştırılırken daha hızlıdır `DOMParser` . Ancak, `atlas.io.core.SimpleXmlReader` sınıfı XML dosyalarının düzgün biçimlendirilmiş olmasını gerektirir. Düzgün biçimlendirilmeyen XML dosyaları, örneğin kapanış etiketleri eksik olabilir.

Aşağıdaki kod, `SimpleXmlReader` sınıfının BIR JSON nesnesine BIR XML dizesini ayrıştırmak ve istenen biçimde serileştirmek için nasıl kullanılacağını gösterir.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>XML dosyalarını yaz

`atlas.io.core.SimpleXmlWriter`Sınıfı düzgün BIÇIMLENDIRILMIŞ XML 'i bellek verimli bir şekilde yazar.

Aşağıdaki kod, `SimpleXmlWriter` sınıfının iyi biçimlendirilmiş BIR XML dizesi oluşturmak için nasıl kullanılacağını gösterir.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Yukarıdaki koddan oluşturulan XML aşağıdaki gibi görünür.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

[Desteklenen veri biçimi ayrıntıları](spatial-io-supported-data-format-details.md)