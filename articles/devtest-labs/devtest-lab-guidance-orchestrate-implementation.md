---
title: Azure DevTest Labs uygulamasını düzenleme
description: Bu makalede, kuruluşunuzda Azure DevTest Labs uygulanmasını düzenleyen rehberlik sunulmaktadır.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330623"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Azure DevTest Labs uygulanmasını düzenleme
Bu makalede Azure DevTest Labs hızlı dağıtım ve uygulama için önerilen bir yaklaşım sunulmaktadır. Aşağıdaki görüntüde, çeşitli sektör gereksinimlerini ve senaryolarını desteklemeye yönelik esnekliği gözlemlerken, genel işlemi öngörülü rehberlik olarak vurgulamaktadır.

![Azure DevTest Labs uygulama adımları](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Varsayımlar
Bu makalede, bir DevTest Labs pilot uygulamadan önce aşağıdaki öğelerin yerinde olduğu varsayılır:

- **Azure aboneliği**: pilot ekibin Azure aboneliğine kaynak dağıtmaya erişimi vardır. İş yükleri yalnızca geliştirme ve test ise, Windows sanal makinelerinde kullanılabilen ek görüntüler ve daha düşük ücretler için Enterprise DevTest teklifini seçmeniz önerilir.
- Şirket **Içi erişim**: gerekirse şirket içi erişim zaten yapılandırılmıştır. Şirket içi erişim, siteden siteye VPN bağlantısı aracılığıyla veya Express Route aracılığıyla gerçekleştirilebilir. Hızlı rota aracılığıyla bağlantı kurmak için genellikle birkaç hafta sürebilir, projenin başlatılması için hızlı yolun yerine gelmesi önerilir.
- **Pilot takımlar**: DevTest Labs kullanan ilk geliştirme projesi ekibi, geçerli geliştirme veya test etkinlikleriyle birlikte tanımlanmıştır ve bu takımlar için gereksinimleri/hedefleri/amaçları oluştururlar.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Kilometre taşı 1: ilk ağ topolojisini ve tasarımı oluşturma
Azure DevTest Labs bir çözüm dağıtıldığında odağın ilk alanı, sanal makineler için planlı bağlantı kurmaya yöneliktir. Aşağıdaki adımlarda gereken yordamlar ana hatlarıyla verilmiştir:

1. Azure 'da DevTest Labs aboneliğine atanan **Ilk IP adresi aralıklarını** tanımlayın. Bu adım, gelecekteki genişlemeye yetecek kadar büyük bir blok sağlayabilmeniz için VM sayısında beklenen kullanımı tahmin etmek için gereklidir.
2. DevTest Labs 'e **istenen erişim yöntemlerini** (örneğin, dış/iç erişim) belirler. Bu adımdaki bir anahtar noktası, sanal makinelerin genel IP adreslerine sahip olup olmadığını belirlemektir (yani, internet 'ten doğrudan erişilebilir).
3. Azure bulut ortamı ve şirket içi geri kalanı ile **bağlantı yöntemlerini** belirleyin ve oluşturun. ExpressRoute ile zorunlu yönlendirme etkinse, sanal makinelerin kurumsal güvenlik duvarında geçiş yapmak için uygun ara sunucu yapılandırmalarına ihtiyacı vardır.
4. VM 'Ler **etki alanına katılmış** ise, bulut tabanlı bir etki alanına (örneğin AAD Dizin Hizmetleri) veya şirket içi etki alanına katılıp katılmayacağını saptayın. Şirket içi için, Active Directory içindeki sanal makinelerin katıldığı kurumsal birimi (OU) saptayın. Ayrıca, kullanıcıların katılmayı (veya etki alanında makine kayıtları oluşturma yeteneğine sahip bir hizmet hesabı oluşturmak için) erişimi olduğunu doğrulayın.

## <a name="milestone-2-deploy-the-pilot-lab"></a>Kilometre taşı 2: pilot Laboratuvarı dağıtma
Ağ topolojisi yerleştirildikten sonra, ilk/pilot laboratuvar aşağıdaki adımları uygulayarak oluşturulabilir:

1. İlk DevTest Labs ortamı oluşturun.
2. Laboratuvar ile kullanım için izin verilen VM görüntülerini ve boyutlarını belirleme. Özel görüntülerin DevTest Labs ile kullanılmak üzere Azure 'a yüklenip yüklenemeyeceğine karar verin.
3. Laboratuvar (Laboratuvar sahipleri ve laboratuar kullanıcıları) için ilk Azure rol tabanlı erişim denetimi (Azure RBAC) oluşturarak laboratuvara güvenli erişim. DevTest Labs ile kimlik için Azure Active Directory ile eşitlenmiş Active Directory hesaplarını kullanmanızı öneririz.
4. Planlar, maliyet yönetimi, çakışan VM 'Ler, özel görüntüler veya formüller gibi ilkeleri kullanmak için DevTest Labs 'i yapılandırın.
5. Azure Repos/git gibi bir çevrimiçi depo oluşturun.
6. Ortak veya özel depoların ya da ikisinin birleşiminin kullanımına karar verin. Dağıtım ve uzun süreli sürdürülebilirlik için JSON şablonlarını düzenleyin.
7. Gerekirse, özel yapıtlar oluşturun. Bu adım isteğe bağlıdır. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Kilometre taşı 3: belge, destek, öğrenme ve geliştirme
İlk pilot takımlar Başlarken desteği gerektirebilir. Azure DevTest Labs 'nin sürekli dağıtımı için doğru belge ve desteğin yapıldığından emin olmak için deneyimlerini kullanın.

1. Pilot ekipleri yeni DevTest Labs kaynaklarına (tanıtımlar, belgeler) tanıtın
2. Pilot ekiplerin deneyimlerini temel alarak gerektiğinde belgeleri planlayın ve sunun
3. Yeni takımları ekleme (Laboratuvar oluşturma ve yapılandırma, erişim sağlama vb.) için şekilleştir işlemi
4. İlk al 'ı temel alarak, IP adresi alanının orijinal tahminlerinin hala makul ve doğru olduğunu doğrulayın
5. Uygun uyumluluk ve güvenlik incelemelerinin tamamlandığından emin olun

## <a name="next-steps"></a>Sonraki adımlar
Bu serinin sonraki makalesine bakın: [Azure DevTest Labs altyapısını idare](devtest-lab-guidance-governance-resources.md) edin
