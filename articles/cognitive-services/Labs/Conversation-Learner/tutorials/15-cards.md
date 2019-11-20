---
title: Conversation Learner modeliyle kartlar kullanma, 1. bölüm-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle kart kullanmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 426d7c8de29abeb88833e94962a7291a641702ac
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703756"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kartları kullanma (Bölüm 1/2)

Bu öğreticide, bot 'unuza basit bir kartın nasıl ekleneceğini ve kullanılacağı gösterilmektedir.

> [!NOTE]
> Conversation Learner Şu anda kart tanım dosyalarınızın, bot 'un başlatıldığı dizinde bulunan "kartlar" adlı bir dizinde bulunmasını bekliyor.

## <a name="video"></a>Video

[![Kartlar öğreticisi önizlemesi](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

Kartlar, kullanıcının konuşmada bir seçenek seçmesine izin veren kullanıcı ARABIRIMI öğeleridir. 

### <a name="open-the-demo"></a>Tanıtımı açın

Web Kullanıcı arabiriminde "öğreticileri Içeri aktar" düğmesine tıklayın ve "öğretici-15-kartlar" adlı modeli seçin.

### <a name="the-card"></a>Kart

Kart tanımı şu konumdadır: C:\<InstallationPath\>\src\cards\prompt,JSON.

Sistem kart tanımlarınızı bu "kartlar" dizininde bulmayı bekler.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Gövde türü "TextBlock" ve "{{soru}}" yer tutucusunu metin alanında görebilirsiniz.
> İki gönderme düğmesi ve her biri için gönderilen metin vardır.

### <a name="actions"></a>Eylemler

Üç eylem oluşturduk. Aşağıda gördüğünüz gibi, ilk eylem bir karttır.

![](../media/tutorial13_actions.PNG)

Kart eylem türünün nasıl oluşturulduğunu görelim:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Kart üç farklı parametre içerir-soru girişi, düğme 1 ve düğme 2. Bu öğeler, soruyu ve ilgili yanıtları girdiğiniz karttaki şablon başvurulardır. Ayrıca varlıklara veya metin ve varlıkların bir karışımını ya da kullanabilirsiniz.

Göz simgesi, kartın nasıl göründüğünü gösterir.

### <a name="practicing-creating-card-actions"></a>Kart eylemleri oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Eylem türü" için "kart" ı seçin.
3. "Şablon" listesinden "sor" seçeneğini belirleyin.
4. "Soru" alanına "sol veya sağ git" yazın
5. "Button1" alanına "left" yazın
6. "BUTTON2" alanına "Right" yazın
7. "Iptal" düğmesine tıklayın.

### <a name="train-dialog-using-an-adaptive-card"></a>Uyarlamalı kart kullanarak Iletişim kutusunu eğitme

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın
3. "Eylemleri puan" düğmesine tıklayın.
4. Yanıt "sor: soru: Sola veya sağa git "
    - Göz simgesi, kartın önizlemesi için kullanılabilir
5. Sohbet panelinde, işlenen istemde "sol" düğmesine tıklayın.
6. "Eylemleri puan" düğmesine tıklayın.
7. "Sol" yanıtını seçin
8. "Kaydet" düğmesine tıklayın.
9. Yanıt "sor: soru: Sola veya sağa git "
10. Sohbet panelinde, işlenen istemde "sağ" düğmesine tıklayın.
11. "Eylemleri puan" düğmesine tıklayın.
12. "Sağ" yanıtını seçin

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Karma botlar](./16-hybrid-bots.md)
