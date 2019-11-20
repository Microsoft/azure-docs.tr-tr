---
title: Tehdit zekası & güvenlik uyarısı eşlemesi-Azure Güvenlik Merkezi
description: Azure Güvenlik Merkezi 'nde güvenlik uyarısı Haritası ve tehdit bilgileri özelliğini kullanarak VM 'lerinizdeki ve bilgisayarlarınızdaki olası tehditleri nasıl belirleyebileceğinizi öğrenin.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 97975d72214426907a2ab91f0d3cd98d0ce6734b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693494"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Güvenlik uyarıları haritası ve tehdit bilgileri
Bu makale, güvenlikle ilgili sorunları gidermek için Azure Güvenlik Merkezi güvenlik uyarıları haritasını ve güvenlik olayı tabanlı tehdit zekası haritasını kullanmanıza yardımcı olur.

> [!NOTE]
> Güvenlik *olayları* eşleme düğmesi 31 Temmuz 2019 tarihinde kullanımdan kaldırılmıştır. Daha fazla bilgi ve diğer hizmetler için bkz. [Güvenlik Merkezi özelliklerini devre dışı bırakma (2019 Temmuz)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Güvenlik uyarıları eşleme nasıl kullanılır?
Güvenlik Merkezi, ortama yönelik güvenlik tehditlerini belirlemenize yardımcı olan bir harita sağlar. Örneğin, belirli bir bilgisayarın botnet 'in bir parçası olup olmadığını ve tehdidi nereden geldiğini belirleyebilirsiniz. Saldırganlar bir botnet içinde, botnet 'i yöneten komut ve denetimle gizlice etkileşimde bulunan bir kötü amaçlı yazılım yasa dışı yollarla yükleyen bilgisayarlar olabilir. 

Güvenlik Merkezi bu eşlemeyi oluşturmak için Microsoft içindeki birden fazla kaynaktan gelen verileri kullanır. Güvenlik Merkezi bu verileri ortamınıza karşı olası tehditleri eşlemek için kullanır. 

[Güvenlik olayı yanıt süreci](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) adımlarından biri, tehlike altındaki sistemlerin önem derecesini tanımlamaktır. Bu aşamada aşağıdaki görevleri gerçekleştirmeniz gerekir:

- Saldırının yapısını belirleme.
- Saldırının kaynak noktasını belirleme.
- Saldırının amacını belirleme. Saldırı kuruluşunuza belirli bilgileri elde etme amacıyla mı yapıldı yoksa rastgele bir saldırı mıydı?
- Gizliliği tehlikeye girmiş sistemleri tanımlayın.
- Erişilen dosyaları ve bu dosyaların gizlilik düzeyini tanımlayın.

Bu görevlerle ilgili yardım almak için Güvenlik Merkezi 'ndeki güvenlik uyarıları haritasını kullanabilirsiniz.

## <a name="access-the-security-alerts-map"></a>Güvenlik uyarıları eşlemesine erişin
Ortamınızdaki geçerli tehditleri görselleştirmek için güvenlik uyarıları haritasını açın:

1. **Güvenlik Merkezi** panosunu açın.
2. Sol bölmede **tehdit koruması** altında **güvenlik uyarıları eşlemesi**' ni seçin. Harita açılır.
3. Uyarı ve düzeltme adımlarını alma hakkında daha fazla bilgi edinmek için, haritadaki uyarı noktası ' na tıklayın ve yönergeleri izleyin. 
 
Güvenlik uyarıları eşlemesi uyarılara dayalıdır. Bu uyarılar, IP adresinin, bilinen bir riskli IP adresi (örneğin, bilinen bir cryptominer) veya tanınmayan bir IP adresi olup olmadığı, başarıyla çözümlenmiş bir IP adresiyle ilişkilendirildiği etkinlikleri temel alır. daha önce riskli olarak. Eşleme, Azure 'da daha önce seçtiğiniz abonelikler arasında uyarılar sağlar. 

Haritadaki uyarılar, kaynağından kaynaklanan olarak algıladığı coğrafi konuma göre görüntülenir ve önem derecesine göre renk kodludur. 
    ![Threat Intelligence bilgilerini](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Ayrıca bkz.
Bu makalede Güvenlik Merkezi'ndeki tehdit bilgilerini kullanarak şüpheli etkinlikleri tanımlamayı öğrendiniz. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Güvenlik Merkezi'nde uyarıları yönetme ve güvenlik olaylarına yanıt vermeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md). Azure kaynaklarınızı durumunu izleme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'ndeki güvenlik uyarılarını anlama](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Farklı güvenlik uyarısı türleri hakkında bilgi edinin.
* [Azure Güvenlik Merkezi sorun giderme kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Güvenlik Merkezi’nde sık karşılaşılan sorunları gidermeyi öğrenin.
* [Azure Güvenlik Merkezi SSS](security-center-faq.md). Hizmet kullanımı ile ilgili sık sorulan soruların yanıtlarını bulun.
* [Azure güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/). Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulun.
