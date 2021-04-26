---
title: Azure Active Directory Connect Health işlemler
description: Bu makalede, Azure AD Connect Health dağıttıktan sonra gerçekleştirilebilecek ek işlemler açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 836c7bf9aefd4b2cb7d52c66bbd37e7ba38a467c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377319"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health işlemler
Bu konuda Azure Active Directory (Azure AD) Connect Health kullanarak gerçekleştirebileceğiniz çeşitli işlemler açıklanmaktadır.

## <a name="enable-email-notifications"></a>E-posta bildirimlerini etkinleştir
Uyarılar kimlik altyapınızın sağlıklı olmadığını gösteriyorsa, Azure AD Connect Health hizmetini e-posta bildirimleri gönderecek şekilde yapılandırabilirsiniz. Bu, bir uyarı oluşturulduğunda ve ne zaman çözümlendiğinde oluşur.

![Azure AD Connect Health e-posta bildirimi ayarlarının ekran görüntüsü](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-posta bildirimleri varsayılan olarak etkindir.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Azure AD Connect Health e-posta bildirimlerini etkinleştirmek için
1. Azure portalında Azure AD Connect Health arayın
2. **Eşitleme hatalarını** seçin
3. **Bildirim ayarlarını** seçin.
5. E-posta bildirim anahtarında **Açık**' ı seçin.
6. Tüm genel yöneticilerin e-posta bildirimleri almasını istiyorsanız onay kutusunu işaretleyin.
7. Diğer herhangi bir e-posta adresinden e-posta bildirimleri almak istiyorsanız, bunları **ek e-posta alıcıları** kutusunda belirtin. Bu listeden bir e-posta adresini kaldırmak için girişe sağ tıklayıp **Sil**' i seçin.
8. Değişiklikleri sonlandırmak için **Kaydet**' e tıklayın. Değişiklikler yalnızca kaydettikten sonra geçerli olur.

>[!NOTE] 
> Arka uç hizmetimizde eşitleme isteklerini işlerken sorunlar olduğunda, bu hizmet, kiracınızın yönetim iletişim e-posta adresine (es) hata ayrıntılarını içeren bir bildirim e-postası gönderir. Müşterilerin belirli durumlarda bu iletilerin hacminin canlı büyüklükte olduğunu öğrendiğimiz ve bu iletileri gönderme şeklini değiştirdiğimiz için geri bildirimde bulunduk. 
>
> Her gerçekleştiğinde her eşitleme hatası için bir ileti göndermek yerine, arka uç hizmetinin döndürdüğü tüm hataların günlük özetini göndereceğiz. Bu, müşterilerin bu hataları daha verimli bir şekilde işlemesini sağlar ve yinelenen hata iletilerinin sayısını azaltır.

## <a name="delete-a-server-or-service-instance"></a>Sunucu veya hizmet örneğini silme

>[!NOTE] 
> Silme adımları için Azure AD Premium lisansı gereklidir.

Bazı örneklerde, bir sunucunun izlenmesini kaldırmak isteyebilirsiniz. Azure AD Connect Health hizmetinden bir sunucuyu kaldırmak için bilmeniz gerekenler aşağıda verilmiştir.

Bir sunucuyu silerken, aşağıdakilere dikkat edin:

* Bu eylem, bu sunucudan daha fazla veri toplamayı durduruyor. Bu sunucu izleme hizmetinden kaldırılmıştır. Bu eylemden sonra, bu sunucu için yeni uyarıları, izlemeyi veya kullanım analizi verilerini görüntüleyemezsiniz.
* Bu eylem, sistem durumu aracısını sunucudan kaldırmaz. Bu adımı gerçekleştirmeden önce sistem durumu aracısını kaldırmadıysanız, sunucuda sistem durumu aracısıyla ilgili hatalarla karşılaşabilirsiniz.
* Bu eylem, bu sunucudan zaten toplanmış olan verileri silmez. Bu veriler, Azure veri bekletme ilkesine göre silinir.
* Bu eylemi gerçekleştirdikten sonra, aynı sunucuyu yeniden izlemeye başlamak istiyorsanız, sistem durumu aracısını bu sunucuya kaldırmanız ve yeniden yüklemeniz gerekir.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Azure AD Connect Health hizmetinden bir sunucuyu silme

>[!NOTE] 
> Silme adımları için Azure AD Premium lisansı gereklidir.

Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ve Azure AD Connect (eşitleme) için Azure AD Connect Health:

1. Kaldırılacak sunucu adını seçerek sunucu **listesi** dikey penceresinden **sunucu** dikey penceresini açın.
2. **Sunucu** dikey penceresinde, eylem çubuğundan **Sil**' e tıklayın.
![Sunucu silme Azure AD Connect Health ekran görüntüsü](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Onay kutusuna sunucu adını yazarak doğrulayın.
4. **Sil**'e tıklayın.

Azure Active Directory Domain Services için Azure AD Connect Health:

1. **Etki alanı denetleyicileri** panosunu açın.
2. Kaldırılacak etki alanı denetleyicisini seçin.
3. Eylem çubuğundan **Seçileni Sil**' e tıklayın.
4. Sunucuyu silme eylemini onaylayın.
5. **Sil**'e tıklayın.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Hizmet örneğini Azure AD Connect Health hizmetten silme
Bazı örneklerde, bir hizmet örneğini kaldırmak isteyebilirsiniz. Azure AD Connect Health hizmetinden bir hizmet örneğini kaldırmak için bilmeniz gerekenler aşağıda verilmiştir.

Bir hizmet örneğini silerken, aşağıdakilere dikkat edin:

* Bu eylem, geçerli hizmet örneğini izleme hizmetinden kaldırır.
* Bu eylem, bu hizmet örneğinin parçası olarak izlenen sunuculardan herhangi birinden sistem durumu Aracısı 'nı kaldırmaz veya kaldırmaz. Bu adımı gerçekleştirmeden önce sistem durumu aracısını kaldırmadıysanız, sunuculardaki sistem durumu aracısıyla ilgili hatalarla karşılaşabilirsiniz.
* Bu hizmet örneğindeki tüm veriler, Azure veri bekletme ilkesine göre silinir.
* Bu eylemi gerçekleştirdikten sonra, hizmeti izlemeye başlamak istiyorsanız tüm sunucularda sistem durumu aracısını kaldırın ve yeniden yükleyin. Bu eylemi gerçekleştirdikten sonra, aynı sunucuyu yeniden izlemeye başlamak istiyorsanız, bu sunucuda sistem durumu aracısını kaldırın, yeniden yükleyin ve kaydedin.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Azure AD Connect Health hizmetinden bir hizmet örneğini silmek için
1. Kaldırmak istediğiniz hizmet tanımlayıcısını (grup adı) seçerek hizmet **listesi** dikey penceresinden **hizmet** dikey penceresini açın. 
2. **Hizmet** dikey penceresinde, eylem çubuğundan **Sil**' e tıklayın. 
![Azure AD Connect Health hizmeti silme ekran görüntüsü](./media/how-to-connect-health-operations/DeleteServer.png)
3. Onay kutusuna hizmet adını yazarak onaylayın (örneğin: sts.contoso.com).
4. **Sil**'e tıklayın.
   <br><br>

[//]: # (RBAC bölümünün başlangıcı)
## <a name="manage-access-with-azure-rbac"></a>Azure RBAC ile erişimi yönetme
Azure AD Connect Health için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) , genel yönetici dışındaki kullanıcılara ve gruplara erişim sağlar. Azure RBAC, istenen kullanıcılara ve gruplara roller atar ve dizininizin içindeki genel yöneticileri sınırlandırmak için bir mekanizma sağlar.

### <a name="roles"></a>Roller
Azure AD Connect Health aşağıdaki yerleşik rolleri destekler:

| Rol | İzinler |
| --- | --- |
| Sahip |Sahipler *erişimi yönetebilir* (örneğin, bir kullanıcıya veya gruba bir rol atayabilir), portaldan *tüm bilgileri görüntüleyebilir* (örneğin, uyarıları görüntüleyebilir) ve Azure AD Connect Health içindeki ayarları (örneğin, e-posta bildirimleri) *değiştirebilirsiniz* . <br>Varsayılan olarak, Azure AD Genel yöneticilerine bu rol atanır ve bu ayar değiştirilemez. |
| Katılımcı |Katkıda bulunanlar portaldan *tüm bilgileri görüntüleyebilir* (örneğin, uyarıları görüntüleyebilir) ve Azure AD Connect Health içindeki ayarları (örneğin, e-posta bildirimleri) *değiştirebilir* . |
| Okuyucu |Okuyucular Azure AD Connect Health içindeki portaldan *tüm bilgileri görüntüleyebilir* (örneğin, uyarıları görüntüleyebilir). |

Diğer tüm rollerin (örneğin, Kullanıcı erişimi yöneticileri veya DevTest Labs kullanıcıları), roller Portal deneyiminde kullanılabilir olsa bile Azure AD Connect Health içinde erişim için hiçbir etkisi yoktur.

### <a name="access-scope"></a>Erişim kapsamı
Azure AD Connect Health, erişimin iki düzeyde yönetilmesini destekler:

* **Tüm hizmet örnekleri**: Bu, çoğu durumda önerilen yoldur. Azure AD Connect Health tarafından izlenmekte olan tüm rol türlerinde tüm hizmet örneklerinin (örneğin, bir AD FS grubu) erişimini denetler.
* **Hizmet örneği**: bazı durumlarda, rol türlerine veya bir hizmet örneğine göre erişimi ayırt etmeniz gerekebilir. Bu durumda, erişimi hizmet örneği düzeyinde yönetebilirsiniz.  

Bir son kullanıcı, dizin ya da hizmet örneği düzeyinde erişime sahipse izin verilir.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Kullanıcıların veya grupların Azure AD Connect Health erişimine izin ver
Aşağıdaki adımlarda erişime nasıl izin verilecek gösterilmektedir.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1. Adım: uygun erişim kapsamını seçin
Azure AD Connect Health içindeki *tüm hizmet örnekleri* düzeyinde kullanıcı erişimine izin vermek için Azure AD Connect Health Ana dikey penceresini açın.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2. Adım: kullanıcıları ve grupları ekleme ve rol atama
1. **Yapılandır** bölümünde **Kullanıcılar**' a tıklayın.<br>
   ![Azure AD Connect Health kaynak kenar çubuğu ekran görüntüsü](./media/how-to-connect-health-operations/startRBAC.png)
2. **Add (Ekle)** seçeneğini belirleyin.
3. **Rol seçin** bölmesinde bir rol (örneğin, **sahip**) seçin.<br>
   ![Azure AD Connect Health ve Azure RBAC yapılandırma menüsünün ekran görüntüsü](./media/how-to-connect-health-operations/RBAC_add.png)
4. Hedeflenen kullanıcı veya grubun adını veya tanımlayıcısını yazın. Aynı anda bir veya daha fazla Kullanıcı veya grup seçebilirsiniz. **Seç**’e tıklayın.
   ![Azure AD Connect Health ve Azure rol listesinin ekran görüntüsü](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. **Tamam**’ı seçin.<br>
6. Rol ataması tamamlandıktan sonra, kullanıcılar ve gruplar listede görüntülenir.<br>
   ![Azure AD Connect Health ve Azure RBAC ve yeni kullanıcıların vurgulandığı ekran görüntüsü](./media/how-to-connect-health-operations/RBAC_user_list.png)

Artık listelenen kullanıcılar ve gruplar, kendilerine atanan rollerine göre erişime sahiptir.

> [!NOTE]
> * Genel yöneticilerin her zaman tüm işlemlere tam erişimi vardır, ancak genel yönetici hesapları önceki listede yoktur.
> * Kullanıcıları davet etme özelliği Azure AD Connect Health içinde desteklenmez.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3. Adım: dikey pencere konumunu kullanıcılarla veya gruplarla paylaşma
1. İzinleri atadıktan sonra, bir Kullanıcı [buraya](https://aka.ms/aadconnecthealth)giderek Azure AD Connect Health erişebilir.
2. Dikey pencerede Kullanıcı dikey pencereyi veya farklı parçalarını panoya sabitleyebilir. **Panoya sabitle** simgesine tıklamanız yeterlidir.<br>
   ![PIN simgesi vurgulanmış şekilde Azure AD Connect Health ve Azure RBAC pin dikey penceresinin ekran görüntüsü](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Atanmış okuyucu rolüne sahip bir Kullanıcı Azure Marketi 'nden Azure AD Connect Health uzantısını alamıyor. Kullanıcı bu işlemi gerçekleştirmek için gerekli "Oluştur" işlemini gerçekleştiremiyor. Kullanıcı, önceki bağlantıya giderek dikey pencereye yine de alabilir. Sonraki kullanımlar için Kullanıcı dikey pencereyi panoya sabitleyebilir.
>
>

### <a name="remove-users-or-groups"></a>Kullanıcıları veya grupları kaldır
Azure AD Connect Health ve Azure RBAC 'ye eklenen bir kullanıcıyı veya grubu kaldırabilirsiniz. Kullanıcıya veya gruba sağ tıklayıp **Kaldır**' ı seçmeniz yeterlidir.<br>
![Vurgulanan Azure AD Connect Health ve Azure RBAC 'nin ekran görüntüsü](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (RBAC bölümünün sonu)

## <a name="next-steps"></a>Sonraki adımlar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Aracısı yüklemesi](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health'i AD FS ile Kullanma](how-to-connect-health-adfs.md)
* [Eşitleme için Azure AD Connect Health kullanma](how-to-connect-health-sync.md)
* [Azure AD Connect Health'i AD DS ile Kullanma](how-to-connect-health-adds.md)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
* [Azure AD Connect Health sürüm geçmişi](reference-connect-health-version-history.md)
