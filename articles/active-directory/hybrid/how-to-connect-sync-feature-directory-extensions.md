---
title: 'Eşitleme Azure AD Connect: Dizin uzantıları | Microsoft Docs'
description: Bu konuda Azure AD Connect içindeki dizin uzantıları özelliği açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976886"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Eşitleme Azure AD Connect: Dizin uzantıları
Azure Active Directory (Azure AD) içindeki şemayı şirket içi Active Directory kendi nitelikleriyle genişletmek için Dizin uzantıları 'nı kullanabilirsiniz. Bu özellik, şirket içinde yönetmeye devam ettiğiniz öznitelikleri kullanıp LOB uygulamaları oluşturmanıza olanak sağlar. Bu öznitelikler, [Uzantılar](/graph/extensibility-overview
)aracılığıyla tüketilebilir. Kullanılabilir öznitelikleri [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer)kullanarak görebilirsiniz. Bu özelliği ayrıca, Azure AD 'de dinamik gruplar oluşturmak için de kullanabilirsiniz.

Mevcut olduğunda, Microsoft 365 iş yükü bu öznitelikleri kullanır.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Azure AD ile hangi özniteliklerin eşitleneceğini özelleştirin

Yükleme sihirbazındaki özel ayarlar yolunda hangi ek özniteliklerin eşitlenmesini istediğinizi yapılandırırsınız.

> [!NOTE]
> 1.2.65.0 ' den önceki sürümlerde Azure AD Connect, **kullanılabilir özniteliklerin** arama kutusu büyük/küçük harfe duyarlıdır.

![Şema uzantısı Sihirbazı](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Yükleme, geçerli adaylar olan aşağıdaki öznitelikleri gösterir:

* Kullanıcı ve grup nesne türleri
* Tek değerli öznitelikler: String, Boolean, Integer, binary
* Çoklu değerli öznitelikler: dize, Ikili


>[!NOTE]
> Azure AD Connect birden çok değerli Active Directory özniteliği Azure AD 'ye çok değerli bir öznitelik uzantısı olarak eşitlendikten sonra, SAML talebine özniteliği eklemek mümkündür. Ancak, bu verileri API çağrısıyla kullanmak mümkün değildir.

Öznitelik listesi, Azure AD Connect yüklemesi sırasında oluşturulan şema önbelleğinden okunurdur. Active Directory şemasını ek özniteliklerle genişlettiyseniz, bu yeni özniteliklerin görünebilmesi [için şemayı yenilemeniz](how-to-connect-installation-wizard.md#refresh-directory-schema) gerekir.

Azure AD 'deki bir nesne dizin uzantıları için en fazla 100 özniteliğe sahip olabilir. En fazla 250 karakter uzunluğunda olur. Bir öznitelik değeri daha uzunsa, eşitleme altyapısı onu keser.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Azure AD 'de sihirbaz tarafından yapılan yapılandırma değişiklikleri

Azure AD Connect yüklenmesi sırasında, bu özniteliklerin kullanılabildiği bir uygulama kaydedilir. Bu uygulamayı Azure portal görebilirsiniz. Adı her zaman **kiracı şeması uzantısı uygulamasıdır**.

![Şema uzantısı uygulaması](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Bu uygulamayı görmek için **tüm uygulamalar** ' ı seçtiğinizden emin olun.

Öznitelikler **\_ {ApplicationId \_ } uzantısıyla** önek olarak eklenir. ApplicationId, Azure AD kiracınızdaki tüm öznitelikler için aynı değere sahiptir. Bu konudaki tüm diğer senaryolar için bu değere ihtiyacınız olacaktır.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Microsoft Graph API 'sini kullanarak öznitelikleri görüntüleme

Bu öznitelikler artık Microsoft Graph API ile [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#)kullanılarak kullanılabilir.

>[!NOTE]
> Microsoft Graph API 'sinde, özniteliklerin döndürülmesini istemek gerekir. Aşağıdaki öznitelikleri açıkça seçin: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Daha fazla bilgi için bkz. [Microsoft Graph: sorgu parametrelerini kullanma](/graph/query-parameters#select-parameter).

>[!NOTE]
> AADConnect ile öznitelik değerlerinin AADConnect tarafından oluşturulmamış uzantı özniteliklerine eşitlenmesi desteklenmez. Bunun yapılması, performans sorunları ve beklenmedik sonuçlar doğurabilir. Yalnızca yukarıda gösterildiği gibi oluşturulan uzantı öznitelikleri eşitleme için desteklenir.

## <a name="use-the-attributes-in-dynamic-groups"></a>Dinamik gruplardaki öznitelikleri kullanma

Daha kullanışlı senaryolarından biri, bu özniteliklerin dinamik güvenlik veya Microsoft 365 gruplarında kullanılması.

1. Azure AD 'de yeni bir grup oluşturun. Buna iyi bir ad verin ve **Üyelik türünün** **Dinamik Kullanıcı** olduğundan emin olun.

   ![Yeni bir grup ile ekran görüntüsü](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. **Dinamik sorgu eklemek** için seçin. Özelliklere bakarsanız, bu genişletilmiş öznitelikleri görmezsiniz. Önce bunları eklemeniz gerekir. **Özel uzantı özellikleri al**' a tıklayın, uygulama kimliğini girin ve **özellikleri Yenile**' ye tıklayın.

   ![Dizin uzantılarının eklendiği ekran görüntüsü](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Özellik açılan öğesini açın ve eklediğiniz özniteliklerin artık görünür olduğunu unutmayın.

   ![Yeni özniteliklerin Kullanıcı arabiriminde gösterdiği ekran görüntüsü](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Gereksinimlerinize uyacak şekilde ifadeyi doldurun. Örneğimizde, kural **(User.extension_9d98ed114c4840d298fad781915f27e4_division-EQ "Sales ve Marketing")** olarak ayarlanır.

4. Grup oluşturulduktan sonra, üyelerin doldurulması için Azure AD 'ye bir süre verin ve ardından üyeleri gözden geçirin.

   ![Dinamik gruptaki üyelere sahip ekran görüntüsü](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
