
# QGISMCP - QGIS Model Context Protocol Integration

QGISMCP connects [QGIS](https://qgis.org/) to [Claude AI](https://claude.ai/chat) through the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/getting-started/intro), allowing Claude to directly interact with and control QGIS. This integration enables prompt assisted project creation, layer loading, code execution and more.

This project is strongly based on the [BlenderMCP](https://github.com/ahujasid/blender-mcp/tree/main) project by [Siddharth Ahuja](https://x.com/sidahuj)

## Features

- **Two-way communication**: Connect Claude AI to QGIS through a socket-based server.
- **Project manipulation**: Create, load and save projects in QGIS.
- **Layer manipulation**: Add and remove vector or raster layers to a project.
- **Execute processing**: Execute processing algorithms ([Processing Toolbox](https://docs.qgis.org/3.40/en/docs/user_manual/processing/toolbox.html)).
- **Code execution**: Run arbitrary Python code in QGIS from Claude. Very powerful, but also be very cautious using this tool.

## Components

The system consists of two main components:

1. **[QGIS plugin](/qgis_mcp_plugin/)**: A QGIS plugin that creates a socket server within QGIS to receive and execute commands.
2. **[MCP Server](/src/qgis_mcp/qgis_mcp_server.py)**: A Python server that implements the Model Context Protocol and connects to the QGIS plugin.

## Installation

### Prerequisites

- QGIS 3.X (only tested on 3.22)
- Claude desktop
- Python 3.10 or newer
- uv package manager:

If you're on Mac, please install uv as

```bash
brew install uv
```

On Windows Powershell

```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Otherwise installation instructions are on their website: [Install uv](https://docs.astral.sh/uv/getting-started/installation/)

**⚠️ Do not proceed before installing UV**

### Download code

Download this repo to your computer. You can clone it with:

```bash
git clone git@github.com:jjsantos01/qgis_mcp.git
```

### QGIS plugin

You need to copy the folder [qgis_mcp_plugin](/qgis_mcp_plugin/) and its content on your QGIS profile plugins folder.

You can get your profile folder in QGIS going to menu `Settings` -> `User profiles` -> `Open active profile folder` Then, go to `Python/plugins` and paste the folder `qgis_mcp_plugin`.

> On a Windows machine the plugins folder is usually located at: `C:\Users\USER\AppData\Roaming\QGIS\QGIS3\profiles\default\python\plugins` 

and on MacOS: `~/Library/Application\ Support/QGIS/QGIS3/profiles/default/python/plugins`

 Then close QGIS and open it again. Go to the menu option `Plugins` > `Installing and Managing Plugins`, select the `All` tab and search for "QGIS MCP", then mark the QGIS MCP checkbox.

### Claude for Desktop Integration

Go to `Claude` > `Settings` > `Developer` > `Edit Config` > `claude_desktop_config.json` to include the following:

> If you can't find the "Developers tab" or the `claude_desktop_config.json` look at this [documentation](https://modelcontextprotocol.io/quickstart/user#2-add-the-filesystem-mcp-server).

```json
{
    # QGIS MCP

    QGIS MCP connects a desktop MCP client to QGIS through a local plugin and a Python MCP server. It supports project, layer, feature, map-rendering, and Processing workflows.

    ## Requirements

    - QGIS 3.x (the project has been tested with QGIS 3.22)
    - Python 3.12 or newer for the MCP server
    - [uv](https://docs.astral.sh/uv/getting-started/installation/)
    - An MCP-compatible client, such as Visual Studio Code or Claude Desktop

    The QGIS plugin runs inside QGIS. The Python MCP server runs as a separate process and communicates with the plugin over `localhost:9876`.

    ## Installation

    1. Clone the repository and install the Python dependencies:

         ```powershell
         git clone https://github.com/aptana/QGIS-MCP.git
         cd QGIS-MCP
         uv sync
         ```

    2. Install the plugin into the active QGIS profile. In QGIS, open **Settings > User Profiles > Open Active Profile Folder**, then copy the repository's `qgis_mcp_plugin` folder into the profile's `python/plugins` directory. The resulting path should end with `python/plugins/qgis_mcp_plugin`.

         Typical plugin directory locations:

         - Windows: `%APPDATA%\QGIS\QGIS3\profiles\default\python\plugins`
         - macOS: `~/Library/Application Support/QGIS/QGIS3/profiles/default/python/plugins`

         Restart QGIS, open **Plugins > Manage and Install Plugins**, and enable **Q-MCP** (shown as **QGIS MCP** in the plugin menu).

    3. In QGIS, open **Plugins > QGIS MCP > QGIS MCP** and click **Start Server**.

         ![QGIS MCP plugin menu](assets/imgs/qgis-plugins-menu.png)

         ![Start the QGIS MCP server](assets/imgs/qgis-mcp-start-server.png)

    ## Configure an MCP Client

    For Visual Studio Code, create or update `.vscode/mcp.json` in your workspace. Replace the directory with the absolute path to your local clone:

    ```json
    {
        "servers": {
            "qgis": {
                "type": "stdio",
                "command": "uv",
                "args": [
                    "--directory",
                    "C:/path/to/QGIS-MCP",
                    "run",
                    "src/qgis_mcp/qgis_mcp_server.py"
                ]
            }
        }
    }
    ```

    For other MCP clients, use the same command and arguments in that client's MCP server configuration. Ensure `uv` is available to the client process; if it is not, set `command` to the full path to `uv.exe`.

    Start or restart the MCP server in the client after saving its configuration. Keep QGIS open and the plugin server running while using the tools.

    ## Available Tools

    - `ping`, `get_qgis_info`
    - `load_project`, `create_new_project`, `get_project_info`, `save_project`
    - `add_vector_layer`, `add_raster_layer`, `get_layers`, `remove_layer`, `zoom_to_layer`
    - `get_layer_features`, `export_layer_features_geojson`
    - `execute_processing`, `render_map`
    - `execute_code`

    ![QGIS MCP tools in an MCP client](assets/imgs/claude-available-tools.png)

    ## Usage Notes

    - Paths passed to tools must be accessible on the machine running QGIS.
    - `execute_processing` runs QGIS Processing algorithms with the supplied algorithm ID and parameters.
    - `execute_code` runs arbitrary Python code inside QGIS. Only use it with code you trust; it can access or modify anything available to the QGIS process.
    - The plugin listens on `localhost:9876`. If the server cannot start, check whether another process is using that port.

    ## Troubleshooting

    - **The MCP server cannot connect:** Start the plugin server from QGIS before starting or restarting the MCP server in your client.
    - **The plugin is missing:** Confirm that `qgis_mcp_plugin` is inside the active profile's `python/plugins` directory, then restart QGIS and enable the plugin.
    - **Dependency or import errors:** Run `uv sync` from the repository root and check that the client configuration points to this clone.
    - **The server cannot bind its port:** Check for another process listening on port `9876`.
