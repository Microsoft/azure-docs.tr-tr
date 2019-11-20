---
title: Autopilot modunda Azure Cosmos kapsayıcıları ve veritabanları oluşturun.
description: Avantajlar, kullanım durumları ve Autopilot modunda Azure Cosmos veritabanları ve kapsayıcıları sağlama hakkında bilgi edinin.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0e9f909aba11d35307e02a98a41ffa04e36e4db2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953116"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Autopilot modunda Azure Cosmos kapsayıcıları ve veritabanları oluşturma (Önizleme)

Azure Cosmos DB, kapsayıcılarınızdaki iş üretimini el ile veya Autopilot modunda sağlamanıza olanak tanır. Bu makalede, Autopilot modunun avantajları ve kullanım durumları açıklanmaktadır.

> [!NOTE]
> Autopilot modu şu anda genel önizlemede kullanılabilir. Azure Cosmos hesabınız için Autopilot özelliğini etkinleştirmek üzere, bu makalenin [Autopilot 'i etkinleştir](#enable-autopilot) bölümüne bakın. Yalnızca yeni veritabanları ve kapsayıcılar için Autopilot etkinleştirebilirsiniz; bu, mevcut kapsayıcılar ve veritabanları için kullanılamaz.

Aktarım hızını el ile sağlamaya ek olarak, artık Autopilot modundaki Azure Cosmos kapsayıcıları ' nı yapılandırabilirsiniz. Autopilot modunda yapılandırılan Azure Cosmos kapsayıcıları ve veritabanları, **SLA 'lara ödün vermeden uygulama gereksinimlerinize göre sağlanan üretilen işi otomatik olarak ve anında ölçeklendirecektir.**

Artık sağlanan üretilen işi el ile yönetmeniz veya hız sınırlandırma sorunlarını ele almanız gerekmez. Autopilot modunda yapılandırılan Azure Cosmos kapsayıcıları, iş yükünün kullanılabilirliğini, gecikmesini, verimini veya küresel olarak performansını etkilemeden iş yüküne anında ölçeklendirilebilir. Yüksek kullanım altında, Autopilot modunda yapılandırılan Azure Cosmos kapsayıcıları, devam eden işlemleri etkilemeden ölçeği artırılabilecek veya azaltılabilir.

Autopilot modunda kapsayıcılar ve veritabanları yapılandırılırken, aşılmayacak `Tmax` en yüksek aktarım hızını belirtmeniz gerekir. Kapsayıcılar daha sonra `0.1*Tmax < T < Tmax` aralığında iş yükü ihtiyaçlarına göre anında ölçeklendirebilir. Diğer bir deyişle, kapsayıcılar ve veritabanları, yapılandırılan en yüksek aktarım hızı değerinin %10 ' u kadar ve yapılandırılan maksimum üretilen iş değerine kadar, iş yükü ihtiyaçlarına göre anında ölçeklenir. Autopilot veritabanı veya kapsayıcısındaki en fazla üretilen iş (Tmax) ayarını dilediğiniz zaman değiştirebilirsiniz. Autopilot seçeneğiyle, kapsayıcı veya veritabanı başına 400 RU/sn en düşük aktarım hızı artık geçerli değildir.

Autopilot önizlemesi sırasında, kapsayıcıda veya veritabanında bulunan belirtilen en yüksek aktarım hızı için sistem, hesaplanan depolama sınırı içinde çalışmasına izin verir. Depolama sınırı aşılırsa, en yüksek aktarım hızı otomatik olarak daha yüksek bir değere ayarlanır. Autopilot modu ile veritabanı düzeyinde aktarım hızı kullanılırken, bir veritabanı içinde izin verilen kapsayıcıların sayısı şu şekilde hesaplanır: (0,001 * en fazla aktarım hızı). Örneğin, 20.000 Autopilot RU/s temin ediyorsanız, veritabanında 20 kapsayıcı olabilir.

## <a name="benefits-of-autopilot-mode"></a>Autopilot modunun avantajları

Autopilot modunda yapılandırılan Azure Cosmos kapsayıcıları aşağıdaki avantajlara sahiptir:

* **Basit:** Autopilot modundaki kapsayıcılar, çeşitli kapsayıcılar için sağlanan üretilen işi (ru) ve kapasitesini el ile yönetme karmaşıklığını ortadan kaldırır.

* **Ölçeklenebilir:** Autopilot modundaki kapsayıcılar, sağlanan verimlilik kapasitesini gerektiği şekilde sorunsuz bir şekilde ölçeklendirin. İstemci bağlantıları, uygulamalar ve mevcut SLA 'Ları etkilemez.

* Uygun **maliyetli:** Autopilot modunda yapılandırılmış Azure Cosmos kapsayıcıları kullandığınızda, yalnızca iş yüklerinizin saat başına ihtiyaç duyduğu kaynaklar için ödeme yaparsınız.

* **Yüksek oranda kullanılabilir:** Autopilot modundaki Azure Cosmos kapsayıcıları, veri dayanıklılığı ve her zaman yüksek kullanılabilirlik sağlamak için aynı küresel olarak dağıtılmış, hataya dayanıklı, yüksek oranda kullanılabilir arka uca sahiptir.

## <a name="use-cases-of-autopilot-mode"></a>Autopilot modunun kullanım durumları

Autopilot modunda yapılandırılan Azure Cosmos kapsayıcıları için kullanım örnekleri şunları içerir:

* **Değişken iş yükleri:** En yüksek kullanımı 1 saat ile her gün birkaç saat arasında veya yılda birkaç kez kullanıldığında, hafif olarak kullanılan bir uygulama çalıştırırken. Örnek olarak insan kaynakları, bütçeleme ve operasyonel raporlama için uygulamalar sayılabilir. Bu tür senaryolar için Autopilot modunda yapılandırılan kapsayıcılar kullanılabilir, artık yoğun veya ortalama kapasite için el ile sağlamanız gerekmez.

* **Öngörülemeyen iş yükleri:** Gün boyunca veritabanı kullanımı olan iş yüklerini çalıştırırken, aynı zamanda tahmin edilmesi zor olan etkinlik de çok fazla olur. Örnek, hava durumu tahmini değiştiğinde etkinliğin ani bir durumunu gösteren bir trafik sitesi içerir. Autopilot modunda yapılandırılan kapsayıcılar, uygulamanın en yoğun yük ihtiyaçlarını karşılamak için kapasiteyi ayarlayın ve etkinliğin aşırı dönmesi üzerindeyken ölçeği yeniden azaltın.

* **Yeni uygulamalar:** Yeni bir uygulama dağıtıyorsanız ve ne kadar sağlanan aktarım hızı (yani, ne kadar ru) gerektiği konusunda emin değilseniz. Autopilot modunda yapılandırılmış kapsayıcılar ile uygulamanızın kapasite ihtiyaçlarına ve gereksinimlerine otomatik olarak ölçeklendirebilirsiniz.

* **Sık kullanılmayan uygulamalar:** Yalnızca birkaç saat için günde bir veya haftada bir veya ayda birkaç kez kullanılan bir uygulamanız varsa (düşük hacimli bir uygulama/web/blog sitesi gibi).

* **Geliştirme ve test veritabanları:** Geliştiriciler iş saatlerinde Azure Cosmos hesaplarını kullanır, ancak bunları gece veya hafta sonları üzerinde gerektirmez. Autopilot modunda yapılandırılmış kapsayıcılar ile, kullanımda olmadığında en düşük olarak ölçeklenir.

* **Zamanlanan üretim iş yükleri/sorguları:** Tek bir kapsayıcıda bir dizi zamanlanmış istek/işlem/sorgu varsa ve mutlak düşük bir verimlilik üzerinde çalıştırmak istediğiniz boşta dönemler varsa, artık bu işlemi kolayca yapabilirsiniz. Zamanlanan bir sorgu/istek Autopilot modunda yapılandırılmış bir kapsayıcıya gönderildiğinde, otomatik olarak gerektiği kadar ölçeklendirecektir ve işlemi çalıştırır.

Önceki sorunlara yönelik çözümler uygulamada yalnızca çok büyük bir süre gerektirmez, ancak aynı zamanda yapılandırmada veya kodunuzda karmaşıklık sağlar ve genellikle el ile müdahale gerektirir. Autopilot modu, yukarıdaki senaryolara izin vermez, böylece artık bu sorunlar hakkında endişelenmenize gerek kalmaz.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Karşılaştırma – manuel modda ve Autopilot modunda yapılandırılmış kapsayıcılar

|  | El ile modda yapılandırılmış kapsayıcılar  | Autopilot modunda yapılandırılmış kapsayıcılar |
|---------|---------|---------|
| **Sağlanan aktarım hızı** | El ile sağlanmış | Otomatik olarak ve anında iş yükü kullanım desenlerine göre ölçeklendirildi. |
| **İsteklerin/işlemlerin hız sınırlaması (429)**  | Tüketim sağlanan kapasiteyi aşarsa meydana gelebilir. | Tüketilen verimlilik, Autopilot modu ile seçtiğiniz en fazla aktarım hızı içindeyse gerçekleşmeyecektir.   |
| **Kapasite planlaması** |  Bir ilk kapasite planlaması ve ihtiyacınız olan aktarım hızını temin etmeniz gerekir. |    Kapasite planlaması konusunda endişelenmeniz gerekmez. Sistem, kapasite planlama ve kapasite yönetimini otomatik olarak gerçekleştirir. |
| **Fiyatlandırma** | Saat başına el ile sağlanan RU/s. | Tek bir yazma bölgesi hesabında, saat başına Autopilot RU/s 'yi kullanarak saatlik olarak kullanılan aktarım hızı için ödeme yaparsınız. <br/><br/>Birden fazla yazma bölgesi olan hesaplar için Autopilot için ek ücret alınmaz. Saat başına aynı çok yöneticili RU/sn 'yi kullanarak saatlik olarak kullanılan aktarım hızı için ödeme yaparsınız. |
| **İş yükü türleri için en uygun** |  Öngörülebilir ve kararlı iş yükleri|   Tahmin edilemeyen ve değişken iş yükleri  |

## <a name="a-idenable-autopilot-enable-autopilot-from-azure-portal"></a>Azure portal Autopilot <a id="enable-autopilot"> etkinleştir

Azure portal ' den ' i etkinleştirerek Azure Cosmos hesaplarınızda Autopilot deneyebilirsiniz. Autopilot seçeneğini etkinleştirmek için aşağıdaki adımları kullanın:

1. Azure portal oturum açın [.](https://portal.azure.com)

2. Azure Cosmos hesabınıza gidin ve **yeni özellikler** sekmesini açın. aşağıdaki ekran görüntüsünde gösterildiği gibi **Auto Pilot** ve **register** ' ı seçin:

![Autopilot modunda kapsayıcı oluşturma](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Autopilot modu ile bir veritabanı veya kapsayıcı oluşturma

Veritabanları veya kapsayıcılar oluşturma sırasında Autopilot yapılandırabilirsiniz. Aşağıdaki adımları yeni bir veritabanı veya kapsayıcıya kullanın, Autopilot etkinleştirin ve en yüksek aktarım hızını belirtin.

1. [Azure Portal](https://portal.azure.com) veya [Azure Cosmos Gezgini](https://cosmos.azure.com/) ' nde oturum açın.

1. Azure Cosmos hesabınıza gidin ve **Veri Gezgini** sekmesini açın.

1. **Yeni kapsayıcı**' yı seçin, bir bölüm anahtarı olan Kapsayıcınız için bir ad girin. **Autopilot** seçeneğini belirleyin ve Autopilot seçeneğini kullanırken kapsayıcının aşmadığı maksimum aktarım hızını seçin.

   ![Autopilot modunda kapsayıcı oluşturma](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. **Tamam**’ı seçin

Benzer adımlarla, Autopilot modunda sağlanan aktarım hızına sahip bir veritabanı da oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal bölümler](partition-data.md)hakkında daha fazla bilgi edinin.
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)hakkında bilgi edinin.
* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)hakkında bilgi edinin.
