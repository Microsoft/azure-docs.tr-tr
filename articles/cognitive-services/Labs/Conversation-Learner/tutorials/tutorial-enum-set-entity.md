---
title: ENUM varlıklarının ne zaman kullanılacağı ve Conversation Learner modeliyle VARLıK eylemleri ayarlama-Azure bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: NUMARALANDıRMA varlıklarını kullanmak ve Conversation Learner modeliyle VARLıK eylemleri ayarlamak için uygun olduğunda bilgi edinin.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 429570d81f7e15758d8ea60951bb4d01b96f8f2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256479"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>ENUM varlıklarının ne zaman kullanılacağı ve VARLıK eylemlerinin AYARLANMASı

Bu öğretici, ENUM (numaralandırma) varlıklarını ve SET_ENTITY eylemlerini ne zaman kullanacağınızı açıklayacak.

## <a name="video"></a>Video

[![Varlık öğreticisi önizlemeyi ayarla](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Kapsanan Özellikler

Bu öğreticide iki yeni özellik tanıtılacaktır. Sabit Listesi (numaralandırma için kısa) adlı yeni bir varlık türü ve bu Enum değerlerinden birine başvurabilen, SET_ENTITY adlı yeni bir eylem türü ve adın gösterdiği gibi, varlığı bu değere ayarlar. Aşağıda öğrenirsiniz, bu yeni özellikler birlikte kullanılır ve bunların ne olduğunu ve bunların nasıl kullanılacağını açıklayacağız. Ayrıntılara girmeden önce, bu özelliklerin çözümüne yardımcı olan sorunu anlamak önemlidir.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Sorun

Konuşmalardaki sözcüklerin anlamı içeriğe göre değişir.  Normalde etiketlenmiş anahtar sözcükler, dil anlama hizmeti kullanılarak öğrenilir ve ayıklanır, ancak bu sistemler bu sistemler etiketli örnekleri kullanarak öğrenmeyebilir.

Yakındaki kişiler arasındaki görüşmenin bir kısmını fazla duyduğunuzu ve yalnızca "Evet" sözcüğünü duyduğunuzu varsayalım. "Evet" in ne olduğunu bilmediğiniz veya teyit etmediğiniz için soruyu duymadığınız için kabul etmiş ne olduğunu bilemezsiniz. Daha önce sorulan soru, yanıtın anlamı veren bağlamıdır. Benzer şekilde, "Yes" birçok farklı soruya ait yaygın bir yanıt olduğundan, bu, [özel eğitilen](04-introduction-to-entities.md) varlıklarla yaptığınız gibi örnekleri sunarak öğrenilebildiğinden, bu varlık için her "Yes" etiketini öğrenmiş olursunuz.

### <a name="example"></a>Örnek

Aşağıdaki örnekle daha fazla açıklanalım:

Hecesi Azure bilişsel hizmetler 'i beğendiniz mi?
Kullanıcısını Evet bot: Dondurma ister misiniz?
Kullanıcısını Evet

Önceki öğreticilerde, [özel eğitilen](04-introduction-to-entities.md) varlıklara baktık ve ilk düşünce "likesCogServices" adlı bir varlık oluşturmak ve Ilk "Yes" i bu varlık olarak etiketliyoruz.  Bununla birlikte, sistem ikinci "Yes" i de etiketleyebilir. İkinci "Yes" etiketini "likesIceCream" olarak düzeltmeye çalıştık, "Yes" olarak iki farklı şeyi de bir çakışma oluşturacak ve takılabilecek.

Bu durumda, ENUM varlıklarını ve SET_ENTITY eylemlerini kullanmanız gerekir.

## <a name="when-to-use-enums-or-set_entity-actions"></a>Numaralandırmalar veya SET_ENTITY eylemleri ne zaman kullanılır?

ENUM varlıklarının ve SET_ENTITY eylemlerinin ne zaman kullanılacağını bildirmek için aşağıdaki kuralları kullanın:

- Varlığın algılanması veya ayarlanması bağlama bağımlıdır
- Olası değer sayısı düzeltildi (Evet ve Hayır iki değer olacaktır)

Diğer bir deyişle, her zaman Evet veya Hayır ile sonuçlanan onay soruları gibi her türlü kapalı istemler için bunları kullanın.

> [!NOTE]
> Şu anda enum varlığı başına en fazla 5 değer sınırlaması vardır. Her değer geçerli 64 sınırında yuvalardan birini kullanır. Bkz. [CL-Values-ve-sınırları](../cl-values-and-boundaries.md)

Örnek: Hecesi Siparişiniz doğru mu?
Kullanıcısını Evet

Varlığın olası değerleri açık uçlu ve düzeltilmediğinde, [beklenen varlık](05-expected-entity.md)gibi alternatif bir özellik kullanmanız gerekir.

Örnek: Hecesi Adınız ne?
Kullanıcısını Matt bot: En sevdiğiniz renk nedir?
Kullanıcısını Gümüş

Bu istemler, rastgele değerlerle yanıtlanabileceğinden açık uçlu olarak değerlendirilir.

## <a name="what"></a>Ne:

### <a name="enum-entities"></a>Sabit Listesi varlıkları

Sabit Listesi varlıkları, tıpkı diğer varlıklar gibi oluşturulur. "Programlı" varlıklara benzer şekilde, kelimeleri bu varlıklar olarak etiketlenemez. Bunun yerine, kod veya SET_ENTITY eylemleri aracılığıyla ayarlanmaları gerekir.

![enum_entity_creation. png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Varlık eylemlerini ayarla

Yukarıda belirtildiği gibi, "varlık ayarla" eylemleri yalnızca bir varlığı bilinen bir sabit listesi değerine ayarlar. Aynı sonuçları bir API geri çağırma eylemi oluşturarak ve varlığı bir değere ayarlamak için bellek Yöneticisi 'ni kullanarak elde edebilirsiniz. Örneğin `memory.Set(entityName, entityValue)`. Bu kodun yazılması ve bu eylemlerin oluşturulması sıkıcı ve yönetimi zor olur. Conversation Learner, bu çalışmayı kolaylaştırmak için özel eylemlere sahiptir ve bu eylemleri kullanıldıkları zaman otomatik olarak oluşturur. Bunların bağımsız eylemlerle birlikte olması, bunları botunuzun diğer eylemlerle veya kodla bulunmadan oluşturma özelliğini korur.

- Küme varlığı eylemleri yalnızca bir sabit listesi varlığının değerine başvurulduğunda oluşturulabilir, bu sayede önce bir sabit listesi varlığı oluşturmanız gerekir.
- Yalnızca görünür bir çıkış olmadığından ve kullanıcının görebileceği bir "bekleme" eylemiyle izlenmeleri gerektiğinden, varlık eylemlerinin ayarlanması da "await" değildir.
- Varlık eylemlerini ayarlama, oluşturma işleminden sonra düzenleyemezsiniz.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Otomatik eylem oluşturma

Modelinizde bir numaralandırma varlığı varsa Conversation Learner, olası değerlerin her biri için yer tutucu eylemler oluşturacak ve eğitim sırasında bunları seçmek için kullanılabilir hale getirir. Seçim yapıldığında eylem sizin için otomatik olarak oluşturulur.

Örneğin, "Yes" ve "No" değerleriyle bir numaralandırma varlığı oluştururum:

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Bu yeni sabit listesi için açıkça eylem oluşturmanız gerekmeden bile, eğitim sırasında iki yeni eylemi görürsünüz:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Bu yeni özellikleri kullanarak bir bot oluşturun

### <a name="requirements"></a>Gereksinimler

Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

Hızlı yiyecek sıralamasına benzetmek için bir bot oluşturacağız. Drmürekkepler ve Cuma boyutları (küçük/orta/büyük) ve onay soruları için Evet/Hayır yanıtları ile ayrı değerlere sahip olacaktır. Bu varlıkların her ikisi de bağlama bağımlı yanıtları ve sabit değerleri yerine iki kuralı karşılar.

### <a name="create-the-model"></a>Modeli oluşturma

1. Web Kullanıcı arabiriminde "Içeri aktar" a tıklayın.
2. "Öğretici-enum-set-Entity" adlı öğreticiyi seçin

Bu işlem sizi model yönetimi sayfasına gitmenizi sağlar.
Modelin zaten birkaç enum varlığı içerdiğini ve varlık eylemlerini ayarlayadığına dikkat edin.

### <a name="create-the-first-dialog"></a>İlk iletişim kutusunu oluşturma

1. Sol gezinti panelinde "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Kullanıcı türü olarak, "Merhaba, bir Coke ve Cuma 'yı sıralamak istiyorum" gibi.
3. "Eylemleri Puanlama" düğmesine tıklayın

   > Kullanıcı içki veya Cuma için boyut belirtmemiştir, bu nedenle sizden sorun olması gerekir.

4. Yanıtla eylemi seçin: "Kurutink hangi boyuta istersiniz?"
5. Kullanıcı türü olarak, "büyük"
6. "Eylemleri Puanlama" düğmesine tıklayın
7. SET_ENTITY-"Drınksize eylemini seçin: MIKTARDA
8. Yanıtla eylemi seçin: "Bu boyut ne kadar boyutlar istersiniz?"
9. ' De kullanım türü olarak, "um" olarak bir ortam oluşturun.
10. "Eylemleri Puanlama" düğmesine tıklayın
11. SET_ENTITY-"friesSize" eylemini seçin: UZUNLUKTA
12. Yanıtla eylemi seçin: "Herhangi bir koşullu istiyor musunuz?"
13. ' De kullanım türü olarak, "Evet"
14. "Eylemleri Puanlama" düğmesine tıklayın
15. SET_ENTITY-"condimentsConfirmation" eylemini seçin. YES
16. Yanıtla eylemi seçin: "Tamam, büyük bir kuruttam ve orta ÖLÇEKLI bir sıralıyorum. Doğru mu? "
17. ' De kullanım türü olarak, "Evet"
18. "Eylemleri Puanlama" düğmesine tıklayın
19. SET_ENTITY-"orderConfirmation" eylemini seçin. YES
20. Yanıtla eylemi seçin: "Tamam, sipariş numaranız 58. Lütfen daha fazla bekleyin. "
21. İletişim kutusunu kapatmak için "Kaydet" e tıklayın

Yalnızca ENUM varlıkları ve SET_ENTITY eylemleri kullanarak ilk iletişim kutusunu oluşturdunuz. Kullanıcının kısmi bilgileri belirtmesini veya diğer kapatma sorularının diğer türleriyle deneme yapmasını deneyebilirsiniz.

> [!NOTE]
> Eğitim sırasında, SET_ENTITY eylemleri için şu gibi yer tutucuları görürsünüz:
>
> ![action_set_entity_training. png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> Ancak, günlük iletişimleri oluştururken veya dağıtılan botlar kullanılırken, kullanıcılar bunları görmez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Alternatif girişler](./10-alternative-inputs.md)
