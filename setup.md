# 💻 Data analysis 001 - Setup

- [Setting up your environment](#setting-up-your-environment)
  - [Install `pweave`](#install-pweave)
  - [Configure Atom](#configure-atom)
  - [Install LaTeX](#install-latex)
- [Organizing your analysis](#organizing-your-analysis)

## Setting up your environment

There are few people who have no need of fiddling when writing analysis code
on the fly. If you aren't one of them, have no fear. The setup set forth in this
document optimizes your environment for interactive development, without having
build and rebuild your output document to check the axis labels on one chart.

### Install `pweave`

"Literate programming" is the process of bundling source code and documentation
into one package in service of legibility. By extension, "literate analysis"
combines analytical code with narrative text in service of legiblity, as well
as transparency and reproducibility.

In the "literate X" nomenclature, code is compiled by "tangling" the combined
source document, and documentation is generated by "weaving." [`pweave` is a
Python library](http://mpastell.com/pweave/) for "weaving" and "tangling" source
files containing text and Python code blocks.

Installation is as simple as:

```
$ pip install pweave
```

`pweave` can be used as both a Python module and a command-line utility. So far,
we've found the CLI to fit most seamlessly into our Make-driven data pipeline.

For more on using `pweave`, head on over to [Data analysis 101](/using-the-toolkit.md).
Still setting up? Read on.

### Configure Atom

At DataMade, we all have our own favorite code editor. When it comes to analysis,
**Atom** plays nicely with `pweave` to create an interactive development environment
that rivals [Jupyter notebooks](https://jupyter.org/) – without the hassle of
illegible diffs and highly customized file conversion steps.

The bad news is that this environment still requires some configuration. The
good news is that one Denver data scientist [wrote an excellent guide](http://protips.maxmasnick.com/literate-python-setup-with-pweave-and-atom)
to that configuration.

There have been a few changes since the original guide was written. So, we've
put together an amended walk through:

1. Install [Atom](http://atom.io/).
2. Install these Atom packages:
    - [`hydrogen`](https://atom.io/packages/Hydrogen) – provides inline execution of Python code
    - [`language-weave`](https://atom.io/packages/language-weave) – adds Pweave input files to languages recognized by Atom, and provides syntax highlighting
3. Open your Atom preferences. In the left pane, select "Packages", then search for "Hydrogen" and click "Settings".
    - Under "Language Mappings", add `{"Pweave markdown": "Python 3", "Pweave LaTeX": "Python 3"}`. This lets Hydrogen know there are Python code blocks in in `.pmd` and `.ptexw` files.
    - Under "Startup Code", add `{"Python 3": "%matplotlib inline\nimport matplotlib\n"}`. This tells matplotlib figures to appear in the output when you run a code cell.
4. In the left pane of the Preferences window, click the "Open Config Folder" button to open your Atom configuration files.
    - In `styles.less`, add:

    ```less
    // Hydrogen output - font size is too small
    .hydrogen.output-bubble pre {
      font-size: 16px !important;
    }
    // Hydrogen - hack for 2x images
    .hydrogen.output-bubble .bubble-result-container img {
      width: 50% !important
    }
    ```
    - In `keymap.cson`, add:

    ```cson
    '.platform-darwin atom-text-editor':
      'cmd-enter': 'hydrogen:run'
      'shift-enter': 'hydrogen:run-cell'
    ```
5. If you haven't already, [install `virtualenvwrapper`](https://virtualenvwrapper.readthedocs.io/en/latest/install.html#basic-installation).
6. In your terminal, create or activate the virtual environment you'll use for your project, then

    ```bash
    pip install ipython ipykernel
    python -m ipykernel install --user --name $(basename $VIRTUAL_ENV) --display-name "Python ($(basename $VIRTUAL_ENV))"
    ```

    This will create a "kernel," or [a program that runs your code](https://jupyter-client.readthedocs.io/en/latest/kernels.html#making-kernels-for-jupyter), named for your current virtual environment. Hydrogen uses kernels to determine how to evaluate the Python you write. Installed packages are included in kernels, so you'll need different kernels for different virtual environments. Thus, you need to run `ipykernel install` each time you want to use Hydrogen from a new virtual environment.

8. In Atom, create and save a Python (`.py`) file. To your file, add:

    ```python
    print('Hello, world!')
    ```

    Then, press `shift + enter`. You'll see a dropdown menu with options for "Python 3", as well as "Python (name)" for each of the virtual environments you've installed a kernel for.

    Select the option corresponding to the virtual environment you'd like to use, then watch in awe as "Hello, world!" materializes before you!
    - Ready to get started with `pweave`? Head on over to [Data analysis 101](/using-the-toolkit.md) to write your first code-prose mash up!

### Install LaTeX

LaTeX is essentially a document formatting markup language. Skip the muss
and fuss and licenses of popular GUI software. You, too, can produce beautiful
documents with only a few inline tags (think HTML) and useful macros (think
templating engines, like Jinja).

Installation is well-trodden ground. Follow the appropriate link for your
operating system:

- [GNU/Linux & Windows](http://www.tug.org/texlive/)
- [OS X](http://www.tug.org/mactex/)

#### A note on OS X Installation

The [standard LaTeX distribution](http://www.tug.org/mactex/) (also available via Homebrew) is pretty darn big. If you prefer a smaller installation (or your internet connection is slow or unreliable), [BasicTeX](http://www.tug.org/mactex/morepackages.html) (look for `BasicTeX.pkg`) provides all the utilities you need for this pipeline.

In order to use the command line utilities that come with BasicTeX, we had to add `/Library/TeX/texbin` to our PATH variable, or the setting that tells your terminal where to look for commands.

To do this, open your bash profile in your favorite code editor (i.e., `subl ~/.bash_profile`) and look for a line that looks like `export PATH=foo/bar:baz:a/file/path:$PATH`.

If it doesn't exist, add: `export PATH=/Library/TeX/texbin:$PATH`.

If it does exist, add `/Library/TeX/texbin:` (don't forget the colon!) to the end, just before `$PATH`.

Once you've added the appropriate value, save the file. Then, return to your terminal and type `source ~/.bash_profile` to load the changes.

To confirm it all worked, try typing `latex` into your terminal. If you get something like this –

```bash
This is pdfTeX, Version 3.14159265-2.6-1.40.18 (TeX Live 2017) (preloaded format=latex)
restricted \write18 enabled.
**
```

– you're good to go! (Hit `ctrl-c` to exit.)

## Organizing your analysis

Data making and analysis may live in the same repository, or an analysis
repository [may list a data making repository as a submodule](https://github.com/City-Bureau/chicago-lead).

Either way, analysis files should generally be organized as follows:

```
├── README.md - instructions for making your data and analysis
├── raw
│   └── raw data
├── documentation
│   └── documentation of raw data
├── intermediate
│   └── local copies of intermediate generated data, i.e. static source data downloaded from third-party
├── scripts
│   └── custom transformation scripts
├── data
│   └── generated data
├── analyses
│   └── raw and generated analysis files
├── requirements.txt
└── Makefile
```
