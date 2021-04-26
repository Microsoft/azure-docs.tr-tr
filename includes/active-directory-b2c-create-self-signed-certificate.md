---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: e26dd7ea9f45af6f725f4deaefd9b5bd79a37e1c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073210"
---
Henüz bir sertifikanız yoksa, kendinden imzalı bir sertifika kullanabilirsiniz. Otomatik olarak imzalanan sertifika, bir sertifika yetkilisi (CA) tarafından imzalanmamış ve CA tarafından imzalanan bir sertifika için güvenlik garantisi sağlamayan bir güvenlik sertifikasıdır. 

# <a name="windows"></a>[Windows](#tab/windows)

Windows 'da, bir sertifika oluşturmak için PowerShell 'in [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 'ini kullanın.

1. Otomatik olarak imzalanan bir sertifika oluşturmak için bu PowerShell komutunu yürütün. `-Subject`Bağımsız değişkeni, uygulamanız için uygun şekilde değiştirin ve kiracı adı Azure AD B2C. Ayrıca, `-NotAfter` sertifika için farklı bir süre sonu belirtmek üzere tarihi de ayarlayabilirsiniz.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. **Kullanıcı sertifikalarını Yönet**  >  **Geçerli Kullanıcı**  >  **Kişisel**  >  **sertifikaları**  >  *YourAppName.yourtenant.onmicrosoft.com*' nı açın.
1. Sertifikayı **seçin ve** sonra  >  **Tüm görevler**  >  **dışarı aktar**' ı seçin.
1. **Evet**  >  **İleri**  >  **Evet ' i seçin, ardından özel anahtarı dışarı aktarın**  >  .
1. **Dışarı aktarma dosya biçimi** için varsayılanları kabul edin.
1. Sertifika için bir parola girin.

Azure AD B2C. pfx dosya parolasını kabul etmek için parola, AES256-SHA256 ' nin aksine Windows sertifika deposu dışa aktarma yardımcı programındaki TripleDES-SHA1 seçeneğiyle şifrelenmelidir.

# <a name="macos"></a>[macOS](#tab/macos)

MacOS 'ta sertifika oluşturmak için anahtar zinciri erişimi 'nde [sertifika Yardımcısı](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) 'nı kullanın.

1. [Mac üzerinde Anahtarlık erişimi olan otomatik olarak imzalanan sertifikalar oluşturma](https://support.apple.com/guide/keychain-access/kyca8916/mac)yönergelerini izleyin.
1. Mac 'inizde Anahtarlık erişimi uygulamasında oluşturduğunuz sertifikayı seçin.
1. **Dosya**  >  **dışarı aktarma öğelerini** seçin.
1. Sertifikanızın kaydedileceği bir dosya adı seçin. Örneğin, **otomatik olarak imzalanan sertifika. p12**.
1. **Dosya biçimi** Için **kişisel bilgi değişimi (. p12)** öğesini seçin.
1. **Kaydet**’i seçin.
1. Bir **parola** girin ve parolayı **doğrulayın** .
1. Dosya uzantısını olarak değiştirin `.pfx` . Örneğin, **self-signed-Certificate. pfx**.

---