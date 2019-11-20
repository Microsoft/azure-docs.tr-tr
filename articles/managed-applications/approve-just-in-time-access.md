---
title: Tam zamanında erişimi onayla-Azure yönetilen uygulaması
description: Azure yönetilen uygulamalar tüketicilerinin, yönetilen bir uygulamaya tam zamanında erişim isteklerini nasıl onaylayabileceğinizi açıklar.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: dd5c3f21b1a4b71d129ccbebeaa7ee66274a672f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529134"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Azure yönetilen uygulamalar için tam zamanında erişimi yapılandırma ve onaylama

Yönetilen bir uygulamanın tüketicisi olarak, yayımcı tarafından yönetilen kaynak grubuna kalıcı erişim verme konusunda rahatsızın. Azure yönetilen uygulamalar, yönetilen kaynaklara erişim verme konusunda daha fazla denetim sağlamak için, şu anda önizleme aşamasında olan tam zamanında (JıT) erişim adlı bir özellik sunar. Yayımcının kaynak grubuna ne kadar süreyle erişebileceğini onaylamanıza olanak sağlar. Yayımcı bu süre içinde gerekli güncelleştirmeleri yapabilir, ancak bu süre sona erdiğinde yayımcının erişiminin süresi dolar.

Erişim vermek için iş akışı:

1. Yayımcı, Market 'e yönetilen bir uygulama ekler ve JıT erişiminin kullanılabilir olduğunu belirtir.

1. Dağıtım sırasında, yönetilen uygulama örneğiniz için JıT erişimini etkinleştirirsiniz.

1. Dağıtımdan sonra, JıT erişimi için ayarları değiştirebilirsiniz.

1. Yayımcı erişim isteği gönderir.

1. İsteği onaylarsınız.

Bu makale, kullanıcılara JıT erişimini etkinleştirmek ve istekleri onaylamak için gereken eylemlere odaklanmaktadır. Yönetilen bir uygulamayı JıT erişimiyle yayımlama hakkında bilgi edinmek için bkz. [Azure yönetilen uygulamalarda tam zamanında erişim isteği](request-just-in-time-access.md).

> [!NOTE]
> Tam zamanında erişimi kullanmak için bir [Azure Active Directory P2 lisansınızın](../active-directory/privileged-identity-management/subscription-requirements.md)olması gerekir.

## <a name="enable-during-deployment"></a>Dağıtım sırasında etkinleştir

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Yönetilen bir uygulama için JıT özellikli bir market girişi bulun. **Oluştur**'u seçin.

1. Yeni yönetilen uygulama için değer sağlarken **JIT yapılandırma** adımı yönetilen uygulama için JIT erişimini etkinleştirmenizi veya devre dışı bırakmanızı sağlar. **JIT erişimini etkinleştirmek**için **Evet** ' i seçin. Bu seçenek, Market 'te JıT etkin ile tanımlanan yönetilen uygulamalar için varsayılan olarak seçilidir.

   ![Erişimi yapılandırma](./media/approve-just-in-time-access/configure-jit-access.png)

   Dağıtım sırasında yalnızca JıT erişimini etkinleştirebilirsiniz. **Hayır**' ı seçerseniz, yayımcı yönetilen kaynak grubuna kalıcı erişim alır. JıT erişimini daha sonra etkinleştiremezsiniz.

1. Varsayılan onay ayarlarını değiştirmek için **JIT yapılandırmasını Özelleştir**' i seçin.

   ![Erişimi Özelleştir](./media/approve-just-in-time-access/customize-jit-access.png)

   Varsayılan olarak, JıT özellikli bir yönetilen uygulama aşağıdaki ayarlara sahiptir:

   * Onay modu – otomatik
   * Maksimum erişim süresi – 8 saat
   * Onaylayanlar – yok

   Onay modu **Otomatik**olarak ayarlandığında, onaylayanlar her istek için bir bildirim alır, ancak istek otomatik olarak onaylanır. **El ile**olarak ayarlandığında, onaylayanlar her istek için bir bildirim alır ve bunlardan biri onu onaylaması gerekir.

   Etkinleştirme en uzun süre, bir yayımcının yönetilen kaynak grubuna erişim isteyemediği maksimum süreyi belirtir.

   Onaylayanlar listesi, JıT erişim isteklerini onaylayabilen Azure Active Directory kullanıcıdır. Onaylayan eklemek için **onaylayan Ekle** ve Kullanıcı ara ' yı seçin.

   Ayarı güncelleştirdikten sonra **Kaydet**' i seçin.

## <a name="update-after-deployment"></a>Dağıtımdan sonra Güncelleştir

İsteklerin nasıl onaylandığını gösteren değerleri değiştirebilirsiniz. Ancak, dağıtım sırasında JıT erişimini etkinleştirmediyseniz, daha sonra etkinleştiremezsiniz.

Dağıtılmış bir yönetilen uygulamanın ayarlarını değiştirmek için:

1. Portalda, uygulamayı Yönet ' i seçin.

1. **JIT yapılandırması** ' nı seçin ve ayarları gerektiği gibi değiştirin.

   ![Erişim ayarlarını değiştir](./media/approve-just-in-time-access/change-settings.png)

1. İşiniz bittiğinde **Kaydet**’i seçin.

## <a name="approve-requests"></a>İstekleri onaylama

Yayımcı erişim istediğinde istek size bildirilir. Doğrudan yönetilen uygulama aracılığıyla veya Azure AD Privileged Identity Management hizmeti aracılığıyla tüm yönetilen uygulamalarda JıT erişim isteklerini onaylayabilirsiniz. Tam zamanında erişimi kullanmak için bir [Azure Active Directory P2 lisansınızın](../active-directory/privileged-identity-management/subscription-requirements.md)olması gerekir.

Yönetilen uygulama aracılığıyla istekleri onaylamak için:

1. Yönetilen uygulama için **JIT erişimi** ' ni seçin ve **istekleri Onayla**' yı seçin.

   ![İstekleri onaylama](./media/approve-just-in-time-access/approve-requests.png)
 
1. Onaylanacak isteği seçin.

   ![İstek Seç](./media/approve-just-in-time-access/select-request.png)

1. Formunda onay nedenini belirtin ve **Onayla**' yı seçin.

İstekleri Azure AD Privileged Identity Management ile onaylamak için:

1. **Tüm hizmetler** ' i seçin ve **Azure AD Privileged Identity Management**aramaya başlayın. Kullanılabilir seçeneklerden seçin.

   ![Hizmet ara](./media/approve-just-in-time-access/search.png)

1. **Istekleri Onayla**' yı seçin.

   ![İstekleri Onayla seçeneğini belirleyin](./media/approve-just-in-time-access/select-approve-requests.png)

1. **Azure yönetilen uygulamalar**' ı seçin ve onaylanacak isteği seçin.

   ![İstekleri seçin](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Sonraki adımlar

Yönetilen bir uygulamayı JıT erişimiyle yayımlama hakkında bilgi edinmek için bkz. [Azure yönetilen uygulamalarda tam zamanında erişim isteği](request-just-in-time-access.md).
