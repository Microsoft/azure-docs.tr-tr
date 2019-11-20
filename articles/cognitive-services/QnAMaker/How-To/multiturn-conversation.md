---
title: Çoklu açma konuşmaları-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Botunuzun bir sorudan diğerine kadar olan birden çok dönüşi yönetmek için istemleri ve bağlamı kullanın. Çoklu açma, önceki sorudaki bağlamın bir sonraki soruyu ve yanıtı etkilediği geri ve ileri bir konuşmaya sahip olabilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: 06b16af941004f6506b43fb36b4d79297b403595
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486892"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Birden çok konuşma geçişi oluşturmak için takip istemlerini kullanma

Botunuzun bir sorudan diğerine kadar olan birden çok _dönüşi yönetmek_için izleme komut istemlerini ve bağlamını kullanın.

Çoklu açma işlevinin nasıl çalıştığını görmek için aşağıdaki tanıtım videosunu görüntüleyin:

[Soru-Cevap Oluşturma ![çoklu-kapatma konuşması](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Çoklu açma konuşması nedir?

Bazı sorular tek bir sırayla yanıtlanamaz. İstemci uygulaması (sohbet botu) konuşmalarınızı tasarlarken, bir kullanıcı doğru yanıtı belirleyebilmek için filtrelenen veya iyileştirilmesi gereken bir soru sorabilir. Bu akışı, kullanıcıyı *takip eden istemlerle*sunarak mümkün olan sorulara göre yaparsınız.

Bir Kullanıcı soru sorarsa, Soru-Cevap Oluşturma yanıtı _ve_ herhangi bir izleme istemini döndürür. Bu yanıt, izleme sorularını seçimler halinde sunmanıza olanak tanır. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Sohbet botu ile örnek çoklu açma konuşması

Çok yönlü olarak, bir sohbet bot, aşağıdaki görüntüde gösterildiği gibi son yanıtı belirlemede Kullanıcı ile bir konuşmayı yönetir:

![Bir konuşmaya karşı kullanıcıya kılavuzluk eden istemlerle çoklu çift yönlü iletişim kutusu](../media/conversational-context/conversation-in-bot.png)

Önceki görüntüde, Kullanıcı **hesabımı**girerek bir konuşma başlattı. Bilgi Bankası 'nda üç bağlantılı soru-cevap çifti bulunur. Kullanıcı, yanıtı iyileştirmek için bilgi bankasındaki üç seçenekten birini seçer. Soru (#1), sohbet bot 'ta üç seçenek olarak sunulan üç izleme istemiyle sahiptir (#2). 

Kullanıcı bir seçenek seçtiğinde (#3), bir sonraki iyileştirme seçenekleri listesi (#4) sunulur. Bu dizi, Kullanıcı doğru, son yanıtı (#6) seçinceye kadar devam eder (#5).


### <a name="use-multi-turn-in-a-bot"></a>Bir bot 'ta Çoklu açma kullan

KB 'nizi yayımladıktan sonra Soru-Cevap Oluşturma bot 'ı Azure bot hizmetine dağıtmak için **bot oluştur** düğmesini seçebilirsiniz. İstemler, bot 'niz için etkinleştirdiğiniz sohbet istemcilerinde görüntülenir.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Belgenin yapısından çok yönlü konuşma oluşturma

Bir Bilgi Bankası oluşturduğunuzda, **KB 'Nizi doldur** bölümünde **URL 'ler,. PDF veya. docx dosyalarından çoklu açmayı etkinleştir** onay kutusunu görüntülenir. 

![Çoklu açma ayıklamasını etkinleştirmek için onay kutusu](../media/conversational-context/enable-multi-turn.png)

Bu seçeneği belirlediğinizde Soru-Cevap Oluşturma belge yapısında mevcut hiyerarşiyi ayıklar. Hiyerarşi, izlemek için ' de dönüştürülür ve hiyerarşinin kökü üst QnA işlevi görür. Bazı belgelerde hiyerarşinin kökünde yanıt olarak kullanılabilecek içerik yok, bu tür hiyerarşileri ayıklamak için ikame yanıt metni olarak kullanılacak ' varsayılan yanıt metni ' ' ni sağlayabilirsiniz.   

Çoklu açma yapısı yalnızca URL 'Ler, PDF dosyaları veya DOCX dosyalarından çıkarsanamıyor. Yapının bir örneği için, [Microsoft Surface Kullanıcı El Ile PDF dosyasının](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)bir görüntüsünü görüntüleyin. 

![! [Kullanıcı el ile yapı örneği] (.. /Media/, tional-Context/Import-File-with-konuşma tional-Structure.exe)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Kendi Çoklu açma belgenizi oluşturma

Çok yönlü bir belge oluşturuyorsanız lütfen aşağıdaki yönergeleri aklınızda bulundurun:

* Hiyerarşiyi göstermek için başlıklar ve alt başlıklar kullanın. Örneğin, komut istemi olarak alınması gereken QnA 'yi göstermek için üst QnA ve H2 'yi belirtmek üzere H1 'yi belirtebilirsiniz. Sonraki hiyerarşiyi göstermek için küçük başlık boyutunu kullanın. Belgenizde yapıyı daha fazla yapmak için stil, renk veya başka bir mekanizma kullanmayın, Soru-Cevap Oluşturma çoklu açma istemlerini ayıklamaz. 

* Bir başlığı soru işaretiyle sonlandırmayın, `?`. 

* [Örnek belgeyi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) bir örnek olarak kullanarak kendi Çoklu açma belgenizi oluşturabilirsiniz.

### <a name="adding-files-to-a-multi-turn-kb"></a>Birden çok açma KB 'ye dosya ekleme

Hiyerarşik bir belge eklediğinizde Soru-Cevap Oluşturma, konuşma akışı oluşturmak için yapıdan izleme istemlerini belirler. 

1. Soru-Cevap Oluşturma ' de, **URL 'ler,. PDF veya. docx dosyalarından çoklu açmayı etkinleştir** seçeneğiyle oluşturulan mevcut bir Bilgi Bankası seçin. Etkinletir. 
1. **Ayarlar** sayfasına gidin, eklenecek dosyayı veya URL 'yi seçin. 
1. Bilgi Bankası 'nı **kaydedip eğitme** .

> [!Caution]
> Yeni veya boş Bilgi Bankası için bir veri kaynağı olarak, aktarılmış bir TSV veya XLS çoklu çift yönlü bilgi tabanı dosyası kullanma desteği desteklenmez. Dışarı aktarılan çoklu oturum istemlerini bir bilgi tabanına eklemek için, bu dosya türünü Soru-Cevap Oluşturma portalının **Ayarlar** sayfasından **içeri aktarmanız** gerekir.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Oluşturma API 'SI ile çok yönlü istemlerle Bilgi Bankası oluşturma

[Soru-cevap oluşturma API oluşturma](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)' yı kullanarak çok yönlü istemlerle bir bilgi talebi oluşturabilirsiniz. İstemler `context` özelliğinin `prompts` dizisine ekliyor. 

## <a name="show-questions-and-answers-with-context"></a>Bağlamla soruları ve yanıtları göster

Görüntülenecek soru-cevap çiftlerini yalnızca bağlamsal konuşmalarla birlikte azaltın. 

**Görünüm seçeneklerini**belirleyin ve ardından **bağlamı göster**' i seçin. Liste, izleme istemleri içeren soru-cevap çiftlerini görüntüler. 

![Bağlama konuşmaları ile soru-cevap çiftlerini filtreleyin](../media/conversational-context/filter-question-and-answers-by-context.png)

Çoklu açma bağlamı ilk sütunda görüntülenir.

![! ["Bağlam (ÖNIZLEME)" sütunu] (.. /Media/, tional-Context/Surce-Manual-PDF-Lay-up-Prompt.exe)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Önceki görüntüde, **#1** sütundaki kalın metni gösterir ve bu da geçerli soruyu belirtir. Üst soru, satırdaki en üstteki öğedir. Aşağıdaki sorular, bağlantılı soru-cevap çiftleridir. Diğer bağlam öğelerine hemen gidebilmeniz için bu öğeler seçilebilir. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Bir izleme istemi olarak mevcut soru-cevap çifti ekleme

İlk soru olan **Hesabım**, **hesaplar ve oturum açma**gibi izleme istemlerine sahiptir. 

!["Hesaplar ve oturum açma" yanıtları ve izleme istemleri](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Mevcut bir soru-cevap çiftine Şu anda bağlı olmayan bir izleme istemi ekleyin. Soru ve yanıt çiftiyle bağlantılı olmadığından, geçerli görünüm ayarının değiştirilmesi gerekir.

1. Mevcut bir soru-cevap çiftini bir izleme istemi olarak bağlamak için, soru-cevap çiftinin satırını seçin. Yüzey el ile, listeyi azaltmak için **oturumu** Kapat ' ı arayın.
1. **SignOut**satırındaki **Yanıt** sütununda, **izleme istemi Ekle**' yi seçin.
1. **İzleme istemi** açılır penceresindeki alanlarda aşağıdaki değerleri girin:

    |Alan|Değer|
    |--|--|
    |Metni görüntüle|**Cihazı**kapatma ' yı girin. Bu, izleme isteminde görüntülenecek özel bir metindir.|
    |Yalnızca bağlam| Bu onay kutusunu seçin. Yanıt yalnızca soru, bağlam belirttiğinde döndürülür.|
    |Yanıta bağla|Mevcut soru-cevap çiftini bulmak için **oturum açma ekranını kullanın** yazın.|


1.  Bir eşleşme döndürülür. Bu yanıtı izleme olarak seçin ve ardından **Kaydet**' i seçin. 

    !["Izleme istemi (ÖNIZLEME)" sayfası](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. İzleme istemi ekledikten sonra, üst gezinmede **Kaydet ve eğitme** ' yi seçin.
  
### <a name="edit-the-display-text"></a>Görüntüleme metnini düzenleme 

Bir izleme istemi oluşturulduğunda ve **Yanıt bağlantısı**olarak mevcut soru-cevap çifti girildiğinde, yeni **görüntü metni**girebilirsiniz. Bu metin, mevcut sorunun yerini almaz ve yeni bir alternatif soru eklemez. Bu değerlerden ayrıdır. 

1. Görüntüleme metnini düzenlemek için **bağlam** alanında soruyu arayın ve seçin.
1. Söz konusu sorunun satırında, yanıt sütununda izleme istemi ' ni seçin. 
1. Düzenlemek istediğiniz görüntü metnini seçin ve ardından **Düzenle**' yi seçin.

    ![Görüntü metni için Düzenle komutu](../media/conversational-context/edit-existing-display-text.png)

1. **İzleme istemi** açılır penceresinde, varolan görüntüleme metnini değiştirin. 
1. Görüntü metnini düzenlemenizi bitirdiğinizde **Kaydet**' i seçin. 
1. Üst gezinti çubuğunda **Kaydet ve eğitme**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Yeni soru-cevap çiftini bir izleme istemi olarak ekleyin

Bilgi Bankası 'na yeni bir soru-cevap çifti eklediğinizde, her bir çiftin bir izleme istemi olarak mevcut bir soruya bağlanması gerekir.

1. Bilgi Bankası araç çubuğunda, **hesaplar ve oturum açmak**için mevcut soru-cevap çiftini arayıp seçin. 

1. Bu sorunun **Yanıt** sütununda, **izleme istemi Ekle**' yi seçin. 
1. **İzleme istemi (Önizleme)** altında, aşağıdaki değerleri girerek yeni bir izleme istemi oluşturun: 

    |Alan|Değer|
    |--|--|
    |Metni görüntüle|*Bir Windows hesabı oluşturun*. İzleme isteminde görüntülenecek özel metin.|
    |Yalnızca bağlam|Bu onay kutusunu seçin. Bu yanıt yalnızca soru bağlam belirttiğinde döndürülür.|
    |Yanıta bağla|Yanıt olarak aşağıdaki metni girin:<br>*Yeni veya var olan bir e-posta hesabıyla bir Windows hesabı [oluşturun](https://account.microsoft.com/)* .<br>Veritabanını kaydedip eğitmeniz durumunda bu metin dönüştürülür. |
    |||

    ![Yeni bir istem sorusu ve yanıtı oluşturma](../media/conversational-context/create-child-prompt-from-parent.png)


1. **Yeni oluştur**' u seçin ve ardından **Kaydet**' i seçin. 

    Bu eylem yeni bir soru-cevap çifti oluşturur ve seçilen soruyu bir izleme istemi olarak bağlar. **Bağlam** sütunu her iki soru için de bir izleme istemi ilişkisini gösterir. 

1. **Görünüm seçeneklerini**belirleyin ve ardından [**BAĞLAMı göster (Önizleme)** ](#show-questions-and-answers-with-context)öğesini seçin.

    Yeni soru, nasıl bağlandığını gösterir.

    ![Yeni bir izleme istemi oluşturun](../media/conversational-context/new-qna-follow-up-prompt.png)

    Üst soru, seçimlerinden biri olarak yeni bir soru görüntüler.

    ![! [Bağlam sütunu her iki soru için de bir izleme istemi ilişkisi gösterir] (.. /Media/konuşma tional-Context/Child-Prompt-Created.exe)](../media/conversational-context/child-prompt-created.png#lightbox)

1. İzleme istemi ekledikten sonra, üst gezinti çubuğunda **Kaydet ve eğitme** ' yi seçin.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>İzleme istemlerinin testi sırasında çoklu açmayı etkinleştir

**Test** bölmesindeki izleme istemleri ile soruyu test ettiğinizde, **Çoklu açmayı etkinleştir**' i seçin ve ardından sorunuzu girin. Yanıt, izleme istemlerini içerir.

![Yanıt, izleme istemlerini içerir](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Çoklu açmayı etkinleştirmezseniz, yanıt döndürülür ancak takip eden istemler döndürülmez.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Bir başlangıç yanıtı ve izleme istemleri döndüren bir JSON isteği

Kullanıcının sorusunun yanıtını istemek ve izleme istemleri eklemek için boş `context` nesnesini kullanın. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Bir başlangıç yanıtı ve izleme istemleri döndüren bir JSON yanıtı

Yukarıdaki bölüm, **hesaplara ve oturum**açmaya yönelik bir yanıt ve herhangi bir izleme istemi istedi. Yanıt, *yanıtları [0]. bağlam*içinde bulunan istem bilgilerini ve kullanıcıya görüntülenecek metni içerir. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` dizisi `displayText` özelliğinde ve `qnaId` değerinde metin sağlar. Bu yanıtları, konuşma akışındaki sonraki görüntülenmiş seçimler olarak gösterebilir ve ardından seçili `qnaId` aşağıdaki istekte Soru-Cevap Oluşturma geri gönderebilirsiniz. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>İlk yanıt olmayan yanıt ve izleme istemlerini döndüren JSON isteği

`context` nesnesini, önceki bağlamını içerecek şekilde doldurur.

Aşağıdaki JSON isteğinde, geçerli soru, *oturum açmak Için Windows Hello* ve önceki soruda *hesaplar ve oturum açmak*için kullanılır. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>İlk yanıt olmayan yanıt ve izleme komut istemlerini döndüren JSON yanıtı

Soru-Cevap Oluşturma _Generateanswer_ JSON yanıtı, `answers` nesnesindeki ilk öğenin `context` özelliğindeki izleme istemlerini içerir:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Bilgi Bankası 'nı Soru-Cevap Oluşturma KIMLIĞIYLE sorgulayın

Çoklu açma özelliğini kullanarak özel bir uygulama oluşturuyorsanız. İlk sorunun yanıtında, tüm izleme istemleri ve onunla ilişkili `qnaId` döndürülür. KIMLIĞINIZ olduğuna göre, bunu izleme isteminin istek gövdesinde geçirebilirsiniz. İstek gövdesi `qnaId`ve bağlam nesnesi (önceki Soru-Cevap Oluşturma özelliklerini içeren) içeriyorsa, bu durumda GenerateAnswer, soru metninin yanıtını bulmak için derecelendirme algoritmasını kullanmak yerine KIMLIĞE göre tam soruyu döndürür. 


## <a name="display-order-is-supported-in-the-update-api"></a>Güncelleştirme API 'sinde görüntüleme sırası destekleniyor

JSON yanıtında döndürülen [görüntüleme metni ve görüntüleme sırası](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), [güncelleştirme API 'si](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)tarafından düzenlenmek üzere desteklenir. 

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Güncelleştirme API 'SI ile çoklu açma istemlerini ekleme veya silme

[Soru-cevap oluşturma Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)'sini kullanarak çok yönlü istemler ekleyebilir veya silebilirsiniz.  İstemler `context` özelliğinin `promptsToAdd` dizisine ve `promptsToDelete` dizisine ekliyor. 

## <a name="export-knowledge-base-for-version-control"></a>Sürüm denetimi için Bilgi Bankası dışarı aktarma

Soru-Cevap Oluşturma, Soru-Cevap Oluşturma portalındaki [Sürüm denetimini destekler](../concepts/development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) ve bu, dışarıya açılan dosyadaki çok yönlü konuşma adımlarını dahil edin.

## <a name="next-steps"></a>Sonraki adımlar

Bu [iletişim kutusu](https://aka.ms/qnamakermultiturnsample) örneğinden bağlamsal konuşmalar hakkında daha fazla bilgi edinin veya [çok yönlü konuşmalar için kavramsal bot tasarımı](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Bilgi Bankası geçirme](../Tutorials/migrate-knowledge-base.md)
