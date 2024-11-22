# Structural clustering

Each dataset (AFDB light, AFDB dark, ESMAtlas, MIP)  has been clustered independently, using `foldseek`. Subsequently, cluster representatives and MIP singletons have been clustered, again, with `foldseek`.  Optimal parameters (i.e. the ones used in our work) can be found in Table S2 in the Supplement. Below we provide example code:

```
foldseek easy-cluster /input/path/to/structures/ /output/path/to/results/ /tmp/path/ -e 0.001 -c 0.8
```

# 2D representation

## Geometricus

For each input dataset, shape-mer representation has been created:

```
import numpy as np
import glob
import tqdm

from pathlib import Path
from geometricus import get_invariants_for_structures, Geometricus, ShapemerLearn

MAIN_PATH = Path('/path/to/inputs/')
PDB_PATH = MAIN_PATH / 'struct'          # structures
IND_PATH = MAIN_PATH / 'repr_indices'    # indices
OUT_PATH = Path('/path/to/embeddings/')

model = ShapemerLearn.load()

# compute embeddings in batches

def batch_files(path, batch_size):
    batches = []
    for i in path.glob("*"):
        batches.append(i)
        if len(batches)==batch_size:
            yield batches
            batches = []
    yield batches

for path in PDB_PATH.glob('*'):
    (OUT_PATH / path.name).mkdir(parents=True, exist_ok=True)
    
    for batch in tqdm.tqdm(list(batch_files(path, 96*10))):
        learned_invariants, learned_errors = get_invariants_for_structures(batch, n_threads=96, verbose=False)
        # Get count matrix (embeddings)
        shapemer_class = Geometricus.from_invariants(learned_invariants, model=model)
        # Save
        with open(OUT_PATH / path.name / 'indices.txt', 'a') as f:
            for el in shapemer_class.protein_keys:
                f.write(f"{el}\n")
        with open(OUT_PATH / path.name / 'keys.txt', 'a') as f:
            for el in shapemer_class.shapemer_keys:
                f.write(f"{el}\n")
                
        with open(OUT_PATH / path.name / 'shapemers.tsv', 'a') as f:
            matrix = shapemer_class.get_count_matrix()
            for row in matrix:
                row_to_write = "\t".join(map(str,row.tolist()))
                f.write(row_to_write+"\n")
```

Subsequently, all representations have been aligned:

```
IN_PATH = Path('/path/to/embeddings/')
OUT_PATH = Path('/path/to/embeddings/merged/')

# Load embeddings

X_dict, indices_dict, keys_dict = {}, {}, {}
for path in IN_PATH.glob('*'):
    cluster = path.stem
    print(cluster)
    # keys
    keys_dict[cluster] = {}
    for subpath in path.glob('*keys.txt'):
        batch = int(subpath.stem.split('_')[0])
        with open(subpath, 'r') as f:
            keys_dict[cluster][batch] = f.readlines()
        keys_dict[cluster][batch] = list(map(lambda x: x.strip(), keys_dict[cluster][batch]))
    # indices
    indices_dict[cluster] = {}
    for subpath in path.glob('*indices.txt'):
        batch = int(subpath.stem.split('_')[0])
        indices_dict[cluster][batch] = pd.read_csv(subpath, header=None)
        indices_dict[cluster][batch] = list(indices_dict[cluster][batch][0].\
                                            str.replace('.pdb', '').str.replace('.cif', ''))
    # shapemers
    X_dict[cluster] = {}
    for subpath in path.glob('*shapemers.npy'):
        batch = int(subpath.stem.split('_')[0])
        X_dict[cluster][batch] = np.load(subpath)

# Concatenate indices

indices_dict_concat = {}
for key in indices_dict.keys():
    indices_dict_concat[key] = np.concatenate([v for v in indices_dict[key].values()], axis=0) 
    print(key, len(indices_dict_concat[key]))

indices = np.concatenate([v for v in indices_dict_concat.values()], axis=0)

# Extend embeddings to 1024 elements

all_dims_dict = {}
for k in X_dict.keys():
    all_dims_dict[k] = list(set().union(*[set(el) for el in keys_dict[k].values()]))
    print(k, len(all_dims_dict[k]))

all_dims = list(set().union(*[set(el) for el in all_dims_dict.values()]))
assert len(set(all_dims)) == len(all_dims)
len(all_dims)

X_dict_new = {}
for key in X_dict.keys():
    print(key)
    X_dict_new[key] = {}
    for k, v in X_dict[key].items():
        assert len(set(keys_dict[key][k])) == len(keys_dict[key][k])
        # which columns are missing?
        adds = list(set(all_dims) - set(keys_dict[key][k]))
        # find order wrt all columns
        sorter_1 = pd.concat([
            pd.DataFrame(index=keys_dict[key][k], data={'idx_1': range(len(keys_dict[key][k]))}),
            pd.DataFrame(index=adds, data={'idx_1': range(len(keys_dict[key][k]), len(keys_dict[key][k]) + len(adds))})
        ])
        sorter_2 = pd.DataFrame(index=all_dims, data={'idx_2': range(len(all_dims))})
        # add zero columns
        X_dict_new[key][k] = np.concatenate([v, np.zeros([v.shape[0], len(adds)])], axis=1)
        # change column order
        X_dict_new[key][k] = X_dict_new[key][k][:, sorter_2.join(sorter_1).idx_1.values]

for key in X_dict.keys():
    X_dict_new[key] = np.concatenate([v for v in X_dict_new[key].values()], axis=0)  

X = np.concatenate([v for v in X_dict_new.values()], axis=0)

# construct normalized embedding (default)

X_norm = (X.T / X.sum(axis=1)).T

# Save

# np.save(OUT_PATH / 'X_concatenated_all_dims.npy', X)
# np.save(OUT_PATH / 'X_concatenated_all_dims_normed.npy', X_normed)
# np.save(OUT_PATH / 'keys_concatenated.npy', all_dims)
# np.save(OUT_PATH / 'indices_concatenated.npy', indices)
```

## PacMAP reduction

2D representations have been obtained using PaCMAP:
```
embedding_2D_unnormed = pacmap.PaCMAP(n_components=2, n_neighbors=13, 
                          MN_ratio=1.9, FP_ratio=1.5, random_state=1) 
X_pacmap_unnormed = embedding_2D.fit_transform(X, init="pca")

embedding_2D_normed = pacmap.PaCMAP(n_components=2, n_neighbors=10, 
                          MN_ratio=1.3, FP_ratio=0.9, random_state=1) 
X_pacmap_normed = embedding_2D_normed.fit_transform(X_normed, init="pca")
```

Optimal parameters have been chosen with grid search (see "Structure space / PaCMAP grid search" section in the Supplement for details).
