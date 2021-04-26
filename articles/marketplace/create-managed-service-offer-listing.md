---
title: Microsoft Iş Ortağı Merkezi 'nde yönetilen hizmet teklifi listeleme ayrıntılarını yapılandırma
description: Azure Market 'te Iş Ortağı Merkezi 'ni kullanarak yönetilen hizmet teklifi listeleme ayrıntılarını nasıl yapılandıracağınızı öğrenin.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 5ab831ae6ab761804fc7a4665000a13ab61acadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918445"
---
# <a name="how-to-configure-your-managed-service-offer-listing-details"></a>Yönetilen hizmet teklifi listeleme ayrıntılarını yapılandırma

Iş Ortağı Merkezi 'nin **teklif listesi** sayfasında sağladığınız bilgiler, Azure Marketi ' nde görüntülenir. Bu teklif adı, açıklama, medya ve diğer pazarlama varlıklarınızı içerir.

> [!NOTE]
> Teklifiniz Ingilizce dışındaki bir dildeyseniz, teklif listesi bu dilde olabilir, ancak açıklama "Bu hizmet, &lt; teklif içeriklerinizin dilinde kullanılabilir>" İngilizce tümcesiyle başlamalıdır veya bitmelidir. Ayrıca, teklif listesi ayrıntılarından farklı bir dilde destekleme belgelerini de sağlayabilirsiniz.

Iş Ortağı Merkezi 'nde **teklif listesi** sayfasında aşağıda açıklanan bilgileri sağlayın. Yönetilen hizmet teklifiniz için liste ayrıntıları hakkında daha fazla bilgi edinmek için, [yönetilen hizmet teklifini planlayın](./plan-managed-service-offer.md)' i gözden geçirin.

## <a name="marketplace-details"></a>Market ayrıntıları

1. **Ad** kutusu, daha önce yeni teklif iletişim kutusunda girdiğiniz adla önceden doldurulmuştur, ancak istediğiniz zaman değiştirebilirsiniz. Bu ad, çevrimiçi mağaza 'daki teklif listelerinizin başlığı olarak görünür.
2. **Arama sonuçları Özeti** kutusunda, teklifinizin amacını veya hedefini 100 karakter veya daha az bir şekilde tanıtın.
3. **Kısa açıklama** alanına teklifinizin kısa bir açıklamasını (en fazla 256 karakter) sağlayın. Azure portal teklif listelemesi üzerinde görüntülenir.
4. **Açıklama** alanında, yönetilen hizmet teklifinizi açıklayacağız. Bu kutuya, HTML etiketleri ve boşluklar dahil olmak üzere en fazla 2.000 karakter girebilirsiniz. HTML biçimlendirme hakkında daha fazla bilgi için bkz. [teklif açıklamalarında desteklenen HTML etiketleri](./supported-html-tags.md).
5. **Gizlilik ilkesi bağlantı** kutusuna kuruluşunuzun gizlilik ilkesine bir bağlantı (https ile başlayarak) girin. Teklifinizin gizlilik yasaları ve yönetmeliklerle uyumlu olduğundan ve geçerli bir gizlilik ilkesi sunmaktan sorumlu olursunuz.

## <a name="useful-links"></a>Yararlı bağlantılar

Çözümünüz hakkında ek çevrimiçi belgeler sağlama seçeneğiniz vardır:

1. **Bağlantı ekle**' yi seçin.
2. Her belge için bir ad ve Web adresi (https ile başlayarak) sağlayın.

## <a name="contact-information"></a>İletişim bilgileri

Şirketinizdeki iki kişinin adını, e-posta adresini ve telefon numarasını girin (bunlardan biri olabilirsiniz): bir destek kişisi ve mühendislik ilgili kişisi. Teklifiniz hakkında sizinle iletişim kurmak için bu bilgileri kullanacağız. Bu bilgiler müşterilere gösterilmez, ancak bulut çözümü sağlayıcısı (CSP) iş ortaklarına temin edilebilir.

## <a name="support-urls"></a>Destek URL 'Leri

Azure küresel müşterileri ve/veya Azure Kamu müşterileri için destek web siteleri varsa, https 'den başlayarak URL 'sini girin.

## <a name="marketplace-media"></a>Market medyası

> [!NOTE]
> Dosya yükleme sorununuz varsa, yerel ağınızın `https://upload.xboxlive.com` Iş Ortağı Merkezi tarafından kullanılan hizmeti engellemediğinden emin olun.

### <a name="add-logos"></a>Logo ekle

**Amblemler** altında **büyük** bir logo yükleyin. 216 x 216 ve 350 x 350 piksel arasında PNG biçimi. İş Ortağı Merkezi, daha sonra değiştirmek için otomatik olarak **Orta** ve **küçük** logo oluşturacak.

* Büyük logo (216 x 216 ile 350 x 350 piksel), Azure Marketi 'nde teklif listeleminizin üzerinde görünür.
* Orta logo (90 x 90 piksel), yeni bir kaynak oluşturulduğunda gösterilir.
* Küçük logo (48 x 48 piksel), Azure Marketi arama sonuçlarında kullanılır.

### <a name="add-screenshots-optional"></a>Ekran görüntüleri ekleme (isteğe bağlı)

Teklifinizi gösteren en fazla beş görüntü ekleyin. Tüm görüntüler 1280 x 720 piksel boyutunda ve içinde olmalıdır. PNG biçimi.

1. **Ekran görüntüleri** altında png dosyanızı **ekran görüntüsü** kutusuna sürükleyip bırakın.
2. **Resim yazısı ekle**' yi seçin.
3. Görüntülenen iletişim kutusunda bir başlık girin.
4. Ek ekran görüntüleri eklemek için 1 ile 3 arasındaki adımları yineleyin.

### <a name="add-videos-optional"></a>Video ekleme (isteğe bağlı)

Teklifinizi gösteren YouTube veya Vimeo videolarınıza bağlantılar ekleyebilirsiniz. Bu videolar, teklifinizle birlikte müşterilere gösterilir. Videonun küçük bir görüntüsünü, boyutlandırılmış 1280 x 720 piksel ve içinde girmeniz gerekir. PNG biçimi. Her teklif için en fazla beş video ekleyin.

1. **Videolar**' ın altında, **video ekle bağlantısını** seçin.
2. Görüntülenen kutulara videonuzun adını ve bağlantısını girin.
3. Bir PNG dosyasını (1280 x 720 piksel) gri **küçük resim** kutusuna sürükleyip bırakın.
4. Başka bir video eklemek için 1 ile 3 arasındaki adımları yineleyin.

Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin: **izleyiciyi Önizle**.

## <a name="next-steps"></a>Sonraki adımlar

* [Önizleme hedef kitlesi ekleme](create-managed-service-offer-preview.md)
