---
title: Sabit nokta ilişkileri ve yol bulma
description: Bağlantı ilişkilerinin arkasındaki kavramsal model hakkında bilgi edinin. Bir alan içindeki çıpası bağlamayı ve bir yol bulma senaryosunu karşılamak için yakındaki API 'yi kullanmayı öğrenin.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce79fe88b33659241a226af7bbb7a966ede41abb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97696162"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Azure uzamsal bağlayıcılarını ve bağlama ilişkilerini ve yolunu bulma

Bağlayıcı ilişkilerini kullanarak, bir alanda bağlı Tutturucular oluşturabilir ve ardından bunlar gibi sorular sorabilirsiniz:

* Yakında bağlayıcı var mı?
* Ne kadar uzakta?

## <a name="examples"></a>Örnekler

Bağlı bağlayıcıları bunlar gibi durumlarda kullanabilirsiniz:

* Çalışan bir endüstriyel fabrikadaki çeşitli konumları ziyaret eden bir görevi tamamlaması gerekir. Fabrikada her konumdaki uzamsal bağlantılar bulunur. HoloLens veya mobil uygulama, çalışan tarafından bir konumdan sonrakine kılavuzluk eder. Uygulama ilk olarak yakın uzamsal Tutturuculara sorar ve sonra çalışanı bir sonraki konuma yönlendirir. Uygulama, genel yönü ve bir sonraki konuma mesafeyi gösterir.

* Bir Museum, genel ekranlarda uzamsal bağlantılar oluşturur. Birlikte, bu Tutturucuların her biri, Museum 'un gerekli genel ekranların bir saatlik turuna göre yapılır. Ortak bir ekranda, ziyaretçiler, mobil cihazlarıyla Museum 'un karma gerçeklik uygulamasını açabilir. Daha sonra genel yönü görmek ve turdaki diğer genel ekranları bulmak için telefon kameralarını alanın çevresinde işaret ederler. Bir Kullanıcı genel bir ekranda ilerlemelerine göre, uygulama, kullanıcıya kılavuzluk eden genel yönü ve mesafeyi güncelleştirir.

## <a name="set-up-way-finding"></a>Yöntemi ayarlama-bulma

Gözetimi sağlayan bir uygulama, kılavuz sağlamak için *yol bulma yöntemini* kullanıyor. Yöntem-bulma işlemi, sırayla gezinmede farklılık açabilir. Açılan gezinmede, kullanıcılar duvarlar, kapıların ve katlar arasında kılavuzluk eder. Yöntemle, Kullanıcı hedefin genel yönü hakkında ipuçları alır. Ancak alanın çıkarım veya bilgisi, kullanıcının hedef yapıya göre gezinmenize de yardımcı olur.

Bir yöntem bulma deneyimi oluşturmak için ilk olarak deneyim için bir alan hazırlayın ve kullanıcıların etkileşime gileceği bir uygulama geliştirin. Bunlar kavramsal adımlardır:

1. **Alanı planlayın**: alanın içindeki konumların, yol bulma deneyiminin bir parçası olacağını belirleyin. Senaryolarımızda, fabrika idarecisi veya Museum Tur Düzenleyicisi, yol bulma deneyimine hangi konumlara dahil edileceğini karar verebilir.
2. **Bağlantı bağlantıları**: uzamsal bağlayıcı oluşturmak için seçili konumları ziyaret edin. Bunu, Son Kullanıcı uygulamasının bir yönetici modunda veya tamamen farklı bir uygulamada yapabilirsiniz. Her bir bağlayıcıyı birbirine bağlayacaksınız veya birbirleriyle ilişkilendirirsiniz. Hizmet bu ilişkileri korur.
3. **Son Kullanıcı deneyimini başlatın**: kullanıcılar, seçili konumlardan hiçbirinde olabilecek bir tutturucu bulmak için uygulamayı çalıştırır. Genel tasarımınız, kullanıcıların deneyimi girebilecekleri konumları belirlemelidir.
4. **Yakın bağlayıcıları bul**: Kullanıcı bir tutturucu bulduktan sonra, uygulama yakındaki bağlantıları isteyebilir. Bu yordam, cihaz ve bu bağlantılar arasında bir poz döndürür.
5. **Kullanıcıya kılavuzluk** edin: uygulama, kullanıcının genel yönü ve mesafesi hakkında rehberlik sağlamak için bu bağlayıcıların her birine ait pozları kullanabilir. Örneğin, uygulamadaki kamera akışı, aşağıdaki görüntüde gösterildiği gibi her bir olası hedefi temsil eden bir simge ve ok gösterebilir.
6. **Kılavuzu daraltın**: Kullanıcı yönergede olduğu gibi, uygulama cihaz ile hedef bağlantı noktası arasında düzenli olarak yeni bir poz hesaplayabilir. Uygulama, kullanıcının hedefe ulaşmaya yardımcı olan kılavuz ipuçlarını iyileştirmeye devam etmektedir.

    ![Uygulamanın yol bulma kılavuzunu nasıl gösterebileceğine ilişkin bir örnek](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Bağlantı bağlantıları

Bir yöntem bulma deneyimi oluşturmak için önce bağlantıları seçili konumlara yerleştirmeniz gerekir. Bu bölümde, uygulamanın yöneticisinin bu çalışmayı zaten tamamladığını varsayacağız.

### <a name="connect-anchors-in-a-single-session"></a>Bağlantıları tek bir oturumda bağlama

Bağlantıları bağlamak için:

1. İlk konuma kılavuzluk edin ve bir CloudSpatialAnchorSession kullanarak bağlayıcı oluşturun.
2. İkinci konuma kılavuzluk edin. Temel alınan MR/AR platformu hareketi izler.
3. Aynı CloudSpatialAnchorSession kullanarak bağlayıcı B oluşturun. A ve B bağlantıları artık bağlı. Uzamsal bağlayıcı hizmeti bu ilişkiyi tutar.
4. Kalan Tutturucuların yordamına devam edin.

### <a name="connect-anchors-in-multiple-sessions"></a>Birden çok oturumda bağlantıları bağlama

Uzamsal Tutturucuların birden çok oturum üzerinden bağlanmasını sağlayabilirsiniz. Bu yöntemi kullanarak, bir seferde bazı bağlayıcıları oluşturup bağlayıp daha sonra daha sonra daha fazla bağlayıcı oluşturup bağlayabilirsiniz.

Birden çok oturumun bağlayıcılarını bağlamak için:

1. Uygulama bir CloudSpatialAnchorSession içinde bazı bağlantılar (A ve B bağlantıları) oluşturur.
2. Farklı bir zamanda uygulama, yeni bir CloudSpatialAnchorSession kullanarak bu bağlayıcıların birini (örneğin, A bağlantısı) bulur.
3. Yeni bir konuma kılavuzluk edin. Temel karma gerçeklik veya genişletilmiş gerçeklik platformu, taşımayı izler.
4. Aynı CloudSpatialAnchorSession kullanarak bir bağlayıcı oluşturun. A, B ve C bağlantıları artık bağlı. Uzamsal bağlayıcı hizmeti bu ilişkiyi tutar.

Zaman içinde daha fazla bağlayıcı ve daha fazla oturum için bu yordama devam edebilirsiniz.

### <a name="verify-anchor-connections"></a>Bağlantı bağlantılarını doğrulama

Uygulama, bir üzerinde ayarını yaparak yaptığınız yakın bağlantı için bir sorgu vererek iki tutturun bağlandığını doğrulayabilirsiniz `NearAnchorCriteria` `CloudSpatialAnchorWatcher` . Sorgunun sonucu hedef bağlayıcıyı içerdiğinde, bağlantı bağlantısı doğrulanır. Bağlantı bağlantıları bağlı değilse, uygulama bunları yeniden bağlamayı deneyebilir.

Bağlantı oluşturulamalarının bağlanamamasının bazı nedenleri aşağıda verilmiştir:

* Bağlantıları bağlama işlemi sırasında temeldeki karma gerçeklik veya genişletilmiş gerçeklik platformu kayıp izleme.
* Uzamsal bağlayıcı hizmeti ile iletişim sırasında bir ağ hatası nedeniyle, bağlantı bağlantısı kalıcı hale getirilemedi.

### <a name="find-sample-code"></a>Örnek kod bulma

Bağlantı ve yakın sorguları nasıl bağlayabileceğinizi gösteren örnek kodu bulmak için bkz. [uzamsal Tutturucuların örnek uygulamaları](https://github.com/Azure/azure-spatial-anchors-samples).
