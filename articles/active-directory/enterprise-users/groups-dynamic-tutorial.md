---
title: Dinamik bir gruba kullanıcı ekleme-öğretici-Azure AD | Microsoft Docs
description: Bu öğreticide kullanıcıları otomatik olarak eklemek veya kaldırmak için grupları ve kullanıcı üyeliği kurallarını kullanacaksınız
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d498fb4efb3de316981963ee9606255e5b1450a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056972"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Öğretici: Grup üyelerini otomatik olarak ekleme veya kaldırma

Azure Active Directory (Azure AD) içinde, otomatik olarak güvenlik gruplarına veya Microsoft 365 gruplara kullanıcı ekleyebilir veya kaldırabilirsiniz. böylece, her zaman el ile yapmanız gerekmez. Bir kullanıcının veya cihazın herhangi bir özelliği değiştiğinde Azure AD, değişikliğin üye eklemesi veya kaldırılması gerekip gerekmediğini görmek için Azure AD kuruluşunuzdaki tüm dinamik grup kurallarını değerlendirir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir iş ortağı şirketten otomatik olarak doldurulmuş bir konuk kullanıcıları grubu oluşturma
> * Gruba konuk kullanıcıların erişmesi için iş ortağına özgü özelliklere yönelik lisans atama
> * Bonus: Üye kullanıcılarınızın yalnızca içeriden erişilebilen sitelere erişimini yönetme gibi işlemler gerçekleştirmek için konuk kullanıcıları kaldırarak **Tüm kullanıcılar** grubunun güvenliğini sağlama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu özellik, kuruluşun genel yöneticisi olarak sizin için bir Azure AD Premium lisansı gerektirir. Bir tane yoksa, Azure AD 'de **Lisans**  >  **ürünlerini**  >  **dene/satın al**' ı seçin.

Dinamik gruplara üye olarak ekleyeceğiniz kullanıcılara lisans atamanıza gerek yoktur. Yalnızca kuruluştaki tüm kullanıcıları kapsayacak olan en düşük Azure AD Premium P1 lisans sayısına ihtiyaç duyarsınız. 

## <a name="create-a-group-of-guest-users"></a>Konuk kullanıcı grubu oluşturma

Öncelikle tamamı tek bir iş ortağı şirketinize ait olan konuk kullanıcılar için bir grup oluşturmanız gerekir. Bu kullanıcılar özel lisansa ihtiyaç duyduğundan bu amaca yönelik bir grup oluşturmak daha etkili olacaktır.

1. Azure portal ( https://portal.azure.com) kuruluşunuzun genel yöneticisi olan bir hesapla) oturum açın.
2. **Azure Active Directory**  >  **gruplar**  >  **Yeni Grup**' u seçin.
   ![Yeni bir grup başlatmak için komutu seçin](./media/groups-dynamic-tutorial/new-group.png)
3. **Grup** dikey penceresinde:
  
   * Grup türü olarak **güvenlik** ' i seçin.
   * `Guest users Contoso`Grubun adını ve açıklamasını girin.
   * **Üyelik türü** değerini **Dinamik Kullanıcı** olarak değiştirin.
   
4. **Sahipler** ' i seçin ve istediğiniz sahipleri bulmak Için **sahip Ekle** dikey penceresinde arama yapın. Seçime eklemek için istediğiniz sahibe tıklayın.
5. **Sahipler Ekle** dikey penceresini kapatmak için **Seç** ' e tıklayın.  
6. **Dinamik Kullanıcı üyeleri** kutusunda **dinamik sorguyu Düzenle** ' yi seçin.
7. **Dinamik üyelik kuralları** dikey penceresinde:

   * **Özellik** alanında, var olan değere tıklayın ve **UserType**' ı seçin. 
   * **İşleç** alanında **eşittir** seçili olduğunu doğrulayın.  
   * **Değer** alanını seçin ve **Konuk** yazın. 
   * Başka bir satır eklemek için **Ifade Ekle** Köprüsü ' ne tıklayın.
   * **Ve/veya** alanında, **ve**' ı seçin.
   * **Özellik** alanında **CompanyName**' i seçin.
   * **İşleç** alanında **eşittir** seçili olduğunu doğrulayın.
   * **Değer** alanına **contoso** girin.
   * **Dinamik üyelik kuralları** dikey penceresini kapatmak için **Kaydet** ' e tıklayın.
   
8. **Grup** dikey penceresinde **Oluştur**'u seçerek grubu oluşturun.

## <a name="assign-licenses"></a>Lisans atama

Yeni grubunuzu oluşturduğunuza göre bu iş ortağı kullanıcılarına ihtiyacı olan lisansları atayabilirsiniz.

1. Azure AD'de **Lisanslar**'ı seçin, bir veya daha fazla lisans seçin ve **Ata**'yı belirleyin.
2. **Kullanıcılar ve gruplar**'ı ve **Guest users Contoso** grubunu seçip değişikliklerinizi kaydedin.
3. **Atama seçenekleri**, seçtiğiniz lisanslarda bulunan hizmet planlarını açıp kapatmanızı sağlar. Değişiklik yaptığınızda **Tamam**'a tıklayarak kaydetmeyi unutmayın.
4. Atamayı tamamlamak için **Lisans ata** bölmesinin en altında bulunan **Ata**'ya tıklayın.

## <a name="remove-guests-from-all-users-group"></a>Tüm kullanıcılar grubundan konukları kaldırma

Yönetim planınız, tüm konuk kullanıcılarınızı şirketlerine göre gruplara ayırmak olabilir. Ayrıca, artık **tüm kullanıcılar** grubunu kuruluşunuzdaki üye kullanıcıları için ayrılmış olacak şekilde değiştirebilirsiniz. Ardından bu grubu kullanarak kuruluşunuza özgü uygulama ve lisans atamaları gerçekleştirebilirsiniz.

   ![Tüm kullanıcılar grubunu yalnızca üyeleri kapsayacak şekilde değiştirme](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

**Konuk kullanıcılar grubunu kaldırmak için**

1. Kuruluşunuzun genel yöneticisi olan bir hesapla [Azure Portal](https://portal.azure.com) oturum açın.
2. **Azure Active Directory**  >  **grupları**' nı seçin. **Guest users Contoso** grubunu, üç noktayı (...) ve ardından **Sil**'i seçin. Grubu sildiğinizde atanmış olan lisanslar kaldırılır.

**Tüm Kullanıcılar grubunu geri yüklemek için**
1. **Azure Active Directory**  >  **grupları**' nı seçin. Grubu açmak için **Tüm kullanıcılar** grubunun adını seçin.
1. **Dinamik üyelik kuralları**'nı seçin, kural metninin tamamını silin ve **Kaydet**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Konuk kullanıcı grubu oluşturma
> * Yeni grubunuza lisans atama
> * Tüm kullanıcılar grubunu yalnızca üyeleri kapsayacak şekilde değiştirme

Grup tabanlı lisanslama ile ilgili temel bilgiler için bir sonraki makaleye geçin
> [!div class="nextstepaction"]
> [Grup tabanlı lisanslama ile ilgili temel bilgiler](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



