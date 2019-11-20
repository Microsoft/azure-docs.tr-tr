---
title: Azure 'da verileri çözümlemek için bir Jupyter Notebook kullanın Veri Gezgini
description: Bu konu başlığı altında, Azure Veri Gezgini 'da Jupyter Notebook ve Ksqlmagic uzantısı kullanılarak verilerin nasıl analiz edileceği gösterilmektedir.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ba21ec6ba82ac4984d3c51fc46f88de2cd56b2b2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933686"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Azure 'da verileri çözümlemek için bir Jupyter Notebook ve Ksqlmagic uzantısı kullanın Veri Gezgini

Jupyter Notebook, canlı kod, denklem, görselleştirmeler ve anlatım metni içeren belgeler oluşturmanıza ve paylaşmanıza olanak sağlayan açık kaynaklı bir Web uygulamasıdır. Kullanım, veri temizleme ve dönüştürme, sayısal benzetim, istatistiksel modelleme, veri görselleştirme ve makine öğrenimi özelliklerini içerir.
[Jupyter Notebook](https://jupyter.org/) , ek komutları destekleyerek çekirdeğin yeteneklerini genişleten sihirli işlevleri destekler. KQL Magic, kusto dil sorgularını yerel olarak çalıştırabilmeniz için Jupyter Notebook Python çekirdeğinin yeteneklerini genişleten bir komuttur. `render` Komutlarla tümleşik zengin Plot.ly kitaplığı kullanarak verileri sorgulamak ve görselleştirmek için Python ve kusto sorgu dilini kolayca birleştirebilirsiniz. Sorgu çalıştırmaya yönelik veri kaynakları desteklenir. Bu veri kaynakları Azure Veri Gezgini, günlük ve telemetri verilerine yönelik hızlı ve yüksek oranda ölçeklenebilir bir veri araştırma hizmeti, Azure Izleyici günlükleri ve Application Insights içerir. KQL Magic Ayrıca Azure Notebooks, Jupyıter Laboratuvarı ve Visual Studio Code Jupyıter Uzantısı ile de kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

- Azure Active Directory (AAD) üyesi olan kurumsal e-posta hesabı.
- Yerel makinenizde yüklü Jupyter Notebook veya Azure Notebooks kullanıp örnek [Azure Not defterini](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb) klonlayın

## <a name="install-kql-magic-library"></a>KQL Magic kitaplığını yükler

1. KQL Magic 'i yüklensin:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Azure Notebooks kullanırken, bu adım gerekli değildir.

1. KQL Magic 'i yükle:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Çekirdek > çekirdeği Değiştir > Python 3,6 ' e tıklayarak çekirdek sürümünü Python 3,6 olarak değiştirin.
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Azure Veri Gezgini yardım kümesine bağlanma

*Yardım* kümesinde barındırılan *örnekler* veritabanına bağlanmak için aşağıdaki komutu kullanın. Microsoft dışı AAD kullanıcıları için kiracı adını `Microsoft.com` AAD kiracınızla değiştirin.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Sorgulama ve görselleştirme

[Oluşturma işlecini](/azure/kusto/query/renderoperator) kullanarak verileri sorgulama ve Ploy.ly kitaplığını kullanarak verileri görselleştirme. Bu sorgu ve görselleştirme, yerel KQL kullanan tümleşik bir deneyim sağlar. Ksqlmagic, `timepivot` `pivotchart`, ve `ladderchart`dışındaki birçok grafiği destekler. Render,, ve `kind` `ysplit` `accumulate`hariç tüm özniteliklerle desteklenir. 

### <a name="query-and-render-piechart"></a>Iechart 'ı sorgula ve işle

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Sorgu ve işleme zaman çizelgesi

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Bu grafikler etkileşimlidir. Belirli bir saate yakınlaştırmak için bir zaman aralığı seçin.

### <a name="customize-the-chart-colors"></a>Grafik renklerini özelleştirme

Varsayılan renk paletini beğenmezseniz, palet seçeneklerini kullanarak grafikleri özelleştirin. Kullanılabilir paletler şurada bulunabilir: [KQL sihirli sorgu grafik sonucu için renk paleti seçin](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Palet listesi için:

    ```python
    %kql --palettes -popup_window
    ```

1. `cool` Renk paletini seçin ve sorguyu yeniden işle:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Python ile sorgu Parametreleştirme

KQL Magic, kusto sorgu dili ve Python arasında basit değişim yapılmasına izin verir. Daha fazla bilgi için: [Python ile KQL Magic sorgunuzu parametreleştirin](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>KQL sorgunuzda bir Python değişkeni kullanma

Verileri filtrelemek için Sorgunuzdaki bir Python değişkeninin değerini kullanabilirsiniz:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Sorgu sonuçlarını Pandas DataFrame 'e Dönüştür

Bir KQL sorgusunun sonuçlarına Pandas DataFrame içinde erişebilirsiniz. En son yürütülen sorgu sonuçlarına değişkene `_kql_raw_result_` göre erişin ve sonuçları Pandas dataframe 'e kolayca aşağıdaki gibi dönüştürün:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Örnek

Birçok analiz senaryosunda, çok sayıda sorgu içeren yeniden kullanılabilir Not defterleri oluşturmak ve sonuçları bir sorgudan sonraki sorgulara akışa almak isteyebilirsiniz. Aşağıdaki örnek, verileri filtrelemek için Python `statefilter` değişkenini kullanır.

1. En fazla olan en fazla `DamageProperty`10 durumu görüntülemek için bir sorgu çalıştırın:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. En üstteki durumu ayıklamak ve bir Python değişkenine ayarlamak için bir sorgu çalıştırın:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. `let` İfadesini ve Python değişkenini kullanarak bir sorgu çalıştırın:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Yardım komutunu çalıştırın:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Kullanılabilir tüm yapılandırmaların kullanımıyla `%config KQLmagic`ilgili bilgi almak için. Bağlantı sorunları ve hatalı sorgular gibi kusto hatalarını gidermek ve yakalamak için şunu kullanın`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Sonraki adımlar

Desteklenen tüm özellikleri içeren aşağıdaki örnek not defterlerini araştırmak için Yardım komutunu çalıştırın:
- [Azure Veri Gezgini için KQL Magic ile çalışmaya başlama](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Application Insights için KQL Magic ile çalışmaya başlama](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Azure Izleme günlükleri için KQL Magic ile çalışmaya başlama](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Python ile KQL Magic sorgunuzu parametrize](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [KQL sihirli sorgu grafik sonucu için renk paleti seçin](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
