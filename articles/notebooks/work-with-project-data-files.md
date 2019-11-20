---
title: Azure Notebooks sahip projelerle verileri içeri ve dışarı aktarma
description: Verileri dış kaynaklardan bir Azure Notebooks projesine getirme ve verileri bir projeden dışarı aktarma.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b33d5dbfa354061b9b25069b3b300010b7cd49bf
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970206"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Azure Not defteri projelerinde veri dosyalarıyla çalışma

Veriler birçok jupi Not defteri 'nin, özellikle de veri bilimi için kullanılan not defterlerinden oluşan yaşam bir kan. Azure Notebooks, çeşitli kaynaklardan kolayca bir projeye aktarabilir ve ardından bu verileri Not defterlerden kullanabilirsiniz. Ayrıca, daha sonra başka bir yerde kullanmak üzere indirebileceğiniz, Not defterleri projede depolanan verileri de oluşturabilirsiniz.

Çalışan bir not defteri içindeki **veri** menüsü Ayrıca, aynı zamanda projedeki dosyalarla ve geçerli not defteri oturumunun geçici dosyalarına çalışan **karşıya yükleme** ve **indirme** komutları sağlar.

Ayrıca, bir proje içindeki dosyalar dahil olmak üzere çeşitli veri kaynaklarına doğrudan erişmek için bir not defteri içindeki kodu kullanabilirsiniz. Ayrıca, bir kod hücresindeki komutları kullanarak da rastgele verilere erişebilirsiniz. Bu tür veriler Not defteri oturumunda değişkenlerde depolandığından, özel olarak proje dosyaları oluşturmak için kod kullanmadığınız müddetçe, bu, projede kaydedilmez.

Veriler üzerinde kodla çalışma, çalışan bir not defteri içinde en iyi deneyimdir: Bu amaçla, [Azure Notebooks örnek not defterinde verilerinize alma](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)bölümüne bakın.

Bu makalenin geri kalanında proje düzeyi dosya işlemlerine ilişkin ayrıntılar sağlanmaktadır.

## <a name="import-data"></a>Veri içeri aktarma

Dosya menüsünü veya `curl` gibi bir komutu **kullanarak, dosyaları** proje panosundan veya çalışan bir not defteri içinde projeye taşıyabilirsiniz.

### <a name="import-files-from-the-project-dashboard"></a>Proje panosundan dosyaları içeri aktar

1. Projede, dosyaları içeri aktarmak istediğiniz klasöre gidin.

1. **Karşıya yükle** komutunu, **URL 'den** veya **bilgisayardan** ve içeri aktarmak istediğiniz veriler için gerekli ayrıntıları Project ' e seçin:

   - **URL 'den**: dosya **URL 'si** alanına kaynak adresini ve **dosya adı** alanında projenizdeki not defterine atanacak dosya adını girin. Ardından, URL 'YI karşıya yükle listesine eklemek için **+ Dosya Ekle** ' yi seçin. İşlemi ek URL 'Ler için tekrarlayın, sonra **bitti**' yi seçin.

     ![URL açılan penceresinden karşıya yükle](media/quickstarts/upload-from-url-popup.png)

   - **Bilgisayardan**: dosyaları açılan pencereye sürükleyip bırakın veya **dosyaları seç**' i seçin ve ardından içeri aktarmak istediğiniz veri dosyalarını seçin. Herhangi bir tür ve biçimdeki herhangi bir sayıda dosyayı bırakabilir veya seçebilirsiniz. Bu, dosyayı açmak ve verilerini ayrıştırmak için not defterindeki koda kadar olabilir.

     ![Açılan bilgisayardan karşıya yükle](media/quickstarts/upload-from-computer-popup.png)

1. İçeri aktarıldıktan sonra, dosyalar proje panosunda görünür ve ilgili klasöre göreli yol adları kullanılarak Not defteri kodu dahilinde erişilebilir.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Dosyaları bir not defterinde Dosya menüsünden içeri aktarma

1. Çalışan bir not defteri içinde **dosya** > **karşıya yükleme** komutunu seçin:

    ![Bir not defteri içinde karşıya dosya yükleme menü komutu](media/file-menu-upload.png)

1. Açılan iletişim kutusunda, ' a gidin ve karşıya yüklemek istediğiniz dosyaları seçin. Herhangi bir türde dosya seçebilirsiniz. Bittiğinde **Aç** ' ı seçin.

1. Görüntülenen **karşıya yükleme durumu** açılan penceresinde, açılan listeden bir **hedef klasör** seçin:

    - Oturum klasörü ( *~/* ): dosyaları geçerli not defteri oturumuna yükler ancak projede dosya oluşturmaz. Oturum klasörü, proje klasörü için bir eşler, ancak oturum bittikten sonra kalıcı olmaz. Koddaki oturum dosyalarına erişmek için, dosya adlarını göreli yol ile önek yapın *. /* .

        Oturum klasörünü kullanmak, deneme için faydalıdır ve projenin uzun süreli olarak gerek duymayan veya ihtiyaç duymayan dosyalarla karışıklık almasını önler. Ayrıca dosyaları, çakışmaya neden olmadan ve dosyaları yeniden adlandırmaya gerek kalmadan projedeki dosyalarla aynı adlara sahip oturum klasörüne de yükleyebilirsiniz. Örneğin, projede zaten bir *Data. csv* sürümüne sahip olduğunu, ancak farklı bir *Data. csv*sürümüyle denemek istediğinizi varsayalım. Dosyayı oturum klasörüne karşıya yükleyerek, karşıya yüklenen dosyadaki verileri kullanarak not defterini çalıştırabilirsiniz (kullanarak kodda bulunan *... /DATAEM CSV*), proje dosyasındaki veriler yerine.

    - Proje klasörü ( */Project*): kodda göreli yol adları kullanılarak erişilebilecekleri projeye dosya yükler. Bu klasöre bir dosya yüklemek, Proje panosuna bir dosya yükleme ile aynıdır. Dosya projeye kaydedilir ve sonraki oturumlarda kullanılabilir.

        Projede zaten mevcut olan bir dosyayı aynı ada sahip bir dosya yüklemeye çalışırsanız karşıya yükleme başarısız olur. Bir dosyanın üzerine yazmak için, bunun yerine yeni dosyayı proje panosundan karşıya yükleyin, bu da size üzerine yazma seçeneği sunar.

1. İşlemi gerçekleştirmek için **karşıya yüklemeyi Başlat** ' ı seçin.

### <a name="create-or-import-files-using-commands"></a>Komutları kullanarak dosya oluşturma veya içeri aktarma

Hem proje hem de oturum klasörlerinde dosya oluşturmak için bir terminaldeki veya Python kod hücresinin içindeki komutları kullanabilirsiniz. Örneğin, `curl` ve `wget` gibi komutlar doğrudan Internet 'ten dosya indirir.

Terminaldeki dosyaları indirmek için, proje panosunda **Terminal** komutunu seçin ve ardından uygun komutları girin:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Bir not defterinde Python kod hücresi kullanırken, komutlara `!` ile önek ekleyin.

Proje klasörü varsayılan klasördür, bu nedenle *oil_price. csv* gibi bir hedef dosya adının belirtilmesi projede dosyayı oluşturur. Bir oturum dosyası oluşturmak için adı ile önek yapın *..*  *... /oil_price.exe*.

### <a name="create-files-in-code"></a>Kodda dosya oluşturma

Pandas `write_csv` işlevi gibi bir dosya oluşturan kodu kullanırken, yol adları her zaman proje klasörüne görelidir. Kullanılarak *... /* Not defteri durdurulduğunda ve kapatıldığında atılan bir oturum dosyası oluşturur.

## <a name="export-files"></a>Dosyaları dışarı aktar

Verileri proje panosundan veya bir not defteri içinden dışarı aktarabilirsiniz.

## <a name="export-files-from-the-project-dashboard"></a>Proje panosundan dosyaları dışarı aktarma

Proje panosunda, bir dosyaya sağ tıklayın ve **İndir**' i seçin:

![Proje öğesi bağlam menüsünde komutu indir](media/download-command.png)

Ayrıca bir dosya seçebilir ve panoda **İndir** komutunu (klavye kısayolu: d) kullanabilirsiniz:

![Proje panosunda araç çubuğu komutunu indir](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Not defteri 'ndeki veri menüsünden dosyaları dışarı aktarma

1. @No__t-1 **Dosya** **yükleme** menü komutunu seçin:

    ![Bir not defteri içinde veri Indirme menü komutu](media/file-menu-download.png)

1. Oturumdaki klasörleri gösteren bir açılan pencere görüntülenir; Proje *klasörü proje* dosyalarını içerir:

    ![Dosya ve klasörleri seçtiğiniz açılan pencerede veri Indirme komutu](media/file-menu-download-popup.png)

1. İndirmek istediğiniz dosya ve klasörlerin solundaki kutuları seçin ve ardından **Seçileni indir**' i seçin.

1. Not defteri, seçili dosyaları içeren tek bir *. zip* dosyası hazırlar ve bu dosya daha sonra tarayıcınızdan yaptığınız şekilde kaydedilir. Tek bir dosyayı indirdiğinizde bile not defteri bir *. zip* dosyası oluşturur.

## <a name="next-steps"></a>Sonraki adımlar

- [Bir not defterinde bulut verilerine erişme](access-data-resources-jupyter-notebooks.md)
