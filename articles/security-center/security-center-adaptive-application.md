---
title: Azure Güvenlik Merkezi'ndeki uyarlamalı uygulama denetimleri | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi'ndeki uyarlamalı uygulama denetimlerini kullanarak Azure VM'lerinde çalışan uygulamaları beyaz listeye eklemenize yardımcı olur.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2019
ms.author: memildin
ms.openlocfilehash: 46ab2fc5c796d960de8b1c5e3391a6356563b50a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202806"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Azure Güvenlik Merkezi'ndeki uyarlamalı uygulama denetimleri
Bu kılavuzu kullanarak Azure Güvenlik Merkezi'ndeki uygulama denetimi özelliklerini yapılandırmayı öğrenebilirsiniz.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Güvenlik Merkezi'ndeki uyarlamalı uygulama denetimleri nelerdir?
Uyarlamalı uygulama denetimi, Azure Güvenlik Merkezi 'nden, Azure ve Azure olmayan VM 'lerde (Windows ve Linux) hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Diğer avantajların yanı sıra bu, VM 'lerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, VM 'leriniz üzerinde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:

- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.
- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.
- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.
- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.
- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.
- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

> [!NOTE]
> Azure dışı ve Linux VM 'Leri için, uyarlamalı uygulama denetimleri yalnızca denetim modunda desteklenir.

## <a name="how-to-enable-adaptive-application-controls"></a>Uyarlamalı uygulama denetimleri nasıl etkinleştirilir?
Uyarlamalı uygulama denetimleri, yapılandırılmış VM grupları üzerinde çalışmasına izin verilen bir uygulamalar kümesi tanımlamanıza yardımcı olur. Bu özellik hem Azure hem de Azure dışı Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux VM 'Leri ve sunucuları için kullanılabilir. Uygulamanızı izin verilenler listesine yapılandırmak için aşağıdaki adımları kullanın:

1. **Güvenlik Merkezi** panosunu açın.
2. Sol bölmeden **Gelişmiş bulut savunması** altında bulunan **Uyarlamalı uygulama denetimlerini** seçin.

    ![Savunma](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

**Uyarlamalı uygulama denetimleri** sayfası açılır.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

**VM grupları** bölümünde üç sekme bulunur:

* **Yapılandırılan**: Uygulama denetimiyle yapılandırılan VM’leri içeren grupların listesidir.
* **Önerilen**: Uygulama denetiminin önerildiği grupların listesidir. Güvenlik Merkezi makine öğrenimi özelliklerini kullanarak VM'lerin tutarlı bir şekilde aynı uygulamaları çalıştırıp çalıştırmadığına bakar ve uygulama denetimi için uygun olan VM'leri tanımlar.
* **Öneri olmayan**: Uygulama denetimi önerisi olmayan VM’leri içeren grupların listesidir. Örneğin, uygulamaların sürekli değiştiği ve kararlı bir duruma geçmediği VM'ler.

> [!NOTE]
> Güvenlik Merkezi benzer VM’lerin önerilen en iyi uygulama denetimi ilkesini alması için VM grupları oluşturan özel bir kümeleme algoritması kullanır.
>
>

### <a name="configure-a-new-application-control-policy"></a>Yeni bir uygulama denetim ilkesi yapılandırma
1. Uygulama denetimi önerileri bulunan grupların listesi için **Önerilen** sekmesine tıklayın:

   ![Önerilen](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Liste aşağıdakileri içerir:

   - **Grup adı**: Aboneliğin ve grubun adı
   - **VM 'ler ve bilgisayarlar**: Gruptaki sanal makine sayısı
   - **Durum**: önerilerin durumu
   - **Önem derecesi**: önerilerin önem düzeyi

2. **Uygulama denetim kuralları oluştur** seçeneğini açmak için bir gruba tıklayın.

   ![Uygulama denetimi kuralları](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. **VM 'Leri Seç**bölümünde, önerilen VM 'lerin listesini gözden geçirin ve uygulama için beyaz listeye ekleme ilkesi uygulamak istemediğiniz herhangi bir onay işaretini kaldırın. Daha sonra iki liste görürsünüz:

   - **Önerilen uygulamalar**: Bu grup içindeki VM 'lerde sık kullanılan uygulamaların listesi ve çalışmasına izin verilmesi önerilir.
   - **Daha fazla uygulama**: Bu gruptaki VM 'lerde daha az sıklıkta veya Patıtables olarak bilinen uygulamaların bir listesi (daha fazla bilgi için bkz.) ve gözden geçirme için önerilir.

4. Her bir listedeki uygulamaları gözden geçirin ve uygulamak istemediklerinizin işaretini kaldırın. Her liste aşağıdakileri içerir:

   - **Ad**: sertifika bilgileri veya bir uygulamanın tam yolu
   - **DOSYA TÜRLERİ**: Uygulama dosya türü. Bu, EXE, betik, MSI veya bu türlerin herhangi bir permütasyonu olabilir.
   - **Açıktan yararlanma**: bir uyarı simgesi, belirli bir uygulamanın bir uygulama izin verilenler listesini atlayıp kullanamayacağını gösterir. Bu uygulamaları onaylamadan önce gözden geçirmeniz önerilir.
   - **KULLANICILAR**: Bir uygulama çalıştırmasına izin verilmesi önerilen kullanıcılar

5. Seçimlerinizi tamamladıktan sonra **Oluştur**’u seçin. <br>
   Oluştur ' u seçtikten sonra Azure Güvenlik Merkezi, yerleşik uygulama izin verilenler listesi çözümünün en üstünde Windows sunucularında (AppLocker) bulunan uygun kuralları otomatik olarak oluşturur.

> [!NOTE]
> - Güvenlik Merkezi, temel yapılandırma oluşturmak ve VM gruplarına benzersiz öneri sunmak için en az iki haftalık veri kullanmaktadır. Güvenlik Merkezi standart katmanının yeni müşterileri başlangıçta VM gruplarının *öneri yok* sekmesi altında olduğunu görebilir.
> - Güvenlik Merkezi'ndeki Uyarlamalı Uygulama Denetimleri, GPO veya yerel güvenlik ilkesi ile AppLocker ilkesinin önceden etkinleştirilmiş olduğu VM'leri desteklemez.
> -  En iyi güvenlik uygulaması olarak, Güvenlik Merkezi, izin verilen uygulamalar için her zaman bir yayımcı kuralı oluşturmaya çalışacaktır ve yalnızca bir uygulama yayımcı bilgilerine (imzasız) sahip değilse, tam yolu için bir yol kuralı oluşturulur. belirli bir uygulama.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Uygulama denetimiyle yapılandırılmış bir grubu düzenleme ve izleme

1. Bir uygulama izin verilenler liste ilkesiyle yapılandırılmış bir grubu düzenlemek ve izlemek için, **Uyarlamalı uygulama denetimleri** sayfasına dönün ve **VM grupları**altında **yapılandırılan** ' ı seçin:

   ![Gruplar](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Liste aşağıdakileri içerir:

   - **Grup adı**: aboneliğin ve grubun adı
   - **VM 'ler ve bilgisayarlar**: gruptaki sanal makinelerin sayısı
   - **Mod**: Denetim modu, izin verilenler listesinde olmayan uygulamaları çalıştırmaya yönelik girişimleri günlüğe kaydeder. Zorla, izin verilenler listesinde olmadıkları takdirde uygulamaların çalışmasına izin vermez
   - **Uyarılar**: tüm geçerli ihlaller

2. **Uygulama denetim Ilkesini Düzenle** sayfasında değişiklik yapmak için bir gruba tıklayın.

   ![Koruma](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. **Koruma modu** altında aşağıdakilerden birini seçebilirsiniz:

   - **Denetim**: Bu modda uygulama denetimi çözümü kuralları zorunlu kılmaz ve yalnızca korumalı VM'lerdeki etkinliği denetler. Bu, bir uygulamanın hedef VM'de çalışmasını engellemeden önce genel davranışını gözlemlemek istediğiniz senaryolar için önerilir.
   - **Zorunlu kıl**: Bu modda uygulama denetimi çözümü kuralları zorunlu kılar ve çalışmasına izin verilmeyen uygulamaların engellenmesini sağlar.

   > [!NOTE]
   > -  Koruma modunu **zorla** , daha fazla uyarı olana kadar devre dışı bırakılır.
   > - Yukarıda belirtildiği gibi yeni uygulama denetimi ilkeleri her zaman *Denetim* modunda yapılandırılır. 
   >

4. **İlke uzantısı**' nın altında, izin vermek istediğiniz tüm uygulama yollarını ekleyin. Bu yollar eklendikten sonra, Güvenlik Merkezi, seçili VM grubu içindeki VM 'lerde uygulama izin verilenler listesini güncelleştirir ve zaten var olan kurallara ek olarak bu uygulamalar için uygun kuralları oluşturur.

5. **Son uyarılar** bölümünde listelenen geçerli ihlalleri gözden geçirin. Azure Güvenlik Merkezi 'nde **Uyarılar** sayfasına yönlendirilmek için her satıra tıklayın ve Azure Güvenlik Merkezi tarafından algılanan tüm uyarıları ilişkili VM 'lerde görüntüleyin.
   - **Uyarılar**: günlüğe kaydedilen tüm ihlaller.
   - **Hayır. VM 'Ler**: Bu uyarı türüne sahip sanal makine sayısı.

6. **Yayımcı beyaz listeleme kuralları**, **yol beyaz listeleme kuralları**ve **karma beyaz listeleme kuralları** altında, kural koleksiyonu türüne göre, bir grup içindeki VM 'lerde hangi uygulama beyaz liste kurallarının yapılandırıldığını görebilirsiniz. Her kural için şunları görebilirsiniz:

   - **Kural**: Bir uygulamanın çalışmasına izin verilip verilmeyeceğini tespit etmek için bir uygulamanın AppLocker tarafından İncelenme yaptığı özel parametreler.
   - **Dosya türü**: Belirli bir kural kapsamındaki dosya türleri. Bu, aşağıdakilerden herhangi biri olabilir: EXE, komut dosyası, MSI veya bu dosya türlerinin herhangi bir permütasyon.
   - **Kullanıcılar**: Uygulama beyaz listeye ekleme kuralı kapsamındaki bir uygulamayı çalıştırmasına izin verilen Kullanıcı adı veya sayısı.

   ![Beyaz listeye ekleme kuralları](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Belirli kuralı silmek veya izin verilen kullanıcıları düzenlemek istiyorsanız her satırın sonundaki üç noktaya tıklayın.

8. **Uyarlamalı uygulama denetimleri** ilkesinde değişiklik yaptıktan sonra **Kaydet**' e tıklayın.

### <a name="not-recommended-list"></a>Önerilmeyenler listesi

Güvenlik Merkezi, yalnızca kararlı bir uygulama kümesi çalıştıran sanal makineler için uygulama beyaz listeleme ilkeleri önerir. Bağlantılı VM'lerdeki uygulamalar sürekli değişiyorsa öneriler oluşturulmayacaktır.

![Öneri](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Liste aşağıdakileri içerir:
- **Grup adı**: aboneliğin ve grubun adı
- **VM 'ler ve bilgisayarlar**: gruptaki sanal makinelerin sayısı

Azure Güvenlik Merkezi, önerilen VM grupları üzerinde de bir uygulama beyaz listeleme ilkesi tanımlamanızı sağlar. Bu gruplardaki bir uygulama beyaz listeye ekleme ilkesini yapılandırmak için daha önce açıklanan ilkeleri izleyin.

## <a name="move-a-vm-from-one-group-to-another"></a>Bir VM 'yi bir gruptan diğerine taşıma

 Bir VM 'yi bir gruptan diğerine taşıdığınızda, uygulanan uygulama denetim ilkesi, onu taşıdığınız grubun ayarlarına değişir. Ayrıca, VM 'ye daha önce uygulanan uygulama Denetim ilkelerinin kaldırılmasına neden olan yapılandırılmış bir gruptan bir VM 'yi yapılandırılmamış bir gruba taşıyabilirsiniz.

 1. **Uyarlamalı uygulama denetimleri** sayfasında, **yapılandırılan** sekmesinden, geçerli olarak taşınacak VM 'nin ait olduğu gruba tıklayın.
1. **Yapılandırılmış VM 'ler ve bilgisayarlar**' a tıklayın.
1. Taşımak için VM 'nin satırındaki üç noktaya tıklayın ve **Taşı**' ya tıklayın. **Bilgisayarı farklı bir gruba taşı** penceresi açılır.

    ![Koruma](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. VM 'nin taşınacağı grubu seçin ve **Bilgisayarı taşı**' ya tıklayın ve **Kaydet**' e tıklayın.

    ![Koruma](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> **Bilgisayarı taşı**' yı tıklattıktan sonra **Kaydet** ' e tıkladığınızdan emin olun. **Kaydet**' e tıkladıysanız bilgisayar taşınmaz.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure 'da ve Azure olmayan VM 'lerde çalışan uygulamaları beyaz listelemek için Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimini nasıl kullanacağınızı öğrendiniz. Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve ele alma](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Güvenlik Merkezi’nde uyarıları yönetme ve güvenlik olaylarına yanıt vermeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md). Azure kaynaklarınızı durumunu izleme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'ndeki güvenlik uyarılarını anlama](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Farklı güvenlik uyarısı türleri hakkında bilgi edinin.
* [Azure Güvenlik Merkezi Sorun Giderme Kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Güvenlik Merkezi’nde sık karşılaşılan sorunları gidermeyi öğrenin.
* [Azure Güvenlik Merkezi SSS](security-center-faq.md). Hizmet kullanımı ile ilgili sık sorulan soruları bulun.
* [Azure Güvenlik Blogu](https://blogs.msdn.com/b/azuresecurity/). Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulun.
