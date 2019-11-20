---
title: Belge biçimleri ve adlandırma kuralları-özel çevirici
titleSuffix: Azure Cognitive Services
description: Bu, özel Çevirmendeki belge biçimleri ve adlandırma kuralı kılavuzudur. Bu kavram, belge adlarını yönetmeye yardımcı olur. daha fazla ABD adlandırma çakışmalarını önleyin.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595858"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Belge biçimleri ve adlandırma kuralı Kılavuzu

Özel çeviri için kullanılan herhangi bir dosya en az **dört** karakter uzunluğunda olmalıdır.

Bu tablo, çeviri sisteminizi oluşturmak için kullanabileceğiniz tüm desteklenen dosya biçimlerini içerir:

| Biçimi            | Uzantılar   | Açıklama                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DOSYASINA             | . XLF,. DOSYASINA | Bir paralel belge biçimi, çeviri belleği sistemlerini dışarı aktarma. Kullanılan diller dosya içinde tanımlanmıştır.                                                                                                                                                              |
| TMX               | . TMX         | Bir paralel belge biçimi, çeviri belleği sistemlerini dışarı aktarma. Kullanılan diller dosya içinde tanımlanmıştır.                                                                                                                                                              |
| ZIP               | . ZIP         | ZIP bir arşiv dosyası biçimidir.                                                                                                                                                                                                        |
| LocStudio         | . LCL         | Paralel belgeler için Microsoft biçimi                                                                                                                                                                                                                                      |
| Microsoft Word    | . DEĞIŞTIRIN        | Microsoft Word belgesi                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . BELGESINI         | Adobe Acrobat taşınabilir belgesi                                                                                                                                                                                                                                                |
| HTML              | . HTML,. DOSYASINI  | HTML belgesi                                                                                                                                                                                                                                                                  |
| Metin dosyası         | . TXT         | UTF-16 veya UTF-8 kodlu metin dosyaları. Dosya adı Japonca karakterler içermemelidir.                                                                                                                                                                                        |
| Hizalanmış metin dosyası | . ACAKTIR       | Uzantı `.ALIGN` , belge çiftindeki Tümcelerin kusursuz hizalandığını biliyorsanız kullanabileceğiniz özel bir uzantıdır. Bir `.ALIGN` dosya sağlarsanız, özel çevirici cümleleri sizin için hizalayıp etmez. |
| Excel dosyası        | . XLSX        | Excel dosyası (2013 veya üzeri). Elektronik tablonun ilk satırı/satırı dil kodu olmalıdır.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Sözlük biçimleri

Sözlükler için özel çevirici, eğitim kümeleri için desteklenen tüm dosya biçimlerini destekler. Excel sözlüğü kullanıyorsanız, elektronik tablonun ilk satırı/satırı dil kodu olmalıdır.

## <a name="zip-file-formats"></a>ZIP dosya biçimleri

Belgeler tek bir ZIP dosyasında gruplandırılabilir ve karşıya yüklenebilir. Özel çevirmen ZIP dosya biçimlerini (ZIP, GZ ve TGT z) destekler.

ZIP dosyasındaki uzantı TXT, HTML, HTM, PDF, DOCX, ALıGN olan her belge, bu adlandırma kuralını izlemelidir:

{belge adı} \_{Language Code} burada {belge adı} belgenizin adı, {Language Code} ise belgenin söz konusu dildeki cümleler içerdiğini gösteren ISO LanguageID (iki karakterdir). Dil kodundan önce bir alt çizgi (_) olmalıdır.

Örneğin, bir ZIP içindeki iki paralel belgeyi Ingilizce ile Ispanyolca sisteme yüklemek için dosyalar "data_en" ve "data_es" olarak adlandırılmalıdır.

Çeviri belleği dosyaları (TMX, XLF, XLıFF, LCL, XLSX), belirli dil adlandırma kuralını izlemek için gerekli değildir.  

## <a name="next-steps"></a>Sonraki adımlar

- Oluşturmak ve yönetmek için [Proje](workspace-and-project.md#what-is-a-custom-translator-project) hakkında bilgi edinin.
