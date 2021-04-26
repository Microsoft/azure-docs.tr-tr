---
title: Eğitim ve model nedir? -Özel çevirici
titleSuffix: Azure Cognitive Services
description: Model, belirli bir dil çifti için çeviri sağlayan sistemidir. Başarılı bir eğitimin sonucu bir modeldir. Bir modeli eğitmek için, karşılıklı birbirini dışlayan üç veri kümesi, eğitim veri kümesi, ayarlama veri kümesi ve test veri kümesi gereklidir.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897943"
---
# <a name="what-are-trainings-and-models"></a>Esinler ve modeller nelerdir?

Model, belirli bir dil çifti için çeviri sağlayan sistemidir.
Başarılı bir eğitimin sonucu bir modeldir. Bir modeli eğitmek için, birbirini dışlayan üç belge türü gereklidir: eğitim, ayarlama ve test. Sözlük belge türü de belirtilebilir. Lütfen [tümce hizalamasını](./sentence-alignment.md#suggested-minimum-number-of-sentences)inceleyin.

Bir eğitimin sıraya alınması sırasında yalnızca eğitim verileri sağlanmışsa, özel çevirmen ayarlama ve test verilerini otomatik olarak birleştirir. Eğitim belgelerinizdeki Tümcelerin rastgele bir alt kümesini kullanacaktır ve bu cümleleri eğitim verilerinin kendisinden dışlayacak.

## <a name="training-document-type-for-custom-translator"></a>Özel çevirici için eğitim belgesi türü

Eğitim kümesine dahil edilen belgeler, modelinizin oluşturulması için temel olarak özel çevirici tarafından kullanılır. Eğitim yürütmesi sırasında, bu belgelerde bulunan cümleler hizalanır (veya eşleştirilmiş). Eğitim belgeleri kümesini oluştururken serbest bir şekilde yararlanabilirsiniz. Tangential ilgi olduğunu düşündüğünüz belgeleri bir modelde dahil edebilirsiniz. Ayrıca, Bleu 'daki etkiyi görmek için bunları başka bir kez hariç tutun [(Iki dilli değerlendirme düşük incelemesi) puanı](what-is-bleu-score.md). Ayarlama kümesi ve test kümesi sabitlerini tutan sürece eğitim kümesi kompozisyonunu deneymekten çekinmeyin. Bu yaklaşım, çeviri sisteminizin kalitesini değiştirmek için etkili bir yoldur.

Bir proje içinde birden fazla çalışma çalıştırabilir ve tüm eğitim çalıştırmaları genelinde [Bleu puanlarını](what-is-bleu-score.md) karşılaştırabilirsiniz. Karşılaştırma için birden çok seyahat çalıştırırken, her seferinde aynı ayarlama/test verilerinin belirtildiğinden emin olun. Ayrıca, ["test"](how-to-view-system-test-results.md) sekmesinde sonuçları el ile de denetlediğinizden emin olun.

## <a name="tuning-document-type-for-custom-translator"></a>Özel çevirici için belge türünü ayarlama

Bu küme içine eklenen paralel belgeler, en iyi sonuçlar için çeviri sistemini ayarlamak üzere özel çevirici tarafından kullanılır.

Ayarlama verileri, çeviri sisteminin tüm parametrelerini ve ağırlıklarını en uygun değerlere ayarlamak için eğitim sırasında kullanılır. Ayarlama verilerinizi dikkatle seçin: ayarlama verileri, daha sonra çevirmek istediğiniz belgelerin içeriğini temsil etmelidir. Ayarlama verilerinin, üretilen çevirilerin kalitesi üzerinde önemli bir etkisi vardır. Ayarlama, çeviri sisteminin ayarlama verilerinde sağladığınız örneklere en yakın çevirileri sağlamasına olanak sağlar. Ayarlama verilerinizde 2500 ' den fazla cümle gerekmez. En iyi çeviri kalitesi için, en çok temsili seçimi seçerek ayarlama ayarlamayı el ile seçmeniz önerilir.

Ayarlama kümesini oluştururken, daha sonra çeviri yapmak istediğiniz Tümcelerin anlamlı ve temsili bir uzunluğu olan tümceler ' ı seçin. Ayrıca, gelecekteki çevirilerde bekleeceğiniz yaklaşık bir dağıtım içinde çevirmek istediğiniz sözcükler ve tümceciklere sahip cümleler seçmelisiniz. Pratikte, bu tümceler en iyi sonuçları üretir ve çok karmaşık olmayan bir tümcecik uzunluğu sağlamak için 7 ila 10 ' a kadar sözcükten oluşan bir cümle uzunluğuna sahip olur.

Ayarlama kümesinde kullanılacak tümceler türünün iyi bir açıklaması şunlardır: gerçek akıcı cümleler. Tablo hücreleri değil, POEMS değil, hiçbir şey listesi değil, yalnızca noktalama işareti değil, tümce için normal bir dilde sayı değil.

Ayarlama verilerinizi el ile seçerseniz, eğitim ve test verileriyle aynı cümleleri içermemelidir. Ayarlama verileri çevirilerin kalitesi üzerinde önemli bir etkiye sahiptir-cümleleri dikkatle seçin.

Ayarlama verileriniz için neleri seçdiğinizden emin değilseniz, yalnızca eğitim verilerini seçin ve özel çevirmenin sizin için ayarlama verilerini seçmesini sağlayın. Özel çeviricisinin ayarlama verilerini otomatik olarak seçmesini sağlamak için, iki dilli eğitim belgelerinden oluşan rastgele bir cümle alt kümesini kullanır ve bu cümleleri eğitim malzemelerinin kendisinden hariç tutar.

## <a name="testing-dataset-for-custom-translator"></a>Özel çevirici için veri kümesini test etme

Test kümesine dahil edilen paralel belgeler, BLEU (Iki dilli değerlendirme az çalışma) Puanını hesaplamak için kullanılır. Bu puan, çeviri sisteminizin kalitesini gösterir. Bu puan, bu eğitimin sonucu olan çeviri sistemi tarafından, test veri kümesindeki başvuru cümleleriyle eşleşen çevirilerin ne kadar yakın olduğunu size bildirir.

BLEU puanı, otomatik çeviri ve başvuru çevirisi arasındaki Delta ölçüdür. Değeri 0 ile 100 aralığındadır. 0 puanı, bir başvurunun tek bir sözcüğünün çeviri içinde göründüğünü gösterir. 100 puanı otomatik çevirinin başvurusuyla tam olarak eşleştiğini gösterir: aynı sözcük tam olarak aynı konumdadır. Aldığınız puan, test verilerinin tüm cümlelerine yönelik BLEU puanı ortasıdır.

Test verileri, hedef dil cümlelerin kaynak-hedef çiftinde karşılık gelen kaynak dil cümlelerini en çok tercih ettiği çevirilerin olduğu paralel belgeleri içermelidir. Ayarlama verilerini oluşturmak için kullandığınız ölçütleri kullanmak isteyebilirsiniz. Ancak, test verilerinin, çeviri sisteminin kalitesi üzerinde hiçbir etkisi yoktur. Yalnızca sizin için BLEU puanı oluşturmak için kullanılır.

Test verileri için 2.500 ' den fazla cümle gerekmez. Sistemin test kümesini otomatik olarak seçmesini izin vermenizden sonra, iki dilli eğitim belgelerinden oluşan rastgele bir cümle alt kümesini kullanır ve bu cümleleri eğitim malzemelerinin kendisinden hariç tutar.

Test kümesinin özel çevirilerini görüntüleyebilir ve bir model içindeki test sekmesine giderek onları test kümesinde sunulan çevirilerle karşılaştırabilirsiniz.