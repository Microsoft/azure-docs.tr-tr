---
title: Azure Dosya Eşitleme bulut katmanlama ilkeleri | Microsoft Docs
description: Tarih ve birim boş alan ilkelerinin farklı senaryolar için birlikte nasıl çalıştığı hakkında ayrıntılı bilgi.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797367"
---
# <a name="cloud-tiering-policies"></a>Bulut katmanlama ilkeleri

Bulut katmanlaması, buluta hangi dosyaların katmanlı olduğunu belirleyen iki ilkeye sahiptir: **birim boş alan ilkesi** ve **tarih ilkesi**.

**Birim boş alan ilkesi** , sunucu uç noktasının bulunduğu yerel birimin belirtilen yüzdesinin her zaman ücretsiz tutulmasını sağlar. 

**Tarih ilkesi** katmanı dosyaları en son x gün önce veya sonraki sürümlere erişti. Birim boş alan ilkesi her zaman öncelikli olur; birimde, tarih ilkesi tarafından açıklandığı gibi çok sayıda gün olması için yeterli boş alan olmadığında, Azure Dosya Eşitleme Tarih ilkesini geçersiz kılar ve birim boş alan yüzdesi karşılanana kadar birlikte, en hedef dosyaları katmanlama devam edecektir.

## <a name="how-both-policies-work-together"></a>Her iki ilke birlikte nasıl çalışır

Bu ilkelerin nasıl çalıştığını göstermek için bir örnek kullanacağız: 500 GB yerel bir birimde Azure Dosya Eşitleme yapılandırdığınızı ve bulut katmanlaması hiç etkinleştirilmemiş. Dosya paylaşımınızda bulunan dosyalar şunlardır:

|Dosya Adı |Son erişim zamanı  |Dosya Boyutu  |Içinde depolanan |
|----------|------------------|-----------|----------|
|Dosya 1    | 2 gün önce  | 10 GB | Sunucu ve Azure dosya paylaşma
|Dosya 2    | 10 gün önce | 30 GB | Sunucu ve Azure dosya paylaşma
|Dosya 3    | 1 yıl önce | 200 GB | Sunucu ve Azure dosya paylaşma
|Dosya 4    | 1 yıl, 2 gün önce | 130 GB | Sunucu ve Azure dosya paylaşma
|Dosya 5    | 2 yıl, 1 gün önce | 140 GB | Sunucu ve Azure dosya paylaşma

**Değiştirme 1:** Bulut katmanlaması etkinleştirilir, bir birim boş alan ilkesini %20 olarak ayarlayıp, tarih ilkesini devre dışı bırakmış olursunuz. Bu yapılandırmayla, bulut katmanlaması %20 ' nin (Bu durumda 100 GB) alan boş ve yerel makinede kullanılabilir olmasını sağlar. Sonuç olarak, yerel önbelleğin toplam kapasitesi 400 GB 'tır. Bu 400 GB, en son ve sık erişilen dosyaları yerel birimde depolayacaktır.

Bu yapılandırmayla, yalnızca 1 ile 4 arasındaki dosyalar yerel önbellekte depolanır ve 5. dosya katmanlı olur. Bu, yalnızca 400 GB 'lık 370 GB 'den fazla kullanılabilir. 5. dosya 140 GB 'tır ve yerel olarak önbelleğe alınmışsa 400 GB sınırını aşabilir. 

**2. değişiklik:** Bir kullanıcının dosya 5 ' i eriştiğini varsayalım. Bu, dosya 5 ' i paylaşımdaki en son erişilen dosyayı yapar. Sonuç olarak, 5. dosya yerel önbellekte depolanır ve 400 GB sınırına sığması için dosya 4 katmanlı olacaktır. Aşağıdaki tabloda, dosyaların nerede depolandığı ve bu güncelleştirmelerle ilgili güncelleştirmeler verilmiştir:

|Dosya Adı |Son erişim zamanı  |Dosya Boyutu  |Içinde depolanan |
|----------|------------------|-----------|----------|
|Dosya 5    | 2 saat önce | 140 GB | Sunucu ve Azure dosya paylaşma
|Dosya 1    | 2 gün önce  | 10 GB | Sunucu ve Azure dosya paylaşma
|Dosya 2    | 10 gün önce | 30 GB | Sunucu ve Azure dosya paylaşma
|Dosya 3    | 1 yıl önce | 200 GB | Sunucu ve Azure dosya paylaşma
|Dosya 4    | 1 yıl, 2 gün önce | 130 GB | Azure dosya paylaşma, yerel olarak katmanlı

**Değiştirme 3:** İlkeyi, tarih tabanlı katmanlama ilkesinin 60 gün ve birim boş alan ilkesi %70 olacak şekilde güncelleştirmiş olalım. Şimdi, yerel önbellekte yalnızca 150 GB 'ye kadar depolama olabilir. Dosya 2 60 günden daha önce erişilse de, birim boş alan ilkesi Tarih ilkesini geçersiz kılar ve %70 yerel boş alanını korumak için 2. dosya katmanlı olur.

**Değiştirme 4:** Birim boş alan ilkesini %20 olarak değiştirdiyseniz ve sonra `Invoke-StorageSyncFileRecall` bulut katmanlama ilkelerine bağlı durumdayken yerel sürücüye sığan tüm dosyaları geri çekmek için kullandıysanız, tablo şöyle görünür:

|Dosya Adı |Son erişim zamanı  |Dosya Boyutu  |Içinde depolanan |
|----------|------------------|-----------|----------|
|Dosya 5    | 1 saat önce  | 140 GB | Sunucu ve Azure dosya paylaşma
|Dosya 1    | 2 gün önce  | 10 GB | Sunucu ve Azure dosya paylaşma
|Dosya 2    | 10 gün önce | 30 GB | Sunucu ve Azure dosya paylaşma
|Dosya 3    | 1 yıl önce | 200 GB | Azure dosya paylaşma, yerel olarak katmanlı
|Dosya 4    | 1 yıl, 2 gün önce | 130 GB | Azure dosya paylaşma, yerel olarak katmanlı

Bu durumda, dosyalar 1, 2 ve 5 yerel olarak önbelleğe alınır ve dosyalar 3 ve 4 katmanlı olur. Tarih ilkesi 60 gün olduğu için, birim boş alan ilkesi yerel olarak 400 GB 'a izin veriyorsa, dosyalar 3 ve 4 katmanlı olur.

> [!NOTE] 
> Müşteriler birim boş alan ilkesini daha küçük bir değere (örneğin, %20 ' den %10 ' a kadar) değiştirirken dosyalar otomatik olarak geri çekilir veya tarih ilkesini daha büyük bir değere (örneğin, 20 günden 50 güne kadar) değiştirin.

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Yerel bir birimde birden çok sunucu uç noktası

Bulut katmanlaması, tek bir yerel birimdeki birden çok sunucu uç noktası için etkinleştirilebilir. Bu yapılandırma için, birim boş alanını aynı birimdeki tüm sunucu uç noktaları için aynı miktara ayarlamanız gerekir. Aynı birimdeki çeşitli sunucu uç noktaları için farklı birim boş alan ilkeleri ayarlarsanız, en büyük birim boş alan yüzdesi öncelikli olur. Bu, **geçerli birim boş alan ilkesi** olarak adlandırılır. Örneğin, aynı yerel birimde üç sunucu uç noktası varsa, biri %15 ' e ve diğeri de %30 ' a ayarlandıysa, %30 ' dan az boş alan olduğunda, tüm bunlar, birlikte bulunan dosyaları katmana başlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Bulut katmanlaması izleme](file-sync-monitor-cloud-tiering.md)
