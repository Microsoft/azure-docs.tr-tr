---
title: Azure yay bulutu 'nda yapılandırma sunucusu örneğinizi ayarlama | Microsoft Docs
description: Bu öğreticide, Azure Spring Cloud için Azure portal bir Spring Cloud config Server örneği ayarlamayı öğreneceksiniz.
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 7589a3a750e2fe04736bb3c8fc072c7a2c0a7358
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147544"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Öğretici: hizmetiniz için bir Spring Cloud config Server örneği ayarlama

Bu makalede, bir yay bulutu yapılandırma sunucusu örneğini Azure Spring Cloud Service 'e nasıl bağlayacaksınız gösterilmektedir.

Spring Cloud config, dağıtılmış bir sistemde bir externalized yapılandırması için sunucu ve istemci tarafı desteği sağlar. Yapılandırma sunucusu örneğiyle, tüm ortamlarda uygulamalara yönelik dış özellikleri yönetmek için merkezi bir yerdir. Daha fazla bilgi için bkz. [Spring Cloud config Server başvurusu](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 
* Zaten sağlanmış ve Azure yay bulut hizmeti çalıştırılıyor. Azure yay bulut hizmetini ayarlamak ve başlatmak için bkz. [hızlı başlangıç: Azure CLI kullanarak bir Java Spring uygulaması başlatma](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Kısıtlama

Yapılandırma sunucusunu git arka ucu ile kullandığınızda bazı kısıtlamalar vardır. Yapılandırma sunucusuna ve hizmet bulmaya erişmek için bazı özellikler otomatik olarak uygulama ortamınıza eklenir. Ayrıca, bu özellikleri yapılandırma sunucusu dosyalarınızda yapılandırırsanız, çakışmalar ve beklenmeyen davranışlarla karşılaşabilirsiniz. Özellikler şunları içerir: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Yukarıdaki Özellikleri config Server uygulama _dosyalarınıza yerleştirmemenizi_ önemle tavsiye ederiz.

## <a name="create-your-config-server-files"></a>Yapılandırma sunucusu dosyalarınızı oluşturma

Azure Spring Cloud, yapılandırma sunucusu dosyalarınızı depolamak için Azure DevOps, GitHub, GitLab ve BitBucket 'ı destekler. Deponuzu hazırsanız, yapılandırma dosyalarını aşağıdaki yönergelerle oluşturun ve burada depolayın.

Ayrıca, bazı yapılandırılabilir özellikler yalnızca belirli türler için kullanılabilir. Aşağıdaki alt bölümlerde her depo türü için özellikler listelenmektedir.

### <a name="public-repository"></a>Ortak depo

Ortak bir depo kullandığınızda, yapılandırılabilir özellikleri daha sınırlıdır.

Genel Git deposunu kurmak için kullanılan yapılandırılabilir tüm özellikler aşağıdaki tabloda listelenmiştir:

> [!NOTE]
> Sözcükleri ayırmak için kısa çizgi (-) kullanmak, şu anda desteklenen tek adlandırma kuralıdır. Örneğin, *defaultlabel*değil, *varsayılan etiketini*kullanabilirsiniz.

| Özellik        | Gerekli | Özellik                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Yes    | Yapılandırma sunucusu arka ucu olarak kullanılan git deposunun URI 'SI *http://* , *https://* , *Git@* veya *SSH://* ile başlar. |
| `default-label` | Hayır     | Git deposunun varsayılan etiketi, deponun *şube adı*, *etiket adı*veya *kayıt kimliği* olmalıdır. |
| `search-paths`  | Hayır     | Git deposunun alt dizinlerinde arama yapmak için kullanılan bir dize dizisi. |

------

### <a name="private-repository-with-ssh-authentication"></a>SSH kimlik doğrulaması ile özel depo

SSH ile özel Git deposunu kurmak için kullanılan yapılandırılabilir tüm özellikler aşağıdaki tabloda listelenmiştir:

> [!NOTE]
> Sözcükleri ayırmak için kısa çizgi (-) kullanmak, şu anda desteklenen tek adlandırma kuralıdır. Örneğin, *defaultlabel*değil, *varsayılan etiketini*kullanabilirsiniz.

| Özellik                   | Gerekli | Özellik                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Yes    | Yapılandırma sunucusu arka ucu olarak kullanılan git deposunun URI 'SI, *http://* , *https://* , *Git@* veya *SSH://* ile başlatılmış olmalıdır. |
| `default-label`            | Hayır     | Git deposunun varsayılan etiketi, deponun *şube adı*, *etiket adı*veya *kayıt kimliği* olmalıdır. |
| `search-paths`             | Hayır     | Git deposunun alt dizinlerinde arama yapmak için kullanılan bir dize dizisi. |
| `private-key`              | Hayır     | Git deposuna erişmek için SSH özel anahtarı, URI *Git@* veya *SSH://* ile başladığı zaman _gereklidir_ . |
| `host-key`                 | Hayır     | Git deposu sunucusunun ana bilgisayar anahtarı, `host-key-algorithm`kapsamındaki algoritma önekini içermemelidir. |
| `host-key-algorithm`       | Hayır     | Konak anahtar algoritması, *SSH-DSS*, *ssh-rsa*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*veya *ECDSA-SHA2-nistp521*olmalıdır. Yalnızca `host-key` mevcutsa *gereklidir* . |
| `strict-host-key-checking` | Hayır     | Yapılandırma sunucusu örneğinin özel `host-key`kullanılırken başlayamayacağını belirtir. *True* (varsayılan değer) veya *false*olmalıdır. |

-----

### <a name="private-repository-with-basic-authentication"></a>Temel kimlik doğrulaması ile özel depo

Temel kimlik doğrulaması ile özel Git deposunu kurmak için kullanılan tüm yapılandırılabilir özellikler aşağıda listelenmiştir.

> [!NOTE]
> Sözcükleri ayırmak için kısa çizgi (-) kullanmak, şu anda desteklenen tek adlandırma kuralıdır. Örneğin, *defaultlabel*değil *varsayılan etiketini*kullanın.

| Özellik        | Gerekli | Özellik                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Yes    | Yapılandırma sunucusu arka ucu olarak kullanılan git deposunun URI 'SI *http://* , *https://* , *Git@* veya *SSH://* ile başlatılmalıdır. |
| `default-label` | Hayır     | Git deposunun varsayılan etiketi, deponun *şube adı*, *etiket adı*veya *kayıt kimliği* olmalıdır. |
| `search-paths`  | Hayır     | Git deposunun alt dizinlerinde arama yapmak için kullanılan bir dize dizisi. |
| `username`      | Hayır     | Git deposu sunucusu `Http Basic Authentication`desteklediğinde _gerekli_ olan git deposu sunucusuna erişmek için kullanılan Kullanıcı adı. |
| `password`      | Hayır     | Git deposu sunucusu `Http Basic Authentication`desteklediğinde _gerekli_ olan git deposu sunucusuna erişmek için kullanılan parola. |

> [!NOTE]
> Birçok `Git` deposu sunucusu, HTTP temel kimlik doğrulaması için parolalar yerine belirteçlerin kullanılmasını destekler. GitHub gibi bazı depolar, belirteçlerin süresiz olarak kalıcı kalmasına izin verir. Ancak, Azure DevOps dahil bazı git deposu sunucuları, belirteçleri birkaç saat içinde sona ermeyecek şekilde zorlar. Belirteçlerin dolmasına neden olan depolar, Azure Spring Cloud ile belirteç tabanlı kimlik doğrulaması kullanmamalıdır.

### <a name="git-repositories-with-pattern"></a>Desenli Git depoları

Aşağıdaki şekilde Git depoları ayarlamak için kullanılan yapılandırılabilir tüm özellikler aşağıda listelenmiştir.

> [!NOTE]
> Sözcükleri ayırmak için kısa çizgi (-) kullanmak, şu anda desteklenen tek adlandırma kuralıdır. Örneğin, *defaultlabel*değil *varsayılan etiketini*kullanın.

| Özellik                           | Gerekli         | Özellik                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Hayır             | Verilen bir ada sahip git deposu ayarlarından oluşan bir harita. |
| `repos."uri"`                      | `repos` Evet | Yapılandırma sunucusu arka ucu olarak kullanılan git deposunun URI 'SI *http://* , *https://* , *Git@* veya *SSH://* ile başlatılmalıdır. |
| `repos."name"`                     | `repos` Evet | Git deposunda tanımlanabilmesi için bir ad, yalnızca `repos` mevcutsa _gereklidir_ . Örneğin, *Ekip-A*, *Takım-B*. |
| `repos."pattern"`                  | Hayır             | Bir uygulama adıyla eşleşen dizeler dizisi. Her bir düzende `{application}/{profile}` biçimini joker karakterlerle kullanın. |
| `repos."default-label"`            | Hayır             | Git deposunun varsayılan etiketi, deponun *şube adı*, *etiket adı*veya *kayıt kimliği* olmalıdır. |
| `repos."search-paths`"             | Hayır             | Git deposunun alt dizinlerinde arama yapmak için kullanılan bir dize dizisi. |
| `repos."username"`                 | Hayır             | Git deposu sunucusu `Http Basic Authentication`desteklediğinde _gerekli_ olan git deposu sunucusuna erişmek için kullanılan Kullanıcı adı. |
| `repos."password"`                 | Hayır             | Git deposu sunucusu `Http Basic Authentication`desteklediğinde _gerekli_ olan git deposu sunucusuna erişmek için kullanılan parola. |
| `repos."private-key"`              | Hayır             | Git deposuna erişmek için SSH özel anahtarı, URI *Git@* veya *SSH://* ile başladığı zaman _gereklidir_ . |
| `repos."host-key"`                 | Hayır             | Git deposu sunucusunun ana bilgisayar anahtarı, `host-key-algorithm`kapsamındaki algoritma önekini içermemelidir. |
| `repos."host-key-algorithm"`       | Hayır             | Konak anahtar algoritması, *SSH-DSS*, *ssh-rsa*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*veya *ECDSA-SHA2-nistp521*olmalıdır. Yalnızca `host-key` mevcutsa *gereklidir* . |
| `repos."strict-host-key-checking"` | Hayır             | Yapılandırma sunucusu örneğinin özel `host-key`kullanılırken başlayamayacağını belirtir. *True* (varsayılan değer) veya *false*olmalıdır. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Yapılandırma sunucusu deponuzu Azure Spring Cloud 'a ekleyin

Yapılandırma dosyalarınız bir depoya kaydedildiğinden artık Azure Spring Cloud 'ı buna bağlamanız gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure yay bulutuna **genel bakış** sayfasına gidin.

1. Yapılandırılacak hizmeti seçin.

1. Hizmet sayfasının sol bölmesinde, **Ayarlar**' ın altında, **yapılandırma sunucusu** sekmesini seçin.

![Yapılandırma sunucusu penceresi](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Depo bilgilerini doğrudan Azure portal girin

#### <a name="default-repository"></a>Varsayılan depo

* **Ortak depo**: **varsayılan depo** bölümünde, **URI** kutusunda, depo URI 'sini yapıştırın.  **Etiketi** **config**olarak ayarlayın. **Kimlik doğrulama** ayarının **genel**olduğundan emin olun ve ardından son ' **u seçin.** 

* **Özel depo**: Azure yay bulutu, temel parola/belirteç tabanlı kimlik doğrulama ve SSH 'yi destekler.

    * **Temel kimlik doğrulaması**: **varsayılan depo** bölümünde, **URI** kutusunda, depo URI 'sini yapıştırın ve ardından **kimlik doğrulaması** ("kurşun kalem" simgesi) düğmesini seçin. **Kimlik doğrulamasını Düzenle** bölmesinde, **kimlik doğrulama türü** aşağı açılan listesinde, **http Basic**' i seçin ve ardından Kullanıcı adınızı ve parolanızı/belirtecinizi girerek Azure yay bulutuna erişim izni verin. **Tamam**' ı seçin ve ardından **Uygula** ' yı seçerek yapılandırma sunucusu örneğinizi ayarlamayı tamamlayın.

    ![Kimlik doğrulama bölmesini Düzenle](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > GitHub gibi bazı git deposu sunucuları, **temel kimlik doğrulaması**için bir *Kişisel belirteç* veya parola gibi bir *erişim belirteci*kullanır. Bu tür bir belirteci, hiçbir zaman dolmayacağı için Azure Spring Cloud 'da parola olarak kullanabilirsiniz. Ancak Bitbucket ve Azure DevOps gibi diğer git deposu sunucuları için *erişim belirtecinin* süresi bir veya iki saat içinde dolar. Bu, Azure Spring Cloud ile bu depo sunucularını kullandığınızda seçeneğinin önemli olmadığı anlamına gelir.

    * **SSH**: **varsayılan depo** bölümünde, **URI** kutusunda, depo URI 'sini yapıştırın ve ardından **kimlik doğrulaması** ("kurşun kalem" simgesi) düğmesini seçin. **Kimlik doğrulamasını Düzenle** bölmesinde, **kimlik doğrulama türü** aşağı açılan listesinde **SSH**' ı seçin ve ardından **özel anahtarınızı**girin. İsteğe bağlı olarak, **ana bilgisayar anahtarınızı** ve **ana bilgisayar anahtarı algoritmanızı**belirtin. Yapılandırma sunucusu deponuza ortak anahtarınızı eklediğinizden emin olun. **Tamam**' ı seçin ve ardından **Uygula** ' yı seçerek yapılandırma sunucusu örneğinizi ayarlamayı tamamlayın.

    ![Kimlik doğrulama bölmesini Düzenle](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Model deposu

Hizmetinizi yapılandırmak için isteğe bağlı bir **model deposu** kullanmak Istiyorsanız, **URI** 'Yi ve **kimlik doğrulamasını** **varsayılan depoyla**aynı şekilde belirtin. Hiyerarşiniz için bir **ad** eklediğinizden emin olun ve ardından örneğine eklemek için **Uygula** ' yı seçin. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Bir YAML dosyasına depo bilgilerini girin

Depo ayarlarınızla bir YAML dosyası yazdıysanız, dosyayı doğrudan yerel makinenizden Azure Spring Cloud 'a aktarabilirsiniz. Temel kimlik doğrulaması ile özel bir depo için basit bir YAML dosyası şuna benzer:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

**Içeri aktarma ayarları** düğmesini seçin ve ardından Proje DIZININIZDEN YAML dosyasını seçin. **Içeri aktar**' ı seçin ve ardından **bildirimlerinizin** `async` bir işlemi açılır. 1-2 dakika sonra, başarıyı bildirmeli.

![Yapılandırma sunucusu bildirimleri bölmesi](media/spring-cloud-tutorial-config-server/local-yml-success.png)


YAML dosyanızdaki bilgiler Azure portal görüntülenmelidir. Son 'a **Uygula** ' yı seçin. 


## <a name="delete-your-app-configuration"></a>Uygulama yapılandırmanızı silme

Bir yapılandırma dosyasını kaydettikten sonra **yapılandırma** sekmesinde **uygulama yapılandırmasını sil** düğmesi görünür. Bu düğme seçildiğinde, var olan ayarlarınız tamamen silinir. Yapılandırma sunucusu örneğinizi GitHub 'dan Azure DevOps 'a geçme gibi başka bir kaynağa bağlamak istiyorsanız bu seçeneği seçmeniz gerekir.



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Spring Cloud config Server örneğinizi nasıl etkinleştireceğinizi ve yapılandıracağınızı öğrendiniz. Uygulamanızı yönetme hakkında daha fazla bilgi edinmek için uygulamanızı el ile ölçeklendirmeyle ilgili öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: Azure Spring Cloud 'da bir uygulamayı ölçeklendirme](spring-cloud-tutorial-scale-manual.md)
