---
title: Yönetici onayı iş akışını Yapılandırma-Azure Active Directory | Microsoft Docs
description: Son kullanıcıların yönetici onayı gerektiren uygulamalara erişim istemesi için bir yol yapılandırmayı öğrenin.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5d1654556df455accb7f615442d3a97952c5e50
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180426"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Yönetici onayı iş akışını yapılandırma (Önizleme)

Bu makalede, son kullanıcılara yönetici onayı gerektiren uygulamalara erişim istemek için bir yol sağlayan yönetici onayı iş akışı (Önizleme) özelliğinin nasıl etkinleştirileceği açıklanır.

Yönetici onayı iş akışı olmadan, Kullanıcı izninin devre dışı olduğu bir Kiracıdaki bir Kullanıcı, kuruluş verilerine erişim izni gerektiren herhangi bir uygulamaya erişmeyi denediğinde engellenir. Kullanıcı, uygulamaya erişimin yetkilendirilmemiş olduğunu bildiren genel bir hata iletisi görür ve yöneticisinden yardım isteyin. Ancak genellikle Kullanıcı, uygulamaya kimin başvuracağını bilmez, bu nedenle uygulamada yeni bir yerel hesap verir veya oluşturur. Bir yöneticiye bildirimde bulunulsa bile, yöneticinin erişim izni vermesini ve kullanıcılarını bilgilendirmesini sağlamak için her zaman kolaylaştırılmış bir işlem yoktur.
 
Yönetici onayı iş akışı yöneticilere yönetici onayı gerektiren uygulamalara erişim vermek için güvenli bir yol sağlar. Bir Kullanıcı bir uygulamaya erişmeyi denediğinde ancak onay sağlayamadığında, yönetici onayı için bir istek gönderebilirler. İstek, gözden geçirenler olarak belirlenmiş yöneticilere e-posta aracılığıyla gönderilir. Gözden geçiren istek üzerinde işlem gerçekleştirir ve kullanıcıya eylem bildirilir.

İstekleri onaylamak için, gözden geçiren bir genel yönetici, bulut uygulama Yöneticisi veya uygulama Yöneticisi olmalıdır. Gözden geçirenin bu yönetici rollerinden birine zaten atanmış olması gerekir; bunları gözden geçiren olarak atamak, ayrıcalıklarını yükselmez.

## <a name="enable-the-admin-consent-workflow"></a>Yönetici onayı iş akışını etkinleştirme

Yönetici onayı iş akışını etkinleştirmek ve gözden geçirenler ' i seçmek için:

1.  [Azure portal](https://portal.azure.com) genel yönetici olarak oturum açın.
2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' a tıklayın.  **Azure Active Directory uzantısı** açılır.
3. Filtre arama kutusuna "**Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Gezinti menüsünde **Kurumsal uygulamalar**' a tıklayın. 
5. **Yönet**altında **Kullanıcı ayarları**' nı seçin.
6. **Yönetici izin istekleri (Önizleme)** altında, kullanıcıları **Evet**olarak **kabul veremediği uygulamalara yönetici onayı isteyebilecekleri** şekilde ayarlayın.

   ![Yönetici onayı iş akışı ayarlarını yapılandırma](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Aşağıdaki ayarları yapılandırın:

   * **Yönetici onay isteklerini gözden geçirmek için kullanıcılar**' ı seçin. Genel yönetici, bulut uygulaması Yöneticisi ve uygulama yöneticisi rollerine sahip olan bir Kullanıcı kümesinden bu iş akışı için gözden geçirenler ' i seçin.
   * **Seçili kullanıcılar, istekler için e-posta bildirimleri alır**. İstek yapıldığında gözden geçirenler için e-posta bildirimlerini etkinleştirin veya devre dışı bırakın.  
   * **Seçili kullanıcılar, istek süre sonu anımsatıcıları alır**. İstek sona ermek üzereyken gözden geçirenlere anımsatıcı e-posta bildirimlerini etkinleştirin veya devre dışı bırakın.  
   * **Onay isteğinin süresi (gün)** . İsteklerin ne kadar süreyle geçerli kalacağını belirtin.

7. **Kaydet**’i seçin. Özelliğin etkin hale gelmesi bir saate kadar sürebilir.

> [!NOTE]
> **Yönetici onay istekleri gözden geçirenler listesini seçerek** bu iş akışı için gözden geçirenler ekleyebilir veya kaldırabilirsiniz. Bu özelliğin geçerli sınırlaması, gözden geçirenlerin gözden geçiren olarak belirlenirken yapılan istekleri gözden geçirebilme özelliğini gerçekleştirebileceğini unutmayın.

## <a name="how-users-request-admin-consent"></a>Kullanıcılar yönetici onayı isteme

Yönetici onayı iş akışı etkinleştirildikten sonra, kullanıcılar, izin verilmeyen bir uygulama için yönetici onayı talep edebilir. Aşağıdaki adımlarda kullanıcının onay isteğinde bulunduğu deneyim açıklanır. 

1. Kullanıcı uygulamada oturum açmaya çalışır.

2. **Onay gerekli** iletisi görüntülenir. Kullanıcı uygulamaya erişim gereksinimi için bir gerekçe belirler ve sonra **istek onayı**' nı seçer.

   ![Yönetici onayı Kullanıcı isteği ve gerekçe](media/configure-admin-consent-workflow/end-user-justification.png)

3. **Gönderilen istek** iletisi, isteğin yöneticiye gönderildiğini onaylar. Kullanıcı birkaç istek gönderirse, yalnızca ilk istek yöneticiye gönderilir.

   ![Yönetici onayı Kullanıcı isteği ve gerekçe](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Kullanıcı, istekleri onaylandığında, reddedildiğinde veya engellendiğinde bir e-posta bildirimi alır. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Yönetici onay isteklerini gözden geçirin ve eylem yapın

Yönetici izin isteklerini gözden geçirmek ve işlem yapmak için:

1. Yönetici onayı iş akışının kayıtlı gözden geçirenlerden biri olarak [Azure portal](https://portal.azure.com) oturum açın.
2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** seçin.  **Azure Active Directory uzantısı** açılır.
3. Filtre arama kutusuna "**Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Gezinti menüsünde **Kurumsal uygulamalar**' a tıklayın.
5. **Etkinlik**' ın altında **Yönetici onay istekleri (Önizleme)** öğesini seçin.

   > [!NOTE]
   > Gözden geçirenler yalnızca, gözden geçiren olarak belirlendikten sonra oluşturulan yönetici isteklerini görürler.

1. İstenen uygulamayı seçin.
2. İstek hakkındaki ayrıntıları gözden geçirin:  

   * Kimlerin erişim istediğini ve neden olduğunu görmek için, **istenen** sekmesini seçin.
   * Uygulama tarafından hangi izinlerin istenmekte olduğunu görmek için, **Gözden geçirme izinleri ve onayı**' nı seçin.

8. İsteği değerlendirin ve uygun eylemi gerçekleştirin:

   * **Isteği onaylayın**. Bir isteği onaylamak için uygulamaya yönetici onayı verin. İstek onaylandıktan sonra, tüm istek sahipleri erişim izni verildiğini bilgilendirilir.  
   * **Isteği reddedin**. Bir isteği reddetmek için tüm istek sahipleri için sağlanacak bir gerekçe sağlamanız gerekir. İstek reddedildikten sonra, tüm istek sahipleri uygulamaya erişimin reddedildikleri bildirilir. Bir isteği reddetmek, kullanıcıların gelecekte uygulamaya yönetici onayı istemesini engellemez.  
   * **Isteği engelleyin**. Bir isteği engellemek için tüm istek sahipleri için sağlanacak bir gerekçe sağlamanız gerekir. İstek engellendiğinde, tüm istek sahipleri uygulamaya erişimin reddedildikleri bildirilir. Bir isteği engellemek, kiracınızdaki uygulama için devre dışı durumda bir hizmet sorumlusu nesnesi oluşturur. Kullanıcılar gelecekte uygulamaya yönetici onayı isteyemeyecektir.
 
## <a name="email-notifications"></a>E-posta bildirimleri
 
Yapılandırıldıysa, tüm gözden geçirenler şu durumlarda e-posta bildirimi alır:

* Yeni bir istek oluşturuldu
* İsteğin süresi doldu
* İstek, sona erme tarihine yaklaşıyor  
 
İstek şu durumlarda e-posta bildirimleri alacak:

* Erişim için yeni bir istek gönderir
* İstek süresi doldu
* İstekleri reddedildi veya engellendi
* İstekleri onaylandı
 
## <a name="audit-logs"></a>Denetim günlükleri 
 
Aşağıdaki tabloda, yönetici onayı iş akışı için kullanılabilen senaryolar ve denetim değerleri özetlenmektedir. 

> [!NOTE]
> Denetim aktörün Kullanıcı bağlamı Şu anda tüm senaryolarda yok. Bu, önizleme sürümünde bilinen bir sınırlamadır.


|Senaryo  |Denetim Hizmeti  |Denetim Kategorisi  |Denetim etkinliği  |Aktör denetimi  |Denetim günlüğü sınırlamaları  |
|---------|---------|---------|---------|---------|---------|
|İzin isteği iş akışını etkinleştirme Yöneticisi        |Erişim Gözden Geçirmeleri           |Kullanıcı Yönetimi           |İdare ilkesi şablonu oluşturma          |Uygulama bağlamı            |Şu anda kullanıcı bağlamını bulamıyoruz            |
|Yönetici onay isteği iş akışını devre dışı bırakma       |Erişim Gözden Geçirmeleri           |Kullanıcı Yönetimi           |İdare ilkesi şablonunu Sil          |Uygulama bağlamı            |Şu anda kullanıcı bağlamını bulamıyoruz           |
|İzin iş akışı yapılandırmasını yönetici olarak güncelleştirme        |Erişim Gözden Geçirmeleri           |Kullanıcı Yönetimi           |İdare ilkesi şablonunu Güncelleştir          |Uygulama bağlamı            |Şu anda kullanıcı bağlamını bulamıyoruz           |
|Son Kullanıcı bir uygulama için yönetici onay isteği oluşturma       |Erişim Gözden Geçirmeleri           |İlke         |İstek oluştur           |Uygulama bağlamı            |Şu anda kullanıcı bağlamını bulamıyoruz           |
|Yönetici onay isteğini onaylayan gözden geçirenler       |Erişim Gözden Geçirmeleri           |Kullanıcı Yönetimi           |İş akışındaki tüm istekleri onaylama          |Uygulama bağlamı            |Şu anda kullanıcı bağlamını veya yönetici onayı verilen uygulama KIMLIĞINI bulamıyoruz.           |
|Yönetici onay isteğini reddeden gözden geçirenler       |Erişim Gözden Geçirmeleri           |Kullanıcı Yönetimi           |İş akışındaki tüm istekleri onaylama          |Uygulama bağlamı            | Şu anda yönetici onay isteğini reddeden aktörün kullanıcı bağlamını bulamıyoruz          |

## <a name="faq"></a>SSS 

**Bu iş akışını etkinleştirdim, ancak işlevselliği sınarken, neden yeni "onay gerekli" istemi 'ni istemediğimi bana izin veriyor?**

Özelliği açtıktan sonra, son kullanıcıların güncelleştirmeyi görme süresi 60 dakika sürebilir. `https://graph.microsoft.com/beta/settings` API 'sindeki **Enableadminconsentrequests** değerini görüntüleyerek yapılandırmanın doğru şekilde sürdüğünü doğrulayabilirsiniz.

**Gözden geçiren olarak, bekleyen tüm istekleri neden göremiyorum?**

Gözden geçirenler yalnızca, gözden geçiren olarak belirlendikten sonra oluşturulan yönetici isteklerini görebilir. Son olarak bir gözden geçiren olarak eklediyseniz, atamasından önce oluşturulmuş herhangi bir istek görmezsiniz.

**Gözden geçiren olarak, neden aynı uygulama için birden çok istek görmem gerekir?**
  
Bir uygulama geliştiricisi kendi uygulamalarını, son kullanıcıların verilerine erişim istemek için statik ve dinamik onay kullanacak şekilde yapılandırdıysa, iki yönetici onay isteği görürsünüz. Bir istek statik izinleri temsil eder ve diğeri dinamik izinleri temsil eder.

**İstek sahibi olarak, istemin durumunu denetleyebilir miyim?**  

Hayır, artık istek sahipleri için yalnızca e-posta bildirimleri aracılığıyla güncelleştirmeleri alabilir.

**Gözden geçiren olarak, uygulamayı onaylamak mümkün olsa da herkes için değil misiniz?**
 
Yönetici onayı verme konusunda endişe ediyorsanız ve Kiracıdaki tüm kullanıcıların uygulamayı kullanmasına izin verirseniz, isteği reddetmenizi öneririz. Daha sonra Kullanıcı Ataması gerektirerek uygulamaya erişimi kısıtlayarak ve uygulamaya Kullanıcı veya grup atayarak yönetici onayını el ile verin. Daha fazla bilgi için bkz. [kullanıcıları ve grupları atamaya yönelik yöntemler](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalara yönelik yarışmaya yönelik daha fazla bilgi için bkz. [Azure Active Directory izin çerçevesi](../develop/consent-framework.md).

[Uygulamaları AzureAD 'e onay ve Tümleştirme](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[AzureAD v 2.0 yakınsanmış uygulamalar için onay ve Izinler](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
