# Energy Manager Integration
The following are instructions on how to integrate Energy Manager with an existing Home Assistant.

> [!TIP]
> The preferred method of installing Energy Manager is a restore to a freshly deployed Home Assistant, but there may be cases when you already have an existing Home Assistant instance and you don't want to dedicate another Home Assistant for your energy management.

> [!NOTE]
> These instructions will start highlevel and will be fleshed out with detail over the coming weeks.

## Install the required integrations and add-ons
1) Install Advanced SSH & Web Terminal
2) Install Mosquitto broker
3) Install Node-RED
4) Install Studio Code Server
5) Install HACS and components
  - Instal HACS
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
2) MQTT -> Cog -> Configure MQTT options -> DISABLE "Enable discovery", keep "Enable birth message" enabled.
3) Solcat -> setup your Solcast API key

## Download the appropriate Energy Manager files
Download and store the following files from the Energy Manager update server:
- https://updates.energymanager.com.au/files/automations.yaml
- https://updates.energymanager.com.au/files/configuration.yaml
- https://updates.energymanager.com.au/files/group.yaml
- https://updates.energymanager.com.au/files/scripts.yaml
- https://updates.energymanager.com.au/files/template.yaml
- https://updates.energymanager.com.au/files/utility_meter.yaml
- https://updates.energymanager.com.au/files/alphaess_modbus.yaml.disabled
- https://updates.energymanager.com.au/files/fronius_modbus.yaml.disabled
- https://updates.energymanager.com.au/files/sigenergy_modbus.yaml.disabled
- https://updates.energymanager.com.au/files/sungrow_modbus.yaml.disabled
- https://updates.energymanager.com.au/files/dashboards/energy_manager_costs.yaml
- https://updates.energymanager.com.au/files/dashboards/energy_manager_overview.yaml
- https://updates.energymanager.com.au/files/dashboards/energy_manager_pv.yaml
- https://updates.energymanager.com.au/files/integrations/mqtt_sensors.yaml
- https://updates.energymanager.com.au/files/nodered/flows.json
- https://updates.energymanager.com.au/files/scripts/check_energy_manager_updates.py
- https://updates.energymanager.com.au/files/scripts/entity_manager.py
- https://updates.energymanager.com.au/files/scripts/file_merger.py
- https://updates.energymanager.com.au/files/scripts/main_update_system.py
- https://updates.energymanager.com.au/files/scripts/perform_energy_manager_update.py
- https://updates.energymanager.com.au/files/scripts/update_checker.py
- https://updates.energymanager.com.au/files/www/energy-manager-settings.svg
  
> [!IMPORTANT]
> It is important that when you copy these files that you use an application like Notepad++ that handles Linux hidden formatting appropriately. If you copy these files using the standard Windows Notepad, you may run into issues and failure of the scripts to run.

## Populate your /config directory with the downloaded scripts
Place the above files in the following locations:
> [!NOTE]
>  Some of these files will already exist in your existing Home Assistant instance. It is **extrememly important** to merge the contents **carefully**. Energy Manager is designed to be able to be updated at regular intervals and should you not follow these instructions, things **will** break.
> - Copy your customised content to the **end** of the file, **after** the line that is similar to **# === ENERGY_MANAGER_UPDATE_INPUTS_END ===**
> - **NEVER** add any content between the following two lines. If you do, it **will be deleted** on the next update:
>   - \# === ENERGY_MANAGER_CORE_START v0.0.15 === and the closing \# === ENERGY_MANAGER_UPDATE_INPUTS_END ===
>   **ALWAYS** add your personalised content **after** the conclusion of the Energy Manager content. This is **extremely** important.
>   - If you have references to other script, template, automation files in your **configuration.yaml**, you will need to **delete them** and move the contents of those files into the Energy Manager version of them, **after** the Energy Manager content.  Home Assistant **cannot** cope with multiple yaml files for scripts, templates, or automations, if you are referencing an additional file.
>   - If you have scripts, templates, automations, groups, utility meters, modbus **inside** your configuration.yaml files, **these must be moved out and placed in the appropriate Energy Manager supplied file**.
>   - If you have a **modbus.yaml** file, you must use the Energy Manager version (which is copied from one of the "disabled" inverter brand files). The appropriate one of these disable inverter brand files **will be copied across to modbus.yaml so will delete any existing modbus.yaml file**.

- configuration.yaml to /config
- automations.yaml to /config
- group.yaml to /config
- scripts.yaml to /config
- template.yaml to /config
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

> [!CAUTION]
> The node-RED flows will **wipe over any existing flows** that you may have. Even if you add them to different tabs, they will wipe over your existing flows on an update. A fix for this will be released shortly in an upcoming update.


