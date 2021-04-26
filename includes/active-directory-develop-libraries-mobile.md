---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007910"
---
| Platform          | Proje açık<br/>GitHub                                                                          | Paket                                                                               | Almanızı<br/>başlama                    | Oturum açma kullanıcıları                                         | Web API 'Lerine erişin                                                 | Genel olarak kullanılabilir (GA) *veya*<br/>Genel Önizleme<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Hızlı Başlangıç](../articles/active-directory/develop/quickstart-v2-android.md) | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| iOS (Swift/obj-C) | [iOS ve macOS için MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Öğretici](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure önizlemeleri için ek kullanım koşulları,][preview-tos] kitaplıklar için *genel önizlemede* geçerlidir.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/