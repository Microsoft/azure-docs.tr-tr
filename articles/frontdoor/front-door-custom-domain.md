---
title: Öğretici-Azure ön Kapıyapılandırmanıza özel etki alanı ekleme
description: Bu öğreticide Azure Front Door'a özel etki alanı eklemeyi öğreneceksiniz.
services: frontdoor
documentationcenter: ''
author: jessie-jyy
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: yuajia
ms.openlocfilehash: 7e2f05a7d911ce2b311a423994d2b459de0fa269
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308872"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Öğretici: Front Door örneğinize özel etki alanı ekleme

Bu öğreticide Front Door'a özel etki alanı ekleme adımları gösterilmektedir. Uygulama teslimi için Azure ön kapısı kullandığınızda, kendi etki alanı adınızın Son Kullanıcı isteğiniz içinde görünmesini istiyorsanız özel bir etki alanı gereklidir. Görünür bir etki alanınızın olması, müşterileriniz için kolaylık sağlar ve markalama için faydalıdır.

Ön kapı oluşturduktan sonra, varsayılan ön uç Konağı `azurefd.net` Varsayılan olarak arka ucunuzdaki ön kapı içeriğini teslim etmek için URL 'ye eklenir (örneğin, https: \/ /contoso-frontend.azurefd.net/activeusers.htm). Size kolaylık olması için Azure Front Door, varsayılan ana bilgisayar adı özel etki alanı ile ilişkilendirme seçeneği sunar. Bu seçeneği kullanarak URL’nizde Front Door'a ait olan etki alanı adı yerine özel etki alanı ile içerik sunabilirsiniz (örneğin, https:\//www.contoso.com/photo.png). 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - CNAME DNS kaydı oluşturun.
> - Özel etki alanını Front Door'unuzla ilişkilendirin.
> - Özel etki alanını doğrulayın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ön kapı, [punyıcode](https://en.wikipedia.org/wiki/Punycode) karakterleriyle özel etki **alanlarını desteklemez.** 

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir Front Door oluşturmanız gerekir. Daha fazla bilgi için bkz. [Hızlı başlangıç: Front Door oluşturma](quickstart-create-front-door.md).

* Zaten özel bir etki alanınız yoksa, önce bir etki alanı sağlayıcısıyla bir tane satın almanız gerekir. Örneğin bkz. [Özel etki alanı adı satın alma](../app-service/manage-custom-dns-buy-domain.md).

* [DNS etki alanlarınızı](../dns/dns-overview.md)barındırmak için Azure kullanıyorsanız, etki alanı sağlayıcının etki alanı adı sistemi 'NI (DNS) bir Azure DNS temsilci olarak oluşturmanız gerekir. Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](../dns/dns-delegate-domain-azure-dns.md). Aksi takdirde, DNS etki alanınızı işlemek için bir etki alanı sağlayıcısı kullanıyorsanız, [CNAME DNS kaydı oluşturmaya](#create-a-cname-dns-record)devam edin.


## <a name="create-a-cname-dns-record"></a>CNAME DNS kaydı oluşturma

Ön kapasitenizin bulunduğu özel bir etki alanını kullanabilmeniz için öncelikle etki alanı sağlayıcınızla birlikte, ön kapısının varsayılan ön uç konağını (contoso.azurefd.net) işaret etmek üzere bir kurallı ad (CNAME) kaydı oluşturmanız gerekir. CNAME kaydı, bir kaynak etki alanı adını hedef etki alanı adına eşleyen bir DNS kaydı türüdür. Azure ön kapısına, kaynak etki alanı adı özel etki alanı adınız ve hedef etki alanı adı ön kapılarınızın varsayılan ana bilgisayar adıdır. Ön kapıda oluşturduğunuz CNAME kaydını doğruladıktan sonra, kaynak özel etki alanı (www contoso.com gibi) ile gelen trafik \. belirtilen hedef ön kapı varsayılan ön uç konağına yönlendirilir (örneğin, contoso-frontend.azurefd.net). 

Özel bir etki alanı ve alt etki alanı, aynı anda yalnızca tek bir ön kapıla ilişkilendirilebilir. Ancak, birden çok CNAME kaydı kullanarak farklı ön kapılara yönelik aynı özel etki alanından farklı alt etki alanlarını kullanabilirsiniz. Ayrıca, aynı ön kapıya farklı alt etki alanlarına sahip özel bir etki alanını eşleyebilirsiniz.


## <a name="map-the-temporary-afdverify-subdomain"></a>Geçici afdverify alt etki alanını eşleme

Üretim aşamasındaki var olan bir etki alanını eşlerken göz önünde bulundurmanız gereken özel durumlar vardır. Özel etki alanınızı Azure portal kaydederken etki alanı için kısa bir kapalı kalma süresi oluşabilir. Web trafiğinin kesintiye uğramasını önlemek için ilk olarak, geçici bir CNAME eşlemesi oluşturmak üzere Azure afdverify alt etki alanı ile özel etki alanınızı ön kapı varsayılan ön uç konağından eşleyin. Bu yöntemle kullanıcılar, DNS eşlemesi gerçekleşirken kesinti olmadan etki alanınıza erişebilir.

Aksi takdirde, özel etki alanınızı ilk kez kullanıyorsanız ve üzerinde üretim trafiği çalışmıyorsa, özel etki alanınızı ön kapıya doğrudan eşleyebilirsiniz. [kalıcı özel etki alanını eşlemeye](#map-the-permanent-custom-domain)devam edin.

afdverify alt etki alanı ile bir CNAME kaydı oluşturmak için:

1. Özel etki alanınızın etki alanı sağlayıcısına ait web sitesinde oturum açın.

2. Sağlayıcının belgelerine başvurarak veya web sitesinin **Etki Alanı Adı**, **DNS** ya da **Ad sunucusu yönetimi** etiketli alanlarını arayarak DNS kayıtlarını yönetmeye ilişkin sayfayı bulun. 

3. Özel etki alanınız için bir CNAME kaydı girişi oluşturun ve alanları (alan adları değişebilir) aşağıdaki tabloda gösterildiği gibi tamamlayın:

    | Kaynak                    | Tür  | Hedef                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Kaynak: afdverify alt etki alanı da dahil olmak üzere özel etki alanı adınızı aşağıdaki biçimde girin: afdverify. _&lt; özel etki alanı &gt; adı_. Örneğin, afdverify.www.contoso.com. . Contoso.com gibi bir joker etki alanını eşleştirıyorsanız \* , kaynak değer joker karakter: afdverify.contoso.com olmadan olacak şekilde aynıdır.

    - Tür: *CNAME* yazın.

    - Hedef: afdverify alt etki alanı da dahil olmak üzere varsayılan ön kapılı ön uç konağını aşağıdaki biçimde girin: afdverify. _&lt; uç nokta &gt; adı_. azurefd.net. Örneğin, afdverify.contoso-frontend.azurefd.net.

4. Yaptığınız değişiklikleri kaydedin.

Örneğin, GoDaddy etki alanı kayıt şirketi için yordam şu şekildedir:

1. Oturum açın ve kullanmak istediğiniz özel etki alanını seçin.

2. Etki Alanları bölümünde **Tümünü Yönet** seçeneğini belirleyip **DNS** | **Bölgeleri Yönet**’i seçin.

3. **Etki Alanı Adı** alanına özel etki alanınızı girin, ardından **Ara**’yı seçin.

4. **DNS Yönetimi** sayfasından **Ekle**’yi ve sonra **Tür** listesinden **CNAME** öğesini seçin.

5. CNAME girişi için aşağıdaki alanları doldurun:

    - Tür: *CNAME* seçeneğini işaretli bırakın.

    - Ana bilgisayar adı: afdverify alt etki alanı adı ile birlikte kullanmak istediğiniz özel etki alanınızın alt etki alanını girin. Örneğin, afdverify.www.

    - İşaret ettiği yer: Varsayılan Front Door ön uç ana bilgisayarınızın adını girin ve afdverify alt etki alanı adını da dahil edin. Örneğin, afdverify.contoso-frontend.azurefd.net. 

    - TTL: *bir saat* seçili olarak bırakın.

6. **Kaydet**’i seçin.
 
    CNAME girişi DNS kayıtları tablosuna eklenir.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Özel etki alanını Front Door'unuzla ilişkilendirme

Özel etki alanınızı kaydettikten sonra Front Door'unuza ekleyebilirsiniz.

1. [Azure portalda](https://portal.azure.com/) oturum açın ve bir özel etki alanına eşlemek istediğiniz ön uç ana bilgisayar adını içeren Front Door'a göz atın.
    
2. **Ön kapı Tasarımcısı** sayfasında, özel bir etki alanı eklemek için ' + ' seçeneğini belirleyin.
    
3. **Özel etki alanı**'nı seçin. 

4. **Ön uç ana bilgisayar adı** alanında CNAME kaydınızın hedef etki alanı olarak kullanılacak ön uç ana bilgisayar adı önceden doldurulmuş ve Front Door'unuzdan alınmıştır: *&lt;varsayılan ana bilgisayar adı&gt;*.azurefd.net. Bu değer değiştirilemez.

5. **Özel ana bilgisayar adı** için, CNAME kaydınızın kaynak etki alanı olarak kullanılacak alt etki alanı dahil özel etki alanınızı girin. Örneğin, www \. contoso.com veya CDN.contoso.com. Afdverify alt etki alanı adını kullanmayın.

6. **Add (Ekle)** seçeneğini belirleyin.

   Azure, girdiğiniz özel etki alanı adı için CNAME kaydının bulunduğunu doğrular. CNAME doğruysa, özel etki alanınız doğrulanır.

>[!WARNING]
> Front Door'unuzdaki her ön uç ana bilgisayar adı (özel etki alanları dahil olmak üzere) ile ilişkilendirilmiş varsayılan yola ('/\*') sahip yönlendirme kuralı olduğundan **emin olmanız** gerekir. Başka bir deyişle tüm yönlendirme kurallarınızın arasında ön uç ana bilgisayar adlarınızın her biri için varsayılan yolda ('/\*') tanımlanmış en az bir yönlendirme kuralı olmalıdır. Aksi takdirde son kullanıcı trafiği doğru şekilde yönlendirilmeyebilir.

## <a name="verify-the-custom-domain"></a>Özel etki alanını doğrulama

Özel etki alanınızı kaydınızı tamamladıktan sonra, özel etki alanının varsayılan ön kapılı ön uç ana bilgisayarınıza başvurduğundan emin olun.
 
Tarayıcınızda, özel etki alanını kullanarak dosyanın adresine gidin. Örneğin, özel etki alanınız robotics.contoso.com ise önbelleğe alınan dosyanın URL’si şu URL’ye benzer olmalıdır: http:\//robotics.contoso.com/my-public-container/my-file.jpg. En önde gelen kapıya doğrudan *&lt; ön kapı ana bilgisayarında &gt;*, sonucun aynı olduğunu doğrulayın. azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Kalıcı özel etki alanını eşleme

Afdverify alt etki alanının ön kapıya başarıyla eşlendiğini doğruladıysanız (veya üretimde olmayan yeni bir özel etki alanı kullanıyorsanız), özel etki alanını doğrudan varsayılan ön kapı ön uç konağınız ile eşleyebilirsiniz.

Özel etki alanınıza yönelik bir CNAME kaydı oluşturmak için:

1. Özel etki alanınızın etki alanı sağlayıcısına ait web sitesinde oturum açın.

2. Sağlayıcının belgelerine danışarak veya **etki alanı adı**, **DNS** ya da **ad sunucusu yönetimi** etiketli Web sitesinin bölümlerini arayarak DNS kayıtlarını yönetmeye ilişkin sayfayı bulun. 

3. Özel etki alanınız için bir CNAME kaydı girişi oluşturun ve alanları (alan adları değişebilir) aşağıdaki tabloda gösterildiği gibi tamamlayın:

    | Kaynak          | Tür  | Hedef           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Kaynak: özel etki alanı adınızı (örneğin, www \. contoso.com) girin.

   - Tür: *CNAME* yazın.

   - Hedef: Varsayılan Front Door ön uç ana bilgisayar adını girin. Şu biçimde olmalıdır:_&lt; hostname &gt;_. azurefd.net. Örneğin, contoso-frontend.azurefd.net.

4. Yaptığınız değişiklikleri kaydedin.

5. Daha önce geçici bir afdverify alt etki alanı CNAME kaydı oluşturduysanız silin. 

6. Bu özel etki alanını üretimde ilk kez kullanıyorsanız, [özel etki alanını ön Kapıınızla ilişkilendirme](#associate-the-custom-domain-with-your-front-door) ve [özel etki alanını doğrulama](#verify-the-custom-domain)adımlarını izleyin.

Örneğin, GoDaddy etki alanı kayıt şirketi için yordam şu şekildedir:

1. Oturum açın ve kullanmak istediğiniz özel etki alanını seçin.

2. Etki Alanları bölümünde **Tümünü Yönet** seçeneğini belirleyip **DNS** | **Bölgeleri Yönet**’i seçin.

3. **Etki Alanı Adı** alanına özel etki alanınızı girin, ardından **Ara**’yı seçin.

4. **DNS Yönetimi** sayfasından **Ekle**’yi ve sonra **Tür** listesinden **CNAME** öğesini seçin.

5. CNAME girişinin alanlarını doldurun:

    - Tür: *CNAME* seçeneğini işaretli bırakın.

    - Ana bilgisayar: Kullanılacak özel etki alanınızın alt etki alanını girin. Örneğin, www or profile.

    - Şuraya işaret eder: Front Door'unuzun varsayılan ana bilgisayar adını girin. Örneğin, contoso.azurefd.net. 

    - TTL: *bir saat* seçili olarak bırakın.

6. **Kaydet**’i seçin.
 
    CNAME girişi DNS kayıtları tablosuna eklenir.

7. Bir afdverify CNAME kaydınız varsa yanındaki kalem simgesini ve sonra çöp kutusu simgesini seçin.

8. CNAME kaydını silmek için **Sil**’i seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda bir özel etki alanını bir Front Door'a eklediniz. Ön kapıyı artık özel bir etki alanıyla ilişkilendirmek istemiyorsanız, aşağıdaki adımları uygulayarak özel etki alanını kaldırabilirsiniz:
 
1. DNS sağlayıcınıza gidin, özel etki alanı için CNAME kaydını silin veya özel etki alanı için CNAME kaydını ön kapısız bir uç noktaya güncelleştirin.

    > [!Important]
    > Azure ön kapısının, DNS girdilerinin ve oluşturdukları güvenlik 2021 risklerini engellemek için, kaynakların silinebilmesi için önce CNAME kayıtlarının ön kapıda kaldırılması gerekir. Kaynaklar, ön kapı özel etki alanları, ön kapı uç noktaları veya ön kapı özel etki alanlarına sahip Azure kaynak gruplarını içerir.

2. Front Door tasarımcısında kaldırmak istediğiniz özel etki alanını seçin.

3. Özel etki alanı için bağlam menüsünden **Sil** ' i seçin. Özel etki alanı artık uç noktanızla ilişkisini keser.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* CNAME DNS kaydı oluşturun.
* Özel etki alanını Front Door'unuzla ilişkilendirin.
* Özel etki alanını doğrulayın.

Özel etki alanınız için HTTPS 'yi etkinleştirme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Özel bir etki alanı için HTTPS'yi etkinleştirme](front-door-custom-domain-https.md)
