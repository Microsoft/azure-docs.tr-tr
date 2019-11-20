---
title: Azure Notification Hubs ve Google Firebase Cloud Messaging (FCM) geçişi
description: Azure Notification Hubs 'ın Google GCM 'e FCM geçişine nasıl adresleneceğini açıklar.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 80eae09240bde61870995468485338db5f0b9c2d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212309"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs ve Google Firebase Cloud Messaging (FCM) geçişi

## <a name="current-state"></a>Geçerli durum

Google, Google Cloud Messaging (GCM) kaynağından Firebase Cloud Messaging 'e (FCM) ait geçişi duyurduğu zaman, bizler gibi anında iletme hizmetleri, bu değişikliğe uyum sağlamak için Android cihazlara nasıl bildirim gönderildiğini ayarlamamız gerekiyordu.

Hizmet arka ucumuzu güncelleştirdik ve gereken güncelleştirmeleri API 'imizde ve SDK 'larda yayımladık. Uygulamamız sayesinde, müşteri etkisini en aza indirmek için mevcut GCM bildirim şemalarıyla uyumluluğu sağlama kararı yaptık. Bu, şu anda FCM eski modda FCM kullanarak Android cihazlara Bildirim gönderdiğimiz anlamına gelir. Son olarak, yeni özellikler ve yük biçimi dahil olmak üzere FCM için gerçek destek eklemek istiyoruz. Bu, daha uzun süreli bir değişiklik ve geçerli geçiş, mevcut uygulamalar ve SDK 'lara uyumlulukla korunmaya odaklanılmıştır. Uygulamanızda GCM veya FCM SDK 'larını (SDK 'umuza birlikte) kullanabilirsiniz ve bildirimin doğru şekilde gönderildiğinden emin olun.

Bazı müşteriler son zamanlarda, bildirimler için GCM uç noktası kullanan uygulamalar hakkında Google uyarısında bir e-posta aldı. Bu yalnızca bir uyarıdır ve hiçbir şey yok. uygulamanızın Android bildirimleri işlenmek üzere Google 'a gönderilmeye devam ediyor ve Google hala işliyor. GCM uç noktasını hizmet yapılandırmasında açıkça belirtmiş bazı müşteriler hala kullanım dışı uç noktasını kullanıyor. Bu boşluğu zaten tanımladık ve Google e-postayı gönderdikten sonra sorunu gidermeye çalışıyoruz.

Bu kullanım dışı bitiş noktasını değiştirdik ve düzeltmeler dağıtıldı.

## <a name="going-forward"></a>İleri git

Google 'ın FCM hakkında SSS, herhangi bir şey yapmanız gerekmez. [FCM hakkında SSS](https://developers.google.com/cloud-messaging/faq)bölümünde, Google "Istemci SDK 'LARı ve GCM belirteçleri sürekli olarak çalışmaya devam edecektir. Ancak, FCM 'ye geçmediğiniz müddetçe Android uygulamanızda Google Play Hizmetleri en son sürümünü hedefleyebilirsiniz. "

Uygulamanız GCM kitaplığını kullanıyorsa, devam edin ve Google 'ın yönergelerini izleyerek uygulamanızdaki FCM kitaplığına yükseltin. SDK 'imiz ile uyumludur, bu nedenle uygulamanızdaki herhangi bir şeyi bizim tarafımızda güncelleştirmeniz gerekmez (SDK sürümümüzle güncel olduğunuz sürece).

## <a name="questions-and-answers"></a>Sorular ve yanıtlar

Müşterilerin duyduğu yaygın soruların bazı yanıtları aşağıda verilmiştir:

**S:** Kesme tarihi ile uyumlu olması için ne yapmam gerekir (Google 'ın geçerli kesme tarihi 29 Mayıs ve değişebilir)?

**C:** Yapma. Mevcut GCM bildirim şeması ile uyumluluğu koruyacağız. GCM anahtarınız, uygulamanız tarafından kullanılan tüm GCM SDK 'Ları ve kitaplıkları olacak şekilde normal şekilde çalışmaya devam edecektir.

Yeni özelliklerden yararlanmak için FCM SDK 'larına ve kitaplıklarına yükseltmeye karar verirseniz, GCM anahtarınız çalışmaya devam edecektir. İsterseniz FCM anahtarını kullanarak geçiş yapabilirsiniz, ancak yeni Firebase projesi oluştururken var olan GCM projenize Firebase 'i eklediğinizden emin olabilirsiniz. Bu, uygulamanın GCM SDK 'larını ve kitaplıklarını kullanmaya devam eden eski sürümlerini çalıştıran müşterilerinizle geriye dönük uyumluluğu garanti eder.

Yeni bir FCM projesi oluşturuyorsanız ve var olan GCM projesine ekleme yapmadıysanız, yeni FCM gizli anahtarı ile Notification Hubs güncelleştirdikten sonra, yeni FCM anahtarının eski GCM 'ye bağlantısı olmadığından, geçerli uygulama yüklemelerine bildirim gönderme özelliğini kaybedersiniz. Proje.

**S:** Bu e-postayı neden kullanılmakta olan eski GCM uç noktaları hakkında alıyorum? Ne yapmam gerekir?

**C:** Yapma. Yeni uç noktalara geçiş yaptık ve yakında bitirilecektir, bu nedenle hiçbir değişiklik gerekli değildir. Hiçbir şey yok, kaçırılmış bir uç nokta yalnızca Google 'dan gelen uyarı iletilerine neden oldu.

**S:** Mevcut kullanıcıları bozmadan yeni FCM SDK 'larına ve kitaplıklarına nasıl geçiş yapabilirim?

Y: İstediğiniz zaman yükseltin. Google henüz var olan GCM SDK 'larını ve kitaplıklarını kullanımdan kaldırma hakkında duyurmadı. Mevcut kullanıcılarınıza anında iletme bildirimleri kesmemenizi sağlamak için, var olan GCM projenizle ilişkilendirdiğiniz yeni Firebase projesini oluştururken emin olun. Bu, yeni Firebase gizliliklerin, GCM SDK 'Ları ve kitaplıkları ile uygulamanızın eski sürümlerini çalıştıran kullanıcılar için ve FCM SDK 'Ları ve kitaplıkları ile uygulamanızın yeni kullanıcıları için çalışacaktır.

**S:** Bildirimlerim için yeni FCM özelliklerini ve şemalarını ne zaman kullanabilirim?

**C:** API ve SDK 'larımız için bir güncelleştirme yayımladığımızda, daha sonra gelen aylarda sizin için bir şey olması beklenir.
