---
title: Azure Active Directory kullanma-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı ile kimlik doğrulaması için Azure Active Directory (Azure AD) ayarlama hakkında bilgi edinin
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 492e56e09129f9d47b863624cd72cd508801c143
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728275"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>MySQL ile kimlik doğrulaması için Azure Active Directory kullanma

Bu makale, MySQL için Azure veritabanı ile Azure Active Directory erişimi yapılandırma ve Azure AD belirteci kullanarak bağlanma adımlarında size yol gösterecektir.

> [!IMPORTANT]
> Azure Active Directory kimlik doğrulaması yalnızca MySQL 5,7 ve üzeri sürümlerde kullanılabilir.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD yönetici kullanıcısını ayarlama

Yalnızca bir Azure AD Yönetici kullanıcısı, kullanıcıları Azure AD tabanlı kimlik doğrulaması için oluşturabilir/etkinleştirebilir. Bir Azure AD Yönetici kullanıcısı oluşturmak için lütfen aşağıdaki adımları izleyin

1. Azure portal Azure AD için etkinleştirmek istediğiniz MySQL için Azure veritabanı örneğini seçin.
2. Ayarlar altında yönetici Active Directory ' yi seçin:

![Azure AD yöneticisini ayarlama][2]

3. Müşteri kiracısında Azure AD yöneticisi olacak geçerli bir Azure AD kullanıcısı seçin.

> [!IMPORTANT]
> Yönetici ayarlanırken, MySQL için Azure veritabanı sunucusuna tam yönetici izinleriyle yeni bir Kullanıcı eklenir.

Her MySQL sunucusu için yalnızca bir Azure AD yöneticisi oluşturulabilir ve başka bir tane seçilebilir, sunucu için yapılandırılmış mevcut Azure AD yöneticisinin üzerine yazılır.

Yönetici yapılandırıldıktan sonra artık oturum açabilirsiniz:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Azure AD kullanarak MySQL için Azure veritabanı 'na bağlanma

Aşağıdaki üst düzey diyagram MySQL için Azure veritabanı ile Azure AD kimlik doğrulaması kullanma iş akışını özetler:

![kimlik doğrulama akışı][1]

Azure AD tümleştirmesini, Azure AD Aware olmayan ve yalnızca MySQL 'e bağlanırken Kullanıcı adı ve parola belirtmeyi destekleyen MySQL CLı gibi ortak MySQL araçlarıyla çalışacak şekilde tasarladık. Yukarıdaki resimde gösterildiği gibi Azure AD belirtecini parola olarak geçiyoruz.

Şu anda aşağıdaki istemcileri test ettik:

- Mysqlçalışma ekranı 
- MySQL CLı

Ayrıca, en yaygın uygulama sürücülerini test ettik, bu sayfanın sonundaki ayrıntıları görebilirsiniz.

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

Azure AD kimliği doğrulanmış kullanıcı için adım 1 ' den MySQL için Azure veritabanı 'na erişim için bir erişim belirteci almak üzere Azure CLı aracını çağırın.

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
PowerShell kullanarak, erişim belirtecini almak için aşağıdaki komutu kullanabilirsiniz:

```azurepowershell-interactive
$accessToken = Get-AzAccessToken -ResourceUrl https://ossrdbms-aad.database.windows.net
$accessToken.Token | out-file C:\temp\MySQLAccessToken.txt
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

Belirteç, kimliği doğrulanmış kullanıcıyla ilgili tüm bilgileri kodlayan ve MySQL için Azure veritabanı hizmetine hedeflenen bir temel 64 dizesidir.

Erişim belirteci geçerliliği, ***5 dakikadan 60 dakika*** arasında bir süre sürer. MySQL için Azure veritabanı 'nda oturum açmayı başlatmadan önce erişim belirtecini almanızı öneririz. Belirteç geçerliliğini görmek için aşağıdaki PowerShell komutunu kullanabilirsiniz. 

```azurepowershell-interactive
$accessToken.ExpiresOn.DateTime
```

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>3. Adım: MySQL ile oturum açmak için belirteci parola olarak kullanın

Bağlantı sırasında MySQL Kullanıcı parolası olarak erişim belirtecini kullanmanız gerekir. Mysqlme gibi GUI istemcilerini kullanırken, belirteci almak için yukarıda açıklanan yöntemi kullanabilirsiniz. 

#### <a name="using-mysql-cli"></a>MySQL CLı kullanma
CLı kullanırken, bu kısa süreli bağlantı kurmak için şunu kullanabilirsiniz: 

**Örnek (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```
#### <a name="using-mysql-workbench"></a>MySQL çalışma ekranı kullanma
* MySQL çalışma ekranı 'nı başlatın ve veritabanı seçeneğine tıklayın, ardından "veritabanına Bağlan" seçeneğine tıklayın
* Ana bilgisayar adı alanına MySQL FQDN 'sini girin. mydb.mysql.database.azure.com
* Kullanıcı adı alanında, MySQL Azure Active Directory yönetici adını girin ve bunu FQDN değil MySQL sunucu adına ekleyin (örneğin,). user@tenant.onmicrosoft.com@mydb
* Parola alanında, "kasa içinde depola" ' ya tıklayın ve dosyadan erişim belirtecini yapıştırın, örneğin C:\temp\MySQLAccessToken.txt
* Gelişmiş sekmesine tıklayın ve "düz metin kimlik doğrulaması eklentisini etkinleştir" seçeneğini işaretleyin
* Veritabanına bağlanmak için Tamam 'a tıklayın

#### <a name="important-considerations-when-connecting"></a>Bağlanırken dikkat edilmesi gereken önemli noktalar:

* `user@tenant.onmicrosoft.com` , bağlanmaya çalıştığınız Azure AD kullanıcısının veya grubunun adıdır
* Azure AD Kullanıcı/Grup adından sonra sunucu adını her zaman Ekle (örn. `@mydb` )
* Azure AD Kullanıcı veya grup adının tam yolunu kullandığınızdan emin olun
* Azure AD Kullanıcı ve grup adları büyük/küçük harfe duyarlıdır
* Grup olarak bağlanırken, yalnızca Grup adını kullanın (ör. `GroupName@mydb` )
* Ad boşluk içeriyorsa, `\` kaçış için her alandan önce kullanın

"Enable-şifresiz-Plugin" ayarını unutmayın. belirtecin karma kalmadan sunucuya gönderilmesini sağlamak için diğer istemcilerle benzer bir yapılandırma kullanmanız gerekir.

Artık Azure AD kimlik doğrulaması kullanarak MySQL sunucunuza kimliğiniz doğrulanır.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda Azure AD kullanıcıları oluşturma

MySQL veritabanı için Azure veritabanı 'na bir Azure AD kullanıcısı eklemek için, bağlandıktan sonra aşağıdaki adımları gerçekleştirin (bkz. bağlanma hakkında sonraki bölüm):

1. İlk olarak Azure AD kullanıcısının `<user>@yourtenant.onmicrosoft.com` Azure AD kiracısında geçerli bir kullanıcı olduğundan emin olun.
2. MySQL için Azure veritabanı örneğinde Azure AD Yönetici kullanıcısı olarak oturum açın.
3. `<user>@yourtenant.onmicrosoft.com`MySQL Için Azure veritabanı 'nda kullanıcı oluşturun.

**Örnek:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

32 karakteri aşan Kullanıcı adları için, bağlantı sırasında kullanılmak üzere bunun yerine bir diğer ad kullanmanız önerilir: 

Örnek:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Azure AD aracılığıyla bir kullanıcının kimliğini doğrulamak, kullanıcıya MySQL için Azure veritabanı veritabanı içindeki nesnelere erişim izni vermez. Kullanıcıya gerekli izinleri el ile vermeniz gerekir.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda Azure AD grupları oluşturma

Veritabanınıza erişim için bir Azure AD grubunu etkinleştirmek üzere, kullanıcılar için aynı mekanizmayı kullanın, bunun yerine grup adını belirtin:

**Örnek:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Oturum açarken, grubun üyeleri kendi kişisel erişim belirteçlerini kullanır, ancak Kullanıcı adı olarak belirtilen grup adıyla oturum açılır.

## <a name="token-validation"></a>Belirteç doğrulama

MySQL için Azure veritabanı 'nda Azure AD kimlik doğrulaması, kullanıcının MySQL sunucusunda mevcut olmasını sağlar ve belirtecin içeriğini doğrulayarak belirtecin geçerliliğini denetler. Aşağıdaki belirteç doğrulama adımları gerçekleştirilir:

-   Belirteç Azure AD tarafından imzalanmış ve bu değişiklik yapılmamıştır
-   Belirteç, sunucuyla ilişkili kiracı için Azure AD tarafından verildi
-   Belirtecin süresi sona ermedi
-   Belirteç, MySQL için Azure veritabanı kaynağına yöneliktir (başka bir Azure kaynağı değil)

## <a name="compatibility-with-application-drivers"></a>Uygulama sürücüleriyle uyumluluk

Çoğu sürücü desteklenir, ancak belirtecin değişiklik yapılmadan gönderilmesi için parolayı düz metin olarak gönderme ayarlarını kullandığınızdan emin olun.

* C/C++
  * libmysqlclient: destekleniyor
  * MySQL-bağlayıcı-c + +: desteklenir
* Java
  * Bağlayıcı/J (MySQL-Connector-Java): desteklenir, ayar kullanılmalıdır `useSSL`
* Python
  * Bağlayıcı/Python: destekleniyor
* Ruby
  * mysql2: destekleniyor
* .NET
  * MySQL-Connector-net: desteklenen, mysql_clear_password için eklenti eklemesi gerekiyor
  * MySQL-net/MySqlConnector: destekleniyor
* Node.js
  * mysqljs: desteklenmez (düzeltme eki olmadan şifresiz metin olarak bir belirteç göndermez)
  * Node-mysql2: destekleniyor
* Perl
  * DBD:: MySQL: destekleniyor
  * Net:: MySQL: desteklenmiyor
* Go
  * Go-SQL-Driver: desteklenir, `?tls=true&allowCleartextPasswords=true` bağlantı dizesine ekleyin

## <a name="next-steps"></a>Sonraki adımlar

* [MySQL Için Azure veritabanı ile Azure Active Directory kimlik doğrulaması](concepts-azure-ad-authentication.md) için genel kavramları gözden geçirin

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
