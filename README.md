Layer-Based AUX Fan Control for Bambu Lab P1S/X1C Printers
==========================================================

Control the AUX fan speed of a Bambu Lab P1S/X1C 3D printer based on the current layer of your print job using Home Assistant.

Overview
--------

-   **Adjust AUX fan speed** based on the current printing layer.
-   **Supports layer ranges** with specified percentages.
-   **Easy to configure and customize** through Home Assistant's UI.

Requirements
------------

-   **[Home Assistant](https://www.home-assistant.io/)** with the [Bambu Lab integration](https://github.com/greghesp/ha-bambulab) installed.
-   A **Bambu Lab P1S/X1C 3D printer** connected to Home Assistant.
-   Entities for:
    -   **Current Layer Sensor** (e.g., `sensor.p1s_current_layer`).
    -   **AUX Fan Control** (e.g., `fan.p1s_aux_fan`).
    -   **AUX Fan Speed Sensor** (e.g., `sensor.p1s_aux_fan_speed`).

Installation
------------

### Importing the Blueprint

1.  **Import the Blueprint URL:**

    Click the button below to import the Layer-Based AUX Fan Control blueprint:

    [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/christabone/aux_fan_by_layer/blob/main/aux_fan_by_layer.yaml)

2.  **In Home Assistant:**

    -   Go to **Settings** > **Automations & Scenes**.
    -   Click on the **Blueprints** tab.
    -   Click **"Import Blueprint"**.
    -   Paste the Blueprint URL into the field.
    -   Click **"Preview Blueprint"**, then **"Import Blueprint"**.

Configuration
-------------

1.  **Create a New Automation:**

    -   Go to **Settings** > **Automations & Scenes**.
    -   Click **"Create Automation"**.
    -   Select **"Start with a Blueprint"**.
    -   Choose **"Layer-Based AUX Fan Control"** from the list.
2.  **Fill in the Inputs:**

    -   **Printer Current Layer Sensor Entity:**
        -   Select your printer's current layer sensor (e.g., `sensor.p1s_current_layer`).
    -   **Printer AUX Fan Control Entity:**
        -   Select your printer's AUX fan control entity (e.g., `fan.p1s_aux_fan`).
    -   **Printer AUX Fan Speed Sensor Entity:**
        -   Select your printer's AUX fan speed sensor (e.g., `sensor.p1s_aux_fan_speed`).
    -   **Fan Speed Settings:**
        -   Input your fan speed settings using the specified format.
        -   **Example:**

            `{"246-250": "10", "251-252": "20", "253": "10"}`

        -   **Note:** Ensure that each layer range is a **unique key** to prevent conflicts.

3.  **Save and Enable the Automation:**

    -   Click **"Save"** to create the automation.
    -   Ensure the automation is **enabled**.

Fan Speed Settings Format
-------------------------

### **Supported Formats**

The `fan_speed_settings` input accepts a JSON string that maps **layer ranges** to **fan speed percentages**. This structure ensures unique layer range keys and avoids duplicate entries.

-   **Format:**

    `{"layer_start-layer_end": "percentage", ...}`

    -   **layer_start-layer_end:** Layer range (use a single number to keep the fan on until the end of the print).
    -   **percentage:** Fan speed percentage (must be in 10% increments, *e.g.*, 10, 20, ..., 100).
-   **Example:**

    `{"246-250": "10", "251-252": "20", "253": "10"}`

### **Layer Range Definitions**

-   **Single Layer:**

    -   **Format:** `"layer": "percentage"`
    -   **Example:** `"253": "10"`
    -   **Behavior:** Fan runs at `10%` from layer `253` onwards.
-   **Layer Range:**

    -   **Format:** `"layer_start-layer_end": "percentage"`
    -   **Example:** `"246-250": "10"`
    -   **Behavior:** Fan runs at `10%` between layers `246` and `250` inclusive.

### **Examples**

-   **Example 1:**

    `{"246-250": "10", "251-252": "20", "253": "10"}`

    -   **Layers 246-250:** Fan runs at `10%`.
    -   **Layers 251-252:** Fan runs at `20%`.
    -   **Layer 253:** Fan runs at `10%` onwards.
-   **Example 2:**

    `{"50-130": "10", "135-145": "20", "200": "10"}`

    -   **Layers 50-130:** Fan runs at `10%`.
    -   **Layers 135-145:** Fan runs at `20%`.
    -   **Layer 200:** Fan runs at `10%` onwards.
-   **Example 3:**

    `{"150": "10"}`

    -   **Layers 150 and above:** Fan runs at `10%`.
    -   **Layers below 150:** Fan is **OFF**.

### **Important Notes:**

-   **Unique Keys:** Ensure that each layer range is a unique key in the JSON to prevent overwriting settings.
-   **10% Increments:** Percentages must be in 10% increments to comply with the AUX fan's operational requirements.
-   **No Overlaps:** Avoid overlapping layer ranges to ensure predictable fan behavior.

How It Works
------------

-   **Layer-Based Control:**

    -   The automation monitors the current layer of the print job via the `printer_layer_sensor`.
    -   Based on the `fan_speed_settings`, it determines the appropriate fan speed percentage and sets the variable `current_percentage`.
    -   If the current layer falls within a defined range, the fan speed is set accordingly.
    -   Percentages are rounded to the nearest 10 to adhere to the fan's requirements.
-   **Fan Control Logic:**

    -   **When `current_percentage > 0`:**
        -   If the fan is **OFF**, it turns **ON**.
        -   Sets the fan speed to `current_percentage`.
    -   **When `current_percentage == 0`:**
        -   If the fan is **ON**, it turns **OFF**.
-   **Optimized Commands:**

    -   The automation checks the current fan speed before sending commands.
    -   Reduces unnecessary commands to the printer.

Notes and Tips
--------------

-   **Input Validation:**

    -   Ensure your `fan_speed_settings` JSON is correctly formatted.
    -   Use online JSON validators (e.g., [JSONLint](https://jsonlint.com/)) to verify your JSON structure before inputting it into Home Assistant.

Troubleshooting
---------------

-   **Fan Not Adjusting as Expected:**

    -   **Check Sensor Entities:**
        -   Verify that `printer_layer_sensor` correctly reports the current layer.
        -   Ensure that `printer_fan_speed_sensor` accurately reflects the fan's current speed.
    -   **Validate JSON Configuration:**
        -   Use a JSON validator to ensure `fan_speed_settings` is correctly formatted.
        -   Ensure there are no duplicate keys in the JSON.
    -   **Check Automation Logs:**
        -   Navigate to **Settings > System > Logs** in Home Assistant.
        -   Look for any errors or debug messages related to the AUX Fan Control automation.
-   **Fan Turns Off Unexpectedly:**
    -   **Review Layer Ranges:**
        -   Confirm that there are no overlapping or conflicting layer ranges.

-   **Automation Not Triggering:**

    -   **Check Triggers:**
        -   Ensure that the `printer_layer_sensor` is correctly set up and updates as the print progresses.
        -   Verify that the automation is **enabled**.
    -   **Entity Names:**
        -   Confirm that the entity IDs in the blueprint inputs match the actual entities in your Home Assistant setup.

Contribution
------------

-   **Feedback and Improvements:**
    -   Contributions are welcome! If you have suggestions or improvements, feel free to open an issue or submit a pull request on GitHub.

License
-------

This project is licensed under the MIT License.

Acknowledgments
---------------

-   Thanks to the Home Assistant community for the support and inspiration.
-   Special thanks to users who helped refine this blueprint.