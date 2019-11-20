---
title: Deneme yinelemelerini yönetme
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) ' de deneme yinelemelerini yönetme. Tüm denemeleri çalıştırmalarını dilediğiniz zaman gözden geçirebilir, geri yükleyebilir ve son olarak önceki varsayımları onaylayın ya da iyileştirebilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 9a69ebaa9b7f6ffffdfc80e09fb2eec9495e2ce2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838833"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio deneme yinelemelerini yönetme (klasik)
Tahmine dayalı analiz modelinin geliştirilmesi yinelemeli bir işlemdir. denemenizin çeşitli işlevlerini ve parametrelerini değiştirirken sonuçlar, eğitilen, etkin bir modeliniz olduğundan memnun kalana kadar yakınlardır. Bu işleme yönelik anahtar, deneme parametrelerinizin ve yapılandırmalarınızın çeşitli yinelemelerini izliyor.



Tüm denemeleri çalıştırmalarını dilediğiniz zaman gözden geçirebilir, geri yükleyebilir ve son olarak önceki varsayımları onaylayın ya da iyileştirebilirsiniz. Bir deneme çalıştırdığınızda Machine Learning Studio (klasik), veri kümesi, modül ve bağlantı noktası bağlantıları ve parametreleri de dahil olmak üzere, çalıştırmanın bir geçmişini tutar. Bu geçmiş ayrıca sonuçları, başlatma ve durdurma zamanları, günlük iletileri ve yürütme durumu gibi çalışma zamanı bilgilerini de yakalar. Deneme ve ara sonuçlarının zaman sayısını gözden geçirmek için herhangi bir zamanda Bu çalıştırmaların herhangi birine geri dönebilirsiniz. Hatta basit, karmaşık veya hatta ensekele modelleme çözümleri oluşturmak üzere yolunuzda yeni bir sorgu ve bulma aşamasına geçmek için denemenizin önceki bir çalıştırmasını kullanabilirsiniz.

> [!NOTE]
> Bir deneyin önceki bir çalıştırmasını görüntülediğinizde, bu deneme sürümü kilitlenir ve düzenlenemez. Bununla birlikte, **farklı kaydet** ' i tıklayarak ve kopya için yeni bir ad sağlayarak bir kopyasını kaydedebilirsiniz. Machine Learning Studio (klasik), daha sonra düzenleyebileceğiniz ve çalıştırabileceğiniz yeni kopyayı açar. Denemenizin bu kopyası, **denemeleri** listesinde tüm diğer denemeleri ile birlikte kullanılabilir.
> 
> 

## <a name="viewing-the-prior-run"></a>Önceki çalıştırmayı görüntüleme
En az bir kez çalıştırdığınız denemenizin bulunduğu bir deneyiniz varsa, Özellikler bölmesinde **önceki Çalıştır** ' a tıklayarak deneme sürümünün önceki çalıştırmasını görüntüleyebilirsiniz.

Örneğin, deneme sürümü oluşturduğunuzu ve 11:23, 11:42 ve 11:55 sürümlerini çalıştırın. Deneme sürümü (11:55) öğesini açıp **önceki çalıştırma**' ya tıkladığınızda, 11:42 ' de çalıştırdığınız sürüm açılır.

## <a name="viewing-the-run-history"></a>Çalıştırma geçmişini görüntüleme
Bir deneyin önceki çalıştırmalarını, açık bir deneyime göre **Çalıştır geçmişini görüntüle** ' ye tıklayarak görüntüleyebilirsiniz.

Örneğin, [Doğrusal regresyon][linear-regression] modülüyle bir deneme oluşturduğunuzu ve deneme sonuçlarınızda **öğrenme oranının** değerini değiştirmenin etkisini gözlemlemek istediğinizi varsayalım. Denemeyi Bu parametre için farklı değerlerle birden çok kez çalıştırırsınız:

| Öğrenme oranı değeri | Çalıştırma başlangıç zamanı |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 PM |
| 0,2 |9/11/2014 4:24:33 PM |
| 0,4 |9/11/2014 4:28:36 PM |
| 0,5 |9/11/2014 4:33:31 PM |

**Çalışma geçmişini görüntüle**' ye tıklarsanız, tüm bu çalıştırmaların bir listesini görürsünüz:

![Örnek çalışma geçmişi](./media/manage-experiment-iterations/viewrunhistory.jpg)

Çalıştırmanın bir anlık görüntüsünü çalıştırdığınız zaman görüntülemek için Bu çalıştırmaların herhangi birine tıklayın. Deneme sürümü çalıştırmanın tamamen bir kaydını sağlamak için yapılandırma, parametre değerleri, açıklamalar ve sonuçların tümü korunur.

> [!TIP]
> Deneme yinelemelerinizi belgelemek için, her çalıştırdığınızda başlığı değiştirebilirsiniz, Özellikler bölmesinde deneyinin **özetini** güncelleştirebilir ve yaptığınız değişiklikleri kaydetmek için bağımsız modüllerde yorum ekleyebilir veya güncelleştirebilirsiniz. Başlık, Özet ve modül açıklamaları her bir deneme çalıştırmasında kaydedilir.
> 
> 

Machine Learning Studio (klasik) içindeki **denemeleri** sekmesinde bulunan denemeleri listesi her zaman bir denemenin en son sürümünü görüntüler. Deneme sürümü ' nü (önceki **çalıştırma** veya **Görünüm çalıştırma geçmişi**kullanarak) açarsanız, **çalışma geçmişini görüntüle** ' ye tıklayarak ve **düzenlenebilir** **durumuna** sahip yinelemeyi seçerek Taslak sürümüne dönebilirsiniz.

## <a name="iterating-on-a-previous-run"></a>Önceki çalıştırmada yineleme
Önceki **çalıştırma** veya **Görünüm çalıştırma geçmişi** ' ne tıkladığınızda ve önceki bir çalıştırmayı açtığınızda, tamamlanmış bir denemeyi salt okuma modunda görüntüleyebilirsiniz.

Denemenizin bir yinelemesini önceki bir çalıştırma için yapılandırdığınız şekilde kullanmaya başlamak istiyorsanız, çalıştırmayı açıp **farklı kaydet**' e tıklayarak bunu yapabilirsiniz. Bu yeni bir deneme oluşturur, yeni bir başlık, boş bir çalıştırma geçmişi ve önceki çalıştırmanın tüm bileşenleri ve parametre değerleri. Bu yeni deneme Machine Learning Studio (klasik) giriş sayfasındaki **denemeleri** sekmesinde listelenir ve bu denemenizin bu yinelemesi için yeni bir çalıştırma geçmişi başlatarak onu değiştirebilir ve çalıştırabilirsiniz. 

Örneğin, önceki bölümde gösterilen deneme çalıştırması geçmişine sahip olduğunuzu varsayalım. **Öğrenme oranı** parametresini 0,4 olarak ayarladığınızda ne olacağını gözlemlemek ve **eğitim dönemleri sayısı** için farklı değerler denemeniz gerekir.

1. **Çalışma geçmişini görüntüle** ' ye tıklayın ve 4:28:36 PM 'de çalıştırdığınız denemenin yinelemesini açın (içinde parametre değerini 0,4 olarak ayarlarsınız).
2. **Farklı kaydet**' e tıklayın.
3. Yeni bir başlık girin ve **Tamam** onay işaretine tıklayın. Denemenin yeni bir kopyası oluşturulur.
4. **Eğitim dönemleri numarasını** değiştirin.
5. **Çalıştır**' a tıklayın.

Şimdi, denemenizin bu sürümünü değiştirmeye ve çalıştırmaya devam edebilir, çalışmanızı kaydetmek için yeni bir çalışma geçmişi oluşturabilirsiniz.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
