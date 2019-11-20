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
ms.openlocfilehash: b448099dab0e79dca38310396896be46cf2e0ff6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826268"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT Çözüm Hızlandırıcıları için sık sorulan sorular

Ayrıca bkz. [bağlı fabrikada özgü SSS](iot-accelerators-faq-cf.md) ve [uzaktan izlemeye özgü SSS](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Çözüm hızlandırıcıları için kaynak kodunu nerede bulabilirim?

Kaynak kodu aşağıdaki GitHub depolarında depolanır:

* [Uzaktan İzleme çözümü hızlandırıcısı (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Uzaktan İzleme çözümü hızlandırıcısı (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Tahmine dayalı bakım çözüm hızlandırıcısı](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Bağlı Fabrika çözüm hızlandırıcısı](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Çözüm hızlandırıcılarına yönelik cihaz istemcileri geliştirmek için hangi SDK’ları kullanabilirim?

Farklı dilin (C, .NET, Java, Node. js, Python) IoT cihaz SDK 'larının bağlantılarını [Microsoft Azure IoT SDK 'ları](https://github.com/Azure/azure-iot-sdks) GitHub depolarında bulabilirsiniz.

DevKit cihazını kullanıyorsanız, [IoT DEVKIT SDK](https://github.com/Microsoft/devkit-sdk) GitHub deposunda kaynakları ve örnekleri bulabilirsiniz.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Yeni mikro hizmet mimarisi, üç Çözüm Hızlandırıcısı için de kullanılabilir mi?

Şu anda, en geniş senaryoyu kapsadığından yalnızca uzaktan Izleme çözümü mikro hizmet mimarisini kullanır.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Yeni bir açık kaynak mikro hizmet tabanlı mimarinin yeni güncelleştirmede sağladığı avantajlar nelerdir?

Son iki yılda, bulut mimarisi büyük ölçüde gelişmiştir. Mikro hizmetler, geliştirme hızından ödün vermeden ölçek ve esneklik elde etmek için harika bir model olarak ortaya çıktı. Bu mimari model, çok sayıda Microsoft hizmetine şirket içinde harika güvenilirlik ve ölçeklenebilirlik sonuçlarıyla birlikte kullanılır. Microsoft bu dersleri ' i çözüm hızlandırıcılarına yerleştirilerek müşterilerin bundan faydalanır.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Bir hizmet yöneticiyim ve aboneliğim ile belirli bir Azure AD kiracısı arasındaki dizin eşlemesini değiştirmek istiyorum. Nasıl yaparım? bu görevi tamamlayacak mı?

[Azure AD dizininize mevcut bir abonelik eklemek için](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory) bkz.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Bir kuruluş hesabıyla oturum açtıklarında bir hizmet yöneticisini veya ortak yöneticiyi değiştirmek istiyorum

[Kurumsal bir hesapla oturum açıldığında hizmet yöneticisi ve ortak yönetici değiştirme](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)destek makalesine bakın.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Bu hatayı neden görüyorum? "Hesabınız bir çözüm oluşturmak için uygun izinlere sahip değil. Lütfen hesap yöneticinize başvurun veya farklı bir hesap ile deneyin."

Rehberlik için aşağıdaki diyagrama bakın:

![İzin akış çizelgesi](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Azure AD kiracısının genel yöneticisi ve aboneliğin ortak Yöneticisi olduğunuz doğrulandıktan sonra hatayı görmeye devam ederseniz, Hesap yöneticinizin kullanıcıyı kaldırmasını ve gerekli izinleri bu sırayla yeniden atanmasını sağlayabilirsiniz. İlk olarak, kullanıcıyı genel yönetici olarak ekleyin ve ardından kullanıcıyı Azure aboneliğinin ortak Yöneticisi olarak ekleyin. Sorun devam ederse [destek & yardım 'a](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)başvurun.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Bir Azure aboneliğim varken bu hatayı neden görüyorum? "Önceden yapılandırılmış çözümler oluşturmak için bir Azure aboneliği gereklidir. Yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. "

Azure aboneliğiniz olduğundan emin değilseniz, aboneliğinizin kiracı eşlemesini doğrulayın ve açılan menüde doğru kiracının seçili olduğunu kontrol edin. Kiracının doğru olduğunu doğruladığınız takdirde, önceki diyagramı izleyin ve aboneliğinizin ve bu Azure AD kiracısının eşlemesini doğrulayın.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Uzaktan Izleme çözümünün önceki sürümüyle ilgili bilgileri nereden bulabilirim?

Uzaktan Izleme çözüm hızlandırıcısının önceki sürümü IoT Suite uzaktan Izleme çözümü olarak bilinir. Arşivlenmiş belgeleri [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)' de bulabilirsiniz.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Yeni Çözüm Hızlandırıcısı var olan çözümle aynı coğrafi bölgede kullanılabilir mi?

Evet, yeni uzaktan Izleme aynı coğrafi bölgede kullanılabilir.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Azure portal bir kaynak grubunu silme ve azureiotsolutions.com içinde çözüm hızlandırıcısında Sil ' i tıklatma arasındaki fark nedir?

* Çözüm hızlandırıcıyı [azureiotsolutions.com](https://www.azureiotsolutions.com/)' de silerseniz, çözüm hızlandırıcıyı oluştururken dağıtılan tüm kaynakları silersiniz. Kaynak grubuna ek kaynaklar eklediyseniz, bu kaynaklar da silinir.
* [Azure Portal](https://portal.azure.com)kaynak grubunu silerseniz bu kaynak grubundaki kaynakları yalnızca silersiniz. Ayrıca çözüm hızlandırıcısında ilişkili Azure Active Directory uygulamasını da silmeniz gerekir.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Azure IoT çözüm hızlandırıcılarından mevcut yatırımlarımı kullanmaya devam edebilir miyim?

Evet. Bugün mevcut olan herhangi bir çözüm, Azure aboneliğinizde çalışmaya devam eder ve kaynak kodu GitHub 'da kullanılabilir kalır.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Bir abonelikte kaç IoT Hub örneği sağlayabilirim?

Varsayılan olarak, [abonelik başına 10 IoT Hub](../azure-subscription-service-limits.md#iot-hub-limits)'ı sağlayabilirsiniz. Bu sınırı yükseltmek için bir [Azure destek bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oluşturabilirsiniz. Sonuç olarak, her çözüm Hızlandırıcısı yeni bir IoT Hub sağladığından, belirli bir abonelikte en fazla 10 çözüm hızlandırıcıyı sağlayabilirsiniz.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Bir abonelikte kaç Azure Cosmos DB örneği sağlayabilirim?

Elli. Bu sınırı yükseltmek için bir [Azure destek bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oluşturabilirsiniz, ancak varsayılan olarak abonelik başına yalnızca 50 Cosmos DB örnekleri sağlayabilirsiniz.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Bir abonelikte kaç tane Ücretsiz Bing Haritaları API'si sağlayabilirim?

İki. Bir Azure aboneliğinde Enterprise planları için yalnızca iki adet İç İşlemler Düzey 1 Bing Haritası oluşturabilirsiniz. Uzaktan Izleme çözümü, dahili Işlem düzeyi 1 planıyla birlikte varsayılan olarak sağlanır. Sonuç olarak, bir abonelikte yalnızca değişiklik yapmadan en fazla iki uzaktan Izleme çözümü sağlayabilirsiniz.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>DreamSpark için Microsoft Azure varsa çözüm hızlandırıcıyı oluşturabilir miyim?

> [!NOTE]
> DreamSpark için Microsoft Azure artık öğrenciler için Microsoft Imagine olarak bilinir.

Şu anda, bir [DreamSpark hesabı için Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) bir çözüm Hızlandırıcısı oluşturamazsınız. Ancak, bir çözüm Hızlandırıcısı oluşturmanıza izin veren birkaç dakikada [Azure için ücretsiz bir deneme hesabı](https://azure.microsoft.com/free/) oluşturabilirsiniz.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Nasıl yaparım? bir Azure AD kiracısı silinsin mi?

[Azure AD kiracısı silme hakkında](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)bilgi için bkz. Eric Golpe 'nin blog gönderisi Kılavuzu.

### <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılarının diğer özellik ve yeteneklerinden bazılarını da keşfedebilirsiniz:

* [Uzaktan Izleme çözüm hızlandırıcısının yeteneklerini keşfet](quickstart-remote-monitoring-deploy.md)
* [Tahmine Dayalı Bakım çözüm hızlandırıcısına genel bakış](iot-accelerators-predictive-overview.md)
* [Bağlı fabrika çözüm Hızlandırıcısını dağıtma](quickstart-connected-factory-deploy.md)
* [Sıfırdan IoT güvenliği](/azure/iot-fundamentals/iot-security-ground-up)
