---
title: Bir buluta yönelik bulutu yönetme-Azure iş ortağı çözümleri
description: Bu makalede, Azure portal bir uyumlu bulutun yönetimi açıklanmaktadır. Çoklu oturum açmayı ayarlama, bir veya daha fazla kuruluşu silme ve destek alma.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989146"
---
# <a name="manage-the-confluent-cloud-resource"></a>Konluent bulut kaynağını yönetme

Bu makalede, Azure 'da bulunan confluent bulutu için Apache Kafka örneğinizi nasıl yöneteceğiniz açıklanmaktadır. Çoklu oturum açma (SSO) ayarlama, bir, bir kuruluşun bir kuruluşu silme ve bir destek isteği oluşturma işlemlerinin nasıl yapılacağını gösterir.

## <a name="single-sign-on"></a>Çoklu oturum açma

Kuruluşunuz için SSO 'yu uygulamak üzere, kiracı yöneticiniz Galeri uygulamasını içeri aktarabilir. Bu adım isteğe bağlıdır. Bir uygulamayı içeri aktarma hakkında bilgi için bkz. [hızlı başlangıç: Azure Active Directory (Azure AD) kiracınıza uygulama ekleme](../../active-directory/manage-apps/add-application-portal.md). Kiracı Yöneticisi uygulamayı içeri aktardığında, kullanıcıların, kesin bir bulut portalı için erişime izin vermeyi açıkça kabul etme gereksinimi yoktur.

SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Konluent bulut kaynağı örneğiniz için **genel bakışa** gidin.
1. **Konluent bulutu üzerinde yönetilecek** bağlantıyı seçin.

   :::image type="content" source="media/sso-link.png" alt-text="Tam Portal çoklu oturum açma.":::

1. Kiracı Yöneticisi, SSO onayı için Galeri uygulamasını içeri aktarmadıysa, izin ve izin verin. Bu adım yalnızca, bağlantıyı yalnızca bir **bulutta yönetmek** için bağlantıya ilk kez eriştiğinizde gereklidir.
1. Bu bulut portalında çoklu oturum açma için bir Azure AD hesabı seçin.
1. Onay sağlandığında, confluent bulut portalına yönlendirilirsiniz.

## <a name="set-up-cluster"></a>Küme ayarlama

Kümenizi ayarlama hakkında daha fazla bilgi için bkz. [confluent Cloud-confluent belgelerinde küme oluşturma](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Konluent organizasyonunu Sil

Artık sizin için bir bulut kaynağına ihtiyacınız kalmadığında, kaynağı Azure 'da ve confluent bulutu 'ndan silin.

Azure 'daki kaynakları silmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Tüm kaynaklar** ' ı seçin ve bu bulut kaynağının adına veya **kaynak türü** _confluent kuruluşa_ **göre filtreleyin** . Veya Azure portal, kaynak adını arayın.
1. Kaynağın **genel bakış** bölümünde **Sil**' i seçin.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Kaynak silme simgesi.":::

1. Silmeyi onaylamak için kaynak adını yazın ve **Sil**' i seçin.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Kaynak silinmesini onaylamak için istemde bulun.":::

Confluent bulutundaki kaynağı silmek için, [uyumlu bulut ortamları için belgelere bakın-confluent belgeleri](https://docs.confluent.io/current/cloud/using/environments.html) ve confluent [bulut temelleri-confluent belgeleri](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

Küme ve kümedeki tüm veriler kalıcı olarak silinir. Sözleşmeniz bir veri saklama yan tümcesi içeriyorsa, Confıluent, verileri [hizmet yapılandırmalı belgelerde](https://www.confluent.io/confluent-cloud-tos)belirtilen zaman aralığına göre korur.

Eşit olarak dağıtılmış kullanım için küme silme zamanına göre faturalandırılırsınız. Kümeniz kalıcı olarak silindikten sonra, Confur size bir e-posta onayı gönderir.

## <a name="get-support"></a>Destek alın

Bir destek talebi göndermek için, aşağıda gösterildiği gibi, bu [destek](https://support.confluent.io) ile iletişim kurun ya da Portal üzerinden bir istek gönderebilirsiniz.

> [!NOTE]
> İlk kez kullanıcılar için, şifre destek portalında oturum açmadan önce parolanızı sıfırlayın. Uyumlu bulutla bir hesabınız yoksa, daha fazla yardım almak için adresine bir e-posta gönderin `cloud-support@confluent.io` .

Portalda Azure yardım ve Destek aracılığıyla bir istek gönderebilir ya da Azure 'da confluent bulutu için doğrudan Apache Kafka örneğinden bir istek gönderebilirsiniz.

Azure yardım ve Destek aracılığıyla bir istek göndermek için:

1. **Destek + yardım** seçeneğini belirleyin.
1. **Destek Isteği oluştur**' u seçin.
1. Formda, **sorun türü** için **Teknik** ' i seçin. Aboneliğinizi seçin. Hizmetler listesinde **Azure 'Da confluent**' ı seçin.

    :::image type="content" source="media/support-request-help.png" alt-text="Yardım 'dan bir destek isteği oluşturun.":::

Kaynağından bir istek göndermek için şu adımları izleyin:

1. Azure portal, konuş kuruluşunuzu seçin.
1. Ekranın sol tarafındaki menüden **Yeni destek isteği**' ni seçin.
1. Bir destek isteği oluşturmak için, **konur portalının** bağlantısını seçin.

    :::image type="content" source="media/support-request.png" alt-text="Örnekten bir destek isteği oluşturun.":::

## <a name="next-steps"></a>Sonraki adımlar

Sorun gidermeye yönelik yardım için bkz. [sorun giderme Apache Kafka, uyumlu bulut çözümleri](troubleshoot.md).
