This is a generic checklist for submitting a new Python or Node.js module for Netdata.  It is by no means comprehensive.

At minimum, to be buildable and testable, the PR needs to include:

* The module itself, following proper naming conventions:
    - For Python modules, it should be `python.d/<module_name>.chart.py`
    - For Node.js modules, it should be `node.d/<module_name>.node.js`
* The configuration file for the module:
    - For Python modules, this should be `conf.d/python.d/<module_name>.conf`.  Python config files are in YAML format, and should include comments describing what options are present.  Take a look at other config files for Python modules for an idea of what is expected.
    - For Node.js modules, you instead need to provide a Markdown document explaining the configuration process with usable examples for the configuration.  The actual configuration files are in JSON format (which doesn't support comments, hence the need for a file explaining the configuration).  Take a look at the other config descriptions for Node.js modules for an idea of what is expected.
* A basic configuration for the plugin in the appropriate global config file:
    - For Python modules, this should be `conf.d/python.d.conf`, which is also in YAML format.  Either add a line that reads `# <module_name>: yes` if the module is to be enabled by default, or one that reads `<module_name>: no` if it is to be disabled by default.
    - For Node.js modules, this should be `conf.d/node.d.conf`, which is also in JSON format.  If the module should be enabled by default, add a section for it in the `modules` dictionary.
* A line for the plugin in the appropriate `Makefile.am` file:
    - For a Python module, you need a line in `python.d/Makefile.am` under `dist_python_DATA`.
    - For a Node.js module, you need a line in `node.d/Makefile.am` under `dist_node_DATA`.
* A line for the plugin configuration file in `conf.d/Makefile.am`:
    - For Python plugins, the line should be under `dist_pythonconfig_DATA`
    - For Node.js plugins, the line should be under `dist_nodeconfig_DATA`
* A section for the plugin in the appropriate README file, including basic information about what stats the plugin collects, as well as some example configuration, and info about what dependencies (if any) it has.
    - For Python plugins, this is `python.d/README.md`
    - For Node.js plugins, this is `node.d/README.md`
* Optionally, chart information in `web/dashboard_info.js`.  This generally involves specifying a name and icon for the section, and may include descriptions for the section or individual charts.

Once the PR has been accepted and merged, there are two more things you should ideally take care of:

* Add a link to the wiki [sidebar](https://github.com/firehol/netdata/wiki/_Sidebar/_edit) in the appropriate section pointing at the section for the module in the plugin's README file.
* Add an entry for the module [here](https://github.com/firehol/netdata/wiki/Add-more-charts-to-netdata).