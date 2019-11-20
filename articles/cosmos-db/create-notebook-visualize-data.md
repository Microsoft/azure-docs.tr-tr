---
title: 'Öğretici: verileri analiz etmek ve görselleştirmek için Azure Cosmos DB bir not defteri oluşturun'
description: 'Öğretici: Azure Cosmos DB verileri içeri aktarmak, verileri çözümlemek ve çıktıyı görselleştirmek için yerleşik Jupyter not defterlerini nasıl kullanacağınızı öğrenin.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720916"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Öğretici: verileri analiz etmek ve görselleştirmek için Azure Cosmos DB bir not defteri oluşturun

Bu makalede, örnek perakende verileri Azure Cosmos DB aktarmak için yerleşik jupi Not defterlerinin nasıl kullanılacağı açıklanır. Sorguları çalıştırmak, verileri çözümlemek ve sonuçları görselleştirmek için SQL ve Azure Cosmos DB Magic komutlarının nasıl kullanılacağını göreceksiniz.

## <a name="prerequisites"></a>Ön koşullar

* [Azure Cosmos hesabını oluştururken Not defteri desteğini etkinleştir](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Kaynakları oluşturma ve verileri içeri aktarma
 
Bu bölümde, Azure Cosmos veritabanını, kapsayıcısını oluşturacak ve perakende verilerini kapsayıcıya aktaracaksınız.

1. Azure Cosmos hesabınıza gidin ve **Veri Gezgini açın.**

1. **Not** defterleri sekmesine gidin, **Not defterlerimin** yanındaki `…` seçin ve **Yeni bir not defteri**oluşturun. Varsayılan Çekirdek olarak **Python 3 ' ü** seçin.

   ![Yeni not defteri oluşturma](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Yeni bir not defteri oluşturulduktan sonra, bunu **Visualizere, Data. ipynb** gibi bir şekilde yeniden adlandırabilirsiniz.

1. Daha sonra perakende verilerini depolamak için "RetailDemo" adlı bir veritabanı ve "WebsiteData" adlı bir kapsayıcı oluşturacaksınız. /CardId ' i bölüm anahtarı olarak kullanabilirsiniz. Aşağıdaki kodu kopyalayıp Not defterinize yeni bir hücreye yapıştırın ve çalıştırın:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Bir hücreyi çalıştırmak için `Shift + Enter` seçin veya hücreyi seçin ve veri Gezgini gezinti çubuğunda **etkin hücreyi Çalıştır** seçeneğini belirleyin.

   ![Etkin hücreyi Çalıştır](./media/create-notebook-visualize-data/run-active-cell.png)

   Veritabanı ve kapsayıcı, geçerli Azure Cosmos hesabınızda oluşturulur. Kapsayıcı, 400 RU/s ile sağlanır. Veritabanı ve kapsayıcı oluşturulduktan sonra aşağıdaki çıktıyı görürsünüz. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Ayrıca, **veri** sekmesini yenileyebilir ve yeni oluşturulan kaynakları görebilirsiniz:

   ![Yeni kapsayıcıyı görmek için veri sekmesini yenileyin](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Ardından, örnek perakende verilerini Azure Cosmos kapsayıcısına içeri aktaracaksınız. Perakende verilerinden bir öğenin biçimi aşağıda verilmiştir:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Öğretici amacıyla, örnek perakende verileri Azure Blob depolama alanında depolanır. Aşağıdaki kodu yeni bir hücreye yapıştırarak Azure Cosmos kapsayıcısına aktarabilirsiniz. Öğelerin sayısını seçmek için bir sorgu çalıştırarak verilerin başarıyla içeri aktarıldığını doğrulayabilirsiniz.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Önceki sorguyu çalıştırdığınızda şu çıktıyı döndürür:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Verilerinizi bir veri çerçevesine alın

Verileri çözümlemek için sorguları çalıştırmadan önce, analiz için kapsayıcıdan bir [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) 'e ait verileri okuyabilirsiniz. Verileri bir DataFrame 'e okumak için aşağıdaki SQL Magic komutunu kullanın:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Daha fazla bilgi edinmek için [Azure Cosmos DB makalesinde yerleşik Not defteri komutları ve özellikleri](use-notebook-features-and-commands.md) bölümüne bakın. `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`sorgusunu çalıştıracaksınız. Sonuçlar df_cosmos adlı bir Pandas veri çerçevesine kaydedilir. Aşağıdaki komutu yeni bir not defteri hücresine yapıştırın ve çalıştırın:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Yeni bir not defteri hücresinde, çıktısından ilk 10 öğeyi okumak için aşağıdaki kodu çalıştırın:

```python
# See a sample of the result
df_cosmos.head(10)
```

![İlk 10 öğeyi almak için sorguyu Çalıştır](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Sorguları çalıştırın ve verilerinizi çözümleyin

Bu bölümde, alınan verilerde bazı sorgular çalıştıracaksınız.

* **Sorgu1:** Her ülkenin toplam satış gelirinin toplamını almak ve sonuçlardan 5 öğe göstermek için veri çerçevesinde sorgu yaparak bir grubu çalıştırın. Yeni bir not defteri hücresinde aşağıdaki kodu çalıştırın:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Toplam satış geliri çıkışı](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Query2:** Satın alınan beş öğenin bir listesini almak için yeni bir not defteri hücresi açın ve aşağıdaki kodu çalıştırın:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![İlk beş satın alınan öğe](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Verilerinizi görselleştirin  

1. Azure Cosmos kapsayıcısından gelir üzerinde verilerimizi öğrendiğimiz için verilerinizi seçtiğiniz bir görselleştirme kitaplığıyla görselleştirebilirsiniz. Bu öğreticide bokeh kitaplığı kullanacağız. Yeni bir not defteri hücresi açın ve bokeh kitaplığını yüklemek için aşağıdaki kodu çalıştırın. Tüm gereksinimler karşılandıktan sonra, kitaplık yüklenir.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Sonra bir haritadaki verileri çizmek için hazırlanın. Azure Cosmos DB verileri Azure Blob depolamada bulunan ülke bilgileriyle birleştirin ve sonucu GeoJSON biçimine dönüştürün. Aşağıdaki kodu yeni bir not defteri hücresine kopyalayın ve çalıştırın.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Yeni bir not defteri hücresinde aşağıdaki kodu çalıştırarak bir dünya eşlemesindeki farklı ülkelerin satış gelirini görselleştirin:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   Çıktı, dünya haritasını farklı renklerle görüntüler. Daha hafif olan renkler, en düşük gelirle en yüksek gelire sahip ülkeleri temsil eder.

   ![Ülkeler gelir Haritası görselleştirme](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Veri görselleştirmesinin başka bir durumunu görelim. WebsiteData kapsayıcısı, bir öğeyi görüntüleyen, sepetine eklenen ve öğeyi satın alan kullanıcıların kaydına sahiptir. Satın alınan öğelerin dönüştürme oranını çizelim. Her bir öğenin dönüştürme oranını görselleştirmek için aşağıdaki kodu yeni bir hücrede çalıştırın:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![Satın alma dönüştürme oranını görselleştirin](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Sonraki adımlar

* Not defteri komutları hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB makalesinde yerleşik Not defteri komutlarını ve özelliklerini kullanma](use-notebook-features-and-commands.md) .
