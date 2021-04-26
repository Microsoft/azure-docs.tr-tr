---
title: İmzalı görüntüleri yönetme
description: Azure Container Registry 'niz için içerik güvenini etkinleştirmeyi ve imzalı görüntüleri gönderme ve çekme hakkında bilgi edinin. İçerik güveni Docker içerik güvenini uygular ve Premium hizmet katmanının bir özelliğidir.
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 238908c0075ffa5d2193eda642175a0cfe75b839
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784128"
---
# <a name="content-trust-in-azure-container-registry"></a>Azure Container Registry'de içerik güveni

Azure Container Registry, Docker 'ın [içerik güven][docker-content-trust] modelini uygular ve imzalı görüntülerin gönderilmesi ve çekmelerini sağlar. Bu makale, kapsayıcı kayıt defterlerinde içerik güvenini etkinleştirmeye başlamanızı tanır.

> [!NOTE]
> İçerik güveni, Azure Container Registry [Premium hizmet katmanının](container-registry-skus.md) bir özelliğidir.

## <a name="how-content-trust-works"></a>İçerik güveni nasıl çalışır?

Güvenlik ön planda tutularak tasarlanmış olan tüm dağıtılmış sistemlerde, sisteme giren verilerin hem *kaynağının* hem de *bütün* doğrulanması önemlidir. Verileri tüketen kullanıcıların, verilerin yayımcısını (kaynağını) doğrulamakla kalmayıp aynı zamanda yayımlandıktan sonra değiştirilmediğinden (bütünlüğünden) de emin olması gerekir. 

İçerik güveni, görüntü yayımcılarının kayıt defterine gönderdikleri görüntüleri **imzalamasını** sağlar. Görüntünüzün tüketicileri (görüntüyü kayıt defterinizden çeken kişiler veya sistemler), istemcilerini *yalnızca* imzalı görüntüleri çekecek şekilde yapılandırır. Tüketici, imzalı görüntüyü çektiğinde Docker istemcisi bu görüntünün bütünlüğünü doğrular. Bu modelde tüketiciler, kayıt defterinizdeki imzalı görüntülerin sizin tarafınızdan yayımlandığından ve yayımlama işleminin sonrasında değiştirilmediğinden emin olur.

### <a name="trusted-images"></a>Güvenilen görüntüler

İçerik güveni, depo içindeki **etiketlerle** çalışır. Görüntü depolarında hem imzalı hem de imzasız etiketlere sahip görüntüler bulunabilir. Örneğin yalnızca `myimage:stable` ve `myimage:latest` görüntülerini imzalayabilir, `myimage:dev` görüntülerini hariç tutabilirsiniz.

### <a name="signing-keys"></a>İmzalama anahtarları

İçerik güveni, şifreli imzalama anahtarları kullanılarak yönetilir. Bu anahtarlar, kayıt defterindeki belirli bir depo ile ilişkilendirilir. Depo içindeki etiketlerin güven durumunu yönetmek için Docker istemcilerinin ve kayıt defterinizin kullandığı birçok imzalama anahtarı türü vardır. İçerik güvenini etkinleştirip kapsayıcı yayımlama ve tüketim işlem hattınızla tümleştirdiğinizde bu anahtarları dikkatli bir şekilde yönetmeniz gerekir. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [Anahtar yönetimi](#key-management) başlığına ve Docker belgelerindeki [İçerik güveni için anahtar yönetimi][docker-manage-keys] bölümüne bakın.

> [!TIP]
> Burada Docker'ın içerik güven modeline yüzeysel bir genel bakış sunulmaktadır. İçerik güveniyle ilgili ayrıntılı bilgiler için bkz. [Docker'da içerik güveni][docker-content-trust].

## <a name="enable-registry-content-trust"></a>Kayıt defteri içerik güvenini etkinleştirme

Öncelikle kayıt defteri düzeyinde içerik güvenini etkinleştirmeniz gerekir. İçerik güvenini etkinleştirdikten sonra istemciler (kullanıcılar veya hizmetler) imzalı görüntüleri kayıt defterine gönderebilir. Kayıt defterinizde içerik güvenini etkinleştirmeniz, kayıt defterinin kullanımını yalnızca içerik güvenini etkinleştirmiş olan tüketicilerle sınırlamaz. İçerik güvenini etkinleştirmemiş tüketiciler de kayıt defterinizi normal bir şekilde kullanmaya devam edebilir. Ancak istemcilerinde içerik güvenini etkinleştirmiş olan tüketiciler kayıt defterinizde *yalnızca* imzalı görüntüleri görebilir.

Kayıt defterinizde içerik güvenini etkinleştirmek için öncelikle Azure portaldan kayıt defterine gidin. **İlkeler** altında **içerik güveni**  >  **etkin**  >  **Kaydet**' i seçin. Azure CLı 'de [az ACR config Content-Trust Update][az-acr-config-content-trust-update] komutunu da kullanabilirsiniz.

![Ekran görüntüsünde Azure portal bir kayıt defteri için içerik güveninin etkinleştirilmesi gösterilmektedir.][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>İstemci içerik güvenini etkinleştirme

Güvenilen görüntülerle çalışmak için hem görüntü yayımcılarının hem de tüketicilerin Docker istemcilerinde içerik güvenini etkinleştirmesi gerekir. Yayımcı olarak içerik güveni etkinleştirilmiş kayıt defterine gönderdiğiniz görüntüleri imzalayabilirsiniz. Tüketici olarak içerik güvenini etkinleştirdiğinizde kayıt defterinde yalnızca imzalı görüntüleri görürsünüz. İçerik güveni Docker istemcilerinde varsayılan olarak devre dışıdır ancak bir kabuk oturumu veya komut için etkinleştirebilirsiniz.

İçerik güvenini bir kabuk oturumunda etkinleştirmek için `DOCKER_CONTENT_TRUST` ortam değişkeninin değerini **1** olarak belirleyin. Örneğin, Bash kabuğunda:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Bunun yerine içerik güvenini tek bir komut için etkinleştirmek veya devre dışı bırakmak istiyorsanız birçok Docker komutuyla `--disable-content-trust` bağımsız değişkenini kullanabilirsiniz. İçerik güvenini tek bir komutta etkinleştirmek için:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

İçerik güvenini kabuk oturumunuz için etkinleştirdiyseniz ve tek bir komut için devre dışı bırakmak istiyorsanız:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Görüntü imzalama izinleri verme

Yalnızca izin verdiğiniz kullanıcılar veya sistemler kayıt defterinize güvenilen görüntü gönderebilir. Bir kullanıcıya (veya hizmet sorumlusu kullanan bir sisteme) güvenilen görüntü gönderme izni vermek için Azure Active Directory kimliklerine `AcrImageSigner` rolünü atayın. Bu, `AcrPush` görüntüleri kayıt defterine göndermek için gereken (veya eşdeğer) rolün yanı sıra. Ayrıntılar için bkz. [Azure Container Registry roller ve izinler](container-registry-roles.md).

> [!IMPORTANT]
> Aşağıdaki yönetim hesaplarına güvenilir görüntü gönderme izni verilemez: 
> * Azure Container Registry 'nin [yönetici hesabı](container-registry-authentication.md#admin-account)
> * [Klasik Sistem Yöneticisi rolüyle](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)Azure Active Directory bir kullanıcı hesabı.

Azure portalda ve Azure CLI ile `AcrImageSigner` rolünün atanmasıyla ilgili ayrıntılı bilgiler aşağıda verilmiştir.

### <a name="azure-portal"></a>Azure portalı

Azure Portal Kayıt defterinize gidin ve ardından **erişim denetimi (IAM)**  >  **rol ataması Ekle**' yi seçin. **Rol ataması Ekle** altında, `AcrImageSigner` **rol** altında öğesini seçin, ardından bir veya daha fazla Kullanıcı veya hizmet sorumlusu **seçin** , sonra **kaydedin**.

Bu örnekte, `AcrImageSigner` rol: "hizmet sorumlusu" adlı bir hizmet sorumlusu ve "Azure user" adlı bir Kullanıcı, bu örnekte atanır.

![Azure portal ACR görüntü imzalama izinleri verme][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

Azure CLI kullanarak bir kullanıcıya imzalama izni vermek için kullanıcıya `AcrImageSigner` rolünü atayın ve kapsamını kayıt defterinizle sınırlandırın. Komut biçimi şu şekildedir:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Örneğin, yönetici olmayan bir kullanıcıya rolü vermek için, kimliği doğrulanmış bir Azure CLı oturumunda aşağıdaki komutları çalıştırabilirsiniz. `REGISTRY` değerini Azure kapsayıcı kayıt defterinizin adıyla değiştirin.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)
```

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee azureuser@contoso.com
```

Dilerseniz bir [hizmet sorumlusuna](container-registry-auth-service-principal.md) da kayıt defterinize güvenilen görüntü gönderme izni verebilirsiniz. Hizmet sorumlusu kullanımı, derleme sistemleri ve kayıt defterinize güvenilen görüntü göndermesi gereken diğer katılımsız sistemler için kullanışlıdır. Biçim, kullanıcıya izin vermeye benzer ancak `--assignee` değerinde hizmet sorumlusu kimliği belirtmeniz gerekir.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

`<service principal ID>`, hizmet sorumlusunun **appId**, **objectId** veya **servicePrincipalNames** değerlerinden biri olabilir. Hizmet sorumluları veya Azure Container Registry ile çalışma hakkında daha fazla bilgi için bkz. [Hizmet sorumlularıyla Azure Container Registry kimlik doğrulaması](container-registry-auth-service-principal.md).

> [!IMPORTANT]
> Herhangi bir rol değişikliği yapıldıktan sonra, `az acr login` yeni rollerin etkili olabilmesi Için Azure CLI yerel kimlik belirtecini yenilemek üzere ' yi çalıştırın. Bir kimlik için rolleri doğrulama hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure rol atamaları ekleme veya kaldırma](../role-based-access-control/role-assignments-cli.md) ve [Azure RBAC sorunlarını giderme](../role-based-access-control/troubleshooting.md).

## <a name="push-a-trusted-image"></a>Güvenilen görüntü gönderme

Kapsayıcı kayıt defterinize güvenilen görüntü etiketi göndermek için içerik güvenini etkinleştirin ve `docker push` ile görüntüyü gönderin. İmzalı bir etikete sahip gönderim ilk kez tamamlandığında, hem kök imza anahtarı hem de depo imzalama anahtarı için bir parola oluşturmanız istenir. Kök ve depo anahtarları yerel makinenizde oluşturulur ve depolanır.

```console
$ docker push myregistry.azurecr.io/myimage:v1
[...]
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

İçerik güveni etkin ilk `docker push` işleminizden sonraki işlemler için Docker istemcisi aynı kök anahtarı kullanır. Aynı depoya yapılan sonraki gönderme işlemlerinde yalnızca depo anahtarı istenir. Güvenilen görüntüyü yeni bir depoya gönderdiğinizde yeni depo anahtarı için bir parola belirtmeniz istenir.

## <a name="pull-a-trusted-image"></a>Güvenilen görüntüyü çekme

Güvenilen görüntüyü çekmek için içerik güvenini etkinleştirin ve `docker pull` komutunu normal bir şekilde çalıştırın. Güvenilen görüntüleri çekmek için, `AcrPull` rol normal kullanıcılar için yeterlidir. Rol gibi ek rol `AcrImageSigner` gerekli değildir. İçerik güvenini etkinleştiren tüketiciler yalnızca imzalı etikete sahip görüntüleri çekebilir. İmzalı etiket çekme örneği aşağıda verilmiştir:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

İçerik güvenine sahip bir istemci imzasız bir etiket çekmeye çalışırsa, işlem aşağıdakine benzer bir hata ile başarısız olur:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
Error: remote trust data does not exist
```

### <a name="behind-the-scenes"></a>Arka planda

`docker pull` komutunu çalıştırdığınızda Docker istemcisi [Notary CLI][docker-notary-cli] ile aynı kitaplığı kullanarak çektiğiniz etiket için SHA-256 özet eşlemesi etiketini ister. İstemci güvenilen verilerdeki imzaları doğruladıktan sonra Docker Engine'den "özetle çekme" gerçekleştirmesini ister. Çekme işlemi sırasında Engine, SHA-256 sağlama toplamını kullanarak Azure kapsayıcı kayıt defterinden görüntü bildirimini ister ve doğrular.

> [!NOTE]
> Azure Container Registry, Nosel CLı 'yı resmi olarak desteklemez, ancak Docker Desktop 'ta bulunan önemli sunucu API 'siyle uyumludur. Şu anda Nosel sürüm **0.6.0** önerilir.

## <a name="key-management"></a>Anahtar yönetimi

İlk güvenilen görüntünüzü gönderdiğinizde gösterilen `docker push` çıktısında belirtildiği üzere kök anahtar en hassas veridir. Kök anahtarınızı yedeklemeyi ve güvenli bir yerde saklamayı unutmayın. Docker istemcisi, imzalama anahtarları varsayılan olarak şu dizinde depolar:

```sh
~/.docker/trust/private
```

Kök ve depo anahtarlarınızı bir arşivde sıkıştırarak ve güvenli bir konumda depolayarak yedekleyin. Örneğin, Bash içinde:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Güvenilen görüntü gönderdiğinizde yerel ortamda oluşturulan kök ve depo anahtarlarına ek olarak Azure Container Registry tarafından oluşturulan ve depolanan anahtarlar da vardır. Docker içerik güveni uygulamasındaki çeşitli anahtarların ayrıntılı açıklaması için Docker belgelerinin [İçerik güveni için anahtar yönetimi][docker-manage-keys] bölümüne bakın.

### <a name="lost-root-key"></a>Kök anahtarın kaybolması

Kök anahtarınıza erişimi kaybederseniz etiketleri bu anahtarla imzalanmış olan depolardaki imzalı etiketlere de erişimi kaybedersiniz. Azure Container Registry, kök anahtarı kayıp olan görüntü etiketlerine erişim sağlayamaz. Kayıt defterinizdeki tüm güven verilerini (imzaları) kaldırmak için kayıt defterinde içerik güvenini devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

> [!WARNING]
> Kayıt defterinizde içerik güvenini devre dışı bırakıp yeniden etkinleştirdiğinizde **kayıt defterinizdeki tüm depolarda bulunan imzalı etiketlerin tüm güven verileri silinir**. Bu eylem geri alınamaz. Azure Container Registry, silinen güven verilerini kurtaramaz. İçerik güvenini devre dışı bıraktığınızda görüntüler silinmez.

Kayıt defterinizde içerik güvenini devre dışı bırakmak için Azure portaldan kayıt defterine gidin. **İlkeler** altında, **içerik güveni**  >  **devre dışı**  >  **Kaydet**' i seçin. Kayıt defterinizdeki tüm imzaların silineceğini belirten bir uyarı görüntülenir. Kayıt defterinizdeki tüm imzaları kalıcı olarak silmek için **Tamam**'ı seçin.

![Azure portalda kayıt defteri için içerik güvenini devre dışı bırakma][content-trust-03-portal]

## <a name="next-steps"></a>Sonraki adımlar

* [Docker Trust](https://docs.docker.com/engine/reference/commandline/trust/) komutları ve [güven temsilcileri](https://docs.docker.com/engine/security/trust/trust_delegation/)dahil içerik güveni hakkında daha fazla bilgi Için bkz. [Docker 'da içerik güveni][docker-content-trust] . Bu makalede birkaç önemli noktaya değinilmiş olsa da içerik güveni kapsamlı bir konudur ve Docker belgelerinde daha ayrıntılı olarak ele alınmıştır.

* Bir Docker görüntüsü oluştururken ve gönderdiğinizde içerik güveninin kullanılması hakkında bir örnek için [Azure Pipelines](/azure/devops/pipelines/build/content-trust) belgelerine bakın.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az_acr_config_content_trust_update
