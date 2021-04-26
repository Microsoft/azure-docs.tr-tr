---
title: Azure faturalama hesabının kişi bilgilerini değiştirme
description: Azure faturalama hesabınızın kişi bilgilerini değiştirme işlemi açıklanır
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/08/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: f394b6b44b2030253f7b78ec68459819c82c3c27
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480911"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Azure faturalama hesabının kişi bilgilerini değiştirme

Bu makale, Azure portalda *faturalama hesabınıza* ilişkin kişi bilgilerini güncelleştirmenize yardımcı olur. İlgili kişi bilgilerini güncelleştirme yönergeleri ödeme hesabı türüne göre farklılık gösterir. Ödeme hesapları hakkında daha fazla bilgi edinmek ve ödeme hesabınızın türünü belirlemek için bkz. [Azure portalında ödeme hesaplarını görüntüleme](view-all-accounts.md). Azure faturalama hesabı Azure kullanıcı hesabınızdan ve [Microsoft hesabınızdan](https://account.microsoft.com/) ayrı bir hesaptır.

Azure Active Directory kullanıcı profili bilgilerinizi güncelleştirmek istiyorsanız, bu değişiklikleri yalnızca kullanıcı yöneticisi yapabilir. Size kullanıcı yöneticisi rolü atanmadıysa, kullanıcı yöneticinize başvurun. Kullanıcının profilini değiştirme hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak kullanıcının profil bilgilerini ekleme veya güncelleştirme](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Satılan adres* - Satılan adres, kuruluşun veya bir ödeme hesabından sorumlu kişinin adresi ve iletişim bilgileridir. Ödeme hesabı için oluşturulan tüm faturalarda gösterilir.

*Fatura adresi* - Fatura adresi, kuruluşun veya bir ödeme hesabı için oluşturulan faturalardan sorumlu kişinin adresi ve iletişim bilgileridir. Bir Microsoft Çevrimiçi Hizmet Programı (MOSP) için ödeme hesabının, hesap için oluşturulan tüm faturalarda görüntülenen bir fatura adresi vardır. Bir Microsoft Müşteri Sözleşmesi (MCA) için ödeme hesabının, her faturalama profili için bir fatura adresi vardır ve bu, faturalama profili için oluşturulan faturada gösterilir.

*Hizmet ve pazarlama e-postaları için iletişim e-posta adresi*: Azure hesabınız hakkında önemli faturalandırma, hizmet ve önerilerle ilgili bildirimleri almak için, oturum açarken kullandığınız e-posta adresinden farklı bir e-posta adresi belirtebilirsiniz. Acil güvenlik sorunları, fiyat değişiklikleri veya hesabınız tarafından kullanılan hizmetlerdeki hataya neden olan değişiklikler gibi hizmet bildirimi e-postaları her zaman oturum açma adresinize gönderilir.

## <a name="update-an-mosp-billing-account-address"></a>MOSP faturalama hesabı adresini güncelleştirme

1. Hesapta hesap yöneticisi izni olan e-posta adresini kullanarak Azure portalında oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.  
    ![Azure portalda maliyet yönetimi + faturalamanın nerede aranacağını gösteren ekran görüntüsü](./media/change-azure-account-profile/search-cmb.png)
1. Sol taraftan **Özellikler**’i seçin.  
    ![MOSP faturalama hesabı özelliklerini gösteren ekran görüntüsü](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Satılan ve fatura adresi adreslerini güncelleştirmek için **Fatura adresini güncelleştir**’i seçin. Yeni adresi girin ve **Kaydet**’i seçin.  
    ![MSOP faturalama hesabı için adres güncelleştirme işlemini gösteren ekran görüntüsü](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Bir MCA ödeme hesabının satılan adresini güncelleştirme

1. Microsoft Müşteri Sözleşmesi için ödeme hesabında sahip veya katkıda bulunan rolü olan e-posta adresini kullanarak Azure portalında oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.  
    ![Azure portalda nerede aranacağını gösteren ekran görüntüsü](./media/change-azure-account-profile/search-cmb.png)
1. Sol taraftan **Özellikler**'i ve ardından **Satılan adresi güncelleştir**'i seçin.  
    ![Satılan adresin değiştirilebileceği MCA faturalama hesabının özelliklerini gösteren ekran görüntüsü](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Yeni adresi girin ve **Kaydet**’i seçin.  
    ![MCA hesabı için satılan adresinin güncelleştirilmesini gösteren ekran görüntüsü](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Bazı hesaplar, satılan adresleri güncelleştirilmeden önce ek doğrulama gerektirir. Hesabınız el ile onay gerektiriyorsa Azure desteğine başvurmanız istenir.

## <a name="update-an-mca-billing-account-address"></a>MCA ödeme hesabı adresini güncelleştirme

1. MCA için bir ödeme hesabı veya faturalama profili üzerinde sahip veya katkıda bulunan rolü olan e-posta adresini kullanarak Azure portalında oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.  
1. Soldaki **Faturalama profilleri** seçeneğini belirleyin.
1. Fatura adresini güncelleştirmek için bir faturalama profili seçin.  
    ![Faturalama profilini seçtiğiniz Faturalama profilleri sayfasını gösteren ekran görüntüsü](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Sol taraftan **Özellikler**’i seçin.
1. **Adresi güncelleştir**’i seçin.  
    ![Adresin nerede güncelleştirileceğini gösteren ekran görüntüsü](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Yeni adresi girin ve **Kaydet**’i seçin.  
    ![Adresi güncelleştirmeyi gösteren ekran görüntüsü](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="update-a-po-number"></a>PO numarasını güncelleştirme

Varsayılan olarak, fatura profili için bir faturanın ilişkili bir PO numarası yoktur. Faturalandırma profili için bir PO numarası ekledikten sonra, fatura profili için faturalarda görüntülenir.

Bir faturalandırma profili için PO numarası eklemek veya değiştirmek için aşağıdaki adımları kullanın.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Maliyet yönetimi + faturalandırma** ' i arayın ve ardından **faturalandırma kapsamları**' nı seçin.
1. Faturalandırma kapsamınızı seçin.
1. **Faturalama** altındaki sol menüde **faturalandırma profilleri**' ni seçin.
1. Uygun faturalandırma profilini seçin.
1. **Ayarlar** altındaki sol menüde **Özellikler**' i seçin.
1. **Po numarasını güncelleştir**' i seçin.
1. Bir PO numarası girip **Güncelleştir**' i seçin.

## <a name="service-and-marketing-emails"></a>Hizmet ve pazarlama e-postaları

Azure portalında, 90 günde bir e-posta adresinizi doğrulamanız veya güncelleştirmeniz istenir. Microsoft, bu e-posta adresine aşağıdakiler için Azure hesabıyla ilgili bilgileri içeren e-postalar gönderir:

- Hizmet bildirimleri
- Güvenlik uyarıları
- Faturalama amaçları
- Destek
- Pazarlama iletişimleri
- Azure kullanımınıza göre en iyi deneyim önerileri

Hesabınızla ilgili iletişimleri almak istediğiniz e-posta adresini girin. Bir e-posta adresi girerek, Microsoft’tan gelen iletişimleri almayı kabul etmiş olursunuz.

![İletişim bilgilerinizi güncelleştirme istemi örneği](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>İletişim e-posta adresinizi değiştirme

Aşağıdaki yöntemlerden birini kullanarak iletişim e-posta adresinizi değiştirebilirsiniz. İletişim e-posta adresinizi güncelleştirdiğinizde, oturum açmak için kullandığınız e-posta adresi güncelleştirilmez.

1. Bir MOSP hesabı için hesap yöneticisiyseniz, [MOSP faturalama hesabı adresini güncelleştirme](#update-an-mosp-billing-account-address) bölümündeki yönergeleri izleyin ve son adımda **Kişi bilgilerini güncelleştir**’i seçin. Ardından yeni e-posta adresini girin.
1. Azure portalında [İletişim bilgileri](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) alanına gidin ve yeni e-posta adresini girin. 
1. Azure portalında, adınızın baş harflerini veya resminizi içeren simgeyi seçin. Ardından açılır menüyü ( **...** ) seçin. Sonra menüden **İletişim Bilgilerim**’i seçin ve yeni e-posta adresini girin.

![Azure’da e-posta adresini güncelleştirme örneği](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Pazarlama e-postalarını geri çevirme

Pazarlama e-postalarını almayı geri çevirmek için:

1. Profil e-posta adresinizi kullanarak bir istek göndermek için [istek formuna](https://account.microsoft.com/profile/permissions-link-request) gidin. Tercihlerinizi güncelleştirmek için e-postayla bir bağlantı alırsınız.
1. **İletişim izinlerini yönetme** sayfasını açmak için bağlantıyı seçin. Bu sayfada, e-posta adresinin kabul edildiği pazarlama iletişimi türleri gösterilir. Geri çevirmek istediğiniz seçimlerin işaretini kaldırın ve sonra **Kaydet**’i seçin.  
    ![İletişim izinlerini yönetme sayfası örneği](./media/change-azure-account-profile/manage-communication-permissions.png)

Pazarlama iletişimlerini geri çevirdiğinizde, hesabınıza göre hizmet bildirimleri almaya devam edersiniz.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Oturum açmak için kullandığınız e-posta adresini güncelleştirme

Hesabınıza erişmek için kullandığınız e-posta adresini güncelleştiremezsiniz. Ancak, bir MOSP için ödeme hesabınız varsa, yeni e-posta adresini kullanarak başka bir hesaba kaydolabilir ve aboneliklerinizin sahipliğini yeni hesaba aktarabilirsiniz. Bir MCA ödeme hesabı için, hesabınızda yeni e-posta adresine izin verebilirsiniz.

## <a name="update-your-credit-card"></a>Kredi kartınızı güncelleştirme

Kredi kartınızı nasıl güncelleştirebileceğinizi öğrenmek için bkz. [Azure aboneliği ödemesi için kullanılan kredi kartını değiştirme](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Ülkenizi veya bölgenizi güncelleştirme

Mevcut bir hesabın ülkesini veya bölgesini değiştirme desteklenmiyor. Ancak, farklı bir ülkede veya bölgede yeni bir hesap oluşturabilir ve ardından aboneliğinizi yeni hesaba aktarmak için Azure desteğine başvurabilirsiniz.

## <a name="change-the-subscription-name"></a>Abonelik adını değiştirme

1. Azure portalda oturum açın, sol bölmeden **Abonelik**’i seçin ve sonra yeniden adlandırmak istediğiniz aboneliği seçin.
1. **Genel Bakış**’ı seçin ve sonra komut çubuğundan **Yeniden Adlandır**’ı seçin.  
    ![Azure aboneliğini yeniden adlandırma örneği](./media/change-azure-account-profile/rename-sub.png)
1. Adı değiştirdikten sonra **Kaydet**’i seçin.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- [Ödeme hesaplarınızı görüntüleme](view-all-accounts.md)
