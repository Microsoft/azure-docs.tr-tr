---
title: Özel sesli konuşma hizmeti ile birleştirmenin geliştirilmesi
titleSuffix: Azure Cognitive Services
description: Özel ses, markanız için tanınabilir, tek bir tür ses oluşturmanıza olanak sağlayan bir dizi çevrimiçi araç. Kullanmaya başlamak her şey için çok sayıda ses dosyası ve ilişkili dökümlerdir. Özel bir konuşmaya metin deneyimi oluşturmaya başlamak için aşağıdaki bağlantıları izleyin.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577470"
---
# <a name="get-started-with-custom-voice"></a>Özel Ses’i kullanmaya başlama

[Özel ses](https://aka.ms/customvoice) , markanız için tanınabilir, tek bir tür ses oluşturmanıza olanak sağlayan bir dizi çevrimiçi araç. Kullanmaya başlamak her şey için çok sayıda ses dosyası ve ilişkili dökümlerdir. Özel bir metin okuma deneyimi oluşturmaya başlamak için aşağıdaki bağlantıları izleyin.

## <a name="whats-in-custom-voice"></a>Özel ses nedir?

Özel sesle başlamadan önce bir Azure hesabı ve bir konuşma hizmeti aboneliğine sahip olmanız gerekir. Bir hesap oluşturduktan sonra verilerinizi hazırlayabilir, modellerinizi eğitmenizi, test edebilir, ses kalitesini değerlendirebilir ve sonunda özel ses modelinizi dağıtabilirsiniz.

Aşağıdaki diyagramda [özel ses portalını](https://aka.ms/customvoice)kullanarak özel bir ses modeli oluşturma adımları vurgulanmaktadır. Daha fazla bilgi edinmek için bağlantıları kullanın.

![Özel ses mimarisi diyagramı](media/custom-voice/custom-voice-diagram.png)

1. [Abone olun ve proje oluşturun](#set-up-your-azure-account) -Azure hesabı oluşturun ve bir konuşma hizmeti aboneliği oluşturun. Bu Birleşik abonelik, konuşmadan metne, metinden konuşmaya, konuşma çevirisine ve özel ses portalına erişmenizi sağlar. Ardından, konuşma hizmeti aboneliğinizi kullanarak ilk özel ses projenizi oluşturun.

2. [Karşıya veri yükleme](how-to-custom-voice-create-voice.md#upload-your-datasets) -özel ses portalı veya özel ses API 'sini kullanarak verileri (ses ve metin) karşıya yükleyin. Portalda, Söyleniş puanlarını ve sinyal-gürültü oranlarını araştırabilir ve değerlendirebilirsiniz. Daha fazla bilgi için bkz. [özel ses için verileri hazırlama](how-to-custom-voice-prepare-data.md).

3. [Modelinize eğitme](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) : verilerinizi kullanarak özel bir metinden konuşmaya ses modeli oluşturun. Modeli farklı dillerde eğitebilirsiniz. Eğitim sonrasında modelinizi test edin ve sonuçtan memnun kaldıysanız modeli dağıtabilirsiniz.

4. [Modelinizi dağıtın](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) -metinden konuşmaya ses modeliniz için özel bir uç nokta oluşturun ve bunu, ürün, araç ve uygulamalarınızda konuşma sensimi için kullanın.

## <a name="custom-neural-voices"></a>Özel sinir sesleri

Özel ses Şu anda hem standart hem de sinir katmanlarını desteklemektedir. Özel sinir ses, kullanıcıların daha az veri gerektirmesi sırasında daha yüksek kaliteli ses modelleri oluşturmasını ve yapay zeka dağıtmanıza yardımcı olan ölçüler sağladığını sağlar. Daha doğal konuşma arabirimleri için daha gerçekçi sesler geliştirmek ve müşterilerinizin ve son kullanıcılarınızın, en son metin okuma teknolojisinden sorumlu bir şekilde faydalanmasını sağlamak için özel sinir Voice kullanmanız önerilir. [Özel sinir sesi hakkında daha fazla bilgi edinin](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> Microsoft 'un sorumlu AI tasarlama taahhüdünün bir parçası olarak özel sinir Voice kullanımını sınırlandırdık. Teknolojiye yalnızca uygulamalarınız incelendikten ve sorumlu AI ilkelerimize göre hizalama olarak kullanmayı taahhüt ettikten sonra erişebilirsiniz. [Erişim sınırı](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) hakkında daha fazla bilgi edinin ve [burada uygulayın](https://aka.ms/customneural). Özel sesin standart ve sinir sürümü için desteklenen [Diller](language-support.md#customization) ve [bölgeler](regions.md#custom-voices) farklıdır. Başlamadan önce ayrıntıları denetleyin.  

## <a name="set-up-your-azure-account"></a>Azure hesabınızı ayarlama

Özel bir model oluşturmak için Özel Konuşma Tanıma portalını kullanabilmeniz için bir konuşma hizmeti aboneliği gerekir. Azure 'da bir konuşma hizmeti aboneliği oluşturmak için bu yönergeleri izleyin. Bir Azure hesabınız yoksa, yeni bir hesap için kaydolabilirsiniz.  

Bir Azure hesabı ve bir konuşma hizmeti aboneliği oluşturduktan sonra, özel sesli portalda oturum açmanız ve aboneliğinizi bağlamanız gerekir.

1. Azure portal konuşma hizmeti abonelik anahtarınızı alın.
2. [Özel ses portalında](https://aka.ms/custom-voice)oturum açın.
3. Aboneliğinizi seçin ve bir konuşma projesi oluşturun.
4. Başka bir konuşma aboneliğine geçiş yapmak isterseniz, üstteki gezinmede bulunan dişli simgesini kullanın.

> [!NOTE]
> Hizmeti kullanabilmeniz için önce Azure 'da oluşturulmuş bir F0 veya S0 konuşma hizmeti anahtarınız olmalıdır. Özel sinir Voice yalnızca S0 katmanını destekler. 

## <a name="how-to-create-a-project"></a>Proje oluşturma

Veriler, modeller, testler ve uç noktalar gibi içerikler, özel ses portalındaki **Projeler** halinde düzenlenmiştir. Her proje, oluşturmak istediğiniz sesin ülke/dil ve cinsiyetine özgüdür. Örneğin, Birleşik Devletler (' en-US ') içinde Ingilizce kullanan çağrı merkezinizin sohbet botları için kadın sesi için bir proje oluşturabilirsiniz.

İlk projenizi oluşturmak için, **metinden konuşmaya/özel ses** sekmesini seçin ve ardından **Yeni proje**' ye tıklayın. Projenizi oluşturmak için sihirbaz tarafından sunulan yönergeleri izleyin. Projeyi oluşturduktan sonra dört sekme görürsünüz: **veri**, **eğitim**, **Test** ve **dağıtım**. Her bir sekmeyi nasıl kullanacağınızı öğrenmek için [sonraki adımlarda](#next-steps) sunulan bağlantıları kullanın.

> [!IMPORTANT]
> [Özel ses portalı](https://aka.ms/custom-voice) yakın zamanda güncelleştirildi! CRIS.ai portalında veya API 'lerle önceki veriler, modeller, testler ve yayımlanmış uç noktalar oluşturduysanız, bu eski varlıklara bağlanmak için yeni portalda yeni bir proje oluşturmanız gerekir.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Özel sinir sesinize geçiş yapma

Özel sesin standart/sinir olmayan eğitim katmanı (Uyarlamalı, istatistiksel parametrik, içsanya) kullanım dışı bırakılıyor. Annoulama, 2/28/2021 öncesi mevcut tüm konuşma aboneliklerine gönderilmiştir. Kullanımdan kaldırma süresi (3/1/2021-2/29/2024) sırasında, mevcut standart katman kullanıcıları sinir olmayan modelleri kullanmaya devam edebilir. Tüm yeni kullanıcılar/yeni konuşma kaynakları sinir Tier/Custom sinir sesinize taşınmalıdır. 2/29/2024 sonra tüm standart/sinir dışı özel sesler artık desteklenmeyecektir. 

Sinir/standart olmayan özel ses kullanıyorsanız aşağıdaki adımları izleyerek hemen özel sinir sesinize geçirin. Özel sinir sesine taşınmanız, daha fazla doğal konuşma arabirimleri için daha gerçekçi sesler geliştirmenize ve müşterilerinizin ve son kullanıcılarınızın en son metin okuma teknolojisinden sorumlu bir şekilde yararlanmasını sağlamanıza yardımcı olur. 

1. [Erişim sınırı](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) hakkında daha fazla bilgi edinin ve [burada uygulayın](https://aka.ms/customneural). Özel sinir Voice hizmetine erişimin, Microsoft 'un, uygunluk ölçütlerimize göre tek bir kararına tabi olduğunu unutmayın. Müşteriler, yalnızca uygulaması incelendikten sonra teknolojiye erişim elde edebilir ve [sorumlu AI](https://microsoft.com/ai/responsible-ai) ilkelerimizi ve [kullanım kuralları](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)ile ilgili olarak bu BT 'yi kullanarak bu teknolojiyi kullanmaya kararlıdır. 
2. Uygulamanız onaylandıktan sonra, "sinir" eğitim özelliğine erişim izni verilir. Uygulamanızda sağladığınız aynı Azure aboneliğini kullanarak [özel bir ses portalında](https://speech.microsoft.com/customvoice) oturum olduğunuzdan emin olun. 
    > [!IMPORTANT]
    > Sesli tatatyi korumak ve ses modellerini izinsiz kayıtla veya sesli taçmadan onay olmadan eğitmek için müşterinin izin verilen ses taçının kayıtlı bir bildirimini yüklemesi gerekir. Kayıt betiğinizi hazırlarken, bu tümceyi eklediğinizden emin olun. "I [soyadınız ve soyadınız], sesimin yapay bir sürümünü oluşturmak ve kullanmak için sesimin kayıtlarının [Şirket adı olarak durum] tarafından kullanılacağını biliyor."
    > Bu cümle, bir Vere onay dosyası olarak **Ses taçanız** sekmesine yüklenmelidir. Eğitim veri kümelerinizde bulunan kayıtların onay yapan kişi tarafından gerçekleştirilip yapılkullanılmadığını doğrulamak için kullanılır.
3. Özel sinir ses modeli oluşturulduktan sonra, Ses modelini yeni bir uç noktaya dağıtın. Sinir ses modelinize yeni bir özel ses uç noktası oluşturmak için, **metinden konuşmaya > özel sesli > dağıtımına** gidin. **Modeli dağıt** ' ı seçin ve özel uç noktanız Için bir **ad** ve **Açıklama** girin. Ardından, bu uç nokta ile ilişkilendirmek istediğiniz özel sinir Voice modelini seçin ve dağıtımı onaylayın.  
4. Yeni bir modelle yeni bir uç nokta oluşturduysanız, uygulamalarınızda kodunuzu güncelleştirin. 

## <a name="next-steps"></a>Sonraki adımlar

- [Özel ses verileri hazırlama](how-to-custom-voice-prepare-data.md)
- [Özel bir ses oluşturun](how-to-custom-voice-create-voice.md)
- [Kılavuz: ses örneklerinizi kaydetme](record-custom-voice-samples.md)
