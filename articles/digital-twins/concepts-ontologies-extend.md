---
title: Ontolololoon genişletiliyor
titleSuffix: Azure Digital Twins
description: Bir ontology genişletmenin arkasındaki nedenler ve stratejiler hakkında bilgi edinin
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b38b4910773c433ed63fd2082c5cbefce81e0e9e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480263"
---
# <a name="extending-ontologies"></a>Ontolololoon genişletiliyor 

[Akıllı binalar Için Dtdl tabanlı Reatastatecore ontology](https://github.com/Azure/opendigitaltwins-building)gibi sektör standardı bir [ontology](concepts-ontologies.md), IoT çözümünüzü oluşturmaya başlamak için harika bir yoldur. Endüstri lideri, etki alanınız için tasarlanan ve Azure Digital TWINS gibi Azure IoT hizmetlerindeki kutudan çıkan kullanıma yönelik bir dizi zengin temel arabirim sağlar. 

Ancak, çözümünüzün sektör ontology kapsamına girmeyen özel ihtiyaçları olabilir. Örneğin, dijital TWINS 'nizi ayrı bir sistemde depolanan 3B modellere bağlamak isteyebilirsiniz. Bu durumda, özgün ontology avantajlarından yararlanmaya çalışırken kendi olanaklarınızı eklemek için bu ontoloya 'nın birini genişletebilirsiniz.

Bu makalede, yeni DTDL özellikleriyle ontolofüu genişletme örneklerinin temeli olarak DTDL tabanlı [Reatastatecore](https://www.realestatecore.io/) ontology kullanılmaktadır. Burada açıklanan teknikler genel, ancak DTDL özelliği (telemetri, özellik, Ilişki, bileşen veya komut) ile DTDL tabanlı ontology herhangi bir bölümüne uygulanabilir. 

## <a name="realestatecore-space-hierarchy"></a>Reatastatecore alanı hiyerarşisi 

DTDL tabanlı Reatastatecore ontology, boşluk hiyerarşisi çeşitli boşluk türlerini tanımlamak için kullanılır: Odalar, binalar, bölge, vb. Hiyerarşi, çeşitli oda, bina ve bölge türlerini tanımlamak için bu modellerden her birini genişletir. 

Hiyerarşinin bir kısmı aşağıdaki diyagramda gibi görünür. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="Reatastatecore alanı hiyerarşisinin bir kısmını gösteren akış diyagramı. En üst düzeyde, alan adlı bir öğe vardır; bir ' extends ' ile bağlı bir düzeyin aşağı okuna doğru bir şekilde bağlanır; Oda iki ' extends ' okunun bir düzeyi ConferenceRoom ve Office olarak bağlanır."::: 

Realetstatecore ontology hakkında daha fazla bilgi için bkz. [*Kavramlar: sektör standardı ontolofütlerini benimseme*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Reatastatecore alan hiyerarşisini genişletme 

Bazen çözümünüz sektörün sektör ontology kapsamına girmeyen özel gereksinimlere sahiptir. Bu durumda, hiyerarşiyi genişletmek, sektörde gereksinimlerinize göre özelleştirmeyi yaparken sektör ontology kullanmaya devam etmenize olanak sağlar. 

Bu makalede, ontology 'in hiyerarşisinin genişletilmesi yararlı olan iki farklı durum tartışıyoruz: 

* Sektörde bulunmayan kavramlar için yeni arabirimler ekleme ontology. 
* Mevcut arabirimlere ek özellikler (ya da ilişkiler, bileşenler, telemetri veya komutlar) ekleme.

### <a name="add-new-interfaces-for-new-concepts"></a>Yeni kavramlar için yeni arabirimler ekleyin 

Bu durumda, çözümünüz için gerekli olan, ancak sektör ontology mevcut olmayan kavramlar için arabirimler eklemek istersiniz. Örneğin, çözümünüz DTDL tabanlı Reatastatecore ontology içinde temsil edilmeyen başka türlerde Odalar içeriyorsa, doğrudan Reatastatecore arabirimlerinden genişleterek bunları ekleyebilirsiniz. 

Aşağıdaki örnek, Reatastatecore ontology içinde olmayan "odak odalarını" temsil etmesi gereken bir çözüm sunar. Odak odası, kullanıcıların bir görevde birkaç saat boyunca odaklanmak üzere tasarlanan küçük bir alandır. 

Sektörün sektör ontology 'yi bu yeni kavram ile genişletmek için, sektör ontology içindeki arabirimlerden [genişleyen](concepts-models.md#model-inheritance) yeni bir arabirim oluşturun. 

Odak odası arabirimini ekledikten sonra, genişletilmiş hiyerarşi yeni oda türünü gösterir. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="Yeni bir ekleme ile, daha önce Reatastatecore alan hiyerarşisini gösteren akış diyagramı. ConferenceRoom ve Office ile en alt düzeyde, Focusoda adlı yeni bir öğe vardır (Ayrıca, odadan ' extends ' oku aracılığıyla da bağlanır)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Mevcut arabirimlere ek yetenekler ekleme 

Bu durumda, sektör ontology 'de bulunan arabirimlere daha fazla Özellik (veya ilişki, bileşen, telemetri veya komut) eklemek istersiniz.

Bu bölümde, iki örnek görürsünüz: 
* Var olan bir sistemde zaten bulunan 3B çizimleri görüntüleyen bir çözüm oluşturuyorsanız, her bir dijital ikizi 3B çizimlerinin (KIMLIĞE göre) ilişkilendirilmesini isteyebilirsiniz, böylece çözüm alan hakkında bilgi görüntülediğinde, varolan sistemden 3B çizimini de alabilir. 
* Çözümünüzün, konferans odalarının çevrimiçi/çevrimdışı durumunu izlemesi gerekiyorsa, görüntüleme veya sorgularda kullanmak üzere konferans odası durumunu izlemek isteyebilirsiniz. 

Her iki örnek de yeni özelliklerle uygulanabilir: `drawingId` 3B çizimi dijital ikizi ve bir "çevrimiçi" özelliği ile ilişkilendiren, konferans odasının çevrimiçi olup olmadığını belirten bir özelliktir. 

Genellikle sektördeki güncelleştirmeleri gelecekte (eklemelerinizin üzerine yazacak şekilde) eklemek istediğiniz için sektör ontology doğrudan değiştirmek istemezsiniz. Bunun yerine, bu tür eklemeler DTDL tabanlı Reatastatecore ontology ' dan genişleyen kendi arabirim hiyerarşinizde yapılabilir. Oluşturduğunuz her arabirim, ana Reatastatecore arabirimini ve üst arabirimini genişletilmiş arabirim hiyerarşinizden genişletmek için birden çok arabirim devralmayı kullanır. Bu yaklaşım, sektör ontology ve eklemelerinizi birlikte kullanmanıza olanak sağlar. 

Sektör ontology genişletmek için, sektör ontology 'deki arabirimlerden genişleyen kendi arabirimlerinizi oluşturur ve yeni özellikleri genişletilmiş arabirimlerinizde ekleyebilirsiniz. Genişletmek istediğiniz her arabirim için yeni bir arabirim oluşturursunuz. Genişletilmiş arabirimler DTDL dilinde yazılır (Bu belgenin ilerleyen kısımlarında bulunan genişletilmiş arabirimler için DTDL bölümüne bakın). 

Yukarıda gösterilen hiyerarşinin bölümünü genişlettikten sonra genişletilmiş hiyerarşi aşağıdaki diyagramda gibi görünür. Burada genişletilmiş alan arabirimi, `drawingId` dijital Ikizi 3D çizimiyle ilişkilentiren BIR ID içeren özelliği ekler. Ayrıca, ConferenceRoom arabirimi, konferans odasının çevrimiçi durumunu içerecek bir "çevrimiçi" özelliği de ekler. Devralma aracılığıyla ConferenceRoom arabirimi, Reatastatecore ConferenceRoom arabiriminden tüm özellikleri ve genişletilmiş alan arabiriminden tüm özellikleri içerir. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="Daha yeni eklemeler sayesinde, yukarıda bulunan genişletilmiş Reatastatecore alanı hiyerarşisini gösteren akış diyagramı. Oda artık, düzeyini bir boşluk öğesi ile paylaşır, bu da bir düzeyi, ConferenceRoom ve Office 'in yanındaki yeni bir oda öğesine aşağı doğru bir şekilde bağlar.  Yeni öğeler, daha fazla ' extends ' ilişkisine sahip var olan ontology 'a bağlanır."::: 

## <a name="using-the-extended-space-hierarchy"></a>Genişletilmiş alan hiyerarşisini kullanma 

Genişletilmiş alan hiyerarşisini kullanarak dijital TWINS oluşturduğunuzda, her dijital ikizi modeli genişletilmiş alan hiyerarşisinden (orijinal sektör ontology değil) bir olur ve sektör ontology ve genişletilmiş arabirimlerin tüm yeteneklerini arabirim devralmasından dahil eder.

Her dijital ikizi modeli, aşağıdaki diyagramda gösterilen genişletilmiş hiyerarşiden bir arabirim olacaktır. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="Genişletilmiş Reatastatecore alanı hiyerarşisinden boşluk (en üst düzey), bir oda (orta seviye) ve ConferenceRoom, Office ve Focusodu (alt düzey) dahil bir alıntı. Modellerin adları her öğeye bağlıdır (örneğin, Oda Room101 adlı bir modele bağlıdır)."::: 

Model KIMLIĞINI (işleci) kullanarak dijital TWINS için sorgulama yaparken `IS_OF_MODEL` , genişletilmiş hiyerarşideki model kimliklerinin kullanılması gerekir. Örneğin, `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Özgün ontology geri katkıda bulunma 

Bazı durumlarda sektör ontology, çoğu kullanıcı için büyük ölçüde yararlı olacak şekilde genişletecektir. Bu durumda, uzantılardan özgün ontology geri katkılarını göz önünde bulundurmanız gerekir. Her ontology katkıda bulunmak için farklı bir işleme sahiptir, bu nedenle katkı ayrıntıları için ontology 'ın GitHub deposunu kontrol edin. 

## <a name="dtdl-for-new-interfaces"></a>Yeni arabirimler için DTDL 

Doğrudan sektör ontology 'dan genişleyen yeni arabirimler için DTDL şuna benzer. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>Genişletilmiş arabirimler için DTDL 

Yukarıda açıklanan bölüm ile sınırlı genişletilmiş arabirimler için DTDL şuna benzer. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Sonraki adımlar

Ontolobir [*model geliştirme yolunda ontology stratejilerini kullanarak*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)modeller geliştirmeye yönelik yol üzerinde devam edin.