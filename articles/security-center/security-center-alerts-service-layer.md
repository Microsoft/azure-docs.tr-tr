---
title: Azure hizmet katmanı tehdit algılama-Azure Güvenlik Merkezi
description: Bu konu başlığı altında, Azure Güvenlik Merkezi 'nde bulunan Azure hizmet katmanı uyarıları sunulmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: b82eab9d20966ddd0678c9213bf25a14b5313f58
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686460"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde Azure hizmet katmanı için tehdit algılama

Bu konu, aşağıdaki Azure hizmeti katmanlarını izlerken kullanılabilen Azure Güvenlik Merkezi uyarılarını gösterir:

* [Azure ağ katmanı](#network-layer)
* [Azure Yönetim Katmanı (Azure Resource Manager) (Önizleme)](#management-layer)

>[!NOTE]
>Aşağıdaki analiz tüm kaynak türleri için geçerlidir. Azure iç akışlarına dokunarak Güvenlik Merkezi 'nin sağladığı telemetrisi kullanır.

## Azure ağ katmanı<a name="network-layer"></a>

Güvenlik Merkezi ağ katmanı analizi, Azure çekirdek yönlendiricileri tarafından toplanan paket üstbilgileri olan örnek [ıpdüzeltme verilerini](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)temel alır. Bu veri akışına bağlı olarak, güvenlik merkezi makine öğrenimi modelleri kötü amaçlı trafik etkinliklerini belirler ve işaretlerdir. Güvenlik Merkezi, IP adreslerini zenginleştirmek için Microsoft Threat Intelligence veritabanını kullanır.

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Şüpheli giden RDP ağ etkinliği**|Örneklenmiş ağ trafiği analizi, dağıtımınızdaki bir kaynaktan kaynaklanan anormal giden Uzak Masaüstü Protokolü (RDP) iletişimi algıladı. Bu etkinlik, bu ortam için anormal olarak kabul edilir. Bu, kaynağınızın güvenliğinin aşıldığını gösterebilir ve artık bir dış RDP uç noktasına deneme yanılma saldırısı için kullanılmaktadır. Bu tür bir etkinliğin, IP 'nizin dış varlıklar tarafından kötü amaçlı olarak işaretlenmesine neden olabileceğini unutmayın.|
|**Birden çok hedefe şüpheli giden RDP ağ etkinliği**|Örnek ağ trafiği analizi, dağıtımınızdaki bir kaynaktan birden çok hedefe ait olan anormal giden RDP iletişimi algıladı. Bu etkinlik, bu ortam için anormal olarak kabul edilir. Bu, kaynağınızın güvenliğinin aşıldığını gösterebilir ve bu durumda, artık dış RDP uç noktalarına deneme saldırısı için kullanılmaktadır. Bu tür bir etkinliğin, IP 'nizin dış varlıklar tarafından kötü amaçlı olarak işaretlenmesine neden olabileceğini unutmayın.|
|**Şüpheli giden SSH ağ etkinliği**|Örneklenmiş ağ trafiği analizi, dağıtımınızdaki bir kaynaktan kaynaklanan anormal giden Secure Shell (SSH) iletişimi algıladı. Bu etkinlik, bu ortam için anormal olarak kabul edilir. Bu, kaynağınızın güvenliğinin aşıldığını gösterebilir ve artık bir dış SSH uç noktasına deneme yanılma saldırısı için kullanılmaktadır. Bu tür bir etkinliğin, IP 'nizin dış varlıklar tarafından kötü amaçlı olarak işaretlenmesine neden olabileceğini unutmayın.|
|**Birden çok hedefe şüpheli giden SSH ağ etkinliği**|Örnek ağ trafiği analizi, dağıtımınızdaki bir kaynaktan birden çok hedefe ait olan anormal giden SSH iletişimi algıladı. Bu etkinlik, bu ortam için anormal olarak kabul edilir. Bu, kaynağınızın güvenliğinin aşıldığını gösterebilir ve bu durumda, artık dış SSH uç noktaları deneme yanılma saldırısı için kullanılıyor olabilir. Bu tür bir etkinliğin, IP 'nizin dış varlıklar tarafından kötü amaçlı olarak işaretlenmesine neden olabileceğini unutmayın.|
|**Birden çok kaynaktan şüpheli gelen SSH ağ etkinliği**|Örneklenmiş ağ trafiği analizi, birden çok kaynaktan dağıtımınızdaki bir kaynağa anormal gelen SSH iletişimleri algıladı. Bu ortamda, kaynağına bağlanan çeşitli benzersiz IP 'Ler anormal olarak kabul edilir. Bu etkinlik, birden çok ana bilgisayardan (botnet) SSH arabiriminize bir deneme yanılma girişimi olduğunu gösterebilir.|
|**Şüpheli gelen SSH ağ etkinliği**|Örneklenmiş ağ trafiği analizi, dağıtımınızdaki bir kaynağa anormal gelen SSH iletişimi algıladı. Bu ortamda, kaynağına görece yüksek sayıda gelen bağlantı, bu ortam için anormal olarak kabul edilir. Bu etkinlik, SSH arabiriminize bir deneme yanılma saldırısı denemesi olduğunu gösterebilir.
|**Birden çok kaynaktan şüpheli gelen RDP ağ etkinliği**|Örneklenmiş ağ trafiği analizi, birden çok kaynaktan dağıtımınızdaki bir kaynağa anormal gelen RDP iletişimleri algıladı. Bu ortamda, kaynağına bağlanan çeşitli benzersiz IP 'Ler anormal olarak kabul edilir. Bu etkinlik, birden çok ana bilgisayardan (botnet) RDP arabiriminize bir deneme yanılma girişimi olduğunu gösterebilir.|
|**Şüpheli gelen RDP ağ etkinliği**|Örneklenmiş ağ trafiği analizi, dağıtımınızdaki bir kaynağa anormal gelen RDP iletişimi algıladı. Bu ortamda, kaynağına görece yüksek sayıda gelen bağlantı, bu ortam için anormal olarak kabul edilir. Bu etkinlik, SSH arabiriminize bir deneme yanılma saldırısı denemesi olduğunu gösterebilir.|
|**Kötü amaçlı bir adresle ağ iletişimi algılandı**|Örneklenmiş ağ trafiği analizi, olası bir komut ve denetim (C & C) sunucusuyla dağıtımınızdaki bir kaynaktan kaynaklanan iletişimi algıladı. Bu etkinlik türünün, IP’nizin dış varlıklar tarafından kötü amaçlı olarak işaretlenmesine neden olabileceğini unutmayın.|

Güvenlik Merkezi 'nin tehdit koruması uygulamak için ağla ilgili sinyalleri nasıl kullanabileceğini anlamak için bkz. [Azure Güvenlik Merkezi 'Nde BULUŞSAL DNS algılamaları](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Azure Güvenlik Merkezi 'nde Azure ağ katmanı tehdit algılama uyarıları yalnızca, şüpheli bir iletişimin gerçekleştiği saatin tamamında aynı IP adresi atanmış sanal makinelerde oluşturulur. Bu, sanal makinelerin yanı sıra, yönetilen hizmetin bir parçası olarak müşterinin aboneliğinde oluşturulan sanal makineler için de geçerlidir (örneğin, AKS, Databricks).

## Azure Yönetim Katmanı (Azure Resource Manager) (Önizleme)<a name ="management-layer"></a>

>[!NOTE]
>Azure Resource Manager tabanlı güvenlik merkezi koruma katmanı Şu anda önizleme aşamasındadır.

Güvenlik Merkezi, Azure için denetim düzlemi olarak kabul edilen Azure Resource Manager olaylarını kullanarak ek bir koruma katmanı sunar. Güvenlik Merkezi, Azure Resource Manager kayıtlarını analiz ederek Azure abonelik ortamında olağandışı veya potansiyel olarak zararlı işlemleri algılar.

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Mikro patlama araç seti çalıştırması**|Ortamınızda bilinen bir Cloud-Environment keşif Toolkit çalıştırması algılandı. Araç [mikro patlama](https://github.com/NetSPI/MicroBurst) , aboneliklerinin kaynaklarını eşlemek, güvenli olmayan konfigürasyonları belirlemek ve gizli bilgileri sızan bir saldırgan (veya sızma Sınayıcısı) tarafından kullanılabilir.|
|**Azurite araç seti çalıştırma**|Ortamınızda bilinen bir Cloud-Environment keşif Toolkit çalıştırması algılandı. Aracı [Azurite](https://github.com/mwrlabs/Azurite) , aboneliklerinin kaynaklarını eşlemek ve güvenli olmayan yapılandırmaların tanımlanması için bir saldırgan (veya sızma Sınayıcısı) tarafından kullanılabilir.|
|**Etkin olmayan bir hesap kullanan şüpheli yönetim oturumu**|Abonelik etkinlik günlükleri Analizi şüpheli davranış algıladı. Uzun bir süre boyunca kullanımda olmayan bir sorumlu, bir saldırgan için kalıcılığı güvenli hale getirmeye yönelik eylemler gerçekleştiriyor.|
|**PowerShell kullanarak şüpheli yönetim oturumu**|Abonelik etkinlik günlükleri Analizi şüpheli davranış algıladı. Abonelik ortamını yönetmek için düzenli olarak PowerShell kullanmayan bir sorumlu artık PowerShell kullanıyor ve bir saldırgan için kalıcılığı güvenli hale getirmenize yönelik eylemler gerçekleştiriyor.|
|**Gelişmiş Azure Kalıcılık teknikleri kullanımı**|Abonelik etkinlik günlükleri Analizi şüpheli davranış algıladı. Özelleştirilmiş rollere, yasallı kimlik varlıkları verildi. Bu, saldırganın bir Azure müşteri ortamında kalıcılığı sağlamasına yol açabilir.|
|**Seyrek bir ülkeden etkinlik**|Kuruluştaki herhangi bir kullanıcı tarafından kısa süre önce veya hiç ziyaret edilmemiş bir konumdan gelen etkinlik oluştu.<br/>Bu algılama, geçmiş etkinlik konumlarını göz önünde bulundurur ve yeni ve seyrek bulunan konumları belirlemektir. Anomali algılama altyapısı, kuruluştaki kullanıcılar tarafından kullanılan önceki konumlara ilişkin bilgileri depolar. 
|**Anonim IP adreslerinden etkinlik**|Anonim bir proxy IP adresi olarak tanımlanmış bir IP adresinden gelen kullanıcılar etkinliği algılandı. <br/>Bu proxy 'ler, cihazının IP adreslerini gizlemek isteyen kişiler tarafından kullanılır ve kötü amaçlı olarak kullanılabilir. Bu algılama, kuruluştaki kullanıcılar tarafından yaygın olarak kullanılan hatalı etiketli IP adresleri gibi hatalı pozitif sonuçları azaltan bir makine öğrenimi algoritması kullanır.|
|**Mümkün olmayan seyahat algılandı**|Coğrafi olarak uzak konumlardan kaynaklanan iki kullanıcı etkinliği (tek veya birden çok oturumunda) oluştu. Bu, kullanıcının ilk konumdan ikincisine gitmesini sağlamak için geçen süreden daha kısa bir süre içinde gerçekleşir. Bu, farklı bir kullanıcının aynı kimlik bilgilerini kullandığını gösterir. <br/>Bu algılama, kuruluştaki diğer kullanıcılar tarafından düzenli olarak kullanılan VPN 'Ler ve konumlar gibi imkansız seyahat koşullarına katkıda bulunan açık olmayan hatalı pozitif durumları yok sayan bir makine öğrenimi algoritması kullanır. Algılama işlemi, yedi günlük ilk öğrenme dönemine sahiptir ve bu sırada yeni bir kullanıcının etkinlik modelini öğrenir.|

>[!NOTE]
> Yukarıdaki analizler Microsoft Cloud App Security tarafından desteklenmektedir. Bu analizlerden yararlanmak için bir Cloud App Security lisansını etkinleştirmeniz gerekir. Cloud App Security lisansınız varsa, bu uyarılar varsayılan olarak etkinleştirilir. Devre dışı bırakmak için:
>
> 1. **Güvenlik Merkezi** dikey penceresinde **güvenlik ilkesi**' ni seçin. Değiştirmek istediğiniz abonelik için **Ayarları Düzenle**' yi seçin.
> 2. **Tehdit algılamayı**seçin.
> 3. **Tümleştirmelere**izin ver ' in altında, **verilerinize erişim Microsoft Cloud App Security izin ver**' i temizleyin ve **Kaydet**' i seçin

>[!NOTE]
>Güvenlik Merkezi, güvenlikle ilgili müşteri verilerini kaynakla aynı coğrafi bölgede depolar. Microsoft, kaynak coğrafi bölgede henüz Güvenlik Merkezi dağıtmamışsa, verileri Birleşik Devletler depolar. Cloud App Security etkinleştirildiğinde, bu bilgiler Cloud App Security coğrafi konum kurallarına uygun olarak depolanır. Daha fazla bilgi için bkz. [bölgesel olmayan hizmetler Için veri depolama](https://azuredatacentermap.azurewebsites.net/).
