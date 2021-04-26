---
title: include dosyası
description: include dosyası
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75392311"
---
Aşağıdaki tabloda, Azure Scheduler 'daki önemli kotalar, sınırlar, varsayılanlar ve kısıtlamaların her biri açıklanmaktadır.

| Kaynak | Limit açıklaması |
| -------- | ----------------- |
| **İş boyutu** | En büyük iş boyutu 16.000 ' dir. Bir PUT veya PATCH işlemi, bu sınırdan daha büyük bir iş boyutuyla sonuçlanırsa, 400 hatalı bir Istek durum kodu döndürülür. | 
| **İş koleksiyonları** | Azure aboneliği başına en fazla iş koleksiyonu sayısı 200.000 ' dir. | 
| **Koleksiyon başına iş** | Varsayılan olarak, en fazla iş sayısı, ücretsiz bir iş koleksiyonundaki beş iş ve standart iş koleksiyonundaki 50 işlerdir. İş koleksiyonundaki en fazla iş sayısını değiştirebilirsiniz. Bir iş koleksiyonundaki tüm işler, iş koleksiyonunda ayarlanan değer ile sınırlıdır. En yüksek iş kotasından daha fazla iş oluşturmayı denerseniz, istek 409 çakışma durum kodu ile başarısız olur. | 
| **Başlangıç zamanı** | Maksimum "başlangıç saati" değeri 18 aydır. |
| **Yinelenme aralığı** | En fazla yinelenme aralığı 18 aydır. | 
| **Sıklık** | Varsayılan olarak, en fazla sıklık kotası, ücretsiz bir iş koleksiyonundaki bir saat ve standart iş koleksiyonundaki bir dakika olur. <p>Bir iş koleksiyonundaki en yüksek frekansı en fazla değerden düşük hale getirebilirsiniz. İş koleksiyonundaki tüm işler, iş koleksiyonunda ayarlanan değer ile sınırlıdır. İş koleksiyonundaki en yüksek sıklıkta bir iş oluşturmayı denerseniz, istek 409 çakışma durum kodu ile başarısız olur. | 
| **Gövde boyutu** | Bir istek için en büyük gövde boyutu 8.192 karakter olur. |
| **İstek URL 'SI boyutu** | İstek URL 'SI için en büyük boyut 2.048 karakter olur. |
| **Üstbilgi sayısı** | Üst bilgi sayısı 50 üst bilgi. | 
| **Toplam üst bilgi boyutu** | En büyük toplam üst bilgi boyutu 4.096 karakter. |
| **Aş** | İstek zaman aşımı statiktir, diğer bir deyişle yapılandırılamaz. HTTP eylemleri için 60 saniyedir. Daha uzun süre çalışan işlemler için HTTP zaman uyumsuz protokollerini izleyin. Örneğin, hemen 202 döndürün, ancak arka planda çalışmaya devam edin. | 
| **İş geçmişi** | İş geçmişinde depolanan en büyük yanıt gövdesi 2.048 bayttır. |
| **İş geçmişi saklama** | İş geçmişi en fazla iki ay veya son 1.000 yürütmeler için tutulur. | 
| **Tamamlanan ve hatalı iş bekletme** | Tamamlanan ve hatalı işler 60 gün boyunca tutulur. |
||| 

