---
title: Créer une source de données pour les cartes Android | Microsoft Azure Maps
description: 'Découvrez comment créer une source de données pour une carte. En savoir plus sur les sources de données utilisées par le kit de développement logiciel (SDK) Android Azure Maps : Sources GeoJSON et mosaïques vectorielles.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e66d1aeaa87ac170cf692360e614335a98802e5a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430663"
---
# <a name="create-a-data-source-android-sdk"></a>Créer une source de données (SDK Android)

Le SDK Android Azure Maps stocke les données dans des sources de données. L’utilisation de sources de données optimise les opérations sur les données pour l’interrogation et le rendu. Il existe actuellement deux types de sources de données :

- **Source GeoJSON** : gère les données d’emplacement brutes au format GeoJSON localement. Convient aux petits et moyens jeux de données (jusqu’à des centaines de milliers de formes).
- **Source de mosaïque vectorielle** : charge des données formatées sous forme de mosaïques vectorielles pour la vue actuelle de la carte, en fonction du système de mosaïque des cartes. Convient aux jeux de données volumineux ou énormes (des millions ou milliards de formes).

## <a name="geojson-data-source"></a>Source de données GeoJSON

Azure Maps utilise GeoJSON comme l’un de ses principaux modèles de données. GeoJSON est une norme géospatiale ouverte utilisée pour représenter des données géospatiales au format JSON. Classes GeoJSON disponibles dans le SDK Android Azure Maps pour créer et sérialiser facilement des données GeoJSON. Chargez et stockez les données GeoJSON dans la classe `DataSource` et affichez-les à l’aide de couches. Le code suivant illustre la façon dont les objets GeoJSON peuvent être créés dans Azure Maps.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

> [!TIP]
> Les données GeoJSON peuvent être ajoutées à une instance `DataSource` à l’aide de l’une des trois méthodes suivantes : `add`, `importDataFromUrl` et `setShapes`. La méthode `setShapes` offre un moyen efficace de remplacer toutes les données d’une source de données. Si vous appelez les méthodes `clear` puis `add` pour remplacer toutes les données d’une source de données, deux appels de rendu sont effectués sur la carte. La méthode `setShape` efface et ajoute les données à la source de données avec un appel de rendu unique à la carte.

::: zone-end

Les propriétés peuvent également être d’abord chargées dans un objet JSON, puis transmises à la fonctionnalité lors de sa création, comme indiqué ci-dessous.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

Une fois que vous avez créé une fonctionnalité GeoJSON, vous pouvez ajouter une source de données à la carte via la propriété `sources` de la carte. Le code suivant montre comment créer une `DataSource`, l’ajouter à la carte et y ajouter une fonctionnalité.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

Le code suivant montre plusieurs façons de créer une classe Feature, FeatureCollection et des géométries GeoJSON.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>Sérialiser et désérialiser des objets GeoJSON

Les classes de collection de fonctionnalités, de fonctionnalités et de géométries ont toutes les méthodes statiques `fromJson()` et `toJson()`, qui aident à la sérialisation. La chaîne JSON valide mise en forme, transmise via la méthode `fromJson()`, crée l’objet de géométrie. Cette méthode `fromJson()` signifie également que vous pouvez utiliser Gson ou d’autres stratégies de sérialisation/désérialisation. Le code suivant montre comment prendre une fonctionnalité GeoJSON convertie et la désérialiser dans la classe Feature, puis la sérialiser de nouveau dans une chaîne GeoJSON.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importer des données GeoJSON à partir d’un dossier web ou de ressources

La plupart des fichiers GeoJSON contiennent une classe FeatureCollection. Lisez les fichiers GeoJSON en tant que chaînes et utilisez la méthode `FeatureCollection.fromJson` pour les désérialiser.

La classe `DataSource` a une méthode intégrée appelée `importDataFromUrl` qui peut être chargée dans des fichiers GeoJSON à l’aide d’une URL vers un fichier sur le web ou dans le dossier de ressources. Cette méthode **doit** être appelée avant que la source de données soit ajoutée à la carte.

zone_pivot_groups : azure-maps-android

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data");

//Examples:
//source.importDataFromUrl("asset://sample_file.json");
//source.importDataFromUrl("https://example.com/sample_file.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
var source = new DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data")

//Examples:
//source.importDataFromUrl("asset://sample_file.json")
//source.importDataFromUrl("https://example.com/sample_file.json")

//Add data source to the map.
map.sources.add(source)
```

::: zone-end

La méthode `importDataFromUrl` offre un moyen simple de charger un flux GeoJSON dans une source de données, mais offre un contrôle limité sur la façon dont les données sont chargées et sur ce qui se produit après leur chargement. Le code suivant est une classe réutilisable pour l’importation des données à partir d’un dossier web ou de ressources et leur renvoi au thread d’interface utilisateur par le biais d’une fonction de rappel. Dans ce rappel, vous pouvez ajouter une logique d’après chargement supplémentaire pour traiter les données, les ajouter à la carte, calculer leur cadre englobant et mettre à jour la caméra de la carte.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

Le code ci-dessous montre comment utiliser cet utilitaire pour importer des données GeoJSON sous forme de chaîne et les renvoyer au thread d’interface utilisateur par le biais d’un rappel. Dans le rappel, les données de chaîne peuvent être sérialisées dans une collection de fonctionnalités GeoJSON et ajoutées à la source de données. Vous pouvez également mettre à jour la caméra de la carte pour vous concentrer sur les données.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

### <a name="update-a-feature"></a>Mettre à jour une fonctionnalité

La classe `DataSource` facilite l’ajout et la suppression de fonctionnalités. La mise à jour de la géométrie ou des propriétés d’une fonctionnalité nécessite de remplacer la fonctionnalité dans la source de données. Il existe deux méthodes qui peuvent être utilisées pour mettre à jour une ou plusieurs fonctionnalités :

1. Créez la ou les nouvelles fonctionnalités avec les mises à jour souhaitées et remplacez toutes les fonctionnalités de la source de données à l’aide de la méthode `setShapes`. Cette méthode fonctionne bien lorsque vous souhaitez mettre à jour toutes les fonctionnalités d’une source de données.

::: zone pivot="programming-language-java-android"

``` java
DataSource source;

private void onReady(AzureMap map) {
    //Create a data source and add it to the map.
    source = new DataSource();
    map.sources.add(source);

    //Create a feature and add it to the data source.
    Feature myFeature = Feature.fromGeometry(Point.fromLngLat(0,0));
    myFeature.addStringProperty("Name", "Original value");

    source.add(myFeature);
}

private void updateFeature(){
    //Create a new replacement feature with an updated geometry and property value.
    Feature myNewFeature = Feature.fromGeometry(Point.fromLngLat(-10, 10));
    myNewFeature.addStringProperty("Name", "New value");

    //Replace all features to the data source with the new one.
    source.setShapes(myNewFeature);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
var source: DataSource? = null

private fun onReady(map: AzureMap) {
    //Create a data source and add it to the map.
    source = DataSource()
    map.sources.add(source)

    //Create a feature and add it to the data source.
    val myFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
    myFeature.addStringProperty("Name", "Original value")
    source!!.add(myFeature)
}

private fun updateFeature() {
    //Create a new replacement feature with an updated geometry and property value.
    val myNewFeature = Feature.fromGeometry(Point.fromLngLat(-10.0, 10.0))
    myNewFeature.addStringProperty("Name", "New value")

    //Replace all features to the data source with the new one.
    source!!.setShapes(myNewFeature)
}
```

::: zone-end

2. Effectuez le suivi de l’instance de fonctionnalité dans une variable et transmettez-la dans la méthode `remove` de sources de données pour la supprimer. Créez la ou les nouvelles fonctionnalités avec les mises à jour souhaitées, mettez à jour la référence de variable et ajoutez-la à la source de données à l’aide de la méthode `add`.

::: zone pivot="programming-language-java-android"

``` java
DataSource source;
Feature myFeature;

private void onReady(AzureMap map) {
    //Create a data source and add it to the map.
    source = new DataSource();
    map.sources.add(source);

    //Create a feature and add it to the data source.
    myFeature = Feature.fromGeometry(Point.fromLngLat(0,0));
    myFeature.addStringProperty("Name", "Original value");

    source.add(myFeature);
}

private void updateFeature(){
    //Remove the feature instance from the data source.
    source.remove(myFeature);

    //Get properties from original feature.
    JsonObject props = myFeature.properties();

    //Update a property.
    props.addProperty("Name", "New value");

    //Create a new replacement feature with an updated geometry.
    myFeature = Feature.fromGeometry(Point.fromLngLat(-10, 10), props);

    //Re-add the feature to the data source.
    source.add(myFeature);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
var source: DataSource? = null
var myFeature: Feature? = null

private fun onReady(map: AzureMap) {
    //Create a data source and add it to the map.
    source = DataSource()
    map.sources.add(source)

    //Create a feature and add it to the data source.
    myFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
    myFeature.addStringProperty("Name", "Original value")
    source!!.add(myFeature)
}

private fun updateFeature() {
    //Remove the feature instance from the data source.
    source!!.remove(myFeature)

    //Get properties from original feature.
    val props = myFeature!!.properties()

    //Update a property.
    props!!.addProperty("Name", "New value")

    //Create a new replacement feature with an updated geometry.
    myFeature = Feature.fromGeometry(Point.fromLngLat(-10.0, 10.0), props)

    //Re-add the feature to the data source.
    source!!.add(myFeature)
}
```

::: zone-end

> [!TIP]
> Si vous avez des données qui vont être régulièrement mises à jour et d’autres données qui seront rarement modifiées, il est préférable de les fractionner en instances de source de données distinctes. Quand une mise à jour se produit dans une source de données, elle force la carte à repeindre toutes les fonctionnalités dans la source de données. En fractionnant ces données, seules les fonctionnalités mises à jour régulièrement sont repeintes lorsqu’une mise à jour se produit dans cette source de données alors que les fonctionnalités de l’autre source de données n’auraient pas besoin d’être repeintes. Les performances s’en trouvent donc améliorées.

## <a name="vector-tile-source"></a>Source de mosaïque vectorielle

Une source de vignettes vectorielles décrit comment accéder à un calque de vignettes vectorielles. Utilisez la classe `VectorTileSource` pour instancier une source de vignettes vectorielles. Les calques de vignettes vectorielles sont similaires aux calques de vignettes, mais ils ne sont pas identiques. Un calque de vignettes est une image raster. Une couche de vignette vectorielle est un fichier compressé au format **PBF**. Ce fichier compressé contient les données d’une carte vectorielle et un ou plusieurs calques. Le fichier peut être rendu et stylisé sur le client, en fonction du style de chaque calque. Les données d’une mosaïque vectorielle contiennent des caractéristiques géographiques sous forme de points, de lignes et de polygones. Il y a plusieurs avantages à utiliser les calques de vignettes vectorielles au lieu des calques de vignettes raster :

- La taille de fichier d’une mosaïque vectorielle est généralement nettement inférieure à celle d’une mosaïque raster équivalente. Ainsi, la bande passante utilisée est inférieure. Cela signifie une latence plus faible, une carte plus rapide et une meilleure expérience utilisateur.
- Les vignettes vectorielles étant rendues sur le client, elles peuvent s’adapter à la résolution de l’appareil où elles sont affichées. Par conséquent, les cartes rendues apparaissent mieux définies, avec des étiquettes bien nettes.
- La modification du style des données dans les cartes vectorielles n’implique pas de retélécharger les données, car le nouveau style peut être appliqué au client. En revanche, la modification du style d’un calque de vignettes raster nécessite généralement le chargement de vignettes à partir du serveur, puis l’application du nouveau style.
- Les données étant fournies sous une forme vectorielle, moins de traitement côté serveur est nécessaire pour préparer les données. Ainsi, les données plus récentes peuvent être rendues disponibles plus rapidement.

Azure Maps est conforme à la [spécification Mapbox Vector Tile](https://github.com/mapbox/vector-tile-spec), qui est un standard ouvert. Azure Maps fournit les services de vignettes vectorielles suivants dans le cadre de la plateforme :

- Vignettes routières : [Documentation](/rest/api/maps/render-v2/get-map-tile) | [Détail du format de données](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Incidents de circulation : [Documentation](/rest/api/maps/traffic/gettrafficincidenttile) | [Détail du format de données](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Débit de circulation : [Documentation](/rest/api/maps/traffic/gettrafficflowtile) | [Détail du format de données](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps Creator permet également de créer des vignettes vectorielles personnalisées et d’y accéder par le biais du [Rendu V2-API Get Map Tile](/rest/api/maps/render-v2/get-map-tile)

> [!TIP]
> Si vous utilisez des vignettes d’images vectorielles ou raster issues du service de rendu Azure Maps avec le kit SDK web, vous pouvez remplacer `atlas.microsoft.com` par l’espace réservé `azmapsdomain.invalid`. Cet espace réservé sera remplacé par le domaine de la carte et ajoutera automatiquement les mêmes informations d’authentification. L’authentification auprès du service de rendu avec Azure Active Directory s’en trouve grandement simplifiée.

Pour afficher les données d’une source de vignette vectorielle sur la carte, connectez la source à l’une des couches de rendu de données. Toutes les couches qui utilisent une source vectorielle doivent spécifier une valeur `sourceLayer` dans les options. Le code suivant charge le service de mosaïque vectorielle Débit de circulation Azure Maps comme source de mosaïque vectorielle, puis l’affiche sur une carte à l’aide d’une couche de lignes. Cette source de vignette vectorielle comporte un seul jeu de données dans la couche source, appelé « Débit de circulation ». Les données de lignes de ce jeu de données possèdent une propriété nommée `traffic_level` qui est utilisée dans ce code pour sélectionner la couleur et mettre à l’échelle la taille des lignes.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![Carte avec des lignes de route avec un codage à l’aide de couleurs présentant différents niveaux de flux de trafic](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Connexion d’une source de données à un calque

Les données sont affichées sur la carte à l’aide de calques de rendu. Une source de données unique peut être référencée par un ou plusieurs calques de rendu. Les calques de rendu suivants nécessitent une source de données :

- [Calque de bulles](map-add-bubble-layer-android.md) : affiche les données de point sous forme de cercles à l’échelle sur la carte.
- [Calque de symboles](how-to-add-symbol-to-android-map.md) : affiche les données de point sous forme d’icônes ou de texte.
- [Calque de carte thermique](map-add-heat-map-layer-android.md) : affiche les données de point sous forme de carte thermique de densité.
- [Calque de lignes](android-map-add-line-layer.md) : pour une ligne ou le contour de polygones.
- [Calque de polygones](how-to-add-shapes-to-android-map.md) : remplit la zone d’un polygone avec un motif d’image ou une couleur unie.

Le code suivant montre comment créer une source de données, l’ajouter à la carte et la connecter à un calque de bulles. Ensuite, il importe dans la source de données des données de points GeoJSON à partir d’un emplacement distant.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data");

//Add data source to the map.
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data")

//Add data source to the map.
map.sources.add(source)
```

::: zone-end

Il existe des calques de rendu supplémentaires qui ne se connectent pas à ces sources de données, mais qui chargent directement les données pour les rendre.

- [Calque de mosaïque](how-to-add-tile-layer-android-map.md) : superpose un calque de mosaïques raster par-dessus la carte.

## <a name="one-data-source-with-multiple-layers"></a>Une source de données avec plusieurs calques

Plusieurs calques peuvent être connectés à une source de données unique. Cette option est utile dans de nombreux scénarios. Par exemple, considérez le scénario où un utilisateur dessine un polygone. Nous devons afficher et remplir la surface du polygone quand l’utilisateur ajoute des points à la carte. L’ajout d’une ligne stylisée pour le contour du polygone facilite la visualisation des bords du polygone à mesure que l’utilisateur le dessine. Pour modifier facilement une position individuelle dans le polygone, nous pouvons ajouter une poignée, comme une épingle ou un marqueur, au-dessus de chaque position.

![Carte montrant plusieurs calques qui affichent des données d’une source de données unique](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

Dans la plupart des plateformes de cartographie, vous avez besoin d’un objet polygone, d’un objet ligne et d’une épingle pour chaque position dans le polygone. Quand le polygone est modifié, vous devez alors mettre à jour manuellement la ligne et les épingles, ce qui peut devenir rapidement complexe.

Avec Azure Maps, tout ce dont vous avez besoin est un seul polygone dans une source de données, comme le montre le code ci-dessous.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> Lorsque vous ajoutez des couches à la carte à l’aide de la méthode `map.layers.add`, l’ID ou l’instance d’une couche existante peut être transmis en tant que second paramètre. Cela permet d’indiquer à la carte d’insérer la nouvelle couche au-dessous de la couche existante. En plus de transmettre un ID de couche, cette méthode prend en charge les valeurs suivantes.
>
> - `"labels"` : insère la nouvelle couche sous les couches d’étiquettes de la carte.
> - `"transit"` : insère la nouvelle couche sous les couches de routes et de transit de la carte.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Données de point de cluster](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une carte thermique](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Exemples de code de kit de développement logiciel web](/samples/browse/?products=azure-maps)
