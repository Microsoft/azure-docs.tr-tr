---
title: 'Azure AD Connect eşitleme: Dizin uzantıları | Microsoft Docs'
description: Bu konuda Azure AD Connect içindeki dizin uzantıları özelliği açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fdfce58bdd8e13637e77d01d4b6c0ab21f696a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607644"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect eşitleme: Dizin genişletmeleri
Azure Active Directory (Azure AD) içindeki şemayı şirket içi Active Directory kendi nitelikleriyle genişletmek için Dizin uzantıları 'nı kullanabilirsiniz. Bu özellik, şirket içinde yönetmeye devam ettiğiniz öznitelikleri kullanıp LOB uygulamaları oluşturmanıza olanak sağlar. Bu öznitelikler [Azure AD Graph API Directory uzantıları](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) veya [Microsoft Graph](https://developer.microsoft.com/graph/)üzerinden kullanılabilir. Kullanılabilir öznitelikleri sırasıyla [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) ve [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)' ı kullanarak görebilirsiniz.

Mevcut olduğunda, Office 365 iş yükü bu öznitelikleri kullanır.

Yükleme sihirbazındaki özel ayarlar yolunda hangi ek özniteliklerin eşitlenmesini istediğinizi yapılandırırsınız.

>[!NOTE]
>Kullanılabilir öznitelikler kutusu, büyük/küçük harfe duyarlıdır.

![Şema uzantısı Sihirbazı](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Yükleme, geçerli adaylar olan aşağıdaki öznitelikleri gösterir:

* Kullanıcı ve grup nesne türleri
* Tek değerli öznitelikler: Dize, Boole, tamsayı, Ikili
* Çoklu değerli öznitelikler: Dize, Ikili


>[!NOTE]
> Azure AD Connect, çok değerli Active Directory özniteliklerinin Azure AD 'ye çok değerli Dizin uzantıları olarak eşitlenmesini desteklediğinden, ancak şu anda çok değerli dizin uzantısı özniteliklerinde karşıya yüklenen verileri almanın/kullanmanın bir yolu yoktur.

Öznitelik listesi, Azure AD Connect yüklemesi sırasında oluşturulan şema önbelleğinden okunurdur. Active Directory şemasını ek özniteliklerle genişlettiyseniz, bu yeni özniteliklerin görünebilmesi [için şemayı yenilemeniz](how-to-connect-installation-wizard.md#refresh-directory-schema) gerekir.

Azure AD 'deki bir nesne dizin uzantıları için en fazla 100 özniteliğe sahip olabilir. En fazla 250 karakter uzunluğunda olur. Bir öznitelik değeri daha uzunsa, eşitleme altyapısı onu keser.

Azure AD Connect yüklenmesi sırasında, bu özniteliklerin kullanılabildiği bir uygulama kaydedilir. Bu uygulamayı Azure portal görebilirsiniz.

![Şema uzantısı uygulaması](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Özniteliklere {appclientıd} \_\_uzantısı ön eki eklenir. Appclientıd, Azure AD kiracınızdaki tüm öznitelikler için aynı değere sahip.

Bu öznitelikler artık Azure AD Graph API aracılığıyla kullanılabilir. [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/)kullanarak bunları sorgulayabilirsiniz.

![Azure AD Graph Gezgini](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

İsterseniz, [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer#)'ni kullanarak Microsoft Graph API aracılığıyla öznitelikleri sorgulayabilirsiniz.

>[!NOTE]
> Özniteliklerin döndürülmesini isteyebilirsiniz. Şunun gibi öznitelikleri açıkça seçin: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $Select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Daha fazla bilgi için bkz [. Microsoft Graph: Sorgu parametrelerini](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)kullanın.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
