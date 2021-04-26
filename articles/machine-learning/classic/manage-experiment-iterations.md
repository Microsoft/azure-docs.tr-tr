---
title: 'ML Studio (klasik): görüntüleme & yeniden çalıştırma denemeleri-Azure'
description: Azure Machine Learning Studio (klasik) ' de deneme çalıştırmalarını yönetin. Tüm denemeleri çalıştırmalarını dilediğiniz zaman gözden geçirebilir, geri yükleyebilir ve son olarak önceki varsayımları onaylayın ya da iyileştirebilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 419a696da1244afab7aa03cd8c4521ea819a5298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100515963"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio deneme çalışmalarını yönetme (klasik)

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Tahmine dayalı analiz modelinin geliştirilmesi yinelemeli bir işlemdir. denemenizin çeşitli işlevlerini ve parametrelerini değiştirirken sonuçlar, eğitilen, etkin bir modeliniz olduğundan memnun kalana kadar yakınlardır. Bu işleme yönelik anahtar, deneme parametrelerinizin ve yapılandırmalarınızın çeşitli yinelemelerini izliyor.

Tüm denemeleri çalıştırmalarını dilediğiniz zaman gözden geçirebilir, geri yükleyebilir ve son olarak önceki varsayımları onaylayın ya da iyileştirebilirsiniz. Bir deneme çalıştırdığınızda Machine Learning Studio (klasik), veri kümesi, modül ve bağlantı noktası bağlantıları ve parametreleri de dahil olmak üzere, çalıştırmanın bir geçmişini tutar. Bu geçmiş ayrıca sonuçları, başlatma ve durdurma zamanları, günlük iletileri ve yürütme durumu gibi çalışma zamanı bilgilerini de yakalar. Deneme ve ara sonuçlarının zaman sayısını gözden geçirmek için herhangi bir zamanda Bu çalıştırmaların herhangi birine geri dönebilirsiniz. Hatta basit, karmaşık veya hatta ensekele modelleme çözümleri oluşturmak üzere yolunuzda yeni bir sorgu ve bulma aşamasına geçmek için denemenizin önceki bir çalıştırmasını kullanabilirsiniz.

> [!NOTE]
> Bir deneyin önceki bir çalıştırmasını görüntülediğinizde, bu deneme sürümü kilitlenir ve düzenlenemez. Bununla birlikte, **farklı kaydet** ' i tıklayarak ve kopya için yeni bir ad sağlayarak bir kopyasını kaydedebilirsiniz. Machine Learning Studio (klasik), daha sonra düzenleyebileceğiniz ve çalıştırabileceğiniz yeni kopyayı açar. Denemenizin bu kopyası, **denemeleri** listesinde tüm diğer denemeleri ile birlikte kullanılabilir.
> 
> 

## <a name="view-the-prior-run"></a>Önceki çalıştırmayı görüntüleme
En az bir kez çalıştırdığınız denemenizin bulunduğu bir deneyiniz varsa, Özellikler bölmesinde **önceki Çalıştır** ' a tıklayarak deneme sürümünün önceki çalıştırmasını görüntüleyebilirsiniz.

Örneğin, deneme sürümü oluşturduğunuzu ve 11:23, 11:42 ve 11:55 sürümlerini çalıştırın. Deneme sürümü (11:55) öğesini açıp **önceki çalıştırma**' ya tıkladığınızda, 11:42 ' de çalıştırdığınız sürüm açılır.

## <a name="view-the-run-history"></a>Çalıştırma geçmişini görüntüleme
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

Machine Learning Studio (klasik) içindeki **denemeleri** sekmesinde bulunan denemeleri listesi her zaman bir denemenin en son sürümünü görüntüler. Deneme sürümü ' nü (önceki **çalıştırma** veya **Görünüm çalıştırma geçmişi** kullanarak) açarsanız, **çalışma geçmişini görüntüle** ' ye tıklayarak ve **düzenlenebilir** **durumuna** sahip yinelemeyi seçerek Taslak sürümüne dönebilirsiniz.

## <a name="run-a-previous-experiment"></a>Önceki bir deneme çalıştırın
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
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression