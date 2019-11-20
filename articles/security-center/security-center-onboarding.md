---
title: Standart katmanın gelişmiş güvenliği-Azure Güvenlik Merkezi
description: " Gelişmiş güvenlik için Azure Güvenlik Merkezi standardına ekleme hakkında bilgi edinin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: db74415d3c6c5f6636a150d212bc2bc3d790fbec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686389"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Gelişmiş güvenlik için Azure Güvenlik Merkezi standardına ekleme
Karma bulut iş yükleriniz için gelişmiş güvenlik yönetimi ve tehdit koruması avantajlarından yararlanmak için Güvenlik Merkezi standardına yükseltin. Standart ücretsiz deneyebilirsiniz. Daha fazla bilgi için Güvenlik Merkezi [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/security-center/) bakın.

Güvenlik Merkezi Standart şunları içerir:

- **Karma güvenlik** : tüm şirket içi ve bulut iş yüklerinizde güvenliğin birleştirilmiş bir görünümünü alın. Güvenlik standartları ile uyumluluğu sağlamak için güvenlik ilkelerini uygulayın ve hibrit bulut iş yüklerinizin güvenliğini sürekli değerlendirin. Güvenlik duvarları ve diğer iş ortaklarının çözümleri gibi farklı kaynaklardan güvenlik verileri toplayın, bunlar üzerinde arama ve analiz gerçekleştirin.
- **Gelişmiş tehdit algılama** -gelişmiş analizler ve Microsoft Intelligent Security Graph gelişen Cyber saldırılarına karşı bir kenar almak için kullanın. Yerleşik davranış analizi ve makine öğrenimi özelliklerinden yararlanarak saldırıları ve sıfır gün saldırılarına yol açabilecek güvenlik açıklarını tespit edin. Ağları, makineleri ve bulut hizmetlerini gelen saldırılara veya güvenlik ihlali sonrası etkinliklere karşı izleyin. Etkileşimli araçlar ve bağlama dayalı tehdit zekası ile araştırmaları kolaylaştırın.
- **Erişim ve uygulama denetimleri** -belirli iş yüklerinize ve makine öğrenimine göre destekli, beyaz listeye ekleme önerileri uygulayarak kötü amaçlı yazılımları ve diğer istenmeyen uygulamaları engelleyin. Azure VM 'lerinde yönetim bağlantı noktalarına tam zamanında, denetimli erişime sahip ağ saldırısı yüzeyini azaltarak, deneme yanılma ve diğer ağ saldırılarına maruz kalma olasılığını büyük ölçüde azaltır.

## <a name="detecting-unprotected-resources"></a>Korumasız kaynakları algılama
Güvenlik Merkezi, Güvenlik Merkezi Standart sürümü için etkinleştirilmemiş herhangi bir Azure aboneliğini veya çalışma alanını otomatik olarak algılar. Buna, Güvenlik Merkezi Ücretsiz sürümünü kullanan Azure abonelikleri ve etkin bir Güvenlik çözümü olmayan çalışma alanları dahildir.

Tüm Azure aboneliğini, abonelik dahilindeki tüm desteklenen kaynaklar tarafından devralınan Standart katmana yükseltebilirsiniz. Standart katmanın bir çalışma alanına uygulanması, çalışma alanına rapor veren tüm kaynaklar için geçerlidir.

> [!NOTE]
> Maliyetlerinizi yönetmek ve bir çözüm için toplanan veri miktarını belirli bir aracı kümesiyle sınırlayarak sınırlamak isteyebilirsiniz. [Çözüm hedefleme](../operations-management-suite/operations-management-suite-solution-targeting.md) , çözüme bir kapsam uygulamanıza ve çalışma alanındaki bilgisayarların bir alt kümesini hedeflemenizi sağlar. Çözüm hedefleme kullanıyorsanız, güvenlik merkezi çalışma alanını bir çözüme sahip değil olarak listeler.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Azure aboneliğini veya çalışma alanını yükseltme
Bir aboneliği veya çalışma alanını standart olarak yükseltmek için:
1. Güvenlik Merkezi ana menüsü altında, **Başlarken**’i seçin.
  ![Başlangıç](./media/security-center-onboarding/get-started.png)
2. **Yükselt** altında, Güvenlik Merkezi, ekleme işlemi için uygun abonelikleri ve çalışma alanlarını listeler. 
   - Tüm abonelikleri ve çalışma alanlarını deneme sürümü uygunluk durumlarıyla birlikte listelemek için, genişletilebilir **Deneme sürümünüzü uygulayın**’a tıklayabilirsiniz.
   -    Deneme sürümü için uygun olmayan abonelikler ve çalışma alanlarını yükseltebilirsiniz.
   -    Deneme sürümünüzü başlatmak için uygun çalışma alanlarını ve abonelikleri seçebilirsiniz.
3.  Seçili aboneliklerde deneme sürümünüzü başlatmak için **Deneme sürümünü başlat**’a tıklayın.
  abonelik ![seçin](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Güvenlik Merkezi 'nin ücretsiz özellikleri yalnızca Azure VM 'lerinize ve VMSS 'ye uygulanır. Ücretsiz yetenekler Azure dışı bilgisayarlarınıza uygulanmaz. Standart ' ı seçerseniz, standart yetenekler, çalışma alanına rapor veren tüm Azure VM 'lerine, VM Ölçek kümelerine ve Azure dışı bilgisayarlara uygulanır. Azure ve Azure dışı kaynaklarınız için gelişmiş güvenlik sağlamak üzere standart uygulamanızı öneririz.
   >

## <a name="onboard-non-azure-computers"></a>Azure dışı bilgisayarlar ekleme
Güvenlik Merkezi, Azure dışı bilgisayarların güvenlik durumunu izleyebilir ancak öncelikle bu kaynakları eklemeniz gerekir. Azure olmayan bilgisayarları, **Başlarken** dikey penceresinden veya **işlem** dikey penceresinden ekleyebilirsiniz. Her iki yöntemde de izlenecek.

### <a name="add-new-non-azure-computers-from-getting-started"></a>**Başlarken** 'Den yeni Azure dışı bilgisayarlar ekleyin

1. **Başlarken**'e geri dönün.
2. **Başlangıç** sekmesini seçin.

   ![Azure dışı](./media/security-center-onboarding/non-azure.png)

3. **Yeni Azure dışı bilgisayarlar ekle** altında, **Yapılandır**’a tıklayın. Log Analytics çalışma alanlarınızın bir listesi gösterilir. Listede, varsa, otomatik sağlama etkinleştirildiğinde Güvenlik Merkezi tarafından sizin için oluşturulan varsayılan çalışma alanı bulunur. Bu çalışma alanını veya kullanmak istediğiniz başka bir çalışma alanını seçin.

   ![Azure dışı bilgisayar ekleme][7]

Mevcut çalışma alanlarınız varsa, bunlar **yeni Azure dışı bilgisayarlar Ekle**altında listelenir. Mevcut bir çalışma alanına bilgisayar ekleyebilir veya yeni bir çalışma alanı oluşturabilirsiniz. Yeni bir çalışma alanı oluşturmak için **Yeni bir çalışma alanı Ekle**bağlantısını seçin.

### <a name="add-new-non-azure-computers-from-compute"></a>**İşlem** sonrasında yeni Azure dışı bilgisayarlar ekleme

**Yeni bir çalışma alanı oluşturun ve bilgisayar ekleyin**

1. **Azure dışı yeni bilgisayarlar Ekle**altında **Yeni bir çalışma alanı Ekle**' yi seçin.

   ![Yeni bir çalışma alanı Ekle][4]

2. **Güvenlik ve denetim**altında, yeni bir çalışma alanı oluşturmak Için **OMS çalışma alanı** ' nı seçin.
   > [!NOTE]
   > OMS çalışma alanları artık Log Analytics çalışma alanları olarak adlandırılır.
3. **OMS çalışma alanı**altında, çalışma alanınızın bilgilerini girin.
4. **OMS çalışma alanı**altında **Tamam**' ı seçin. Tamam ' ı seçtikten sonra, aracı yapılandırma bölümünde kullanmak üzere çalışma alanı KIMLIĞINIZ için bir Windows veya Linux Aracısı ve anahtarlar indirmek üzere bir bağlantı alacaksınız.
5. **Güvenlik ve denetim**altında **Tamam**' ı seçin.

**Mevcut bir çalışma alanını seçin ve bilgisayar ekleyin**

Daha önce gösterildiği gibi, **ekleme**işleminden sonra iş akışını izleyerek bir bilgisayar ekleyebilirsiniz. Ayrıca, iş akışını **işlem**akışından izleyerek de bir bilgisayar ekleyebilirsiniz. Bu örnekte, **işlem**kullanırız.

1. Güvenlik Merkezi 'nin ana menüsüne ve **genel bakış** panosuna geri dönün.

   ![Genel Bakış][5]

2. **İşlem & uygulamalar**' ı seçin.
3. **İşlem & uygulamalar**altında **Bilgisayar Ekle**' yi seçin.

   ![Bilgi İşlem dikey penceresi][6]

4. **Azure olmayan yeni bilgisayarlar Ekle**' nin altında, bilgisayarınızı bağlamak için bir çalışma alanı seçin ve bilgisayar **Ekle**' ye tıklayın.

   ![Bilgisayar Ekle][7]

   **Doğrudan aracı** dikey penceresi, bir Windows veya Linux aracısının yanı sıra aracıyı yapılandırmada kullanılacak çalışma alanı kimliğini ve anahtarlarını indirmek için bir bağlantı sağlar.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, güvenlik merkezi 'nin gelişmiş güvenlik avantajlarından yararlanmak için Azure ve Azure dışı kaynakları nasıl kullanabileceğiniz hakkında daha fazla kaynak bulabilirsiniz. Eklendi kaynaklarınız hakkında daha fazla bilgi için bkz.

- [Veri toplamayı etkinleştirme](security-center-enable-data-collection.md)
- [Tehdit zekası raporu](security-center-threat-report.md)
- [Tam zamanında VM erişimi](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
