---
title: Tasarım Bilgi Bankası-Soru-Cevap Oluşturma
description: Soru-Cevap Oluşturma Bilgi Bankası, her QnA çiftiyle ilişkili bir soru-cevap (QnA) çiftleri ve isteğe bağlı meta veri kümesi içerir.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: ef5476ade205109f5dfede1b3bb2c3a4ae2e81a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94506014"
---
# <a name="question-and-answer-pair"></a>Soru ve yanıt çifti

Bilgi Bankası, soru ve yanıt (QnA) çiftlerinden oluşur.  Her çiftin bir yanıtı vardır ve bir çift, bu _Yanıt_ ile ilişkili tüm bilgileri içerir. Bir yanıt, bir veritabanı satırına veya bir veri yapısı örneğine benzeyebilirler.

## <a name="question-and-answer-pairs"></a>Soru ve yanıt çiftleri

Soru-cevap (QnA) çiftinde **gerekli** ayarlar şunlardır:

* farklı bir metinle, ancak aynı Yanıt ile Kullanıcı sorusunun metniyle hizalamak için makine öğrenimine Soru-Cevap Oluşturma için kullanılan Kullanıcı sorgusunun **soru** metni.
* **Yanıt** -çiftin yanıtı, bir Kullanıcı sorgusu ilişkili soru ile eşleştiğinde döndürülen yanıttır

Her çift bir **kimlik** ile temsil edilir.

Bir çiftin **isteğe bağlı** ayarları şunlardır:

* **Sorunun alternatif biçimleri** -bu, soru-cevap oluşturma çok çeşitli soru açısından doğru yanıtı döndürmenize yardımcı olur
* **Meta** veri: meta veriler bir QNA çiftiyle ilişkili etiketlerdir ve anahtar-değer çiftleri olarak temsil edilir. Meta veri etiketleri, QnA çiftlerini filtrelemek ve sorgu eşleştirmesinin gerçekleştirileceği kümeyi sınırlamak için kullanılır.
* Çoklu çift **istemler**, çok yönlü bir konuşmaya devam etmek için kullanılır

![Bilgi temellerini Soru-Cevap Oluşturma](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Bilgi tabanına genel olarak ekle

Bilgi bankasını doldurmak için önceden mevcut içeriğiniz yoksa, Soru-Cevap Oluşturma portalına genel olarak QnA çiftleri ekleyebilirsiniz. Bilgi bankaınızı [buradan](How-To/edit-knowledge-base.md)güncelleştirmeyi öğrenin.

## <a name="editing-your-knowledge-base-locally"></a>Bilgi bankaınızı yerel olarak Düzenle

Bilgi Bankası oluşturulduktan sonra, yerel dosyalar aracılığıyla dışarı aktarmak ve yeniden içeri aktarmak yerine [soru-cevap oluşturma portalındaki](https://qnamaker.ai)Bilgi Bankası metninde düzenleme yapmanız önerilir. Ancak, bir bilgi bankasını yerel olarak düzenlemeniz gerektiği zamanlar olabilir.

Bilgi Bankası 'nı **Ayarlar** sayfasından dışarı aktarın ve Bilgi Bankası 'Nı Microsoft Excel ile düzenleyin. Verdiğiniz dosyayı düzenlemek için başka bir uygulama kullanmayı seçerseniz, uygulama tam olarak uyumlu olmadığından sözdizimi hataları verebilir. Microsoft Excel 'in TSV dosyaları genellikle biçimlendirme hataları sunmaz.

Düzenlemelerinizi tamamladıktan sonra, **Ayarlar** sayfasından TSV dosyasını yeniden içeri aktarın. Bu işlem, geçerli bilgi bankasını içeri aktarılan Bilgi Bankası ile tamamen değiştirir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma Bilgi Bankası yaşam döngüsü](Concepts/development-lifecycle-knowledge-base.md)
