---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008016"
---
| Dil/çerçeve | Proje açık<br/>GitHub                                                                 | Paket                                                                                | Almanızı<br/>başlama                           | Oturum açma kullanıcıları                                            | Web API 'Lerine erişin                                                 | Genel olarak kullanılabilir (GA) *veya*<br/>Genel Önizleme<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Hızlı Başlangıç](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![Kitaplık, Kullanıcı oturumu açma için KIMLIK belirteçleri isteyemezsiniz.][n] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Kitaplık, Kullanıcı oturumu açma için KIMLIK belirteçleri isteyemezsiniz.][n] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| Düğüm               | [MSAL düğümü](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-Node](https://www.npmjs.com/package/@azure/msal-node)  | [Hızlı Başlangıç](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![Kitaplık, Kullanıcı oturumu açma için KIMLIK belirteçleri isteyemezsiniz.][n] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Hızlı Başlangıç](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![Kitaplık, Kullanıcı oturumu açma için KIMLIK belirteçleri isteyemezsiniz.][n] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure önizlemeleri için ek kullanım koşulları,][preview-tos] kitaplıklar için *genel önizlemede* geçerlidir.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/