---
title: Diğer bot oluşturma teknolojileriyle Conversation Learner kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner diğer bot oluşturma teknolojileriyle nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c964c62c34f952a547d077e93e7bb4d0eb7b192d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703672"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Diğer bot oluşturma teknolojileriyle Conversation Learner kullanma

Bu öğreticide, diğer bot oluşturma teknolojileriyle Conversation Learner kullanımı ve bu teknolojiler arasında belleğin (veya durumun) nasıl paylaşılacağını ele alınmaktadır. 

## <a name="video"></a>Video

[![Karma botlar öğretici Önizleme](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, günlük Iletişim kutusu Web Kullanıcı arabirimine değil günlük iletişimleri oluşturmak için bot öykünücüsünün kullanılması gerekir. Bot Framework öykünücüsünü ayarlama hakkında daha fazla bilgiye [buradan](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0)ulaşabilirsiniz. 

Bu öğretici karma Öğretici bot 'ın çalışmasını gerektirir:

    npm run tutorial-hybrid

## <a name="details"></a>Ayrıntılar

Conversation Learner denetimde olduğunda, Conversation Learner oturumuna göre tüm durum Conversation Learner bellek yöneticisinde depolanmalıdır. Bu, makine öğrenimi, konuşmayı nasıl kullanacağınızı anlamak için durumunu kullandığı için gereklidir. Dış durum, oturum başladığında çağrılan OnSessionStartCallback içindeki Conversation Learner geçirilebilir. Oturum sonlandırıldığında OnSessionEndCallback tarafından iç durum döndürülebilir.

Conversation Learner, bir ilk durumu alan ve değer döndüren bir işlev çağrısı olarak neredeyse düşünebilirsiniz.

Bu örnekte, iki farklı sistem kullanan bir karma bot oluşturacaksınız:
1. Conversation Learner modeli <br/>
    Geçerli oturuma göre bot 'un sonraki eylemini öğrenmek için konuşmayı öğrenme modelini kullanır. Bot 'un bu bölümü ilk durum `isOpen` parçasını alır (bir deponun açık veya kapalı olduğunu gösterir) ve başka bir durum `purchaseItem` parçası (kullanıcının satın aldığı bir öğenin adı) döndürür.

2. Metin eşleştirme <br />
    Yalnızca belirli dizeler için gelen metne bakar ve yanıt verir. Bot 'un bu bölümü, botların diğer depolama mekanizmalarını yönetir ve CL oturumunun başlatılmasından sorumludur. Özellikle üç değişkeni yönetir: `usingConversationLearner`, `storeIsOpen`, ve `purchaseItem`.

Bu tanıtımda kullanılan modele göz atacağız.

### <a name="open-the-demo"></a>Tanıtımı açın

Web Kullanıcı arabiriminde "öğreticileri Içeri aktar" düğmesine tıklayın ve "öğretici-16-HybridBot" adlı modeli seçin.

## <a name="entities"></a>Varlıklar

Varlıklar sayfasını açın ve iki varlık olduğuna dikkat edin `isOpen` : ve`purchaseItem`

Bu varlıkların nasıl kullanıldığını anlamak için dosyayı açın: `C:\<installedpath>\src\demos\tutorialHybrid.ts` geri çağırmaları aramak için.

İçindeki `OnSessionStartCallback` kodun,, Conversation Learner için kullanılabilir olması için `storeIsOpen` , botbuilder konuşma depolamadan değeri, `isOpen` varlık değeri olarak kopyaladığına dikkat edin. Aşağıdaki koda bakın:

![](../media/tutorial17_sessionstart.PNG)

Benzer şekilde, içindeki `OnSessionEndCallback` kod (oturum yalnızca öğrenilmiş bir etkinlik nedeniyle sonlandırıldıysa ve yalnızca bir zaman aşımı değil), varlığın `purchaseItem` değerini botbuilder depolama alanına `purchaseItem`kopyalar. Aşağıdaki koda bakın:

![](../media/tutorial17_sessionend.PNG)

Şimdi eylemlere göz atalım.

## <a name="actions"></a>Eylemler

Modelde dört eylem olduğuna dikkat edin.

Eylemlerin amaçlanan kuralları aşağıdaki gibidir:

- `isOpen` Varlık ayarlandıysa, bot "ne satın almak istiyorsunuz?" soracaktır. ve bunu `puchaseItem` yuvada depolayın.
- `isOpen` Ayarlanmamışsa, bot "özür dileriz" diyorum.
- Diğer iki eylem türüdür `END_SESSION`.
- END_SESSION eylemi, görüşmenin tamamlandığını öğrendiğini gösterir.

### <a name="overall-bot-logic"></a>Genel bot mantığı

İlk olarak, bot durumunun `usingConversationLearner` bayrağı ayarlandıysa Conversation Learner denetimi geçiyoruz. Aksi takdirde, denetimi başka bir şeye geçiyoruz.  Bu örnekte, basit metin eşleştirme gösteriliyor, ancak bu, LUSıS, QnA yapıcısı ve hatta başka bir Conversation Learner örneği de dahil olmak üzere başka bir bot teknolojisi olabilir.

Kullanıcının mağazayı açması ve kapatması için bir yönteme ihtiyacımız var, bu yüzden "açık mağaza" ve "depoyu kapat" ile bir dize karşılaştırması yaptık ve "storeIsOpen" bayrağını ayarlayacağız.

Daha sonra, Conversation Learner modelinize denetim sağlamak için bir yol gerekir. "Shop" dizesiyle eşleştiğimiz zaman şunları yaptık:
- Bot 'ın belleğindeki bayrağını ayarlayın. `usingConversationLearner`
- Conversation Learner modelimizde "StartSession" yöntemini çağırın.  Bu işlem, `isOpen` varlık değerini başlatacak olan "onsessionstartcallback" öğesini tetikler

Aşağıya bakın:

![](../media/tutorial17_useConversationLearner.PNG)

Ayrıca, bu son satın alma öğesini görüntüleyen "geçmiş" e bir metin eşleşmesi de yaptık.
Son olarak, başka bir şey yazılmışsa, kullanılabilir Kullanıcı komutlarını görüntüliyoruz

## <a name="train-dialog"></a>Iletişim kutusunu eğitme

Bu öğretici için, model önceden eğitilmiş.  Uygulamada başlangıç ve bitiş oturumu geri çağırmaları etkisini görmek için tam bot 'ı test edeceğiz.

## <a name="testing-the-bot"></a>Bot test ediliyor

Tek konuşmaya leaner model botları 'nın aksine, bunu yalnızca Conversation Learner modeli tarafından işlenen öğeleri gösterebildiği için Conversation Learner Kullanıcı arabiriminde test edemeyeceksiniz.

### <a name="install-the-bot-framework-emulator"></a>Bot Framework öykünücüsünü yükler

- [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) kısmına gidin.
- Öykünücüyü indirin ve yükleyin.

### <a name="configure-the-emulator"></a>Öykünücüyü yapılandırma

- Öykünücüyü açın ve URL 'nin, bot 'unuzun üzerinde çalıştığı aynı bağlantı noktasını hedeflediğinden emin olun. Mesinden`http://localhost:3978/api/messages`

### <a name="test"></a>Test etme 

#### <a name="scenario-1-store-is-closed"></a>Senaryo 1: Mağaza kapalı
1. ' Shop ' girin. Bu, metin eşleştirme tarafından işlenir ve Conversation Learner modeline denetim verecektir.
2. ' Merhaba ' girin.  `isOpen` Değer ayarlanmadığından, bot "özür dileriz" diyorum ve oturumu sonlandıracaktır.

#### <a name="scenario-2-store-is-open"></a>Senaryo 2: Depo açık
1. ' Open Store ' girin.  Bu işlem, `isOpen` değerini true olarak ayarlar.
1. ' Shop ' girin.
1. ' Merhaba ' girin.  `isOpen` Değer true olarak ayarlandığından, bot "ne satın almak istiyorsunuz?" dilecektir.
1. ' Sandalyesi ' girin. ' sandalyesi ' varlık `purchaseItem`olarak CL belleğine kaydedilir. Bu değeri konuşma deposuna kopyalayan son oturum geri çağırması çağrılır.
1. ' History ' girin.  Bu, son `purchaseItem`yaptığınız gibi ' sandalyesi satın aldınız ' deyin.

## <a name="conclusion"></a>Sonuç

Yukarıdaki öğrendikleriniz sayesinde, Conversation Learner diğer bir bot oluşturma teknolojisiyle birleştirebilmelisiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dallandırma ve geri alma](./17-branch-undo.md)
