---
title: 'Öğretici: Microsoft Azure eşlemeleriyle Azure Notebooks (Python) kullanarak sensör tahmin verileriyle sensörlü verileri birleştirin'
description: Azure Notebooks (Python) kullanarak Microsoft Azure Maps Hava durumu hizmetlerinden gelen hava durumu tahmin verileriyle algılayıcı verilerine nasıl katılabilmek için öğretici.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 8ab3458003366416e10588d3f2edb29b51619ecf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257645"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Öğretici: Azure Notebooks kullanarak (Python) Hava durumu tahmin verileriyle algılayıcı verileri ekleme

Rüzgar gücü, fossıl için bir alternatif enerji kaynağıdır ve bu da iklim değişikliğine karşı mücadele sağlar. Rüzgar doğası gereği uyumlu olmadığından, Rüzgar güç işleçlerini, Rüzgar güç kapasitesini tahmin etmek için makine öğrenimi (ML) modellerini derlemeniz gerekir. Bu tahmin, elektrik talebini karşılamak ve ızgara kararlılığını sağlamak için gereklidir. Bu öğreticide, Azure Maps Hava durumu tahmin verilerinin, hava durumu okumaları için tanıtım verileriyle nasıl birleştirildiğine yol göstereceğiz. Hava durumu tahmin verileri, Azure Maps Hava durumu Hizmetleri ' ni çağırarak istenir.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Bulutta [Azure Notebooks](https://notebooks.azure.com) veri dosyalarıyla çalışın.
> * Tanıtım verilerini dosyadan yükle.
> * Python 'da Azure haritalar REST API 'Lerini çağırın.
> * Haritada konum verileri oluşturma.
> * Azure haritalar ile tanıtım verilerinin [günlük tahmin](/rest/api/maps/weather/getdailyforecast) Hava durumu verilerini zenginleştirin.
> * Tahmin verilerini grafiklerde çiz.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için öncelikle şunları yapmanız gerekir:

1. [Hesap oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)konusundaki yönergeleri izleyerek S0 fiyatlandırma katmanında bir Azure Maps hesabı aboneliği oluşturun.
2. Hesabınız için birincil abonelik anahtarını alın, [birincil anahtar al](quick-demo-map-app.md#get-the-primary-key-for-your-account)bölümündeki yönergeleri izleyin.


Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md).

Azure Not defterleri hakkında bilgi edinmek ve nasıl başlaleyeceğinizi öğrenmek için, [Azure Not defteri oluşturma](./tutorial-ev-routing.md#create-an-azure-notebooks-project)yönergelerini izleyin.

> [!Note]
> Bu projenin Jupyter Not defteri dosyası, [Hava durumu haritaları Jupyter Notebook deposundan](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)indirilebilir.

## <a name="load-the-required-modules-and-frameworks"></a>Gerekli modülleri ve çerçeveleri yükleme

Gerekli tüm modülleri ve çerçeveleri yüklemek için aşağıdaki betiği çalıştırın:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Hava durumu verilerini içeri aktar

Bu öğreticinin bir listesi için, dört farklı rüzgar türgisinde yüklü sensörlerden gelen hava durumu verileri okumaları kullanacağız. Örnek veriler 30 günden fazla hava durumu okuduklarını içerir. Bu okumalar, her türbin konumunun yakınında Hava durumu veri merkezinden toplanır. Tanıtım verileri, sıcaklık, Rüzgar hızı ve yön için veri okuduklarını içerir. Tanıtım verilerini [buradan](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)indirebilirsiniz. Aşağıdaki komut dosyası tanıtım verilerini Azure Not defteri 'ne aktarır.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Günlük tahmin verileri iste

Senaryolarımızda her bir algılayıcı konumu için günlük tahmin istemek istiyoruz. Aşağıdaki betik, Azure Maps Hava durumu hizmetlerinin [günlük tahmın API](/rest/api/maps/weather/getdailyforecast) 'sini çağırır. Bu API, geçerli tarihten sonraki 15 gün boyunca her bir rüzgar Turbin için hava durumu tahminini döndürür.

```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Aşağıdaki komut dosyası, Azure haritalar [Get harita görüntüsü hizmetini](/rest/api/maps/render/getmapimage)çağırarak haritadaki türbin konumlarını işler.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Türbin konumları](./media/weather-service-tutorial/location-map.png)


Veri tahmini verileri, istasyon KIMLIĞI temel alınarak tanıtım verileriyle gruplandıracağız. İstasyon KIMLIĞI, hava durumu veri merkezine yöneliktir. Bu gruplandırma, tanıtım verilerini tahmin verileriyle genişlettiğini.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

Aşağıdaki tabloda, türbin konumlarından birine yönelik Birleşik geçmiş ve tahmin verileri görüntülenmektedir.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Gruplanmış veriler](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Tahmin verilerini çiz

Tahmin edilen değerleri tahmin ettikleri günlere göre çizeceğiz. Bu çizim, sonraki 15 gün boyunca rüzgar hızı ve yön değişikliklerini görmemize olanak sağlar.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Aşağıdaki grafiklerde tahmin verileri görselleştirilecek. Rüzgar hızı değişikliği için bkz. sol grafik. Rüzgar yönündeki değişiklik için sağ grafiğe bakın. Bu veriler, verilerin istendiği günden sonraki 15 günlük tahmindir.

<center>

![Rüzgar hızı çizimi ](./media/weather-service-tutorial/speed-date-plot.png) ![ Rüzgar yönü çizimi](./media/weather-service-tutorial/direction-date-plot.png)</center>

Bu öğreticide, hava durumu tahmin verileri almak için Azure haritalar REST API 'Lerini nasıl çağıracağınız anlatılmaktadır. Ayrıca grafiklerde verileri görselleştirmeyi de öğrendiniz.

Azure Notebooks içinde Azure haritalar REST API 'Lerini çağırma hakkında daha fazla bilgi için, bkz. [Azure Notebooks kullanarak ev yönlendirme](./tutorial-ev-routing.md).

Bu öğreticide kullanılan Azure Maps API 'Lerini araştırmak için, bkz.:

* [Günlük tahmin](/rest/api/maps/weather/getdailyforecast)
* [Render-harita görüntüsünü al](/rest/api/maps/render/getmapimage)

Azure haritalar REST API 'lerinin tüm listesi için bkz. [Azure Maps REST API 'leri](./consumption-model.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Azure Notebooks hakkında daha fazla bilgi edinmek için bkz.

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)