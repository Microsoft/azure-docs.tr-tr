---
title: Eğitim ve model nedir? -Özel Translator
titleSuffix: Azure Cognitive Services
description: Model, belirli bir dil çifti için çeviri sağlayan sistemidir. Başarılı bir eğitimin sonucu bir modeldir. Bir modeli eğitmek için, karşılıklı birbirini dışlayan üç veri kümesi, eğitim veri kümesi, ayarlama veri kümesi ve test veri kümesi gereklidir.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: af3f795dc5036b23b82562e7af4582bd90b44f47
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595422"
---
# <a name="what-are-trainings-and-models"></a>Esinler ve modeller nelerdir?

Model, belirli bir dil çifti için çeviri sağlayan sistemidir.
Başarılı bir eğitimin sonucu bir modeldir. Bir modeli eğitmek için, birbirini dışlayan üç veri kümesi gereklidir: eğitim veri kümesi, ayarlama veri kümesi ve test veri kümesi. Sözlük verileri de belirtilebilir.

Bir eğitimin sıraya alınması sırasında yalnızca eğitim verileri sağlanmışsa, özel çevirici ayarlama ve test veri kümelerini otomatik olarak birleştirir. Eğitim verilerinizde 5.000 cümleleri dışarıda bırakır ve ayarlama ve test kümelerini birleştirmek için her bir 2.500 kullanın.

## <a name="training-dataset-for-custom-translator"></a>Özel çevirici için eğitim veri kümesi

Eğitim kümesine dahil edilen belgeler, modelinizin oluşturulması için temel olarak özel çevirici tarafından kullanılır. Eğitim yürütmesi sırasında, bu belgelerde bulunan cümleler hizalanır (veya eşleştirilmiş). Eğitim belgeleri kümesini oluştururken serbest bir şekilde yararlanabilirsiniz. Tangential ilgi olduğunu düşündüğünüz belgeleri bir modelde dahil edebilirsiniz. Ayrıca, Bleu 'daki etkiyi görmek için bunları başka bir kez hariç tutun [(Iki dilli değerlendirme düşük incelemesi) puanı](what-is-bleu-score.md). Ayarlama kümesi ve test kümesi sabitlerini tutan sürece eğitim kümesi kompozisyonunu deneymekten çekinmeyin. Bu yaklaşım, çeviri sisteminizin kalitesini değiştirmek için etkili bir yoldur.

Bir proje içinde birden fazla çalışma çalıştırabilir ve tüm eğitim çalıştırmaları genelinde [Bleu puanlarını](what-is-bleu-score.md) karşılaştırabilirsiniz. Karşılaştırma için birden çok seyahat çalıştırırken, her seferinde aynı ayarlama/test verilerinin belirtildiğinden emin olun. Ayrıca, ["test"](how-to-view-system-test-results.md) sekmesinde sonuçları el ile de denetlediğinizden emin olun.

## <a name="tuning-dataset-for-custom-translator"></a>Özel çevirici için veri kümesini ayarlama

Bu küme içine eklenen paralel belgeler, en iyi sonuçlar için çeviri sistemini ayarlamak üzere özel çevirici tarafından kullanılır.

Ayarlama kümesi, çeviri sisteminin tüm parametrelerini ve ağırlıklarını en uygun değerlere ayarlamak için eğitim sırasında kullanılır. Ayarlama kümesini dikkatle seçin: ayarlama kümesi, daha sonra çevirmek istediğiniz belgelerin içeriğinin temsili olmalıdır. Ayarlama kümesinin, üretilen çevirilerin kalitesi üzerinde önemli bir etkisi vardır. Ayarlama, çeviri sisteminin ayarlama veri kümesinde sağladığınız örneklere en yakın çevirileri sağlamasına olanak sağlar. Ayar kümesi olarak 2500 taneden fazla cümle gerekmez. En iyi çeviri kalitesi için, en çok temsili seçimi seçerek ayarlama ayarlamayı el ile seçmeniz önerilir.

Ayarlama kümesini oluştururken, daha sonra çeviri yapmak istediğiniz Tümcelerin anlamlı ve temsili bir uzunluğu olan tümceler ' ı seçin. Ayrıca, gelecekteki çevirilerde bekleeceğiniz yaklaşık bir dağıtım içinde çevirmek istediğiniz sözcükler ve tümceciklere sahip cümleler seçmelisiniz. Pratikte, bu tümceler en iyi sonuçları üretir ve çok karmaşık olmayan bir tümcecik uzunluğu sağlamak için, 5 ila 18 ' e kadar sözcük uzunluğunda sözcüklerdir.

Ayarlama kümesinde kullanılacak tümceler türünün iyi bir açıklaması şunlardır: gerçek akıcı cümleler. Tablo hücreleri değil, POEMS değil, hiçbir şey listesi değil, yalnızca noktalama işareti değil, tümce için normal bir dilde sayı değil.

Ayarlama veri kümesini el ile seçerseniz, eğitim ve test verileriyle aynı cümlelerden herhangi birine sahip olmamalıdır. Ayarlama kümesi, çevirilerin kalitesi üzerinde önemli bir etkiye sahiptir-cümleleri dikkatle seçin.

Ayarlama ayarlarınız için neleri seçdiğinizden emin değilseniz, yalnızca eğitim kümesini seçmeniz ve özel çevirmenlerin sizin için ayarladığınız ayarı seçmesini seçelim. Özel çeviricisinin ayarlama ayarlarını otomatik olarak seçmesini sağlamak için, iki dilli eğitim belgelerinden oluşan rastgele bir cümle alt kümesini kullanır ve bu cümleleri eğitim malzemelerinin kendisinden hariç tutar.

## <a name="testing-dataset-for-custom-translator"></a>Özel çevirici için veri kümesini test etme

Test kümesine dahil edilen paralel belgeler, BLEU (Iki dilli değerlendirme az çalışma) Puanını hesaplamak için kullanılır. Bu puan, çeviri sisteminizin kalitesini gösterir. Bu puan, bu eğitimin sonucu olan çeviri sistemi tarafından, test veri kümesindeki başvuru cümleleriyle eşleşen çevirilerin ne kadar yakın olduğunu size bildirir.

BLEU puanı, otomatik çeviri ve başvuru çevirisi arasındaki Delta ölçüdür. Değeri 0 ile 100 aralığındadır. 0 puanı, bir başvurunun tek bir sözcüğünün çeviri içinde göründüğünü gösterir. 100 puanı otomatik çevirinin başvurusuyla tam olarak eşleştiğini gösterir: aynı sözcük tam olarak aynı konumdadır. Aldığınız puan, test kümesinin tüm cümlelerine yönelik BLEU puanı ortasıdır.

Test kümesi, hedef dil cümlelerin çiftin karşılık gelen kaynak dil cümlelerini en çok tercih ettiği çevirilerin olduğu paralel belgeleri içermelidir. Ayarlama kümesini oluşturmak için kullandığınız ölçütleri kullanmak isteyebilirsiniz. Ancak, test kümesinin, çeviri sisteminin kalitesi üzerinde hiçbir etkisi yoktur. Yalnızca sizin için BLEU puanı oluşturmak için ve başka hiçbir şey için kullanılır.

Test kümesi olarak 2.500 taneden fazla cümle gerekmez. Sistemin test kümesini otomatik olarak seçmesini izin vermenizden sonra, iki dilli eğitim belgelerinden oluşan rastgele bir cümle alt kümesini kullanır ve bu cümleleri eğitim malzemelerinin kendisinden hariç tutar.

Test kümesinin özel çevirilerini görüntüleyebilir ve bir model içindeki test sekmesine giderek onları test kümesinde sunulan çevirilerle karşılaştırabilirsiniz.
