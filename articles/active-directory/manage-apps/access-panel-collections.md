---
title: Azure Active Directory | Uygulama portallarım için Koleksiyonlar oluşturun | Microsoft Docs
description: Son kullanıcılarınıza yönelik daha basit uygulamalar için uygulamalarımı özelleştirmek üzere uygulama koleksiyonlarımı kullanın. Uygulamaları ayrı sekmeler içeren gruplar halinde düzenleyin.
services: active-directory
documentationcenter: ''
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc79e8026cb91b8ef3eac2addbb097b9db83afa7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377698"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Uygulamalarım portalında koleksiyonlar oluşturma

Kullanıcılarınız, erişimi olan bulut tabanlı uygulamaları görüntülemek ve başlatmak için My Apps portalını kullanabilir. Varsayılan olarak, bir kullanıcının erişebileceği tüm uygulamalar tek bir sayfada birlikte listelenir. Kullanıcılarınıza yönelik bu sayfayı daha iyi düzenlemek için, Azure AD Premium P1 veya P2 lisansınız varsa, koleksiyonları ayarlayabilirsiniz. Bir koleksiyonla, ilişkili uygulamaları (örneğin, iş rolü, görev veya proje) gruplandırabilirsiniz ve bunları ayrı bir sekmede görüntüleyebilirsiniz. Bir koleksiyon aslında kullanıcının zaten erişebileceği uygulamalara bir filtre uygular, böylece Kullanıcı yalnızca koleksiyonda atanmış olan uygulamaları görür.

> [!NOTE]
> Bu makalede, bir yöneticinin koleksiyonları nasıl etkinleştirebileceğini ve oluşturabileceğiniz ele alınmaktadır. My Apps Portal ve koleksiyonlarımı kullanma hakkında bilgi için bkz. [erişim ve kullanım koleksiyonları](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>En son uygulamalarımı etkinleştir özellikleri

1. [**Azure Portal**](https://portal.azure.com/) açın ve Kullanıcı Yöneticisi veya genel yönetici olarak oturum açın.

2. **Azure Active Directory**  >  **Kullanıcı ayarları**' na gidin.

3. **Kullanıcı özelliği önizlemeleri** altında **Kullanıcı özelliği Önizleme ayarlarını yönet**' i seçin.

4. **Kullanıcılar Uygulamalarım için Önizleme özelliklerini kullanabilir**, aşağıdaki seçeneklerden birini belirleyin:
   * **Selected** -belirli bir grup için özellikleri etkinleştirilir. Özelliklerini etkinleştirmek istediğiniz grubu seçmek için **Grup Seç** seçeneğini kullanın.  
   * **All** -tüm kullanıcılar için özellikleri etkinleştirilir.

> [!NOTE]
> Uygulamalar portalı 'nı açmak için, kullanıcılar bağlantısını `https://myapps.microsoft.com` veya kuruluşunuzun gibi özelleştirilmiş bağlantıyı kullanabilir (örneğin,) `https://myapps.microsoft.com/contoso.com` . Yeni uygulamalarım deneyimini etkinleştirdikten sonra, **güncelleştirilmiş uygulamalar deneyimimin kullanılabilir olduğu** başlık, uygulamalar sayfasının en üstünde görüntülenir ve kullanıcılar yeni deneyimi görüntülemek için **deneyin** seçeneğini belirleyebilir. Kullanıcılar yeni deneyimi kullanmayı durdurmak için sayfanın üst kısmındaki **yeni deneyim dışında bırak** başlığından **Evet** ' i seçebiliyor.

## <a name="create-a-collection"></a>Koleksiyon oluşturma

Bir koleksiyon oluşturmak için bir Azure AD Premium P1 veya P2 lisansına sahip olmanız gerekir.

1. [**Azure Portal**](https://portal.azure.com/) açın ve Azure AD Premium P1 veya P2 lisansıyla yönetici olarak oturum açın.

2. **Azure Active Directory**  >  **kurumsal uygulamalara** gidin.

3. **Yönet** altında **koleksiyonlar**' ı seçin.

4. **Yeni koleksiyon**' ı seçin. **Yeni koleksiyon** sayfasında, koleksiyon Için bir **ad** girin (adında "koleksiyon" kullanmaktan önermiyoruz. Sonra bir **Açıklama** girin.

   ![Yeni koleksiyon sayfası](media/acces-panel-collections/new-collection.png)

5. **Uygulamalar** sekmesini seçin. **+ Uygulama** Ekle ' yi seçin ve ardından uygulama **Ekle** sayfasında, koleksiyona eklemek istediğiniz tüm uygulamaları seçin ya da uygulamaları bulmak için **arama** kutusunu kullanın.

   ![Koleksiyona uygulama ekleme](media/acces-panel-collections/add-applications.png)

6. Uygulama ekleme işiniz bittiğinde **Ekle**' yi seçin. Seçilen uygulamaların listesi görüntülenir. Listedeki uygulamaların sırasını değiştirmek için yukarı oklarını kullanabilirsiniz. Bir uygulamayı aşağı taşımak veya koleksiyondan silmek için **diğer** menüyü (**...**) seçin.

7. **Sahipler** sekmesini seçin. **+ Kullanıcı ve** Grup Ekle ' yi seçin ve ardından **Kullanıcı ve Grup Ekle** sayfasında, sahipliği atamak istediğiniz kullanıcıları veya grupları seçin. Kullanıcılar ve gruplar seçimini tamamladıktan sonra **Seç**' i seçin.

9. **Kullanıcılar ve gruplar** sekmesini seçin. **+ Kullanıcı ve** Grup Ekle ' yi seçin ve ardından **Kullanıcı ve Grup Ekle** sayfasında, koleksiyonu atamak istediğiniz kullanıcıları veya grupları seçin. Ya da kullanıcıları veya grupları bulmak için **arama** kutusunu kullanın. Kullanıcılar ve gruplar seçimini tamamladıktan sonra **Seç**' i seçin.

   ![Kullanıcı ve Grup Ekle](media/acces-panel-collections/add-users-and-groups.png)

11. **Gözden geçir + Oluştur**’u seçin. Yeni koleksiyonun özellikleri görüntülenir.


## <a name="view-audit-logs"></a>Denetim günlüklerini görüntüleme

Denetim günlükleri, koleksiyon oluşturma Son Kullanıcı eylemleri de dahil olmak üzere uygulamalarımı kaydet işlemlerini kaydeder. Uygulamalarımın aşağıdaki olayları oluşturulmuştur:

* Koleksiyon Oluştur
* Koleksiyonu düzenle
* Koleksiyonu silme
* Uygulama başlatma (Son Kullanıcı)
* Self Servis uygulama ekleme (Son Kullanıcı)
* Self Servis uygulama silme (Son Kullanıcı)

[](https://portal.azure.com) Etkinlik bölümündeki **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **Denetim günlükleri** ' ni seçerek Azure Portal denetim günlüklerine erişebilirsiniz. **Hizmet** Için **uygulamalarım**' ı seçin.

## <a name="get-support-for-my-account-pages"></a>Hesap Sayfalarım için destek alın

Uygulamalarım sayfasında, bir Kullanıcı hesap ayarlarını açmak için **Hesabım görünümmi seçip Hesabım** seçeneğini belirleyebilir  >   . Azure **ad Hesabım** sayfasında, kullanıcılar güvenlik bilgilerini, cihazlarını, parolalarını ve daha fazlasını yönetebilir. Office hesap ayarlarına de erişebilirler.

Azure AD hesabı sayfası veya Office hesabı sayfasında bir sorun için destek isteği göndermeniz gerekiyorsa, isteğiniz doğru şekilde yönlendirilmek için aşağıdaki adımları izleyin: 

* **Azure AD "Hesabım"** sayfasındaki sorunlar için Azure Portal içinden bir destek isteği açın.   >    >  **Yeni destek isteği** Azure Active Directory Azure Portal gidin.

* **Office "Hesabım"** sayfasındaki sorunlar için Microsoft 365 Yönetim Merkezi içinden bir destek isteği açın. **Microsoft 365 Yönetim Merkezi**  >  **desteği**' ne gidin. 

## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory uygulamalar için son kullanıcı deneyimleri](end-user-experiences.md)