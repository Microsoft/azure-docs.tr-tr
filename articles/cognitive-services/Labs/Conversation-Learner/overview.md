---
title: Konuşma Öğrenici nedir? -Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner ve nasıl çalıştığını öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 725a1d3628fb320a58e073fe2d825af23b02c0bd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707012"
---
# <a name="what-is-conversation-learner"></a>Konuşma Öğrenici nedir?

Conversation Learner, örnek etkileşimlerden öğrenmiş olan konuşma arabirimlerini derlemenize ve öğretebilmenizi sağlar. 

Geleneksel yaklaşımlardan farklı olarak Conversation Learner, yanıtları geliştirmek ve daha ilgi çekici kullanıcı deneyimleri sunmak için bir iletişim kutusu uçtan uca bağlamını dikkate alır. Çok sayıda görev odaklı kullanım durumunu kapsayan Conversation Learner, botların ve akıllı aracıların kullanıcıları rahatsız etmek, ek müşteri hizmetleri maliyetlerine tabi hale getirmek ve daha sezgisel etkileşimler sunmak için arka planda makine öğrenimi uygular.

Geliştiriciler, taklit etmek istedikleri prototipte iletişim kutularını girerek başlar. Model daha fazla iletişim kutusu olarak öğrenir. Model düzgün çalışmaya başladıktan sonra, bot son kullanıcılara dağıtılabilir. Conversation Learner, kullanıcılarla ilgili konuşmaları günlüğe kaydeder ve geliştirici bunları gözden geçirebilir. Hatalar sposısa, geliştirici bir nokta düzeltmesi yapabilir ve model yeniden kullanıma sunulmuştur ve hemen kullanılabilir.

Bu yaklaşım, iletişim denetim mantığının el ile kodlanmasını azaltır ve iş sahiplerinin veya etki alanı uzmanlarının, daha önceki bir makine öğrenimi bilgisi olmadan bir konuşma arabirimine katkıda bulunmasını sağlar. Conversation Learner, bir bot 'ın, akıllı cihazın veya akıllı aracının bir parçası olarak dağıtılmasının yanı sıra yeni becerileri, davranışları ve Uzmanlıklar hızla yineleyebilir ve kalitesini hızlı bir şekilde artırabilir. 

Conversation Learner, geliştiricilerin, Microsoft bot Framework veya tek başına altyapısını kullanarak birden çok konuşma kanalında hızla pazara ve başarılı bir şekilde çağrı yapmasına olanak sağlar.

Özet ve vurgular:

- Conversation Learner, görev odaklı botların oluşturulmasına yönelik ilk bir AI yoludur.

- Uçtan uca bir yinelenen sinir ağı (LSTM) kullanır ve doğrudan konuşmaları çok yönlü örneklerinden öğrenir. 

- Tasarımcılar, geliştiriciler, iş kullanıcıları ve çağrı merkezi çalışanları tarafından botları derleyip bakımını sağlar. 

- Kod içinde iş kurallarını ve yaygın anlamda anlamlı bir şekilde ifade etme yeteneği sağlar.

- Eğitim oturumları sırasında, sinir ağ modeli, konuşmadaki bir sonraki beklenen eylem kümesini öğrenmek için kullanılır. Bot geliştiricisi daha sonra doğru eylemi seçebilir ve uygun yanıtı sağlamak için ağı eğitebilir.
 
- Eğitim tamamlandıktan sonra, geliştirici, bot yanıtlarına düzeltme yapmak ve modeli yeniden eğitme için Kullanıcı etkileşimlerinden günlük iletişim kutularını kullanabilir. 

- , Görevleri tamamlamaya yönelik etki alanına özgü ve üçüncü taraf API 'Leri çağırabilir.

