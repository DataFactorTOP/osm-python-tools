[back to readme](../../../)

# General remarks

Here, we make some general remarks that are of more generic character. They do not refer to a single module but rather to the entire library.

## OSM ID formats

OSM IDs can be provided an different places, such as when using the `overpassQueryBuilder` to create a query related to a certain area.  In these cases, the following input can be provided:
* A string formatted as `'node/*'`, `'way/*'`, or `'relation/*'`
* A string formatted as `'node *'`, `'way *'`, or `'relation *'`
* A string formatted as `'node*'`, `'way*'`, or `'relation*'`
* A string formatted as `'n *'`, `'w *'`, or `'r *'`
* A string formatted as `'n*'`, `'w*'`, or `'r*'`
* The result of an OSM Api query or an element returned by an Overpass query.
* The result of a Nominatim query.

Please observe that in case an area is expected as input, a node is not accepted.

## Common paramters for all modules

All modules that allow fetching data, such as [Api](api.md), [Nominatim](nominatim.md), and [Overpass](overpass.md), share some common parameters to adjust the way the data is fetched and cached.

### Endpoint

In order to adjust the endpoint being used, i.e., the server that requests are directed to, the default URL can be replaced by a user-defined one:
```python
api = API(endpoint='XYZ')
nominatim = Nominatim(endpoint='XYZ')
overpass = Overpass(endpoint='XYZ')
```
Such change of the endpoint makes sense in particular if you have your own server instance of the OSM API, Nominatim, or Overpass. The following URLs are used by default:

| Module | Default URL |
| ------ | ----------- |
| [Api](api.md) | `http://www.openstreetmap.org/api/0.6/` |
| [Nominatim](nominatim.md) | `https://nominatim.openstreetmap.org/` |
| [Overpass](overpass.md) | `http://overpass-api.de/api/` |

Please ensure that you comply with the usage policy of the server you use.

### Waiting time between queries

Complying with the usage policy of a server usually means to not overuse the service offered in order to allow for a fair sharing of the available resources. In case you want or have to reduce the number of requests send out, you can provde a minimum waiting time between to queries. To do so, you can provide the number of seconds to wait, such as:
```python
api = Api(waitBetweenQueries=5) # waits at least 5 seconds between requests
```
Please note that this paramter does not work for the [Overpass](overpass.md) module, because the Overpass server implements a load balancing strategy already. The [Overpass](overpass.md) module therefore receives such information from the Overpass server automatically and acts accordingly.

### User agent

In case you are using the `OSMPythonTools` more extensively, it is suggested that you adapt the user agent name. This makes possible for the servers used to track how much traffic you generate and, in case of an overuse, get into contact with you.

By default, the following user agent is used (with `X.Y.Z` being the version number):
```
OSMPythonTools/X.Y.Z (https://github.com/mocnik-science/osm-python-tools)
```
To extend this user agent information, you can provide a user-defined user agent as follows:
```python
api = Api(userAgent='Example App')
```
This results in an extend user agent name:
```
Example App // OSMPythonTools/X.Y.Z (https://github.com/mocnik-science/osm-python-tools)
```
Please note that the default part cannot and should not be removed from the user agent name by intention. It is important for the operators of the servers you are using, for instance, when `OSMPythonTools` contains a bug that leads to heavy overuse. In this case, the maintainers of `OSMPythonTools` can be contacted directly by the operators.

### Caching Strategies

The data is cached to ensure that the resources of the various services employed by this library, including the [Overpass endpoint](https://wiki.openstreetmap.org/wiki/Overpass_API), [Nominatim](http://nominatim.openstreetmap.org), and the [OSM API](https://wiki.openstreetmap.org/wiki/API), are not overused.  While the caching strategies supported are very similar in structure, they store the data in different formats.  By default, the data is stored in individual files in the JSON format.  As an alternative, the data can also be [pickled](https://docs.python.org/3/library/pickle.html) and stored in one file:
```python
from OSMPythonTools.cachingStrategy import CachingStrategy, JSON, Pickle
CachingStrategy.use(Pickle)
...
```
The caching strategy chosen applies to all subsequent requests.  The pickle file is gzipped by default.  This behaviour can, however, be changed:
```python
CachingStrategy.use(Pickle, gzip=False)
...
```
The default behaviourcan be restored as follows:
```python
CachingStrategy.use(JSON)
...
```
