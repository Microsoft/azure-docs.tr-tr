---
title: Azure Key Vault kimlik doğrulaması temelleri
description: Anahtar kasasının kimlik doğrulama modelinin nasıl çalıştığı hakkında bilgi edinin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/15/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f0cd2fb341dd790a7628cc3cf6a5bdd87d7c3687
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753425"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault Kimlik Doğrulamasının temelleri

Azure Key Vault, merkezi ve güvenli bir bulut deposundaki gizli diziler, anahtarlar ve sertifikalar gibi uygulama kimlik bilgilerini güvenli bir şekilde depolamanıza ve yönetmenize olanak sağlar. Key Vault, uygulamalarınızda kimlik bilgilerini depolama gereksinimini ortadan kaldırır. Uygulamalarınız, kimlik bilgilerini almak için çalışma zamanında Key Vault kimlik doğrulaması yapabilir.

Yönetici olarak, hangi kullanıcıların ve uygulamaların anahtar kasanıza erişebileceğini sıkı bir şekilde kontrol edebilir ve gerçekleştirdikleri işlemleri sınırlayabilir ve denetleyebilirsiniz. Bu belgede, Anahtar Kasası erişim modelinin temel kavramları açıklanmaktadır. Bu, size tanıtım düzeyi bilgileri sağlar ve baştan sona kadar bir kullanıcı veya uygulamanın anahtar kasasında nasıl kimlik doğrulayacağınızı gösterir.

## <a name="required-knowledge"></a>Gerekli bilgi

Bu belgede aşağıdaki kavramlar hakkında bilgi sahibi olduğunuz varsayılır. Bu kavramlardan herhangi birini tanımıyorsanız, devam etmeden önce yardım bağlantılarını izleyin.

* Azure Active Directory [bağlantısı](../../active-directory/fundamentals/active-directory-whatis.md)
* Güvenlik sorumluları [bağlantısı](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault yapılandırma adımları Özeti

1. Kullanıcı veya uygulamanızı bir güvenlik sorumlusu olarak Azure Active Directory kaydedin.
1. Azure Active Directory güvenlik sorumlusu için bir rol ataması yapılandırın.
1. Güvenlik sorumlusu için Anahtar Kasası erişim ilkelerini yapılandırın.
1. Anahtar kasanıza Key Vault güvenlik duvarı erişimi yapılandırma (isteğe bağlı).
1. Güvenlik sorumlusunun anahtar kasasına erişme yeteneğini test edin.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Bir kullanıcı veya uygulamayı güvenlik sorumlusu olarak Azure Active Directory kaydetme

Bir kullanıcı veya uygulama, Anahtar Kasası 'na istek yaptığında, önce isteğin kimliği Azure Active Directory tarafından doğrulanmalıdır. Bunun çalışması için, kullanıcının veya uygulamanın güvenlik sorumlusu olarak Azure Active Directory kayıtlı olması gerekir.

Azure Active Directory bir kullanıcı veya uygulamayı nasıl kaydedeceğinizi öğrenmek için aşağıdaki belge bağlantılarını izleyin.
**Kullanıcı kaydı ve uygulamalar için istemci parolası ya da istemci sertifikası kimlik bilgileri için bir parola oluşturduğunuzdan emin olun.**

* Azure Active Directory [bağlantısına](../../active-directory/fundamentals/add-users-azure-active-directory.md) Kullanıcı kaydetme
* Azure Active Directory [bağlantısına](../../active-directory/develop/quickstart-register-app.md) uygulama kaydetme

## <a name="assign-your-security-principal-a-role"></a>Güvenlik sorumlusuna rol atama

Güvenlik sorumlularına izin atamak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz. Bu izinlere rol atamaları denir.

Anahtar Kasası bağlamında, bu rol atamaları, anahtar kasasının yönetim düzleminde (denetim düzlemi olarak da bilinir) bir güvenlik sorumlusunun erişim düzeyini tespit ediyor. Bu rol atamaları doğrudan veri düzlemi gizli dizileri için erişim sağlamaz, ancak anahtar kasasının özelliklerini yönetmek için erişim sağlar. Örneğin, bir **okuyucu rolü** atanan bir kullanıcı veya uygulamanın, Anahtar Kasası güvenlik duvarı ayarlarında değişiklik yapmasına izin verilmez, ancak **katkıda bulunan bir rolün** atandığı kullanıcı veya uygulama değişiklik yapabilir. Hiçbir rolün, Anahtar Kasası veri düzlemine erişim atanıncaya kadar, değerlerini oluşturma veya alma gibi parolalar, anahtarlar ve sertifikalar üzerinde işlem gerçekleştirmek için doğrudan erişimi olmayacaktır. Bu, bir sonraki adımda ele alınmıştır.

>[!IMPORTANT]
> Katkıda bulunan veya sahip rolü olan kullanıcıların, varsayılan olarak anahtar kasasında depolanan gizli dizileri üzerinde işlem gerçekleştirme erişimi olmasa da, katkıda bulunan ve sahip rolleri, Anahtar Kasası 'nda depolanan gizli dizileri için erişim ilkeleri ekleme veya kaldırma izinleri sağlar. Bu nedenle, bu rol atamalarına sahip bir Kullanıcı, anahtar kasasındaki gizli anahtarlara erişim yetkisi verebilir. Bu nedenle, katkıda bulunan veya sahip rollerine yalnızca yöneticilerin erişiminin olması önerilir. Yalnızca anahtar kasasından gizli dizileri alması gereken kullanıcılara ve uygulamalara okuyucu rolü verilmelidir. **Sonraki bölümde daha fazla ayrıntı.**

>[!NOTE]
> Bir kullanıcıya Azure Active Directory kiracı düzeyinde bir rol ataması atadığınızda, bu izin kümesi, atama kapsamındaki tüm abonelikler, kaynak grupları ve kaynaklara göre yapılır. En az ayrıcalığın sorumlusunu izlemek için, bu rol atamasını daha ayrıntılı bir kapsamda yapabilirsiniz. Örneğin, bir kullanıcıya abonelik düzeyinde bir okuyucu rolü ve tek bir Anahtar Kasası için sahip rolü atayabilirsiniz. Daha ayrıntılı bir kapsamda rol ataması yapmak için bir aboneliğin, kaynak grubunun veya anahtar kasasının kimlik erişim yönetimi (ıAM) ayarlarına gidin.

* Azure rolleri [bağlantısı](../../role-based-access-control/built-in-roles.md) hakkında daha fazla bilgi edinmek için
* Rol [atamaları atama](../../role-based-access-control/role-assignments-portal.md) veya kaldırma hakkında daha fazla bilgi edinmek için

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Güvenlik sorumlusu için Anahtar Kasası erişim ilkelerini yapılandırma

Kullanıcılarınızın ve uygulamalarınızın anahtar kasasına erişmesi için erişim izni vermeden önce, bir anahtar kasasında gerçekleştirilebilecek farklı işlem türlerini anlamak önemlidir. İki ana anahtar kasası işlemi, yönetim düzlemi (denetim düzlemi olarak da anılır) işlemleri ve veri düzlemi Işlemleri vardır.

Bu tabloda, veri düzlemine göre yönetim düzlemi tarafından denetlenen farklı işlemlere yönelik birkaç örnek gösterilmektedir. Anahtar kasasının özelliklerini değiştiren işlemler, yönetim düzlemi operasyonlardır. Anahtar Kasası 'nda depolanan gizli dizi değerlerini değiştiren veya alan işlemler veri düzlemi operasyonlarıdır.

|Yönetim düzlemi Işlemleri (örnekler)|Veri düzlemi Işlemleri (örnekler)|
| --- | --- |
| Key Vault oluştur | Anahtar, gizli dizi, sertifika oluşturma
| Key Vault Sil | Anahtar, gizli dizi, sertifika silme
| Key Vault rol atamaları ekleme veya kaldırma | Anahtarların, parolaların, sertifikaların değerlerini listeleyin ve alın
| Key Vault erişim Ilkeleri ekleme veya kaldırma | Anahtarları, parolaları, sertifikaları yedekleme ve geri yükleme
| Key Vault güvenlik duvarı ayarlarını değiştir | Anahtarları, parolaları, sertifikaları yenile
| Key Vault kurtarma ayarlarını değiştir | Geçici olarak silinen anahtarları, gizli dizileri, sertifikaları Temizleme veya kurtarma
| Key Vault tanılama günlükleri ayarlarını değiştir

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Yönetim düzlemi erişim & Azure Active Directory rol atamaları

Azure Active Directory rol atamaları, bir anahtar kasasında yönetim düzlemi işlemleri gerçekleştirmek için erişim izni verir. Bu erişim genellikle uygulamalara değil, kullanıcılara verilir. Kullanıcının rol atamasını değiştirerek, kullanıcının gerçekleştirebileceği yönetim düzlemi işlemlerini kısıtlayabilirsiniz.

Örneğin, bir kullanıcıya Key Vault okuyucu rolü atamak, kullanıcıların erişim ilkeleri gibi anahtar kasalarınızın özelliklerini görmesine izin verir, ancak herhangi bir değişiklik yapmasına izin vermez. Kullanıcı atama bir sahip rolü, Anahtar Kasası yönetim düzlemi ayarlarını değiştirmek için tam erişime izin verir.

Rol atamaları, Anahtar Kasası Access Control (ıAM) dikey penceresinde denetlenir. Belirli bir kullanıcının bir okuyucu veya birden çok Anahtar Kasası kaynağı Yöneticisi olmasını istiyorsanız, kasada, kaynak grubunda veya abonelik düzeyinde bir rol ataması oluşturabilir ve rol ataması atama kapsamındaki tüm kaynaklara eklenir.

Veri düzlemi erişimi veya anahtarlar, gizlilikler ve Anahtar Kasası 'nda depolanan sertifikalar üzerinde işlem gerçekleştirme erişimi iki şekilde eklenebilir.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Veri düzlemi erişim seçeneği 1: klasik Key Vault erişim Ilkeleri

Anahtar Kasası erişim ilkeleri, kullanıcılara ve uygulamalara bir anahtar kasasında veri düzlemi işlemleri gerçekleştirme erişimi verir.

> [!NOTE]
> Bu erişim modeli, aşağıda belgelenen Anahtar Kasası (seçenek 2) için Azure RBAC ile uyumlu değildir. Birini seçmeniz gerekir. Anahtar kasanızın erişim Ilkesi sekmesine tıkladığınızda bu seçimi yapma fırsatına sahip olursunuz.

Klasik erişim ilkeleri ayrıntılı olduğundan, her bir kullanıcının veya uygulamanın bir anahtar kasasında bireysel işlemler gerçekleştirmesine izin verebilir veya bu yeteneği reddedebileceğiniz anlamına gelir. İşte birkaç örnek:

* Güvenlik sorumlusu 1 herhangi bir anahtar işlemi gerçekleştirebilir, ancak herhangi bir gizli dizi veya sertifika işlemi gerçekleştirmesine izin verilmez.
* Güvenlik sorumlusu 2 tüm anahtarları, parolaları ve sertifikaları listeleyebilir ve okuyabilir, ancak herhangi bir oluşturma, silme veya yenileme işlemi gerçekleştiremez.
* Güvenlik sorumlusu 3 tüm gizli dizileri yedekleyebilir ve geri yükleyebilir, ancak gizli dizi değerlerini okuyamaz.

Ancak, klasik erişim ilkeleri nesne düzeyindeki izinler için izin vermez ve atanan izinler tek bir Anahtar Kasası kapsamına uygulanır. Örneğin, belirli bir anahtar kasasındaki güvenlik sorumlusuna "gizli alma" erişim ilkesi izni verirseniz, güvenlik sorumlusu söz konusu anahtar kasasındaki tüm gizli dizileri alabilir. Ancak, bu "gizli dizi" izni diğer anahtar kasaları için otomatik olarak genişlemez ve açıkça atanmalıdır.

> [!IMPORTANT]
> Klasik Anahtar Kasası erişim ilkeleri ve Azure Active Directory rol atamaları birbirinden bağımsızdır. Güvenlik sorumlusu atama bir abonelik düzeyindeki ' katılımcı ' rolü otomatik olarak güvenlik sorumlusuna, abonelik kapsamındaki her anahtar kasasında veri düzlemi işlemleri gerçekleştirme imkanı vermez. Güvenlik sorumlusunun yine de verilmesi gerekir veya veri düzlemi işlemlerini gerçekleştirmek için kendilerine erişim ilkesi izinleri vermelidir.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault"></a>Veri düzlemi erişim seçeneği 2: Key Vault için Azure RBAC

Anahtar Kasası veri düzlemine erişim vermek için yeni bir yol, Anahtar Kasası için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanmaktır.

> [!NOTE]
> Bu erişim modeli yukarıda gösterilen Anahtar Kasası klasik erişim ilkeleriyle uyumlu değil. Birini seçmeniz gerekir. Anahtar kasanızın erişim Ilkesi sekmesine tıkladığınızda bu seçimi yapma fırsatına sahip olursunuz.

Key Vault rol atamaları, anahtarlara, sırları ve sertifikalara erişmek için kullanılan ortak izin kümelerini çevreleyen bir dizi Azure yerleşik rol atamalarından oluşur. Bu izin modeli, klasik Anahtar Kasası erişim ilkesi modelinde kullanılamayan ek yetenekler de sunar.

* Azure RBAC izinleri, kullanıcılara bir abonelikte, kaynak grubunda veya tek bir Anahtar Kasası düzeyinde atanmış bu rollere sahip olmasını sağlayarak ölçeklenebilir şekilde yönetilebilir. Bir kullanıcının Azure RBAC atamasının kapsamındaki tüm anahtar kasaları için veri düzlemi izinleri olacaktır. Bu, Anahtar Kasası başına Kullanıcı/uygulama başına bireysel erişim ilkesi izinleri atama gereksinimini ortadan kaldırır.

* Azure RBAC izinleri Privileged Identity Management veya PıM ile uyumludur. Bu, Key Vault Yöneticisi gibi ayrıcalıklı roller için tam zamanında erişim denetimleri yapılandırmanıza olanak tanır. Bu, en iyi güvenlik uygulamasıdır ve anahtar kasalarınıza olan erişimi ortadan kaldırarak en az ayrıcalık sorumlusunu izler.

Key Vault için Azure RBAC hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* Key Vault için Azure RBAC [bağlantısı](rbac-guide.md)
* Key Vault rolleri için Azure RBAC [bağlantısı](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Key Vault güvenlik duvarını yapılandırma

Varsayılan olarak, Anahtar Kasası genel İnternet 'ten gelen trafiğin ortak bir uç nokta aracılığıyla anahtar kasanıza ulaşmasını sağlar. Ek bir güvenlik katmanı için, Anahtar Kasası genel uç noktasına erişimi kısıtlamak üzere Azure Key Vault güvenlik duvarını yapılandırabilirsiniz.

Anahtar Kasası güvenlik duvarını etkinleştirmek için, Anahtar Kasası portalındaki Ağ sekmesine tıklayın ve şu kaynaktan erişime Izin vermek için radyo düğmesini seçin: "özel uç nokta ve seçili ağlar". Anahtar Kasası güvenlik duvarını etkinleştirmeyi seçerseniz, bu, Anahtar Kasası güvenlik duvarı üzerinden trafiğe izin vermenin yollarıdır.

* Anahtar Kasası güvenlik duvarı izin verilenler listesine IPv4 adresleri ekleyin. Bu seçenek, statik IP adresleri olan uygulamalar için en iyi şekilde kullanılır.

* Anahtar Kasası güvenlik duvarına bir sanal ağ ekleyin. Bu seçenek, sanal makineler gibi dinamik IP adreslerine sahip Azure kaynakları için en iyi şekilde geçerlidir. Azure kaynaklarını bir sanal ağa ekleyebilir ve sanal ağı Anahtar Kasası güvenlik duvarı izin verilenler listesine ekleyebilirsiniz. Bu seçenek, sanal ağ içinde özel bir IP adresi olan bir hizmet uç noktası kullanır. Bu, ek bir koruma katmanı sağlar, böylece Anahtar Kasası ve sanal ağınız arasında hiçbir trafik genel İnternet üzerinden yönlendirilir. Hizmet uç noktası hakkında daha fazla bilgi için aşağıdaki belgelere bakın. [bağlantısının](./network-security.md)

* Anahtar kasasına özel bağlantı bağlantısı ekleyin. Bu seçenek, Sanal ağınızı, anahtar kasasının sanal ağınızın içinde etkin bir şekilde bir araya getiren bir Anahtar Kasası örneğine doğrudan bağlar. Bir özel uç nokta bağlantısını Anahtar Kasası ile yapılandırma hakkında daha fazla bilgi edinmek için aşağıdaki [bağlantıya](./private-link-service.md) bakın

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Hizmet sorumlunun anahtar kasasına erişme yeteneğini test etme

Yukarıdaki adımların tümünü izledikten sonra anahtar kasanızdan gizli dizileri ayarlayıp elde edebilirsiniz.

### <a name="authentication-process-for-users-examples"></a>Kullanıcılar için kimlik doğrulama işlemi (örnekler)

* Kullanıcılar, anahtar kasasını kullanmak için Azure portal oturum açabilirler. [Key Vault portalı hızlı başlangıç](./quick-create-portal.md)

* Kullanıcı, anahtar kasasını kullanmak için Azure CLı kullanabilir. [Azure CLı hızlı başlangıç Key Vault](./quick-create-cli.md)

* Kullanıcı, anahtar kasasını kullanmak için Azure PowerShell kullanabilir. [Key Vault Azure PowerShell hızlı başlangıç](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Uygulamalar veya hizmetler için Azure Active Directory kimlik doğrulama işlemi (örnekler)

* Bir uygulama, bir Azure Active Directory belirteci almak için bir işlevde istemci gizli anahtarı ve istemci KIMLIĞI sağlar. 

* Uygulama, Azure Active Directory belirteci almak için bir sertifika sağlar. 

* Azure kaynağı Azure Active Directory belirtecini almak için MSI kimlik doğrulamasını kullanır. 

* MSI kimlik doğrulaması [bağlantısı](../../active-directory/managed-identities-azure-resources/overview.md) hakkında daha fazla bilgi edinin

### <a name="authentication-process-for-application-python-example"></a>Uygulama için kimlik doğrulama işlemi (Python örneği)

Uygulamanızın yapılandırdığınız hizmet sorumlusunu kullanarak anahtar kasasından gizli dizi alıp almamadığını test etmek için aşağıdaki kod örneğini kullanın.

>[!NOTE]
>Bu örnek yalnızca tanıtım ve test amaçlıdır. **ÜRETIMDE ISTEMCI gızlı KIMLIK doğrulaması** kullanmayın Bu güvenli bir tasarım uygulaması değil. En iyi uygulama olarak istemci sertifikası veya MSI kimlik doğrulamasını kullanmanız gerekir.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Sonraki Adımlar

Anahtar Kasası kimlik doğrulaması hakkında daha ayrıntılı bilgi edinmek için aşağıdaki belgeye bakın. [Key Vault Kimlik Doğrulaması](./authentication.md)
