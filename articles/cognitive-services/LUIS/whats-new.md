---
title: Yenilikler-Language Understanding (LUSıS)
titleSuffix: Azure Cognitive Services
description: Bu makale, Azure bilişsel hizmetler Language Understanding API hakkındaki haberlerle düzenli olarak güncelleştirilir.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 62ce0ac36223098d013cc75b7f62fc2c68e358ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953375"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding yenilikleri

Hizmette nelerin yeni olduğunu öğrenin. Bu öğeler sürüm notlarını, Videoları, blog gönderilerini ve diğer bilgi türlerini içerir. Hizmetle güncel kalmasını sağlamak için bu sayfaya yer işareti ekleyin.  

## <a name="release-notes"></a>Sürüm notları 

### <a name="november-4-2019---ignite"></a>4 Kasım 2019-Menite

* Geliştirilmiş geliştirici verimliliği
    * [Tahmin uç noktası v3](luis-migration-api-v3.md)'nin genel kullanılabilirliği. 
    * Uygulamaları. lu ([Luaşağı](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) biçimiyle içeri ve dışarı aktarma özelliği. Bu, etkin bir CI/CD işleminin yolunu ele alır. 
* Dil genişletmesi
    * [Arapça ve Hintçe](luis-language-support.md) genel önizlemede.
* Önceden oluşturulmuş modeller
    * [Önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md) artık genel kullanıma sunuldu (GA)
    * Japonca [önceden oluşturulmuş varlıklar](luis-reference-prebuilt-entities.md#japanese-entity-support) -yaş, para birimi, sayı, yüzde, v3 'de desteklenmez.
    * İtalyanca [önceden oluşturulmuş varlıklar](luis-reference-prebuilt-entities.md#italian-entity-support) -yaş, para birimi, boyut, sayı, yüzde çözünürlüğü v2 'den değişti.
* [Preview.Luis.ai Portal](https://preview.luis.ai) 'da Kullanıcı deneyimini geliştirin-karmaşık modellerin oluşturulmasını ve hata ayıklamasını etkinleştirmek için yeniden etiketleme deneyimidir. Önizleme portalı öğreticilerini deneyin:
    * [Yalnızca amaçlar](tutorial-intents-only.md)
    * [Ayrıştırılmış makine tarafından öğrenilen varlık](tutorial-machine-learned-entity.md) 
* Gelişmiş dil özelliklerini anlama-daha az çaba ile [Gelişmiş dil modelleri oluşturma](luis-concept-entity-types.md) . 
* Makine öğrenimi özelliklerini model düzeyinde tanımlama ve modelleri amaçlar ve diğer varlıklara özellikler olarak kullanma gibi diğer modele işaret olarak kullanılmak üzere etkinleştirme.
* Yeni, genişletilmiş [sınırlar](luis-boundaries.md) -tümcecik listeleri ve toplam ifadeler için daha yüksek maksimum değer, özellik sınırları olarak yeni model
* Derin hiyerarşi yapısı biçimindeki metinden bilgi ayıklayın ve konuşma uygulamalarını daha güçlü hale getirir.

    ![makine tarafından öğrenilen varlık görüntüsü](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 Eylül 2019

* Azure yazma kaynağı- [Şimdi geçirin](luis-migration-authoring.md).
    * Azure kaynağı başına 500 uygulama
    * Uygulama başına 100 sürüm
* Önceden oluşturulmuş varlıklar için Türkçe desteği
* DatetimeV2 için İtalyanca desteği

### <a name="july-23-2019"></a>23 Temmuz 2019

* [Tanıyıcıları-metin](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) ile 1.2.3 güncelleştirme
    * Italyanca ' de yaş, sıcaklık, boyut ve para birimi tanıyıcıları.
    * Tatil tabanlı tarihleri doğru şekilde hesaplamak için Ingilizce olarak tatil tanıma geliştirme.
    * Tarih ve saat olmayan varlıkların yanlış pozitiflerini azaltmak için Fransız Tarih/saatteki geliştirmeler.
    * Ingilizce gün içinde takvim/okul/mali yıl ve kısaltmalar için destek.
    * Çince ve Japonca için, iyileştirilmiş PhoneNumber Tanıma.
    * Ingilizce olarak NumberRange desteği geliştirildi.
    * Performans iyileştirmeleri.

### <a name="june-24-2019"></a>24 Haziran 2019

* İleri, önceki ve son gibi sıralamayı desteklemek için [OrdinalV2 önceden oluşturulmuş varlık](luis-reference-prebuilt-ordinal-v2.md) . Yalnızca İngilizce kültürü.

### <a name="may-6-2019---build-conference"></a>6 Mayıs 2019-/sürüm Konferansı

Aşağıdaki özellikler Build 2019 Konferansı 'nda yayımlanmıştır:

* [V3 API geçiş kılavuzu önizlemesi](luis-migration-api-v3.md)
* [Geliştirilmiş analiz panosu](luis-how-to-use-dashboard.md)
* [Geliştirilmiş önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md) 
* [Dinamik liste varlıkları](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Dış varlıklar](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Bloglar

[Bot çerçevesi](https://blog.botframework.com/)

## <a name="videos"></a>Videolar

### <a name="2019-build-videos"></a>2019 derleme videoları

[Azure konuşma AI kullanarak işinizi bir sonraki nesil için ölçeklendirin](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Hizmet güncelleştirmeleri

[Bilişsel hizmetler için Azure Update duyuruları](https://azure.microsoft.com/updates/?product=cognitive-services)
