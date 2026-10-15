# Contributing to QGIS MCP

Thanks for helping improve QGIS MCP. Changes to the QGIS plugin or MCP server should be tested with QGIS running locally.

## Development Setup

1. Clone the repository and install the server dependencies:

   ```powershell
   git clone https://github.com/aptana/QGIS-MCP.git
   cd QGIS-MCP
   uv sync
   ```

2. Install the `qgis_mcp_plugin` folder into the active QGIS profile's `python/plugins` directory. In QGIS, find it under **Settings > User Profiles > Open Active Profile Folder**. Restart QGIS and enable **Q-MCP** from **Plugins > Manage and Install Plugins**.

   Windows profile plugins path: `%APPDATA%/QGIS/QGIS3/profiles/default/python/plugins`

3. Start the plugin server from **Plugins > QGIS MCP > QGIS MCP > Start Server**. Configure your MCP client to run `uv --directory /absolute/path/to/QGIS-MCP run src/qgis_mcp/qgis_mcp_server.py` using stdio.

## Contribution Guidelines

- Keep pull requests focused and describe the behavior being changed.
- Update documentation when setup or tool behavior changes.
- Test plugin and server changes with QGIS 3.x; the project was tested with QGIS 3.22.
- Be careful with `execute_code`: it runs arbitrary Python inside QGIS.

## Report Issues

Open an issue at https://github.com/aptana/QGIS-MCP/issues and include your operating system, QGIS version, and relevant error details.
