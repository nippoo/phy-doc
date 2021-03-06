## Manual clustering

The manual clustering subpackage (`phy.cluster.manual`) is the main component of phy 0.1.0. It provides a programmatic Python API as well as a lightweight graphical interface to manually refine the automatic clustering.

There is a light command-line tool to start the GUI on a dataset. Type the following in a terminal: `phy cluster manual myexperiment.kwik`. Add `-i` to start an IPython console at the same time.

In the following, we describe the API in case you want more control on the process.

### Session

The `Session` is the main interface to the manual clustering engine. Use tab completion in IPython to discover the methods. See also the [API documentation](https://github.com/kwikteam/phy-doc/blob/master/api.md#phyclustermanualsession).

#### Activating Qt event loop integration in IPython

**Important**: if you use the GUI in the notebook, don't forget to do this at the beginning:

```python
>>> import phy
>>> phy.enable_qt()
2015-05-25 23:49:39  dock:142                Qt event loop activated.
```

You can also type `%gui qt`.

#### Starting a session

Let's create a new manual clustering session using an already automatically clustered dataset:

```python
>>> from phy.cluster.manual import Session
```

```python
>>> kwik_path = 'myexperiment.kwik'
>>> session = Session(kwik_path)
Features and masks initialized.[K
Waveforms initialized.[K
Statistics initialized.[K
```

> Putting your data files on a SSD is **highly recommended** for performance reasons.

This may take a while the first time, because a **cluster store** is automatically created. This folder in `myexperiment.phy/cluster_store/` contains a copy of all masks, features, and part of the waveforms for every cluster (one file per cluster). This makes the GUI orders of magnitude faster than fetching data from the HDF5 kwik files. The cluster store is transparently handled by the engine and you don't have to worry about it. The cluster store is also used to compute various cluster statistics at initialization time so that the interface is fast during manual clustering.

Oncce the session is loaded, you can view the data and start clustering it. See the API documentation for more details.

### Objects

There are several objects accessible from the session. See the API documentation for more details.

#### Model

This is documented earlier in the user guide, as well as in the API documentation:

```python
>>> session.model
<phy.io.kwik.model.KwikModel at 0x7ffc084b30b8>
```

#### Clustering

The `Clustering` object handles the spike-cluster mapping:

```python
>>> session.clustering
<phy.cluster.manual.clustering.Clustering at 0x7ffc084b3ef0>
```

The following two dictionaries can be useful:

```python
>>> session.clustering.cluster_counts
{2: 553,
 3: 188,
 ...
 24: 825,
 25: 418}
```

```python
>>> session.clustering.spikes_per_cluster
{2: array([   44,    68,   131,   133,   213,   224,   235,   364,   399,
   ....
           18480, 18484, 18485, 18521])}
```

These dictionaries are always up to date with the current manual clustering.

#### Other objects

In general you don't need to use these objects directly.

```python
>>> session.view_creator
<phy.cluster.manual.views.ViewCreator at 0x7ffc084b3208>
```

```python
>>> session.gui_creator
<phy.cluster.manual.gui.GUICreator at 0x7ffc084b3518>
```

```python
>>> session.wizard
<phy.cluster.manual.wizard.Wizard at 0x7ffbeb0b32e8>
```

### Wizard GUI

Type `session.show_gui()` to launch the clustering GUI. This minimal GUI provides two things:

* A window where to dock visualization widgets.
* Keyboard shortcuts to go through your best clusters and a list of potential merge candidates (**wizard**).

> Type `ctrl+h` to see the list of keyboard shortcuts.

The wizard is there to save you a lot of time clustering your data manually. It makes you propositions with 1 or 2 clusters, and you have to decide an outcome based on the views in the window.

Here is the intended workflow:

* Initially, the **best** cluster found is shown. You can explore it in the various views (waveforms, features, traces, ACG).
* Use `space` and `shift+space` to scroll through your best clusters by decreasing quality.
* At any point, you can **pin** a cluster by pressing `enter`. Then you'll see a second cluster selected: this is the **closest match**. Unpin with `backspace`.
* When a cluster is pinned, scroll through the list of closest matches with `space` and `shift+space`. The pinned cluster stays selected.
* You have keyboard shortcuts to:
    * Merge the two displayed clusters.
    * Move either or both clusters to cluster groups (noise, MUA, good).
* You can also draw a lasso in the feature view to create a new cluster out of the enclosed spikes (`ctrl+click` and `k` to split).

> Better quality and similarity measures will be used in the future. You'll also be able to write your own functions in Python.

![Wizard GUI screenshot](images/cluster-manual-gui.png)

All actions are accessible from the Python API. Use tab completion on `session.gui` and look at the [API](https://github.com/kwikteam/phy-doc/blob/master/api.md#phyclustermanualclustermanualgui).

```python
>>> gui = session.show_gui()
```

#### Adding views

You can add new views. Here is how to create a new CCG view for a given set of clusters:

```python
>>> view = session.show_view('correlograms', [2, 3, 5, 7], winsize_bins=101, binsize=10)
```

Here, `winsize_bins` is the size of the CCG window in number of bins, whereas `binsize` is the bin size in number of samples.

Now you can add this view to the GUI: the shown clusters will then be bound to the current cluster selection in the GUI.

```python
>>> gui.add_view(view)
```

### Changing the shank or clustering

```python
>>> session.change_channel_group(0)
Features and masks initialized.[K
Waveforms initialized.[K
Statistics initialized.[K
```

```python
>>> session.change_clustering('original')
Features and masks initialized.[K
Features and masks initialized.[K
Waveforms initialized.[K
Statistics initialized.[K
```

### The cluster store

The cluster stores contains a copy of the features, masks, waveforms of your data, in a format much more adapted to manual clustering. This makes the progam much faster than before. You can also use it to access per-cluster data and statistics.

#### Masks and features

```python
>>> store = session.cluster_store
```

```python
>>> store.display_status()
Cluster store status (/data/spikesorting/1_simple120sec/test_hybrid_120sec.phy/cluster_store/0/main)
----------------------------------------------------------------------------------------------------
Number of clusters in the store     24
Number of old clusters               0
Total size (MB)                     19
Consistent                        True
```

You can obtain the features and masks of all spikes in any given cluster very efficiently:

```python
>>> store.masks(3).shape
(188, 32)
```

```python
>>> store.features(5).shape
(260, 32, 3)
```

#### Waveforms

Only a subset of the waveforms (those that are displayed) are stored:

```python
>>> store.waveforms(3).shape
(100, 32, 32)
```

Here are the corresponding spikes:

```python
>>> store.items['waveforms'].spikes_per_cluster[3]
array([  152,   171,   173,   284,   695,   801,   829,   831,   927,
        ...
       17331, 17332, 17411, 17728, 17881, 17883, 18127, 18139, 18184, 18233])
```

#### Cluster statistics

There are also some basic cluster statistics, computed and stored in RAM when you load a dataset:

```python
>>> store.mean_masks(3)
array([  5.53777814e-03,   7.00678080e-02,   6.73059449e-02,
         ...
         3.20874620e-03,   3.55315686e-04], dtype=float32)
```

```python
>>> store.mean_probe_position(3)
array([ -1.71710563,  26.0843029 ], dtype=float32)
```

```python
>>> store.mean_features(3)
array([[  6.89437687e-01,   1.20974028e+00,   1.33888766e-01],
          ...
       [  1.01085186e+00,   8.73123109e-01,   2.94472277e-01]], dtype=float32)
```

A cluster store contains several **store items**: these objects are responsible for storing some data. Here are the default store items:

```python
>>> store.items
OrderedDict([('features and masks', <phy.io.kwik.store_items.FeatureMasks object at 0x7ffbed23ca58>), ('waveforms', <phy.io.kwik.store_items.Waveforms object at 0x7ffbed23c9b0>), ('statistics', <phy.io.kwik.store_items.ClusterStatistics object at 0x7ffbed23ccc0>)])
```

```python
>>> stats = store.items['statistics']
```

Every store items contains a list of **fields** which represents one particular bit of data, stored either on disk or in memory. For example, the statistics item contains the following default statistics:

```python
>>> stats.fields
['mean_masks',
 'mean_features',
 'mean_waveforms',
 'mean_probe_position',
 'main_channels',
 'n_unmasked_channels']
```

We'll see below how to customize these objects.

### Extending and customizing the interface

The whole API is designed fo modularity and extendability.

#### Using a custom quality function in the wizard

You can write your own quality function for the wizard:

```python
>>> @session.wizard.set_quality_function
... def stupid_quality(cluster):
...     return cluster
```

```python
>>> session.wizard.start()
```

```python
>>> session.wizard.best_clusters(n_max=10)
[25, 24, 23, 22, 21, 20, 19, 18, 17, 16]
```

Inside the function, you can use the cluster store to access the data and statistics.

If you have a GUI running, it will be automatically synchronized with this new statistic.

#### Using a custom similarity function in the wizard

```python
>>> @session.wizard.set_similarity_function
... def stupid_similarity(target, candidate):
...     return target - candidate
```

```python
>>> session.wizard.start()
```

```python
>>> session.wizard.most_similar_clusters(25, n_max=10)
[2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

The higher the score, the most similar the clusters are. Use negative numbers if you want to write a distance instead. The actual values don't count, only the cluster-per-cluster differences.

#### Registering a new statistic

You can create a new cluster statistic. It will be automatically computed for all clusters and kept up-to-date during the manual clustering session.

```python
>>> @session.register_statistic
... def n_spikes(cluster):
...     return len(store.spikes_per_cluster[cluster])
2015-05-25 23:50:00  session:283             Registered statistic `n_spikes`.
```

The new statistic now appears here:

```python
>>> stats.fields
['mean_masks',
 'mean_features',
 'mean_waveforms',
 'mean_probe_position',
 'main_channels',
 'n_unmasked_channels',
 'n_spikes']
```

And it is available from the store like the other statistics:

```python
>>> store.n_spikes(3)
188
```

#### Integrating your own plots in the GUI

Coming soon...

#### User parameters

You can put custom settings in `~/.phy/user_settings.py`. See the [default settings here](https://github.com/kwikteam/phy/blob/master/phy/cluster/manual/default_settings.py).

For example, to add a new CCG view with custom parameters by default in all GUIs, put this in your settings file:

```
gui_config = [
    ('wizard', {'position': 'right'}),
    ('features', {'position': 'left'}),
    ('correlograms', {'position': 'left'}),
    ('correlograms', {'position': 'left',
                      'binsize': 5,
                      'winsize_bins': 4*100}),
    ('waveforms', {'position': 'right'}),
    ('traces', {'position': 'right'}),
]
```

#### Internal settings

Some internal settings (especially related to the views and the GUI, such as the position of the views) are automatically saved in `~/.phy/internal_settings`. You can delete this file if you have some strange bugs in the GUI.

You can also set some settings manually:

```python
>>> session.settings['hello'] = 'world'
```

```python
>>> session.settings.save()
```
