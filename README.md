# Create Your Own HeliosWeb

[Helios-Web](https://heliosweb.io) is a browser-based interactive visualization app that can render large-scale networks in real-time. This is one of my key tools for science and public discourse, hypothesis generations, and self-entertainments! 

The goal of this repository is to make it easier to deploy *public* interactive visualizations on GitHub Page. I hope this repository servers as an entry point for this great visualization tool. If you like the tool, please give a star ⭐ to [the HeliosWeb repository here](https://github.com/filipinascimento/helios-web).

**Demo**: [Demonstration](https://skojaku.github.io/myheliosweb-template/?network=scientific-mobility&advanced&dark&density&size=0.5&layout=0&use3d&colorProperty=country&additive&shad). This visualizaiton stems from [Unsupervised embedding of trajectories captures the latent structure of scientific migration](https://www.pnas.org/doi/10.1073/pnas.2305414120)  by Dakota Murray, Jisung Yoon, Sadamori Kojaku, Rodrigo Costas, Woo-Sung Jung, Staša Milojević, and Yong-Yeol Ahn.


## Creating Your Own *Public* Visualization🔨!

1. Create a new GitHub repository (public).
2. Copy the `index.html` file and the `scripts/` folder in this repository to your own repository.
3. Place your network data files (in `.xnet` format) in a `networks/` folder in your repository.
4. Make the repository a GitHub Page site (Settings > Pages > Source: `main` branch / `/root` folder).
5. Access your visualization at `https://<your-github-username>.github.io/<your-repo-name>/index.html?network=<your-network-file without the extension>`.

> [!NOTE]
> If you don't want to share your visualization publicly, see [Running Locally](#running-locally) for instructions on how to run it on your local machine.

> [!NOTE]
> You can also fork this repository directly and modify it as needed. 


## Data Preparation

Prepare your network data using `igraph` and convert it to the `.xnet` format:

> [!NOTE]
> **Prerequisites**: Helios-Web accepts `.xnet` format files. First, install the xnet library by running `pip install xnetwork`. You will also need ``igraph`` and ``numpy``.

```python
import igraph as ig
import xnetwork as xn
import numpy as np

# Create a graph with your data
n_nodes = 100
edges = [(0, 1), (1, 2), (2, 3), ...]  # Your edge list

g = ig.Graph(n_nodes, edges).simplify()

# Add node attributes
g.vs["label"] = ["Node " + str(i) for i in range(n_nodes)]
g.vs["type"] = [...]  # Your node types
g.vs["country"] = [...]  # Custom attribute
g.vs["degree"] = g.degree()

# Add 2D or 3D positions if you want to specify layout. Otherwise, the network will be automatically laid out. 
# For 2D visualization:

# positions_2d = np.random.rand(n_nodes, 2)  # Replace with your layout algorithm
# g.vs["Position"] = positions_2d

# For 3D visualization:
# positions_3d = np.random.rand(n_nodes, 3)
# g.vs["Position"] = positions_3d

# Export to xnet format
xn.igraph2xnet(g, "output_network.xnet")
```

> [!NOTE]
> You can specify the positions of nodes by **Position** attribute (2D or 3D numpy array). If not provided, Helios-Web will compute a layout automatically. 
> The **Label** is a special attribute that can be used for searching nodes in the visualization. 
> - Categorical attributes (e.g., `type`, `country` etc; names are arbitrary) will be recognized as attribute for coloring
> - Numerical attributes (e.g., `degree`) will be recognized as attribute for sizing nodes.

## URL Parameters

Load specific networks by adding URL parameters:

### Loading Local Network Files

To load a network file from the `networks/` folder:

```
index.html?network=scientific-mobility
```

### Loading Compressed Network Files (.xnet.tar.gz) - NEW!

You can also load compressed `.xnet.tar.gz` files containing `.xnet` networks. This is useful for reducing file size and bandwidth usage:

```
index.html?network=my-network.xnet.tar.gz
```

**How it works:**
- The `.xnet.tar.gz` file is automatically decompressed in the browser
- The first `.xnet` file found in the archive is loaded
- The extracted content is cached for faster subsequent loads

**Creating a compressed network file:**
```bash
# Create a .xnet.tar.gz file containing your .xnet network
tar -czf my-network.xnet.tar.gz my-network.xnet
```

**Important:** Save the compressed file with the `.xnet.tar.gz` extension (not just `.tar.gz`). Place it in the `networks/` folder alongside your regular `.xnet` files.

This can significantly reduce download times for large networks!

### Combining with Other Parameters

You can combine the network parameter with other visualization parameters:

```
index.html?network=scientific-mobility&dark&use3d&advanced&size=0.5
```

Customize the visualization behavior using URL parameters:

### Basic Parameters

| Parameter | Purpose | Example | Default |
|-----------|---------|---------|---------|
| `network` | Network file to load (without extension) | `?network=karate` | First available network |
| `format` | Data format | `?format=xnet` | `xnet` |
| `use2d` or `2d` | Enable 2D mode | `?use2d` | 3D mode |
| `use3d` or `3d` | Enable 3D mode | `?use3d` | Based on data |

### Visual Style Parameters

| Parameter | Purpose | Example | Default |
|-----------|---------|---------|---------|
| `dark` | Enable dark background | `?dark` | Light background |
| `size` | Node size | `?size=0.5` | Auto |
| `opacity` | Node opacity | `?opacity=0.8` | Auto |
| `zoom` | Initial zoom level | `?zoom=1.5` | Auto |
| `shaded` | Enable shaded nodes | `?shaded` | Disabled |
| `additive` | Additive blending mode | `?additive` | Disabled |

### Layout & Rendering Parameters

| Parameter | Purpose | Example | Default |
|-----------|---------|---------|---------|
| `layout` | Auto-start layout | `?layout=1` (start), `?layout=0` (stop) | Based on size |
| `advanced` | Advanced edge rendering | `?advanced` | Disabled |
| `hyperbolic` | Hyperbolic geometry | `?hyperbolic` | Disabled |

### Data Property Parameters

| Parameter | Purpose | Example | Default |
|-----------|---------|---------|---------|
| `colorProperty` | Attribute for node coloring | `?colorProperty=country` | `index` |
| `densityProperty` | Attribute for density calculation | `?densityProperty=Degree` | `Uniform` |
| `vsDensityProperty` | Secondary density property | `?vsDensityProperty=Age` | `None` |
| `shallNormalizeVsDensity` | Normalize secondary density | `?shallNormalizeVsDensity` | Disabled |

### UI Parameters

| Parameter | Purpose | Example | Default |
|-----------|---------|---------|---------|
| `nosearch` | Disable search | `?nosearch` | Search enabled |
| `nolegends` | Disable legends | `?nolegends` | Legends enabled |
| `density` | Enable density visualization | `?density` | Disabled |

Example Usage:
```
index.html?network=scientific-mobility&dark&size=0.5
```

## Running Locally

Download the repository and run a local web server to visualize your networks:

```bash
# Start a local web server
python -m http.server 8000

# Open in browser
# Navigate to: http://localhost:8000
```

Place your network dat under networks folder and access it via URL parameters as described above.

## Project Structure

```
.
├── index.html              # Main HTML file with embedded styles
├── scripts/
│   └── main-c9742021.js   # Bundled application JavaScript
└── networks/               # Network data files (.xnet or .xnet.tar.gz format)
    ├── scientific-mobility.xnet
    ├── scientific-mobility.xnet.tar.gz  # Compressed version
    └── ...
```

