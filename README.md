Skytek ArcGis Integration
===


## Prerequisites

Install package:

```bash
pip install skytek-arcgis-integration
```

Add to installed apps in Django configuration:

```python
INSTALLED_APPS += [
    "rest_framework",
    "skytek_arcgis_integration",
]
```
Note: you need to add `rest_framework` if you don't already have it.

## Usage: ad-hoc without configuration

This is the fastest and easiest way of running the integration. Data source url is provided by the user in layer settings. It should be used in proof of concept projects only.

To use this integration simply include urls somewhere in your url config:

```python
urlpatterns = [
    ...
    path("ad-hoc-arcgis/", include("skytek_arcgis_integration.urls")),
]
```

## Usage: ad-hoc with layer configured

This method allows you to set up data source url on the backed.

To use this integration you need to use provided factory to generate view, configure a router and append urls to your configurations:

```python
from generic_map_api.routers import MapApiRouter
from skytek_arcgis_integration.views import arcgis_view_factory

fires_view = arcgis_view_factory("https://services3.arcgis.com/T4QMspbfLg3qTGWY/ArcGIS/rest/services/Current_WildlandFire_Perimeters/FeatureServer/0/")

router = MapApiRouter()
router.register("arc-gis-fires", fires_view, basename="arc-gis-fires")

# existing urlpatterns initialization goes here

urlpatterns += router.urls
```

## Usage: generated integration with storage

This method generates a separate, ready to use django application. It contains api client, celery tasks, storage models, view with serializer and django admin page.

Run generation command in your shell:

```bash
./manage.py generate_arcgis_integration
```

and provide configuration details. Only layer base url is required, you can use defaults for the rest.
```
$ ./manage.py generate_arcgis_integration
Enter base layer_url: https://services3.arcgis.com/T4QMspbfLg3qTGWY/ArcGIS/rest/services/Current_WildlandFire_Perimeters/FeatureServer/0
Enter full module path [arcgis.fh_perimeters]:
Enter model name [FhPerimeter]:
Enter celery app path [react_events.celery.app]:
```

Now add newly generated module to your `INSTALLED_APPS`, ie.:

```python
INSTALLED_APPS += [
    "rest_framework",
    "skytek_arcgis_integration",
    "arcgis.fh_perimeters",
]
```

Run migrations, ie.:

```bash
./manage makemigrations arcgis.fh_perimeters
./manage migrate
```

And include urls in your configuration, ie.:

```python
urlpatterns = [
    ...
    path("fh_perimeters/", include("arcgis.fh_perimeters.urls")),
]
```
