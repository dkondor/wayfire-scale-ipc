# wayfire-scale-ipc

Extra IPC interaction for the scale plugin of Wayfire. Currently, it provides the `scale_ipc_filter/activate_appid` IPC method, which will start scale and filter the displayed apps based on the `app_id` parameter given. This can be used by e.g. a taskbar or dock to show all open views of one app. An example implementation for Cairo-Dock is given [here](https://github.com/dkondor/cairo-dock-core/blob/wayfire_integration/src/implementations/cairo-dock-wayfire-integration.c).

## Installing

### 1. Dependencies

It needs a recent version of Wayfire (at least commit [3ac0284](https://github.com/WayfireWM/wayfire/pull/1864/commits/3ac028406cc3697dd40c128721fb6e681b00c337)) and [nlohmann_json/](https://github.com/nlohmann/json/). For building, it needs [Meson](https://mesonbuild.com/).


### 2. Downloading the source

Use git:

```
git clone https://github.com/dkondor/wayfire-scale-ipc.git
cd wayfire-scale-ipc
```

or the "Download ZIP" option (click on the green "Code" button above).


### 3. Building and installing

Before using, the code needs to be compiled and installed. It uses the standard Meson way:

```
meson build
ninja -C build
sudo ninja -C build install
```

### 4. Usage

Enable it in [WCM](https://github.com/WayfireWM/wcm) ("Scale IPC addon" in the Utilities category) or in the config file by adding `scale_ipc_filter` to the list of active plugins (`core/plugins`).

Important: it needs the IPC plugin to also be active! For recent Wayfire versions, this is available as "IPC protocol" in WCM and can be enabled from there. For older Wayfire, it can be enabled in the config file:
1. Open the config file in a text editor (by default, it is under `~/.config/wayfire.ini`)
2. Find the `[core]` section and the line starting with `plugins = `
3. Add `ipc` anywhere in this line

Once this plugin is enabled, it can be activated by sending the `scale_ipc_filter/activate_appid` command via IPC. A simple example in Python that uses the [pywayfire](https://github.com/WayfireWM/pywayfire) library would work as follows:

```
import wayfire
from wayfire.core.template import get_msg_template
socket = wayfire.WayfireSocket('/tmp/wayfire-wayland-1.socket') # or use: os.environ['WAYFIRE_SOCKET']
msg = get_msg_template('scale_ipc_filter/activate_appid')
msg['data']['all_workspaces'] = True
msg['data']['app_id'] = 'sakura'
socket.send_json(msg)
```

This should show all open instances of Sakura (terminal app) in scale. If everything works, the method call should return the following response:
```
{'result': 'ok'}
```

The following options are supported (add to the `data` dictionary of the message):
 - `app_id`: string, application ID to filter for; only apps that match this exactly will be shown (required)
 - `case_sensitive`: boolean, whether the app ID is matched in a case sensitive way (optional, default: True)
 - `all_workspaces`: boolean, whether views from all workspaces will be included (optional, default: False)
 - `output_id`: integer, numeric ID of the output (monitor) to use (optional, by default, the currently active output is used)

Possible issues:
 - Cannot connect to the socket (e.g. `FileNotFoundError` exception in the above code): IPC plugin is not activated (check in the config file), or Wayfire uses a different filename for sockets (check in `/tmp` or the `WAYFIRE_SOCKET` environment variable).
 - Response is `{'error': 'No such method found!'}`: this plugin is not activated, check in WCM or in the config file.
 - All views disappear: this can happen if `app_id` does not match any open view. In this case, simply exit scale with its activator keybinding.



