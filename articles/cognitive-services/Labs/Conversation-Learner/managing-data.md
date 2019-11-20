---
title: Conversation Learner ile Kullanıcı verilerini yönetme-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner ile Kullanıcı verilerini yönetmeyi öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 857e899764d284e2d78f1172fa8eeac04c57d618
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705189"
---
# <a name="managing-user-data"></a>Kullanıcı verilerini yönetme

Bu sayfa, son kullanıcılarla iletişim kutuları çalıştırırken Conversation Learner bulut hizmetinin ne günlüğe kaydettiğini açıklar.  Ayrıca, belirli bir kullanıcıyla ilişkili tüm günlükleri alabilmeniz veya silebilmeniz için Conversation Learner günlüklerinin Kullanıcı kimlikleriyle nasıl ilişkilendirileceğini açıklar.

## <a name="overview-of-end-user-data-logging"></a>Son Kullanıcı veri günlüğüne genel bakış

Varsayılan olarak, Conversation Learner bulut hizmeti, son kullanıcılar ile bot 'niz arasındaki etkileşimleri günlüğe kaydeder.  Bu Günlükler, bot 'ın yanlış varlığı ayıkladığı veya yanlış eylemi seçtiği durumları tanımlamanızı sağlayan botunuzu iyileştirmek için önemlidir.  Bu hatalar daha sonra Kullanıcı arabiriminin "günlük Iletişim kutuları" sayfasına gidip düzeltmeler yaparak ve bu düzeltilen iletişim kutusu yeni bir eğitme iletişim kutusu olarak depolanarak düzeltilebilir. Daha fazla bilgi için "günlük Iletişim kutuları" hakkında öğreticiye bakın.

## <a name="how-to-disable-logging"></a>Günlüğe kaydetmeyi devre dışı bırakma

Son kullanıcılara sahip olan konuşmalar Conversation Learner modelinizdeki "Ayarlar" sayfasında olup olmadığını kontrol edebilirsiniz.  "Günlük konuşmaları" için bir onay kutusu vardır.  Bu kutunun işaretini kaldırarak, son kullanıcılar ile konuşmalar günlüğe kaydedilmez.

## <a name="what-is-logged"></a>Günlüğe kaydedilen 

Günlük iletişim kutularında, Conversation Learner her bir ve için Kullanıcı girişini, varlık değerlerini, seçili eylemleri ve zaman damgalarını depolar.  Bu günlükler bir süre boyunca depolanır ve sonra atılır (Ayrıntılar için "varsayılan değer ve sınırlar" sayfasındaki yardım sayfasına bakın).  

Conversation Learner, günlüğe kaydedilen her iletişim kutusu için benzersiz bir KIMLIK oluşturur.  Conversation Learner, oturum açılan diyaloglarla bir Kullanıcı *tanımlayıcısı depolamaz.*  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Günlüğe kaydedilen iletişim kutularını bir Kullanıcı KIMLIĞIYLE ilişkilendirme

Günlüğe kaydedilen iletişim kutularını kullanıcının KIMLIĞIYLE ilişkilendirebilmek önemlidir. Örneğin, belirli bir kullanıcıdan günlüğe kaydedilmiş iletişim kutularını almak veya silmek için.  Conversation Learner bir kullanıcı tanımlayıcısı depomadığından, bu ilişkinin geliştirici kodu tarafından saklanması gerekir.  

Bu eşlemeyi oluşturmak için, ' de `EntityDetectionCallback`günlüğe kaydedilen iletişim kutusunun kimliğini edinin; ardından, bot 'ın deposunda, Kullanıcı kimliği ve bu günlüğe kaydedilmiş iletişim kutusu arasındaki ilişkilendirmeyi saklayın.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>HTTP çağrıları için üst bilgiler

Aşağıdaki HTTP çağrılarının her birinde aşağıdaki üstbilgiyi ekleyin:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

`<LUIS_AUTHORING_KEY>` , Conversation Learner uygulamalarınıza erişmek için kullanılan luın yazma anahtarıdır.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Günlüğe kaydedilen iletişim kutusu için ham verileri edinme

Günlük iletişim kutusu için ham verileri almak üzere bu HTTP çağrısını kullanabilirsiniz:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

, Bu Conversation Learner modelinin GUID 'sidir ve `<logDialgoId>` almak istediğiniz günlük iletişim kutusunun kimliğidir. `<appId>`  

> [!NOTE]
> Günlük iletişimleri geliştirici tarafından düzenlenebilir ve ardından tren iletişim kutusu olarak depolanabilir.  Bu tamamlandığında, Conversation Learner "kaynak" günlük iletişim kutusunun KIMLIĞINI eğitme iletişim kutusuyla depolar.  Ayrıca, eğitme iletişim kutusu kullanıcı arabiriminde "dallanmış" olabilir; eğitme iletişim kutusunun ilişkili bir kaynak günlük iletişim kutusu KIMLIĞI varsa, bu tren iletişim kutusundan dallar aynı günlük iletişim kutusu KIMLIĞIYLE işaretlenir.

Bir günlük iletişim kutusundan türetilmiş tüm tren iletişim kutularını almak için aşağıdaki adımları izleyin.

İlk olarak, tüm tren iletişim kutularını alın:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

`<appId>` Bu Conversation Learner modelinin GUID 'sidir.  

Bu, tüm tren iletişim kutularını döndürür.  Bu listede ilişkili `sourceLogDialogId`' i arayın ve ilişkili `trainDialogId`öğesine göz önünde bulun. 

KIMLIĞE göre tek bir tren iletişim kutusuna:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

, Bu Conversation Learner modelinin GUID 'sidir ve `<trainDialogId>` almak istediğiniz tren iletişim kutusunun kimliğidir. `<appId>`  

## <a name="how-to-delete-a-logged-dialog"></a>Günlüğe kaydedilen iletişim kutusunu silme

KIMLIĞI verilen bir günlük iletişim kutusunu silmek istiyorsanız, bu HTTP çağrısını kullanabilirsiniz:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

, Bu Conversation Learner modelinin GUID 'sidir ve `<logDialogId>` silmek istediğiniz günlük iletişim kutusunun kimliğidir. `<appId>` 

Bir tren iletişim kutusunu kendi KIMLIĞIYLE silmek istiyorsanız, bu HTTP çağrısını kullanabilirsiniz:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

, Bu Conversation Learner modelinin GUID 'sidir ve `<trainDialogId>` silmek istediğiniz tren iletişim kutusunun kimliğidir. `<appId>` 
