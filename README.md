# Energy Manager Integration
### https://energymanager.com.au
## This is a work in progress, as at 2/09/25 it will not work.
The following are instructions on how to integrate Energy Manager with an existing Home Assistant.

> [!CAUTION]
> Before starting on updating your Home Assistant to support Energy Manager, **make a backup of your system**. Download this backup to your local computer and confirm you have it before progressing. If you make a mistake with the below process, you may need to start again.  Make sure your backup contains all your entity history.

> [!TIP]
> The preferred method of installing Energy Manager is a restore to a freshly deployed Home Assistant, but there may be cases when you already have an existing Home Assistant instance and you don't want to dedicate another Home Assistant for your energy management.

> [!NOTE]
> These instructions are a work-in-progress and may contain mistakes or missing content. This is a retrospective installation guide, installing Energy Manager in a way that it wasn't intended, so as yet these instructions may be incomplete.

## Install the required integrations and add-ons
1) From Add-ons:
  - Install Advanced SSH & Web Terminal, and start it
  - Install Mosquitto broker, and start it
  - Install Node-RED, go into configuration settings of it and disable SSL, and then start it
  - Install Studio Code Server, and start it and show in sidebar
2) From Integrations, install HACS
  - Browse to [HACS website](https://www.hacs.xyz/docs/use/download/download/#to-download-hacs)
  - Follow the link in step 1 ("**my link**")
  - Enter in your Home Assistant address, if different from that shown
  - Open the link when shown
  - Add the **Missing add-on repository**
  - From the **Get HACS** page, click on **install**
  - Start the add-on
  - Click on **Log** up the top of the page
  - Confirm you see the line "INFO: Installation complete"
  - Restart Home Assistant
  - Go to Settings -> Devices & services -> Integrations
  - Click on **Add integration**
  - Search for "HACS" and select it
  - Tick all four boxes and submit
  - Follow the link to Github, copying and pasting in your device activation key
  - Authorise HACS
  - Back on Home Assistant, select **Skip and finish**
3) From HACs (left menu), install:
  - Install apexcharts-card (download and reload)
  - Install card-mod (download and reload)
  - Install layout-card (download and reload)
  - Install Node-RED Companion 
  - Install state-switch (download and reload)
  - Install Modern Circular Gauge (download and reload)
  - Install Platinum Weather Card (download and reload)
  - Install Tabbed Card (download and reload)
  - Install Solcast PV Forecast
  - Install Bureau of Meteorology

**Restart Home Assistant**

## Configure some integrations
> [!NOTE]
> Refer to the main website for configurations instructions - work through these instructions at the same time as the website instructions.

### Preparation work
1) Setup your Solcast API key and site IDs (note, you will need to have signed up with Solcast after following the main website instructions on the process). Refer to     https://energymanager.com.au/usersc/step-four-pv-solcast.php for more details.
2) Obtain your latitude & longitude from https://www.latlong.net/convert-address-to-lat-long.html
   
### Install and configure integrations
1) From Integrations (Settings -> Devices & services -> Integrations -> + Add integration)
  a) Install Solcast PV Forecast
    - Add your Solcast API key
    - Click on the top 3-dot menu of the **Services** area (to the right of "Solcast Solar" service) and select **1 service**
    - Under **sensors** you will see 6 disabled entities. Enable each of them (click on each one, select the cog, and then select **enable**), except "Forecast Next X hours".
  b) Install Bureau of Meteorology (you need your coordinates for this step)
  Make sure you name the entity **weather.home** (so enter strictly **home**), and your basename **home**. This is **very important** as Energy Manager will refer to specific entity names. Check on the main website for details on how to configure BOM as it needs to be configured correctly (https://energymanager.com.au/usersc/step-four-bom.php). You will run into all sorts of trouble if you do not name your weather entities **home**.
  c) Install MQTT integration (when it asks "What do you want to add?", answer "MQTT" (upper-case)). When asked, select "Use the official Mosquitto Mqtt Broke add-on.", and then **Finish**. Click on the **Cog** and then select **Configure MQTT options** at the top. **DISABLE** "Enable discovery" and keep "Enable birth message" enabled. Select submit.
  d) Install Node-RED Companion (just "Skip and Finish" at the end)

**RESTART HOME ASSISTANT**

## Create the following helpers
1) input_select.inverter_brand
    - type: dropdown
    - name: Inverter Brand
    - icon: mdi:form-select
    - options:
      - sungrow
      - sigenergy
      - alphaess
      - fronius

Note: The brand names are **case-sensitive**. Do not put upper-case first letters on them.

2) input_text.amber_api_key
    - type: text
    - name: Amber API Key
    - icon: mdi:shield-key-outline
    - minimum length: 36
    - maximum length: 36

3) input_text.amber_site
    - type: text
    - name: Amber Site
    - icon: mdi:shield-key-outline

4) input_text.localvolts_key
    - type: text
    - name: LocalVolts Key
    - icon: mdi:shield-key-outline

5) input_text.localvolts_partner_id
    - type: text
    - name: LocalVolts Partner ID
    - icon: mdi:shield-key-outline

6) input_text.nmi
    - type: text
    - name: NMI
    - icon: mdi:numeric

7) input_select.electricity_provider
    - type: dropdown
    - name: Electricity Provider
    - icon: mdi:lightning-bolt
    - options:
      - LocalVolts
      - Amber Electric

Note: The options are **case-sensitive**

8) input_date.battery_install_date
     - type: data and/or time
     - name: Battery Install Date
     - icon: mdi:home-battery
     - option: date
  
9) input_number.inverter_import_limit
    - type: number
    - name: Inverter Size
    - icon: mdi:home-import-outline
    - minimum: 0
    - maximum: 25
    - step size: 0.1
    - display mode: input field

Note: The name and entity name are not the same.

10) input_number.solar_array_size
    - type: number
    - name: Solar Array Size
    - icon: mdi:solar-panel
    - minimum: 1
    - maximum: 25
    - display mode: input field
    - step size: 0.1
    - unit of measurement: kW
   
11) input_text.energymanager_key
    - type: text
    - name: EnergyManager Key
    - icon: mdi:shield-key-outline

13) input_text.em_api_version
    - type: text
    - name: EM API Version
    - icon: mdi:api
    - minimum: 0
    - maximim: 16
   
14) input_text.timezone
    - type: text
    - name: Timezone
    - icon: mdi:map-clock
    - minimum: 4
    - maximum: 4
   
15) input_boolean.demand_period_enabled
    - type: toggle
    - name: Demand Period Enabled
    - icon: mdi:clock-time-five-outline
   
16) input_datetime.demand_start
    - type: date and/or time
    - name: Demand Start
    - icon: mdi:clock-time-five-outline
    - option: time

17) input_datetime.demand_end
    - type: date and/or time
    - name: Demand End
    - icon: mdi:clock-time-nine-outline
    - option: time
   
18) input_boolean.enable_exclusion_period
    - type: toggle
    - name: Enable Exclusion Period
    - icon: mdi:clock-outline

19) input_datetime.exclusion_start_time
    - type: date and/or time
    - name: Exclusion start time
    - icon: mdi:clock-time-two-outline
    - option: time

20) input_datetime.exclusion_end_time
    - type: date and/or time
    - name: Exclusion end time
    - icon: mdi:clock-time-three-outline
    - option: time

21) input_text.solcast_key
    - type: text
    - name: solcast_key
    - icon: mdi:shield-key-outline
   
22) input_text.solcast_array_1
    - type: text
    - name: solcast_array_1
    - icon: mdi:shield-key-outline
   
23) input_text.solcast_array_2
    - type: text
    - name: solcast_array_2
    - icon: mdi:shield-key-outline
    - 
24) input_number.inverter_export_limit
    - type: number
    - name: Inverter Export Limit
    - icon: mdi:home-export-outline
    - minimum: 0
    - maximum: 15
    - step size: 0.1

25) number.battery_2a
    - type: template number
    - state: {{ (states('input_number.battery_1') | float) - 1}}
    - name: battery_2a

25) number.battery_3a
    - type: template number
    - state: {{ (states('input_number.battery_2') | float) - 1}}
    - name: battery_3a

25) number.battery_4a
    - type: template number
    - state: {{ (states('input_number.battery_3') | float) - 1}}
    - name: battery_4a

25) number.battery_5a
    - type: template number
    - state: {{ (states('input_number.battery_4') | float) - 1}}
    - name: battery_5a

25) number.battery_6a
    - type: template number
    - state: {{ (states('input_number.battery_5') | float) - 1}}
    - name: battery_6a

26) input_number.bad_weather_rain_today
    - type: number
    - name: bad_weather_rain_today
    - icon: mdi:weather-rainy

27) input_number.bad_weather_rain_tomorrow
    - type: number
    - name: bad_weather_rain_tomorrow
    - icon: mdi:weather-rainy

30) number.battery_high_sell_mode_usable
    - type: template number
    - state: {{ 100 - ((states('input_number.high_sell_battery_reserve')) | float)}}
    - minimum: 0
    - maximim: 100
    - step value: 1

31) input_boolean.high_sell_price_toggle
    - type: toggle
    - name: high_sell_price_toggle
    - icon: mdi:toggle-switch
   
## Launch Node-RED
Lauch node-RED (from Settings -> Add-ons -> Open Web UI.
We need to install some components to Node-RED (close any prompts at this stage):

1) Click on the hamburger menu, top right
2) Select **Manage Palette**
3) Install the following components:
   - node-red-contrib-traffic
   - node-red-contrib-time-switch
   - node-red-contrib-sun-position
   - node-red-contrib-sunevents
   - node-red-contrib-home-assistant-websocket
   - node-red-contrib-counter
   - node-red-node-random
   - node-red-node-suncalc

Note: There may be a number of warnings at the top right of the screen. This is because not all sensors are created yet.

## Configure Electricity Provider and Solcast secrets
1) From the PV menu, click on the **cog**
2) Add in your electricity provider secrets
3) Add in your solcast secrets (key plus at least one site code)
4) Restart node-RED (Settings -> Add-ons)
5) Add your Solcast API key to the Solcast integration (Settings -> Devices & services -> Solcast (if not done already)

## Download the appropriate Energy Manager files
**From home assistant**, download the following tar file as follows (through **Advanced SSH & Web Terminal** (start it if it is stopped, make sure that **Start on boot** is enabled)):
Note: If you can't start SSH and receive a "502: Bad Gateway", go into the configuration settings of the addon and add a password, then try again. 
```bash
cd /tmp
wget https://updates.energymanager.com.au/em-v023.tar
```

## Extract the files to a temporary location on Home Assistant
We will extract these files in a temporary location as they risk overwriting your existing files, since some will be named the same thing.
```bash
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

**IMPORTANT: Copy any of your personal/original files that are duplicates with the Energy Manager files to a separate location. Once we have copied the Energy Manager versions across, we will then need to **carefully** copy any content that you need to retain across to the Energy Manager versions.

Make the following directories:
```bash
cd /config
mkdir dashboards
mkdir integrations
mkdir scripts
cd /tmp
```

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

## Prepare modbus.yaml
Go into your /config directory and copy the appropriate brand "disabled" file to modbus.yaml. The following is an example for Sungrow:
```bash
cd /config
cp sungrow_modbus.yaml.disabled modbus.yaml
```

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

## Node-RED sensor update
1) Settings -> Devices & services -> Node-RED Companion -> Electricity Provider (select) -> Click on "Forecast" -> Cog -> Rename sensor to sensor.nodered_forecast -> Update
2) Rename "Sell Price" sensor to sensor.nodered_sellprice
3) Rename "Buy Price" sensor to sensor.nodered_buyprice

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

## Disable Energy Control
> [!IMPORTANT]
> You must disable energy control so that Energy Manager does not take control yet, as not everything is configured.

Browse to your PV Dashboard -> Ensure that **Energy Control** (down the bottom left) is NOT orange. If it is orange (aka enabled), toggle it so that it is disabled.

## Update the Energy Manager entity values
Because we have manually added a lot of entities, they will default to no value.  We need to make sure that the settings contain something meaningful.
1) Browse to PV dashboard -> Cog tab. Enter all your unique details, as per https://energymanager.com.au/usersc/step-four-pv-energy.php
2) Browse to PV dashboard -> Home tab. Enter appropriate values for the following (suggestions next to the item, but you can change them to what you want):
   - Minimum SoC: 20%
   - Maximum SoC: somewher between 80-100%
   - Reserved SOC for Backup: 0%
   - Forced Charge/Discharge Power: 5000
   - Max Battery Charge Power: 5000
   - Max Battery Discharge Power: 5000
   - Safety Limit - Max Charge Power: 5500
   - Safety Limit - Max Discharge Power: 5500
   - Export Power Limit: 5000
3) Browse to PV dashbard -> battery tab. Enter appropriate values for the following (suggestions next to the item, but you can change them to what you want):
  - In the **Spike Preparation and Buy Pricing** image (left to right):
    - Charge to: 40%
      - Reserve: 20%
      - Max charging price if morning spike detected: 25
      - Spike defintion (on left): 45
      - Normal day time maximum charging price: 20c
      - Import from grid when under: 5
      - Battery charge prep for evening: 80-100% (your choice)
      - Spike definition (on right): 400
      - Maximum charging price if evening spike detected: 40
  - In the **Normal Mode** image
    - Set the battery levels (the left-most column in the battery) as follows:
      - 80
      - 65
      - 50
      - 40
      - 30
      - 20
    - Set the **Sell for** pricing, down the right hand side of the battery as follows:
      - 0.25
      - 0.30
      - 0.50
      - 1.00
      - 2.00
      - 3.00
    - Set the **High Sell Mode** as follows:
      - Sell for: 200
      - Reserver: 40%
  - In the **Weather Settings** image, enter the following:
    - Rain amount for bad weather - today: 12mm
    - Raim amount for bad weather - tomorrow: 20mm 

## Tidy up the Overview dashboard
By default Home Assistant adds a lot of entities to the **Overview** dashboard, whether you like it or not. This makes it very untidy and not how Energy Manager intended.  The strong recommendation is to manage the Overview dashboard and remove all the cards that have been placed there. Perform the following to tidy the dashboard up:
1) From the Overview dashboard, selec that **edit** pencil icon (top right)
2) From the **Edit dashboard** modal window, select **Take control** from the 3 dot menu at top right
3) Select either **Take control** or enable **Start with an empty dashboard** and then **Take control**, depending on what you want
4) Go through and delete all the cards

## Add in your own personal yaml settings
Add the contents of your personal yaml files into the Energy Manager version of the files located in /config. Pay **special attention** not to double-up anything and to make sure that you place your content **outside** of the Energy Manager section dividers. Failure to do this will mean that your settings will be **deleted** on the next update.  Keep a copy of your personalised files elsewhere so if after an update you realise that you failed to do this, you can at least get them back again.

## Set the system for your own inverter brand
Go to the PV dashboard and from the home screen of it, select the appropriate inverter brand. If it already states your brand, you can ignore this step. However, if you change it you must **restart Home Assistant** for the changes to take effect.

## Continue with configuration as per the website
If you have got this far and the new menus & dashboards have populated without any missing entities, you should continue with the configuration steps as per the website:
 - Configure updates: https://energymanager.com.au/usersc/step-four-update.php (ensure you configure SSH as per the website instructions)
 - Energy settings: https://energymanager.com.au/usersc/step-four-pv-energy.php
 - Electricity provider: https://energymanager.com.au/usersc/step-four-pv-provider.php
 - Solcast: https://energymanager.com.au/usersc/step-four-pv-solcast.php
 - Notifications: https://energymanager.com.au/usersc/step-four-pv-notifications.php

## Enable energy control
Once you have confirmed that the appropriate inverter readings are correct (PV dashboard -> home -> "Power" section), you should test out a manual power charge and discharge through the **EMS Control** section on the dashboard. Once the charge/discharge has been configured to work, you should enable the **energy control** toggle down the bottom left of the same dashboard tab.

If your Home Assistant configuration is playing up, or there are missing entities, please let us know at https://discord.gg/yeVxvmADKn or log a support ticket on the website.
