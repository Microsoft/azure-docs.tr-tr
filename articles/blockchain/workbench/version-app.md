---
title: Blok zinciri uygulama sürümü oluşturma-Azure blok zinciri çalışma ekranı
description: Azure blok zinciri çalışma ekranı önizlemesinde uygulama sürümlerini kullanma.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 2a70112fd0ab6e2f664ca48265c121936b01e58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85209887"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure blok zinciri çalışma ekranı önizleme uygulaması sürümü oluşturma

Azure blok zinciri çalışma ekranı önizleme uygulamasının birden çok sürümünü oluşturup kullanabilirsiniz. Aynı uygulamanın birden fazla sürümü karşıya yüklenirse, bir sürüm geçmişi kullanılabilir ve kullanıcılar hangi sürümü kullanmak istediğinizi seçebilir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Blok zinciri çalışma ekranı dağıtımı. Daha fazla bilgi için bkz. dağıtım ayrıntıları için [Azure blok zinciri çalışma ekranı dağıtımı](deploy.md)
* Blok zinciri çalışma ekranı 'nda dağıtılan bir blok zinciri uygulaması. Bkz. [Azure blok zinciri üzerinde blok zinciri uygulaması oluşturma çalışma ekranı](create-app.md)

## <a name="add-an-app-version"></a>Uygulama sürümü Ekle

Yeni bir sürüm eklemek için yeni yapılandırmayı ve akıllı sözleşme dosyalarını blok zinciri çalışma ekranına yükleyin.

1. Bir Web tarayıcısında, blok zinciri çalışma ekranı web adresine gidin. Örneğin, `https://{workbench URL}.azurewebsites.net/` blockzincirinin çalışma ekranı web adresini bulma hakkında daha fazla bilgi için bkz. [blok zinciri Web URL 'si](deploy.md#blockchain-workbench-web-url)
2. [Blok zinciri çalışma ekranı Yöneticisi](manage-users.md#manage-blockchain-workbench-administrators)olarak oturum açın.
3. Başka bir sürümle güncelleştirmek istediğiniz blok zinciri uygulamasını seçin.
4. **Sürüm Ekle**' yi seçin. **Sürüm Ekle** bölmesi görüntülenir.
5. Karşıya yüklenecek yeni sürüm sözleşmesi yapılandırması ve sözleşme kodu dosyalarını seçin. Yapılandırma dosyası otomatik olarak onaylanır. Uygulamayı dağıtmadan önce tüm doğrulama hatalarını düzeltin.
6. Yeni blok zinciri uygulama sürümünü eklemek için **Sürüm Ekle** ' yi seçin.

    ![Yeni bir sürüm ekleme](media/version-app/add-version.png)

Blok zinciri uygulamasının dağıtılması birkaç dakika sürebilir. Dağıtım tamamlandığında, uygulama sayfasını yenileyin. Uygulamayı seçme ve **Sürüm geçmişi** düğmesini seçme, uygulamanın sürüm geçmişini görüntüler.

> [!IMPORTANT]
> Uygulamanın önceki sürümleri devre dışı bırakıldı. Eski sürümleri tek tek yeniden etkinleştirebilirsiniz.
>
> Yeni sürümdeki uygulama rollerinde değişiklik yapılırsa, uygulamayı uygulama rollerine yeniden eklemeniz gerekebilir.

## <a name="using-app-versions"></a>Uygulama sürümlerini kullanma

Varsayılan olarak, uygulamanın en son etkin sürümü blok zinciri çalışma ekranı 'nda kullanılır. Uygulamanın önceki bir sürümünü kullanmak istiyorsanız, önce uygulama sayfasından sürümü seçmeniz gerekir.

1. Blok zinciri çalışma ekranı uygulaması bölümünde, kullanmak istediğiniz sözleşmeyi içeren uygulama onay kutusunu seçin. Önceki sürümler etkinleştirilmişse, sürüm geçmişi düğmesi kullanılabilir.
2. **Sürüm geçmişi** düğmesini seçin.
3. Sürüm Geçmişi bölmesinde, *değiştirilen tarih* sütunundaki bağlantıyı seçerek uygulamanın sürümünü seçin.

    ![Önceki bir sürümü seçin](media/version-app/use-version.png)

    Yeni sözleşmeler oluşturabilir veya önceki sürüm sözleşmeleri üzerinde işlem yapabilirsiniz. Uygulamanın sürümü uygulama adının ardından görüntülenir ve eski sürüm hakkında bir uyarı görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure blok zinciri ve sorun giderme](troubleshooting.md)
