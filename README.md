# Energy Manager Integration
### https://energymanager.com.au
The following are instructions on how to integrate Energy Manager with an existing Home Assistant.

> [!CAUTION]
> Before starting on updating your Home Assistant to support Energy Manager, **make a backup of your system**. Download this backup to your local computer and confirm you have it before progressing. If you make a mistake with the below process, you may need to start again.  Make sure your backup contains all your entity history.

> [!TIP]
> The preferred method of installing Energy Manager is a restore to a freshly deployed Home Assistant, but there may be cases when you already have an existing Home Assistant instance and you don't want to dedicate another Home Assistant for your energy management.

> [!NOTE]
> These instructions are a work-in-progress and may contain mistakes or missing content. This is a retrospective installation guide, installing Energy Manager in a way that it wasn't intended, so as yet these instructions may be incomplete.

## Install the required integrations and add-ons
1) Install Advanced SSH & Web Terminal
2) Install Mosquitto broker
3) Install Node-RED
4) Install Studio Code Server
5) Install HACS and components
  - Install HACS (you will need to authorise it through github)
  - Install apexcharts-card
  - Install card-mod
  - Install layout-card
  - Install Node-RED Companion
  - Install state-switch
  - Install Solcast PV Forecast
  - Install Bureau of Meteorology
  - Install Modern Circular Gauge
  - Install Platinum Weather Card
  - Install Tabbed Card

## Configure some integrations
> [!NOTE]
> Refer to the main website for configurations instructions - work through these instructions at the same time as the website instructions.
From Settings -> Devices, perform the following:
1) Enter details for Bureau of Meteorology (your coordinates, important)
  - Obtain your latitude & longitude from https://www.latlong.net/convert-address-to-lat-long.html
  - Devices & services -> Bureau of Meteorology -> Cog -> enter your latitude & longitude
  - Make sure you name the entity **weather.home**, and your basename **home**. This is **very important** as Energy Manager will refer to specific entity names. Check on the main website for details on how to configure BOM as it needs to be configured correctly (https://energymanager.com.au/usersc/step-four-bom.php).
3) MQTT -> Cog -> Configure MQTT options -> **DISABLE** "Enable discovery", keep "Enable birth message" enabled.
4) Solcast -> setup your Solcast API key and site IDs (note, you will need to have signed up with Solcast after following the main website instructions on the process). Refer to https://energymanager.com.au/usersc/step-four-pv-solcast.php for more details.

## Create the following helpers
1) input_select.inverter_brand
- name: inverter_brand
- icon: mdi:form-select
- Options:
  - sungrow
  - sigenergy
  - alphaess
  - fronius

Note: The brand names are **case-sensitive**. Do not put upper-case first letters on them.

## Download the appropriate Energy Manager files
**From home assistant**, download the following tar file as follows:
```bash
wget https://github.com/ha-energymanager/energy-manager-integration/blob/main/em-v023.tar
```

## Extract the files to a temporary location on Home Assistant
We will extract these files in a temporary location as they risk overwriting your existing files, since some will be named the same thing.
```bash
cd /tmp
tar -xf em-v023.tar
```

## Move the files to their appropriate location
> [!NOTE]
>  Some of these files will already exist in your existing Home Assistant instance. It is **extrememly important** to merge the contents **carefully**. Energy Manager is designed to be able to be updated at regular intervals and should you not follow these instructions, things **will** break.
> - Copy your customised content to the **end** of the file, **after** the line that is similar to **# === ENERGY_MANAGER_UPDATE_INPUTS_END ===**
> - **NEVER** add any content between the two section identifiers. If you do, it **will be deleted** on the next update without warning:
>   \# === ENERGY_MANAGER_SCRIPTS_START v0.2.3 === and the closing \# === ENERGY_MANAGER_SCRIPTS_END ===
> - **ALWAYS** add your personalised content **after** the conclusion of the Energy Manager content. This is **extremely** important.
> - If you have references to other script, template, automation, automation, utility meter or group files in your **configuration.yaml**, you will need to **delete them** and move the contents of those files into the Energy Manager version of them, **after** the Energy Manager content.  Home Assistant **cannot** cope with multiple yaml files for scripts, templates, or automations, etc (they have to be located in a single place) if you are referencing an additional file.  For example, you cannot add a script inside both the configuration.yaml file and a scripts.yaml file, it can only be in the one place or else the other content will be ignored.
> - If you have scripts, templates, automations, groups, utility meters, modbus entities **inside** your configuration.yaml files, **these must be moved out and placed in the appropriate Energy Manager supplied file**.
> - If you have a **modbus.yaml** file, you must use the Energy Manager version (which is copied from one of the "disabled" inverter brand files). The appropriate one of these disable inverter brand files **will be copied across to modbus.yaml so will delete any existing modbus.yaml file**. If you have a custom modbus.yaml file, it will be replaced by Home Assistant when the inverter brand input_select box is changed.

> [!CAUTION]
> The node-RED flows will **wipe over any existing flows** that you may have. Even if you add them to different tabs, they will wipe over your existing flows on an update. A fix for this will be released shortly in an upcoming update.

```bash
mv {filename} {destination}
e.g.
mv configuration.yaml /config
```
**Important: This will overwrite existing files, be warned! Make sure you have a backup before progressing.**
- configuration.yaml to /config
- automations.yaml to /config
- group.yaml to /config
- scripts.yaml to /config
- template.yaml to /config
- utility_meter.yaml to /config
- alphaess_modbus.yaml.disabled to /config
- fronius_modbus.yaml.disabled to /config
- sigenergy_modbus.yaml.disabled to /config
- sungrow_modbus.yaml.disabled to /config
- energy_manager_costs.yaml to /config/dashboards
- energy_manager_overview.yaml to /config/dashboards
- energy_manager_pv.yaml to /config/dashboards
- mqtt_sensors.yaml to /config/integrations
- flows.json to /addon_configs/a0d7b954_nodered
- check_energy_manager_updates.py to /config/scripts
- entity_manager.py to /config/scripts
- file_merger.py to /config/scripts
- main_update_system.py to /config/scripts
- perform_energy_manager_update.py to /config/scripts
- update_checker.py to /config/scripts
- energy-manager-settings.svg to /config/www

## Configure your secrets.yaml file
Place the following code in your /config/secrets.yaml file:
```bash
# Use this file to store secrets like usernames and passwords.
# Learn more at https://www.home-assistant.io/docs/configuration/secrets/
modbus_host_ip: x.x.x.x # Update with the IP of your inverter. No default. Check the IP of your inverter!
modbus_port: 502  # Update with the Modbus port of your inverter. Default is '502'
modbus_slave: 1  # Update with the slave address of your inverter. Default is '1'
```
## Remove any other modbus integrations
Energy Manager works on Home Assistant's built in modbus integration. Inverter modbus isn't generally designed to be polled too frequently, and if it is, data will be missed. Ideally you should remove any other modbus related integrations so that modbus can answer in a timely manner when it is queried or written to.

## Restart Home Assistant
Go to **Developer tools -> YAML -> Check configuration** before you restart Home Assistant. If you get some warnings about modbus/baud rate, etc, that should be fine, but watch out for any critical errors that may stop Home Assistant from restarting. If any are shown, don't restart, but fix the issues first.

Once all of the above has been completed, restart Home Assistant. Once back, pay close attention to what errors occur in the system logs.

You should see some new menu items:
- PV
  - Tabs: Home, Battery, Cog, Updates
- Energy Overview
  - Tabs: Dashboard, History
- Costs
  - Tabs: Single tab containing costs and usage details

 ## Continue with configuration as per the website
 If you have got this far and the new menus & dashboards have populated without any missing entities, you should continue with the configuration steps as per the website:
 - Configure updates: https://energymanager.com.au/usersc/step-four-update.php (ensure you configure SSH as per the website instructions)
 - Energy settings: https://energymanager.com.au/usersc/step-four-pv-energy.php
 - Electricity provider: https://energymanager.com.au/usersc/step-four-pv-provider.php
 - Solcast: https://energymanager.com.au/usersc/step-four-pv-solcast.php
 - Notifications: https://energymanager.com.au/usersc/step-four-pv-notifications.php

If your Home Assistant configuration is playing up, or there are missing entities, please let us know at https://discord.gg/yeVxvmADKn or log a support ticket on the website.


