---
title: Görsel öğeler
description: Verilerinizi görselleştirmek için Azure SYNAPSE not defterlerini kullanma
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 7e57cdca1d212e6077d685d95a8f869c12e546a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627957"
---
# <a name="visualize-data"></a>Verileri görselleştirme
Azure SYNAPSE, veri ambarlarında ve büyük veri analizi sistemlerinde fikir süresini hızlandıran tümleşik bir analiz hizmetidir. Veri görselleştirme, verilerinize ilişkin Öngörüler elde edebilmekte olan bir temel bileşendir. İnsanların anlaşılması büyük ve küçük verilerin daha kolay olmasına yardımcı olur. Ayrıca, veri gruplarındaki desenleri, eğilimleri ve aykırı değerleri algılamayı kolaylaştırır. 

Azure SYNAPSE Analytics 'te Apache Spark kullanırken, verilerinizi görselleştirmenize yardımcı olan SYNAPSE Not defteri grafik seçenekleri, popüler açık kaynaklı kitaplıklara erişim ve SYNAPSE SQL ve Power BI ile tümleştirme gibi çeşitli yerleşik seçenekler vardır.

## <a name="notebook-chart-options"></a>Not defteri grafik seçenekleri
Bir Azure SYNAPSE Not defteri kullanırken tablo sonuçları görünümünüzü grafik seçeneklerini kullanarak özelleştirilmiş bir grafiğe dönüştürebilirsiniz. Burada, herhangi bir kod yazmak zorunda kalmadan verilerinizi görselleştirebilirsiniz. 

### <a name="displaydf-function"></a>Display (DF) işlevi
```display```İşlevi, SQL sorguları ve veri çerçevelerini Apache Spark veri çerçevelerini ve RDDs 'yi zengin veri görselleştirmelere açmanızı sağlar. ```display```İşlev, PySpark, Scala, Java ve .NET içinde oluşturulan veri çerçeveleri veya RDD 'ler üzerinde kullanılabilir.

Grafik seçeneklerine erişmek için:
1. ```%%sql```MAGIC komutlarının çıktısı, varsayılan olarak işlenmiş tablo görünümünde görüntülenir. Ayrıca, ```display(df)``` işlenmiş tablo görünümünü oluşturmak Için Spark Dataframe veya dayanıklı Dağıtılmış veri kümeleri (RDD) işlevini çağırabilirsiniz. 
   
2. İşlenmiş Tablo görünümüne sahip olduktan sonra grafik görünümüne geçin.
   ![yerleşik grafikler](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Artık görselleştirmenizi aşağıdaki değerleri belirterek özelleştirebilirsiniz:

   | Yapılandırma | Açıklama |
   |--|--| 
   | Grafik Türü | ```display```İşlevi çubuk grafikler, dağılım çizimleri, çizgi grafikler ve daha fazlası dahil olmak üzere çok çeşitli grafik türlerini destekler |
   | Anahtar | X ekseni için değer aralığını belirtin|
   | Değer | Y ekseni değerleri için değer aralığını belirtin |
   | Seri grubu | Toplama gruplarını belirlemede kullanılır | 
   | Toplama | Görselleştirmenizdeki verileri toplamanın yöntemi| 
   
   
    > [!NOTE]
    > Varsayılan olarak, ```display(df)``` işlev, grafikleri işlemek için verilerin yalnızca ilk 1000 satırını alır. **Tüm sonuçlar üzerinde toplamayı** denetleyin ve **Uygula** düğmesine tıkladıktan sonra grafik oluşturma 'yı tüm veri kümesinden uygulayacaksınız. Grafik ayarı değiştiğinde bir Spark işi tetiklenecektir. Hesaplama işleminin tamamlanması ve grafiği işlemek için birkaç dakika sürebileceğini lütfen unutmayın.
   
4. İşiniz bittiğinde, son görselleştirmenizi görüntüleyebilir ve bunlarla etkileşim kurabilirsiniz!

### <a name="displaydf-statistic-details"></a>Display (DF) İstatistik ayrıntıları
<code>display(df, summary = true)</code>Belirli bir Apache Spark DataFrame 'in, sütun adı, sütun türü, benzersiz değerler ve her sütun için eksik değerleri içeren istatistik özetini denetlemek için kullanabilirsiniz. En küçük değerini, en büyük değerini, ortalama değeri ve standart sapmayı görmek için belirli bir sütunu da seçebilirsiniz.
    ![yerleşik grafikler-Özet](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (DF) seçeneği
Azure SYNAPSE Analytics Not defterleri, işlevini kullanarak HTML grafiklerini destekler ```displayHTML``` .

Aşağıdaki görüntü, [D3.js](https://d3js.org/)kullanarak görselleştirmeler oluşturma örneğidir.

   ![D3-js-örnek](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Yukarıdaki görselleştirmeyi oluşturmak için aşağıdaki kodu çalıştırın.

```python
displayHTML("&quot;&quot;<!DOCTYPE html>
<meta charset=&quot;utf-8&quot;>

<!-- Load d3.js -->
<script src=&quot;https://d3js.org/d3.v4.js&quot;></script>

<!-- Create a div where the graph will take place -->
<div id=&quot;my_dataviz&quot;></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select(&quot;#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Popüler kitaplıklar
Veri görselleştirmesine geldiğinde, Python birçok farklı özellik ile paketlenmiş birden çok grafikleme kitaplığı sunar. Varsayılan olarak, Azure SYNAPSE Analytics 'teki tüm Apache Spark havuzları, bir dizi seçkin ve popüler açık kaynak kitaplık içerir. Ayrıca, Azure SYNAPSE Analytics kitaplığı yönetim özelliklerini kullanarak ek kitaplıklar & sürümleri ekleyebilir veya yönetebilirsiniz. 

### <a name="matplotlib"></a>Matplotlib
Her kitaplık için yerleşik işleme işlevlerini kullanarak, Matplotlib gibi standart çizim kitaplıklarını işleyebilirsiniz.

Aşağıdaki görüntü, **Matplotlib** kullanarak bir çubuk grafik oluşturma örneğidir.
   ![Çizgi grafik örneği.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Yukarıdaki görüntüyü çizmek için aşağıdaki örnek kodu çalıştırın.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```


### <a name="bokeh"></a>Bokeh
Kullanarak **bokeh** gibi HTML veya etkileşimli kitaplıkları işleyebilirsiniz ```displayHTML(df)``` . 

Aşağıdaki resim **bokeh** kullanarak bir harita üzerinde karakter çizme örneğidir.

   ![bokeh-örnek](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Yukarıdaki görüntüyü çizmek için aşağıdaki örnek kodu çalıştırın.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator&quot;, y_axis_type=&quot;mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```


### <a name="plotly"></a>Plotly
**Plotly** gibi HTML veya etkileşimli kitaplıkları **displayhtml ()** kullanarak işleyebilirsiniz.

Aşağıdaki örnek kodu aşağıda bulunan görüntüyü çizmek için çalıştırın.

   ![plotly-örnek](./media/apache-spark-development-using-notebooks/synapse-plotly-image.png#lightbox)


```python
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

import plotly.express as px

fig = px.choropleth(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           scope="usa",
                           labels={'unemp':'unemployment rate'}
                          )
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# create an html document that embeds the Plotly plot
h = plotly.offline.plot(fig, output_type='div')

# display this html
displayHTML(h)
```


### <a name="additional-libraries"></a>Ek kitaplıklar 
Bu kitaplıkların ötesinde Azure SYNAPSE Analytics Runtime, veri görselleştirme için genellikle kullanılan aşağıdaki kitaplık kümesini de içerir:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 
- [Plotly](https://plotly.com/)

Kullanılabilir kitaplıklar ve sürümlerle ilgili en güncel bilgiler için Azure SYNAPSE Analytics çalışma zamanı [belgelerini](./spark/../apache-spark-version-support.md) ziyaret edebilirsiniz.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Isteğe bağlı SQL Apache Spark & kullanarak Power BI bağlanma
Azure SYNAPSE Analytics, veri mühendislerinin analiz çözümleri oluşturmasına olanak tanıyan Power BI ile tümleşir.

Azure SYNAPSE Analytics, farklı çalışma alanı hesaplama altyapılarının Spark havuzları ve sunucusuz SQL havuzu arasında veritabanlarını ve tabloları paylaşmasına izin verir. [Paylaşılan meta veri modelini](../metadata/overview.md)kullanarak, Apache Spark tablolarınızı SQL isteğe bağlı olarak sorgulama yapabilirsiniz. İşiniz bittiğinde, eşitlenmiş Spark tablolarınızı kolayca sorgulamak için SQL isteğe bağlı uç noktanıza Power BI bağlayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
- Spark SQL DW bağlayıcısını ayarlama hakkında daha fazla bilgi için: [SYNAPSE SQL Bağlayıcısı](./spark/../synapse-spark-sql-pool-import-export.md)
- Varsayılan kitaplıkları görüntüleme: [Azure SYNAPSE Analytics çalışma zamanı](../spark/apache-spark-version-support.md)