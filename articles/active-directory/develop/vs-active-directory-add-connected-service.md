---
title: Bağlı hizmetleri kullanarak bir Azure Active Directory ekleme | Mavisi
description: Visual Studio bağlı hizmetler Ekle iletişim kutusunu kullanarak Azure Active Directory ekleme
author: ghogen
manager: jillfra
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36827401060e330d57b6afac3db0abbde0c27dc2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73941938"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Visual Studio 'da bağlı hizmetleri kullanarak Azure Active Directory ekleme

Azure Active Directory (Azure AD) kullanarak, ASP.NET MVC web uygulamaları için çoklu oturum açma (SSO) veya Web API hizmetlerinde Active Directory kimlik doğrulama desteği sağlayabilirsiniz. Azure AD kimlik doğrulaması ile kullanıcılarınız, Web uygulamalarınıza bağlanmak için Azure Active Directory hesaplarını kullanabilir. Web API 'si ile Azure AD kimlik doğrulamasının avantajları, bir Web uygulamasından API 'YI kullanıma sunmadan gelişmiş veri güvenliği içerir. Azure AD ile ayrı bir kimlik doğrulama sistemini kendi hesabı ve Kullanıcı yönetimi ile yönetmeniz gerekmez.

Bu makale ve yardımcı makaleler, Active Directory için Visual Studio bağlı hizmet özelliğini kullanma hakkında ayrıntılı bilgi sağlar. Özelliği Visual Studio 2015 ve üzeri sürümlerde kullanılabilir.

Active Directory bağlı hizmet, ASP.NET Core uygulamalarını desteklemez.

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı: bir Azure hesabınız yoksa, [ücretsiz deneme için kaydolabilir](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) veya [Visual Studio abone avantajlarınızı etkinleştirebilirsiniz](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** veya üzeri. [Visual Studio 'yu şimdi indirin](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Bağlı hizmetler iletişim kutusunu kullanarak Azure Active Directory bağlanma

1. Visual Studio 'da bir ASP.NET MVC projesi veya bir ASP.NET Web API projesi oluşturun veya açın. MVC, Web API, tek sayfalı uygulama, Azure API uygulaması, Azure mobil uygulaması ve Azure mobil hizmet şablonlarını kullanabilirsiniz.

1. **Project > bağlı hizmet ekle...** menü komutunu seçin veya Çözüm Gezgini içindeki proje altında bulunan **bağlı hizmetler** düğümüne çift tıklayın.

1. **Bağlı hizmetler** sayfasında, **Azure Active Directory kimlik doğrulaması**' nı seçin.

    ![Bağlı hizmetler sayfası](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. **Giriş** sayfasında, **İleri**' yi seçin. Bu sayfada hata görürseniz, [Azure Active Directory bağlı hizmeti ile hataları tanılama](vs-active-directory-error.md)bölümüne bakın.

    ![Giriş sayfası](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. **Çoklu oturum açma** sayfasında, **etki alanı** açılır listesinden bir etki alanı seçin. Liste, Visual Studio 'nun hesap ayarları iletişim kutusunda listelenen hesaplar tarafından erişilebilen tüm etki alanlarını içerir (**dosya > hesap ayarları..** .). Alternatif olarak, aradığınızı bulamazsanız, `mydomain.onmicrosoft.com`gibi bir etki alanı adı girebilirsiniz. Azure Active Directory bir uygulama oluşturma veya var olan bir Azure Active Directory uygulamasındaki ayarları kullanma seçeneğini belirleyebilirsiniz. Bittiğinde **İleri ' yi** seçin.

    ![Çoklu oturum açma sayfası](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. **Dizin erişimi** sayfasında, istediğiniz şekilde **Dizin verilerini oku** seçeneğini belirleyin. Geliştiriciler genellikle bu seçeneği içerir.

    ![Dizin erişimi sayfası](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Azure AD kimlik doğrulamasını etkinleştirmek üzere projenizde değişiklikler başlatmak için **son** ' u seçin. Visual Studio şu süre boyunca ilerleme durumunu gösterir:

    ![Bağlı hizmet ilerlemesini Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. İşlem tamamlandığında, Visual Studio tarayıcınızı proje türüne uygun şekilde aşağıdaki makalelerden birine açar:

    - [.NET MVC projelerini kullanmaya başlama](vs-active-directory-dotnet-getting-started.md)
    - [.NET WebAPI projelerini kullanmaya başlama](vs-active-directory-webapi-getting-started.md)

1. Ayrıca, [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)Active Directory etki alanını da görebilirsiniz.

## <a name="how-your-project-is-modified"></a>Projenizi nasıl değiştirilir

Bağlı hizmeti sihirbaza eklediğinizde, Visual Studio projenize Azure Active Directory ve ilişkili başvuruları ekler. Projenizdeki yapılandırma dosyaları ve kod dosyaları da Azure AD desteği eklemek için değiştirilir. Visual Studio 'Nun yaptığı belirli değişiklikler proje türüne bağlıdır. Ayrıntılar için aşağıdaki makalelere bakın:

- [.NET MVC projeme ne oldu?](vs-active-directory-dotnet-what-happened.md)
- [Web API projem ne oldu?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory için kimlik doğrulama senaryoları](authentication-scenarios.md)
- [Microsoft 'a bir ASP.NET Web uygulamasına oturum açma ekleme](quickstart-v1-aspnet-webapp.md)
