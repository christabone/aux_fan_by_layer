Layer-Based AUX Fan Control for Bambu Lab P1S/X1C Printers
==========================================================

Control the AUX fan speed of a Bambu Lab P1S/X1C 3D printer based on the current layer of your print job using Home Assistant.

Overview
--------

-   **Adjust AUX fan speed** based on the current printing layer.
-   **Optimized commands** by checking current fan speed before sending new commands.
-   **Supports single layers and layer ranges** with specified percentages.
-   **Default fan percentage** when no specific setting applies.
-   **Easy to configure and customize** through Home Assistant's UI.

Requirements
------------

-   **Home Assistant** with the Bambu Lab integration installed.
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

    ![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/christabone/aux_fan_by_layer/blob/main/aux_fan_by_layer.yaml)

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
        -   Example: `[50:190-210], [60:220-230], [150]`
    -   **Default Fan Percentage:**
        -   Set the default fan speed percentage (e.g., `70`).
        -   This percentage is used when no specific setting applies.
3.  **Save and Enable the Automation:**

    -   Click **"Save"** to create the automation.
    -   Ensure the automation is **enabled**.

Fan Speed Settings Format
-------------------------

### **Supported Formats**

-   **Single Layer without Percentage:**

    `[layer]`

    -   **Example:** `[150]`
    -   **Behavior:**
        -   Fan turns **ON** at the default percentage from layer 150 onwards.
        -   Fan is **OFF** before layer 150.
-   **Layer Range without Percentage:**

    `[layer_start-layer_end]`

    -   **Example:** `[190-210]`
    -   **Behavior:**
        -   Fan runs at the default percentage between layers 190 and 210.
        -   Fan is **OFF** outside this range.
-   **Single Layer with Percentage:**

    `[percentage:layer]`

    -   **Example:** `[50:190]`
    -   **Behavior:**
        -   Fan runs at **50%** from layer 190 onwards.
        -   Fan is **OFF** before layer 190.
-   **Layer Range with Percentage:**

    `[percentage:layer_start-layer_end]`

    -   **Example:** `[50:190-210]`
    -   **Behavior:**
        -   Fan runs at **50%** between layers 190 and 210.
        -   Fan is **OFF** outside this range.

### **Combining Multiple Settings**

-   **Separate multiple settings with commas.**

    -   **Example:** `[50:190-210], [60:220-230], [240]`

### **Examples**

-   **Example 1:**

    `[50:190-210]`

    -   **Layers 1-189:** Fan is **OFF**.
    -   **Layers 190-210:** Fan runs at **50%**.
    -   **Layers 211 and above:** Fan is **OFF**.
-   **Example 2:**

    `[45:80-120], [80:130-145], [60:150]`

    -   **Layers 1-79:** Fan is **OFF**.
    -   **Layers 80-120:** Fan runs at **45%**.
    -   **Layers 121-129:** Fan is **OFF**.
    -   **Layers 130-145:** Fan runs at **80%**.
    -   **Layers 146-149:** Fan is **OFF**.
    -   **Layers 150 and above:** Fan runs at **60%**.
-   **Example 3:**

    `[150]`

    -   **Layers 1-149:** Fan is **OFF**.
    -   **Layers 150 and above:** Fan runs at the **default percentage** (e.g., 70%).

### **Default Fan Percentage**

-   **Purpose:**
    -   Specifies the fan speed percentage when **no percentage is provided** in a setting.
    -   Used **only** when the current layer matches a setting without a specified percentage.
-   **Default Value:**
    -   `70` (can be adjusted during configuration).
-   **Fan is OFF when the current layer does not match any settings.**

How It Works
------------

-   **Optimized Command Sending:**

    -   The automation checks the current fan speed before sending commands.
    -   If the fan is already at the desired speed, no action is taken.
    -   Reduces unnecessary commands and potential wear on the device.
-   **Fan Control Logic:**

    -   **When the desired fan speed differs from the current speed:**
        -   If the desired speed is greater than `0`:
            -   If the fan is off, it turns on the fan.
            -   Sets the fan to the desired speed.
        -   If the desired speed is `0`:
            -   Turns off the fan if it's currently on.

Notes and Tips
--------------

-   **Input Validation:**

    -   Ensure your fan speed settings are correctly formatted.
    -   Incorrect formats may lead to unexpected behavior.
-   **Testing:**

    -   Test the automation with different layer values to ensure it behaves as expected.
    -   Use the **Template Editor** in Home Assistant for advanced testing of the parsing logic.
-   **Avoiding Unnecessary Commands:**

    -   By checking the current fan speed, the automation minimizes unnecessary commands, which can help prevent communication overload with the printer.

Troubleshooting
---------------

-   **Fan Not Adjusting as Expected:**

    -   Check that your `printer_layer_sensor` is reporting the correct layer.
    -   Verify that your `printer_fan_control` entity is functioning and supports percentage control.
    -   Ensure that the `printer_fan_speed_sensor` is correctly reporting the fan speed.
-   **Invalid Fan Speed Settings:**

    -   Ensure all settings are enclosed in square brackets `[ ]`.
    -   Separate brackets with commas `,`.
    -   Use colons `:` to separate percentages from layers.
    -   Use hyphens `-` to indicate layer ranges.

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