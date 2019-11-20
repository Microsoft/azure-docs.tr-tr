---
title: Ayarları yönetme -Özel Translator
titleSuffix: Azure Cognitive Services
description: Özel çevirmende ayarları yönetme, çalışma alanı oluşturma, çalışma alanı paylaşma ve abonelik anahtarını yönetme.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595774"
---
# <a name="how-to-manage-settings"></a>Ayarları yönetme

Özel çevirici ayarları sayfasında, yeni bir çalışma alanı oluşturabilir, çalışma alanınızı paylaşabilir ve Microsoft çeviri abonelik anahtarınızı ekleyebilir veya değiştirebilirsiniz.

Ayarlar sayfasına erişmek için:

1. [Özel çevirmen](https://portal.customtranslator.azure.ai/) portalında oturum açın.
2. Özel çevirmen portalında kenar çubuğundaki dişli simgesine tıklayın.

    ![Ayar bağlantısı](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Microsoft Translator aboneliğini ilişkilendirme

Modelleri eğitmek veya dağıtmak için çalışma alanınız ile ilişkili bir Microsoft Translator Metin Çevirisi API'si abonelik anahtarınız olması gerekir.

Aboneliğiniz yoksa, aşağıdaki adımları izleyin:

1. Microsoft Translator Metin Çevirisi API'si abone olun. Bu makalede, Microsoft Translator Metin Çevirisi API'si nasıl abone olunacağı gösterilmektedir.
2. Çevirmen aboneliğinizin anahtarını aklınızda edin. KEY1 veya key2 seçeneklerinden biri kabul edilebilir.
3. Özel çevirmen portalına geri gidin.

### <a name="add-existing-key"></a>Mevcut anahtarı Ekle

1.  Çalışma alanınızın "Ayarlar" sayfasına gidin.
2.  Anahtar Ekle 'ye tıklayın

    ![Abonelik anahtarı ekleme](media/how-to/how-to-add-subscription-key.png)

3. İletişim kutusunda, Translator aboneliğiniz için anahtarı girin ve ardından "Ekle" düğmesine tıklayın.

    ![Abonelik anahtarı ekleme](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Bir anahtar ekledikten sonra, anahtarı dilediğiniz zaman değiştirebilir veya silebilirsiniz.

    ![Ekleme sonrasında abonelik anahtarı](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Çalışma alanınızı yönetin

Çalışma alanı, özel çeviri sisteminizi oluşturmaya ve oluşturmaya yönelik bir çalışma alanıdır. Bir çalışma alanı birden fazla proje, model ve belge içerebilir.

Çalışmanızın farklı bir kısmının farklı kişilerle paylaşılması gerekiyorsa, birden çok çalışma alanı oluşturmak yararlı olabilir.

## <a name="create-a-new-workspace"></a>Yeni bir çalışma alanı oluşturma

1.  Çalışma alanı "ayarları" sayfasına gidin.
2.  "Yeni çalışma alanı oluştur" bölümünde "yeni çalışma alanı" düğmesine tıklayın.

    ![Yeni çalışma alanı oluşturma](media/how-to/create-new-workspace.png)

4.  İletişim kutusunda yeni çalışma alanının adını girin.
5.  "Oluştur" a tıklayın.

    ![Yeni çalışma alanı oluştur iletişim kutusu](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Çalışma alanınızı paylaşma

Özel çevirmende, çalışmanızın farklı bir kısmının farklı kişilerle paylaşılması gerekiyorsa, çalışma alanınızı başkalarıyla paylaşabilirsiniz.

1.  Çalışma alanı "ayarları" sayfasına gidin.
2.  "Paylaşım ayarları" bölümünde "paylaşım" düğmesine tıklayın.

    ![Çalışma alanını paylaşma](media/how-to/share-workspace.png)

3.  İletişim kutusunda, bu çalışma alanının paylaşılacağını istediğiniz, virgülle ayrılmış bir e-posta adresi listesi girin. İle özel çevirmende oturum açmak için kişinin kullandığı e-posta adresiyle paylaşımda olduğunuzdan emin olun. Ardından uygun paylaşım düzeyini seçin.

4.  Çalışma alanınız "My Workspace" varsayılan adına sahipse, çalışma alanınızı paylaşmadan önce değiştirmeniz gerekecektir.
5.  "Kaydet" e tıklayın.

## <a name="sharing-permissions"></a>Paylaşım izinleri

1.  **Okuyucu** Çalışma alanındaki bir okuyucu, çalışma alanındaki tüm bilgileri görüntüleyebilecektir.

2.  **Düzenleyen** Çalışma alanındaki bir düzenleyici belge ekleyebilir, modelleri eğitebilir ve belge ve projeleri silebilir. Abonelik anahtarı ekleyebilirler, ancak çalışma alanının kimlerle paylaşıldığını değiştiremezler, çalışma alanını silebilir veya çalışma alanı adını değiştirebilirsiniz.

3.  **İnde** Bir sahibin çalışma alanı üzerinde tam izinleri vardır.

## <a name="change-sharing-permission"></a>Paylaşım iznini Değiştir

Bir çalışma alanı paylaşıldığında, "paylaşım ayarları" bölümü, bu çalışma alanının paylaşıldığı tüm e-posta adreslerini gösterir. Çalışma alanına sahip erişiminiz varsa, her bir e-posta adresi için varolan paylaşım iznini değiştirebilirsiniz.

1.  Her e-posta için "paylaşım ayarları" bölümünde, bir açılan menü, geçerli izin düzeyini gösterir.

2.  Açılır menüye tıklayın ve bu e-posta adresine atamak istediğiniz yeni izin düzeyini seçin.

    ![İzin ayarlarını paylaşma](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Çalışma alanınızı ve projenizi](how-to-migrate.md) [Microsoft Translator hub 'ından](https://hub.microsofttranslator.com) nasıl geçirebileceğinizi öğrenin
