---
title: Demo Conversation Learner modeli, parola sıfırlama-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Demo Conversation Learner modeli oluşturmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703388"
---
# <a name="demo-password-reset"></a>Tanıtım: Parola sıfırlama
Bu öğreticide, Conversation Learner tarafından desteklenen parola sıfırlamalarıyla ilgili yardımcı olabilecek basit bir teknik destek bot da gösterilebilir. Bot 'un modeli, önemsiz olmayan iletişim akışları ve etki alanı dışı sınıflar içeren çoklu açma dizileri öğreneleyebilir. Görev, kod veya varlık olmadan gerçekleştirilebilir.

## <a name="video"></a>Video

[![Tanıtım parolası önizlemesi](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Gereksinimler
Bu öğretici, parola sıfırlama botunuzun çalışıyor olmasını gerektirir

    npm run demo-password

### <a name="open-the-demo"></a>Tanıtımı açın

Web Kullanıcı arabiriminin model listesinde, öğretici tanıtım parola sıfırlama ' ya tıklayın. 

### <a name="actions"></a>Eylemler

Model, kullanıcıların ortak parola sorunlarını çözmesine yardımcı olmak için tasarlanmış bir eylem kümesi içerir.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Eğitim Iletişim kutuları

Modelde ayrıca, etki alanı sınıfı eğitimini gösteren bazıları da dahil olmak üzere birkaç eğitim Iletişim kutusu bulunur. Örneğin, yönlere yol açan kullanıcılar gibi istekte bulunabilir. Örnek bot, tanıtım amaçlı olarak birkaç kez eğitilmiştir ve yalnızca "Bu konuda yardımcı olamaz" ifadesi ile yanıt verir. Mevcut tren Iletişim kutularının listesi, sol paneldeki "Iletişim kutularını eğitme" altında bulunur.

![](../media/tutorial_pw_reset_entities.PNG)

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "parolamı kaybetmem" yazın.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Yerel hesabınız veya Microsoft hesabı için mi?" yanıtını seçin
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "yerel hesap \" yazın.
6. "Eylemleri puan" düğmesine tıklayın.
7. "Windows 'un hangi sürümü kullanıyorsunuz?" yanıtını seçin.
8. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Windows XP" yazın
9. "Eylemleri puan" düğmesine tıklayın.
10. "+ Action" düğmesine tıklayın.
11. "Bot 'un yanıtı..." alan, tür "Çözüm: Windows XP 'de parola sıfırlama.. "
12. "Oluştur" düğmesine tıklayın.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Etki alanı dışı senaryolar Için Eğitim Iletişimleri

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine tıklayın ve ardından mevcut "oyuncak mağaza" eğitme Iletişim kutusunu tıklatın.
2. Sohbet panelinde "Oyuncak Mağazaları" deterance ' e tıklayın.
3. "Alternatif giriş Ekle..." alanına "Web araması" yazın ve ENTER tuşuna basın.
4. "Alternatif giriş Ekle..." alanına "uçuş kaydı" yazın ve ENTER tuşuna basın.
5. "Değişiklikleri Kaydet" düğmesine tıklayın.
6. "Düzenlemeyi Kaydet" düğmesine tıklayın.
7. Sol bölmede "günlük Iletişimleri" ve ardından "yeni günlük Iletişim" düğmesine tıklayın.
8. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "parolamı bulamıyorum" ifadesini yazın.
9. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Microsoft hesabı" yazın
10. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "teşekkürler" yazın
11. "Test bitti" düğmesine tıklayın.
12. Kılavuz görünümünden "parolamı bulamıyorum" günlüğü iletişim kutusu ' na tıklayın.
13. Sohbet panelinde, yanlış işlenmiş "çözüme tıklayın: Microsoft hesabı parolasını sıfırlama "yanıtı.
14. "+ Action" düğmesine tıklayın.
15. "Bot 'un yanıtı..." alanına "hoş geldiniz" yazın
16. "Oluştur" düğmesine tıklayın.
17. "Tren olarak kaydet Iletişim kutusu" düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Demo-pizza sırası](./demo-pizza-order.md)
