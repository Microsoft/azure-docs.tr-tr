---
title: Maliyet Analizi ve bütçe-Azure Batch
description: Maliyet analizini alma ve Batch iş yükünüz için bir bütçe ayarlama hakkında bilgi edinin.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: lahugh
ms.openlocfilehash: 6ccf530fe2164b3d9b1936648ffe9057c334efd6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70094213"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch için maliyet analizi ve bütçeler

Azure Batch kendisi için ücret alınmaz, yalnızca toplu iş yüklerini çalıştırmak için kullanılan temel işlem kaynakları ve yazılım lisanslarıdır. Yüksek düzeyde, bir havuzdaki sanal makineler (VM), VM 'den veri aktarımı veya bulutta depolanan herhangi bir giriş ya da çıkış verisi üzerinden maliyetler alınır. Maliyetlerin nereden geldiği, bir havuz veya hesap için nasıl bütçe ayarlanacağı ve toplu iş yüklerinizi daha verimli hale getirmek için bazı teknikler için bazı önemli bileşenlere göz atalım.

## <a name="batch-resources"></a>Batch kaynakları

Sanal makineler toplu işleme için kullanılan en önemli kaynaktır. Batch için VM kullanmanın maliyeti, tür, miktar ve kullanım süresi temel alınarak hesaplanır. VM faturalandırma seçenekleri [, Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/) veya [rezervasyon](../billing/billing-save-compute-costs-reservations.md) (önceden ödeme) içerir. Her iki ödeme seçeneğinin da işlem iş yükünüze bağlı olarak farklı avantajları vardır ve her iki ödeme modeli de faturanızı farklı şekilde etkiler.

Uygulamalar, [uygulama paketleri](batch-application-packages.md)kullanılarak toplu iş düğümlerine (VM 'ler) dağıtıldığında, uygulama paketlerinizin kullanacağı Azure depolama kaynakları için faturalandırılırsınız. Ayrıca, kaynak dosyaları ve diğer günlük verileri gibi herhangi bir giriş veya çıkış dosyası depolaması için faturalandırılırsınız. Genel olarak, Batch ile ilişkili depolama verilerinin maliyeti, işlem kaynakları maliyetinden çok daha düşüktür. **Virtualmachineconfiguration** ile oluşturulan HAVUZDAKI her VM 'Nin, Azure tarafından yönetilen diskleri kullanan ilişkili bir işletim sistemi diski vardır. Azure tarafından yönetilen disklerin ek bir maliyeti vardır ve diğer disk performansı katmanlarında de farklı maliyetler de vardır.

Batch havuzları ağ kaynaklarını kullanır. Özellikle, **Virtualmachineconfiguration** havuzları için, statik IP adresleri gerektiren standart yük dengeleyiciler kullanılır. Batch tarafından kullanılan yük dengeleyiciler **Kullanıcı abonelik** hesapları için görünür, ancak **Batch hizmeti** hesapları için görünür değildir. Standart yük dengeleyiciler, Batch havuzu VM 'lerine ve bu VM 'lerden aktarılan tüm veriler için ücretlendirilir; Havuz düğümlerinden (görev/düğüm dosyası alma gibi) veri alan Batch API 'Lerini seçin, görev uygulama paketleri, kaynak/çıkış dosyaları ve kapsayıcı görüntüleri ücretlendirilecektir.

### <a name="additional-services"></a>Ek hizmetler

VM 'Leri ve depolamayı dahil etme hizmetleri, Batch hesabınızın maliyetine göre ücretlendirme yapabilir.

Batch ile yaygın olarak kullanılan diğer hizmetler şunlar olabilir:

- Application Insights
- Data Factory
- Azure İzleyici
- Sanal Ağ
- Grafik uygulamalarına sahip VM 'Ler

Batch çözümünüz ile kullandığınız hizmetlere bağlı olarak, ek ücretler uygulanabilir. Her ek hizmetin maliyetini öğrenmek için [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' na bakın.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Bir havuz için maliyet analizi ve bütçe

Azure portal aracılığıyla, toplu Iş havuzlarınız veya Batch hesabınız için bütçeler ve harcama uyarıları oluşturabilir ve izleyebilirsiniz. Bütçeler ve uyarılar, paydaşların fazla harcama riskleri konusunda bilgilendirmek için faydalıdır. Uyarı harcama ve bir bütçeyi biraz aşmaya yönelik bir gecikme olması mümkündür. Bu örnekte, tek bir Batch havuzunun maliyet analizini inceleyeceğiz.

1. Azure portal, sol gezinti çubuğundan **maliyet yönetimi + faturalandırma** ' i seçin.
1. **Aboneliklerim** bölümünde aboneliğinizi seçin
1. Sol gezinti çubuğunun **maliyet yönetimi** bölümünde **Maliyet Analizi** ' ne giderek şöyle bir görünüm gösterir:
1. **Filtre Ekle**' yi seçin. İlk açılan kutuda kaynak filtresi ![Select **kaynak** ' ı seçin ](./media/batch-budget/resource-filter.png)
1. İkinci açılan kutuda Batch havuzunu seçin. Havuz seçildiğinde, maliyet analizi aşağıdaki Analize benzer şekilde görünür.
    Havuzun ![Cost analizini ](./media/batch-budget/pool-cost-analysis.png)

Elde edilen maliyet analizi, havuzun maliyetini ve bu maliyete katkıda bulunan kaynakları gösterir. Bu örnekte, havuzda kullanılan VM 'Ler en pahalı kaynaktır.

Havuzun bütçesini oluşturmak için **Bütçe: yok**' u seçin ve ardından **Yeni bütçe > Oluştur**' u seçin. Şimdi havuzunuzun özel bir bütçesini yapılandırmak için pencereyi kullanın.

Bir bütçeyi yapılandırma hakkında daha fazla bilgi için bkz. [Azure bütçeleri oluşturma ve yönetme](../cost-management/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch Azure Cloud Services ve Azure sanal makineler teknolojisinden oluşturulmuştur. **Cloud Services yapılandırma**' yı seçtiğinizde, Cloud Services fiyatlandırma yapısına göre ücretlendirilirsiniz. **Sanal makine yapılandırması**' nı seçtiğinizde, sanal makinelerin fiyatlandırma yapısına göre ücretlendirilirsiniz. Bu sayfadaki örnek, **sanal makine yapılandırmasını**kullanır.

## <a name="minimize-cost"></a>Maliyeti en aza indir

Uzun süreler için birkaç VM ve Azure hizmeti kullanmak maliyetli olabilir. Neyse ki, harcamalarınızı azaltmaya yardımcı olacak hizmetler vardır ve iş yükünüzün verimliliğini en üst düzeye çıkarmak için stratejiler de mevcuttur.

### <a name="low-priority-virtual-machines"></a>Düşük öncelikli sanal makineler

Düşük öncelikli VM 'Ler, Azure 'daki daha fazla bilgi işlem kapasitesinden yararlanarak Batch iş yüklerinin maliyetini azaltır. Havuzlarınızda düşük öncelikli VM 'Ler belirttiğinizde Batch, iş yükünüzü çalıştırmak için bu fazlalığı kullanır. Adanmış VM 'lerin yerine düşük öncelikli VM 'Ler kullanılarak önemli bir maliyet tasarrufu vardır.

[Batch ile düşük öncelikli VM 'Ler kullanma](batch-low-pri-vms.md)konusunda iş yükünüz için düşük öncelikli VM 'ler ayarlama hakkında daha fazla bilgi edinin.

### <a name="virtual-machine-os-disk-type"></a>Sanal makine işletim sistemi disk türü

Birden çok [VM işletim sistemi disk türü](../virtual-machines/windows/disks-types.md)var. Çoğu VM Serisi hem Premium hem de standart depolamayı destekleyen boyutlarda. Bir havuz için bir ' VM boyutu seçildiğinde Batch, Premium SSD işletim sistemi disklerini yapılandırır. ' S ' olmayan VM boyutu seçildiğinde, standart HDD Disk türü kullanılır. Örneğin, Premium SSD işletim sistemi diskleri `Standard_D2s_v3` için kullanılır ve standart HDD işletim sistemi diskleri `Standard_D2_v3` için kullanılır.

Premium SSD işletim sistemi diskleri daha pahalıdır, ancak Premium diskler içeren daha yüksek performansa ve VM 'lere standart HDD işletim sistemi disklerine sahip VM 'lerden biraz daha hızlı başlayabilirler. Batch ile, işletim sistemi diski genellikle uygulamalar ve görev dosyaları VM 'lerde geçici SSD disk yer aldığı sürece çoğu zaman kullanılmaz. Bu nedenle, çoğu durumda, bir ' VM boyutu belirtildiğinde, Premium SSD için artan maliyeti ödemenizi gerektirmez.

### <a name="reserved-virtual-machine-instances"></a>Ayrılmış sanal makine örnekleri

Toplu Işi uzun bir süre kullanmak istiyorsanız, iş yükleriniz için [Azure ayırmaları](../billing/billing-save-compute-costs-reservations.md) kullanarak sanal makinelerin maliyetine tasarruf edebilirsiniz. Rezervasyon oranı, Kullandıkça Öde tarifesine göre önemli ölçüde düşüktür. Rezervasyon olmadan kullanılan sanal makine örnekleri, Kullandıkça Öde fiyatı üzerinden ücretlendirilir. Bir rezervasyon satın alırsanız, rezervasyon iskontosu uygulanır ve artık Kullandıkça Öde tarifesine göre ücretlendirilmezsiniz.

### <a name="automatic-scaling"></a>Otomatik ölçeklendirme

[Otomatik ölçeklendirme](batch-automatic-scaling.md) , geçerli iş taleplerine göre Batch havuzunuzdaki VM sayısını dinamik olarak ölçeklendirir. Havuzu bir işin kullanım süresine göre ölçeklendirerek, otomatik ölçeklendirme, VM 'Lerin ölçeğini yalnızca gerçekleştirilecek bir iş olduğunda ölçeklendirmesini ve kullanılmasını sağlar. İş tamamlandığında veya hiçbir iş yoksa, işlem kaynaklarını kaydetmek için VM 'Ler otomatik olarak ölçeklendirilir. Ölçeklendirme, yalnızca ihtiyacınız olan kaynakları kullanarak Batch çözümünüzün genel maliyetini düşürmenize olanak tanır.

Otomatik ölçeklendirme hakkında daha fazla bilgi için bkz. [bir Azure Batch havuzundaki işlem düğümlerini otomatik olarak ölçeklendirme](batch-automatic-scaling.md).

## <a name="next-steps"></a>Sonraki adımlar

- Batch çözümleri oluşturmak ve izlemek için kullanılabilen [Batch API 'leri ve araçları](batch-apis-tools.md) hakkında daha fazla bilgi edinin.  

- [Batch ile düşük öncelikli VM 'ler](batch-low-pri-vms.md)hakkında bilgi edinin.
