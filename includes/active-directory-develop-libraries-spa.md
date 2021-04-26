---
ms.openlocfilehash: 05fc91667f34262c6b510c0e1464d3e5fad339bc
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590040"
---
| Dil/çerçeve | Proje açık<br/>GitHub                                                                                                    | Paket                                                                      | Almanızı<br/>başlama                             | Oturum açma kullanıcıları                                         | Web API 'Lerine erişin                                                 | Genel olarak kullanılabilir (GA) *veya*<br/>Genel Önizleme<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [Msal angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | Genel Önizleme                                               |
| Angular              | [Msal angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Öğretici](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| AngularJS            | [Msal AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [msal-AngularJS](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | Genel Önizleme                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-tarayıcı](https://www.npmjs.com/package/@azure/msal-browser)     | [Öğretici](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
|JavaScript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-çekirdek](https://www.npmjs.com/package/@azure/msal-core)    | [Öğretici](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| React                | [Msal tepki](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)verme<sup>2</sup>                 | [msal-tepki verme](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | Genel Önizleme                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure önizlemeleri için ek kullanım koşulları,][preview-tos] kitaplıklar için *genel önizlemede* geçerlidir.

<sup>2</sup> yalnızca pcke ile [kimlik doğrulama kodu akışı][auth-code-flow] (önerilir). 

<sup>3</sup> yalnızca [dolaylı verme akışı][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md