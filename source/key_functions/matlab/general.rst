# G-Node MATLAB Toolbox

> The global scale of neuroinformatics offers unprecedented
> opportunities for scientific collaborations between and among
> experimental and theoretical neuroscientists. To fully harvest these
> possibilities, a set of coordinated activities is required that will
> improve three key ingredients of neuroscientific research: data
> access, data storage, and data analysis (...) ([Herz et al., 2008](http://www.g-node.org/publications/NN2436.pdf))

The G-Node MATLAB Toolbox (GMT) allows convenient access to electro-physiological
recordings stored in the G-Node [Data Store](http://portal.g-node.org/data) through
the MATLAB command line. Requirements are

* MATLAB R2008b or higher on any platform supporting Java (Linux, Mac, Windows), as well as a
* G-Node account (available [here](http://portal.g-node.org/data)).

In addition, a backend for persistent and lab-wide caching is recommended but
not necessary (e.g., MongoDB).

## Installation

Installation of GMT is similar to that of most MATLAB
toolboxes. Simply download the newest release and drop it into your
toolbox folder. In order to get the latest stable version (0.41b),
follow these steps:

1. [Download one of the provided archives from GitHub.](http://github.com/G-Node/gnode-client-matlab/downloads)
2. Unzip/untar the release to your MATLAB toolbox directory. In most cases, this
   corresponds to `$MATLAB_HOME/toolbox`. Your toolbox directory should now contain
   a folder `gnode`.
3. Adjust the account settings in the default configuration file
   (`$MATLAB_HOME/toolbox/gnode/default.json`):
   * Set `username` to the name of the G-Node account you want to use.
   * Set `password` to your password. (All transfer is mediated by a secure
     connection.)
   * Set `apiDefinition` to the supplied `requirements.json` file. For instance,
     if your `$MATLAB_HOME` corresponds to `/home/john/matlab/`, you
     should put `/home/john/matlab/toolbox/gnode/requirements.json`.
4. Open MATLAB.
5. Check that your installation was successful by typing `import gnode.*;` at the
   MATLAB prompt. If there are no errors, you can start using the toolbox.
6. Read the [documentation](https://github.com/g-node/gnode-client-matlab/wiki).

If you want to track the development branch, follow these steps. NB,
release channel updates may break compatibility or introduce bugs.

1. Locate your MATLAB toolbox directory. In most cases, this corresponds
   to `$MATLAB_HOME/toolbox`.
2. Clone the git repository from GitHub via
   `git clone https://github.com/g-node/gnode-client-matlab.git`.
3. Proceed from `3` in the outline above.

## Getting started

Before using the toolbox, you should set up your MATLAB environment and initialize a session.

```matlab
% Move all toolbox functions into scope
import gnode.*;

% Prepare MATLAB
set_up_classpath();

% Initialize session. 'default' will use the account settings specified
% during installation.
session = init('default');
```

Now the toolbox is ready for use. It requires a network connection for all
operations involving download or upload. The G-Node data store uses a simple
object model similar to [NEO](http://packages.python.org/neo/). All your data
is stored in a set of object types tailored to electrophysiology, such as blocks,
segments, recording channels, signals et cetera.

In the MATLAB environment, we represent these objects as structures. Here is a
basic example for creating and uploading such an object.

```matlab
% Create a structure containing signal data

signal = make_dummy(session, 'analogsignal');

signal.name = 'My brand new signal';
signal.sampling_rate = struct('units', 'Hz', 'data', 12000);
signal.t_start = struct('units', 'ms', 'data', 0);
signal.signal = struct('units', 'mV', 'data', my_data);

% This returns the ID of the newly created object in
% the G-Node data store

new_object = create(session, signal);
```

Subsequent object retrieval is equally straightforward and supports
various access methods (e.g., single ID, array of IDs, numeric
range). Here are a few examples to get you going:

```matlab
% Lists of objects
my_list = get_list(g, 'analogsignal');

% Single objects
my_recording = get(session, 'analogsignal_947');

% Multiple objects
all = { 'analogsignal_948', 'segment_43', 'unit_8' };
my_recordings = get(session, all);

% Ranges
more_recordings = get_range(session, 'analogsignal', 947, 1002);
```

More advanced operations (e.g., updates, download queues, batch object
creation and upload, and so on) are described in the [GMT wiki](https://github.com/g-node/gnode-client-matlab/wiki).

## FAQ

**Q:** Is the G-Node toolbox compatible with older versions of MATLAB (i.e., before R2008b)?  
**A:** Unfortunately not. GMT makes heavy use of the JVM bridge provided by Mathworks. This
       bridge is under constant development. Various
       incompatibilities and lack of features make it unfeasible to support MATLAB versions
       before R2008b.

**Q:** Is there any versioning support for my data (e.g., revert after destructive edit)?  
**A:** Not yet.

**Q:** Can I contribute?  
**A:** GMT is open-source; pull requests are always welcome. As a user, please report
       any bugs or missing features on the Issue Tracker.

## License

GMT is distributed under the MIT license (see `LICENSE`).
