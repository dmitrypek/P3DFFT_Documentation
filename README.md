# Read the Docs for P3DFFT

First of all, there is an [official Read the Docs guide page](https://docs.readthedocs.io/en/stable/). If you need more detailed instruction, please refer to the link.

1. Getting started
   * First, you need to download [Sphinx](http://www.sphinx-doc.org/en/master/usage/installation.html).
     * The official Sphinx homepage includes installation instruction.
   * Second, you need to install the [Read the Docs theme](https://sphinx-rtd-theme.readthedocs.io/en/stable/) we are currently using.
     * Make sure to install it via Python package.

2. Editing existing pages
   * While Sphinx and the RtD theme are installed, go to `/PATH/TO/THIS/REPOSITORY/docs/source/` directory.
   * Using any text editor, such as vim, open one of the files with rst extension in the directory, and edit it as you want. Make sure to save it at the end.
     * You may refer to this page for the markup language(reStructuredText) syntax, but be aware there are more resources and syntax than this: (https://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html).

3. Adding or removing a page
   * To add or remove a page, simply create a new rst extension file or remove an existing rst extended file in `/PATH/TO/THIS/REPOSITORY/docs/source/` directory.
   * Make sure to include or remove the file under toctree in `/PATH/TO/THIS/REPOSITORY/docs/source/index.rst` file or any toctrees.

4. Building HTML files with new/modified source files
   * Move to `/PATH/TO/THIS/REPOSITORY/docs/` directory.
   * Do `make clean` and `make html` to wipe existing build and build a new set of HTML files from the source files.
   * If you want to preview the page before publishing it, you can load the HTML files under `/PATH/TO/THIS/REPOSITORY/docs/build/html/` directory using any web browser supporting HTML.
   * Add, commit, and push to Github.
     * If you have not set up the webhook, you need to manually build the version from the [Read the Docs homepage](https://readthedocs.org). Navigate to your profile, and the projects (P3DFFT++). Then, click ‘Build version’. Once it’s finished, click ‘View Docs’ to see the actual documentation page. You might need to refresh the page to see the change.
     * If you have set up the webhook, it will automatically rebuild the version every time you push to the repository.

