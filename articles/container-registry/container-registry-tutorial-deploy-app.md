---
title: Öğretici-uygulamayı coğrafi olarak çoğaltılan Azure Container Registry dağıtma
description: Coğrafi olarak çoğaltılan bir Azure Container Registry 'den bir kapsayıcı görüntüsü kullanarak Linux tabanlı bir Web uygulamasını iki farklı Azure bölgesine dağıtın. Üç bölümden oluşan bir serinin ikinci bölümü.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 08/20/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 0aebc31e1b8e6849d4cc0aeccc0e0a79edf0e65d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931409"
---
# <a name="tutorial-deploy-a-web-app-from-a-geo-replicated-azure-container-registry"></a>Öğretici: coğrafi olarak çoğaltılan bir Azure Container Registry 'den Web uygulaması dağıtma

Bu, üç bölümden oluşan bir öğretici serisinin ikinci bölümüdür. [Birinci bölümde](container-registry-tutorial-prepare-registry.md), özel bir coğrafi olarak çoğaltılmış kapsayıcı kayıt defteri oluşturuldu, kaynaktan bir kapsayıcı görüntüsü derlendi ve kayıt defterine gönderildi. Bu makalede, kapsayıcıyı farklı Azure bölgelerinde bulunan iki Web App örneğine dağıtarak coğrafi olarak çoğaltılmış kayıt defterinin ağa yakın özelliğinden yararlanacaksınız. Ardından her bir örnek, kapsayıcı görüntüsünü en yakın kayıt defterinden çeker.

Bu öğreticide, serinin ikinci kısmı:

> [!div class="checklist"]
> * İki *Kapsayıcılar için Web Uygulamaları* örneğine bir kapsayıcı görüntüsü dağıtma
> * Dağıtılan uygulamayı doğrulama

Henüz coğrafi olarak çoğaltılmış bir kayıt defteri oluşturmadıysanız ve kapsayıcılı örnek uygulamanın görüntüsünü kayıt defterine gönderdiyseniz, serideki [Coğrafi olarak çoğaltılmış bir Azure kapsayıcı kayıt defteri hazırlama](container-registry-tutorial-prepare-registry.md) adlı önceki öğreticiye geri dönün.

Serinin sonraki makalesinde, uygulamayı güncelleştirecek, sonra kayıt defterine güncelleştirilmiş kapsayıcı görüntüsünü göndereceksiniz. Son olarak, çalıştırılan her Web App örneğine göz atarak, değişikliğin her ikisinde otomatik olarak yansıtılıp yansıtılmadığına bakar ve Azure Container Registry coğrafi çoğaltma ve web kancalarını çalışır halde görürsünüz.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Kapsayıcılar için Web Uygulamalarına otomatik dağıtım

Azure Container Registry, kapsayıcılı uygulamaları doğrudan [Kapsayıcılar için Web Uygulamaları](../app-service/containers/index.yml)’na dağıtma desteği sağlar. Bu öğreticide, Azure portalını kullanarak, önceki öğreticide oluşturulan kapsayıcı görüntüsünü, farklı Azure bölgelerinde bulunan iki web uygulaması planına dağıtacaksınız.

Kayıt defterinizdeki bir kapsayıcı görüntüsünden web uygulaması dağıttığınızda ve aynı bölgede coğrafi olarak çoğaltılmış bir kayıt defteriniz olduğunda Azure Container Registry sizin için bir görüntü dağıtımı [web kancası](container-registry-webhook.md) oluşturur. Kapsayıcı deponuza yeni bir görüntü gönderdiğinizde web kancası değişikliği alır ve otomatik olarak yeni kapsayıcı görüntüsünü web uygulamanıza dağıtır.

## <a name="deploy-a-web-app-for-containers-instance"></a>Kapsayıcılar için Web App örneğini dağıtma

Bu adımda, *Batı ABD* bölgesinde bir Kapsayıcılar için Web App örneği oluşturacaksınız.

[Azure portalında](https://portal.azure.com) oturum açın ve önceki öğreticide oluşturduğunuz kayıt defterine gidin.

**Depolar** > **acr-helloworld** seçeneğini belirleyin, ardından **Etiketler** bölümünde **v1** etiketine tıklayın ve **Web uygulamasına dağıt**’ı seçin:

![Azure portalında uygulama hizmetine dağıtma][deploy-app-portal-01]

"Web uygulamasına dağıt" seçeneği devre dışı bırakılırsa kayıt defteri yönetici kullanıcısını ilk öğreticinin [Kapsayıcı kayıt defteri oluşturma](container-registry-tutorial-prepare-registry.md#create-a-container-registry) bölümünde anlatılan şekilde etkinleştirememiş olabilirsiniz. Yönetici kullanıcısını Azure portalda **Ayarlar** > **Erişim anahtarları** sayfasından etkinleştirebilirsiniz.

"Web uygulamasında dağıt"ı seçtikten sonra görüntülenen **Kapsayıcılar için Web App** bölümünde, her bir ayar için aşağıdaki değerleri belirtin:

| Ayar | Değer |
|---|---|
| **Site Adı** | Web uygulaması için genel benzersiz bir ad. Bu örnekte, web uygulamasının içinden dağıtıldığı bölgeyi ve kayıt defterini kolayca belirlemek için `<acrName>-westus` biçimini kullanıyoruz. |
| **Kaynak Grubu** | **Var olanı kullan** > `myResourceGroup` |
| **Uygulama hizmeti planı/Konumu** | `plan-westus`Batı ABD**bölgesinde** adlı yeni bir plan oluşturun. |
| **Görüntü** | `acr-helloworld:v1` |
| **İşletim sistemi** | Linux |

> [!NOTE]
> Kapsayıcılı uygulamanızı dağıtmak için yeni bir App Service planı oluşturduğunuzda, uygulamanızı barındırmak için varsayılan bir plan otomatik olarak seçilir. Varsayılan plan, işletim sistemi ayarına bağlıdır.

**Oluştur**’u seçerek, web uygulamasını *Batı ABD* bölgesine sağlayın.

![Azure portalında Linux yapılandırmasındaki web uygulaması][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Dağıtılan web uygulamasını görüntüleme

Dağıtım tamamlandığında, çalıştırılan uygulamanın URL’sine tarayıcınızda giderek bu uygulamayı görüntüleyebilirsiniz.

Portalda **Uygulama Hizmetleri** seçeneğini belirleyin ve önceki adımda sağladığınız web uygulamasını seçin. Bu örnekte, web uygulaması *uniqueregistryname-westus* olarak adlandırılmıştır.

Çalıştırılan uygulamayı tarayıcınızda görüntülemek için, **App Service** genel bakışının sağ üst kısmında web uygulamasının köprülü URL’sini seçin.

![Azure portalında Linux yapılandırmasındaki web uygulaması][deploy-app-portal-04]

Coğrafi olarak çoğaltılmış kapsayıcı kayıt defterinizden Docker görüntüsü dağıtıldıktan sonra site, kapsayıcı kayıt defterini barındıran Azure bölgesini temsil eden bir görüntü gösterir.

![Bir tarayıcıda görüntülenen, dağıtılan web uygulaması][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>İkinci Kapsayıcılar için Web App örneğini dağıtma

İkinci bir web uygulamasını *Doğu ABD* bölgesine dağıtmak için önceki bölümde açıklanan yordamı kullanın. **Kapsayıcılar için Web App** bölümünde aşağıdaki değerleri belirtin:

| Ayar | Değer |
|---|---|
| **Site Adı** | Web uygulaması için genel benzersiz bir ad. Bu örnekte, web uygulamasının içinden dağıtıldığı bölgeyi ve kayıt defterini kolayca belirlemek için `<acrName>-eastus` biçimini kullanıyoruz. |
| **Kaynak Grubu** | **Var olanı kullan** > `myResourceGroup` |
| **Uygulama hizmeti planı/Konumu** | `plan-eastus`Doğu ABD**bölgesinde** adlı yeni bir plan oluşturun. |
| **Görüntü** | `acr-helloworld:v1` |
| **İşletim sistemi** | Linux |

**Oluştur**’u seçerek, web uygulamasını *Doğu ABD* bölgesine sağlayın.

![Azure portalında Linux yapılandırmasındaki web uygulaması][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Dağıtılan web uygulamasını görüntüleme

Daha önce olduğu gibi, çalıştırılan uygulamanın URL’sine tarayıcınızda giderek bu uygulamayı görüntüleyebilirsiniz.

Portalda **Uygulama Hizmetleri** seçeneğini belirleyin ve önceki adımda sağladığınız web uygulamasını seçin. Bu örnekte, web uygulaması *uniqueregistryname-eastus* olarak adlandırılmıştır.

Çalıştırılan uygulamayı tarayıcınızda görüntülemek için, **App Service** genel bakışının sağ üst kısmında web uygulamasının köprülü URL’sini seçin.

![Azure portalında Linux yapılandırmasındaki web uygulaması][deploy-app-portal-07]

Coğrafi olarak çoğaltılmış kapsayıcı kayıt defterinizden Docker görüntüsü dağıtıldıktan sonra site, kapsayıcı kayıt defterini barındıran Azure bölgesini temsil eden bir görüntü gösterir.

![Bir tarayıcıda görüntülenen, dağıtılan web uygulaması][deployed-app-eastus]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, coğrafi olarak çoğaltılmış Azure kapsayıcı kayıt defterinden iki Kapsayıcılar için Web App örneğini dağıttınız.

Yeni bir kapsayıcı görüntüsünü güncelleştirmek ve kapsayıcı kayıt defterine dağıtmak, ardından her iki bölgede çalıştırılan web uygulamalarının otomatik olarak güncelleştirildiğini doğrulamak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Coğrafi olarak çoğaltılmış kapsayıcı görüntüsüne güncelleştirmeyi dağıtma](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png