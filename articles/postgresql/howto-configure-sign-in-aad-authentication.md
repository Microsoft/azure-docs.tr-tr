---
title: Azure Active Directory kullanın-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı-tek sunucu ile kimlik doğrulaması için Azure Active Directory (AAD) ayarlama hakkında bilgi edinin
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 729879bb472786165b21a47a7baf058294a4db1f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961532"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>PostgreSQL ile kimlik doğrulaması için Azure Active Directory kullanma

Bu makale, PostgreSQL için Azure veritabanı ile Azure Active Directory erişimi yapılandırma ve Azure AD belirteci kullanarak bağlanma adımlarında size yol gösterecektir.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD yönetici kullanıcısını ayarlama

Yalnızca Azure AD yöneticisi kullanıcıları Azure AD tabanlı kimlik doğrulaması için Kullanıcı oluşturabilir/etkinleştirebilir. Yükseltilmiş kullanıcı izinleri (ör. CREATEDB) olduğundan, normal veritabanı işlemleri için Azure AD yöneticisi 'ni kullanmaktan önermiyoruz.

Azure AD yöneticisini ayarlamak için (bir kullanıcı veya grup kullanabilirsiniz), lütfen aşağıdaki adımları izleyin

1. Azure portal Azure AD için etkinleştirmek istediğiniz PostgreSQL için Azure veritabanı örneğini seçin.
2. Ayarlar altında yönetici Active Directory ' yi seçin:

![Azure AD yöneticisini ayarlama][2]

3. Müşteri kiracısında Azure AD yöneticisi olacak geçerli bir Azure AD kullanıcısı seçin.

> [!IMPORTANT]
> Yönetici ayarlanırken, PostgreSQL için Azure veritabanı sunucusuna tam yönetici izinleriyle yeni bir Kullanıcı eklenir. PostgreSQL için Azure veritabanı 'nda Azure AD yönetici kullanıcısının rolü olacaktır `azure_ad_admin` .

Her PostgreSQL sunucusu için yalnızca bir Azure AD yöneticisi oluşturulabilir ve başka bir tane seçilebilir, sunucu için yapılandırılmış mevcut Azure AD yöneticisinin üzerine yazılır. Tek bir kullanıcı yerine birden çok yönetici olmak üzere bir Azure AD grubu belirtebilirsiniz. Daha sonra yönetim amaçları için grup adıyla oturum açacaksınız.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Azure AD 'yi kullanarak PostgreSQL için Azure veritabanı 'na bağlanma

Aşağıdaki üst düzey diyagram, PostgreSQL için Azure veritabanı ile Azure AD kimlik doğrulaması kullanma iş akışını özetler:

![kimlik doğrulama akışı][1]

Azure AD tümleştirmesini, Azure AD Aware olmayan ve yalnızca PostgreSQL 'e bağlanırken Kullanıcı adı ve parola belirtmeyi destekleyen psql gibi ortak PostgreSQL araçlarıyla çalışacak şekilde tasarladık. Yukarıdaki resimde gösterildiği gibi Azure AD belirtecini parola olarak geçiyoruz.

Şu anda aşağıdaki istemcileri test ettik:

- psql CommandLine (belirteci geçirmek için PGPASSWORD değişkenini kullanın, aşağıya bakın)
- Azure Data Studio (PostgreSQL uzantısını kullanarak)
- Diğer libpq tabanlı istemciler (örneğin, yaygın uygulama çerçeveleri ve ORMs)

> [!NOTE]
> Parola için 256 karakterlik sabit kodlanmış bir sınırlamaya sahip olduğundan, lütfen pgAdmin ile Azure AD belirtecini kullanmanın şu anda desteklenmediğini unutmayın (belirtecin sonunda).

Bu adımlar, bir kullanıcı/uygulamanın, aşağıda açıklanan Azure AD ile kimlik doğrulaması yapmak için gereken adımlardır:

### <a name="prerequisites"></a>Önkoşullar

Azure Cloud Shell, bir Azure VM veya yerel makinenizde da izleyebilirsiniz. [Azure CLI 'nin yüklü](/cli/azure/install-azure-cli)olduğundan emin olun.

### <a name="step-1-authenticate-with-azure-ad"></a>1. Adım: Azure AD ile kimlik doğrulama

Azure CLı aracını kullanarak Azure AD ile kimlik doğrulaması yaparak başlayın. Azure Cloud Shell Bu adım gerekli değildir.

```
az login
```

Bu komut, Azure AD kimlik doğrulama sayfasında bir tarayıcı penceresi başlatır. Azure AD Kullanıcı KIMLIĞINIZ ve parolanızı sağlamanız gerekir.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2. Adım: Azure AD erişim belirtecini alma

Azure AD kimliği doğrulanmış kullanıcı için adım 1 ' den PostgreSQL için Azure veritabanı 'na erişim için bir erişim belirteci almak üzere Azure CLı aracını çağırın.

Örnek (genel bulut için):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Yukarıdaki kaynak değeri tam olarak gösterildiği gibi belirtilmelidir. Diğer bulutlarda, kaynak değeri şu kullanılarak aranabilir:

```azurecli-interactive
az cloud show
```

Azure CLı sürüm 2.0.71 ve üzeri için, komut tüm bulutlar için aşağıdaki daha uygun sürümde belirtilebilir:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Kimlik doğrulaması başarılı olduktan sonra Azure AD, bir erişim belirteci döndürür:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Belirteç, kimliği doğrulanmış kullanıcıyla ilgili tüm bilgileri kodlayan ve PostgreSQL için Azure veritabanı hizmetine hedeflenen bir temel 64 dizesidir.

> [!NOTE]
> Erişim belirteci geçerliliği, 5 dakikadan 60 dakika arasında bir süre sürer. PostgreSQL için Azure veritabanı 'nda oturum açmayı başlatmadan önce erişim belirtecini almanızı öneririz.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3. Adım: PostgreSQL ile oturum açmak için belirteci parola olarak kullanın

Bağlantı sırasında, PostgreSQL Kullanıcı parolası olarak erişim belirtecini kullanmanız gerekir.

`psql`Komut satırı istemcisini kullanırken, erişim belirteci `PGPASSWORD` doğrudan kabul edebilecek parola uzunluğunu aştığından, erişim belirtecinin ortam değişkenine geçirilmesi gerekir `psql` :

Windows örneği:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Linux/macOS örneği:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Artık şu şekilde, PostgreSQL için Azure veritabanı ile bir bağlantı başlatabilirsiniz:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Bağlanırken dikkat edilmesi gereken önemli noktalar:

* `user@tenant.onmicrosoft.com` , bağlanmaya çalıştığınız Azure AD kullanıcısının veya grubunun adıdır
* Azure AD Kullanıcı/Grup adından sonra sunucu adını her zaman Ekle (örn. `@mydb` )
* Azure AD Kullanıcı veya grup adının tam yolunu kullandığınızdan emin olun
* Azure AD Kullanıcı ve grup adları büyük/küçük harfe duyarlıdır
* Grup olarak bağlanırken, yalnızca Grup adını kullanın (ör. `GroupName@mydb` )
* Ad boşluk içeriyorsa, `\` kaçış için her alandan önce kullanın

Artık Azure AD kimlik doğrulaması kullanarak PostgreSQL sunucunuza kimlik doğrulamış olursunuz.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nda Azure AD kullanıcıları oluşturma

PostgreSQL için Azure veritabanı veritabanınıza bir Azure AD kullanıcısı eklemek için, bağlandıktan sonra aşağıdaki adımları gerçekleştirin (bkz. bağlanma hakkında sonraki bölüm):

1. İlk olarak Azure AD kullanıcısının `<user>@yourtenant.onmicrosoft.com` Azure AD kiracısında geçerli bir kullanıcı olduğundan emin olun.
2. PostgreSQL için Azure veritabanı örneğiniz için Azure AD Yönetici kullanıcısı olarak oturum açın.
3. `<user>@yourtenant.onmicrosoft.com`PostgreSQL Için Azure veritabanı 'nda rol oluştur.
4. `<user>@yourtenant.onmicrosoft.com`Azure_ad_user rolün bir üyesini oluşturun. Bu yalnızca Azure AD kullanıcılarına verilmelidir.

**Örnek:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Azure AD ile bir kullanıcının kimlik doğrulaması, kullanıcıya PostgreSQL için Azure veritabanı veritabanı içindeki nesnelere erişim izni vermez. Kullanıcıya gerekli izinleri el ile vermeniz gerekir.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nda Azure AD grupları oluşturma

Veritabanınıza erişim için bir Azure AD grubunu etkinleştirmek üzere, kullanıcılar için aynı mekanizmayı kullanın, bunun yerine grup adını belirtin:

**Örnek:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Oturum açarken, grubun üyeleri kendi kişisel erişim belirteçlerini kullanır, ancak Kullanıcı adı olarak belirtilen grup adıyla oturum açılır.

## <a name="token-validation"></a>Belirteç doğrulama

PostgreSQL için Azure veritabanı 'nda Azure AD kimlik doğrulaması, kullanıcının PostgreSQL sunucusunda mevcut olmasını sağlar ve belirtecin içeriğini doğrulayarak belirtecin geçerliliğini denetler. Aşağıdaki belirteç doğrulama adımları gerçekleştirilir:

- Belirteç Azure AD tarafından imzalanmış ve bu değişiklik yapılmamıştır
- Belirteç, sunucuyla ilişkili kiracı için Azure AD tarafından verildi
- Belirtecin süresi sona ermedi
- Belirteç, PostgreSQL için Azure veritabanı kaynağına (başka bir Azure kaynağı değil) yöneliktir

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Mevcut PostgreSQL kullanıcılarını Azure AD tabanlı kimlik doğrulamasına geçirme

Mevcut kullanıcılar için Azure AD kimlik doğrulamasını etkinleştirebilirsiniz. Dikkate alınması gereken iki durum vardır:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Durum 1: PostgreSQL Kullanıcı adı, Azure AD Kullanıcı asıl adıyla eşleşiyor

Mevcut kullanıcılarınızın Azure AD kullanıcı adlarıyla zaten eşleştiği durumlarda, bu `azure_ad_user` rolü Azure AD kimlik doğrulaması için etkinleştirmek üzere rol verebilirsiniz:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Artık, daha önce yapılandırılmış olan PostgreSQL Kullanıcı parolasını kullanmak yerine Azure AD kimlik bilgileriyle oturum açabiliyor.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Durum 2: PostgreSQL Kullanıcı adı, Azure AD Kullanıcı asıl adından farklı

Bir PostgreSQL kullanıcısı Azure AD 'de yoksa veya farklı bir kullanıcı adına sahipse, bu PostgreSQL kullanıcısı olarak kimlik doğrulaması yapmak için Azure AD gruplarını kullanabilirsiniz. PostgreSQL kullanıcısına eşleşen bir ada sahip bir Azure AD grubu oluşturarak ve ardından mevcut PostgreSQL kullanıcısına rol azure_ad_user vererek, PostgreSQL için mevcut Azure veritabanı kullanıcılarını Azure AD 'ye geçirebilirsiniz.

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Bu, Azure AD 'de "DBReadUser" grubunu oluşturmuş olduğunuzu varsayar. Bu gruba ait kullanıcılar artık bu kullanıcı olarak veritabanında oturum açabiliyor.

## <a name="next-steps"></a>Sonraki adımlar

* [PostgreSQL Için Azure veritabanı ile Azure Active Directory kimlik doğrulaması](concepts-aad-authentication.md) için genel kavramları gözden geçirin-tek sunucu

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
