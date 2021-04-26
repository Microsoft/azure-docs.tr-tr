---
title: 'Nasıl yapılır: özel komutlar uygulamasını uzak beceri konuşma hizmeti olarak dışarı aktarma'
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel komutlar uygulamasını bir yetenek olarak dışarı aktarmayı öğreneceksiniz
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025883"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Özel komutlar uygulamasını uzak bir yetenek olarak dışarı aktar

Bu makalede, özel bir komut uygulamasını uzak bir yetenek olarak dışarı aktarmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar
> [!div class="checklist"]
> * [Bot Framework beceriyi anlama](/azure/bot-service/skills-conceptual)
> * [Yetenek bildirimini anlama](https://aka.ms/speech/cc-skill-manifest)
> * [Bir bot Framework bot 'tan yetenek çağırma](/azure/bot-service/skills-about-skill-consumers)
> * Varolan özel komutlar uygulaması. Özel bir komut uygulamanız yoksa,- [hızlı başlangıç: özel komutlar kullanarak bir ses Yardımcısı oluşturma](quickstart-custom-commands-application.md) ile deneyin

## <a name="custom-commands-as-remote-skills"></a>Uzak beceriler olarak özel komutlar
* Bot Framework becerileri, konuşma kullanımı ve bir bot 'ın dakikalar içinde çok çeşitli işlevler eklemenize olanak tanıyan bir altyapı oluşturma bloklarında yeniden kullanılabilir. Bu konuda daha fazla bilgi edinmek için, [bot Framework becerisi](https://microsoft.github.io/botframework-solutions/overview/skills/)' ne gidin.
* Özel bir komutlar uygulaması yetenek olarak verilebilirler. Bu beceri daha sonra, bir bot Framework bot 'tan uzak beceriler protokolü üzerinden çağrılabilir.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Bir uygulamayı uzak bir yetenek olarak sunulacak şekilde yapılandırma

### <a name="application-level-settings"></a>Uygulama düzeyi ayarları
1. Sol bölmede **Ayarlar**  >  **uzak beceriler**' ı seçin.
1. **Uzaktan becerileri etkin yap özelliği** açık olarak ayarlayın.

### <a name="authentication-to-skills"></a>Becerilerle kimlik doğrulaması
1. Kimlik doğrulamasını etkinleştirmek istiyorsanız, özel komutlar uygulamasını çağırmak için yapılandırmak istediğiniz bot Framework botları 'nın Microsoft uygulama kimliklerini ekleyin.
      > [!div class="mx-imgBorder"]
      > ![Yeteneğe bir MSA kimliği ekleyin](media/custom-commands/skill-add-msa-id.png)

1. Listeye en az bir giriş eklenirse, uygulama üzerinde kimlik doğrulaması etkinleştirilir ve yalnızca izin verilen botların uygulamayı çağırabilmesi gerekir.
> [!TIP]
>  Kimlik doğrulamasını devre dışı bırakmak için, tüm Microsoft uygulama kimliklerini izin verilenler listesinden silin. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Yetenek olarak ortaya çıkarmak için komutları etkinleştirin/devre dışı bırakın

Uzak yetenekler üzerinden hangi komutları dışarı aktarmak istediğinizi seçebilirsiniz.

1. Bir komutu yetenekler üzerinde göstermek için **yetenekler Için etkinleştir komutlarının** altında **Yeni bir komutu etkinleştir** ' i seçin.
1. Açılan menüden, eklemeyi düşündüğünüz komutu seçin.
1. **Kaydet**’i seçin.

### <a name="configure-triggering-utterances-for-commands"></a>Komutlar için tetikleme tetiklemesini yapılandırma
Özel komutlar, SÖYLERE tetikleme becerileri oluşturmak için komutlar için yapılandırılan örnek cümleleri kullanır. Bu **tetikleme tetiklenmesi** , **dağıtıcı** bölümü [**yetenek bildirimini**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)oluşturmak için kullanılacaktır.

Yazar olarak, becerilerin tetiklenmesi için kullanılan **örnek cümlelerinizi** denetlemek isteyebilirsiniz.
1. Varsayılan olarak, bir komuttan gelen **tetiklemenin tüm örnekleri** bildirim dosyasına dahil edilir.
1. Herhangi bir örneği açıkça ortadan kaldırmak istiyorsanız, **yetenekler Için etkin komutlar** bölümünde **Düzenle** simgesini seçin.
    > [!div class="mx-imgBorder"]
    > ![Yetenek için etkin bir komutu düzenleme](media/custom-commands/skill-edit-enabled-command.png)

1. Ardından, atlamak istediğiniz örnek cümlelerde   >  **örnek tümceyi devre dışı bırak**' a sağ tıklayın.
    > [!div class="mx-imgBorder"]
    > ![Örnekleri devre dışı bırak](media/custom-commands/skill-disable-example-sentences.png)

1. **Kaydet**’i seçin.
1. Bu pencerede yeni bir örnek ekleyemiyorum fark edeceksiniz. Bunu yapmanız gereken bir sorun varsa, ayarlar bölümünden çık bölümüne ilerleyin ve Accordion **komutlarından** ilgili komutu seçin. Bu noktada, **örnek cümleler** bölümüne yeni giriş ekleyebilirsiniz. Bu değişiklik, komut için uzak beceriler ayarları değerine otomatik olarak yansıtılır.

> [!IMPORTANT]
> Mevcut örnek **tümcelerinin String > Catalog** Data-Type öğesine başvuruları varsa, bu tümceler, utterler listesini tetikleyen yetenekler arasından otomatik olarak atlanır. 

## <a name="download-skill-manifest"></a>Yetenek bildirimini indir
1. Uygulamanızı **yayımladıktan** sonra, yetenek bildirimi dosyasını indirebilirsiniz.
1. Bot Framework tüketici botunuzu özel komutların yeteneklerine çağırmak üzere yapılandırmak için yetenek bildirimini kullanın.
> [!IMPORTANT]
> Yetenek bildirimini indirmek için özel komutlar uygulamanızı **yayımlamanız** gerekir. </br>
> Ayrıca, uygulamada **herhangi bir değişiklik** yaptıysanız, en son değişikliklerin bildirim dosyasına yansıtılması için uygulamayı yeniden yayımlamanız gerekir.

> [!NOTE]
> Uygulamayı yayımlama ile ilgili herhangi bir sorun yaşıyorsanız ve hata, bu becerilerin tetiklenmesine yönelik olarak yeniden yönlendirirse, **beceriler Için etkin komutlar** için yapılandırmayı yeniden kontrol edin. Sunulma komutlarının her birinde en az bir adet geçerli tetikleme olması gerekir.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İstemciden bir komut güncelleştirme](./how-to-custom-commands-update-command-from-client.md)
