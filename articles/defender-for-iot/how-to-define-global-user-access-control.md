---
title: Global kullanıcı erişim denetimini tanımlama
description: Büyük kuruluşlarda, Kullanıcı izinleri karmaşık olabilir ve standart site ve bölge yapısına ek olarak genel bir kuruluş yapısıyla belirlenebilir.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784297"
---
# <a name="define-global-access-control"></a>Genel erişim denetimi tanımlama

Büyük kuruluşlarda, Kullanıcı izinleri karmaşık olabilir ve standart site ve bölge yapısına ek olarak genel bir kuruluş yapısıyla belirlenebilir.

Küresel ve daha karmaşık olan kullanıcı erişimi izinlerinin talebini desteklemek için, iş birimleri, bölgeler ve siteler temelinde küresel bir iş topolojisi oluşturabilirsiniz. Daha sonra, bu varlıklar etrafında Kullanıcı erişim izinleri tanımlayabilirsiniz.

İş topolojisi için erişim araçları ile çalışma, kuruluşların, kullanıcıların IoT Platformu için Azure Defender 'da cihazları yönetip ve analiz etmesini sağlar.

## <a name="about-access-groups"></a>Erişim grupları hakkında

Genel erişim denetimi, Kullanıcı erişim gruplarının oluşturulmasıyla oluşturulur. Erişim grupları, kullanıcıların belirli iş varlıklarına erişebileceği kuralların oluşur. Gruplarla çalışma, ilgili iş birimleri, bölgeler ve sitelerdeki belirli kullanıcı rolleri için, Defender 'a görüntüleme ve yapılandırma erişimini denetlemenize olanak tanır.

Örneğin, bir Active Directory grubundan Güvenlik analistlerinin tüm Batı Avrupa Oto ve cam üretim satırlarına, tek bir bölgedeki Platik çizgi ile erişmesine izin verin.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Güvenlik analisti Active Directory grubunun diyagramı.":::

Erişim grupları oluşturmadan önce şunları yapmanızı öneririz:

- İş topolojinizi dikkatle ayarlayın. İş topolojisi hakkında daha fazla bilgi için bkz. [site haritası görünümleriyle çalışma](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Hangi kullanıcıların oluşturduğunuz erişim gruplarıyla ilişkilendirildiğini planlayın. Kullanıcıları erişim gruplarına atamak için iki seçenek mevcuttur:

  - **Active Directory grubu grupları ata**: şirket içi yönetim konsoluyla tümleştirilecek bir Active Directory örneği ayarladığınızdan emin olun.
  
  - **Yerel kullanıcıları ata**: kullanıcıları oluşturduğunuz doğrulayın. Daha fazla bilgi için bkz. [kullanıcıları tanımlama](how-to-create-and-manage-users.md#define-users).

Yönetici kullanıcılar erişim gruplarına atanamaz. Bu kullanıcıların varsayılan olarak tüm iş topolojisi varlıklarına erişimi vardır.

## <a name="create-access-groups"></a>Erişim grupları oluşturma

Bu bölümde, erişim gruplarının nasıl oluşturulacağı açıklanmaktadır. Varsayılan küresel iş birimleri ve bölgeler, oluşturduğunuz ilk grup için oluşturulur. İlk grubunuzu tanımlarken varsayılan varlıkları düzenleyebilirsiniz.

Grupları oluşturmak için:

1. Şirket içi yönetim konsolunun yan menüsünde **erişim grupları** ' nı seçin.

2. :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false"::: öğesini seçin. **Erişim grubu Ekle** iletişim kutusunda, erişim grubu için bir ad girin. Konsol 64 karakter destekler. Bu grubu diğer gruplardan kolayca ayırt etmenize yardımcı olacak şekilde adı atayın.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Erişim grupları oluşturduğunuz erişim grubu Ekle iletişim kutusu.":::

3. **Active Directory grubu ata** seçeneği görüntülenirse, bu erişim grubuna Active Directory bir grup Kullanıcı grubu atayabilirsiniz.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Erişim grubu oluştur iletişim kutusunda bir Active Directory grubu atayın.":::

   Seçenek görüntülenmezse ve erişim gruplarına Active Directory grupları eklemek istiyorsanız **sistem ayarları**' nı seçin. **Tümleştirmeler** bölmesinde grupları tanımlayın. Active Directory yapılandırmalarında ve küçük harfle göründüğü şekilde bir grup adı girin.

5. **Kullanıcılar** bölmesinde, gruba gereken sayıda kullanıcı atayın. Ayrıca, kullanıcıları farklı gruplara atayabilirsiniz. Bu şekilde çalışıyorsanız, erişim grubunu ve kurallarını oluşturup kaydetmeniz ve ardından **Kullanıcılar bölmesinden kullanıcıları** gruba atamanız gerekir.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Kullanıcılarınızın rollerini yönetin ve gerektiğinde bunları atayın.":::

6. İş topolojinizin erişim gereksinimlerine bağlı olarak ***ad* için kurallar Ekle** iletişim kutusunda kurallar oluşturun. Burada görüntülenen seçenekler, **Kurumsal Görünüm** ve **site yönetimi** penceresinde tasarladığınız topolojiye bağlıdır. 

   Her grup için birden fazla kural oluşturabilirsiniz. Birden çok sitede karmaşık erişim ayrıntı düzeyiyle çalışırken her grup için birden fazla kural oluşturmanız gerekebilir. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Sisteminize bir kural ekleyin.":::

Oluşturduğunuz kurallar, **erişim grubu Ekle** iletişim kutusunda görünür. Burada, bunları silebilir veya düzenleyebilirsiniz.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Tüm erişim gruplarınızı bu pencereden görüntüleyin ve düzenleyin.":::

### <a name="about-rules"></a>Kurallar hakkında

Kural oluştururken, aşağıdaki bilgileri unutmayın:

- Bir erişim grubu çeşitli kurallar içerdiğinde, kural mantığı tüm kuralları toplar. Örneğin, kurallar ve Logic, değildir veya Logic.

- Bir kuralın başarıyla uygulanması için, **site yönetimi** penceresinde bölgelere algılayıcı atamanız gerekir.

- Her kural için yalnızca bir öğe atayabilirsiniz. Örneğin, her kural için bir iş birimi, bir bölge ve bir site atayabilirsiniz. Örneğin, bir Active Directory grubundaki kullanıcıların farklı bölgelerdeki farklı iş birimlerine erişmesini istiyorsanız grup için daha fazla kural oluşturun.

- Bir varlığı değiştirirseniz ve değişiklik kural mantığını etkiliyorsa, kural silinir. Bir topoloji varlığında yapılan değişiklikler kural mantığını etkilediğinden, tüm kuralların silindiği, erişim grubu kalır, ancak kullanıcılar şirket içi yönetim konsolunda oturum açamaz. Kullanıcıların oturum açması için yönetici ile iletişim kurma bilgilendirilir.

- Hiçbir iş birimi veya bölge seçilmezse, kullanıcılar tüm tanımlı iş birimlerine ve bölgelere erişebilir.

## <a name="see-also"></a>Ayrıca bkz.

[IoT konsol kullanıcıları için Defender hakkında](how-to-create-and-manage-users.md)
