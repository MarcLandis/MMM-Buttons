# MMM-Buttons

This a module for [MagicMirror²](https://github.com/MagicMirrorOrg/MagicMirror) to act based on button presses via GPIO.

![symbolization](symbolization.png)

It is capable of connecting multiple buttons at once, which can be individually configured.
It is basically a generalized version of the [Button module](https://github.com/PtrBld/MMM-Button), original idea comes from @PtrBld.

However it only sends out notifications to other modules.

For example this can be used to send notifications to the following modules:

- [Remote Control](https://forum.magicmirror.builders/topic/735/remote-control-shutdown-configure-and-update-your-magicmirror)
- [Profile Switcher](https://forum.magicmirror.builders/topic/1402/mmm-profileswitcher-a-profile-user-layout-switching-module)

## Installation

Clone this repository in your `modules` folder, and install dependencies:

```bash
cd ~/MagicMirror/modules # adapt directory if you are using a different one
git clone https://github.com/MarcLandis/MMM-Buttons
cd MMM-Buttons
npm install # this can take a while
```

## Configuration

Add the module to your modules array in your `config.js`.

Below is a simple example (needs [Remote Control](https://forum.magicmirror.builders/topic/735/remote-control-shutdown-configure-and-update-your-magicmirror) installed), with two buttons conneted, on pins 24 and 25.

One switches on the display on a short press, and switches it off on a long press.
The other does not do anything on a short press, but shuts down the system after keeping it pressed for 3 seconds with an explanatory user alert.

```js
{
    module: "MMM-Buttons",
    config: {
        buttons: [
            {
                pin: 25,
                name: "monitor_control",
                longPress: [
                    {
                        notification: "REMOTE_ACTION",
                        payload: {action: "MONITOROFF"}
                    }
                ],
                shortPress: [
                    {
                        notification: "REMOTE_ACTION",
                        payload: {action: "MONITORON"}
                    }
                ]
            },
            {
                pin: 24,
                name: "power",
                longPress: [
                    {
                        title: "Power off",
                        message: "Keep pressed for 3 seconds to shut down",
                        imageFA: "power-off",
                        notification: "REMOTE_ACTION",
                        payload: {action: "SHUTDOWN"}
                    }
                ],
                shortPress: [
                    undefined
                ]
            }
        ]
    }
},
```

### Module Configuration

Here is full documentation of options for the modules configuration:

| Option        | Description   |
| ------------- | ------------- |
| `buttons` | An array of button configurations. See [Button Configuration](README.md#Button-Configuration) below. Default is `[]` (no buttons registered). |
| `minShortPressTime` | Minimum duration to trigger a short press in `ms`. Default is `0`. |
| `maxShortPressTime` | Maximum duration to trigger a short press in `ms`. Default is `500`. |
| `minLongPressTime` | Minimum time needed to trigger a long press in `ms`. Default is `3000`. Any press duration between `maxShortPressTime` and `minLongPressTime` does not do anything. |
| `bounceTimeout` | Duration to ignore bouncing (unintentional doubble press on the button). |

### Button Configuration

Each button configuration is an object with the following properties:

| Property      | Description   |
| ------------- | ------------- |
| `pin` | Pin number of the button input (use [**BCM** numbering](http://raspberrypi.stackexchange.com/a/12967)). |
| `name` | Name of the button for easier identification and log output. |
| `longPress` | Choose what notification to send on a long press. See [Notification Configuration](README.md#Notification-Configuration) below. Use `undefined` if nothing should trigger. |
| `shortPress` | Choose what notification to send on a short press. See [Notification Configuration](README.md#Notification-Configuration) below. Use `undefined` if nothing should trigger. |

### Notification Configuration

Each notification configuration is an array of objects with the following properties:

| Property      | Description   |
| ------------- | ------------- |
| `notification` | Notification name. |
| `payload` | Notification payload. Can be anything, for example a `string` or an `object`. |
| `title`, `message`, and `imageFA` | *Optional (only for long press notifications):* If you want to display a message before executing set its options here. See [Alert documentation](https://github.com/MagicMirrorOrg/MagicMirror/tree/master/modules/default/alert#alert-params) for their meaning. |
