---
title: Cloud Services bir özel etki alanı adı yapılandırma | Microsoft Docs
description: DNS ayarlarını yapılandırarak, Azure uygulamanızı veya verilerinizi özel bir etki alanında internet 'te kullanıma sunma hakkında bilgi edinin.  Bu örnekler Azure portal kullanır.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: 8940d1a319d5bfabf8fd32b98f47cc6d283a8517
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359388"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Azure bulut hizmeti için özel etki alanı adı yapılandırma
Bir bulut hizmeti oluşturduğunuzda, Azure onu **cloudapp.net**'in bir alt etki alanına atar. Örneğin, bulut hizmetiniz "contoso" olarak adlandırılmışsa, kullanıcılarınız uygulamanıza benzer `http://contoso.cloudapp.net`bir URL üzerinden erişebilecektir. Azure, bir sanal IP adresi de atar.

Bununla birlikte, uygulamanızı **contoso.com**gibi kendi etki alanı adınızla da kullanıma sunabilirsiniz. Bu makalede, bulut hizmeti Web rolleri için özel bir etki alanı adı ayırma veya yapılandırma açıklanmaktadır.

Hangi CNAME ve bir kaydın olduğunu zaten öğrensin mi? [Açıklamayı geçti](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Bu görevdeki yordamlar Azure Cloud Services için geçerlidir. Uygulama Hizmetleri için bkz. [mevcut bir özel DNS adını Azure Web Apps eşleme](../app-service/app-service-web-tutorial-custom-domain.md). Depolama hesapları için bkz. [Azure Blob depolama uç noktanız için özel bir etki alanı adı yapılandırma](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Daha hızlı bir şekilde çalışmaya başlayın. yenı Azure [destekli izlenecek yolu](https://support.microsoft.com/kb/2990804)kullanın!  Özel bir etki alanı adının ilişkilendirilmesini ve Azure Cloud Services veya Azure Web siteleri ile iletişimin (SSL) güvenliğini sağlamaya olanak sağlar.
> 
> 

## <a name="understand-cname-and-a-records"></a>CNAME ve A kayıtlarını anlayın
CNAME (veya diğer ad kayıtları) ve bir kayıt, her ikisi de bir etki alanı adını belirli bir sunucuyla (veya bu durumda hizmette) ilişkilendirmenize olanak tanır, ancak farklı şekilde çalışır. Azure Cloud Services ile birlikte kullanılacak bir kayıt kullanırken göz önünde bulundurmanız gereken bazı belirli hususlar de vardır.

### <a name="cname-or-alias-record"></a>CNAME veya diğer ad kaydı
CNAME kaydı, **contoso.com** veya **\.www contoso.com**gibi *belirli* bir etki alanını kurallı bir etki alanı adına eşleştirir. Bu durumda, kurallı etki alanı adı, Azure barındırılan uygulamanızın **[MyApp]. cloudapp. net** etki alanı adıdır. Bir kez oluşturulduktan sonra, CNAME **[MyApp]. cloudapp. net**için bir diğer ad oluşturur. CNAME girişi, **[MyApp]. cloudapp. net** HIZMETI 'nin IP adresine otomatik olarak çözümlenir, bu nedenle bulut hizmeti IP adresi değişirse herhangi bir işlem yapmanız gerekmez.

> [!NOTE]
> Bazı etki alanı kayıt şirketlerinde, yalnızca www\.contoso.com gibi bir CNAME kaydı kullanırken (örneğin, contoso.com gibi) alt etki alanlarını eşlemenizi sağlar. CNAME kayıtları hakkında daha fazla bilgi için, kaydediciniz tarafından verilen belgelere, [CNAME kaydı üzerindeki Vikipedi entry](https://en.wikipedia.org/wiki/CNAME_record)veya [IETF etki alanı adları-uygulama ve belirtim](https://tools.ietf.org/html/rfc1035) belgesine bakın.

### <a name="a-record"></a>Bir kayıt
*A* kaydı, **contoso.com** veya **www\.contoso.com**gibi bir etki alanını veya  **\*. contoso.com**gibi bir *joker etki alanını* bir IP adresine eşler. Azure bulut hizmeti söz konusu olduğunda hizmetin sanal IP 'si. Bu nedenle bir CNAME kaydı üzerinden bir kaydın ana avantajı, \* **mail.contoso.com**  **gibi birden çok alt etki alanı için istekleri işleyebilen. contoso.com gibi bir joker karakter kullanan bir girişiniz olabilir. Login.contoso.com**veya **www\.contso.com**.

> [!NOTE]
> Bir kayıt bir statik IP adresiyle eşlendiğinden, bulut hizmetinizin IP adresine yapılan değişiklikler otomatik olarak çözümlenemiyor. Bulut hizmetiniz tarafından kullanılan IP adresi, boş bir yuvaya (üretim ya da hazırlama) ilk kez dağıttığınızda ayrılır. Yuva için dağıtımı silerseniz, IP adresi Azure tarafından serbest bırakılır ve yuvaya yönelik gelecekteki dağıtımlar yeni bir IP adresi olarak verilebilir.
> 
> Kolayca, belirli bir dağıtım yuvasının (üretim veya hazırlama) IP adresi, hazırlama ve üretim dağıtımları arasında takas edildiğinde veya var olan bir dağıtımın yerinde yükseltmesini gerçekleştirirken kalıcı hale getirilir. Bu eylemleri gerçekleştirme hakkında daha fazla bilgi için bkz. [Cloud Services 'i yönetme](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Özel etki alanınız için bir CNAME kaydı ekleyin
Bir CNAME kaydı oluşturmak için, kaydediciniz tarafından sunulan araçları kullanarak özel etki alanınız için DNS tablosuna yeni bir giriş eklemeniz gerekir. Her kaydedici, bir CNAME kaydı belirtmenin benzer ancak biraz farklı bir yöntemine sahiptir ancak kavramlardır.

1. Bulut hizmetinize atanan **. cloudapp.net** etki alanı adını bulmak için aşağıdaki yöntemlerden birini kullanın.

   * [Azure Portal]oturum açın, bulut hizmetinizi seçin, **genel bakış** bölümüne BAKıN ve ardından **site URL** girişini bulun.

       ![Site URL 'sini gösteren hızlı bakış bölümü][csurl]

       **VEYA**
   * [Azure PowerShell](/powershell/azure/overview)'i yükleyip yapılandırın ve ardından aşağıdaki komutu kullanın:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Bir CNAME kaydı oluştururken ihtiyacınız olacağı için, her iki yöntem tarafından döndürülen URL 'de kullanılan etki alanı adını kaydedin.
2. DNS Kaydedicinizin Web sitesinde oturum açın ve DNS yönetme sayfasına gidin. **Etki alanı adı**, **DNS**ya da **ad sunucusu yönetimi**olarak etiketlenmiş sitenin bağlantılarını veya bölgelerini arayın.
3. Şimdi CNAME 'i seçebileceğiniz veya girebileceğiniz yerleri bulun. Açılan listeden kayıt türünü seçmeniz veya bir Gelişmiş ayarlar sayfasına gitmeniz gerekebilir. **CNAME**, **diğer ad**veya alt **etki alanları**sözcüklerini aramanız gerekir.
4. **Www\.customdomain.com**için bir diğer ad oluşturmak istiyorsanız, bkz. CNAME için etki alanı  veya alt etki alanı diğer adı sağlamanız gerekir. Kök etki alanı için bir diğer ad oluşturmak istiyorsanız, bu, kayıt Kaydedicinizin DNS araçlarındaki ' **\@** ' sembolü olarak listelenebilir.
5. Daha sonra, bu durumda uygulamanızın **cloudapp.net** etki alanı olan kurallı bir ana bilgisayar adı sağlamanız gerekir.

Örneğin, aşağıdaki CNAME kaydı, dağıtılan uygulamanızın özel etki alanı adı olan **\.www contoso.com** 'dan **contoso.cloudapp.net**'e giden tüm trafiği iletir:

| Diğer ad/ana bilgisayar adı/alt etki alanı | Kurallı etki alanı |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Bir **www\.contoso.com** ziyaretçisi, doğru ana bilgisayarı (contoso.cloudapp.net) hiçbir şekilde görmez, bu nedenle iletme işlemi son kullanıcı tarafından görünmez.
> 
> Yukarıdaki örnek yalnızca **www** alt etki alanında yer alan trafiğe uygulanır. CNAME kayıtlarıyla joker karakter kullanbileceğinizden, her etki alanı/alt etki alanı için bir CNAME oluşturmanız gerekir. Örneğin, *. contoso.com gibi alt etki alanlarından gelen trafiği cloudapp.net adresine yönlendirmek istiyorsanız DNS ayarlarınızda bir **URL yeniden yönlendirme** veya **URL iletme** girişi yapılandırabilir veya bir kayıt oluşturabilirsiniz.

## <a name="add-an-a-record-for-your-custom-domain"></a>Özel etki alanınız için bir kayıt ekleyin
Bir kayıt oluşturmak için, önce bulut hizmetinizin sanal IP adresini bulmanız gerekir. Ardından, kaydediciniz tarafından sunulan araçları kullanarak özel etki alanınız için DNS tablosuna yeni bir giriş ekleyin. Her kaydedici benzer ancak farklı bir kayıt belirtme yöntemine sahiptir, ancak kavramlar aynıdır.

1. Bulut hizmetinizin IP adresini almak için aşağıdaki yöntemlerden birini kullanın.

   * [Azure Portal]oturum açın, bulut hizmetinizi seçin, **genel bakış** bölümüne bakın ve **genel IP adresleri** girişini bulun.

       ![VIP 'yi gösteren hızlı bakış bölümü][vip]

       **VEYA**
   * [Azure PowerShell](/powershell/azure/overview)'i yükleyip yapılandırın ve ardından aşağıdaki komutu kullanın:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Bir kayıt oluştururken ihtiyacınız olacak şekilde IP adresini kaydedin.
2. DNS Kaydedicinizin Web sitesinde oturum açın ve DNS yönetme sayfasına gidin. **Etki alanı adı**, **DNS**ya da **ad sunucusu yönetimi**olarak etiketlenmiş sitenin bağlantılarını veya bölgelerini arayın.
3. Şimdi bir kaydın nerede seçip seçebileceğiniz veya girebileceğinizi bulabilirsiniz. Açılan listeden kayıt türünü seçmeniz veya bir Gelişmiş ayarlar sayfasına gitmeniz gerekebilir.
4. Bu kaydı kullanacak etki alanı veya alt etki alanını seçin veya girin. Örneğin, www **\.customdomain.com**için bir diğer ad oluşturmak istiyorsanız **www** ' i seçin. Tüm alt etki alanları için bir joker karakter girişi oluşturmak istiyorsanız ' * * * * * ' girin. Bu, **mail.customdomain.com**, **login.customdomain.com**ve **www\.customdomain.com**gibi tüm alt etki alanlarını kapsar.

    Kök etki alanı için bir kayıt oluşturmak istiyorsanız, bu, kayıt Kaydedicinizin DNS araçlarındaki ' **\@** ' sembolü olarak listelenebilir.
5. Belirtilen alana bulut hizmetinizin IP adresini girin. Bu, A kaydında kullanılan etki alanı girdisini bulut hizmeti dağıtımınızın IP adresiyle ilişkilendirir.

Örneğin, aşağıdaki bir kayıt tüm trafiği **contoso.com** 'ten **137.135.70.239**'e ILETIR, dağıtılan uygulamanızın IP adresidir:

| Ana bilgisayar adı/alt etki alanı | IP adresi |
| --- | --- |
| \@ |137.135.70.239 |

Bu örnek, kök etki alanı için bir kayıt oluşturmayı gösterir. Tüm alt etki alanlarını kapsayacak bir joker karakter girişi oluşturmak istiyorsanız, alt etki alanı olarak ' * * * * * ' girersiniz.

> [!WARNING]
> Azure 'daki IP adresleri varsayılan olarak dinamiktir. IP adresinizin değişmediğinden emin olmak için büyük olasılıkla [ayrılmış BIR IP adresi](../virtual-network/virtual-networks-reserved-public-ip.md) kullanmak isteyeceksiniz.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
* [Cloud Services nasıl yönetilir?](cloud-services-how-to-manage-portal.md)
* [CDN İçeriğini Özel Etki Alanı ile Eşleme](../cdn/cdn-map-content-to-custom-domain.md)
* [Bulut hizmetinizin genel yapılandırması](cloud-services-how-to-configure-portal.md).
* [Bulut hizmetini dağıtmayı](cloud-services-how-to-create-deploy-portal.md)öğrenin.
* [SSL sertifikalarını](cloud-services-configure-ssl-certificate-portal.md)yapılandırma.

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
