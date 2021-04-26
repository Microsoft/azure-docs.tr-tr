---
title: Azure Sentinel 'de olay işlemeyi otomatikleştirin | Microsoft Docs
description: Bu makalede, güvenlik tehditlerine yanıt olarak SOC 'in verimliliğini ve verimliliğini en üst düzeye çıkarmak için olay işlemeyi otomatikleştirmek üzere otomasyon kurallarının nasıl kullanılacağı açıklanmaktadır.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610101"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Otomasyon kurallarıyla Azure Sentinel 'de olay işlemeyi otomatikleştirme

Bu makalede, Azure Sentinel Otomasyon kurallarının ne olduğu ve güvenlik düzenleme, otomasyon ve yanıt (SOAR) işlemlerini uygulamak için nasıl kullanılacağı, SOC 'in verimliliğini artırma ve zaman ve kaynaklarınızın nasıl kaydedileceği açıklanmaktadır.

> [!IMPORTANT]
>
> - **Otomasyon kuralları** özelliği şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="what-are-automation-rules"></a>Otomasyon kuralları nelerdir?

Otomasyon kuralları Azure Sentinel 'de yeni bir kavramdır. Bu özellik, kullanıcıların olay işleme otomasyonunu merkezi olarak yönetmesine olanak tanır. Otomasyon kuralları, bir seferde birden fazla analiz kuralına yönelik yanıtları otomatik hale getirmenize, el ile etiketleme, atayabilir veya kapatabilir ve bu da yürütülen eylemlerin sırasını kontrol etmenizi sağlar ve bunları çalıştırabilirsiniz. Otomasyon kuralları, Azure Sentinel 'de Otomasyon kullanımını kolaylaştırır ve olay düzenleme işlemleriniz için karmaşık iş akışlarını basitleştirmenize imkan sağlar.

## <a name="components"></a>Bileşenler

Otomasyon kuralları çeşitli bileşenlerden oluşur:

### <a name="trigger"></a>Tetikleyici

Otomasyon kuralları bir olay oluşturma tarafından tetiklenir. 

Gözden geçirmek için – olaylar, [Azure Sentinel 'de yerleşik analitik kurallarla tehditleri algılayarak](tutorial-detect-threats-built-in.md), öğreticide açıklandığı şekilde, dört tür olan analiz kuralları tarafından uyarılardan oluşturulur.

### <a name="conditions"></a>Koşullar

Karmaşık koşullar kümesi, eylemlerin (aşağıya bakın) ne zaman çalışacağını yönetmek için tanımlanabilir. Bu koşullar genellikle olayların ve varlıklarındaki özniteliklerin durumlarına veya değerlerine dayanır ve `AND` / `OR` / `NOT` / `CONTAINS` işleçler içerebilir.

### <a name="actions"></a>Eylemler

Eylemler, koşullar (yukarıya bakın) karşılandığında çalıştırılacak şekilde tanımlanabilir. Bir kuralda birçok eylem tanımlayabilir ve bunların çalıştırılacağı sırayı seçebilirsiniz (aşağıya bakın). Aşağıdaki eylemler, [bir PlayBook 'ın gelişmiş işlevselliğine](automate-responses-with-playbooks.md)gerek kalmadan Otomasyon kuralları kullanılarak tanımlanabilir:

- Bir olayın durumunu değiştirerek iş akışınızı güncel tutun.

  - "Kapalı" olarak değiştirilirken, [kapatma nedenini](tutorial-investigate-cases.md#closing-an-incident) belirtme ve açıklama ekleme. Bu, performans ve verimliliğinden haberdar olmanıza ve hatalı pozitif sonuçları azaltmak için ince ayar yapmanıza yardımcı olur.

- Bir olayın önem derecesini değiştirme – olayla ilgili varlık, devamsızlık, değer veya varlıkların özniteliklerine göre yeniden önceliklendirebilir ve yeniden önceliklendirmenizi sağlayabilirsiniz.

- Bir sahibe bir olay atama – Bu, olay türlerini bunlarla veya en uygun personele en uygun personele yönlendirmenize yardımcı olur.

- Bir olaya etiket ekleme – Bu, olayları konuya, saldırgana veya diğer tüm ortak paydaya göre sınıflandırmak için yararlıdır.

Ayrıca, dış sistemleri içeren herhangi bir de dahil olmak üzere daha karmaşık yanıt eylemleri gerçekleştirmek için bir [PlayBook](tutorial-respond-threats-playbook.md)çalıştırmaya yönelik bir eylem tanımlayabilirsiniz. Yalnızca [olay tetikleyicisi](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) tarafından etkinleştirilen PlayBook 'ları 'lar Otomasyon kurallarında kullanılabilir. Birden çok PlayBook veya PlayBook 'lar ile diğer eylemlerin birleşimlerini ve bunların çalıştırılacağı sırayı dahil etmek için bir eylem tanımlayabilirsiniz.

### <a name="expiration-date"></a>Son kullanma tarihi

Bir Otomasyon kuralında bir sona erme tarihi tanımlayabilirsiniz. Bu tarihten sonra kural devre dışı bırakılacak. Bu, penme testi gibi planlanmış, zamana sınırlı etkinliklerin (yani kapatan) "gürültü" olaylarını işlemek için yararlıdır.

### <a name="order"></a>Sipariş

Otomasyon kurallarının çalışacağı sırayı tanımlayabilirsiniz. Daha sonraki Otomasyon kuralları, önceki Otomasyon kuralları tarafından işlem yapıldıktan sonra olay koşullarını değerlendirerek duruma göre değerlendirir.

Örneğin, "Ilk Otomasyon kuralı" bir olayın önem derecesini orta ile düşük olarak değiştirdiyseniz ve "Ikinci Otomasyon kuralı" yalnızca orta veya daha yüksek önem derecesine sahip olaylar üzerinde çalışmak üzere tanımlanmışsa, bu olay üzerinde çalışmaz.

## <a name="common-use-cases-and-scenarios"></a>Yaygın kullanım örnekleri ve senaryolar

### <a name="incident-triggered-automation"></a>Olay tarafından tetiklenen Otomasyon

Bu aşamada, yalnızca uyarılar PlayBook 'lar kullanılarak otomatik bir yanıt tetikleyebdir. Otomasyon kuralları sayesinde, olaylar artık bir olay oluşturulduğunda yeni olay tetiklenen PlayBook['lar (özel izinler gereklidir](#permissions-for-automation-rules-to-run-playbooks)) içerebilen otomatik yanıt zincirlerini tetikleyebilirler. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Microsoft sağlayıcıları için PlayBook 'ları tetikleyin

Otomasyon kuralları, bu kuralları uyarılardan oluşturulan olaylara uygulayarak Microsoft güvenlik uyarılarını işlemeyi otomatikleştirmek için bir yol sağlar. Otomasyon kuralları, PlayBook 'ları çağırabilir ([özel izinler gereklidir](#permissions-for-automation-rules-to-run-playbooks)) ve uyarılar ve varlıklar dahil olmak üzere tüm ayrıntılarla olayları bunlara geçirebilir. Genel olarak, Azure Sentinel en iyi uygulamaları, güvenlik işlemleri için odak noktası olarak olaylar kuyruğunu kullanmayı dikte ediyor.

Microsoft güvenlik uyarıları şunları içerir:

- Microsoft Cloud App Security (MCAS)
- Azure AD Kimlik Koruması
- Azure Defender (ASC)
- IoT için Defender (daha önce IoT için ASC)
- Office 365 için Microsoft Defender (eski adıyla Office 365 ATP)
- Uç nokta için Microsoft Defender (eski adıyla MDATP)
- Kimlik için Microsoft Defender (eski adıyla Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Tek bir kuralda birden çok sıralı PlayBook/eylem

Artık tek bir Otomasyon kuralında eylemlerin ve PlayBook yürütmesinin sırası üzerinde neredeyse tam denetime sahip olabilirsiniz. Ayrıca, Otomasyon kurallarının yürütme sırasını da kontrol edersiniz. Bu, PlayBook 'ları büyük ölçüde basitleştirmenize, bunları tek bir görevde veya küçük ve kolay bir görev dizisine düşürmenize ve bu küçük PlayBook 'ları farklı Otomasyon kurallarında farklı bileşimlerde birleştirmenize olanak tanır.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Tek seferde birden çok analiz kuralına bir PlayBook atama

Tüm analiz kurallarınız üzerinde otomatik hale getirmek istediğiniz bir göreviniz varsa (örneğin, bir dış anahtar sisteminde destek bileti oluşturma), tüm analiz kurallarınızın herhangi birine veya tümüne, bir görüntüsündeki tüm kurallar dahil olmak üzere tek bir PlayBook uygulayabilirsiniz. Bu, basit ancak yinelenen bakım ve temizlik görevlerini çok daha az bir şekilde sağlar.

### <a name="automatic-assignment-of-incidents"></a>Olayların otomatik olarak atanması

Olayları doğru Sahibe otomatik olarak atayabilirsiniz. SOC ' in belirli bir platformda uzmanlaşmış bir analisti varsa, Bu platformla ilgili tüm olaylar otomatik olarak bu analiste atanabilir.

### <a name="incident-suppression"></a>Olay gizleme

PlayBook 'ları kullanmadan, bilinen yanlış/zararsız pozitif sonuçlar olan olayları otomatik olarak çözümlemek için kurallarını kullanabilirsiniz. Örneğin, sızma testleri çalıştırırken, zamanlanmış bakım veya yükseltmeler yaparken ya da Otomasyon yordamlarını test ederken, SOC 'in yok saymak istediği birçok hatalı pozitif olay oluşturulabilir. Zaman sınırlı bir Otomasyon kuralı, oluşturulduklarında bu olayları otomatik olarak kapatabilir ve bunların neslin nedenine yönelik bir tanımlayıcı ile etiketleniyor.

### <a name="time-limited-automation"></a>Zamana sınırlı Otomasyon

Otomasyon kurallarınız için sona erme tarihleri ekleyebilirsiniz. Zamana sınırlı Otomasyonu sağlayan olay göstermeme dışında bir durum olabilir. Belirli bir zaman çerçevesi için belirli bir olay türünü belirli bir kullanıcıya (örneğin, bir Intern veya danışman) atamak isteyebilirsiniz. Zaman çerçevesi önceden biliniyorsa, kuralın bunun sonunda devre dışı kalmasına gerek kalmadan, kuralı yeniden devre dışı bırakabilirsiniz.

### <a name="automatically-tag-incidents"></a>Olayları otomatik etiketle

İstediğiniz ölçütlere göre gruplamak veya sınıflandırmak için olaylara serbest metin etiketleri otomatik olarak ekleyebilirsiniz.

## <a name="automation-rules-execution"></a>Otomasyon kuralları yürütme

Otomasyon kuralları, belirlediğiniz sıraya göre sıralı olarak çalıştırılır. Her Otomasyon kuralı, önceki bir çalıştırma çalışmasını tamamladıktan sonra yürütülür. Otomasyon kuralında, tüm eylemler tanımlandıkları sırada sırayla çalıştırılır.

PlayBook eylemleri için, PlayBook eyleminin başlangıcı ve listedeki bir sonraki eylem arasında iki dakikalık bir gecikme vardır.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>PlayBook 'ları çalıştırmak için Otomasyon kuralları izinleri

Bir Azure Sentinel Otomasyon kuralı bir PlayBook çalıştırdığında, bu eylem için özel olarak yetkilendirilmiş özel bir Azure Sentinel hizmet hesabını kullanır. Bu hesabın kullanımı (Kullanıcı hesabınıza karşılık olarak), hizmetin güvenlik düzeyini artırır.

Bir Otomasyon kuralının bir PlayBook çalıştırması için, bu hesaba PlayBook 'un bulunduğu kaynak grubuna açık izinler verilmelidir. Bu noktada, herhangi bir Otomasyon kuralı bu kaynak grubunda herhangi bir PlayBook 'u çalıştırabilecektir.

Bir Otomasyon kuralı yapılandırırken ve **PlayBook Çalıştır** eylemi eklerken, PlayBook 'lar açılır listesi görüntülenir. Azure Sentinel 'in izinleri olmayan PlayBook 'lar kullanılamıyor ("gri") olarak görünür. **PlayBook Izinlerini Yönet** bağlantısını seçerek, leke üzerinde playbooks ' kaynak gruplarına Azure Sentinel izni verebilirsiniz.

> [!NOTE]
> **Çok kiracılı bir mimarideki izinler**
>
> Otomasyon kuralları çoklu çalışma alanını ve çok kiracılı dağıtımları tam olarak destekler ( [Azure Athouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)kullanarak çok kiracılı olması durumunda).
>
> Bu nedenle, Azure Sentinel dağıtımınız çok kiracılı bir mimari kullanıyorsa (örneğin, bir MSSP kullanıyorsanız), tek bir kiracıya farklı bir kiracıda bulunan bir PlayBook çalıştıran bir Otomasyon kuralına sahip olabilirsiniz, ancak bu durumda Sentinel 'in PlayBook 'ları çalıştırması için izinleri, Otomasyon kurallarının tanımlandığı kiracıda değil, PlayBook 'ları çalıştırmak için tanımlanmış olmalıdır.

## <a name="creating-and-managing-automation-rules"></a>Otomasyon kuralları oluşturma ve yönetme

Özel gereksiniminize ve kullanım durumunuza bağlı olarak Azure Sentinel deneyiminde farklı noktalarda Otomasyon kuralları oluşturabilir ve bunları yönetebilirsiniz.

- **Otomasyon dikey penceresi**

    Otomasyon kuralları, yeni **Otomasyon** dikey penceresinde ( **PlayBook 'ları** dikey penceresinden), **Otomasyon kuralları** sekmesi altında merkezi olarak yönetilebilir. (artık **, PlayBook 'lar sekmesinin altında** PlayBook 'ları 'ları bu dikey pencerede da yönetebilirsiniz.) Buradan yeni otomasyon kuralları oluşturabilir ve var olanları düzenleyebilirsiniz. Ayrıca, yürütme sırasını değiştirmek için Otomasyon kurallarını sürükleyebilir ve bunları etkinleştirebilir veya devre dışı bırakabilirsiniz.

    **Otomasyon** dikey penceresinde, çalışma alanında tanımlanan tüm kuralları (etkin/devre dışı) ve hangi analiz kurallarını uygulandığını görürsünüz.

    Birçok analiz kuralına uygulanacak bir Otomasyon kuralına ihtiyacınız olduğunda, bunu doğrudan **Otomasyon** dikey penceresinde oluşturun. Üstteki menüden **Oluştur** ' a tıklayın ve yeni **Otomasyon kuralı oluştur** bölmesini açan **Yeni kural ekle**' ye tıklayın. Buradan, kuralı yapılandırma konusunda kapsamlı bir esneklik elde edersiniz: Bu kuralı herhangi bir analiz kuralına uygulayabilir (ileride bu dahil) ve en geniş koşul ve eylem aralığını tanımlayabilirsiniz.

- **Analiz Kuralı Sihirbazı**

    Analiz kuralı sihirbazının **Otomatik Yanıt** sekmesinde, sihirbazda oluşturulan veya düzenlenen belirli analiz kuralına uygulanan Otomasyon kurallarını görüntüleyebilir, yönetebilir ve oluşturabilirsiniz.

    **Analiz** dikey penceresindeki üst menüden **Oluştur** ve kural türlerinden biri (**Zamanlanmış sorgu kuralı** veya **Microsoft olay oluşturma kuralı**) ' ne tıkladığınızda veya var olan bir analiz kuralını seçip **Düzenle**' ye tıkladığınızda, Kural Sihirbazı ' nı açarsınız. **Otomatik Yanıt** sekmesini seçtiğinizde, **olay Otomasyonu** adlı bir bölüm görürsünüz. Bu, şu anda bu kurala uygulanan Otomasyon kurallarının görüntülenmesini sağlar. Düzenlenecek varolan bir Otomasyon kuralını seçebilir veya yeni bir tane oluşturmak için **Yeni Ekle** ' ye tıklayabilirsiniz.

    Buradan Otomasyon kuralı oluşturduğunuzda, **yeni otomasyon kuralı oluştur** paneli **analiz kuralı** koşulunu kullanılamaz olarak gösterir, çünkü bu kural yalnızca sihirbazda düzenlemekte olduğunuz analiz kuralına uygulanabilir olarak ayarlanmıştır. Diğer tüm yapılandırma seçenekleri hala sizin için kullanılabilir.

- **Olaylar dikey penceresi**

    Ayrıca, tek bir yinelenen olaya yanıt vermek için **Olaylar** dikey penceresinden bir Otomasyon kuralı oluşturabilirsiniz. Bu, "gürültülü" olayları otomatik olarak kapatmak için bir [gizleme kuralı](#incident-suppression) oluştururken kullanışlıdır. Kuyruktan bir olay seçin ve üst menüden **Otomasyon kuralı oluştur** ' a tıklayın.

    **Yeni otomasyon kuralı oluştur** panelinin tüm alanları olaydan alınan değerlerle doldurduğuna dikkat edin. Kuralı, olayla aynı ada adlandırır, olayı oluşturan analiz kuralına uygular ve kuralın koşulları olarak olaydaki tüm kullanılabilir varlıkları kullanır. Ayrıca, varsayılan olarak bir gizleme (kapatma) eylemi önerir ve kural için bir sona erme tarihi önerir. Koşullar ve eylemler ekleyebilir veya kaldırabilir ve sona erme tarihini istediğiniz gibi değiştirebilirsiniz.

## <a name="auditing-automation-rule-activity"></a>Otomasyon kuralını denetleme etkinliği

Belirli bir olaya ne olduğunu ve belirli bir Otomasyon kuralını kendisi için ne olduğunu bilmek isteyebilirsiniz. **Günlükler** dikey penceresinde *securityıncident* tablosunda kullanabileceğiniz bir olay Chronicles tam kaydınız vardır. Tüm otomasyon kuralı etkinliğinizi görmek için aşağıdaki sorguyu kullanın:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel olayları kuyruğunu yönetmek ve bazı temel olay işleme otomasyonu uygulamak için Otomasyon kurallarını nasıl kullanacağınızı öğrendiniz.

- Gelişmiş otomasyon seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Azure Sentinel 'de PlayBook 'ları ile tehdit yanıtını otomatikleştirme](automate-responses-with-playbooks.md).
- Otomasyon kuralları ve PlayBook 'ları uygulama konusunda yardım için bkz. [öğretici: Azure Sentinel 'de tehdit yanıtlarını otomatikleştirmek için PlayBook 'Ları kullanma](tutorial-respond-threats-playbook.md).
