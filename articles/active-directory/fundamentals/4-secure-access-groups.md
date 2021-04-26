---
title: Azure Active Directory ve Microsoft 365 Gruplar ile dış erişimi güvenli hale getirme
description: Azure Active Directory ve Microsoft 365 Gruplar, dış kullanıcılar kaynaklarınıza erişirken güvenliği artırmak için kullanılabilir.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d9d63c7a703987d7b17e6e03d8b5596d5f1dfa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560625"
---
# <a name="securing-external-access-with-groups"></a>Gruplarla dış erişimin güvenliğini sağlama 

Gruplar, herhangi bir erişim denetimi stratejisinin önemli bir parçasıdır. Azure Active Directory (Azure AD) güvenlik grupları ve Microsoft 365 (M365) grupları, kaynaklara erişimin güvenliğini sağlamaya yönelik temel olarak kullanılabilir.

Gruplar aşağıdaki erişim denetimi mekanizmalarının temeli olarak kullanılması için en iyi seçenektir:

* Koşullu Erişim ilkeleri

* Yetkilendirme Yönetimi erişim paketleri 

* M365 kaynaklarına, Microsoft ekiplerine ve SharePoint sitelerine erişim

Gruplar aşağıdaki rollere sahiptir:

* Sahipler: Grup sahipleri grup ayarlarını ve üyeliğini yönetir.

* Üyeler: izinleri ve gruba atanan erişimi alan Üyeler.

* Konuklar – Konuklar, kuruluşunuzun dışından gelen üyelerdir. 

## <a name="determine-your-group-strategy"></a>Grup stratejinizi belirleme

Kaynaklarınıza dış erişimin güvenliğini sağlamak için Grup stratejinizi geliştirdikçe, aşağıdaki seçimleri öğrenmek için [istediğiniz güvenlik duruşunuzu](1-secure-access-posture.md) göz önünde bulundurun.

* **Grupları oluşturabiliyor musunuz?** Yalnızca yöneticilerin grup oluşturmasını ister veya çalışanların ve dış kullanıcıların da bu grupları oluşturmasını istiyor musunuz?

   * *Varsayılan olarak, tüm kiracı üyeleri Azure AD güvenlik grupları oluşturabilir*. 

      * [Yönetici olmayanlar için portala erişimi kısıtlayabilir](../develop/howto-restrict-your-app-to-a-set-of-users.md) ve PowerShell 'de Grup oluşturma özelliğini devre dışı bırakabilirsiniz [.](../enterprise-users/groups-troubleshooting.md) 

      * Ayrıca, [Azure Active Directory içinde Self Servis Grup yönetimini de ayarlayabilirsiniz](../enterprise-users/groups-self-service-management.md). 

   * *Varsayılan olarak, tüm kullanıcılar M365 grupları oluşturabilir ve kiracınızdaki tüm (iç ve dış) kullanıcıların katılması için gruplar açıktır*. 

      * [Microsoft 365 grubu oluşturmayı,](/microsoft-365/solutions/manage-creation-of-groups) belirli bir güvenlik grubunun üyeleriyle kısıtlayabilirsiniz. Bu ayarı yapılandırmak için Windows PowerShell 'i kullanın. 

* **Kişileri gruplara davet edebiliyor musunuz?** Tüm grup üyeleri başka Üyeler ekleyebiliyor mu, yoksa yalnızca sahipleri üye ekleyebiliyor musunuz?

* **Gruplara kimler davet edilebilir?** Varsayılan olarak, dış kullanıcılar gruplara eklenebilir. 

### <a name="assign-users-to-groups"></a>Kullanıcıları gruplara atama

Kullanıcılar, kullanıcı nesnesindeki Kullanıcı özniteliklerine veya diğer ölçütlere göre el ile gruplara atanabilir. Kullanıcılar yalnızca özniteliklerine göre dinamik olarak gruplara atanabilir.

Örneğin, kullanıcıları gruplarına göre gruplara atayabilirsiniz:

* belirli iş unvanı veya departmanı

* ait oldukları iş ortağı kuruluşu (el ile veya bağlı kuruluşlar aracılığıyla)

* Kullanıcı türü (üye veya konuk)

* belirli bir projeye katılım (el ile)

* location

Dinamik Gruplar Kullanıcı veya cihazları içerebilir ancak ikisini birden içeremez. Kullanıcıları dinamik gruba atamak için Kullanıcı özniteliklerine dayalı sorgular eklersiniz. Aşağıdaki örnek, Üyeler (konuklar değil) ve finans departmanının üyesi olmaları durumunda gruba Kullanıcı ekleyen sorguları gösterir.

![Dinamik üyelik kurallarını yapılandırma ekran görüntüsü.](media/secure-external-access/4-dynamic-membership-rules.png)

Dinamik Gruplar hakkında daha fazla bilgi için, bkz [. Azure Active Directory bir dinamik grup oluşturma veya güncelleştirme.](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Grupları birden çok amaçla kullanma

Güvenlik veya kaynak erişimi amacıyla grupları kullanırken, tek bir işleve sahip olmaları önemlidir. Kaynaklara erişim izni vermek için bir grup kullanılıyorsa, bu herhangi bir amaçla kullanılmamalıdır. Bir grup, konum veya takım üyeliğini tanımlamak gibi genel amaçlarla kullanılıyorsa, erişimin güvenliğini sağlamak için de kullanılmamalıdır. 

Amacı açık hale getiren güvenlik grupları için bir adlandırma kuralı öneririz. Örnek:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Grup türleri

Azure AD portal veya M365 yönetim portalından hem Azure AD güvenlik grupları hem de Microsoft 365 grupları oluşturulabilir. Her iki tür de, dış erişimin güvenliğini sağlamak için temel olarak kullanılabilir:

|Dikkat edilmesi gerekenler | Azure AD güvenlik grupları (el ile ve dinamik)| Microsoft 365 grupları |
| - | - | - |
| Grup neleri içerebilir?| Kullanıcılar<br>Gruplar<br>Hizmet ilkeleri<br>Cihazlar| Yalnızca kullanıcılar |
| Grup nerede oluşturulur?| Azure AD portalı<br>M365 portalı (posta olarak etkinleştirilmişse)<br>PowerShell<br>Microsoft Graph<br>Son Kullanıcı Portalı| M365 portalı<br>Azure AD portalı<br>PowerShell<br>Microsoft Graph<br>Microsoft 365 uygulamalarda |
| Kim varsayılan olarak oluşturulur?| Yöneticiler <br>Son kullanıcılar| Yöneticiler<br>Son kullanıcılar |
| Kim, varsayılan olarak eklenebilir mi?| İç kullanıcılar (Üyeler)| Herhangi bir kuruluştan kiracı üyeleri ve konukları |
| Ne erişim izni veriyor?| Yalnızca kendisine atanan kaynaklar.| Tüm grup ile ilgili kaynaklar:<br>(Grup posta kutusu, site, takım, sohbetler ve diğer dahil M365 kaynakları)<br>Grubun eklendiği diğer kaynaklar |
| İle kullanılabilir| Koşullu Erişim<br>Yetkilendirme Yönetimi<br>Grup lisanslama| Koşullu Erişim<br>Yetkilendirme Yönetimi<br>Duyarlılık etiketleri |



Ekip ve ilişkili siteleri ve içeriği gibi bir Microsoft 365 kaynakları kümesi oluşturmak ve yönetmek için Microsoft 365 gruplarını kullanın. Bunlar, proje tabanlı çaba için harika bir seçimdir. 

 

## <a name="azure-ad-security-groups"></a>Azure AD güvenlik grupları 

[Azure AD güvenlik grupları](./active-directory-manage-groups.md) , kullanıcıları veya cihazları içerebilir ve erişimini yönetmek için kullanılabilir 

* Microsoft 365 uygulamalar, özel uygulamalar ve bir hizmet olarak yazılım (SaaS) uygulaması gibi Azure kaynakları Dropbox 'ın ServiceNow gibi.

* Azure verileri ve abonelikleri.

* Azure hizmetleri.

Azure AD güvenlik grupları şunları yapmak için de kullanılabilir:

* M365, Dynamics 365 ve Enterprise Mobility ve Security gibi hizmetler için lisans atayın. Daha fazla bilgi için bkz. [grup tabanlı lisanslama](./active-directory-licensing-whatis-azure-portal.md).

* yükseltilmiş izinler atayın. Daha fazla bilgi için bkz. [rol atamalarını yönetmek için bulut gruplarını kullanma (Önizleme](../roles/groups-concept.md)). 

[Azure Portal](./active-directory-groups-create-azure-portal.md) bir grup oluşturmak için Azure Active Directory ve ardından Gruplar ' a gidin. Ayrıca, [PowerShell cmdlet 'lerini](../enterprise-users/groups-settings-v2-cmdlets.md)kullanarak Azure AD güvenlik grupları da oluşturabilirsiniz. 

> [!NOTE]
> Bir güvenlik grubu en fazla 1500 uygulama ataması için kullanılabilir, ancak daha fazla kullanılamaz. 

![Güvenlik grubu oluşturma ekranının ekran görüntüsü.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Posta etkin bir güvenlik grubu oluşturmak için [Microsoft 365 yönetim merkezine](https://admin.microsoft.com/)gidin**. Azure AD portalında oluşturamazsınız. 
<br>Bir güvenlik grubunu oluşturma sırasında posta için etkinleştirmeniz gerekir. Daha sonra etkinleştiremezsiniz.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Karma kuruluşlar ve Azure AD güvenlik grupları

Hibrit kuruluşların hem şirket içi altyapısı hem de bir Azure AD bulut altyapısı vardır. Active Directory kullanan birçok karma kuruluş, şirket içinde güvenlik gruplarını oluşturur ve bunları buluta eşitler. Bu yöntemi kullanarak, yalnızca şirket içi ortamdaki kullanıcılar güvenlik gruplarına eklenebilir.

Şirket içi **altyapınızı güvenlik altına alarak koruyun çünkü şirket içi bir ihlal, Microsoft 365 kiracınıza erişim kazanmak için kullanılabilir**. Yönergeler için bkz. [Şirket içi saldırılardan Microsoft 365 koruma](./protect-m365-from-on-premises-attacks.md) .

## <a name="microsoft-365-groups"></a>Microsoft 365 grupları

[Microsoft 365 Gruplar](/microsoft-365/admin/create-groups/office-365-groups) , tüm erişimi M365 genelinde yönlendiren temel üyelik hizmetidir. [Azure Portal](https://portal.azure.com/)veya [M365 Portal](https://admin.microsoft.com/)' dan oluşturulabilirler. Bir M365 grubu oluşturulduğunda, işbirliği yapmak için kullanılan bir kaynak grubuna erişim izni verirsiniz. Bu kaynakların tüm listesi için bkz. [Yöneticiler için Microsoft 365 gruplarına genel bakış](/microsoft-365/admin/create-groups/office-365-groups) .

M365 gruplar, rollerinin aşağıdaki nuslarını sahiptir

* **Sahipler** grubu sahipleri üye ekleyebilir veya kaldırabilir ve paylaşılan gelen kutusundan konuşmaları silme veya grup ayarlarını değiştirme yeteneği gibi benzersiz izinlere sahip olabilir. Grup sahipleri grubu yeniden adlandırabilir, açıklama veya resmi güncelleştirebilir ve daha fazlasını yapabilir.

* **Üyeler** -Üyeler gruptaki her şeye erişebilir, ancak Grup ayarlarını değiştiremezler. Varsayılan olarak, Grup üyeleri konukları grubunuza katılmaya davet edebilir, ancak [Bu ayarı denetleyebilirsiniz](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Konuklar** grubu konukları, kuruluşunuzun dışından gelen üyedir. Varsayılan olarak konuklar ekipteki işlevlere bazı sınırlara sahiptir.

 

### <a name="m365-group-settings"></a>M365 grubu ayarları

E-posta diğer adı, gizlilik ve kurulumu, kurulum sırasında takımlar için etkinleştirilip etkinleştirilmeyeceğini seçin. 

![Microsoft 365 grubu ayarlarını düzenlemenin ekran görüntüsü](media/secure-external-access/4-edit-group-settings.png)

Kurulumdan sonra, Üyeler ekler ve e-posta kullanımı, vb. ayarlarını yapılandırabilirsiniz.

### <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için istediğiniz güvenlik duruşunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanın](4-secure-access-groups.md) (burada bulabilirsiniz.)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)