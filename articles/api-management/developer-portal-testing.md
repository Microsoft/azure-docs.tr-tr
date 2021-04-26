---
title: Şirket içinde barındırılan geliştirici portalını test etme
titleSuffix: Azure API Management
description: Kendi kendine barındırılan API Management portalınız için birim testlerini ve uçtan uca testleri ayarlamayı öğrenin.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741884"
---
# <a name="test-the-self-hosted-developer-portal"></a>Şirket içinde barındırılan geliştirici portalını test etme

Bu makalede, [kendi kendine barındırılan portalınız](developer-portal-self-host.md)için birim testlerinin ve uçtan uca testlerin nasıl ayarlanacağı açıklanır.

## <a name="unit-tests"></a>Birim testleri

Birim testi, küçük işlevsellik parçalarını doğrulamaya yönelik bir yaklaşımdır. Uygulamanın diğer bölümlerinden yalıtımıyla yapılır.

### <a name="example-scenario"></a>Örnek senaryo

Bu senaryoda, bir parola girişi denetimini test eteceğiz. Yalnızca en az içeren parolalar kabul eder:

- Tek harf

- Bir sayı

- Tek bir özel karakter
 
Bu nedenle, bu gereksinimleri doğrulamaya yönelik test şöyle görünür:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Proje yapısı

Doğrulanması gereken bileşenin yanında bir birim testinin tutulması yaygındır.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Sahte HTTP istekleri

Bir bileşenin HTTP istekleri yapmasını bekleyen durumlar vardır. Bileşenin farklı tür yanıtlara doğru şekilde tepki vermesini sağlar. Belirli HTTP yanıtlarının benzetimini yapmak için kullanın `MockHttpClient` . `HttpClient`Projenin diğer birçok bileşeni tarafından kullanılan arabirimini uygular.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Uçtan uca testler

Uçtan uca test, belirli bir Kullanıcı senaryosunu, kullanıcının gerçekleştirmesini umduğunuz tam adımları gerçekleştirerek yürütür. Azure API Management geliştirici portalı gibi bir Web uygulamasında, Kullanıcı içerik üzerinde geziniyor ve belirli sonuçları elde etmek için seçenekleri seçiyor. 

Kullanıcı gezintisini çoğaltmak için [Puppeteer](https://github.com/puppeteer/puppeteer)gibi tarayıcı işleme yardımcı kitaplıklarını kullanabilirsiniz. Kullanıcı eylemlerinin benzetimini yapmanızı ve kabul edilen senaryolara otomatik hale getirmenizi sağlar. Ayrıca, Puppeteer otomatik olarak sayfaların veya bileşenlerin ekran görüntülerini otomatik olarak alır. Bunları daha sonra catch sapmaları ve potansiyel gerilemeler için önceki sonuçlarla karşılaştırın.

### <a name="example-scenario"></a>Örnek senaryo

Bu senaryoda, bir Kullanıcı oturum açma akışını doğrulamanız gerekir. Bu senaryo aşağıdaki adımları gerektirir:

1. Tarayıcıyı açın ve oturum açma sayfasına gidin.

1. E-posta adresini girin.

1. Parolayı girin.

1. **Oturum aç '** ı seçin.

1. Kullanıcının giriş sayfasına yönlendirildiğini doğrulayın.

1. Sayfanın **profil** menü öğesini içerdiğini doğrulayın. Bu, başarıyla oturum açtığınız olası göstergelerden biridir.

Testi otomatik olarak çalıştırmak için, tam olarak aynı adımlarla bir komut dosyası oluşturun:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> "#Email", "#password" ve "#signin" gibi dizeler, sayfadaki HTML öğelerini belirleyen bir CSS benzeri seçicileridir. Daha fazla bilgi edinmek için [seçiciler düzey 3](https://www.w3.org/TR/selectors-3/) W3C belirtimine bakın.

### <a name="ui-component-maps"></a>UI bileşen eşlemeleri

Kullanıcı akışları genellikle aynı sayfalara veya bileşenlere gider. Her sayfada bulunan ana web sitesi menüsü iyi bir örnektir. 

Her test için aynı seçicileri yapılandırmayı ve güncellemeyi önlemek için bir UI bileşen haritası oluşturun. Örneğin, yukarıdaki örnekteki 2 ile 6 arasındaki adımları yalnızca iki satır ile değiştirebilirsiniz:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Test yapılandırması

Belirli senaryolar önceden oluşturulmuş veriler veya yapılandırma gerektirebilir. Örneğin, sosyal medya hesaplarıyla Kullanıcı oturumunu otomatik hale getirmeniz gerekebilir. Bu verilerin hızla veya kolayca oluşturulması zordur.

Bu amaçla, test senaryonuza özel bir yapılandırma dosyası ekleyebilirsiniz. Test betikleri dosyadan gerekli verileri alabilir. Yapı ve test işlem hattına bağlı olarak testler, adlandırılmış bir güvenli mağazadan gizli dizileri çekebilir.

Projenizin klasöründe depolanacak bir örneği aşağıda verilmiştir `validate.config.json` `src` .

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Gözetimsiz vs normal testler

Chrome veya Microsoft Edge gibi modern tarayıcılar hem gözetimsiz modda hem de normal modda Otomasyon çalıştırmanızı sağlar. Tarayıcı, gözetimsiz modda grafiksel bir kullanıcı arabirimi olmadan çalışır. Hala aynı sayfayı ve Belge Nesne Modeli (DOM) işlemelerini gerçekleştirir. Tarayıcı Kullanıcı Arabirimi genellikle teslim işlem hatları 'nda gerekli değildir. Bu durumda, gözetimsiz modda testlerin çalıştırılması harika bir seçenektir.

Bir test betiği geliştirirken, tarayıcıda tam olarak ne olduğunu görmek faydalı olur. Normal mod kullanmak iyi bir zamandır.

Modlar arasında geçiş yapmak için, `headless` dosyadaki seçenek seçeneğini değiştirin `constants.ts` . Bu, `tests` projenizdeki klasörde yer alabilir:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Başka bir yararlı seçenek de vardır `slowMo` . Her eylem arasındaki testin yürütülmesini duraklatır:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Testleri çalıştırma

Bu projede testlerin yürütülmesi için iki yerleşik yol vardır:

**NPM komutu**

```console
npm run test
```

**Test Gezgini**

VS Code için test Gezgini uzantısının (örneğin, [Mocha test Gezgini](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)), uygun bir kullanıcı arabirimi ve kaynak kodunun her değiştiğinde testleri otomatik olarak çalıştırma seçeneği vardır:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Test Gezgini Visual Studio Code ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)

- [Geliştirici portalını kendi kendine barındırma](developer-portal-self-host.md)
