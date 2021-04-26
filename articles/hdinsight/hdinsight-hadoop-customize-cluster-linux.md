---
title: Betik eylemlerini kullanarak Azure HDInsight kümelerini özelleştirme
description: Betik eylemleri kullanarak HDInsight kümelerine özel bileşenler ekleyin. Betik eylemleri, küme yapılandırmasını özelleştirmek için kullanılan Bash betikleridir. Ya da ton, Solr veya R gibi ek hizmetler ve yardımcı programlar ekleyin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: d5500c04b4299c215eba843530dc84932fa10894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775052"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Betik eylemlerini kullanarak Azure HDInsight kümelerini özelleştirme

Azure HDInsight, kümeyi özelleştirmek için özel betikler çağıran **betik eylemleri** adlı bir yapılandırma yöntemi sağlar. Bu betikler ek bileşenleri yüklemek ve yapılandırma ayarlarını değiştirmek için kullanılır. Betik eylemleri, küme oluşturma sırasında veya sonrasında kullanılabilir.

Betik eylemleri, Azure Marketi 'Nde de HDInsight uygulaması olarak yayımlanabilir. HDInsight uygulamaları hakkında daha fazla bilgi için bkz. [Azure Marketi 'Nde HDInsight uygulaması yayımlama](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Betik eylemlerini anlama

Bir betik eylemi, HDInsight kümesindeki düğümlerde çalışan Bash betiktir. Betik eylemlerinin özellikleri ve özellikleri şunlardır:

- Bash betiği URI 'SI (dosyaya erişim konumu) HDInsight kaynak sağlayıcısı ve kümesinden erişilebilir olmalıdır.
- Olası depolama konumları aşağıda verilmiştir:

   - Normal (ESP olmayan) kümeler için:
     - HDInsight kümesi için birincil veya ek depolama hesabı olan bir Azure depolama hesabındaki blob. HDInsight, küme oluşturma sırasında bu tür depolama hesaplarının her ikisine de erişim izni verilir.
    
       > [!IMPORTANT]  
       > Bu Azure depolama hesabında depolama anahtarını döndürmeyin, çünkü bu komut dosyası eylemlerinin sonraki betiklerin başarısız olmasına neden olur.

     - Data Lake Storage 1.: hizmet sorumlusu HDInsight 'ın, Data Lake Storage erişmek için kullandığı hizmet, betikte okuma erişimine sahip olmalıdır. Bash betiği URI biçimi `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . 

     - Data Lake Storage 2. betik eylemleri için kullanılması önerilmez. `abfs://` Bash betiği URI 'SI için desteklenmez. `https://` URI 'Ler olasıdır, ancak bunlar ortak erişime sahip kapsayıcılar için ve HDInsight kaynak sağlayıcısı için güvenlik duvarı açıktır ve bu nedenle önerilmez.

     - Yollar aracılığıyla erişilebilen ortak bir dosya paylaşım hizmeti `https://` . Azure blob, GitHub veya OneDrive örnekleri verilmiştir. Örneğin URI 'Ler için bkz. [örnek betik eylemi betikleri](#example-script-action-scripts).

  - ESP içeren kümeler için, `wasb://` veya `wasbs://` veya `http[s]://` URI 'leri desteklenir.

- Betik eylemleri yalnızca belirli düğüm türlerinde çalışacak şekilde kısıtlanabilir. Örnekler, baş düğüm veya çalışan düğümlerdir.
- Betik eylemleri kalıcı veya *geçici* olabilir.

  - Kalıcı betik eylemlerinin benzersiz bir adı olmalıdır. Kalıcı betikler, ölçek işlemleri aracılığıyla kümeye eklenen yeni çalışan düğümlerini özelleştirmek için kullanılır. Kalıcı bir betik, ölçeklendirme işlemleri gerçekleştiğinde başka bir düğüm türüne değişiklikler de uygulayabilir. Bir baş düğüm bir örnektir.
  - *Geçici betikler kalıcı* değildir. Küme oluşturma sırasında kullanılan betik eylemleri otomatik olarak kalıcı hale getirilir. Betik çalıştırıldıktan sonra kümeye eklenen çalışan düğümlerine uygulanmaz. Ardından *, geçici bir* betiği kalıcı bir betiğe yükseltebilir veya kalıcı bir betiği *geçici* bir komut dosyasına indirgeyebilirsiniz. Özel olarak olması gerektiğini gösterseniz bile, başarısız olan betikler kalıcı olmaz.

- Betik eylemleri yürütme sırasında komut dosyası tarafından kullanılan parametreleri kabul edebilir.
- Betik eylemleri, küme düğümlerinde kök düzeyi ayrıcalıklarla çalışır.
- Betik eylemleri Azure portal, Azure PowerShell, Azure CLı veya HDInsight .NET SDK 'Sı aracılığıyla kullanılabilir.
- VM 'deki hizmet dosyalarını kaldırmak veya değiştirmek için betik eylemleri, hizmet durumunu ve kullanılabilirliğini etkileyebilir.

Küme, çalıştırılan tüm betiklerin geçmişini tutar. Geçmiş, yükseltme veya indirgeme işlemleri için bir betiğin KIMLIĞINI bulmanız gerektiğinde yardımcı olur.

> [!IMPORTANT]  
> Betik eylemi tarafından yapılan değişiklikleri geri almanın otomatik bir yolu yoktur. Değişiklikleri el ile tersine çevirin ya da onları geri yükleyen bir betik sağlayın.

## <a name="permissions"></a>İzinler

Etki alanına katılmış bir HDInsight kümesi için, kümeyle birlikte betik eylemlerini kullandığınızda gereken iki Apache ambarı izni vardır:

* **Ambarı. \_özel \_ komutu çalıştırın**. Varsayılan olarak, ambarı yönetici rolü bu izne sahiptir.
* **Küme. \_özel \_ komutu çalıştırın**. HDInsight kümesi Yöneticisi ve ambarı yöneticisinin bu izni varsayılan olarak vardır.

Etki alanına katılmış HDInsight ile izinlerle çalışma hakkında daha fazla bilgi için bkz. [Kurumsal güvenlik paketi HDInsight kümelerini yönetme](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Erişim denetimi

Azure aboneliğinizin Yöneticisi veya sahibi değilseniz, hesabınızın `Contributor` HDInsight kümesini içeren kaynak grubuna en az erişimi olmalıdır.

Azure aboneliğine en az katkıda bulunan erişimi olan birisi, sağlayıcıya daha önce kaydolmalıdır. Sağlayıcıya katkıda bulunan erişimi olan bir Kullanıcı bir kaynak oluşturduğunda sağlayıcı kaydı gerçekleşir. Kaynak oluşturmadan, bkz. [rest kullanarak sağlayıcı kaydetme](/rest/api/resources/providers#Providers_Register).

Erişim yönetimiyle çalışma hakkında daha fazla bilgi alın:

- [Azure portalında erişim yönetimi ile çalışmaya başlama](../role-based-access-control/overview.md)
- [Azure abonelik kaynaklarınıza erişimi yönetmek için Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Betik eylemlerini kullanma yöntemleri

Küme ilk oluşturulduğunda veya var olan bir kümede çalışırken çalışacak bir betik eylemi yapılandırma seçeneğiniz vardır.

### <a name="script-action-in-the-cluster-creation-process"></a>Küme oluşturma işlemindeki betik eylemi

Küme oluşturma sırasında kullanılan betik eylemleri, var olan bir kümede çalıştırılan betik eylemlerden biraz farklıdır:

- Komut dosyası otomatik olarak kalıcıdır.
- Betikteki bir hata, küme oluşturma işleminin başarısız olmasına neden olabilir.

Aşağıdaki diyagramda, oluşturma işlemi sırasında betik eyleminin ne zaman çalıştığı gösterilmektedir:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Küme oluşturma sırasında aşamalar" border="false":::

HDInsight yapılandırılırken betik çalışır. Betik, kümedeki tüm belirtilen düğümlerde paralel olarak çalışır. Düğümlerde kök ayrıcalıklarla çalışır.

Apache Hadoop ilgili hizmetler de dahil olmak üzere Hizmetleri durdurma ve başlatma gibi işlemler yapabilirsiniz. Hizmetleri durdurursanız, komut dosyası tamamlanmadan önce ambarı ve diğer Hadoop ile ilgili hizmetlerin çalıştığından emin olun. Bu gerekli hizmetler, oluşturulduğu sırada kümenin sistem durumunu ve durumunu beliryordu.

Küme oluşturma sırasında aynı anda birçok betik eylemini kullanabilirsiniz. Bu betikler belirtildikleri sırayla çağırılır.

> [!IMPORTANT]  
> Betik eylemleri 60 dakika içinde bitmelidir veya zaman aşımına uğrar. Küme sağlama sırasında, komut dosyası diğer kurulum ve yapılandırma işlemleriyle eşzamanlı olarak çalışır. CPU süresi veya ağ bant genişliği gibi kaynaklara yarışma, komut dosyasının geliştirme ortamınızda olduğundan daha uzun sürmesine neden olabilir.
>
> Betiği çalıştırmak için gereken süreyi en aza indirmek için, kaynaktan uygulama indirme ve derleme gibi görevlerden kaçının. Uygulamaları önceden derleyin ve ikiliyi Azure Storage 'da depolayın.

### <a name="script-action-on-a-running-cluster"></a>Çalışan bir kümede betik eylemi

Zaten çalışan bir kümede bir betik hatası kümenin başarısız durumuna geçmesine otomatik olarak neden olmaz. Bir komut dosyası tamamlandıktan sonra, kümenin çalışır duruma dönmesi gerekir. Kümede çalışan bir durum olsa bile, başarısız olan betik bozulmuş olabilir. Örneğin, bir betik, küme için gereken dosyaları silebilir.

Komut dosyaları eylemleri kök ayrıcalıklarla çalışır. Kümenize uygulamadan önce bir betiğin ne yaptığını anladığınızdan emin olun.

Bir kümeye komut dosyası uyguladığınızda, küme durumunun **çalışmayı** **kabul edildi** olarak değişir. Ardından, **HDInsight yapılandırmasına** ve son olarak, başarılı betikler için **çalışmaya** geri değişir. Betik durumu komut dosyası eylem geçmişine kaydedilir. Bu bilgiler, betiğin başarılı veya başarısız olduğunu bildirir. Örneğin, `Get-AzHDInsightScriptActionHistory` PowerShell cmdlet 'i bir betiğin durumunu gösterir. Aşağıdaki metne benzer bilgiler döndürür:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Küme Kullanıcı, yönetici, parolayı küme oluşturulduktan sonra değiştirirseniz, bu kümeye karşı çalıştırılan betik eylemleri başarısız olabilir. Çalışan düğümlerini hedefleyen kalıcı betik eylemleriniz varsa, kümeyi ölçeklendirirken bu betikler başarısız olabilir.

## <a name="example-script-action-scripts"></a>Örnek betik eylemi betikleri

Betik eylemi betikleri aşağıdaki yardımcı programlar aracılığıyla kullanılabilir:

* Azure portalı
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK 'Sı

HDInsight, HDInsight kümelerine aşağıdaki bileşenleri yüklemek için komut dosyaları sağlar:

| Name | Komut Dosyası |
| --- | --- |
| Azure depolama hesabı ekleme |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Bkz. [HDInsight 'a ek depolama hesapları ekleme](hdinsight-hadoop-add-storage.md). |
| Ton 'yi yükler |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Bkz. [HDInsight Hadoop kümelerinde ton 'U yükleyip kullanma](hdinsight-hadoop-hue-linux.md). |
| Hive kitaplıklarını Önyükle |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Bkz. [HDInsight kümenizi oluştururken özel Apache Hive kitaplıkları ekleme](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Küme oluşturma sırasında betik eylemi

Bu bölümde, HDInsight kümesi oluştururken betik eylemlerini kullanmanın farklı yolları açıklanmaktadır.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Azure portal küme oluşturma sırasında bir betik eylemi kullanın

1. [Azure Portal kullanarak HDInsight 'Ta Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)bölümünde açıklandığı gibi bir küme oluşturmaya başlayın. **Yapılandırma + fiyatlandırma** sekmesinden **+ betik eylemi Ekle**' yi seçin.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Azure portal kümesi betik eylemi":::

1. Önceden oluşturulmuş bir betik seçmek için __betik Seç__ girişini kullanın. Özel bir komut dosyası kullanmak için __özel__' i seçin. Ardından, betiğiniz için __ad__ ve __Bash betiği URI 'si__ sağlayın.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Betik Seç formuna betik ekleme":::

   Aşağıdaki tablo, formundaki öğeleri açıklar:

   | Özellik | Değer |
   | --- | --- |
   | Betik seçin | Kendi komut dosyanızı kullanmak için __özel__' i seçin. Aksi takdirde, belirtilen betiklerin birini seçin. |
   | Name |Betik eylemi için bir ad belirtin. |
   | Bash betiği URI 'SI |Betiğin URI 'sini belirtin. |
   | Baş/çalışan/ZooKeeper |Betiğin çalıştırıldığı düğümleri belirtin: **Head**, **Worker** veya **ZooKeeper**. |
   | Parametreler |Komut dosyası için gerekliyse parametreleri belirtin. |

   Komut dosyasının ölçeklendirme işlemleri sırasında uygulandığından emin olmak için __bu betiği Sürdür eylem__ girişini kullanın.

1. Betiği kaydetmek için __Oluştur__ ' u seçin. Daha sonra başka bir betik eklemek için __+ Yeni Gönder__ ' i kullanabilirsiniz.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="HDInsight birden çok betik eylemi":::

   Betikler ekleme işiniz bittiğinde **yapılandırma + fiyatlandırma** sekmesine dönersiniz.

1. Kalan küme oluşturma adımlarını her zamanki gibi doldurun.

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

- [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../azure-resource-manager/templates/deploy-powershell.md)
- [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Azure PowerShell kümeden küme oluşturma sırasında betik eylemi kullan

Bu bölümde, bir kümeyi özelleştirmek üzere betikleri çağırmak için [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet 'ini kullanırsınız. Başlamadan önce Azure PowerShell yükleyip yapılandırırken emin olun. Bu PowerShell komutlarını kullanmak için [az modüle](/powershell/azure/)ihtiyacınız vardır.

Aşağıdaki betik, PowerShell kullanarak bir küme oluşturduğunuzda bir betik eyleminin nasıl uygulanacağını göstermektedir:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Kümenin oluşturulması birkaç dakika sürebilir.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>HDInsight .NET SDK 'dan küme oluşturma sırasında betik eylemi kullanma

HDInsight .NET SDK 'Sı, bir .NET uygulamasından HDInsight ile çalışmayı kolaylaştıran istemci kitaplıkları sağlar. Kod örneği için bkz. [betik eylemleri](/dotnet/api/overview/azure/hdinsight#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Çalışan bir kümeye betik eylemi

Bu bölümde, çalışan bir kümede betik eylemlerinin nasıl uygulanacağı açıklanmaktadır.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Azure portal çalışan bir kümeye betik eylemi uygulama

1. [Azure Portal](https://portal.azure.com) oturum açın ve kümenizi bulun.

1. Varsayılan görünümden **Ayarlar** altında **betik eylemleri**' ni seçin.

1. **Betik eylemleri** sayfasının en üstünden **+ Yeni Gönder**' i seçin.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Çalışan bir kümeye betik ekleme":::

1. Önceden oluşturulmuş bir betik seçmek için __betik Seç__ girişini kullanın. Özel bir komut dosyası kullanmak için __özel__' i seçin. Ardından, betiğiniz için __ad__ ve __Bash betiği URI 'si__ sağlayın.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Betik Seç formuna betik ekleme":::

   Aşağıdaki tablo, formundaki öğeleri açıklar:

   | Özellik | Değer |
   | --- | --- |
   | Betik seçin | Kendi komut dosyanızı kullanmak için __özel__' i seçin. Aksi takdirde, bir belirtilen betiği seçin. |
   | Name |Betik eylemi için bir ad belirtin. |
   | Bash betiği URI 'SI |Betiğin URI 'sini belirtin. |
   | Baş/çalışan/Zookeeper |Betiğin çalıştırıldığı düğümleri belirtin: **Head**, **Worker** veya **ZooKeeper**. |
   | Parametreler |Komut dosyası için gerekliyse parametreleri belirtin. |

   Komut dosyasının ölçeklendirme işlemleri sırasında uygulandığından emin olmak için __bu betiği Sürdür eylem__ girişini kullanın.

1. Son olarak, komut dosyasını kümeye uygulamak için **Oluştur** düğmesini seçin.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Azure PowerShell 'den çalışan bir kümeye betik eylemi uygulama

Bu PowerShell komutlarını kullanmak için [az modüle](/powershell/azure/)ihtiyacınız vardır. Aşağıdaki örnek, çalışan bir kümeye nasıl betik eylemi uygulanacağını gösterir:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

İşlem bittikten sonra aşağıdaki metne benzer bilgiler alırsınız:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Azure CLı 'dan çalışan bir kümeye betik eylemi uygulama

Başlamadan önce Azure CLı 'yi yüklediğinizden ve yapılandırmadığınızdan emin olun. En son sürüme sahip olduğunuzdan emin olun. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).

1. Azure aboneliğinizde kimlik doğrulaması yapın:

   ```azurecli
   az login
   ```

1. Çalışan bir kümeye betik eylemi uygulayın:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Geçerli roller şunlardır,,, `headnode` `workernode` `zookeepernode` `edgenode` . Betiğin birkaç düğüm türüne uygulanması gerekiyorsa, rolleri bir boşlukla ayırın. Örneğin, `--roles headnode workernode`.

   Betiği kalıcı hale getirmek için, ekleyin `--persist-on-success` . Ayrıca, kullanarak betiği daha sonra da kalıcı hale getirebilirsiniz `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>REST API kullanarak çalışan bir kümeye betik eylemi uygulama

Bkz. [Azure HDInsight 'Ta küme REST API](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>HDInsight .NET SDK ' dan çalışan bir kümeye betik eylemi uygulama

Bir kümeye betikleri uygulamak üzere .NET SDK kullanmanın bir örneği için bkz. [çalışan bir Linux tabanlı HDInsight kümesine karşı betik eylemi uygulama](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Geçmişi görüntüleme ve betik eylemlerini yükseltme ve indirgeme

### <a name="the-azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) oturum açın ve kümenizi bulun.

1. Varsayılan görünümden **Ayarlar** altında **betik eylemleri**' ni seçin.

1. Bu küme için betikler geçmişi betik eylemleri bölümünde görüntülenir. Bu bilgiler, kalıcı betikler listesini içerir. Aşağıdaki ekran görüntüsünde, bu kümede Solr betiğinin çalıştırıldığı gösterilmektedir. Ekran görüntüsünde kalıcı betikler gösterilmez.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="Portal betik eylemleri gönderme geçmişi":::

1. Bu betiğin **Özellikler** bölümünü göstermek için geçmişten bir komut dosyası seçin. Ekranın en üstünden betiği yeniden çalıştırabilir veya yükseltebilirsiniz.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Betik eylemleri özellikleri yükseltir":::

1. Ayrıca, eylemler için komut dosyası eylemleri bölümünde girişlerin sağındaki **..**. üç nokta simgesini de seçebilirsiniz.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Kalıcı betik eylemleri silme":::

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | İşlev |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Kalıcı betik eylemleriyle ilgili bilgileri alın. Bu cmdlet bir betik tarafından gerçekleştirilen eylemleri geri almaz, yalnızca kalıcı bayrağı kaldırır.|
| `Get-AzHDInsightScriptActionHistory` |Belirli bir betiğin kümesine veya ayrıntılarına uygulanan betik eylemlerinin geçmişini alın. |
| `Set-AzHDInsightPersistedScriptAction` |`ad hoc`Betik eylemini kalıcı betik eylemine yükseltin. |
| `Remove-AzHDInsightPersistedScriptAction` |Kalıcı betik eylemini bir eyleme indirgeyin `ad hoc` . |

Aşağıdaki örnek betik, bir betiği yükseltmek ve alçaltmak için cmdlet 'leri kullanmayı göstermektedir.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Komut | Açıklama |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_delete) |Kümenin belirtilen kalıcı betik eylemini siler. Bu komut, bir komut dosyası tarafından yapılan eylemleri geri almaz, yalnızca kalıcı bayrağı kaldırır.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_execute)|Belirtilen HDInsight kümesinde betik eylemlerini yürütün.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list) |Belirtilen küme için tüm kalıcı betik eylemlerini listeler. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list_execution_history)|Belirtilen küme için tüm betiklerin yürütme geçmişini listeler.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_promote)|, Belirtilen geçici betik yürütmeyi kalıcı bir betiğe yükseltir.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_show_execution_details)|Verilen betik yürütme KIMLIĞI için betik yürütme ayrıntısı alır.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK 'Sı

.NET SDK kullanarak bir kümeden betik geçmişi alma, betikleri yükseltme veya indirgeme hakkında bir örnek için bkz. [ çalışan bir Linux tabanlı HDInsight kümesine karşı betik eylemi uygulama](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Bu örnek ayrıca, .NET SDK kullanarak bir HDInsight uygulamasının nasıl yükleneceğini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight için betik eylem betikleri geliştirme](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight kümesine ek depolama ekleme](hdinsight-hadoop-add-storage.md)
* [Betik eylemlerindeki sorunları giderme](troubleshoot-script-action.md)
