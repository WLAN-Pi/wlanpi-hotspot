# WLAN Pi Hotspot Mode

## Overview

The **Hotspot Mode** transforms the WLAN Pi into a wireless access point (AP). This allows other devices to connect to the WLAN Pi via Wi-Fi to access tools, services, or use it as a bridge to the Ethernet port.

## How It Works

The transition between "Classic" mode and "Hotspot" mode is managed by the `hotspot_switcher` script. This script ensures a clean state switch by backing up existing configurations, applying new ones, and rebooting the device.

### Activation (`hotspot_on`)

When Hotspot mode is enabled:

1.  **Pre-flight Checks**:
    *   Verifies the device is currently in "Classic" mode.
    *   Checks if the Wi-Fi adapter supports AP mode.
    *   (Optional) Checks for `ser2net` status if Wi-Fi Console is enabled.

2.  **Configuration Randomization**:
    *   If the default SSID ("WLAN Pi Default SSID") is detected, it is renamed to `WLAN Pi <Last 3 MAC chars>` to ensure uniqueness.
    *   If the default passphrase ("WL@NP123") is detected, a new random 12-character passphrase is generated.

3.  **Backup**:
    *   Existing network and system configurations (DHCP, Interfaces, Firewall, Sysctl) are backed up with a `.hspt` extension (e.g., `/etc/network/interfaces.hspt`).

4.  **Configuration Switch**:
    *   Original config files are removed.
    *   Symbolic links are created pointing to the Hotspot configurations located in `/etc/wlanpi-hotspot/`.

5.  **Service Enablement**:
    *   `hostapd` (Access Point software) and `isc-dhcp-server` are enabled to start on boot.

6.  **Reboot**:
    *   The device reboots to apply all network and service changes.

### Deactivation (`hotspot_off`)

When switching back to Classic mode:

1.  **Cleanup**:
    *   Symbolic links to Hotspot configurations are removed.
    *   Firewall rules specific to Hotspot/WConsole are deleted.

2.  **Restore**:
    *   The original configuration files (backed up as `.hspt`) are restored.

3.  **Service Disablement**:
    *   `hostapd` and `isc-dhcp-server` are disabled.

4.  **Reboot**:
    *   The device reboots to return to the standard state.

## Wi-Fi Console (wconsole) Integration

The Hotspot switcher includes built-in support for **wconsole** functionality. This feature allows users to access serial console ports (via USB-to-Serial adapters connected to the WLAN Pi) over the Wi-Fi Hotspot network using `ser2net`.

### How to Enable wconsole

By default, the wconsole functionality is disabled in the `hotspot_switcher` script. To enable it:

1.  Open the `hotspot_switcher` script (located at `/usr/sbin/hotspot_switcher` on the device or in the repository source).
2.  Locate the `WCONSOLE` variable at the top of the script.
3.  Change the value from `false` to `true`.

```bash
# Change to true to enable wconsole functionality.
WCONSOLE=true
```

### What Happens When Enabled?

When `WCONSOLE` is set to `true`:

*   **Dependency Check**: The script checks if the `ser2net` service is installed.
*   **Configuration**: It links the wconsole-specific `ser2net.conf` (from `/etc/wlanpi-wconsole/conf/ser2net.conf`) to `/etc/ser2net.conf`.
*   **Firewall**: It opens specific TCP ports in the firewall (UFW) to allow incoming connections to the serial streams.
    *   Ports allowed: 2400-2408, 4800-4808, 9600-9608, 19200-19208, 38400-38408, 11520-11528, 2000-2008.
*   **Services**: The `ser2net` service is enabled to start on boot.