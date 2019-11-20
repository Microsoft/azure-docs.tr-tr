---
title: Azure Active Directory Enterprise State Roaming etkinleştirme | Microsoft Docs
description: Windows cihazlarındaki Enterprise State Roaming ayarları hakkında sık sorulan sorular.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f4a260bb52fb10147f6d6b9e74aa5cd4fd0e1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562146"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory'de Kurumsal Durumda Dolaşımı etkinleştirme
Enterprise State Roaming, Azure AD Premium veya Enterprise Mobility + Security (EMS) lisansı olan tüm kuruluşlar tarafından kullanılabilir. Azure AD aboneliği alma hakkında daha fazla bilgi için bkz. [Azure AD ürün sayfası](https://azure.microsoft.com/services/active-directory).

Enterprise State Roaming etkinleştirdiğinizde, kuruluşunuza Azure Rights Management koruması için Azure Information Protection 'ten ücretsiz, sınırlı kullanım lisansı verilir. Bu ücretsiz abonelik, Enterprise State Roaming tarafından eşitlenen kurumsal ayarları ve uygulama verilerini şifrelemek ve şifrelerini çözmek için sınırlıdır. Azure Rights Management hizmetinin tüm yeteneklerini kullanabilmeniz için [ücretli bir aboneliğinizin](https://azure.microsoft.com/pricing/details/information-protection/) olması gerekir.

## <a name="to-enable-enterprise-state-roaming"></a>Enterprise State Roaming etkinleştirmek için

1. [Azure AD Yönetim Merkezi](https://aad.portal.azure.com/)' nde oturum açın.
1. **Azure Active Directory** &gt; cihazları EnterpriseStateRoaming&gt; seçin.
1. **Kullanıcılar ayarları ve uygulama verilerini cihazlar arasında eşitleyebilir '** i seçin. Daha fazla bilgi için bkz. [cihaz ayarlarını yapılandırma](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![Kullanıcılar, cihazlar arasında ayarları ve uygulama verilerini eşitleyebileceği cihaz ayarı görüntüsü](./media/enterprise-state-roaming-enable/device-settings.png)
  
Windows 10 cihazının Enterprise State Roaming hizmetini kullanabilmesi için cihazın bir Azure AD kimliği kullanarak kimlik doğrulaması yapması gerekir. Azure AD 'ye katılmış cihazlarda kullanıcının birincil oturum açma kimliği Azure AD kimliğidir, bu nedenle ek yapılandırma gerekmez. Şirket içi Active Directory kullanan cihazlarda, BT yöneticisinin [karma Azure Active Directory katılmış cihazları yapılandırması](hybrid-azuread-join-manual-steps.md)gerekir. 

## <a name="data-storage"></a>Veri depolama
Enterprise State Roaming veriler, Azure Active Directory örneğinde ayarlanan ülke/bölge değeri ile en iyi şekilde hizalaan bir veya daha fazla [Azure](https://azure.microsoft.com/regions/) bölgesinde barındırılır. Enterprise State Roaming veriler, üç önemli coğrafi bölgeye göre bölümlendirilir: Kuzey Amerika, EMEA ve APAC. Kiracının Enterprise State Roaming verileri, coğrafi bölge ile yerel olarak bulunur ve bölgeler arasında çoğaltılmaz.  Örneğin:

| Ülke/bölge değeri | içinde barındırılan verileri vardır |
| -------------------- | ------------------------ |
| Fransa veya Zambiya gibi bir ülke/bölge | Avrupa 'daki bir veya daha fazla Azure bölgesi |
| Birleşik Devletler veya Kanada gibi Kuzey Amerika ülkesi/bölgesi | ABD içindeki bir veya daha fazla Azure bölgesi |
| Avustralya veya Yeni Zelanda gibi bir APAC ülkesi/bölgesi | Asya 'daki bir veya daha fazla Azure bölgesi |
| Güney Amerika ve Antarktika bölgeleri | ABD içindeki bir veya daha fazla Azure bölgesi |

Ülke/bölge değeri, Azure AD dizin oluşturma sürecinin bir parçası olarak ayarlanır ve daha sonra değiştirilemez. Veri depolama konumunuz hakkında daha fazla ayrıntıya ihtiyacınız varsa [Azure desteği](https://azure.microsoft.com/support/options/)ile bir bilet dosyası yapın.

## <a name="view-per-user-device-sync-status"></a>Kullanıcı başına cihaz eşitleme durumunu görüntüle
Kullanıcı başına cihaz eşitleme durumu raporunu görüntülemek için bu adımları izleyin.

1. [Azure AD Yönetim Merkezi](https://aad.portal.azure.com/)' nde oturum açın.
1. **Azure Active Directory** &gt; kullanıcıları tüm&gt; **Kullanıcılar**' ı seçin.
1. Kullanıcıyı seçin ve ardından **cihazlar**' ı seçin.
1. **Göster**altında, eşitleme durumunu göstermek için **ayarları ve uygulama verilerini eşitleyen cihazlar** ' ı seçin.
  
   ![cihaz eşitleme verileri ayarının görüntüsü](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Bu Kullanıcı için eşitleme cihazları varsa, cihazları burada gösterildiği gibi görürsünüz.
  
   ![cihaz eşitleme sütunlu verilerinin görüntüsü](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Veri saklama
Enterprise State Roaming kullanılarak Microsoft bulutuyla eşitlenen veriler, el ile silinene veya söz konusu veriler eski olarak belirlenene kadar tutulur. 

### <a name="explicit-deletion"></a>Açık silme
Açık silme, bir Azure Yöneticisi bir kullanıcıyı veya dizini sildiğinde ya da açıkça verilerin silineceğini belirtmektedir.

* **Kullanıcı silme**: Azure AD 'de bir Kullanıcı silindiğinde, Kullanıcı hesabı dolaşım verileri 90 ile 180 gün sonra silinir. 
* **Dizin silme**: Azure AD 'de bir dizinin tamamını silmek, anlık bir işlemdir. Bu dizinle ilişkili tüm ayar verileri 180 90 gün sonra silinir. 
* **İstek silme sırasında**: Azure AD Yöneticisi belirli bir kullanıcının verilerini veya ayarlarının verilerini el ile silmek isterse, yönetici [Azure desteğiyle](https://azure.microsoft.com/support/)bir bilet oluşturabilir. 

### <a name="stale-data-deletion"></a>Eski veri silme
Bir yıl boyunca erişilmeyen veriler ("Bekletme dönemi") eski olarak değerlendirilir ve Microsoft bulutunda silinebilir. Saklama süresi değişebilir, ancak 90 günden daha az olmayacaktır. Eski veriler belirli bir Windows/uygulama ayarları kümesi veya bir kullanıcıya ait tüm ayarlar olabilir. Örneğin:

* Hiçbir cihaz belirli bir ayarlar koleksiyonuna erişiyorsa (örneğin, bir uygulama cihazdan kaldırılırsa veya bir kullanıcının aygıtları için "Tema" gibi bir ayar grubu devre dışı bırakılmışsa), bu koleksiyon saklama süresinden sonra eski hale gelir ve silinebilir . 
* Bir Kullanıcı, tüm cihazlarında ayarları eşitlemeyi kapatmışsa, ayar verilerinin hiçbirine erişilmeyecektir ve bu kullanıcının tüm ayar verileri eskimiş olur ve saklama süresinden sonra silinebilir. 
* Azure AD dizin Yöneticisi tüm dizin için Enterprise State Roaming kapattığında, bu dizindeki tüm kullanıcılar ayarları eşitlemeyi durdurur ve tüm kullanıcılar için tüm ayarlar verileri eski olur ve saklama süresinden sonra silinebilir. 

### <a name="deleted-data-recovery"></a>Silinen veri kurtarma
Veri bekletme ilkesi yapılandırılamaz. Veriler kalıcı olarak silindikten sonra kurtarılamaz. Ancak, ayarlar verileri, Son Kullanıcı aygıtından değil yalnızca Microsoft bulutunda silinir. Herhangi bir cihaz daha sonra Enterprise State Roaming hizmetine yeniden bağlanırsa, ayarlar yeniden eşitlenir ve Microsoft bulutunda depolanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Enterprise State Roaming genel bakış](enterprise-state-roaming-overview.md)
* [Ayarlar ve veri dolaşımı hakkında SSS](enterprise-state-roaming-faqs.md)
* [Ayarları eşitleme için grup ilkesi ve MDM ayarları](enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 dolaşım ayarları başvurusu](enterprise-state-roaming-windows-settings-reference.md)
* [Sorun giderme](enterprise-state-roaming-troubleshooting.md)
