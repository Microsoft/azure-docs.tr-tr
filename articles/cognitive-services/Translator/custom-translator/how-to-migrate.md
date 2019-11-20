---
title: Microsoft Translator hub çalışma alanı ve projeleri geçişi yapılsın mı? -Özel çevirici
titleSuffix: Azure Cognitive Services
description: Bu makalede, hub çalışma alanınızı ve projelerinizi Azure bilişsel hizmetler özel çeviricisine nasıl geçirebileceğiniz açıklanır.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 7ea7b48e1fc36399a0ca173f9068faf8b88849d5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836586"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Hub çalışma alanını ve projelerini özel çevirmene geçirme

[Microsoft Translator hub](https://hub.microsofttranslator.com/) çalışma alanınızı ve projelerinizi özel çevirmene kolayca geçirebilirsiniz. Geçiş, Microsoft hub 'dan bir çalışma alanı veya proje seçilerek, sonra özel çevirmende bir çalışma alanı seçerek ve ardından aktarmak istediğiniz eğitimleri seçerek başlatılır. Geçiş başladıktan sonra, seçilen eğitim ayarları tüm ilgili belgelerle birlikte aktarılır. Dağıtılan modeller eğitilir ve tamamlama sonrasında otomatik olarak dağıtılabilir.

Bu eylemler geçiş sırasında gerçekleştirilir:
* Tüm belgeler ve proje tanımlarının adları, adının önüne "hub_" eklenmiş olarak aktarılır. Otomatik olarak oluşturulan test ve ayarlama verileri hub_systemtune_\<ModelId > veya hub_systemtest_\<ModelId > olarak adlandırılır.
* Geçiş gerçekleşirken dağıtılan durumda olan tüm insanlar otomatik olarak Merkez eğitiminin belgeleri kullanılarak eğitilecektir. Bu eğitim aboneliğinize ücretlendirilmeyecektir. Geçiş için otomatik dağıtım seçilmişse, eğitilen model tamamlama sonrasında dağıtılır. Düzenli barındırma ücretleri uygulanır.
* Dağıtılmış durumda olmayan tüm geçirilen değişiklikler geçirilmiş taslak durumuna alınacaktır. Bu durumda, geçirilen tanım ile bir modeli eğitme seçeneğine sahip olursunuz, ancak düzenli eğitim ücretleri uygulanır.
* Herhangi bir noktada, hub eğitiminden geçirilen BLEU puanı, "MT hub 'ında Bleu puanı" başlığında bulunan modelin Traıningdetails sayfasında bulunabilir.

> [!Note] 
> Bir eğitimin başarılı olması için, özel çevirmen en az 10.000 benzersiz ayıklanan tümce gerektirir. Özel çevirmen [Önerilen en düşük](sentence-alignment.md#suggested-minimum-number-of-sentences)boyuttan daha az bir eğitim yapamıyor.

## <a name="find-custom-translator-workspace-id"></a>Özel çevirmen çalışma alanı KIMLIĞI bul

[Microsoft Translator hub](https://hub.microsofttranslator.com/) çalışma alanını geçirmek Için özel çevirmende hedef çalışma alanı kimliği gereklidir. Özel Translator 'daki hedef çalışma alanı, tüm Hub çalışma alanlarınızın ve projelerinizin geçirilecek yerdir.

Hedef çalışma alanı KIMLIĞINIZI özel çevirici ayarları sayfasında bulacaksınız:

1. Özel çevirmen portalındaki "ayar" sayfasına gidin.

2. Çalışma alanı KIMLIĞINI temel bilgiler bölümünde bulacaksınız.

    ![Hedef çalışma alanı KIMLIĞINI bulma](media/how-to/how-to-find-destination-ws-id.png)

3. Geçiş işlemi sırasında başvurmak için hedef çalışma alanı KIMLIĞINIZI saklayın.

## <a name="migrate-a-project"></a>Projeyi geçirme

Projelerinizi seçmeli olarak geçirmek istiyorsanız, Microsoft Translator hub size bu özelliği sunar.

Bir projeyi geçirmek için:

1. Microsoft Translator hub 'da oturum açın.

2. "Projeler" sayfasına gidin.

3. Uygun proje için "geçir" bağlantısına tıklayın.

    ![Hub 'dan geçiş yapma](media/how-to/how-to-migrate-from-hub.png)

4. Geçiş bağlantısına bastıktan sonra, aşağıdakileri sağlayan bir form görüntülenir:
   * Özel çevirmende aktarmak istediğiniz çalışma alanını belirtin
   * Başarılı ilerler veya yalnızca dağıtılan ilerler ile tüm eğitimleri aktarmak isteyip istemediğinizi belirtin. Varsayılan olarak, tüm başarılı traınler aktarılır.
   * Eğitim tamamlandığında eğitime otomatik olarak dağıtılıp dağıtılmayacağını belirtin. Varsayılan olarak, eğitimin tamamlanma sonrasında otomatik olarak dağıtılmayacak.

5. "Istek gönder" seçeneğine tıklayın.

## <a name="migrate-a-workspace"></a>Çalışma alanını geçirme

Tek bir projeyi geçirmeye ek olarak, çalışma alanındaki başarılı değişikliklerle tüm projeleri de geçirebilirsiniz. Bu, çalışma alanındaki her projenin geçiş bağlantısına basılmasına rağmen değerlendirilmesine neden olur. Bu özellik, tümünü aynı ayarlarla özel çevirmene geçirmek isteyen birçok projesi olan kullanıcılar için uygundur. Bir çalışma alanı geçişi, çevirmen hub 'ının ayarlar sayfasından başlatılabilir.

Çalışma alanını geçirmek için:

1. Microsoft Translator hub 'da oturum açın.

2. "Ayarlar" sayfasına gidin.

3. "Ayarlar" sayfasında "çalışma alanı verilerini özel çevirmene geçir" düğmesine tıklayın.

    ![Hub 'dan geçiş yapma](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Sonraki sayfada, bu iki seçeneklerinden birini seçin:

    a. Yalnızca dağıtılan Traınler: Bu seçeneğin belirlenmesi yalnızca dağıtılan sistemlerinizi ve ilgili belgelerinizi taşıyacaktır.

    b. Tüm başarılı Insanlar: Bu seçeneğin belirlenmesi, tüm başarılı çalışmanızı ve ilgili belgelerinizi geçirebilir.

    c. Özel çevirmende hedef çalışma alanı KIMLIĞINIZI girin.

    ![Hub 'dan geçiş yapma](media/how-to/how-to-migrate-from-hub-screen.png)

5. Istek Gönder ' e tıklayın.

## <a name="migration-history"></a>Geçiş geçmişi

Hub 'dan çalışma alanı/proje geçişi isteğinde bulunuldığınızda, geçiş geçmişinizi özel çevirici ayarları sayfasında bulabilirsiniz.

Geçiş geçmişini görüntülemek için aşağıdaki adımları izleyin:

1. Özel çevirmen portalındaki "ayar" sayfasına gidin.

2. Ayarlar sayfasının geçiş geçmişi bölümünde geçiş geçmişi ' ne tıklayın.

    ![Geçiş geçmişi](media/how-to/how-to-migration-history.png)

Geçiş geçmişi sayfası, istediğiniz her geçişin özeti olarak aşağıdaki bilgileri görüntüler.

1. Geçirme ölçütü: Bu geçiş isteğini gönderen kullanıcının adı ve e-postası

2. Geçirme tarihi: geçişin tarih ve saat damgası

3. Projeler: başarılı bir şekilde geçirilmekte olan geçiş v/s proje sayısı için istenen proje sayısı.

4. Seyahat: geçiş için istenen istek sayısı/sn başarılı bir şekilde geçirildi.

5. Belgeler: geçiş v/s belgelerinin başarıyla geçirildiği belge sayısı.

    ![Geçiş geçmişi ayrıntıları](media/how-to/how-to-migration-history-details.png)

Projeleriniz, seyahat ve belgeler hakkında daha ayrıntılı geçiş raporu isterseniz, ayrıntıları dışa aktarma ayrıntılarına CSV olarak sahipsiniz seçeneğini kullanabilirsiniz.

## <a name="implementation-notes"></a>Uygulama notları
* Yalnızca özel çevirmende bulunmayan dil çiftlerine sahip sistemler, verilere erişmek veya özel çevirici aracılığıyla dağıtımı geri almak için kullanılabilir. Bu projeler, Projeler sayfasında "kullanılamaz" olarak işaretlenir. Özel çevirmenle yeni dil çiftlerini etkinleştirdiğimiz için, projeler eğecek ve dağıtılacak şekilde etkin hale gelir. 
* Projeyi hub 'dan özel çevirmene geçirmek, hub 'ınız veya projeleriniz üzerinde herhangi bir etkiye sahip olmayacaktır. Geçiş sırasında proje veya belge hub 'ından silinmez ve modellerin dağıtımı geri alınamadı.
* Her proje için yalnızca bir kez geçiş yapabilirsiniz. Bir projede geçiş tekrarlamanız gerekiyorsa lütfen bizimle iletişime geçin.
* Özel çevirici, Ingilizce veya Ingilizce için NMT dil çiftlerini destekler. [Desteklenen dillerin tüm listesini görüntüleyin](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Hub temel modeller gerektirmez ve bu nedenle birkaç bin dili destekler. Desteklenmeyen bir dil çiftini geçirebilirsiniz, ancak yalnızca belge ve proje tanımlarının geçişini gerçekleştiririz. Yeni modeli eğitmeyeceksiniz. Ayrıca, bu belgeler ve projeler Şu anda kullanılamayacağını göstermek için etkin değil olarak görüntülenecektir. Bu projeler ve/veya belgeler için destek eklenirse, etkin ve sürdürülebilir hale gelir.
* Özel çevirici Şu anda monolingual eğitim verilerini desteklememektedir. Desteklenmeyen dil çiftleri gibi, monolingual belgelerini geçirebilirsiniz, ancak monolingual verileri desteklenene kadar etkin değil olarak gösterilir.
* Özel çevirmen, eğitebilmek için 10.000 paralel cümleler gerektirir. Microsoft hub, daha küçük bir veri kümesi üzerinde eğitim verebilir. Bu gereksinimi karşılamayan bir eğitim geçirilirse, bu işlem eğitilemez.

## <a name="custom-translator-versus-hub"></a>Özel Translator ile hub karşılaştırması

Bu tablo, Microsoft Translator hub ve özel çevirmen arasındaki özellikleri karşılaştırır.

|   | Hub | Özel Çevirmen |
|:-----|:----:|:----:|
|Özelleştirme özelliği durumu   | Genel Erişilebilirlik  | Genel Erişilebilirlik |
| Metin API 'SI sürümü  | V2    | Yüklemesinde  |
| SMT özelleştirmesi | Evet   | Hayır |
| NMT özelleştirmesi | Hayır    | Evet |
| Yeni Birleşik konuşma Hizmetleri özelleştirmesi | Hayır    | Evet |
| İzleme Yok | Evet | Evet |

## <a name="new-languages"></a>Yeni diller

Microsoft Translator için yeni bir dil sistemi oluşturma konusunda çalışan bir topluluk veya kuruluşunuzda daha fazla bilgi edinmek için [custommt@microsoft.com](mailto:custommt@microsoft.com) ulaşın.

## <a name="next-steps"></a>Sonraki adımlar

- [Bir modeli eğitme](how-to-train-model.md).
- Dağıtılmış özel çeviri modelinizi [Microsoft Translator metin çevirisi API'si v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)aracılığıyla kullanmaya başlayın.
