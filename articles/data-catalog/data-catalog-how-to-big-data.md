---
title: Azure Veri Kataloğu 'nda büyük verileri katalog halinde kullanma
description: Azure Blob depolama, Azure Data Lake ve Hadoop gibi ' büyük veri ' veri kaynaklarıyla Azure Veri Kataloğu 'Nu kullanmaya yönelik desenleri vurgulama hakkında nasıl yapılır makalesi.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300578"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda büyük verileri katalog halinde kullanma

## <a name="introduction"></a>Giriş

**Microsoft Azure Veri Kataloğu** , kurumsal veri kaynakları için bir kayıt sistemi ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Kişilerin veri kaynaklarını bulmasına, anlamasına ve kullanmasına yardımcı olmak ve kuruluşların büyük veriler de dahil olmak üzere mevcut veri kaynaklarından daha fazla değer almasını sağlar.

**Azure Veri Kataloğu** , Azure blog depolama bloblarının ve dizinlerinin yanı sıra Hadoop, dosya ve dizinlerin kaydedilmesini da destekler. Bu veri kaynaklarının yarı yapılandırılmış doğası büyük esneklik sağlar. Ancak, **Azure Veri Kataloğu**'na kaydolduğunuz en yüksek değeri almak için, kullanıcıların veri kaynaklarının nasıl düzenlendiğini göz önünde bulundurmaları gerekir.

## <a name="directories-as-logical-data-sets"></a>Mantıksal veri kümeleri olarak dizinler

Büyük veri kaynaklarını düzenlemek için ortak bir düzen, dizinleri mantıksal veri kümeleri olarak değerlendirmek olur. Üst düzey dizinler, bir veri kümesini tanımlamak için kullanılır, alt klasörler bölümleri tanımlar ve içerdikleri dosyalar verilerin kendisini depolar.

Bu düzene bir örnek şu olabilir:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

Bu örnekte, vehicle_maintenance_events ve location_tracking_events mantıksal veri kümelerini temsil eder. Bu klasörlerin her biri, alt klasörlere yıl ve ay göre düzenlenmiş veri dosyaları içerir. Bu klasörlerin her biri yüzlerce veya binlerce dosya içerebilir.

Bu düzende, tek tek dosyaların **Azure Veri Kataloğu** ile kaydedilmesi büyük olasılıkla anlamlı değildir. Bunun yerine, verilerle çalışan kullanıcılara anlamlı olan veri kümelerini temsil eden dizinleri kaydedin.

## <a name="reference-data-files"></a>Başvuru veri dosyaları

Tamamlayıcı bir model, başvuru veri kümelerini tek tek dosyalar olarak depolayıcıdır. Bu veri kümeleri, büyük verilerin ' küçük ' tarafı olarak düşünülebilir ve genellikle bir analitik veri modelindeki boyutlara benzerdir. Başvuru veri dosyaları, büyük veri deposunda başka bir yerde depolanan veri dosyalarının toplu olarak bağlamını sağlamak için kullanılan kayıtları içerir.

Bu düzene bir örnek şu olabilir:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Bir analist veya veri bilimcu daha büyük dizin yapılarında bulunan verilerle çalışıyorsa, bu başvuru dosyalarındaki veriler, yalnızca daha büyük veri kümesindeki ad veya KIMLIĞE göre başvuruda bulunulan varlıklara yönelik daha ayrıntılı bilgi sağlamak için kullanılabilir.

Bu düzende, bireysel başvuru verileri dosyalarını **Azure Veri Kataloğu**ile kaydetmek mantıklı olur. Her dosya bir veri kümesini temsil eder ve her biri tek tek açıklanmış ve keşfedilebilir.

## <a name="alternate-patterns"></a>Alternatif desenler

Önceki bölümde açıklanan desenler, büyük veri deposunun organize olabilme biçimleridir ancak her uygulama farklıdır. Veri kaynaklarınızın yapılandırılma şeklinden bağımsız olarak, **Azure Veri Kataloğu**ile büyük veri kaynaklarını kaydederken, kuruluşunuzdaki diğer kullanıcılara değer olan veri kümelerini temsil eden dosya ve dizinleri kaydetmeye odaklanın. Tüm dosyaları ve dizinleri kaydetmek, kataloğun üzerinde değişiklik yapmalarını sağlayarak kullanıcıların ihtiyaç duydukları öğeleri bulmasını zorlaştırır.

## <a name="summary"></a>Özet

**Azure Veri Kataloğu** ile veri kaynaklarını kaydetme, bulmayı ve anlamayı kolaylaştırır. Mantıksal veri kümelerini temsil eden büyük veri dosyalarını ve dizinleri kaydederek ve bunlara ek olarak, kullanıcıların ihtiyaç duydukları büyük veri kaynaklarını bulmasına ve kullanmasına yardımcı olabilirsiniz.
