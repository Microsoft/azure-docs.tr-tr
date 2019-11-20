---
title: 'Hızlı Başlangıç: Azure Veri Gezgini kümesi ve veritabanı oluşturma'
description: Bu hızlı başlangıçta Azure Veri Gezgini kümesi ve veritabanı oluşturup veri almayı (yüklemeyi) öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: 10030cf69009c59fa607ad68c3b7ab19c7a22860
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326721"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Hızlı Başlangıç: Azure Veri Gezgini kümesi ve veritabanı oluşturma

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM şablonu](create-cluster-database-resource-manager.md)


Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanına (yükleme) sahip olursunuz. Bu hızlı başlangıçta bir küme ve bir veritabanı oluşturacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/) oturum açın.

## <a name="create-a-cluster"></a>Küme oluşturma

Azure Kaynak grubunda tanımlı bir dizi işlem ve depolama kaynağı içeren bir Azure Veri Gezgini kümesi oluşturun.

1. Portalın sol üst köşesinde bulunan **Kaynak oluştur** düğmesini (+) seçin.

1. *Azure Veri Gezgini* için arama yapın.

   ![Kaynak ara](media/create-cluster-database-portal/search-resources.png)

1. **Azure Veri Gezgini**'nin altında, ekranın en alt bölümünde **Oluştur**'u seçin.

1. Aşağıdaki bilgilerle temel küme ayrıntılarını doldurun.

   ![Küme formu oluşturma](media/create-cluster-database-portal/create-cluster-form2.png)

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Subscription | Aboneliğiniz | Kümeniz için kullanmak istediğiniz Azure aboneliğini seçin.|
    | Resource group | Kaynak grubunuz | Mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. |
    | Küme adı | Benzersiz küme adı | Kümenizi tanımlayan benzersiz bir ad seçin. Girdiğiniz küme adının sonuna *[bölge].servicebus.windows.net* etki alanı adı eklenir. Ad yalnızca küçük harf ve sayı içerebilir. 4 ile 22 karakter arasında olmalıdır.
    | Bölge | *Batı ABD* veya *Batı ABD 2* | Bu hızlı başlangıç için *Batı ABD* veya *Batı ABD 2* (kullanılabilirlik alanları kullanılıyorsa) seçeneğini belirleyin. Üretim sisteminde ihtiyaçlarınıza en uygun bölgeyi seçmeniz gerekir.
    | Kullanılabilirlik alanları | *1*, *2*, ve/veya *3* | Küme örneklerini aynı bölgedeki çeşitli kullanılabilirlik bölgelerine yerleştirin (isteğe bağlı). [Azure kullanılabilirlik alanları](/azure/availability-zones/az-overview) aynı Azure bölgesi içindeki benzersiz fiziksel konumlardır. Azure Veri Gezgini kümesini ve kısmi bölge hatasından verileri korur. Küme düğümleri varsayılan olarak, aynı veri merkezinde oluşturulur. Çeşitli kullanılabilirlik alanları ' nı seçerek, tek bir başarısızlık noktasını ortadan kaldırabilir ve yüksek kullanılabilirlik sağlayabilirsiniz. Kullanılabilirlik bölgelerine dağıtım yalnızca küme oluşturma sırasında mümkündür ve daha sonraki bir tarihte değiştirilemez.
    | İşlem belirtimleri | *D13_v2* | Bu hızlı başlangıç için en düşük fiyatlı özelliği seçin. Üretim sisteminde ihtiyaçlarınıza en uygun özelliği seçmeniz gerekir.
    | | | |

1. Küme ayrıntılarınızı gözden geçirmek için **gözden geçir + oluştur** ' u seçin ve kümeyi sağlamak için **oluşturun** . Sağlama genellikle yaklaşık 10 dakika sürer.

1. Dağıtım tamamlandığında **Kaynağa Git**' i seçin.

    ![Kaynağa git](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Veritabanı oluşturma

Artık işlemin ikinci adımı olan veritabanı oluşturma bölümüne geçebilirsiniz.

1. **Genel bakış** sekmesinde **Veritabanı oluştur**'u seçin.

    ![2\. Adım: veritabanı oluşturma](media/create-cluster-database-portal/database-creation.png)

1. Formu aşağıdaki bilgilerle doldurun.

    ![Veritabanı oluşturma formu](media/create-cluster-database-portal/create-database.png)

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Veritabanı adı | *TestDatabase* | Veritabanı adı küme içinde benzersiz olmalıdır.
    | Bekletme süresi | *3650* | Verilerin sorgu için kullanılabilir durumda tutulması garantisini veren zaman aralığı (gün cinsinden). Zaman aralığı verilerin alındığı andan itibaren hesaplanır.
    | Önbellek süresi | *31* | Sık sorgulanan verilerin, daha uzun vadeli depolama yerine SSD depolamada veya RAM 'de kullanılabildiği zaman aralığı (gün cinsinden).
    | | | |

1. Veritabanını oluşturmak için **Oluştur** ' u seçin. Oluşturma işlemi genellikle bir dakikadan kısa sürer. İşlem tamamlandığında yeniden kümenin **Genel bakış** sekmesi görüntülenir.

## <a name="run-basic-commands-in-the-database"></a>Veritabanında temel komutları çalıştırma

Artık bir kümeye ve veritabanına sahip olduğunuza göre sorgu ve komut çalıştırabilirsiniz. Veritabanında henüz veri yok ancak yine de araçların nasıl çalıştığını görebilirsiniz.

1. Kümenizde **Sorgu**'yu seçin. @No__t-0 komutunu sorgu penceresine yapıştırın ve **Çalıştır**' ı seçin.

    ![Veritabanlarını göster komutu](media/create-cluster-database-portal/show-databases.png)

    Sonuç kümesi, kümedeki tek veritabanı olan **TestDatabase** veritabanını gösterir.

1. Sorgu penceresine `.show tables` komutunu yapıştırın ve **Çalıştır**' ı seçin.

    Henüz bir tablonuz olmadığı için bu komut boş sonuç kümesi döndürür. Bu dizinin bir sonraki makalesinde tablo ekleyeceksiniz.

## <a name="stop-and-restart-the-cluster"></a>Kümeyi durdurma ve yeniden başlatma

İhtiyaç duymanız halinde kümeyi durdurup yeniden başlatabilirsiniz.

1. Kümeyi durdurmak için **Genel bakış** sekmesinin en üstünden **Durdur**'u seçin.

    Küme durdurulduğunda veriler sorgulanamaz ve yeni veri alınamaz.

1. Kümeyi yeniden başlatmak için **Genel bakış** sekmesinin en üstünden **Başlat**'ı seçin.

    Küme yeniden başlatıldığında, bunun kullanılabilir olması yaklaşık 10 dakika sürer (ilk sağlandığı gibi). Verilerin sık erişim önbelleğine yüklenmesi daha uzun zaman alabilir.  

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer hızlı başlangıç ve öğreticilerle izlemeyi planlıyorsanız, oluşturduğunuz kaynakları saklayın. Aksi takdirde, maliyetlerinizi önlemek için kaynak grubunuzu temizleyin.

1. Azure portal, en solda bulunan **kaynak grupları** ' nı seçin ve sonra Veri Gezgini kümenizi içeren kaynak grubunu seçin.  

1. Kaynak grubunun tamamını silmek için **kaynak grubunu sil** ' i seçin. Mevcut bir kaynak grubu kullanıyorsanız, yalnızca Veri Gezgini kümesini silmeyi seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: Olay Hub 'ından Azure 'a veri alma Veri Gezgini @ no__t-0


