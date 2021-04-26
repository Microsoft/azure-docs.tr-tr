---
title: Kayıt defteri sistem durumu denetimleri için hata başvurusu
description: Azure Container Registry ' de az ACR Check-Health Diagnostic komutu çalıştırılarak bulunan sorunlara yönelik olası çözümler ve hata kodları
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773450"
---
# <a name="health-check-error-reference"></a>Sistem durumu denetimi hata başvurusu

[Az ACR Check-Health][az-acr-check-health] komutu tarafından döndürülen hata kodlarıyla ilgili ayrıntılar aşağıda verilmiştir. Her hata için olası çözümler listelenir.

Çalışma hakkında daha fazla bilgi için `az acr check-healh` bkz. [Azure Container Registry 'nin sistem durumunu denetleme](container-registry-check-health.md).

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Bu hata, CLı için Docker istemcisinin bulunamadığı anlamına gelir. Sonuç olarak, aşağıdaki ek denetimler çalıştırılmaz: Docker sürümü bulunuyor, Docker Daemon durumunu değerlendiriyor ve Docker Pull komutu çalıştırılıyor.

*Olası çözümler*: Docker Client 'ı yükler; Sistem değişkenlerine Docker yolu ekleyin.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Bu hata, Docker Daemon durumunun kullanılamadığı veya CLı kullanılarak erişilemeyen anlamına gelir. Sonuç olarak, Docker işlemleri ( `docker login` ve gibi `docker pull` ) CLI aracılığıyla kullanılamaz.

*Olası çözümler*: Docker Daemon 'ı yeniden başlatın veya düzgün bir şekilde yüklendiğini doğrulayın.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Bu hata, CLı 'nin komutu çalıştıramayacağı anlamına gelir `docker --version` .

*Olası çözümler*: komutu el ile çalıştırmayı deneyin, en son CLI sürümüne sahip olduğunuzdan emin olun ve hata iletisini araştırın.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Bu hata, CLı 'nin ortamınıza örnek bir görüntü çekemediği anlamına gelir.

*Olası çözümler*: bir görüntüyü çekmek için gereken tüm bileşenlerin düzgün şekilde çalıştığını doğrulayın.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Bu hata, diğer HELI işlemlerini daha fazla kullanan CLı tarafından, Helu istemcisinin bulunamadıkları anlamına gelir.

*Olası çözümler*: hele istemcisinin yüklü olduğunu ve yolunun sistem ortam değişkenlerine eklendiğini doğrulayın.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Bu hata, CLı 'nin yüklü Held sürümünü belirleyemediği anlamına gelir. Bu durum, kullanılmakta olan Azure CLı sürümü (veya Held sürümü) kullanılmıyorsa oluşabilir.

*Olası çözümler*: en son Azure CLI sürümüne veya önerilen Held sürümüne güncelleştirin; komutu el ile çalıştırın ve hata iletisini araştırın.

## <a name="cmk_error"></a>CMK_ERROR

Bu hata, kayıt defterinin, müşteri tarafından yönetilen bir anahtarla kayıt defteri şifrelemesini yapılandırmak için kullanılan Kullanıcı tarafından atanan veya Sysem tarafından atanan yönetilen kimliğe erişemeyeceği anlamına gelir. Yönetilen kimlik silinmiş olabilir.  

*Olası çözüm*: sorunu çözmek ve farklı bir yönetilen kimlik kullanarak anahtarı döndürmek için, bkz. [Kullanıcı tarafından atanan kimlik](container-registry-customer-managed-keys.md#troubleshoot)sorunlarını giderme adımları.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Bu hata, verilen kayıt defteri oturum açma sunucusu için DNS 'nin ping işlemi yaptığı ancak yanıt vermediği anlamına gelir; bu da kullanılamaz. Bu, bazı bağlantı sorunlarını gösterebilir. Alternatif olarak, kayıt defteri mevcut olmayabilir, Kullanıcı kayıt defteri üzerinde izinlere sahip olmayabilir (oturum açma sunucusunu düzgün bir şekilde almak için) veya hedef kayıt defteri, Azure CLı 'de kullanılandan farklı bir bulutta bulunuyor olabilir.

*Olası çözümler*: bağlantıyı doğrulama; kayıt defterinin var olduğunu ve kayıt defteri olduğunu doğrulayın; kullanıcının üzerinde doğru izinlere sahip olduğunu ve kayıt defterinin bulutu 'nın Azure CLı 'de kullanılan aynı olduğunu doğrulayın.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Bu hata, verilen kayıt defteri için zorluk bitiş noktasının 403 yasaklanmış bir HTTP durumuyla yanıt verdiğini gösterir. Bu hata, büyük olasılıkla bir sanal ağ yapılandırması nedeniyle veya kayıt defterinin genel uç noktasına erişime izin verilmediğinden, kullanıcıların kayıt defterine erişimi olmadığı anlamına gelir. Yapılandırılmış olan güvenlik duvarı kurallarını görmek için öğesini çalıştırın `az acr show --query networkRuleSet --name <registry>` .

*Olası çözümler*: sanal ağ kurallarını kaldırın veya GEÇERLI istemci IP adresini izin verilenler listesine ekleyin.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Bu hata, hedef kayıt defterinin sınama uç noktasının bir zorluk yayınlamadığı anlamına gelir.

*Olası çözümler*: bir süre sonra yeniden deneyin. Hata devam ederse, öğesinde bir sorun açın https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Bu hata, hedef kayıt defterinin sınama uç noktasının bir zorluk verdiği, ancak kayıt defterinin Azure Active Directory kimlik doğrulamasını desteklemediği anlamına gelir.

*Olası çözümler*: Örneğin, yönetici kimlik bilgileriyle kimlik doğrulamanın farklı bir yolunu deneyin. Kullanıcıların Azure Active Directory kullanarak kimlik doğrulaması yapması gerekiyorsa, ' de bir sorun açın https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Bu hata, kayıt defteri oturum açma sunucusunun yenileme belirteciyle yanıt vermediği anlamına gelir; bu nedenle hedef kayıt defterine erişim reddedildi. Bu hata, kullanıcının kayıt defterinde doğru izinlere sahip olmaması veya Azure CLı için Kullanıcı kimlik bilgilerinin eski olması durumunda meydana gelebilir.

*Olası çözümler*: kullanıcının kayıt defterinde doğru izinlere sahip olup olmadığını doğrulayın; `az login` izinleri, belirteçleri ve kimlik bilgilerini yenilemek için ' i çalıştırın.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Bu hata, kayıt defteri oturum açma sunucusunun bir erişim belirteciyle yanıt vermediği, hedef kayıt defterine erişimin reddedilmediği anlamına gelir. Bu hata, kullanıcının kayıt defterinde doğru izinlere sahip olmaması veya Azure CLı için Kullanıcı kimlik bilgilerinin eski olması durumunda meydana gelebilir.

*Olası çözümler*: kullanıcının kayıt defterinde doğru izinlere sahip olup olmadığını doğrulayın; `az login` izinleri, belirteçleri ve kimlik bilgilerini yenilemek için ' i çalıştırın.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Bu hata, istemcinin kapsayıcı kayıt defterine güvenli bir bağlantı kurmadığını gösterir. Bu hata genellikle bir ara sunucu çalıştırıyorsanız veya kullanıyorsanız oluşur.

*Olası çözümler*: bir proxy 'nin arkasında çalışma hakkında daha fazla bilgi [burada bulunabilir](/cli/azure/use-cli-effectively).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Bu hata, CLı 'nin verilen kayıt defteri 'nin oturum açma sunucusunu bulamadığı ve geçerli bulut için varsayılan sonekin bulunmadığı anlamına gelir. Kayıt defteri yoksa, kullanıcının kayıt defterinde doğru izinleri yoksa, kayıt defterinin bulutu ve geçerli Azure CLı bulutu eşleşmiyorsa veya Azure CLı sürümü artık kullanılmıyor ise bu hata oluşabilir.

*Olası çözümler*: Yazımın doğru olduğundan ve kayıt defterinin bulunduğundan emin olun; kullanıcının kayıt defterinde doğru izinlere sahip olduğunu ve kayıt defterinin ve CLı ortamının bulutlarının eşleştiğini doğrulayın; Azure CLı 'yı en son sürüme güncelleştirin.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Bu hata, CLı 'nin şu anda yüklü olan Docker/Nosel sürümü ile uyumlu olmadığı anlamına gelir. Bu sorunu çözmek için Docker yüklemenizin Nosel istemcinizi el ile değiştirerek notary.exe sürümünüzü 0.6.0 ' den önceki bir sürüme düşürmeye çalışın.

## <a name="next-steps"></a>Sonraki adımlar

Bir kayıt defterinin sistem durumunu denetleme seçenekleri için bkz. [Azure Container Registry 'nin sistem durumunu denetleme](container-registry-check-health.md).

Sık sorulan sorular ve Azure Container Registry ilgili diğer bilinen sorunlar için [SSS](container-registry-faq.md) bölümüne bakın.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
