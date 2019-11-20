---
title: Betik eylemlerini kullanarak Azure HDInsight kümelerini özelleştirme
description: Betik eylemlerini kullanarak Linux tabanlı HDInsight kümelerine özel bileşenler ekleyin. Betik eylemleri, küme yapılandırmasını özelleştirmek veya ton, Solr veya R gibi ek hizmetleri ve yardımcı programları eklemek için kullanılabilecek Bash betikleridir.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 16b0fdcbae51b30e14fbf7ea4d98699dfaf19804
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035748"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Betik eylemlerini kullanarak Azure HDInsight kümelerini özelleştirme

Azure HDInsight, kümeyi özelleştirmek için özel betikler çağıran **betik eylemleri** adlı bir yapılandırma yöntemi sağlar. Bu betikler ek bileşenleri yüklemek ve yapılandırma ayarlarını değiştirmek için kullanılır. Betik eylemleri, küme oluşturma sırasında veya sonrasında kullanılabilir.

Betik eylemleri, Azure Marketi 'Nde de HDInsight uygulaması olarak yayımlanabilir. HDInsight uygulamaları hakkında daha fazla bilgi için bkz. [Azure Marketi 'Nde HDInsight uygulaması yayımlama](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>İzinler

Etki alanına katılmış bir HDInsight kümesi için, kümeyle birlikte betik eylemlerini kullandığınızda gereken iki Apache ambarı izni vardır:

* **Ambarı. @ No__t-1CUSTOM @ no__t-2KOMUTUNU ÇALıŞTıRıN**. Varsayılan olarak, ambarı yönetici rolü bu izne sahiptir.
* **Küme. @ No__t-1CUSTOM @ no__t-2KOMUTUNU ÇALıŞTıRıN**. HDInsight kümesi Yöneticisi ve ambarı yöneticisinin bu izni varsayılan olarak vardır.

Etki alanına katılmış HDInsight ile izinlerle çalışma hakkında daha fazla bilgi için bkz. [Kurumsal güvenlik paketi HDInsight kümelerini yönetme](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Erişim denetimi

Azure aboneliğinizin Yöneticisi veya sahibi değilseniz, hesabınız HDInsight kümesini içeren kaynak grubuna en az katkıda bulunan erişime sahip olmalıdır.

Bir HDInsight kümesi oluşturursanız, Azure aboneliğine en az katkıda bulunan bir kişinin daha önce HDInsight sağlayıcısına kayıtlı olması gerekir. Sağlayıcı kaydı, aboneliğe Katkıda Bulunan erişimi olan bir kullanıcı abonelikte ilk kez kaynak oluşturduğunda gerçekleşir. Ayrıca, [rest kullanarak bir sağlayıcıyı kaydettiğinizde](https://msdn.microsoft.com/library/azure/dn790548.aspx), kaynak oluşturulmadan da yapılabilir.

Erişim yönetimiyle çalışma hakkında daha fazla bilgi alın:

* [Azure portalında erişim yönetimi ile çalışmaya başlama](../role-based-access-control/overview.md)
* [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanın](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Betik eylemlerini anlama

Bir betik eylemi, HDInsight kümesindeki düğümlerde çalışan Bash betiktir. Betik eylemlerinin özellikleri ve özellikleri şunlardır:

* HDInsight kümesinden erişilebilen bir URI üzerinde depolanmalıdır. Olası depolama konumları aşağıda verilmiştir:

    * Normal kümeler için:

      * ADLS 1.: hizmet sorumlusu HDInsight 'ın, Data Lake Storage erişmek için kullandığı hizmet, betikte okuma erişimine sahip olmalıdır. Data Lake Storage 1. depolanan betiklerin URI biçimi `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` ' dır.

      * HDInsight kümesi için birincil veya ek depolama hesabı olan bir Azure depolama hesabındaki blob. HDInsight, küme oluşturma sırasında bu tür depolama hesaplarının her ikisine de erişim izni verilir.

        > [!IMPORTANT]  
        > Bu Azure depolama hesabında depolama anahtarını döndürmeyin, çünkü bu komut dosyası eylemlerinin sonraki betiklerin başarısız olmasına neden olur.

      * Http://yollarla erişilebilen bir ortak dosya paylaşım hizmeti. Örnekler Azure blob, GitHub, OneDrive.

        Örneğin URI 'Ler için bkz. [örnek betik eylemi betikleri](#example-script-action-scripts).

     * ESP içeren kümeler için:

         * Wasb://veya wasbs://veya http [s]://URI 'Leri desteklenir.

* Yalnızca belirli düğüm türlerinde çalışacak şekilde kısıtlanabilir. Örnekler, baş düğüm veya çalışan düğümlerdir.

* Kalıcı veya geçici olabilir.

    Kalıcı betikler, ölçek işlemleri aracılığıyla kümeye eklenen yeni çalışan düğümlerini özelleştirmek için kullanılır. Kalıcı bir betik, ölçeklendirme işlemleri gerçekleştiğinde başka bir düğüm türüne değişiklikler de uygulayabilir. Bir baş düğüm bir örnektir.

  > [!IMPORTANT]  
  > Kalıcı betik eylemlerinin benzersiz bir adı olmalıdır.

    Geçici betikler kalıcı değildir. Betik çalıştırıldıktan sonra kümeye eklenen çalışan düğümlerine uygulanmaz. Ardından, geçici bir betiği kalıcı bir betiğe yükseltebilir veya kalıcı bir betiği geçici bir komut dosyasına indirgeyebilirsiniz.

  > [!IMPORTANT]  
  > Küme oluşturma sırasında kullanılan betik eylemleri otomatik olarak kalıcı hale getirilir.
  >
  > Özel olarak olması gerektiğini gösterseniz bile, başarısız olan betikler kalıcı olmaz.

* , Yürütme sırasında komut dosyası tarafından kullanılan parametreleri kabul edebilir.

* Küme düğümlerinde kök düzeyi ayrıcalıklarla çalıştırın.

* Azure portal, Azure PowerShell, Azure klasik CLı veya HDInsight .NET SDK ile kullanılabilir.

Küme, çalıştırılan tüm betiklerin geçmişini tutar. Geçmiş, yükseltme veya indirgeme işlemleri için bir betiğin KIMLIĞINI bulmanız gerektiğinde yardımcı olur.

> [!IMPORTANT]  
> Betik eylemi tarafından yapılan değişiklikleri geri almanın otomatik bir yolu yoktur. Değişiklikleri el ile tersine çevirin ya da onları geri yükleyen bir betik sağlayın.

### <a name="script-action-in-the-cluster-creation-process"></a>Küme oluşturma işlemindeki betik eylemi

Küme oluşturma sırasında kullanılan betik eylemleri, var olan bir kümede çalıştırılan betik eylemlerden biraz farklıdır:

* Komut dosyası otomatik olarak kalıcıdır.

* Betikteki bir hata, küme oluşturma işleminin başarısız olmasına neden olabilir.

Aşağıdaki diyagramda, oluşturma işlemi sırasında betik eyleminin ne zaman çalıştığı gösterilmektedir:

![Küme oluşturma sırasında HDInsight kümesi özelleştirmesi ve aşamaları][img-hdi-cluster-states]

HDInsight yapılandırılırken betik çalışır. Betik, kümedeki tüm belirtilen düğümlerde paralel olarak çalışır. Düğümlerde kök ayrıcalıklarla çalışır.

> [!NOTE]  
> Apache Hadoop ilgili hizmetler de dahil olmak üzere Hizmetleri durdurma ve başlatma gibi işlemleri gerçekleştirebilirsiniz. Hizmetleri durdurursanız, komut dosyası tamamlanmadan önce, ambarı hizmetinin ve Hadoop ile ilgili diğer hizmetlerin çalıştığından emin olun. Bu hizmetler, oluşturulduğu sırada kümenin sistem durumunu ve durumunu başarıyla tespit etmek için gereklidir.

Küme oluşturma sırasında aynı anda birçok betik eylemini kullanabilirsiniz. Bu betikler belirtildikleri sırayla çağırılır.

> [!IMPORTANT]  
> Betik eylemleri 60 dakika içinde bitmelidir veya zaman aşımına uğrar. Küme sağlama sırasında, komut dosyası diğer kurulum ve yapılandırma işlemleriyle eşzamanlı olarak çalışır. CPU süresi veya ağ bant genişliği gibi kaynaklara yarışma, komut dosyasının geliştirme ortamınızda olduğundan daha uzun sürmesine neden olabilir.
>
> Betiği çalıştırmak için gereken süreyi en aza indirmek için, kaynaktan uygulama indirme ve derleme gibi görevlerden kaçının. Uygulamaları önceden derleyin ve ikiliyi Azure Storage 'da depolayın.

### <a name="script-action-on-a-running-cluster"></a>Çalışan bir kümede betik eylemi

Zaten çalışan bir kümede çalıştırılan bir betikte hata otomatik olarak kümenin başarısız durumuna geçmesine neden olmaz. Bir komut dosyası tamamlandıktan sonra, kümenin çalışır duruma dönmesi gerekir.

> [!IMPORTANT]  
> Kümede çalışan bir durum olsa bile, başarısız olan betik bozulmuş olabilir. Örneğin, bir betik, küme için gereken dosyaları silebilir.
>
> Komut dosyaları eylemleri kök ayrıcalıklarla çalışır. Kümenize uygulamadan önce bir betiğin ne yaptığını anladığınızdan emin olun.

Bir kümeye komut dosyası uyguladığınızda, küme durumunun **çalışmayı** **kabul edildi**olarak değişir. Ardından, **HDInsight yapılandırmasına** ve son olarak, başarılı betikler için **çalışmaya** geri değişir. Betik durumu komut dosyası eylem geçmişine kaydedilir. Bu bilgiler, betiğin başarılı veya başarısız olduğunu bildirir. Örneğin, `Get-AzHDInsightScriptActionHistory` PowerShell cmdlet 'i bir betiğin durumunu gösterir. Aşağıdaki metne benzer bilgiler döndürür:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Küme Kullanıcı, yönetici, parolayı küme oluşturulduktan sonra değiştirirseniz, bu kümeye karşı çalıştırılan betik eylemleri başarısız olabilir. Çalışan düğümlerini hedefleyen kalıcı betik eylemleriniz varsa, kümeyi ölçeklendirirken bu betikler başarısız olabilir.

## <a name="example-script-action-scripts"></a>Örnek betik eylemi betikleri

Betik eylemi betikleri aşağıdaki yardımcı programlar aracılığıyla kullanılabilir:

* Azure portal
* Azure PowerShell
* Klasik Azure CLı
* An HDInsight .NET SDK

HDInsight, HDInsight kümelerine aşağıdaki bileşenleri yüklemek için komut dosyaları sağlar:

| Adı | Komut Dosyası |
| --- | --- |
| Azure depolama hesabı ekleme |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Bkz. [HDInsight 'a ek depolama hesapları ekleme](hdinsight-hadoop-add-storage.md). |
| Hue yükleme |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Bkz. [HDInsight Hadoop kümelerinde ton 'U yükleyip kullanma](hdinsight-hadoop-hue-linux.md). |
| Giraph 'yi yükler |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Bkz. [HDInsight Hadoop kümelerine Apache Giraph 'Yi Install](hdinsight-hadoop-giraph-install-linux.md). |
| Hive kitaplıklarını Önyükle |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Bkz. [HDInsight kümenizi oluştururken özel Apache Hive kitaplıkları ekleme](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Küme oluşturma sırasında betik eylemi kullanma

Bu bölümde, HDInsight kümesi oluştururken betik eylemlerini kullanmanın farklı yolları açıklanmaktadır.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Azure portal küme oluşturma sırasında bir betik eylemi kullanın

1. [Azure Portal kullanarak HDInsight 'Ta Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)bölümünde açıklandığı gibi bir küme oluşturmaya başlayın. Küme oluşturma sırasında 6. adım, **betik eylemleri**' ne ulaşır. **Isteğe bağlı** >  **+ Yeni Gönder**' e gidin.

    ![Azure portal kümesi betik eylemi](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-classic-script-action.png)

1. Önceden oluşturulmuş bir betik seçmek için __betik Seç__ girişini kullanın. Özel bir komut dosyası kullanmak için __özel__' i seçin. Ardından, betiğiniz için __ad__ ve __Bash betiği URI 'si__ sağlayın.

    ![Betik Seç formuna betik ekleme](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Aşağıdaki tablo, formundaki öğeleri açıklar:

    | Özellik | Değer |
    | --- | --- |
    | Betik seçin | Kendi komut dosyanızı kullanmak için __özel__' i seçin. Aksi takdirde, belirtilen betiklerin birini seçin. |
    | Adı |Betik eylemi için bir ad belirtin. |
    | Bash betiği URI 'SI |Betiğin URI 'sini belirtin. |
    | Baş/çalışan/ZooKeeper |Betiğin çalıştırıldığı düğümleri belirtin: **Head**, **Worker**veya **ZooKeeper**. |
    | Parametreler |Komut dosyası için gerekliyse parametreleri belirtin. |

    Komut dosyasının ölçeklendirme işlemleri sırasında uygulandığından emin olmak için __bu betiği Sürdür eylem__ girişini kullanın.

1. Betiği kaydetmek için __Oluştur__ ' u seçin. Daha sonra başka bir betik eklemek için __+ Yeni Gönder__ ' i kullanabilirsiniz.

    ![HDInsight birden çok betik eylemi](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Betikler ekleme işiniz bittiğinde, __küme Özeti__ bölümüne devam etmek için __Seç__ düğmesini ve sonra __İleri__ düğmesini seçin.

1. Kümeyi oluşturmak için __küme Özeti__ seçiminden __Oluştur__ ' u seçin.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarından betik eylemi kullanma

Betik eylemleri, Azure Resource Manager şablonlarıyla birlikte kullanılabilir. Bir örnek için bkz. [HDInsight Linux kümesi oluşturma ve betik eylemi çalıştırma](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Bu örnekte, komut dosyası eylemi aşağıdaki kod kullanılarak eklenir:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Bir şablonu dağıtma hakkında daha fazla bilgi alın:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Azure PowerShell kümeden küme oluşturma sırasında betik eylemi kullan

Bu bölümde, bir kümeyi özelleştirmek üzere betikleri çağırmak için [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet 'ini kullanırsınız. Başlamadan önce Azure PowerShell yükleyip yapılandırırken emin olun. Bu PowerShell komutlarını kullanmak için [az modüle](https://docs.microsoft.com/powershell/azure/overview)ihtiyacınız vardır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki betik, PowerShell kullanarak bir küme oluşturduğunuzda bir betik eyleminin nasıl uygulanacağını göstermektedir:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Kümenin oluşturulması birkaç dakika sürebilir.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>HDInsight .NET SDK 'dan küme oluşturma sırasında betik eylemi kullanma

HDInsight .NET SDK 'Sı, bir .NET uygulamasından HDInsight ile çalışmayı kolaylaştıran istemci kitaplıkları sağlar. Kod örneği için bkz. [HDInsight 'ta .NET SDK kullanarak Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Çalışan bir kümeye betik eylemi uygulama

Bu bölümde, çalışan bir kümeye betik eylemlerinin nasıl uygulanacağı açıklanmaktadır.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Azure portal çalışan bir kümeye betik eylemi uygulama

[Azure Portal](https://portal.azure.com)gidin:

1. Sol menüden **tüm hizmetler** >  **analiz** > **HDInsight kümelerine**gidin.

1. Listeden, varsayılan görünümü açan kümenizi seçin.

1. Varsayılan görünümden **Ayarlar**altında **betik eylemleri**' ni seçin.

1. **Betik eylemleri** sayfasının en üstünden **+ Yeni Gönder**' i seçin.

    ![Çalışan bir kümeye betik ekleme](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Önceden oluşturulmuş bir betik seçmek için __betik Seç__ girişini kullanın. Özel bir komut dosyası kullanmak için __özel__' i seçin. Ardından, betiğiniz için __ad__ ve __Bash betiği URI 'si__ sağlayın.

    ![Betik Seç formuna betik ekleme](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Aşağıdaki tablo, formundaki öğeleri açıklar:

    | Özellik | Değer |
    | --- | --- |
    | Betik seçin | Kendi komut dosyanızı kullanmak için __özel__' i seçin. Aksi takdirde, bir belirtilen betiği seçin. |
    | Adı |Betik eylemi için bir ad belirtin. |
    | Bash betiği URI 'SI |Betiğin URI 'sini belirtin. |
    | Baş/çalışan/Zookeeper |Betiğin çalıştırıldığı düğümleri belirtin: **Head**, **Worker**veya **ZooKeeper**. |
    | Parametreler |Komut dosyası için gerekliyse parametreleri belirtin. |

    Komut dosyasının ölçeklendirme işlemleri sırasında uygulandığından emin olmak için __bu betiği Sürdür eylem__ girişini kullanın.

1. Son olarak, komut dosyasını kümeye uygulamak için **Oluştur** düğmesini seçin.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Azure PowerShell 'den çalışan bir kümeye betik eylemi uygulama

Bu PowerShell komutlarını kullanmak için [az modüle](https://docs.microsoft.com/powershell/azure/overview)ihtiyacınız vardır.

Aşağıdaki örnek, çalışan bir kümeye nasıl betik eylemi uygulanacağını gösterir:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

İşlem bittikten sonra aşağıdaki metne benzer bilgiler alırsınız:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Azure CLı 'dan çalışan bir kümeye betik eylemi uygulama

Başlamadan önce Azure CLı 'yi yüklediğinizden ve yapılandırmadığınızdan emin olun. Daha fazla bilgi için bkz. [Azure klasık CLI 'Yı yüklemeyin](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Azure Resource Manager moduna geç:

    ```bash
    azure config mode arm
    ```

2. Azure aboneliğinizde kimlik doğrulaması yapın:

    ```bash
    azure login
    ```

3. Çalışan bir kümeye betik eylemi uygulayın:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Bu komutun parametrelerini atlarsanız sizden sorulur. @No__t-0 ile belirttiğiniz betik parametreleri kabul ediyorsa, bunları `-p` parametresini kullanarak belirtebilirsiniz.

    Geçerli düğüm türleri `headnode`, `workernode` ve `zookeeper` ' dir. Komut dosyasının birkaç düğüm türüne uygulanması gerekiyorsa, noktalı virgülle ayrılmış türleri @no__t belirtin-0. Örneğin, `-n headnode;workernode`.

    Betiği kalıcı hale getirmek için @no__t ekleyin-0. Betiği daha sonra `azure hdinsight script-action persisted set` kullanarak da kalıcı hale getirebilirsiniz.

    İş bittikten sonra aşağıdaki metin gibi bir çıktı alırsınız:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>REST API kullanarak çalışan bir kümeye betik eylemi uygulama

Bkz. [Azure HDInsight 'Ta küme REST API](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>HDInsight .NET SDK ' dan çalışan bir kümeye betik eylemi uygulama

Bir kümeye betikleri uygulamak üzere .NET SDK kullanmanın bir örneği için bkz. [çalışan bir Linux tabanlı HDInsight kümesine karşı betik eylemi uygulama](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Geçmişi görüntüleme ve betik eylemlerini yükseltme ve indirgeme

### <a name="the-azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Sol menüden **tüm hizmetler** > **analiz** > **HDInsight kümelerine**gidin.

1. Listeden, varsayılan görünümü açan kümenizi seçin.

1. Varsayılan görünümden **Ayarlar**altında **betik eylemleri**' ni seçin.

1. Bu küme için betikler geçmişi betik eylemleri bölümünde görüntülenir. Bu bilgiler, kalıcı betikler listesini içerir. Aşağıdaki ekran görüntüsünde, bu kümede Solr betiğinin çalıştırıldığı gösterilmektedir. Ekran görüntüsünde kalıcı betikler gösterilmez.

    ![Portal betik eylemleri gönderme geçmişi](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Bu betiğin **Özellikler** bölümünü göstermek için geçmişten bir komut dosyası seçin. Ekranın en üstünden betiği yeniden çalıştırabilir veya yükseltebilirsiniz.

    ![Betik eylemleri özellikleri yükseltir](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Ayrıca, eylemler gerçekleştirmek için komut dosyası eylemleri bölümündeki girdilerin sağ tarafındaki **.** .. ve üç nokta simgesini de seçebilirsiniz.

    ![Kalıcı betik eylemleri silme](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | İşlev |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Kalıcı betik eylemleriyle ilgili bilgileri alın. |
| `Get-AzHDInsightScriptActionHistory` |Belirli bir betiğin kümesine veya ayrıntılarına uygulanan betik eylemlerinin geçmişini alın. |
| `Set-AzHDInsightPersistedScriptAction` |Geçici betik eylemini kalıcı betik eylemine yükseltin. |
| `Remove-AzHDInsightPersistedScriptAction` |Kalıcı bir betik eylemini geçici bir eyleme indirgeyin. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` bir komut dosyası tarafından gerçekleştirilen eylemleri geri almaz. Bu cmdlet yalnızca kalıcı bayrağını kaldırır.

Aşağıdaki örnek betik, bir betiği yükseltmek ve alçaltmak için cmdlet 'leri kullanmayı göstermektedir.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Klasik Azure CLı

| cmdlet | İşlev |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Kalıcı betik eylemlerinin bir listesini alın. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Belirli bir kalıcı betik eylemiyle ilgili bilgileri alın. |
| `azure hdinsight script-action history list <clustername>` |Kümeye uygulanan betik eylemlerinin geçmişini alın. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Belirli bir betik eylemiyle ilgili bilgileri alın. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Geçici betik eylemini kalıcı betik eylemine yükseltin. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Kalıcı bir betik eylemini geçici bir eyleme indirgeyin. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` bir komut dosyası tarafından gerçekleştirilen eylemleri geri almaz. Bu cmdlet yalnızca kalıcı bayrağını kaldırır.

### <a name="the-hdinsight-net-sdk"></a>HDInsight .NET SDK 'Sı

.NET SDK kullanarak bir kümeden betik geçmişi alma, betikleri yükseltme veya indirgeme hakkında bir örnek için bkz. [çalışan bir Linux tabanlı HDInsight kümesine karşı betik eylemi uygulama](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Bu örnek ayrıca, .NET SDK kullanarak bir HDInsight uygulamasının nasıl yükleneceğini gösterir.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight kümelerinde kullanılan açık kaynaklı yazılım desteği

Microsoft Azure HDInsight hizmeti Apache Hadoop etrafında oluşturulmuş açık kaynaklı teknolojilerin ekosistemini kullanır. Microsoft Azure, açık kaynaklı teknolojiler için genel bir destek düzeyi sağlar. Daha fazla bilgi için [Azure desteği SSS](https://azure.microsoft.com/support/faq/)konusunun **Destek kapsamı** bölümüne bakın. HDInsight hizmeti, yerleşik bileşenler için ek bir destek düzeyi sağlar.

HDInsight hizmetinde iki tür açık kaynaklı bileşen mevcuttur:

* **Yerleşik bileşenler**. Bu bileşenler HDInsight kümelerine önceden yüklenir ve kümenin temel işlevlerini sağlar. Aşağıdaki bileşenler bu kategoriye aittir:

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * Hive sorgu dili [Hiveql](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    [HDInsight ile kullanılabilen Apache Hadoop bileşenleri ve sürümleri olan](hdinsight-component-versioning.md) küme bileşenlerinin tam listesi mevcuttur

* **Özel bileşenler**. Kümenin bir kullanıcısı olarak, kuruluşunuzda bulunan veya sizin tarafınızdan oluşturulan herhangi bir bileşeni iş yükünüze yükleyebilir veya kullanabilirsiniz.

> [!WARNING]  
> HDInsight kümesiyle birlikte sunulan bileşenler tam olarak desteklenmektedir. Microsoft Desteği, bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Microsoft Desteği sorunu çözebiliyor olabilir. Ya da bu teknoloji için derin uzmanlığın bulunduğu açık kaynaklı teknolojiler için kullanılabilir kanalları da sunmanız istenebilir. Birçok topluluk sitesi kullanılabilir. HDInsight ve [Stack Overflow](https://stackoverflow.com) [için MSDN Forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) örnekleri aşağıda verilmiştir.
>
> Apache projelerinde Ayrıca, [Apache Web sitesinde](https://apache.org)proje siteleri de vardır. [Hadoop](https://hadoop.apache.org/)bir örnektir.

HDInsight hizmeti özel bileşenleri kullanmak için çeşitli yollar sağlar. Aynı destek düzeyi, bir bileşenin küme üzerinde nasıl kullanıldığını veya yüklendiğine bakılmaksızın geçerlidir. Aşağıdaki listede, HDInsight kümelerinde özel bileşenlerin kullanıldığı en yaygın yollar açıklanmaktadır:

1. **İş gönderme**. Hadoop veya özel bileşenleri çalıştıran veya kullanan diğer iş türleri de kümeye gönderilebilir.

2. **Küme özelleştirmesi**. Küme oluşturma sırasında, küme düğümlerinde yüklü ek ayarları ve özel bileşenleri belirtebilirsiniz.

3. **Örnekler**. Popüler özel bileşenlerde, Microsoft ve diğerleri bu bileşenlerin HDInsight kümelerinde nasıl kullanılabileceği hakkında örnekler sağlayabilir. Bu örnekler destek olmadan sağlanır.

## <a name="troubleshooting"></a>Sorun giderme

Betik eylemleri tarafından günlüğe kaydedilen bilgileri görüntülemek için, ambarı Web Kullanıcı arabirimini kullanabilirsiniz. Küme oluşturma sırasında betik başarısız olursa Günlükler, kümeyle ilişkili varsayılan depolama hesabında da kullanılabilir. Bu bölümde, bu seçeneklerin her ikisi de kullanılarak günlüklerin nasıl alınacağını gösteren bilgiler sağlanmaktadır.

### <a name="the-apache-ambari-web-ui"></a>Apache ambarı Web Kullanıcı arabirimi

1. Tarayıcınızda https://CLUSTERNAME.azurehdinsight.net ' a gidin. **CLUSTERNAME** değerini HDInsight kümenizin adıyla değiştirin.

    İstendiğinde, küme için yönetici hesap adı, **yönetici**ve parolayı girin. Yönetici kimlik bilgilerini bir Web formunda yeniden girmeniz gerekebilir.

2. Sayfanın üst kısmındaki çubuktan **Ops** girişini seçin. Bir liste, ambarı aracılığıyla kümede yapılan geçerli ve önceki işlemleri görüntüler.

    ![Ops seçiliyken ambarı Web UI çubuğu](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

3. **İşlemler** sütununda **@ no__t-1customscriptaction** çalıştıran girişleri bulun. Bu girişler, betik eylemleri çalıştırıldığında oluşturulur.

    ![Apache ambarı betik eylem işlemleri](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    **Stdout** ve **stderr** çıkışını görüntülemek için **run\customscriptaction** girişini seçin ve bağlantılar aracılığıyla detaya gidin. Bu çıktı, komut dosyası çalıştırıldığında üretilir ve yararlı bilgiler içerebilir.

### <a name="access-logs-from-the-default-storage-account"></a>Varsayılan depolama hesabındaki günlüklere erişin

Küme oluşturma bir betik hatası nedeniyle başarısız olursa Günlükler küme depolama hesabında tutulur.

* Depolama günlükleri `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` ' da kullanılabilir.

    ![Betik eylemi günlükleri](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Bu dizin altında Günlükler, **baş düğümüne**, **çalışan düğümü**ve **Zookeeper düğümü**için ayrı olarak düzenlenir. Aşağıdaki örneklere bakın:

    * **Headnode**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Çalışan düğümü**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper düğümü**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Karşılık gelen konağın tüm **stdout** ve **stderr** depolama hesabına yüklenir. Her betik eylemi için bir **çıkış @no__t -1. txt** ve **Errors-@no__t -3. txt** . **Output-*. txt** dosyası, konakta çalıştırılan betiğin URI 'si hakkında bilgiler içerir. Aşağıdaki metin bu bilgilere bir örnektir:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Yinelenen olarak aynı ada sahip bir betik eylemi kümesi oluşturmanız mümkündür. Bu durumda, ilgili günlükleri **Tarih** klasörü adı temelinde ayırabilirsiniz. Örneğin, bir kümenin klasör yapısı, farklı tarihlerde oluşturulan **MyCluster**, aşağıdaki günlük girdilerine benzer şekilde görünür:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Aynı günde aynı ada sahip bir betik eylemi kümesi oluşturursanız, ilgili günlük dosyalarını tanımlamak için benzersiz öneki kullanabilirsiniz.

* Gece yarısı 12:00 ' de bir küme oluşturursanız, günlük dosyalarının iki gün boyunca yayılması olasıdır. Bu durumda, aynı küme için iki farklı tarih klasörü görürsünüz.

* Günlük dosyalarını varsayılan kapsayıcıya yüklemek, özellikle büyük kümeler için beş dakikaya kadar sürebilir. Bu nedenle, günlüklere erişmek istiyorsanız, bir betik eylemi başarısız olursa kümeyi hemen silmelisiniz.

### <a name="ambari-watchdog"></a>Ambarı izleme

> [!WARNING]  
> Hdinsightwatchdog, Linux tabanlı HDInsight kümenizdeki ambarı izleme, için parolayı değiştirmeyin. Bu hesabın parolasının değiştirilmesi, HDInsight kümesinde yeni betik eylemleri çalıştırma yeteneğini keser.

### <a name="cant-import-name-blobservice"></a>BlobService adı içeri aktarılamıyor

__Belirtiler__. Betik eylemi başarısız olur. Aşağıdaki hata gibi metinler, ambarı 'nda işlemi görüntülediğinizde görüntülenir:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Neden__. Bu hata, HDInsight kümesine dahil edilen Python Azure depolama istemcisini yükseltirseniz oluşur. HDInsight, Azure Storage Client 0.20.0 gerektirir.

__Çözümleme__. Bu hatayı çözmek için `ssh` kullanarak her bir küme düğümüne el ile bağlanın. Doğru depolama istemci sürümünü yeniden yüklemek için aşağıdaki komutu çalıştırın:

```bash
sudo pip install azure-storage==0.20.0
```

SSH ile kümeye bağlanma hakkında daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'a (Apache Hadoop) bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Geçmiş, küme oluşturma sırasında kullanılan betikleri göstermiyor

Kümeniz 15 Mart 2016 ' den önce oluşturulduysa, betik eylemi geçmişinde bir giriş görmeyebilirsiniz. Kümenin yeniden boyutlandırılması, betiklerin betik eylemi geçmişinde görünmesine neden olur.

İki istisna mevcuttur:

* Kümeniz 1 Eylül 2015 ' den önce oluşturulmuştur. Bu tarih, betik eylemlerinin tanıtılmasından yapılır. Bu tarihten önce oluşturulan herhangi bir küme, küme oluşturma için betik eylemlerini kullanamadı.

* Küme oluşturma sırasında birden çok betik eylemi kullandınız. Ya da birden çok komut dosyası için aynı adı, aynı URI 'yi, ancak birden çok komut dosyası için farklı parametreleri kullandınız. Bu durumlarda, şu hatayı alırsınız:

    Mevcut betiklerde çakışan betik adları nedeniyle bu kümede yeni betik eylemleri çalıştırılamıyor. Küme oluşturulurken belirtilen betik adları benzersiz olmalıdır. Mevcut betikler yeniden boyutlandırmayla çalıştırılır.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight için betik eylem betikleri geliştirme](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight kümelerine Apache Giraph 'yi yükleyip kullanma](hdinsight-hadoop-giraph-install-linux.md)
* [HDInsight kümesine ek depolama ekleme](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Küme oluşturma sırasında aşamalar"
