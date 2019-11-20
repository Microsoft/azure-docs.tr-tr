---
title: Azure AD Yetkilendirme Yönetimi 'nde kaynakların kataloğunu oluşturma ve yönetme-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetiminde yeni bir kaynak kapsayıcısı oluşturmayı ve paketlerin nasıl erişebileceğini öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66e3dee83ccf4a857fda9d5804848108c0cc251
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174431"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde kaynakların kataloğunu oluşturma ve yönetme

## <a name="create-a-catalog"></a>Katalog oluşturma

Katalog, kaynak ve erişim paketlerinin bir kapsayıcısıdır. İlgili kaynakları ve erişim paketlerini gruplamak istediğinizde bir katalog oluşturursunuz. Her koşulda Katalog, ilk katalog sahibi olur. Katalog sahibi, ek Katalog sahipleri ekleyebilir.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi veya katalog Oluşturucu

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar**' a tıklayın.

    ![Azure portal 'de Yetkilendirme Yönetimi katalogları](./media/entitlement-management-catalog-create/catalogs.png)

1. **Yeni Katalog**' a tıklayın.

1. Katalog için benzersiz bir ad girin ve bir açıklama sağlayın.

    Kullanıcılar bu bilgileri bir erişim paketinin ayrıntılarında görür.

1. Bu katalogdaki erişim paketlerinin kullanıcıların, oluşturulduktan hemen sonra istemesi için kullanılabilir olmasını istiyorsanız, **etkin** ' i **Evet**olarak ayarlayın.

1. Seçili dış dizinlerde bulunan kullanıcıların bu katalogda erişim paketleri isteyebilmesini sağlamak istiyorsanız, **dış kullanıcılar Için etkin** ayarını **Evet**olarak belirleyin.

    ![Yeni Katalog bölmesi](./media/entitlement-management-shared/new-catalog.png)

1. Kataloğu oluşturmak için **Oluştur** ' a tıklayın.

## <a name="add-resources-to-a-catalog"></a>Kataloğa kaynak ekleme

Kaynakları bir erişim paketine dahil etmek için, kaynakların bir katalogda mevcut olması gerekir. Ekleyebileceğiniz kaynak türleri gruplar, uygulamalar ve SharePoint Online siteleridir. Gruplar, bulutta oluşturulmuş Office 365 grupları veya bulutta oluşturulmuş Azure AD güvenlik grupları olabilir. Uygulamalar hem SaaS uygulamaları hem de Azure AD 'ye federe olan uygulamalar dahil olmak üzere Azure AD kurumsal uygulamaları olabilir. Siteler SharePoint Online siteleri veya SharePoint Online site koleksiyonları olabilir.

**Önkoşul rolü:** [Kataloğa kaynak eklemek Için gerekli rollere](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) bakın

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve ardından kaynak eklemek istediğiniz kataloğu açın.

1. Sol taraftaki menüden **kaynaklar**' a tıklayın.

1. **Kaynak Ekle**' ye tıklayın.

1. Kaynak türüne tıklayın: **gruplar ve takımlar**, **uygulamalar**veya **SharePoint siteleri**.

    Eklemek istediğiniz bir kaynağı görmüyorsanız veya kaynak ekleyemezseniz, gerekli Azure AD dizin rolüne ve Yetkilendirme Yönetimi rolüne sahip olduğunuzdan emin olun. Gerekli rollere sahip bir kişiye kaynağı kataloğunuza eklemeniz gerekebilir. Daha fazla bilgi için bkz. [bir kataloğa kaynak eklemek Için gerekli roller](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Kataloğa eklemek istediğiniz bir veya daha fazla kaynak türünü seçin.

    ![Kataloğa kaynak ekleme](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. İşiniz bittiğinde **Ekle**' ye tıklayın.

    Bu kaynaklar artık kataloğun içindeki erişim paketlerine dahil edilebilir.

## <a name="remove-resources-from-a-catalog"></a>Katalogdan kaynakları kaldırma

Bir katalogdan kaynakları kaldırabilirsiniz. Bir kaynak, kataloğun erişim paketlerinde kullanılmıyorsa bir katalogdan kaldırılabilir,.

**Önkoşul rolü:** [Kataloğa kaynak eklemek Için gerekli rollere](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) bakın

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve kaynakları kaldırmak istediğiniz kataloğu açın.

1. Sol taraftaki menüden **kaynaklar**' a tıklayın.

1. Kaldırmak istediğiniz kaynakları seçin.

1. **Kaldır** ' a tıklayın (veya üç nokta ( **...** ) simgesine tıklayın ve ardından **kaynağı kaldır**' a tıklayın.

## <a name="add-additional-catalog-owners"></a>Ek katalog sahipleri ekleme

Katalog oluşturan kullanıcı ilk katalog sahibi olur. Bir kataloğun yönetimini devretmek için, Katalog sahibi rolüne kullanıcılar eklersiniz. Bu, katalog yönetimi sorumluluklarını paylaşmanıza yardımcı olur. 

Bir kullanıcıyı Katalog sahibi rolüne atamak için aşağıdaki adımları izleyin:

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi veya katalog sahibi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve ardından Yöneticiler eklemek istediğiniz kataloğu açın.

1. Sol taraftaki menüden **Roller ve yöneticiler**' e tıklayın.

    ![Rolleri ve yöneticileri kataloglandırır](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Bu rollerin üyelerini seçmek için **sahip Ekle** ' ye tıklayın.

1. Bu üyeleri eklemek için **Seç** ' e tıklayın.

## <a name="edit-a-catalog"></a>Katalog düzenleme

Bir kataloğun adını ve açıklamasını düzenleyebilirsiniz. Kullanıcılar bu bilgileri bir erişim paketinin ayrıntılarında görür.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi veya katalog sahibi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve ardından düzenlemek istediğiniz kataloğu açın.

1. Kataloğun **genel bakış** sayfasında, **Düzenle**' ye tıklayın.

1. Kataloğun adını, açıklamasını veya etkin ayarlarını düzenleyin.

    ![Katalog ayarlarını Düzenle](./media/entitlement-management-shared/catalog-edit.png)

1. **Kaydet** düğmesine tıklayın.

## <a name="delete-a-catalog"></a>Katalog silme

Bir kataloğu yalnızca herhangi bir erişim paketi yoksa silebilirsiniz.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi veya katalog sahibi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve ardından silmek istediğiniz kataloğu açın.

1. Kataloğun **genel bakış**sayfasında **Sil**' e tıklayın.

1. Görüntülenen ileti kutusunda **Evet**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Paket yöneticilerine erişim yönetimi yetkisi verme](entitlement-management-delegate-managers.md)
