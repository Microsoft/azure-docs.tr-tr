---
title: Grup süre sonu ilkesi hızlı başlangıç-Azure AD | Microsoft Docs
description: Microsoft 365 grupları için süre sonu-Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4eeaa98cb19b119e4da2af7bee39b5aebd303485
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063160"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Hızlı başlangıç: Azure Active Directory Microsoft 365 grupları sona ermek üzere ayarla

Bu hızlı başlangıçta, Microsoft 365 gruplarınız için süre sonu ilkesini ayarlarsınız. Kullanıcılar kendi gruplarını oluşturma iznine sahip olduğunda, kullanılmayan grupların sayısı artabilir. Kullanılmayan grupları yönetmenin bir yolu, bu grupların için sona erme ilkesi ayarlayarak grupları el ile silme zahmetini ortadan kaldırmaktır.

Süre sonu ilkesi oldukça basittir:

- Kullanıcı etkinlikleri olan gruplar, süre sonu yaklaştığında otomatik olarak yenilenir
- Grup sahiplerine süresi dolan bir grupla ilgili yenileme bildirimi gönderilir
- Yenilenmeyen gruplar silinir
- Silinen bir Microsoft 365 Grubu, bir grup sahibine veya bir Azure AD yöneticisi tarafından 30 gün içinde geri yüklenebilir

> [!NOTE]
> Gruplar artık, son kullanımda olup olmadığına göre otomatik olarak yenilenmekte olan Azure AD zekasını kullanır. Bu yenileme kararı Outlook, SharePoint, takımlar, Yammer ve diğerleri gibi Microsoft 365 hizmetler genelinde gruplar halinde Kullanıcı etkinliklerini temel alır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Önkoşul

 Grup süre sonunu ayarlamak için gereken en az ayrıcalıklı rol, kuruluştaki Kullanıcı yöneticisidir.

## <a name="turn-on-user-creation-for-groups"></a>Kullanıcılar için grup oluşturma özelliğini açma

1. [Azure Portal](https://portal.azure.com) bir kullanıcı yönetici hesabıyla oturum açın.

2. **Gruplar**'ı ve ardından **Genel**'i seçin.
  
   ![Self servis grup ayarları sayfası](./media/groups-quickstart-expiration/self-service-settings.png)

3. Kullanıcıları, **Evet**'e **Microsoft 365 gruplar oluşturabilir** .

4. İşiniz tamamlandığında grup ayarlarını kaydetmek için **Kaydet**'i seçin.

## <a name="set-group-expiration"></a>Grup süre sonunu ayarlama

1. [Azure Portal](https://portal.azure.com)oturum açın, süre sonu   >    >  ayarlarını açmak için Azure Active Directory grupları **süre sonu** ' nu seçin.
  
   ![Grup için süre sonu ayarları sayfası](./media/groups-quickstart-expiration/expiration-settings.png)

2. Süre sonu aralığını ayarlayın. Önceden belirlenmiş değerlerden birini seçin veya 31 günden yüksek bir değer girin. 

3. Sahibi olmayan gruplar için sona erme bildirimlerinin gönderileceği e-posta adresini girin.

4. Bu hızlı başlangıç için, **bu Microsoft 365 gruplarının tümünün kullanım süresini etkinleştir** olarak ayarlayın. 

5. İşiniz tamamlandığında süre sonu ayarlarını kaydetmek için **Kaydet**'i seçin.

İşte bu kadar! Bu hızlı başlangıçta, seçili Microsoft 365 grupları için süre sonu ilkesini başarıyla ayarlarsınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="to-remove-the-expiration-policy"></a>Süre sonu ilkesi kaldırmak için

1. Azure AD kuruluşunuzun genel yöneticisi olan bir hesapla [Azure Portal](https://portal.azure.com) oturum açtığınızdan emin olun.
2. **Azure Active Directory**  >  **grupları**  >  **süre sonu**' nu seçin.
3. **Bu Microsoft 365 grupları için süre sonunu etkinleştir** ' i **hiçbiri** olarak ayarlayın.

### <a name="to-turn-off-user-creation-for-groups"></a>Kullanıcılar için grup oluşturma özelliğini kapatmak için

1. **Azure Active Directory**  >  **grupları**  >  **genel**' i seçin. 
2. **Kullanıcılar, Azure portalları 'Nda Hayır olarak Microsoft 365 grupları oluşturabilir** . 

## <a name="next-steps"></a>Sonraki adımlar

PowerShell yönergelerini ve teknik kısıtlamaları içeren süre sonu hakkında daha fazla bilgi için aşağıdaki makaleye bakın:

> [!div class="nextstepaction"]
> [Sona erme ilkesi PowerShell](groups-lifecycle.md)
