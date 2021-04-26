---
title: Belge biçimini içeri aktarma yönergeleri-Soru-Cevap Oluşturma
description: İçeriğiniz için en iyi sonuçları elde etmek üzere belgeleri içeri aktarmak için bu yönergeleri kullanın.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99549550"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>İçeri aktarılan belgeler ve URL 'Ler için biçimlendirme yönergeleri

İçeriğiniz için en iyi sonuçları elde etmek üzere bu biçimlendirme kılavuzlarını gözden geçirin.

## <a name="formatting-considerations"></a>Biçimlendirme konuları

Bir dosya veya URL 'YI içeri aktardıktan sonra Soru-Cevap Oluşturma, içeriğinizi [markı biçiminde](https://en.wikipedia.org/wiki/Markdown)dönüştürür ve depolar. Dönüştürme işlemi, metinde gibi yeni satırlar ekler `\n\n` . Markaşağı biçimi hakkında bilgi, dönüştürülen içeriği anlamanıza ve Bilgi Bankası içeriğinizi yönetmenize yardımcı olur.

İçeriğinizi doğrudan bilgi tabanınız içinde ekler veya düzenlersiniz, zengin metin içeriği oluşturmak veya zaten yanıtdaki markaşağı biçim içeriğini değiştirmek için **markı biçimlendirme** kullanın. Soru-Cevap Oluşturma, içeriğinize zengin metin özellikleri getirmek için markaşağı biçiminin çoğunu destekler. Ancak, bir sohbet bot gibi istemci uygulaması aynı markı biçimleri kümesini desteklemiyor olabilir. İstemci uygulamanın yanıt görüntüsünü test etmek önemlidir.

[İçerik türlerinin tam listesi ve örnekleri](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)için bkz..

## <a name="basic-document-formatting"></a>Temel belge biçimlendirmesi

Soru-Cevap Oluşturma, dosyadaki görsel ipuçlarına göre bölümleri ve alt bölümleri ve ilişkileri tanımlar:

* yazı tipi boyutu
* yazı tipi stili
* numaralandırmasının
* Renkler

> [!NOTE]
> Şu anda karşıya yüklenen belgelerden görüntülerin ayıklanmasını desteklemiyoruz.

### <a name="product-manuals"></a>Ürün kılavuzları

Bir el ile, genellikle bir ürüne eşlik eden rehberlik malzemeleridir. Kullanıcının ürünü ayarlama, kullanma, bakımını yapma ve sorun giderme konusunda yardımcı olur. Soru-Cevap Oluşturma bir el ile işlediğinde, başlıkları ve alt başlıkları sorular olarak ve sonraki içeriği yanıt olarak ayıklar. [Buraya](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)bir örnek bakın.

Aşağıda Dizin sayfası ve hiyerarşik içerik içeren bir el ile örnek verilmiştir

 ![Bilgi Bankası için ürün el Ile örnek](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Ayıklama, içindekiler tablosu ve/veya dizin sayfası ve hiyerarşik başlıkları olan açık bir yapı içeren el ile en iyi şekilde kullanılır.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broşürler, yönergeler, incelemeler ve diğer dosyalar

Diğer birçok belge türü, bir açık yapıya ve düzene sahip olmaları şartıyla QA çiftleri oluşturmak için de işlenebilir. Bunlar arasında şunlar yer alır: Broşürler, yönergeler, raporlar, teknik incelemeler, bilimsel incelemeler, ilkeler, kitaplar vb. [Buraya](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)bir örnek bakın.

Bir dizin olmadan yarı yapılandırılmış bir belge örneği aşağıda verilmiştir:

 ![Azure Blob depolama yarı yapılandırılmış belge](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Yapılandırılmış QnA belgesi

Belge dosyalarında yapılandırılmış Question-Answers biçimi, satır başına değişen sorular ve yanıtlar biçiminde, her satıra bir soru ve aşağıdaki satırdaki yanıtı aşağıda gösterildiği gibi, aşağıdaki satıra gelir:

```text
Question1

Answer1

Question2

Answer2
```

Aşağıda bir yapılandırılmış QnA Word belgesi örneği verilmiştir:

 ![Bilgi Bankası için yapılandırılmış QnA belgesi örneği](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Yapılandırılmış *txt*, *TSV* ve *xls* dosyaları

Bir Bilgi Bankası oluşturmak veya genişletmek için yapılandırılmış *. txt*, *. tsv* veya *. xls* dosyaları biçimindeki qnas de soru-cevap oluşturma yüklenebilir.  Bunlar düz metin olabilir veya RTF ya da HTML 'de içeriğe sahip olabilir.

| Soru  | Yanıt  | Meta veriler (1 anahtar: 1 değer) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Kaynak dosyadaki tüm ek sütunlar yok sayılır.

#### <a name="example-of-structured-excel-file"></a>Yapılandırılmış Excel dosyası örneği

Aşağıda, bir yapılandırılmış QnA *. xls* dosyası olan HTML içeriğiyle ilgili bir örnek verilmiştir:

 ![Bilgi Bankası için yapılandırılmış QnA Excel örneği](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel dosyasında tek yanıt için alternatif soruların örneği

Aşağıda, tek bir yanıt için çeşitli alternatif sorulara sahip bir yapılandırılmış QnA *. xls* dosyası örneği verilmiştir:

 ![Excel dosyasında tek yanıt için alternatif soruların örneği](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Dosya alındıktan sonra, soru-cevap çifti aşağıda gösterildiği gibi Bilgi Bankası 'nda bulunur:

 ![Bilgi Bankası 'na aktarılan tek yanıt için alternatif soruların ekran görüntüsü](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>İçeri aktarma yoluyla yapılandırılmış veri biçimi

Bilgi bankasını içeri aktarmak, mevcut bilgi bankasındaki içeriğin yerini alır. İçeri aktarma, veri kaynağı bilgilerini içeren bir yapılandırılmış. tsv dosyası gerektirir. Bu bilgiler, soru-cevap çiftlerini ve onları belirli bir veri kaynağına göre gruplamak Soru-Cevap Oluşturma yardımcı olur.

| Soru  | Yanıt  | Kaynak| Meta veriler (1 anahtar: 1 değer) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Düzenleme|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Belge biçimlendirmesini çoklu aç

* Hiyerarşiyi göstermek için başlıklar ve alt başlıklar kullanın. Örneğin, komut istemi olarak alınması gereken QnA 'yi göstermek için üst QnA ve H2 'yi belirtmek üzere H1 'yi belirtebilirsiniz. Sonraki hiyerarşiyi göstermek için küçük başlık boyutunu kullanın. Belgenizde yapıyı daha fazla yapmak için stil, renk veya başka bir mekanizma kullanmayın, Soru-Cevap Oluşturma çoklu açma istemlerini ayıklamaz.
* Başlığın ilk karakteri büyük harfle yazılmalıdır.
* Bir başlığı soru işaretiyle sonlandırmayın `?` .

**Örnek belgeler**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso avantajları (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso avantajları (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>SSS URL 'Leri

Soru-Cevap Oluşturma, 3 farklı formda SSS Web sayfalarını destekleyebilir:

* Düz SSS sayfaları
* Bağlantılarla ilgili SSS sayfaları
* Konular giriş sayfası ile SSS sayfaları

### <a name="plain-faq-pages"></a>Düz SSS sayfaları

Bu en yaygın SSS sayfası türüdür ve burada, yanıtlar aynı sayfada soruları hemen izler.

Aşağıda, düz bir SSS sayfasına örnek verilmiştir:

![Bilgi Bankası için düz SSS sayfası örneği](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Bağlantılarla ilgili SSS sayfaları

Bu tür bir SSS sayfasında, sorular birlikte toplanır ve aynı sayfanın farklı bölümlerinde ya da farklı sayfalarda bulunan yanıtlara bağlanır.

Aşağıda, aynı sayfada bulunan bölümlerde bulunan bağlantılarla ilgili bir SSS sayfası örneği verilmiştir:

 ![Bilgi Bankası için bölüm bağlantısı SSS sayfası örneği](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Üst konular sayfası alt yanıt sayfalarına bağlantılar

Bu tür bir SSS, her konunun farklı bir sayfada ilgili soru ve yanıt kümesiyle bağlantılı olduğu konular sayfasına sahiptir. Soru-Cevap Oluşturma, karşılık gelen soruların & yanıtları ayıklamak için tüm bağlantılı sayfalara gezinir.

Aşağıda, farklı sayfalardaki SSS bölümlerinin bağlantılarıyla ilgili konular sayfasına bir örnek verilmiştir.

 ![Ayrıntılı bağlantı SSS sayfası bir Bilgi Bankası için örnek](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>Destek URL 'Leri

Soru-Cevap Oluşturma, belirli bir görevin nasıl gerçekleştirileceğini, belirli bir sorunun nasıl tanılanacağı ve çözümleneceğini ve belirli bir işlem için en iyi uygulamaların hangileri olduğunu betimleyen web makaleleri gibi yarı yapılandırılmış destek Web sayfalarını işleyebilir. Ayıklama, hiyerarşik başlıkları olan açık bir yapıya sahip içerikte en iyi şekilde kullanılır.

> [!NOTE]
> Destek makaleleri için ayıklama yeni bir özelliktir ve erken aşamalarındayken. İyi yapılandırılmış olan ve karmaşık üstbilgiler/altbilgiler içermeyen basit sayfalar için en iyi şekilde kullanılır.

![Soru-Cevap Oluşturma, açık bir yapının hiyerarşik başlıklarla sunulduğu yarı yapılandırılmış Web sayfalarından ayıklanmasını destekler](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Bilgi bankasını içeri ve dışarı aktarma

Aktarılan bilgi tabanlarından **TSV ve xls dosyaları** yalnızca soru-cevap oluşturma portalındaki **Ayarlar** sayfasından dosyaları içeri aktarılarak kullanılabilir. Bunlar Bilgi Bankası oluşturma sırasında veya **Ayarlar** sayfasındaki **+ Dosya Ekle** veya **+ URL Ekle** özelliğinden veri kaynağı olarak kullanılamaz. 

Bilgi Bankası 'nı bu **TSV ve xls dosyaları** aracılığıyla içeri aktardığınızda, QNA çiftleri, dışarı aktarılan Bilgi Bankası 'Nda qnas 'nin ayıklandığı kaynaklara değil, düzenleme kaynağına eklenir. 


## <a name="next-steps"></a>Sonraki adımlar

[İçerik türlerinin tam listesi ve örnekleri](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base) için bkz.
