# API documentation of phy

phy is an open source electrophysiological data analysis package in Python
for neuronal recordings made with high-density multielectrode arrays
containing up to thousands of channels.

## Table of contents

### [phy.cluster.manual](#phyclustermanual)

* [phy.cluster.manual.ClusterManualGUI](#phyclustermanualclustermanualgui)
* [phy.cluster.manual.Clustering](#phyclustermanualclustering)
* [phy.cluster.manual.GUICreator](#phyclustermanualguicreator)
* [phy.cluster.manual.Session](#phyclustermanualsession)
* [phy.cluster.manual.ViewCreator](#phyclustermanualviewcreator)
* [phy.cluster.manual.Wizard](#phyclustermanualwizard)


### [phy.electrode](#phyelectrode)

* [phy.electrode.MEA](#phyelectrodemea)


### [phy.io](#phyio)

* [phy.io.open_h5](#phyioopen_h5filename-modenone)
* [phy.io.ClusterStore](#phyioclusterstore)
* [phy.io.File](#phyiofile)
* [phy.io.KwikModel](#phyiokwikmodel)
* [phy.io.StoreItem](#phyiostoreitem)


### [phy.plot](#phyplot)

* [phy.plot.BaseSpikeCanvas](#phyplotbasespikecanvas)
* [phy.plot.BaseSpikeVisual](#phyplotbasespikevisual)
* [phy.plot.CorrelogramView](#phyplotcorrelogramview)
* [phy.plot.FeatureView](#phyplotfeatureview)
* [phy.plot.FeatureVisual](#phyplotfeaturevisual)
* [phy.plot.PanZoom](#phyplotpanzoom)
* [phy.plot.PanZoomGrid](#phyplotpanzoomgrid)
* [phy.plot.TraceView](#phyplottraceview)
* [phy.plot.WaveformView](#phyplotwaveformview)
* [phy.plot.WaveformVisual](#phyplotwaveformvisual)


### [phy.stats](#phystats)

* [phy.stats.pairwise_correlograms](#phystatspairwise_correlogramsspike_samples-spike_clusters-binsizenone-winsize_binsnone)


### [phy.utils](#phyutils)

* [phy.utils.debug](#phyutilsdebugmsg)
* [phy.utils.download_file](#phyutilsdownload_fileurl-outputnone-checksumnone)
* [phy.utils.download_test_data](#phyutilsdownload_test_dataname-output_dirnone)
* [phy.utils.enable_qt](#phyutilsenable_qt)
* [phy.utils.info](#phyutilsinfomsg)
* [phy.utils.qt_app](#phyutilsqt_appargs-kwds)
* [phy.utils.register](#phyutilsregisterlogger)
* [phy.utils.run_qt_app](#phyutilsrun_qt_app)
* [phy.utils.set_level](#phyutilsset_levellevel)
* [phy.utils.start_qt_app](#phyutilsstart_qt_app)
* [phy.utils.unregister](#phyutilsunregisterlogger)
* [phy.utils.warn](#phyutilswarnmsg)
* [phy.utils.Bunch](#phyutilsbunch)
* [phy.utils.DockWindow](#phyutilsdockwindow)
* [phy.utils.EventEmitter](#phyutilseventemitter)
* [phy.utils.ProgressReporter](#phyutilsprogressreporter)
* [phy.utils.Selector](#phyutilsselector)
* [phy.utils.Settings](#phyutilssettings)




## phy.cluster.manual

Manual clustering facilities.

### phy.cluster.manual.ClusterManualGUI

Manual clustering GUI.

This object represents a main window with:

* multiple views
* a wizard panel
* high-level clustering methods
* global keyboard shortcuts

#### Methods

##### `ClusterManualGUI.add_view(item, title=None, **kwargs)`

Add a new view model instance to the GUI.

##### `ClusterManualGUI.close()`

Close the GUI.

##### `ClusterManualGUI.connect(func=None, event=None, set_method=False)`

Register a callback function to a given event.

To register a callback function to the `spam` event, where `obj` is
an instance of a class deriving from `EventEmitter`:

```python
@obj.connect
def on_spam(arg1, arg2):
    pass
```

This is called when `obj.emit('spam', arg1, arg2)` is called.

Several callback functions can be registered for a given event.

The registration order is conserved and may matter in applications.

##### `ClusterManualGUI.emit(event, *args, **kwargs)`

Call all callback functions registered with an event.

Any positional and keyword arguments can be passed here, and they will
be fowarded to the callback functions.

##### `ClusterManualGUI.exit()`

Close the GUI.

##### `ClusterManualGUI.first()`

Go to the first cluster proposed by the wizard.

##### `ClusterManualGUI.get_views(name=None)`

Return the list of views of a given type.

##### `ClusterManualGUI.last()`

Go to the last cluster proposed by the wizard.

##### `ClusterManualGUI.merge()`

Merge all selected clusters together.

##### `ClusterManualGUI.next()`

Go to the next cluster proposed by the wizard.

##### `ClusterManualGUI.pin()`

Pin the current best cluster.

##### `ClusterManualGUI.previous()`

Go to the previous cluster proposed by the wizard.

##### `ClusterManualGUI.redo()`

Redo the last clustering action.

##### `ClusterManualGUI.reset_gui()`

Reset the GUI configuration.

##### `ClusterManualGUI.reset_wizard()`

Restart the wizard.

##### `ClusterManualGUI.save()`

Save the clustering changes to the `.kwik` file.

##### `ClusterManualGUI.select(cluster_ids)`

Select clusters.

##### `ClusterManualGUI.show()`

Show the GUI

##### `ClusterManualGUI.show_shortcuts()`

Show the list off all keyboard shortcuts.

##### `ClusterManualGUI.split()`

Create a new cluster out of the selected spikes.

##### `ClusterManualGUI.start()`

Start the wizard.

##### `ClusterManualGUI.unconnect(*funcs)`

Unconnect specified callback functions.

##### `ClusterManualGUI.undo()`

Undo the last clustering action.

##### `ClusterManualGUI.unpin()`

Unpin the current best cluster.

#### Properties

##### `ClusterManualGUI.dock_widgets`



##### `ClusterManualGUI.main_window`

Dock main window.

##### `ClusterManualGUI.selected_clusters`

The list of selected clusters.

##### `ClusterManualGUI.title`

Title of the main window.

### phy.cluster.manual.Clustering

Handle cluster changes in a set of spikes.

*Features*

* List of clusters appearing in a `spike_clusters` array
* Dictionary of spikes per cluster
* Merge
* Split and assign
* Undo/redo stack

*Notes*

The undo stack works by keeping the list of all spike cluster changes
made successively. Undoing consists of reapplying all changes from the
original `spike_clusters` array, except the last one.

*UpdateInfo*

Most methods of this class return an UpdateInfo instance. This object
contains information about the clustering changes done by the operation.
This object is used throughout the `phy.cluster.manual` package to let
different classes know about clustering changes.

`UpdateInfo` is a dictionary that also supports dot access (`Bunch` class).
The keys are the following:


* `description` (str)

    Description of the clustering operation. It is one of  `merge`,
    `assign`, or `metadata_group`.

* `history` (str or None)

    This is None except when it is `undo` or `redo`.

* `spikes` (array)

    Array of all spike ids affected by the update.

* `added` (list)

    New cluster ids created by this operation.

* `deleted` (list)

    Cluster ids that are deleted following this operation.

* `descendants` (list)

    List of `(old, new)` pairs of cluster ids to track provenance
    information about the clusters.

* `metadata_changed` (list)

    List of clusters with changed metadata (cluster group changes)

* `old_spikes_per_cluster` (dict)

    Dictionary of `{cluster: spikes}` for the old clusters and
    old clustering.

* `new_spikes_per_cluster` (dict)

    Dictionary of `{cluster: spikes}` for the new clusters and
    new clustering.

#### Methods

##### `Clustering.assign(spike_ids, spike_clusters_rel=0)`

Make new spike cluster assignements.

*Parameters*


* `spike_ids` (array-like)

    List of spike ids.

* `spike_clusters_rel` (array-like)

    Relative cluster ids of the spikes in `spike_ids`. This
    must have the same size as `spike_ids`.

*Returns*


* `up` (UpdateInfo instance)


*Note*

`spike_clusters_rel` contain *relative* cluster indices. Their values
don't matter: what matters is whether two give spikes
should end up in the same cluster or not. Adding a constant number
to all elements in `spike_clusters_rel` results in exactly the same
operation.

The final cluster ids are automatically generated by the `Clustering`
class. This is because we must ensure that all modified clusters
get brand new ids. The whole library is based on the assumption that
cluster ids are unique and "disposable". Changing a cluster always
results in a new cluster id being assigned.

If a spike is assigned to a new cluster, then all other spikes
belonging to the same cluster are assigned to a brand new cluster,
even if they were not changed explicitely by the `assign()` method.

In other words, the list of spikes affected by an `assign()` is almost
always a strict superset of the `spike_ids` parameter. The only case
where this is not true is when whole clusters change: this is called
a merge. It is implemented in a separate `merge()` method because it
is logically much simpler, and faster to execute.

##### `Clustering.merge(cluster_ids, to=None)`

Merge several clusters to a new cluster.

*Parameters*


* `cluster_ids` (array-like)

    List of clusters to merge.

* `to` (integer or None)

    The id of the new cluster. By default, this is `new_cluster_id()`.

*Returns*


* `up` (UpdateInfo instance)


##### `Clustering.new_cluster_id()`

Generate a brand new cluster id.

This is `maximum cluster id + 1`.

##### `Clustering.redo()`

Redo the last cluster assignement operation.

*Returns*


* `up` (UpdateInfo instance of the changes done by this operation.)


##### `Clustering.reset()`

Reset the clustering to the original clustering.

All changes are lost.

##### `Clustering.spikes_in_clusters(clusters)`

Return the array of spike ids belonging to a list of clusters.

##### `Clustering.split(spike_ids)`

Split a number of spikes into a new cluster.

This is equivalent to an `assign()` to a single new cluster.

*Parameters*


* `spike_ids` (array-like)

    Array of spike ids to merge.

*Returns*


* `up` (UpdateInfo instance)


*Note*

The note in the `assign()` method applies here as well. The list
of spikes affected by the split is almost always a strict superset
of the spike_ids parameter.

##### `Clustering.undo()`

Undo the last cluster assignement operation.

*Returns*


* `up` (UpdateInfo instance of the changes done by this operation.)


#### Properties

##### `Clustering.cluster_counts`

Dictionary with the number of spikes in each cluster.

##### `Clustering.cluster_ids`

Ordered list of ids of all non-empty clusters.

##### `Clustering.n_clusters`

Total number of clusters.

##### `Clustering.n_spikes`

Number of spikes.

##### `Clustering.spike_clusters`

A n_spikes-long vector containing the cluster ids of all spikes.

##### `Clustering.spike_ids`

Array of all spike ids.

##### `Clustering.spikes_per_cluster`

A dictionary `{cluster: spikes}`.

### phy.cluster.manual.GUICreator



#### Methods

##### `GUICreator.add(config=None, show=True)`

Add a new manual clustering GUI.

*Parameters*


* `config` (list)

    A list of tuples `(name, kwargs)` describing the views in the GUI.

* `show` (bool)

    Whether to show the newly-created GUI.

*Returns*


* `gui` (ClusterManualGUI)

    The GUI.

#### Properties

##### `GUICreator.gui`

The GUI if there is only one.

##### `GUICreator.guis`

List of GUIs.

### phy.cluster.manual.Session

A manual clustering session.

This is the main object used for manual clustering. It implements
all common actions:

* Loading a dataset (`.kwik` file)
* Listing the clusters
* Changing the current channel group or current clustering
* Showing views (waveforms, features, correlograms, etc.)
* Clustering actions: merge, split, undo, redo
* Wizard: cluster quality, best clusters, most similar clusters
* Save back to .kwik

#### Methods

##### `Session.change_channel_group(channel_group)`

Change the current channel group.

##### `Session.change_clustering(clustering)`

Change the current clustering.

##### `Session.close()`

Close the currently-open dataset.

##### `Session.connect(func=None, event=None, set_method=False)`

Register a callback function to a given event.

To register a callback function to the `spam` event, where `obj` is
an instance of a class deriving from `EventEmitter`:

```python
@obj.connect
def on_spam(arg1, arg2):
    pass
```

This is called when `obj.emit('spam', arg1, arg2)` is called.

Several callback functions can be registered for a given event.

The registration order is conserved and may matter in applications.

##### `Session.emit(event, *args, **kwargs)`

Call all callback functions registered with an event.

Any positional and keyword arguments can be passed here, and they will
be fowarded to the callback functions.

##### `Session.merge(clusters)`

Merge some clusters.

##### `Session.move(clusters, group)`

Move some clusters to a cluster group.

Here is the list of cluster groups:

* 0=Noise
* 1=MUA
* 2=Good
* 3=Unsorted

##### `Session.on_close()`

Save the settings when the data is closed.

##### `Session.on_open()`

Update the session after new data has been loaded.

##### `Session.open(kwik_path=None, model=None)`

Open a .kwik file.

##### `Session.redo()`

Redo the last undone action.

##### `Session.register_statistic(func=None, shape=(-1,))`

Decorator registering a custom cluster statistic.

*Parameters*


* `func` (function)

    A function that takes a cluster index as argument, and returns
    some statistics (generally a NumPy array).

*Notes*

This function will be called on every cluster when a dataset is opened.
It is also automatically called on new clusters when clusters change.
You can access the data from the model and from the cluster store.

##### `Session.save()`

Save the spike clusters and cluster groups to the Kwik file.

##### `Session.show_gui(config=None, **kwargs)`

Show a new manual clustering GUI.

##### `Session.show_view(name, cluster_ids, **kwargs)`

Create and display a new view.

*Parameters*


* `name` (str)

    Can be `waveforms`, `features`, `correlograms`, or `traces`.

* `cluster_ids` (array-like)

    List of clusters to show.

*Returns*


* `vm` (`ViewModel` instance)


##### `Session.split(spikes)`

Make a new cluster out of some spikes.

*Notes*

Spikes belonging to affected clusters, but not part of the `spikes`
array, will move to brand new cluster ids. This is because a new
cluster id must be used as soon as a cluster changes.

##### `Session.unconnect(*funcs)`

Unconnect specified callback functions.

##### `Session.undo()`

Undo the last clustering action.

#### Properties

##### `Session.cluster_ids`

Array of all cluster ids used in the current clustering.

##### `Session.has_unsaved_changes`

Whether there are unsaved changes in the model.

If true, a prompt message for saving will be displayed when closing
the GUI.

##### `Session.n_clusters`

Number of clusters in the current clustering.

### phy.cluster.manual.ViewCreator

Create views from a model.

#### Methods

##### `ViewCreator.add(vm_or_name, show=True, **kwargs)`

Add a new view.

##### `ViewCreator.get(name=None)`

Return the list of views of a given type.

##### `ViewCreator.save_view_params(save_size_pos=True)`

Save all view parameters to user settings.

#### Properties

### phy.cluster.manual.Wizard

Propose a selection of high-quality clusters and merge candidates.

#### Methods

##### `Wizard.best_clusters(n_max=None, quality=None)`

Return the list of best clusters sorted by decreasing quality.

The default quality function is the registered one.

##### `Wizard.first()`

First match or first best.

##### `Wizard.get_panel(extra_styles='')`



##### `Wizard.last()`

Last match or last best.

##### `Wizard.most_similar_clusters(cluster=None, n_max=None, similarity=None)`

Return the `n_max` most similar clusters to a given cluster.

The default similarity function is the registered one.

##### `Wizard.next()`

Next cluster proposition.

##### `Wizard.next_best()`

Select the next best cluster.

##### `Wizard.next_match()`

Select the next match.

##### `Wizard.on_cluster(up)`



##### `Wizard.pin(cluster=None)`

Pin the current best cluster and set the list of closest matches.

##### `Wizard.previous()`

Previous cluster proposition.

##### `Wizard.previous_best()`

Select the previous best in cluster.

##### `Wizard.previous_match()`

Select the previous match.

##### `Wizard.set_quality_function(func)`

Register a function returning the quality of a cluster.

Can be used as a decorator.

##### `Wizard.set_similarity_function(func)`

Register a function returning the similarity between two clusters.

Can be used as a decorator.

##### `Wizard.start()`

Start the wizard by setting the list of best clusters.

##### `Wizard.unpin()`

Unpin the current cluster.

#### Properties

##### `Wizard.best`

Currently-selected best cluster.

##### `Wizard.best_list`

Current list of best clusters, by decreasing quality.

##### `Wizard.cluster_groups`

Dictionary with the groups of each cluster.

The groups are: `None` (corresponds to unsorted), `good`, or `ignored`.

##### `Wizard.cluster_ids`

Array of cluster ids in the current clustering.

##### `Wizard.match`

Currently-selected closest match.

##### `Wizard.match_list`

Current list of closest matches, by decreasing similarity.

##### `Wizard.n_clusters`

Total number of clusters.

##### `Wizard.n_processed`

Numbered of processed clusters so far.

A cluster is considered processed if its group is not `None`.

##### `Wizard.selection`

Return the current best/match cluster selection.

## phy.electrode

Electrodes.

### phy.electrode.MEA

A Multi-Electrode Array.

#### Methods

#### Properties

##### `MEA.adjacency`

Adjacency graph.

##### `MEA.channels`

Channel ids.

##### `MEA.n_channels`

Number of channels.

##### `MEA.positions`

Channel positions.

## phy.io

Input/output.

##### `phy.io.open_h5(filename, mode=None)`

Open an HDF5 file and return a File instance.

### phy.io.ClusterStore

Hold per-cluster information on disk and in memory.

*Note*

Currently, this is used to accelerate access to per-cluster data
and statistics. All data is dynamically updated when clustering
changes occur.

#### Methods

##### `ClusterStore.clean()`

Erase all old files in the store.

##### `ClusterStore.clear()`

Erase all files in the store.

##### `ClusterStore.display_status()`

Display the current status of the cluster store.

##### `ClusterStore.generate(mode=None)`

Generate the cluster store.

*Parameters*


* `mode` (str (default is None))

    How the cluster store should be generated. Options are:

    * None or `default`: only regenerate the missing or inconsistent
      clusters
    * `force`: fully regenerate the cluster
    * `read-only`: just load the existing files, do not write anything

##### `ClusterStore.is_consistent()`

Return whether the cluster store is probably consistent.

Return true if all cluster stores files exist and have the expected
file size.

##### `ClusterStore.load(name, clusters=None, spikes=None)`

Load some data for a number of clusters and spikes.

##### `ClusterStore.register_field(name, item_name=None)`

Register a new piece of data to store on memory or on disk.

*Parameters*


* `name` (str)

    The name of the field.

* `item_name` (str)

    The name of the item.

##### `ClusterStore.register_item(item_cls, **kwargs)`

Register a `StoreItem` class in the store.

A `StoreItem` class is responsible for storing some data to disk
and memory. It must register one or several pieces of data.

##### `ClusterStore.update_spikes_per_cluster(spikes_per_cluster)`



#### Properties

##### `ClusterStore.cluster_ids`

All cluster ids appearing in the `spikes_per_cluster` dictionary.

##### `ClusterStore.disk_store`

Manage the cache of per-cluster voluminous data.

##### `ClusterStore.files`

List of files present in the disk store.

##### `ClusterStore.items`

Dictionary of registered store items.

##### `ClusterStore.memory_store`

Hold some cluster statistics.

##### `ClusterStore.model`

Model.

##### `ClusterStore.old_clusters`

Clusters in the disk store that are no longer in the clustering.

##### `ClusterStore.path`

Path to the disk store cache.

##### `ClusterStore.spikes_per_cluster`

Dictionary `{cluster_id: spike_ids}`.

##### `ClusterStore.status`

Return the current status of the cluster store.

##### `ClusterStore.total_size`

Total size of the disk store.

### phy.io.File



#### Methods

##### `File.attrs(path='/')`

Return the list of attributes at the given path.

##### `File.children(path='/')`

Return the list of children of a given node.

##### `File.close()`



##### `File.copy(path, new_path)`

Copy a group or dataset to another location.

##### `File.datasets(path='/')`

Return the list of datasets under a given node.

##### `File.delete(path)`

Delete a group or dataset.

##### `File.describe()`

Display the list of all groups and datasets in the file.

##### `File.exists(path)`



##### `File.groups(path='/')`

Return the list of groups under a given node.

##### `File.has_attr(path, attr_name)`

Return whether an attribute exists at a given path.

##### `File.is_open()`



##### `File.move(path, new_path)`

Move a group or dataset to another location.

##### `File.open(mode=None)`



##### `File.read(path)`

Read an HDF5 dataset, given its HDF5 path in the file.

##### `File.read_attr(path, attr_name)`

Read an attribute of an HDF5 group.

##### `File.write(path, array, overwrite=False)`

Write a NumPy array in the file.

*Parameters*

* `path` (str)

    Full HDF5 path to the dataset to create.

* `array` (ndarray)

    Array to write in the file.

* `overwrite` (bool)

    If False, raise an error if the dataset already exists. Defaults
    to False.

##### `File.write_attr(path, attr_name, value)`

Write an attribute of an HDF5 group.

#### Properties

##### `File.h5py_file`

Native h5py file handle.

### phy.io.KwikModel

Holds data contained in a kwik file.

#### Methods

##### `KwikModel.add_cluster_group(group_id, name)`

Add a new cluster group.

##### `KwikModel.add_clustering(name, spike_clusters)`

Save a new clustering to the file.

##### `KwikModel.close()`

Close the `.kwik`, `.kwx`, and `.raw.kwd` files if they are open.

##### `KwikModel.copy_clustering(name, new_name)`

Copy a clustering in the `.kwik` file.

##### `KwikModel.delete_cluster_group(group_id)`



##### `KwikModel.delete_clustering(name)`

Delete a clustering.

##### `KwikModel.describe()`

Display information about the dataset.

##### `KwikModel.has_kwd()`

Returns whether the `.raw.kwd` file is present.

If not, the waveforms won't be available.

##### `KwikModel.has_kwx()`

Returns whether the `.kwx` file is present.

If not, the features and masks won't be available.

##### `KwikModel.open(kwik_path, channel_group=None, clustering=None)`

Open a Kwik dataset.

The `.kwik`, `.kwx`, and `.raw.kwd` must be in the same folder with the
same basename.

*Notes*

The `.kwik` file is opened in read-only mode, and is automatically
closed when this function returns. It is temporarily reopened when
the channel group or clustering changes.

The `.kwik` file is temporarily opened in append mode when saving.

The `.kwx` and `.raw.kwd` files stay open in read-only mode as long
as `model.close()` is not called. This is because there might be
read accesses to `features_masks` (`.kwx`) and waveforms (`.raw.kwd`)
while the dataset is opened.

*Parameters*


* `kwik_path` (str)

    Path to a `.kwik` file.

* `channel_group` (int or None (default is None))

    The channel group (shank) index to use. This can be changed
    later after the file has been opened. By default, the first
    channel group is used.

* `clustering` (str or None (default is None))

    The clustering to use. This can be changed later after the file
    has been opened. By default, the `main` clustering is used. An
    error is raised if the `main` clustering doesn't exist.

##### `KwikModel.rename_cluster_group(group_id, name)`

Rename an existing cluster group.

##### `KwikModel.rename_clustering(old_name, new_name)`

Rename a clustering in the `.kwik` file.

##### `KwikModel.save(spike_clusters, cluster_groups)`

Save the spike clusters and cluster groups in the Kwik file.

#### Properties

##### `KwikModel.channel_group`



##### `KwikModel.channel_groups`

List of channel groups found in the Kwik file.

##### `KwikModel.channel_order`

List of kept channels in the current channel group.

If you want the channels used in the features, masks, and waveforms,
this is the property you want to use, and not `model.channels`.

The channel order is the same than the one from the PRB file.
This order was used when generating the features and masks
in SpikeDetekt2. The same order is used in phy when loading the
waveforms from the `.raw.kwd` file.

##### `KwikModel.channels`

List of all channels in the current channel group.

This list comes from the /channel_groups HDF5 group in the Kwik file.

##### `KwikModel.cluster_groups`

Groups of all clusters in the current channel group and clustering.

This is a regular Python dictionary.

##### `KwikModel.cluster_ids`

List of cluster ids from the current channel group and clustering.

This is a sorted list of unique cluster ids as found in the current
`spike_clusters` array.

##### `KwikModel.cluster_metadata`

Metadata about the clusters in the current channel group and
clustering.

`cluster_metadata.group(cluster_id)` returns the group of a given
cluster. The default group is 3 (unsorted).

##### `KwikModel.clustering`

The currently-active clustering.

Default is `main`.

##### `KwikModel.clusterings`

List of clusterings found in the Kwik file.

The first one is always `main`.

##### `KwikModel.duration`

Duration of the experiment (in seconds).

##### `KwikModel.features`

Features from the current channel group.

This is memory-mapped to the `.kwx` file.

Note: in general, it is better to use the cluster store to access
the features and masks of some clusters.

##### `KwikModel.features_masks`

Features-masks from the current channel group.

This is memory-mapped to the `.kwx` file.

Note: in general, it is better to use the cluster store to access
the features and masks of some clusters.

##### `KwikModel.masks`

Masks from the current channel group.

This is memory-mapped to the `.kwx` file.

Note: in general, it is better to use the cluster store to access
the features and masks of some clusters.

##### `KwikModel.metadata`

A dictionary holding metadata about the experiment.

This information comes from the PRM file. It was used by
SpikeDetekt2 and KlustaKwik during automatic clustering.

##### `KwikModel.n_channels`

Number of all channels in the current channel group.

##### `KwikModel.n_clusters`

Number of clusters in the current channel group and clustering.

##### `KwikModel.n_features_per_channel`

Number of features per channel (generally 3).

##### `KwikModel.n_recordings`

Number of recordings found in the Kwik file.

##### `KwikModel.n_samples_waveforms`



##### `KwikModel.n_spikes`

Number of spikes in the current channel group.

##### `KwikModel.probe`

A `Probe` instance representing the probe used for the recording.

This object contains information about the adjacency graph and
the channel positions.

##### `KwikModel.recordings`

List of recording indices found in the Kwik file.

##### `KwikModel.sample_rate`

Sample rate of the recording.

This value is found in the metadata coming from the PRM file.

##### `KwikModel.spike_clusters`

Spike clusters from the current channel group and clustering.

Every element is the cluster identifier of a spike.

The shape is `(n_spikes,)`.

##### `KwikModel.spike_ids`

List of spike ids.

##### `KwikModel.spike_recordings`

The recording index for each spike.

This is a NumPy array of integers with `n_spikes` elements.

##### `KwikModel.spike_samples`

Spike samples from the current channel group.

This is a NumPy array containing `uint64` values (number of samples
in unit of the sample rate).

The spike times of all recordings are concatenated. There is no gap
between consecutive recordings, currently.

##### `KwikModel.spike_times`

Spike times from the current channel_group.

This is a NumPy array containing `float64` values (in seconds).

The spike times of all recordings are concatenated. There is no gap
between consecutive recordings, currently.

##### `KwikModel.traces`

Raw traces as found in the `.raw.kwd` file.

This object is memory-mapped to the HDF5 file.

##### `KwikModel.waveforms`

High-passed filtered waveforms from the current channel group.

This is a virtual array mapped to the `.raw.kwd` file. Filtering is
done on the fly.

The shape is `(n_spikes, n_samples, n_channels)`.

### phy.io.StoreItem

A class describing information stored in the cluster store.

*Parameters*


* `name` (str)

    Name of the item.

* `fields` (list)

    A list of field names.

* `model` (Model)

    A `Model` instance for the current dataset.

* `memory_store` (MemoryStore)

    The `MemoryStore` instance for the current dataset.

* `disk_store` (DiskStore)

    The DiskStore instance for the current dataset.

#### Methods

##### `StoreItem.empty_values(name)`

Return a null array of the right shape for a given field.

##### `StoreItem.is_consistent(cluster, spikes)`

Return whether the stored item is consistent.

To be overriden.

##### `StoreItem.load(cluster, name)`

Load data for one cluster.

##### `StoreItem.load_multi(clusters, name)`

Load data for several clusters.

##### `StoreItem.load_spikes(spikes, name)`

Load data from an array of spikes.

##### `StoreItem.on_assign(up)`

Called when a new split occurs.

May be overriden.

##### `StoreItem.on_cluster(up=None)`

Called when the clusters change.

Old data is kept on disk and in memory, which is useful for
undo and redo. The `cluster_store.clean()` method can be called to
delete the old files.

Nothing happens during undo and redo (the data is already there).

##### `StoreItem.on_merge(up)`

Called when a new merge occurs.

May be overriden if there's an efficient way to update the data
after a merge.

##### `StoreItem.spikes_in_clusters(clusters)`

Return the spikes belonging to clusters.

##### `StoreItem.store(cluster)`

Store data for a cluster from the model to the store.

May be overridden.

##### `StoreItem.store_all(mode=None, **kwargs)`

Copy all data for that item from the model to the cluster store.

##### `StoreItem.to_generate(mode=None)`

Return the list of clusters that need to be regenerated.

#### Properties

##### `StoreItem.cluster_ids`

Array of cluster ids.

##### `StoreItem.progress_reporter`

Progress reporter instance.

##### `StoreItem.spikes_per_cluster`

Spikes per cluster.

## phy.plot

Interactive and static visualization of data.

### phy.plot.BaseSpikeCanvas

Base class for a VisPy canvas with spike data.

Display a main `BaseSpikeVisual` with pan zoom.

#### Methods

##### `BaseSpikeCanvas.emit(name, **kwargs)`



##### `BaseSpikeCanvas.on_draw(event)`

Draw the main visual.

##### `BaseSpikeCanvas.on_resize(event)`

Resize the OpenGL context.

#### Properties

### phy.plot.BaseSpikeVisual

Base class for a VisPy visual showing spike data.

There is a notion of displayed spikes and displayed clusters.

#### Methods

##### `BaseSpikeVisual.draw()`

Draw the waveforms.

##### `BaseSpikeVisual.set_to_bake(*bakes)`

Mark data items to be prepared for GPU.

#### Properties

##### `BaseSpikeVisual.cluster_colors`

Colors of the displayed clusters.

The first color is the color of the smallest cluster.

##### `BaseSpikeVisual.cluster_ids`

Cluster ids of the displayed spikes.

##### `BaseSpikeVisual.cluster_order`

List of selected clusters in display order.

##### `BaseSpikeVisual.empty`

Specify whether the visual is currently empty or not.

##### `BaseSpikeVisual.masks`

Masks of the displayed spikes.

##### `BaseSpikeVisual.n_clusters`

Number of displayed clusters.

##### `BaseSpikeVisual.spike_clusters`

The clusters assigned to the displayed spikes.

##### `BaseSpikeVisual.spike_ids`

Spike ids to display.

### phy.plot.CorrelogramView

A VisPy canvas displaying correlograms.

#### Methods

##### `CorrelogramView.emit(name, **kwargs)`



##### `CorrelogramView.on_draw(event)`

Draw the correlograms visual.

##### `CorrelogramView.on_resize(event)`

Resize the OpenGL context.

#### Properties

##### `CorrelogramView.cluster_ids`

Displayed cluster ids.

### phy.plot.FeatureView

A VisPy canvas displaying features.

#### Methods

##### `FeatureView.emit(name, **kwargs)`



##### `FeatureView.on_draw(event)`

Draw the features in a grid view.

##### `FeatureView.on_key_press(event)`

Handle key press events.

##### `FeatureView.on_mouse_press(e)`



##### `FeatureView.on_resize(event)`

Resize the OpenGL context.

##### `FeatureView.update_dimensions(dimensions)`



#### Properties

##### `FeatureView.diagonal_dimensions`

Dimensions.

##### `FeatureView.dimensions`

Dimensions.

##### `FeatureView.marker_size`

Marker size.

### phy.plot.FeatureVisual

Display a grid of multidimensional features.

#### Methods

##### `FeatureVisual.draw()`

Draw the waveforms.

##### `FeatureVisual.project(data, box)`

Project data to a subplot's two-dimensional subspace.

*Parameters*

* `data` (array)

    The shape is `(n_points, n_channels, n_features)`.

* `box` (2-tuple)

    The `(row, col)` of the box.

*Notes*

The coordinate system is always the world coordinate system, i.e.
`[-1, 1]`.

##### `FeatureVisual.set_to_bake(*bakes)`

Mark data items to be prepared for GPU.

#### Properties

##### `FeatureVisual.cluster_colors`

Colors of the displayed clusters.

The first color is the color of the smallest cluster.

##### `FeatureVisual.cluster_ids`

Cluster ids of the displayed spikes.

##### `FeatureVisual.cluster_order`

List of selected clusters in display order.

##### `FeatureVisual.diagonal_dimensions`

Displayed dimensions on the diagonal y axis.

This is a list of items which can be:

* tuple `(channel_id, feature_idx)`
* `'time'`

##### `FeatureVisual.dimensions`

Displayed dimensions.

This is a list of items which can be:

* tuple `(channel_id, feature_idx)`
* `'time'`

##### `FeatureVisual.empty`

Specify whether the visual is currently empty or not.

##### `FeatureVisual.features`

Displayed features.

This is a `(n_spikes, n_features)` array.

##### `FeatureVisual.marker_size`

Marker size in pixels.

##### `FeatureVisual.masks`

Masks of the displayed spikes.

##### `FeatureVisual.n_boxes`

Number of boxes in the grid.

##### `FeatureVisual.n_clusters`

Number of displayed clusters.

##### `FeatureVisual.spike_clusters`

The clusters assigned to the displayed spikes.

##### `FeatureVisual.spike_ids`

Spike ids to display.

##### `FeatureVisual.spike_samples`

Time samples of the displayed spikes.

### phy.plot.PanZoom

Pan & zoom transform.

The panzoom transform allow to translate and scale an object in the window
space coordinate (2D). This means that whatever point you grab on the
screen, it should remains under the mouse pointer. Zoom is realized using
the mouse scroll and is always centered on the mouse pointer.

You can also control programmatically the transform using:

* aspect: control the aspect ratio of the whole scene
* pan   : translate the scene to the given 2D coordinates
* zoom  : set the zoom level (centered at current pan coordinates)
* zmin  : minimum zoom level
* zmax  : maximum zoom level

#### Methods

##### `PanZoom.add(programs)`

Add programs to this tranform.

##### `PanZoom.attach(canvas)`

Attach this tranform to a canvas.

##### `PanZoom.on_key_press(event)`

Key press event.

##### `PanZoom.on_mouse_move(event)`

Pan and zoom with the mouse.

##### `PanZoom.on_mouse_wheel(event)`

Zoom with the mouse wheel.

##### `PanZoom.on_resize(event)`

Resize event.

#### Properties

##### `PanZoom.aspect`

Aspect (width/height).

##### `PanZoom.is_attached`

Whether the transform is attached to a canvas.

##### `PanZoom.pan`

Pan translation.

##### `PanZoom.xmax`

Maximum x allowed for pan.

##### `PanZoom.xmin`

Minimum x allowed for pan.

##### `PanZoom.ymax`

Maximum y allowed for pan.

##### `PanZoom.ymin`

Minimum y allowed for pan.

##### `PanZoom.zmax`

Maximal zoom level.

##### `PanZoom.zmin`

Minimum zoom level.

##### `PanZoom.zoom`

Zoom level.

##### `PanZoom.zoom_to_pointer`

Whether to zoom toward the pointer position.

### phy.plot.PanZoomGrid

Pan & zoom transform for a grid view.

This is used in a grid view with independent per-subplot pan & zoom.

The currently-active subplot depends on where the cursor was when
the mouse was clicked.

#### Methods

##### `PanZoomGrid.add(programs)`

Add programs to this tranform.

##### `PanZoomGrid.attach(canvas)`

Attach this tranform to a canvas.

##### `PanZoomGrid.on_key_press(event)`

Key press event.

##### `PanZoomGrid.on_mouse_double_click(event)`

Double click event.

##### `PanZoomGrid.on_mouse_move(event)`

Mouse move event.

##### `PanZoomGrid.on_mouse_press(event)`

Mouse press event.

##### `PanZoomGrid.on_mouse_wheel(event)`

Mouse wheel event.

##### `PanZoomGrid.on_resize(event)`

Resize event.

#### Properties

##### `PanZoomGrid.aspect`

Aspect (width/height).

##### `PanZoomGrid.global_pan_zoom_axis`

Global pan zoom matrix.

This matrix indicates how each subplot reacts to global pan zoom
events.

Element `(i, j)` in this matrix is:

* `x` if only this axis is to be changed
* `y` if only this axis is to be changed
* `b` if both axes are to be changed
* `n` if no axis is to be changed

##### `PanZoomGrid.is_attached`

Whether the transform is attached to a canvas.

##### `PanZoomGrid.n_rows`

Number of rows.

##### `PanZoomGrid.pan`

Pan translation.

##### `PanZoomGrid.pan_matrix`

Pan in every subplot.

##### `PanZoomGrid.xmax`



##### `PanZoomGrid.xmin`



##### `PanZoomGrid.ymax`



##### `PanZoomGrid.ymin`



##### `PanZoomGrid.zmax`

Maximal zoom level.

##### `PanZoomGrid.zmin`

Minimum zoom level.

##### `PanZoomGrid.zoom`

Zoom level.

##### `PanZoomGrid.zoom_matrix`

Zoom in every subplot.

##### `PanZoomGrid.zoom_to_pointer`

Whether to zoom toward the pointer position.

### phy.plot.TraceView

A VisPy canvas displaying traces.

#### Methods

##### `TraceView.emit(name, **kwargs)`



##### `TraceView.on_draw(event)`

Draw the main visual.

##### `TraceView.on_key_press(event)`

Handle key press events.

##### `TraceView.on_resize(event)`

Resize the OpenGL context.

#### Properties

##### `TraceView.channel_scale`

Vertical scale of the traces.

### phy.plot.WaveformView

A VisPy canvas displaying waveforms.

#### Methods

##### `WaveformView.emit(name, **kwargs)`



##### `WaveformView.on_draw(event)`

Draw the visual.

##### `WaveformView.on_key_press(event)`

Handle key press events.

##### `WaveformView.on_key_release(event)`



##### `WaveformView.on_mouse_press(e)`



##### `WaveformView.on_mouse_wheel(event)`

Handle mouse wheel events.

##### `WaveformView.on_resize(event)`

Resize the OpenGL context.

#### Properties

##### `WaveformView.box_scale`

Scale of the waveforms.

This is a pair of scalars.

##### `WaveformView.overlap`

Whether to overlap waveforms.

##### `WaveformView.probe_scale`

Scale of the probe.

This is a pair of scalars.

##### `WaveformView.show_mean`

Whether to show_mean waveforms.

### phy.plot.WaveformVisual

Display waveforms with probe geometry.

#### Methods

##### `WaveformVisual.channel_hover(position)`

Return the channel id closest to the mouse pointer.

*Parameters*


* `position` (tuple)

    The normalized coordinates of the mouse pointer, in world
    coordinates (in `[-1, 1]`).

##### `WaveformVisual.draw()`

Draw the waveforms.

##### `WaveformVisual.set_to_bake(*bakes)`

Mark data items to be prepared for GPU.

#### Properties

##### `WaveformVisual.alpha`

Alpha transparency (between 0 and 1).

##### `WaveformVisual.box_scale`

Scale of the waveforms.

This is a pair of scalars.

##### `WaveformVisual.channel_order`



##### `WaveformVisual.channel_positions`

Positions of the channels.

This is a `(n_channels, 2)` array.

##### `WaveformVisual.cluster_colors`

Colors of the displayed clusters.

The first color is the color of the smallest cluster.

##### `WaveformVisual.cluster_ids`

Cluster ids of the displayed spikes.

##### `WaveformVisual.cluster_order`

List of selected clusters in display order.

##### `WaveformVisual.empty`

Specify whether the visual is currently empty or not.

##### `WaveformVisual.masks`

Masks of the displayed spikes.

##### `WaveformVisual.n_clusters`

Number of displayed clusters.

##### `WaveformVisual.overlap`

Whether to overlap waveforms.

##### `WaveformVisual.probe_scale`

Scale of the probe.

This is a pair of scalars.

##### `WaveformVisual.spike_clusters`

The clusters assigned to the displayed spikes.

##### `WaveformVisual.spike_ids`

Spike ids to display.

##### `WaveformVisual.waveforms`

Displayed waveforms.

This is a `(n_spikes, n_samples, n_channels)` array.

## phy.stats

Statistics

##### `phy.stats.pairwise_correlograms(spike_samples, spike_clusters, binsize=None, winsize_bins=None)`

Compute all pairwise correlograms in a set of neurons.

TODO: improve interface and documentation.

## phy.utils

Utilities.

##### `phy.utils.debug(*msg)`

Generate a debug message.

##### `phy.utils.download_file(url, output=None, checksum=None)`

Download a binary file from an URL.

##### `phy.utils.download_test_data(name, output_dir=None)`

Download a test dataset.

##### `phy.utils.enable_qt()`



##### `phy.utils.info(*msg)`

Generate an info message.

##### `phy.utils.qt_app(*args, **kwds)`

Context manager to ensure that a Qt app is running.

##### `phy.utils.register(logger)`

Register a logger.

##### `phy.utils.run_qt_app()`

Start the Qt application's event loop.

##### `phy.utils.set_level(level)`

Set the level of all registered loggers.

*Parameters*


* `level` (str)

    Can be `warn`, `info`, or `debug`.

##### `phy.utils.start_qt_app()`

Start a Qt application if necessary.

If a new Qt application is created, this function returns it.
If no new application is created, the function returns None.

##### `phy.utils.unregister(logger)`

Unregister a logger.

##### `phy.utils.warn(*msg)`

Generate a warning.

### phy.utils.Bunch

A dict with additional dot syntax.

#### Methods

##### `Bunch.copy()`



#### Properties

### phy.utils.DockWindow

A Qt main window holding docking Qt or VisPy widgets.

#### Methods

##### `DockWindow.add_action(name, callback=None, shortcut=None, checkable=False, checked=False)`

Add an action with a keyboard shortcut.

##### `DockWindow.add_view(view, title='view', position=None, closable=True, floatable=True, floating=None, **kwargs)`

Add a widget to the main window.

##### `DockWindow.closeEvent(e)`

Qt slot when the window is closed.

##### `DockWindow.connect_views(name_0, name_1)`

Decorator for a function that accepts any pair of views.

This is used to connect any view of type `name_0` to any other view
of type `name_1`.

##### `DockWindow.list_views(title='', is_visible=True)`

List all views which title start with a given string.

##### `DockWindow.on_close(func)`

Register a callback function when the window is closed.

##### `DockWindow.on_show(func)`

Register a callback function when the window is shown.

##### `DockWindow.restore_geometry_state(gs)`

Restore the position of the main window and the docks.

The dock widgets need to be recreated first.

This function can be called in `on_show()`.

##### `DockWindow.save_geometry_state()`

Return picklable geometry and state of the window and docks.

This function can be called in `on_close()`.

##### `DockWindow.shortcut(name, key=None)`

Decorator to add a global keyboard shortcut.

##### `DockWindow.show()`

Show the window.

##### `DockWindow.view_counts()`

Return the number of opened views.

#### Properties

### phy.utils.EventEmitter

Class that emits events and accepts registered callbacks.

Derive from this class to emit events and let other classes know
of occurrences of actions and events.

#### Methods

##### `EventEmitter.connect(func=None, event=None, set_method=False)`

Register a callback function to a given event.

To register a callback function to the `spam` event, where `obj` is
an instance of a class deriving from `EventEmitter`:

```python
@obj.connect
def on_spam(arg1, arg2):
    pass
```

This is called when `obj.emit('spam', arg1, arg2)` is called.

Several callback functions can be registered for a given event.

The registration order is conserved and may matter in applications.

##### `EventEmitter.emit(event, *args, **kwargs)`

Call all callback functions registered with an event.

Any positional and keyword arguments can be passed here, and they will
be fowarded to the callback functions.

##### `EventEmitter.unconnect(*funcs)`

Unconnect specified callback functions.

#### Properties

### phy.utils.ProgressReporter

A class that reports progress done.

*Emits*

* `progress(value, value_max)`
* `complete()`

#### Methods

##### `ProgressReporter.connect(func=None, event=None, set_method=False)`

Register a callback function to a given event.

To register a callback function to the `spam` event, where `obj` is
an instance of a class deriving from `EventEmitter`:

```python
@obj.connect
def on_spam(arg1, arg2):
    pass
```

This is called when `obj.emit('spam', arg1, arg2)` is called.

Several callback functions can be registered for a given event.

The registration order is conserved and may matter in applications.

##### `ProgressReporter.emit(event, *args, **kwargs)`

Call all callback functions registered with an event.

Any positional and keyword arguments can be passed here, and they will
be fowarded to the callback functions.

##### `ProgressReporter.is_complete()`

Return wheter the task has completed.

##### `ProgressReporter.set_complete()`

Set the task as complete.

##### `ProgressReporter.set_complete_message(message)`

Set a complete message.

##### `ProgressReporter.set_progress_message(message)`

Set a progress message.

The string needs to contain `{progress}`.

##### `ProgressReporter.unconnect(*funcs)`

Unconnect specified callback functions.

#### Properties

##### `ProgressReporter.progress`

Return the current progress.

##### `ProgressReporter.value`

Current value (integer).

##### `ProgressReporter.value_max`

Maximum value.

### phy.utils.Selector

Object representing a selection of spikes or clusters.

#### Methods

##### `Selector.on_cluster(up=None)`

Callback method called when the clustering has changed.

This currently does nothing, i.e. the spike selection remains
unchanged when merges and splits occur.

##### `Selector.wrapped(*args, **kwargs)`



##### `Selector.subset_spikes_clusters(clusters, n_spikes_max=None, excerpt_size=None)`

Take a subselection of spikes belonging to a set of clusters.

This method ensures that the same number of spikes is chosen
for every spike.

`n_spikes_max` is the maximum number of spikers *per cluster*.

#### Properties

##### `Selector.excerpt_size`

Maximum number of spikes allowed in the selection.

##### `Selector.n_clusters`



##### `Selector.n_spikes`



##### `Selector.n_spikes_max`

Maximum number of spikes allowed in the selection.

##### `Selector.selected_clusters`

Cluster ids appearing in the current spike selection.

##### `Selector.selected_spikes`

Ids of the selected spikes.

### phy.utils.Settings

Manage default, user-wide, and experiment-wide settings.

#### Methods

##### `Settings.get(key, default=None)`

Return a settings value.

##### `Settings.keys()`

Return the list of settings keys.

##### `Settings.on_open(path)`

Initialize settings when loading an experiment.

##### `Settings.save()`

Save settings to an internal settings file.

#### Properties

