---
title: Kullanıcıları oluşturma ve yönetme
description: Algılayıcıların kullanıcılarını ve şirket içi yönetim konsolunu oluşturun ve yönetin. Kullanıcılara yönetici, güvenlik analisti veya salt okuma Kullanıcı rolü atanabilir.
ms.date: 03/03/2021
ms.topic: article
ms.openlocfilehash: 2afc3cb2b9cfc0ac6b75c98198d9f0965b6dc04c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779027"
---
# <a name="about-defender-for-iot-console-users"></a>IoT konsol kullanıcıları için Defender hakkında

Bu makalede, algılayıcı kullanıcıları ve şirket içi yönetim konsolu 'nun nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. Kullanıcı rolleri yönetici, güvenlik analistini veya salt okunurdur. Her rol, algılayıcı veya şirket içi yönetim konsoluna yönelik araçlara yönelik bir dizi izinle ilişkilendirilir. Roller, IoT için Azure Defender 'a parçalı, güvenli erişimi kolaylaştırmak için tasarlanmıştır.

Ayrıca özellikler, Kullanıcı etkinliğini izlemek ve Active Directory oturum açmayı etkinleştirmek için de kullanılabilir.

Varsayılan olarak, her bir algılayıcı ve şirket içi yönetim konsolu bir *Six ve destek* kullanıcısı ile yüklenir. Bu kullanıcıların sorun giderme ve kurulum için gelişmiş araçlara erişimi vardır. Yönetici kullanıcıların bu kullanıcı kimlik bilgileriyle oturum açması, Yönetici Kullanıcı oluşturması ve ardından Güvenlik analistleri ve salt okunurdur kullanıcıları için ek kullanıcılar oluşturması gerekir.

## <a name="role-based-permissions"></a>Rol tabanlı izinler
Aşağıdaki Kullanıcı rolleri kullanılabilir:

- **Salt okunurdur**: salt okunurdur kullanıcılar cihaz eşlemesindeki uyarıları ve cihazları görüntüleme gibi görevleri gerçekleştirir. Bu kullanıcıların **Gezinti** altında görünen seçeneklere erişimi vardır.

- **Güvenlik analist**: Güvenlik analistlerinin salt okuma Kullanıcı izinleri vardır. Bunlar ayrıca cihazlarda eylemler gerçekleştirebilir, uyarıları kabul edebilir ve araştırma araçlarını kullanabilir. Bu kullanıcıların, **Gezinti** ve **analiz** altında görünen seçeneklere erişimi vardır.

- **Yönetici**: Yöneticiler, sistem yapılandırmalarının tanımlanması, Kullanıcı oluşturma ve yönetme gibi tüm araçlara erişebilir ve daha fazlasını sağlar. Bu kullanıcıların **Gezinti**, **analiz** ve **Yönetim** altında görünen seçeneklere erişimi vardır.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Şirket içi yönetim konsolu araçlarına rol tabanlı izinler

Bu bölümde, şirket içi yönetim konsolu için Yöneticiler, Güvenlik analistleri ve salt okuma kullanıcılarına sunulan izinler açıklanmaktadır.  

| İzin | Salt okunur | Güvenlik analisti | Yönetici |
|--|--|--|--|
| Kurumsal Haritayı görüntüleme ve filtreleme | ✓ | ✓ | ✓ |
| Site oluşturma |  |  | ✓ |
| Bir siteyi yönetme (bölge ekleme ve düzenleme) |  |  | ✓ |
| Cihaz envanterini görüntüleme ve filtreleme | ✓ | ✓ | ✓ |
| Uyarıları görüntüleme ve yönetme: bildirim, öğrenme ve sabitleme | ✓ | ✓ | ✓ |
| Rapor oluştur |  | ✓ | ✓ |
| Risk değerlendirmesi raporlarını görüntüleme |  | ✓ | ✓ |
| Uyarı dışlamalarını ayarla |  | ✓ | ✓ |
| Erişim gruplarını görüntüle veya tanımla |  |  | ✓ |
| Sistem ayarlarını yönetme |  |  | ✓ |
| Kullanıcıları yönetme |  |  | ✓ |
| İş ortaklarına uyarı verisi gönder |  |  | ✓ |
| Sertifikaları yönetme |  |  | ✓ |
| Kullanıcılar etkin olmadığında oturum zaman aşımı | 30 dakika | 30 dakika  | 30 dakika  |

#### <a name="assign-users-to-access-groups"></a>Kullanıcıları erişim gruplarına atama

Yöneticiler, kullanıcıları belirli *erişim gruplarına* atayarak IoT için Defender 'daki Kullanıcı erişim denetimini geliştirebilir. Erişim grupları, bir sensör bulunduğu bölgelere, sitelere, bölgelere ve iş birimlerine atanır. Yöneticiler, kullanıcılara gruplara erişim atayarak, kullanıcıların cihaz algılamalarını yönetmesi ve analiz etmek için belirli bir denetim elde eder. 

Bu şekilde çalışmak, Kullanıcı izinlerinin karmaşık veya küresel bir kurumsal güvenlik ilkesiyle belirlendiği büyük kuruluşlara uyum sağlar. Daha fazla bilgi için bkz. [genel erişim denetimi tanımlama](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Algılayıcı araçlarına rol tabanlı izinler

Bu bölümde, algılayıcı yöneticileri, Güvenlik analistleri ve salt okuma kullanıcılarına sunulan izinler açıklanmaktadır.  

| İzin | Salt okunur | Güvenlik analisti | Yönetici |
|--|--|--|--|
| Panoyu görüntüleme | ✓ | ✓ | ✓ |
| Denetim Haritası yakınlaştırma görünümleri |  |  | ✓ |
| Uyarıları görüntüleme | ✓ | ✓ | ✓ |
| Uyarıları yönetme: Onayla, öğren ve sabitle |  | ✓ | ✓ |
| Olayları bir zaman çizelgesinde görüntüleme |  | ✓ | ✓ |
| Yetkilendirme cihazları, bilinen tarama cihazları, programlama cihazları |  | ✓ | ✓ |
| Araştırma verilerini görüntüle | ✓ | ✓ | ✓ |
| Sistem ayarlarını yönetme |  |  | ✓ |
| Kullanıcıları yönetme |  |  | ✓ |
| Ters arama için DNS sunucuları |  |  | ✓ |
| İş ortaklarına uyarı verisi gönder |  | ✓ | ✓ |
| Uyarı açıklamaları oluştur |  | ✓ | ✓ |
| Programlama değişikliği geçmişini görüntüle | ✓ | ✓ | ✓ |
| Özelleştirilmiş uyarı kuralları oluşturma |  | ✓ | ✓ |
| Birden çok bildirimi eşzamanlı olarak yönetme |  | ✓ | ✓ |
| Sertifikaları yönetme |  |  | ✓ |
| Kullanıcılar etkin olmadığında oturum zaman aşımı | 30 dakika | 30 dakika | 30 dakika |

## <a name="define-users"></a>Kullanıcıları tanımlama

Bu bölümde kullanıcıların nasıl tanımlanacağı açıklanmaktadır. Six, destek ve yönetici kullanıcılar diğer Kullanıcı tanımlarını ekleyebilir, kaldırabilir ve güncelleştirebilir.

Bir kullanıcı tanımlamak için:

1. Algılayıcı veya şirket içi yönetim konsolunun sol bölmesinden **Kullanıcılar**' ı seçin.
1. **Kullanıcılar** penceresinde, **Kullanıcı oluştur**' u seçin.
1. **Kullanıcı oluştur** bölmesinde, aşağıdaki parametreleri tanımlayın:

   - **Kullanıcı adı**: bir Kullanıcı adı girin.
   - **E-posta**: kullanıcının e-posta adresini girin.
   - **Ad**: kullanıcının adını girin.
   - **Soyadı**: kullanıcının soyadını girin.
   - **Rol**: kullanıcının rolünü tanımlayın. Bkz. [rol tabanlı izinler](#role-based-permissions).
   - **Erişim grubu**: şirket içi yönetim konsolu için bir Kullanıcı oluşturuyorsanız, kullanıcının erişim grubunu tanımlayın. Bkz. [genel erişim denetimi tanımlama](how-to-define-global-user-access-control.md).
   - **Parola**: Kullanıcı türünü aşağıdaki gibi seçin:
     - **Yerel Kullanıcı**: bir algılayıcı veya şirket içi yönetim konsolunun kullanıcısı için bir parola tanımlayın. Parola en az altı karakter içermeli ve harf ve rakam içermelidir.
     - **Active Directory Kullanıcı**: Active Directory kimlik bilgilerini kullanarak kullanıcıların sensörde veya yönetim konsolunda oturum açmalarına izin verebilirsiniz. Tanımlı Active Directory grupları, belirli izin düzeyleriyle ilişkilendirilebilir. Örneğin, belirli bir Active Directory grubunu yapılandırın ve gruptaki tüm kullanıcıları salt okunurdur Kullanıcı türüne atayın.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Kullanıcılarınızı yönetin.":::

## <a name="user-session-timeout"></a>Kullanıcı oturumu zaman aşımı

Kullanıcılar klavye veya farede belirli bir süre etkin değilse, oturumları oturumlarını kapatırlar ve yeniden oturum açması gerekir.

Kullanıcılar konsol faresi veya klavye ile 30 dakikalık bir süre boyunca çalışmadığında, oturum kapatma işlemi zorlanır.

Bu özellik varsayılan olarak ve yükseltmesinde etkindir ancak devre dışı bırakılabilir. Ayrıca, oturum sayma süreleri de güncelleştirilemeyebilir. Oturum süreleri Saniyeler içinde tanımlanır. Tanımlar, algılayıcı ve şirket içi yönetim konsolu başına uygulanır.

Etkin olmama zaman aşımı geçtiğinde konsolda bir oturum zaman aşımı iletisi görüntülenir.

### <a name="control-inactivity-sign-out"></a>Denetim eylemsizlik oturumu kapatma

Yönetici kullanıcılar, etkinlik dışı bırakmayı etkinleştirip devre dışı bırakabilir ve eylemsizlik eşiklerini ayarlayabilir.

Komutuna erişmek için:

1. IoT yönetim kimlik bilgileri için Defender 'ı kullanarak sensör veya şirket içi yönetim konsolu için CLı 'da oturum açın.

1. `sudo nano /var/cyberx/properties/authentication` yazın.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Özelliği devre dışı bırakmak için öğesini `infinity_session_expiration = true` olarak değiştirin `infinity_session_expiration = false` .

Oturum kapatma sayma dönemlerini güncelleştirmek için `= <number>` değeri gerekli zamana ayarlayın.

## <a name="track-user-activity"></a>Kullanıcı etkinliğini izleme 

Her sensörde olay zaman çizelgesinde Kullanıcı etkinliğini izleyebilirsiniz. Zaman çizelgesi, olayı veya etkilenen cihazı, kullanıcının etkinliği gerçekleştirmiş olduğu saati ve tarihi görüntüler.

Kullanıcı etkinliğini görüntülemek için:

1. Sensörde oturum açın.
1. Olay zaman çizelgesinde, **Kullanıcı işlemleri** seçeneğini etkinleştirin. 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Kullanıcının etkinliğini görüntüleyin.":::

## <a name="integrate-with-active-directory-servers"></a>Active Directory sunucularıyla tümleştirme 

Algılayıcıyı veya şirket içi yönetim konsolunu Active Directory ile çalışacak şekilde yapılandırın. Bu, Active Directory kullanıcıların Active Directory kimlik bilgilerini kullanarak IoT konsolları için Defender 'a erişmelerini sağlar.

İki tür LDAP tabanlı kimlik doğrulaması desteklenir:

- **Tam kimlik doğrulaması**: kullanıcı ayrıntıları LDAP sunucusundan alınır. Örnek adı, soyadı, e-posta ve Kullanıcı izinleridir.

- **Güvenilen Kullanıcı**: yalnızca Kullanıcı parolası alınır. Alınan diğer kullanıcı ayrıntıları, sensörde tanımlanan kullanıcıları temel alır.

### <a name="active-directory-and-defender-for-iot-permissions"></a>IoT izinleri için Active Directory ve Defender

Burada tanımlanan Active Directory gruplarını belirli izin düzeyleriyle ilişkilendirebilirsiniz. Örneğin, belirli bir Active Directory grubunu yapılandırın ve gruptaki tüm kullanıcılara salt okuma izinleri atayın.

Active Directory yapılandırmak için:

1. Sol bölmeden **sistem ayarları**' nı seçin.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Active Directory sistem ayarlarınızı görüntüleyin.":::

2. **Sistem ayarları** bölmesinde **Active Directory**' yi seçin.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Active Directory yapılandırmalarınızı düzenleyin.":::

3. **Active Directory yapılandırmayı Düzenle** iletişim kutusunda **Active Directory tümleştirme etkin**  >  **Kaydet**' i seçin. **Active Directory yapılandırmayı Düzenle** iletişim kutusu genişletilir ve artık Active Directory yapılandırılacak parametreleri girebilirsiniz.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Active Directory yapılandırılacak parametreleri girin.":::

    > [!NOTE]
    > - LDAP parametrelerini, tam olarak Active Directory göründükleri gibi tanımlamanız gerekir.
    > - Tüm Active Directory parametreleri için yalnızca küçük harf kullanın. Active Directory ' deki yapılandırmaların büyük harfle kullanıldığı durumlarda bile küçük harf kullanın.
    > - Aynı etki alanı için hem LDAP hem de LDAPS yapılandıramazsınız. Ancak, aynı anda farklı etki alanları için her ikisini de kullanabilirsiniz.

4. Active Directory sunucusu parametrelerini aşağıdaki şekilde ayarlayın:

   | Sunucu parametresi | Description |
   |--|--|
   | Etki alanı denetleyicisi FQDN 'SI | Tam etki alanı adını (FQDN) tam olarak LDAP sunucunuzda göründüğü gibi ayarlayın. Örneğin, `host1.subdomain.domain.com` girin. |
   | Etki alanı denetleyicisi bağlantı noktası | LDAP 'nizin yapılandırıldığı bağlantı noktasını tanımlayın. |
   | Birincil etki alanı | Etki alanı adını (örneğin, `subdomain.domain.com` ) ve bağlantı türünü LDAP yapılandırmanıza göre ayarlayın. |
   | Active Directory grupları | LDAP sunucusundaki Active Directory yapılandırmanızda tanımlanan grup adlarını girin. |
   | Güvenilen etki alanları | Güvenilen bir etki alanı eklemek için, etki alanı adını ve güvenilen bir etki alanının bağlantı türünü ekleyin. <br />Güvenilen etki alanlarını yalnızca kullanıcılar altında tanımlı kullanıcılar için yapılandırabilirsiniz. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Şirket içi yönetim konsolu için ActiveDirectory grupları

Şirket içi yönetim konsolu kullanıcıları için Active Directory grupları oluşturuyorsanız, her bir Active Directory grubu için bir erişim grubu kuralı oluşturmanız gerekir. Active Directory Kullanıcı grubu için bir erişim grubu kuralı yoksa, şirket içi yönetim konsolu Active Directory kimlik bilgileri çalışmayacaktır. Bkz. [genel erişim denetimi tanımlama](how-to-define-global-user-access-control.md).

1. **Kaydet**’i seçin.

2. Güvenilen bir sunucu eklemek için **Sunucu Ekle** ve başka bir sunucu Yapılandır ' ı seçin.

## <a name="resetting-passwords"></a>Parolaları sıfırlama

### <a name="cyberx-or-support-user"></a>Six veya destek kullanıcısı

**Parola kurtarma** özelliğine yalnızca **Six** ve **destek** kullanıcısının erişimi vardır. **Six** veya **destek** kullanıcısı parolasını unutmuşsa, IoT oturum açma sayfası için Defender 'daki **parola kurtarma** seçeneği aracılığıyla parolayı sıfırlayabilirler.

Bir Six veya destek kullanıcısının parolasını sıfırlamak için:

1. IoT için Defender oturum açma ekranında  **parola kurtarma**' yı seçin. **Parola kurtarma** ekranı açılır.

1. **Six** ya da **destek**' i seçin ve benzersiz tanımlayıcıyı kopyalayın.

1. Azure portal gidin ve **siteler ve Algılayıcılar '** ı seçin.  

1. Üstteki araç çubuğundan **Abonelik filtresi** simgesini seçin :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  ve sensörizin bağlı olduğu aboneliği seçin.

1. **Şirket içi yönetim konsolu parolasını kurtar** sekmesini seçin.

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Kurtarma dosyasını indirmek için şirket içi yönetimi kurtar düğmesini seçin.":::

1. **Parola kurtarma** ekranında aldığınız benzersiz tanımlayıcıyı girip **kurtar**' ı seçin. `password_recovery.zip`Dosya indirilir.

    > [!NOTE]
    > Parola kurtarma dosyasını değiştirmeyin. Bu, imzalanmış bir dosyadır ve bununla karşılaşırsanız çalışmaz.

1. **Parola kurtarma** ekranında **karşıya yükle**' yi seçin. **Parola kurtarma dosyası yükle** penceresi açılır.

1. Dosyanızı bulmak için **Araştır** `password_recovery.zip` ' ı seçin veya `password_recovery.zip` pencereyi pencereye sürükleyin.

    > [!NOTE]
    > Dosyanın geçersiz olduğunu belirten bir hata iletisi görünebilir. Bu hata iletisini onarmak için, yüklemeden önce doğru aboneliği seçtiğinizden emin olun `password_recovery.zip` ve yeniden indirin.  

1. **İleri**' yi seçtiğinizde, Kullanıcı ve yönetim konsolunuz için sistem tarafından oluşturulan parolanız görüntülenir.

### <a name="administrator-security-analyst-and-read-only-user"></a>Yönetici, güvenlik çözümleyici ve salt okuma Kullanıcı

Salt okuma ve Güvenlik analistleri kendi parolalarını sıfırlayamaz ve parolasını sıfırlamak için yönetici, destek veya Six rolleriyle bir kullanıcıyla iletişim kurmak zorunda kalmaz. Bir yönetici kullanıcı, parolasını sıfırlamak için **Six** veya **destek** kullanıcısına başvurmalıdır.

Kullanıcının algılayıcıdaki parolasını sıfırlamak için:

1. Yönetici, destek veya Six rolü Kullanıcı sensörde oturum açması gerekir.

1. Sol taraftaki panelden **Kullanıcılar** ' ı seçin.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Sol taraftaki bölmeden Kullanıcı seçeneğini belirleyin.":::

1. Kullanıcıyı bulun ve **Eylemler** açılan menüsünden **Düzenle** ' yi seçin.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Eylemler açılan menüsünden Düzenle ' yi seçin.":::

1. Yeni parola alanına yeni parolayı girin **ve** **Yeni parola onaylama alanlarını onaylayın** .

1. **Güncelleştir**’i seçin.

Şirket içi yönetim konsolunda bir kullanıcının parolasını sıfırlamak için:

1. Yönetici, destek veya Six rolü Kullanıcı sensörde oturum açması gerekir.

1. Sol taraftaki panelden **Kullanıcılar** ' ı seçin.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Sol panelde kullanıcının seçeneğini belirleyin.":::

1. Kullanıcıyı bulun ve Düzenle simgesini seçin :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Yeni parola alanına yeni parolayı girin **ve** **Yeni parola onaylama alanlarını onaylayın** .

1. **Güncelleştir**’i seçin.

## <a name="see-also"></a>Ayrıca bkz.

Algılayıcıyı etkinleştirme ve [ayarlama](how-to-activate-and-set-up-your-sensor.md) 
 Şirket [içi yönetim konsolunuzu](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 etkinleştirin ve ayarlayın [Algılayıcı etkinliğini izle](how-to-track-sensor-activity.md)
