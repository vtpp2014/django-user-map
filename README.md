Django User Map
=================

Django User Map is a reusable django application for making community user's map. This app relies on the active auth user model and extend that model with OneToOne relationship. Users can add themselves on the map by providing some additional information:

1. Location on the map
2. Roles (the choices can be configured through setting)
3. Image, and
4. Website

Read here for the documentation: www.akbargumbira.com/django-user-map

## Installation
* Install django-user-map with pip:

```python
pip install django-user-map
```

* Make sure you have these modules in INSTALLED_APPS of your django project settings.py:

```python
INSTALLED_APPS = (
    'django.contrib.gis',
    'user_map',
    'leaflet',
    'bootstrapform',
    'rest_framework',
    'rest_framework_gis'
)
```

* Include user-map URLconf in your project urls.py with namespace user_map (required) e.g:

```python
url(r'^user-map/', include('user_map.urls', namespace='user_map')),
```

* Configure user map with USER_MAP variable in your project's settings.py or override some templates. See [Configurations](#configurations) section for further information.

* Run ```python manage.py migrate user_map``` to migrate the ```user_map``` 
models. 

* Visit the URL that you set before to see the apps.


## Configurations
You can configure Django User Map with one single USER_MAP variable in project's settings.py. The setting below is the default. Add this default to your project's setting and configure the necessary bits that you want to change:

```python   
USER_MAP = {
    'project_name': 'Django',
    'favicon_file': '',
    'login_view': 'django.contrib.auth.views.login',
    'marker': {
        # See leaflet icon valid options here:
        # http://leafletjs.com /reference.html#icon-iconurl
        'iconUrl': 'static/user_map/img/user-icon.png',
        'shadowUrl': 'static/user_map/img/shadow-icon.png',
        'iconSize': [19, 32],
        'shadowSize': [42, 35],
        'iconAnchor': [10, 0],
        'shadowAnchor': [12, 0],
    },
    'leaflet_config': {
        'TILES': [(
            # The title
            'MapQuest',
            # Tile's URL
            'http://otile{s}.mqcdn.com/tiles/1.0.0/osm/{z}/{x}/{y}.png',
            # More valid leaflet option are passed here
            # See here: http://leafletjs.com/reference.html#tilelayer
            {
                'attribution':
                    '© <a href="http://www.openstreetmap.org" '
                    'target="_parent">OpenStreetMap'
                    '</a> and contributors, under an <a '
                    'href="http://www.openstreetmap.org/copyright" '
                    'target="_parent">open license</a>. Tiles Courtesy of '
                    '<a '
                    'href="http://www.mapquest.com/">MapQuest</a> <img '
                    'src="http://developer.mapquest.com/content/osm/mq_logo'
                    '.png"',
                'subdomains': '1234'

            }
        )]
    },
    'roles': [
        {
            'id': 1,
            'name': 'Django User',
            'badge': 'user_map/img/badge-user.png'
        },
        {
            'id': 2,
            'name': 'Django Trainer',
            'badge': 'user_map/img/badge-trainer.png'
        },
        {
            'id': 3,
            'name': 'Django Developer',
            'badge': 'user_map/img/badge-developer.png'
        }
    ],
    'api_user_fields': [
        # e.g 'username', 'first_name', 'last_name'
    ],
}
```

### Project Name
This variable (```project_name```) represents the project name of the apps. If this is not specified, 'Django' will be used.

### Favicon
This variable (```favicon_file```) represents the file path to the favicon on the browser's tab. You can point it to any files in your static dirs from any apps.
   
### Login View
Django User Map relies on the auth user model in your project. For users to be able to add (or update) themselves, they need to log in first. You can point it to the view function e.g ```'login_view': 'django.contrib.auth.views.login'``` or even to the view name e.g ```'login_view': 'fe_login'```

### Marker
This variable represents the marker icon on the map. You can use any valid Leaflet ```L.icon``` options. Read here for the valid options http://leafletjs.com/reference.html#icon-iconurl 

An example for this variable:

```python
'marker': {
    'iconUrl': 'static/user_map/img/user-icon.png',
    'shadowUrl': 'static/user_map/img/shadow-icon.png',
    'iconSize': [19, 32],
    'shadowSize': [42, 35],
    'iconAnchor': [10, 0],
    'shadowAnchor': [12, 0],
}
```

### Map Configuration
You can configure the basemap of the form that uses LeafletWidget and the basemap of the homepage through  ```leaflet_config``` variable in settings.py. You can also pass leaflet tile layer options (read here: http://leafletjs.com/reference.html#tilelayer):

```python
'leaflet_config': {
    'TILES': [(
        # The title
        'MapQuest',
        # Tile's URL
        'http://otile{s}.mqcdn.com/tiles/1.0.0/osm/{z}/{x}/{y}.png',
        # More valid leaflet option are passed here
        {
            'attribution':
                '© <a href="http://www.openstreetmap.org" '
                'target="_parent">OpenStreetMap'
                '</a> and contributors, under an <a '
                'href="http://www.openstreetmap.org/copyright" '
                'target="_parent">open license</a>. Tiles Courtesy of '
                '<a '
                'href="http://www.mapquest.com/">MapQuest</a> <img '
                'src="http://developer.mapquest.com/content/osm/mq_logo'
                '.png"',
            'subdomains': '1234'

        }
    )]
},
```

### Roles

Using ```roles```, you can specify the user's role, its name, and its badge path. User can select multiple roles. On the map, visitors of the apps can also filter the marker (users) based on their role(s).If not specified, this variable will take this as the default:
   
```python
'roles': [
        {
            'id': 1,
            'name': 'Django User',
            'badge': 'user_map/img/badge-user.png'
        },
        {
            'id': 2,
            'name': 'Django Trainer',
            'badge': 'user_map/img/badge-trainer.png'
        },
        {
            'id': 3,
            'name': 'Django Developer',
            'badge': 'user_map/img/badge-developer.png'
        }
    ],
}
```
   
### User Fields
User fields are list of field from user model (AUTH_USER_MODEL) that will be shown on the popup content and will be added to the REST API. To specify which fields that should be visible from user model, you can configure it through ```api_user_fields``` as a list of fields. For example if you want to show username field from the user model, you must add this setting:`

```python
'api_user_fields': ['username'],
```

## Updating Roles
If in any case you need to update or change roles in ```settings.py``` after the ```user_map``` models are migrated, you must run django management command:
 
```python
python manage.py update_roles
```

Bear in mind that you should not change the id's and just change the name or the badge path. 

This command also will only update the role table, not the usermap table that uses the role table. So, adding roles is fine (as long as the new id's are unique). Removing roles has no effect (it will not remove the row in the table). Well, it could give you problems as it means that some of the users might not have valid roles defined in the role table.


## Changing Template
You can change the content of the data privacy (the modal popped up when user clicks 'Data Privacy' link at the bottom left of the map) or add navigation  menu. By  default, there is no navigation menu - the map will use the whole screen.
 
### Data Privacy
To modify data privacy content, create a directory ```user_map``` under ```templates``` dir of your Django project and create html file named ```data_privacy.html```. The default content of this template looks like this:

```html
<script id="data-privacy-content-section" type="text/template">
  The data you enter on this site may be visible to others. We suggest that
  you approximate your physical location to the nearest
  town or major center. Parts of your data will be made available for
  others to download and use. Click the REST API link to see the data that are
  available for public to use.
</script>
```

In the new ```data_privacy.html``` file, copy that and edit the wording yourself. Note that the ```<script>``` tag is needed completely with the same ```id``` and ```type```.


### Navigation
By default, the map showing all users does not have navigation menu. This is how it looks like without the navigation menu:

![User Map without Navigation](docs/img/without_nav.png)

But you can add a navigation menu so it looks like this as an example:

![User Map with Navigation](docs/img/with_nav.png)

To add navigation menu, add html file named ```navigation.html``` in ```user_map/templates/``` dir under your project's directory (along with your custom data privacy content) 

If you need more style or javascript resources to create this navigation menu, please add another template named ```extra_resources.html``` and save it in the same directory with the ```navigation.html```. These additional resources will be included in the head tag, so the content could be something like:
 
```html
<link rel="stylesheet" href="/static/style/new/basic.css" type="text/css" />
<link rel="stylesheet" href="/static/bootstrap/css/bootstrap.min.css" type="text/css" />
<link rel="stylesheet" href="/static/style/new/qgis-style.css" type="text/css" />
<link rel="stylesheet" href="/static/bootstrap/css/bootstrap-responsive.min.css" type="text/css" />
<link rel="stylesheet" href="/static/font-awesome/css/font-awesome.min.css">


<style>
  body {
    padding-top : 0px !important;
  }
  .navbar-fixed-top {
    position: relative;
  }
</style>
```
 
## Testing

You can run the test suite by using django manage.py from your django project:
```
python manage.py test user_map
```

or you can do it from the root of this django apps by running:
```
python setup.py test
```

## Status
[![Develop Branch](https://api.travis-ci.org/akbargumbira/django-user-map.svg?branch=develop)](https://travis-ci.org/akbargumbira/django-user-map)
[![Coverage Status](https://coveralls.io/repos/github/akbargumbira/django-user-map/badge.svg?branch=develop)](https://coveralls.io/github/akbargumbira/django-user-map?branch=develop)
