---
title: Ontolobir dönüştürme
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS için sektör standardı modellerini DTDL 'ye dönüştürme sürecini anlayın
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484494"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Azure dijital TWINS için sektör standardı ontolo, DTDL 'ye Dönüştür

Çoğu [ontolo,](concepts-ontologies.md) [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)ve [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)gibi Anlamsal Web standartlarını temel alır. 

Azure dijital TWINS ile bir model kullanmak için, DTDL biçiminde olmalıdır. Bu makaleler, Azure dijital TWINS ile kullanılabilmesi için RDF tabanlı modelleri DTDL 'ye dönüştürmek üzere bir **dönüştürme modeli** biçiminde genel tasarım kılavuzunu açıklar. 

Makale Ayrıca, bina sektöründe diğer şemalar için genişletilebilen RDF ve OWL Dönüştürücülerine yönelik [**örnek dönüştürücü kodu**](#converter-samples) içerir.

## <a name="conversion-pattern"></a>Dönüştürme deseninin

RDF tabanlı modelleri DTDL 'ye dönüştürürken kullanılabilecek birkaç üçüncü taraf kitaplığı vardır. Bu kitaplıkların bazıları, model dosyanızı bir grafiğe eklemenize olanak tanır. Belirli RDFS ve OWL yapılarını bulmak ve bunları DTDL 'e dönüştürmek için grafik boyunca döngü yapabilirsiniz.   

Aşağıdaki tabloda, RDFS ve OWL yapılarının DTDL ile nasıl eşleştirileneceğini gösteren bir örnek verilmiştir. 

| RDFS/OWL kavram | RDFS/OWL yapısı | DTDL kavram | DTDL yapısı |
| --- | --- | --- | --- |
| Sınıflar | `owl:Class`<br>IRI soneki<br>``rdfs:label``<br>``rdfs:comment`` | Arabirim | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Alt sınıflar | `owl:Class`<br>IRI soneki<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Arabirim | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| DataType özellikleri | `owl:DatatypeProperty`<br>`rdfs:label` veya `INode`<br>`rdfs:label`<br>`rdfs:range` | Arabirim Özellikleri | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Nesne özellikleri | `owl:ObjectProperty`<br>`rdfs:label` veya `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | İlişki | `type:Relationship`<br>`name`<br>`target` (veya yoksa atlandı `rdfs:range` )<br>`comment`<br>`displayName`<br>

Aşağıdaki C# kod parçacığı, bir RDF model dosyasının bir grafiğe nasıl yüklendiğini ve [**Dotnetrdf**](https://www.dotnetrdf.org/) kitaplığı kullanılarak dtdl 'ye nasıl dönüştürüleceğini gösterir. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Dönüştürücü örnekleri

### <a name="rdf-converter-application"></a>RDF dönüştürücü uygulaması 

Azure dijital TWINS hizmeti tarafından kullanılmak üzere RDF tabanlı model dosyasını [Dtdl 'ye (sürüm 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) dönüştüren örnek bir uygulama mevcuttur. Bu, [blok](https://brickschema.org/ontology/) şeması için onaylanmış ve yapı sektöründe ( [TOPOLOJI ontology (bot)](https://w3c-lbd-cg.github.io/bot/), [semantik algılayıcı ağı](https://www.w3.org/TR/vocab-ssn/)veya [BUILDINGSMART sektör temel sınıfları (ifc)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)gibi diğer şemalar için genişletilebilir.

Örnek, **Rdftodtdlconverter** adlı bir .NET Core komut satırı uygulamasıdır.

Örneği buradan edinebilirsiniz: [**Rdftodtdlconverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Kodu makinenize indirmek için örnek sayfasındaki başlığın altında bulunan **koda gözatıp** düğmesini seçin. Bu, sizi örnek için GitHub deposuna götürür. Örnek olarak indirmek için **kod** düğmesini SEÇIN ve **ZIP 'i indirin** *. ZIP* dosyası *RdfToDtdlConverter-main.zip* çağırdı. Sonra dosyayı açabilir ve kodu keşfedebilirsiniz.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="GitHub 'daki RdfToDtdlConverter deposunun ekran görüntüsü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Bu örneği, dönüştürme düzenlerini bağlam içinde görmek ve kendi uygulamalarınız için kendi özel gereksinimlerinize göre model dönüştürmeleri gerçekleştiren bir yapı taşı olarak kullanmak için kullanabilirsiniz.

### <a name="owl2dtdl-converter"></a>OWL2DTDL Dönüştürücüsü 

[**OWL2DTDL Dönüştürücüsü**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) , Azure Digital TWINS hizmeti ile kullanılabilen bir dtdl arabirim bildirimleri kümesine bir Owl ontology çeviren bir örnektir. Ayrıca, ontology Networks için de kullanılır, bir kök ontology, bildirimler aracılığıyla diğer ontoloon 'u yeniden kullanma `owl:imports` .

Bu dönüştürücü, [gerçek emlak çekirdek ontology](https://doc.realestatecore.io/3.1/full.html) dtdl 'ye çevirmek için kullanıldı ve herhangi bir Owl tabanlı ontology için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar 

* Belirtimlerinizi karşılamak için sektör standardı ontoloi 'yi genişletme hakkında daha fazla bilgi edinin: [*Kavramlar: sektör ontoloi 'Yi genişletme*](concepts-ontologies-extend.md).

* Ya da, [*ontolobir model geliştirme yolunda ontology stratejilerini kullanarak*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)modeller geliştirmeye yönelik yol üzerinde devam edin.