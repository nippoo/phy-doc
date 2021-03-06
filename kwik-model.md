## Working with Kwik files

> phy 0.1.0 only supports files in the Kwik format. Future versions may support other file formats.

The [**Kwik format**](https://github.com/klusta-team/kwiklib/wiki/Kwik-format) features several files:

* `.kwik`: metadata, spikes, clusters
* `.kwx`: features, waveforms
* `.raw.kwd`: raw extracellular traces

phy 0.1.0 requires you to provide all of these files. You can use the [`klusta`](https://github.com/klusta-team/example) program to generate them from raw data in `.dat` format (flat binary file with int16 samples). As part of this process, spikes are detected, extracted, and grouped into putative clusters.

> OpenEphys can also generate Kwik files. Feel free to contact us if you get any problem or incompatibility.

phy 0.2.0 will integrate the entire toolchain to go from raw data to Kwik files, and you won't need to use the external `klusta` program anymore.

> Currently, the `klusta` program may complete in minutes to hours, days, or even weeks for the largest datasets. We will make this process considerably faster (notably using parallelization) in phy 0.2.0 and later.


### Opening a dataset

Open a Kwik dataset with:

```python
>>> from phy.io import KwikModel
```

Let's assume that we have a Kwik dataset in the current directory:

```python
>>> !ls | grep myexperiment
myexperiment.kwik
myexperiment.kwx
myexperiment.raw.kwd
```

```python
>>> kwik_path = 'myexperiment.kwik'
>>> model = KwikModel(kwik_path)
```

The waveforms in the `.kwx` file are not used at all in phy: rather, they are dynamically extracted and filtered from the raw data. This is because storing waveforms takes too much storage space.

Now, `model` offers you access to all of your data. See the [API](https://github.com/kwikteam/phy-doc/blob/master/api.md#phyiokwikmodel) for more details. In IPython, use tab completion to discover the attributes and methods of the object.

```python
>>> model.describe()
Kwik file               myexperiment.kwik
Recordings              1
List of shanks          0*
Clusterings             main*, original
Channels                32
Spikes                  18539
Clusters                24
Duration                120s
```

### Spikes and clusters

```python
>>> model.spike_samples
array([     35,     795,     812, ..., 2398670, 2398753, 2399983], dtype=uint64)
```

```python
>>> model.spike_times
array([  1.75000000e-03,   3.97500000e-02,   4.06000000e-02, ...,
         1.19933500e+02,   1.19937650e+02,   1.19999150e+02])
```

```python
>>> model.spike_clusters
array([20, 10, 25, ..., 10, 12, 20], dtype=uint32)
```

```python
>>> model.cluster_ids
array([ 2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16, 17, 18,
       19, 20, 21, 22, 23, 24, 25])
```

```python
>>> model.cluster_groups
{2: 3,
 3: 3,
 ...
 24: 3,
 25: 3}
```

Here are the default cluster groups:

```python
>>> model.default_cluster_groups
{0: 'Noise', 1: 'MUA', 2: 'Good', 3: 'Unsorted'}
```

### Metadata

The metadata contains information from the PRM file (used by SpikeDetekt2):

```python
>>> model.metadata
{'assigntofirstclosestmask': 1,
 'chunk_overlap': 300,
 'chunk_size': 20000,
 'connected_component_join_size': 1,
 'debug': 0,
 'detect_spikes': "b'negative'",
 'excerpt_size': 20000,
 'experiment_name': "b'test_hybrid_120sec'",
 'extract_s_after': 16,
 'extract_s_before': 16,
 'filter_butter_order': 3,
 'filter_high': 9500.0,
 'filter_lfp_high': 300,
 'filter_lfp_low': 0,
 'filter_low': 500.0,
 'fullstepevery': 10,
 'maskstarts': 100,
 'maxiter': 10000,
 'maxpossibleclusters': 500,
 'nbits': 16,
 'nchannels': 32,
 'nexcerpts': 50,
 'nfeatures_per_channel': 3,
 'pca_nwaveforms_max': 10000,
 'penaltyk': 0,
 'penaltyklogn': 1,
 'prb_file': "b'32chan1shankbuzsaki.prb'",
 'priorpoint': 1,
 'randomseed': 654,
 'raw_data_files': "b'test_hybrid_120sec.raw.kwd'",
 'sample_rate': 20000,
 'savecovariancemeans': 0,
 'savesorted': 0,
 'splitevery': 100,
 'splitfirst': 20,
 'subset': 1,
 'threshold_strong_std_factor': 4.5,
 'threshold_weak_std_factor': 2.0,
 'usedistributional': 1,
 'usemaskedinitialconditions': 1,
 'voltage_gain': 10.0,
 'waveforms_nsamples': 32}
```

### Masks and features

You have two ways to access masks and features. One is faster when you want to access to particular spikes, the other when you want to access to particular clusters. The two methods only differ in the speed of data access. The second method is described in the manual clustering guide.

#### Accessing spikes

The `model.masks` acts as a memory-mapped array to HDF5. You can access the masks of individual spikes. The shape is `(n_spikes, n_channels)`.

```python
>>> model.masks.shape
(18539, 32)
```

> This is an instance of `PartialArray`: in the current file format, masks and features are stored in the same array, for legacy reasons. This partial array dyamically maps indexing to the original HDF5 array. This is why this object is not just an `h5py` dataset.

Mask vector of spike number 10:

```python
>>> model.masks[10]
array([ 0.,  0.,  ...,  0.,  0.], dtype=float32)
```

Masks of the first ten spikes:

```python
>>> model.masks[:10].shape
(10, 32)
```

Masks of three spikes:

```python
>>> model.masks[[10, 20, 30]].shape
(3, 32)
```

This is the same thing for the features, except that this is now a 2D `(n_spikes, n_channels * n_features)` array:

```python
>>> model.features.shape
(18539, 96)
```

### Waveforms

Waveforms are dynamically fetched and filtered from the raw data file. This is all transparent, and you can access them as if it was a `(n_spikes, n_samples, n_channels)` array:

```python
>>> model.waveforms
<phy.io.kwik.model.SpikeLoader at 0x7fa9d986be48>
```

```python
>>> model.waveforms.shape
(18539, 32, 32)
```

```python
>>> model.waveforms[:10].shape
(10, 32, 32)
```

### Extracellular traces

The following is a memory-mapped array from the `.raw.kwd` file:

```python
>>> model.traces
<HDF5 dataset "data": shape (2400000, 32), type "<i2">
```

### Working with multiple shanks and clusterings

```python
>>> model.channel_groups
[0]
```

```python
>>> model.channel_group
0
```

```python
>>> model.clusterings
['main', 'original']
```

```python
>>> model.clustering
'main'
```

Here is how to change the current clustering (by default, the **main** clustering is the one that you modify during manual clustering, while the **original** one comes from the automatic clustering algorithm like KlustaKwik):

```python
>>> model.clustering = 'original'
```

```python
>>> model.spike_clusters
array([20, 10, 25, ..., 10, 12, 20], dtype=uint32)
```

### Modifying the data

If you want to modify the clusters, you need to start a manual clustering session, as described later in the user guide.
