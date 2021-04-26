---
title: Tümce eşleştirme ve hizalama-özel çevirici
titleSuffix: Azure Cognitive Services
description: Eğitim yürütmesi sırasında, paralel belgelerde bulunan cümleler eşleştirilmiş veya hizalanır. Özel çevirici, bu tümcenin bir cümlesini okuyarak her seferinde bir cümleyi bir tümce öğrenir. Ardından, bu iki tümce içindeki kelimeleri ve tümceleri birbirlerine hizalar.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/19/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 43268afccbe66a21d2ce78709ba372a8a6682444
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727159"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Paralel belgelerde tümce eşleştirme ve hizalama

Belgeler karşıya yüklendikten sonra, paralel belgelerde bulunan cümleler eşleştirilmiş veya hizalanır. Özel çevirmen, veri kümelerinin her birinde hizalanmış cümleler olarak eşlenebildiği cümle sayısını raporlar.

## <a name="pairing-and-alignment-process"></a>Eşleştirme ve hizalama işlemi

Özel çevirici, her seferinde bir cümleden oluşan Tümcelerin çevirilerini öğrenir. Kaynak metinden bir cümleyi ve sonra bu tümcenin hedef metinden çevirisini okur. Ardından, bu iki tümce içindeki kelimeleri ve tümceleri birbirlerine hizalar. Bu işlem, bir tümcedeki sözcüklerin ve deyimlerin, tümcenin çevirisi içindeki eşdeğer sözcüklere ve tümceciklere bir harita oluşturmasını sağlar. Hizalama, sistemin birbirleriyle çevirileri olan cümleler üzerinde hareket etmeye çalışır.

## <a name="pre-aligned-documents"></a>Önceden hizalanmış belgeler

Paralel belgeleriniz olduğunu biliyorsanız, önceden hizalanmış metin dosyaları sağlayarak cümle hizalamasını geçersiz kılabilirsiniz. Her iki belgedeki tüm tümceleri metin dosyasına çıkarabilir, her satır için bir cümleyi organize edebilir ve bir uzantıya sahip yükleyebilirsiniz `.align` . `.align`Uzantı, tümce hizalamasını atlayabilmesi Için özel çeviriciyi işaret eder.

En iyi sonuçlar için, dosyalarınızda her satırda bir cümle olduğuna emin olun. Tümce içinde yeni satır karakterleri eklemeyin ve bu işlem kötü hizalamalara neden olur.

## <a name="suggested-minimum-number-of-sentences"></a>Önerilen en az sayıda tümce

Bir eğitimin başarılı olması için, aşağıdaki tabloda her belge türünde gereken en az sayıda tümce gösterilmektedir.Bu sınırlama, paralel Cümlelerinizin bir çeviri modelini başarıyla eğitmek için yeterli benzersiz sözlük içerdiğinden emin olmak için bir güvenlik ağı sağlar. Genel kılavuz, insan çevirisi kalitesinin daha fazla etki alanı içi paralel cümlelere sahip olmaya daha yüksek kaliteli modeller üretmelidir.

| Belge türü   | Önerilen minimum tümce sayısı | En fazla tümce sayısı |
|------------|--------------------------------------------|--------------------------------|
| Eğitim   | 10,000                                     | Üst sınır yok                 |
| Ayarlama     | 500                                      | 2,500       |
| Test Etme    | 500                                      | 2,500  |
| Sözlük | 0                                          | 250.000                 |

> [!NOTE]
>
> - Eğitim başlatılmaz ve eğitim için en az 10.000 cümle sayısı karşılanmazsa başarısız olur.
> - Ayarlama ve test isteğe bağlıdır. Bunları sağlamazsanız, sistem, doğrulama ve test için kullanılan eğitimin uygun bir yüzdesini kaldırır.
> - Yalnızca Sözlük verilerini kullanarak bir modeli eğitebilirsiniz. Lütfen [sözlüğe](./what-is-dictionary.md)bakın.
> - Sözlüğünüz 250.000 taneden fazla cümle içeriyorsa, **[belge çevirmeni](https://docs.microsoft.com/azure/cognitive-services/translator/document-translation/overview)** büyük olasılıkla daha iyi bir seçimdir.

## <a name="next-steps"></a>Sonraki adımlar

- Özel çevirmende bir [sözlüğü](what-is-dictionary.md) nasıl kullanacağınızı öğrenin.
