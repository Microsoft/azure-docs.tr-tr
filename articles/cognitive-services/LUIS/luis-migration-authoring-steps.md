---
title: Azure yazma kaynağına geçiş
titleSuffix: Azure Cognitive Services
description: Bir Azure yazma kaynağına geçiş yapın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bebfad57da41d257749acd3842a75ace114ed643
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492813"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Azure yazma kaynağına geçiş adımları

Language Understanding (LUSıS) portalından, Azure yazma kaynağını kullanmak için sahip olduğunuz tüm uygulamaları geçirin.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Önkoşullar

* **Isteğe bağlı**olarak, her uygulamayı dışarı aktararak ve dışarı aktarma [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)'sini kullanarak, bu uygulamaları lusıs portalının uygulamalar listesinden yedekleyin.
* **Isteğe bağlı**olarak, her bir uygulamanın ortak kayıt listesini kaydedin. Tüm ortak çalışanlarla geçiş sürecinin bir parçası olarak bir e-posta gönderilebilir.
* **Gerekli**, bir [Azure aboneliğinizin](https://azure.microsoft.com/free/)olması gerekir. Abonelik işleminin bir parçası faturalandırma bilgileri gerektirir. Ancak, LUSıS kullandığınızda ücretsiz (F0) fiyatlandırma katmanlarını kullanabilirsiniz. Kullanımınız arttıkça ücretli bir katmana ihtiyacınız olduğunu fark edebilirsiniz. 

Azure aboneliğiniz yoksa [kaydolun](https://azure.microsoft.com/free/). 

## <a name="access-the-migration-process"></a>Geçiş işlemine erişin

Haftalık olarak, uygulamalarınızı geçirmeniz istenir. Bu pencereyi geçirmeden iptal edebilirsiniz. Bir sonraki zamanlanan dönemden önce geçiş yapmak istiyorsanız, HALU portalının üst araç çubuğundaki **kilit** simgesine geçiş işlemini başlatabilirsiniz. 

## <a name="app-owner-begins-the-migration-process"></a>Uygulama sahibi geçiş işlemini başlatır

Herhangi bir LUO uygulama sahibiyseniz, geçiş işlemi kullanılabilir. 

1. [Luo portalında](https://www.luis.ai) oturum açın ve kullanım koşullarını kabul edin.
1. Geçiş açılır penceresi geçişe devam etmenize veya daha sonra geçiş yapmanıza olanak sağlar. **Şimdi geçir**' i seçin. Daha sonra geçirmeyi seçerseniz, Azure 'daki yeni yazma anahtarına geçiş yapmak için 9 ay daha vardır.

    ![Geçiş işlemindeki ilk açılan pencere, şimdi geçir ' i seçin.](./media/migrate-authoring-key/migrate-now.png)

1. İsteğe bağlı olarak, uygulamalarınızdan birinde ortak çalışanlar varsa, geçiş hakkında bilgi veren **bir e-posta göndermeniz** istenir. Bu, isteğe bağlı bir adımdır. 

    Hesabınızı Azure 'a geçirdikten sonra uygulamalarınız artık ortak olmayan uygulamalar tarafından kullanılamaz.

    Her ortak çalışan ve uygulama için varsayılan e-posta uygulaması, hafif biçimli bir e-posta ile açılır. Göndermeden önce e-postayı düzenleyebilirsiniz.

    E-posta şablonu, tam uygulama KIMLIĞI ve uygulama adını içerir. 

    ```html
    Dear Sir/Madam,
    
    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:
    
    App Id: <app-ID-omitted>
    App name: Human Resources
    
    Thank you
    ```

1. **Uygulamalarınızı geçirmek için bir yazma kaynağı oluşturarak Başlat**' ı seçerek bir lusıs yazma kaynağı oluşturmayı seçin. 

    ![Yazma kaynağı oluşturma](./media/migrate-authoring-key/choose-authoring-resource.png)

1. Sonraki pencerede, kaynak anahtarı bilgilerinizi girin. Bilgileri girdikten sonra **kaynak oluştur**' u seçin. Her bir abonelik için bölge başına 10 ücretsiz yazma kaynağına sahip olabilirsiniz.

    ![Yazma kaynağı oluşturma](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Yeni bir yazma kaynağı oluştururken**aşağıdaki bilgileri sağlayın: 

    * **Kaynak adı** -seçtiğiniz özel bir ad, yazma ve tahmin uç noktası sorgularınız için URL 'nin bir parçası olarak kullanılır.
    * **Kiracı** -Azure aboneliğinizin ilişkilendirildiği kiracı. 
    * **Abonelik adı** -kaynak için faturalandırılacak abonelik.
    * **Kaynak grubu** -seçtiğiniz veya oluşturduğunuz özel bir kaynak grubu adı. Kaynak grupları, erişim ve yönetim için Azure kaynaklarını gruplandıreklemenize olanak tanır. 
    * **Konum** -konum seçimi, **kaynak grubu** seçimini temel alır.
    * **Fiyatlandırma katmanı** -fiyatlandırma katmanı, saniye başına en fazla işlemi ve ayı belirler. 

1. Authoring Resource dosyanızı doğrulayıp **Şimdi geçirin**.

    ![Yazma kaynağı oluşturma](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Yazma kaynağı oluşturulduğunda, başarı iletisi gösterilir. Açılır pencereyi kapatmak için **Kapat** ' ı seçin.

    ![Yazma kaynağınız başarıyla oluşturuldu.](./media/migrate-authoring-key/migration-success.png)

    **Uygulamalarım** listesinde, yeni yazma kaynağına geçirilen uygulamalar gösterilir. 

    Uygulamalarınızı LUE portalında düzenlemeyle çalışmaya devam etmek için yazma kaynağının anahtarını bilmeniz gerekmez. Uygulamalarınızı programlama yoluyla düzenlemeyi planlıyorsanız, yazma anahtarı değerleri gerekir. Bu değerler, Lua portalındaki **Manage-> Azure kaynakları** sayfasında görüntülenir ve ayrıca kaynağın **anahtarlar** sayfasındaki Azure Portal de mevcuttur.  

1. Uygulamalarınıza erişmeden önce, yazarlarınızın bulunduğu uygulamaları görmek için abonelik ve LUSıS yazma kaynağını seçin.

    ![Yazanların yazarındaki uygulamaları görmek için abonelik ve LUSıS yazma kaynağı ' nı seçin.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Uygulama katılımcısı geçiş işlemini başlatır

Geçiş için uygulama sahibiyle aynı adımları izleyin. İşlem `LUIS.Authoring`türü yeni bir yazma kaynağı oluşturur. 

Başkalarının sahip olduğu geçirilmiş uygulamalara katkıda bulunan olarak eklenmek üzere hesabınızı geçirmeniz gerekir.  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Geçiş işleminden sonra, yazma kaynağına katkıda bulunanlar ekleyin

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

[Katkıda bulunanlar eklemeyi](luis-how-to-collaborate.md)öğrenin. 

## <a name="troubleshooting-errors-with-the-migration-process"></a>Geçiş işlemiyle ilgili sorun giderme hataları

Geçiş işlemi sırasında bir Red bildirim çubuğu ile LUU portalında `MissingSubscriptionRegistration` hatası alırsanız, [Azure Portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) veya [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli)Içinde bilişsel hizmet kaynağı oluşturun. [Bu hatanın nedenleri](../../azure-resource-manager/resource-manager-register-provider-errors.md#cause)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar


* Yazma ve çalışma zamanı anahtarları hakkındaki [kavramları](luis-concept-keys.md) gözden geçirin
* [Anahtar atamayı](luis-how-to-azure-subscription.md) ve [katkıda bulunanlar](luis-how-to-collaborate.md) eklemeyi inceleyin
