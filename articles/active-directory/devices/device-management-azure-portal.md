---
title: Azure portal kullanarak cihazları yönetme | Microsoft Docs
description: Azure portal kullanarak cihazları yönetme hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11182b8331f218b970d867764f575ba5b7854d62
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550702"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Azure portalını kullanarak cihaz kimliklerini yönetme

Azure AD, cihaz kimliklerini yönetmek için size merkezi bir yer sağlar.

**Tüm cihazlar** sayfası şunları yapmanızı sağlar:

- Aşağıdakiler dahil olmak üzere cihazları tanımla:
   - Azure AD 'ye katılmış veya kayıtlı olan cihazlar.
   - [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)kullanılarak dağıtılan cihazlar.
   - [Evrensel Yazdırma](/universal-print/fundamentals/universal-print-getting-started) kullanan yazıcılar
- Etkinleştirme, devre dışı bırakma, silme veya yönetme gibi cihaz kimlik yönetimi görevlerini gerçekleştirin.
   - [Yazıcıların](/universal-print/fundamentals/) ve [Windows AUTOPILOT](/windows/deployment/windows-autopilot/windows-autopilot) cihazlarının Azure AD 'de sınırlı sayıda yönetim seçeneği vardır. Bunların ilgili yönetici arabirimlerinden yönetilmesi gerekir.
- Cihaz kimliği ayarlarınızı yapılandırın.
- Enterprise State Roaming etkinleştirin veya devre dışı bırakın.
- Cihazla ilgili denetim günlüklerini gözden geçirme
- Aygıtları indir (Önizleme)

[![Azure portal tüm cihazlar görünümü](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Aşağıdaki adımları kullanarak cihazlar portalına erişebilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory**  >  **cihazlara** gidin.

## <a name="manage-devices"></a>Cihazları yönetme

Azure AD 'de cihazları yönetmek için iki konum vardır:

- **Azure Portal**  >  **Azure Active Directory**  >  **Cihazlar**
- **Azure Portal**  >  **Azure Active Directory**  >  **Kullanıcılar** > bir Kullanıcı > **cihaz** seçin

Her iki seçenek de yöneticilere şunları yapabilme olanağı sağlar:

- Cihazlar için arama yapın.
- Aşağıdakiler dahil cihaz ayrıntılarına bakın:
    - Cihaz adı
    - Cihaz Kimliği
    - İşletim sistemi ve sürüm
    - Katılım türü
    - Sahip
    - Mobil cihaz yönetimi ve uyumluluğu
    - BitLocker kurtarma anahtarı
- Etkinleştirme, devre dışı bırakma, silme veya yönetme gibi cihaz kimlik yönetimi görevlerini gerçekleştirin.
   - [Yazıcıların](/universal-print/fundamentals/) ve [Windows AUTOPILOT](/windows/deployment/windows-autopilot/windows-autopilot) cihazlarının Azure AD 'de sınırlı sayıda yönetim seçeneği vardır. Bunların ilgili yönetici arabirimlerinden yönetilmesi gerekir.

> [!TIP]
> - Karma Azure AD 'ye katılmış Windows 10 cihazlarının sahibi yok. Bir cihazı sahibine göre arıyorsanız ve bulamıyorsanız, cihaz KIMLIĞINE göre arama yapın.
>
> - KAYıTLı sütun altında "bekliyor" durumuna sahip "hibrit Azure AD 'ye katılmış" bir cihaz görürseniz, cihazın Azure AD Connect 'ten eşitlendiğini ve istemciden kayıt tamamlamayı beklediğini gösterir. [Hibrit Azure AD JOIN uygulamanızın nasıl planlanacağı](hybrid-azuread-join-plan.md)hakkında daha fazla bilgi edinin. Daha fazla bilgi için, [cihazlar sık sorulan sorular](faq.yml)makalesinde bulunabilir.
>
> - Bazı iOS cihazlarında, kesme işaretleri içeren cihaz adları, kesme işareti gibi farklı karakterler kullanabilir. Bu nedenle, bu tür cihazları aramak biraz karmaşık değildir. arama sonuçlarını doğru şekilde görmüyorsanız, arama dizesinin eşleşen kesme işareti karakteri içerdiğinden emin olun.

### <a name="manage-an-intune-device"></a>Intune cihazını yönetme

Bir Intune yöneticisiyseniz, MDM 'nin **Microsoft Intune** olarak işaretlendiği cihazları yönetebilirsiniz. Cihaz Microsoft Intune kayıtlı değilse, "Yönet" seçeneği gri kalır.

### <a name="enable-or-disable-an-azure-ad-device"></a>Azure AD cihazını etkinleştirme veya devre dışı bırakma

Cihazları etkinleştirmek veya devre dışı bırakmak için iki seçeneğiniz vardır:

- Bir veya daha fazla cihaz seçildikten sonra **tüm cihazlar** sayfasındaki araç çubuğu.
- Belirli bir cihazda detaya gidildikten sonra araç çubuğu.

> [!IMPORTANT]
> - Bir cihazı etkinleştirmek veya devre dışı bırakmak için Azure AD 'de bir genel yönetici veya bulut cihaz yöneticisi olmanız gerekir. 
> - Bir cihazın devre dışı bırakılması, cihazın Azure AD ile başarılı bir şekilde kimlik doğrulamasına engel olur, böylece cihazın cihaz tabanlı koşullu erişim ile korunan Azure AD kaynaklarınıza erişmesini veya Iş kimlik bilgileri için Windows Hello 'Yu kullanmasını önler.
> - Cihazı devre dışı bırakmak, hem birincil yenileme belirtecini (PRT) hem de cihazdaki yenileme belirteçlerini (RT) iptal eder.
> - Azure AD 'de yazıcılar etkinleştirilemez veya devre dışı bırakılamaz.

### <a name="delete-an-azure-ad-device"></a>Azure AD cihazını silme

Bir cihazı silmek için iki seçeneğiniz vardır:

- Bir veya daha fazla cihaz seçildikten sonra **tüm cihazlar** sayfasındaki araç çubuğu.
- Belirli bir cihazda detaya gidildikten sonra araç çubuğu.

> [!IMPORTANT]
> - Bir cihazı silmek için Azure AD 'de bulut Cihaz Yöneticisi, Intune Yöneticisi veya genel yönetici rolü atanmış olmalıdır.
> - Yazıcılar ve Windows Autopilot cihazları Azure AD 'de silinemez
> - Bir cihazı silme:
>    - Bir cihazın Azure AD kaynaklarınıza erişmesini önler.
>    - Cihaza iliştirilmiş tüm ayrıntıları kaldırır, örneğin, Windows cihazları için BitLocker anahtarları.  
>    - Kurtarılabilir olmayan bir etkinliği temsil eder ve gerekli olmadığı takdirde önerilmez.

Bir cihaz başka bir yönetim yetkilisi tarafından yönetiliyorsa (örneğin, Microsoft Intune), cihazın Azure AD 'de silinmeden önce temizlendiğinden/devre dışı olduğundan emin olun. Herhangi bir cihazı silmeden önce [eski cihazların nasıl yönetileceğini](manage-stale-devices.md) inceleyin.

### <a name="view-or-copy-device-id"></a>Cihaz KIMLIĞINI görüntüle veya Kopyala

Cihazdaki cihaz KIMLIĞI ayrıntılarını doğrulamak veya sorun giderme sırasında PowerShell 'i kullanmak için bir cihaz KIMLIĞI kullanabilirsiniz. Kopyala seçeneğine erişmek için cihaza tıklayın.

![Cihaz KIMLIĞINI görüntüleme](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker anahtarlarını görüntüleme veya kopyalama

Kullanıcıların şifrelenmiş sürücüleri kurtarmasına izin vermek için BitLocker anahtarlarını görüntüleyebilir ve kopyalayabilirsiniz. Bu anahtarlar yalnızca şifrelenmiş ve anahtarları Azure AD 'de depolanan Windows cihazlarında kullanılabilir. **Kurtarma anahtarını göster**' i seçerek, bir cihazın ayrıntılarına erişirken bu anahtarları bulabilirsiniz. **Kurtarma anahtarını göster** ' i seçtiğinizde, kategoride bulabileceğiniz bir denetim günlüğü oluşturulur `KeyManagement` .

![BitLocker anahtarlarını görüntüle](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

BitLocker anahtarlarını görüntülemek veya kopyalamak için, cihazın sahibi ya da aşağıdaki rollerden en az birine sahip olan bir kullanıcı olmanız gerekir:

- Bulut Cihaz Yöneticisi
- Genel Yönetici
- Yardım Masası Yöneticisi
- Intune Hizmet Yöneticisi
- Güvenlik Yöneticisi
- Güvenlik okuyucusu

### <a name="device-list-filtering-preview"></a>Cihaz listesi filtrelemesi (Önizleme)

Daha önce, cihaz listesini etkinliğe ve etkin duruma göre filtreleyebilirsiniz. Bu önizleme artık cihazlar listesini bir cihazda aşağıdaki özniteliklere göre filtrelemenize olanak tanır:

- Etkin durum
- Uyumlu durum
- Katılım türü (Azure AD 'ye katılmış, karma Azure AD 'ye katılmış, Azure AD kayıtlı)
- Etkinlik zaman damgası
- İşletim Sistemi
- Cihaz türü (yazıcılar, güvenli VM 'Ler, paylaşılan cihazlar, kayıtlı cihazlar)

**Tüm cihazlar** görünümünde önizleme filtreleme işlevini etkinleştirmek için:

![Önizleme işlevselliğinin filtrelenmesini etkinleştir](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory**  >  **cihazlara** gidin.
1. Görüntülenen başlığı seçin, **yeni cihaz filtreleme geliştirmelerini deneyin. Önizlemeyi etkinleştirmek için tıklayın.**

Artık, **tüm cihazlar** görünümlarınıza **filtre ekleme** imkanına sahip olursunuz.

### <a name="download-devices-preview"></a>Aygıtları indir (Önizleme)

Bulut cihaz yöneticileri, Intune yöneticileri ve genel Yöneticiler, cihazların bir CSV dosyasını, uygulanan filtreleri temel alarak dışarı aktarmak için **aygıtları indir (Önizleme)** seçeneğini kullanabilir. Listeye hiçbir filtre uygulanmışsa, tüm cihazlar verilecek. Bir dışarı aktarma işlemi, şu değere bağlı olarak bir saate kadar sürebilir 

Bu liste, aşağıdaki cihaz kimliği özniteliklerini içerir:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Cihaz ayarlarını yapılandırma

Azure AD portalını kullanarak cihaz kimliklerini yönetmek için, bu cihazların [kayıtlı veya](overview.md) Azure AD 'ye katılmış olmaları gerekir. Yönetici olarak, aşağıdaki cihaz ayarlarını yapılandırarak cihazları kaydetme ve birleştirme sürecini denetleyebilirsiniz.

Azure portal cihaz ayarlarını görüntülemek veya yönetmek için aşağıdaki rollerden birine atanmış olmanız gerekir:

- Genel yönetici
- Bulut Cihaz Yöneticisi
- Genel okuyucu
- Dizin okuyucu

![Azure AD ile ilgili cihaz ayarları](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Kullanıcılar cihazları Azure AD 'ye katabilir** -Bu ayar, CIHAZLARıNı Azure AD 'ye katılmış cihazlar olarak kaydedebilen kullanıcıları seçmenize olanak sağlar. Varsayılan değer **Tümü**' dir.

> [!NOTE]
> **Kullanıcılar cihazları Azure AD 'ye katabilir** ve yalnızca Windows 10 ' da Azure AD katılımı için geçerlidir. Bu ayar, karma Azure AD 'ye katılmış cihazlar, Azure [ad 'ye katılmış](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) Azure ve Azure AD 'ye katılmış cihazlarda [Windows Autopilot Self-Deployment modunu](/mem/autopilot/self-deploying) kullanarak bu yöntemler, kullanıcısız bir bağlamda çalışır.

- **Azure AD 'ye katılmış cihazlarda ek yerel Yöneticiler** -bir cihazda yerel yönetici hakları verilen kullanıcıları seçebilirsiniz. Bu kullanıcılar, Azure AD 'de *cihaz yöneticileri* rolüne eklenir. Azure AD ve cihaz sahiplerine Genel Yöneticiler varsayılan olarak yerel yönetici haklarına sahiptir. Bu seçenek, Azure AD Premium veya Enterprise Mobility Suite (EMS) gibi ürünlerle kullanılabilen bir Premium sürüm özelliğidir.
- **Kullanıcılar cihazlarını Azure AD 'ye kaydedebileceği** için bu ayarı Windows 10 Personal, IOS, Android ve MacOS CIHAZLARıNıN Azure AD 'ye kaydolmalarına izin verecek şekilde yapılandırmanız gerekir. **Hiçbiri**' ni seçerseniz, CIHAZLARıN Azure AD 'ye kaydolmaya izin verilmez. Microsoft 365 için Microsoft Intune veya mobil cihaz yönetimi (MDM) kaydı için kayıt gereklidir. Bu hizmetlerden birini yapılandırdıysanız, **Tümü** seçilidir ve **hiçbiri** kullanılamaz.
- **Azure AD 'ye katılmış veya Azure ad kaydı Için cihazların Multi-Factor Authentication gerektir** -kullanıcıların, CIHAZLARıNı Azure AD 'ye katmak veya kaydetmek için ek bir kimlik doğrulama faktörü sağlaması gerekip gerekmediğini seçebilirsiniz. Varsayılan değer **Hayır**' dır. Bir cihazı kaydederken veya katılırken çok faktörlü kimlik doğrulamasının gerekli olmasını öneririz. Bu hizmet için Multi-Factor Authentication 'ı etkinleştirmeden önce, cihazlarını kaydeden kullanıcılar için Multi-Factor Authentication ' ın yapılandırıldığından emin olmanız gerekir. Farklı Azure AD Multi-Factor Authentication hizmetleri hakkında daha fazla bilgi için bkz. [Azure ad Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)kullanmaya başlama. 

> [!NOTE]
> Azure AD **'ye katılmış veya Azure ad kaydı yapılacak cihazların** Azure AD 'ye katılmış (bazı özel durumlar ile) veya Azure AD 'nin kayıtlı olduğu cihazlar için geçerli Multi-Factor Authentication ayarı gereklidir. Bu ayar, karma Azure AD 'ye katılmış cihazlara, Azure ['da Azure AD 'ye katılmış VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) 'Lere ve [Windows Autopilot Self-Deployment modunu](/mem/autopilot/self-deploying)kullanarak Azure AD 'ye katılmış cihazlara uygulanmaz.

> [!IMPORTANT]
> - Bir cihazın katılması veya kaydedilmesi için Multi-Factor Authentication 'ı zorunlu tutmanın koşullu erişim 'teki ["cihazları Kaydet veya katılım" Kullanıcı eyleminin](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) kullanılması önerilir. 
> - Multi-Factor authencation gerektirmek için koşullu erişim ilkesi kullanıyorsanız bu ayarı **Hayır** olarak ayarlamanız gerekir. 

- **En fazla cihaz sayısı** -Bu ayar, bir kullanıcının Azure AD 'de sahip olduğu en fazla Azure AD 'ye katılmış veya Azure AD kayıtlı cihaz sayısını seçmenize olanak sağlar. Bir Kullanıcı bu kotaya ulaşırsa, mevcut cihazların bir veya daha fazlası kaldırılana kadar ek cihaz ekleyemeyecektir. Varsayılan değer **50**' dir. Değeri 100 ' e kadar artırabilirsiniz ve 100 ' ye bir değer girerseniz, Azure AD bunu 100 olarak ayarlar. Ayrıca, mevcut kota limitinden farklı bir sınır olmadan zorlamak için sınırsız değer kullanabilirsiniz.

> [!NOTE]
> Azure AD 'ye katılmış ya da Azure AD 'ye kayıtlı cihazlar için **en fazla cihaz sayısı** ayarı geçerlidir. Bu ayar, karma Azure AD 'ye katılmış cihazlar için geçerlidir.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Denetim günlükleri

Cihaz etkinlikleri, etkinlik günlükleri aracılığıyla kullanılabilir. Bu Günlükler cihaz kayıt hizmeti tarafından tetiklenen etkinlikleri ve kullanıcılar tarafından şunları içerir:

- Cihaz oluşturma ve cihazda sahipler/kullanıcılar ekleme
- Cihaz ayarlarında yapılan değişiklikler
- Bir cihazı silme veya güncelleştirme gibi cihaz işlemleri

Denetim verilerine giriş noktanız, **cihazlar** sayfasının **etkinlik** bölümündeki **Denetim günlükleri** ' dir.

Denetim günlüğünde, şunu gösteren bir varsayılan liste görünümü vardır:

- Olayın tarih ve saati
- Hedefler
- Etkinliğin başlatıcısı/aktör (kim)
- Etkinlik (ne)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Cihazlar sayfasının etkinlik bölümündeki, dört denetim günlüğü için tarihi, hedefi, aktörü ve etkinliği listeleyen bir tablonun ekran görüntüsü." border="false":::

Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Cihazlar sayfasının araç çubuğunu gösteren ekran görüntüsü. Sütunlar öğesi vurgulanır." border="false":::

Raporlanan verileri istediğiniz düzeye gelecek şekilde daraltmak için, aşağıdaki alanları kullanarak denetim verilerini filtreleyebilirsiniz:

- Kategori
- Etkinlik kaynak türü
- Etkinlik
- Tarih aralığı
- Hedef
- Başlatan (aktör)

Filtrelerin yanı sıra belirli girdileri de arayabilirsiniz.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Kategori, etkinlik kaynak türü, etkinlik, tarih aralığı, hedef ve aktör alanları ve arama alanı ile denetim veri filtresi denetimlerinin ekran görüntüsü." border="false":::

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD 'de eski cihazları yönetme](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)
