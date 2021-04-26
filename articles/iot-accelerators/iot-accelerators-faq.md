---
title: IoT Çözüm Hızlandırıcıları SSS-Azure | Microsoft Docs
description: Bu makalede IoT Çözüm Hızlandırıcıları için sık sorulan sorular yanıtlanmaktadır. GitHub depoları bağlantıları içerir.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 61993df77b0831926f16339a741a2553e80c2a0d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713849"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT çözüm hızlandırıcıları için sık sorulan sorular

Ayrıca bkz. [bağlı fabrikada özgü SSS](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Çözüm hızlandırıcılarına ait kaynak kodunu nerede bulabilirim?

Kaynak kodu aşağıdaki GitHub depolarında depolanır:

* [Bağlı fabrika çözümü Hızlandırıcısı](https://github.com/Azure/azure-iot-connected-factory)
* [Cihaz benzetimi Çözüm Hızlandırıcısı](https://github.com/Azure/azure-iot-pcs-device-simulation)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Uzaktan izleme ve tahmine dayalı bakım çözüm hızlandırıcılarını nerede bulabilirim?

10 Aralık 2020 itibariyle, uzaktan izleme ve tahmine dayalı bakım Hızlandırıcılar, [Azure IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com/Accelerators) sitesinden kaldırılmıştır ve artık Yeni dağıtımlar için kullanılamaz. Her iki hızlandırıcı için GitHub depoları arşivlenir. Kod, herkesin erişimine açık olmaya devam eder, ancak depolar yeni bir katkı almaz.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Var olan uzaktan izleme ve tahmine dayalı bakım dağıtımlarıma ne olur?

Mevcut dağıtımlar, uzaktan izleme ve tahmine dayalı bakım çözümü hızlandırıcılarını kaldırmasından etkilenmez ve çalışmaya devam edecektir. Tüm depolar de etkilenmez. GitHub 'daki ana depolar arşivlendi.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Cihaz benzetimi çözüm hızlandırıcıyı Nasıl yaparım?.

Cihaz benzetimi çözüm Hızlandırıcısını dağıtmak için [cihaz benzetimi](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) GitHub deposuna bakın.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Kaldırılmış çözüm hızlandırıcılarına ilişkin bilgileri nerede bulabilirim?

Önceki sürümler sitesinde aşağıdaki sayfalara bakın:

* [Uzaktan izleme](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Tahmine dayalı bakım](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Çözüm hızlandırıcılarına yönelik cihaz istemcileri geliştirmek için hangi SDK 'Ları kullanabilirim?

Farklı dilin (C, .NET, Java, Node.js, Python) IoT cihaz SDK 'larının bağlantılarını [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 'lar GitHub depolarında bulabilirsiniz.

DevKit cihazını kullanıyorsanız, [IoT DEVKIT SDK](https://github.com/Microsoft/devkit-sdk) GitHub deposunda kaynakları ve örnekleri bulabilirsiniz.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Bir hizmet yöneticiyim ve aboneliğim ile belirli bir Azure AD kiracısı arasındaki dizin eşlemesini değiştirmek istiyorum. Nasıl yaparım? bu görevi tamamlayacak mı?

[Azure AD dizininize mevcut bir abonelik eklemek için](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory) bkz.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Bir kuruluş hesabıyla oturum açarken bir hizmet yöneticisini veya Co-Administrator değiştirmek istiyorum

[Bir kuruluş hesabıyla oturum açıldığında hizmet yöneticisi ve Co-Administrator değiştirme](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)destek makalesine bakın.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Bu hatayı neden görüyorum? "Hesabınız bir çözüm oluşturmak için uygun izinlere sahip değil. Lütfen hesap yöneticinize başvurun veya farklı bir hesap ile deneyin."

Rehberlik için aşağıdaki diyagrama bakın:

![İzin akış çizelgesi](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Azure AD kiracısının genel yöneticisi ve aboneliğin ortak Yöneticisi olduğunuz doğrulandıktan sonra hatayı görmeye devam ederseniz, Hesap yöneticinizin kullanıcıyı kaldırmasını ve gerekli izinleri bu sırayla yeniden atanmasını sağlayabilirsiniz. İlk olarak, kullanıcıyı genel yönetici olarak ekleyin ve ardından kullanıcıyı Azure aboneliğinin ortak Yöneticisi olarak ekleyin. Sorun devam ederse [destek & yardım 'a](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)başvurun.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Bir Azure aboneliğim varken bu hatayı neden görüyorum? "Önceden yapılandırılmış çözümler oluşturmak için bir Azure aboneliği gereklidir. Yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. "

Azure aboneliğiniz olduğundan emin değilseniz, aboneliğinizin kiracı eşlemesini doğrulayın ve açılan menüde doğru kiracının seçili olduğunu kontrol edin. Kiracının doğru olduğunu doğruladığınız takdirde, önceki diyagramı izleyin ve aboneliğinizin ve bu Azure AD kiracısının eşlemesini doğrulayın.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Azure portal bir kaynak grubunu silme ve azureiotsolutions.com içinde çözüm hızlandırıcısında Sil ' i tıklatma arasındaki fark nedir?

* Çözüm hızlandırıcıyı [azureiotsolutions.com](https://www.azureiotsolutions.com/)' de silerseniz, çözüm hızlandırıcıyı oluştururken dağıtılan tüm kaynakları silersiniz. Kaynak grubuna ek kaynaklar eklediyseniz, bu kaynaklar da silinir.
* [Azure Portal](https://portal.azure.com)kaynak grubunu silerseniz bu kaynak grubundaki kaynakları yalnızca silersiniz. Ayrıca çözüm hızlandırıcısında ilişkili Azure Active Directory uygulamasını da silmeniz gerekir.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Azure IoT çözüm hızlandırıcılarından mevcut yatırımlarımı kullanmaya devam edebilir miyim?

Evet. Bugün mevcut olan herhangi bir çözüm, Azure aboneliğinizde çalışmaya devam eder ve kaynak kodu GitHub 'da kullanılabilir kalır.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Bir abonelikte kaç IoT Hub örneği sağlayabilirim?

Varsayılan olarak, [abonelik başına 10 IoT Hub](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits)'ı sağlayabilirsiniz. Bu sınırı yükseltmek için bir [Azure destek bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oluşturabilirsiniz. Sonuç olarak, her çözüm Hızlandırıcısı yeni bir IoT Hub sağladığından, belirli bir abonelikte en fazla 10 çözüm hızlandırıcıyı sağlayabilirsiniz.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Bir abonelikte kaç Azure Cosmos DB örneği sağlayabilirim?

Elli. Bu sınırı yükseltmek için bir [Azure destek bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oluşturabilirsiniz, ancak varsayılan olarak abonelik başına yalnızca 50 Cosmos DB örnekleri sağlayabilirsiniz.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>DreamSpark için Microsoft Azure varsa çözüm hızlandırıcıyı oluşturabilir miyim?

> [!NOTE]
> DreamSpark için Microsoft Azure artık öğrenciler için Microsoft Imagine olarak bilinir.

Şu anda, bir [DreamSpark hesabı için Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) bir çözüm Hızlandırıcısı oluşturamazsınız. Ancak, bir çözüm Hızlandırıcısı oluşturmanıza izin veren birkaç dakikada [Azure için ücretsiz bir deneme hesabı](https://azure.microsoft.com/free/) oluşturabilirsiniz.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Nasıl yaparım? bir Azure AD kiracısı silinsin mi?

[Azure AD kiracısı silme hakkında](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)bilgi için bkz. Eric Golpe 'nin blog gönderisi Kılavuzu.

### <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılarının diğer özellik ve yeteneklerinden bazılarını da keşfedebilirsiniz:

* [Bağlı fabrika çözüm Hızlandırıcısını dağıtma](quickstart-connected-factory-deploy.md)
* [Baştan sona IoT güvenliği](../iot-fundamentals/iot-security-ground-up.md)