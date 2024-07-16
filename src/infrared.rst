.. _infrared:

Infrared
========

Overview
--------

I use Infrared signals to:

- change profile of the :ref:`Retrotink4K <retrotink_4k>`
- change the input of the :ref:`HDMI switch <hdmi_switch>`

This is how the whole process looks like:

.. graphviz:: graphs/infrared_sequence.dot


This is how the whole setup looks like:

.. figure:: _static/infrared/arduino.jpg
  :alt: Arduino with raspberry pi

  This image:
  1. Raspberry pi Zero 2W -- 2. USB Hub -- 3. Arduino Nano Every with board. There's one cable going to the Retrotink4K and another one going to the HDMI switch.



.. figure:: _static/keypad.jpg
   :alt: Keypad
   :align: center

   This image: an IR Led just above the keypad pointing at the Retrotink4K.

WIP

.. code-block:: yaml

    substitutions:
      name: "esphome-retro-ir-nano"
      friendly_name: ESPHome Retro IR Nano

    esphome:
      name: ${name}
      friendly_name: ${friendly_name}
      name_add_mac_suffix: false
      platformio_options:
        board_build.flash_mode: dio
      project:
        name: esphome.web
        version: '1.0'

    esp32:
      board: esp32-s3-devkitc-1
      framework:
        #type: arduino
        type: esp-idf


    # Enable logging
    logger:

    # Enable Home Assistant API
    api:
      encryption:
        key: !secret esphome_encryption_key

    # Allow Over-The-Air updates
    ota:
      platform: esphome

    # Allow provisioning Wi-Fi via serial
    improv_serial:

    wifi:
      # Set up a wifi access point
      ap: {}

    # In combination with the `ap` this allows the user
    # to provision wifi credentials to the device via WiFi AP.
    captive_portal:

    dashboard_import:
      package_import_url: github://esphome/firmware/esphome-web/esp32s3.yaml@v2
      import_full_config: true

    # Sets up Bluetooth LE (Only on ESP32) to allow the user
    # to provision wifi credentials to the device.
    esp32_improv:
      authorizer: none

    # To have a "next url" for improv serial
    #web_server:


    remote_transmitter:
      pin:
        number: GPIO06
      carrier_duty_percent: 50%


    button:
      - platform: template
        name: Switch HDMI_1
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0x1
              command: 0xE41B

      - platform: template
        name: Switch HDMI_2
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0x1
              command: 0xE11E

      - platform: template
        name: Switch HDMI_3
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0x1
              command: 0xF20D

      - platform: template
        name: Switch HDMI_4
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0x1
              command: 0xED12

      - platform: template
        name: Switch HDMI_5
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0x1
              command: 0xEF10

      # https://consolemods.org/wiki/AV:RetroTINK-4K#Remote
      - platform: template
        name: RetroTINK 4k Profile 1
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xF40B
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 2
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xF807
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 3
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xFC03
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 4
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xF50A
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 5
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xF906
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 6
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xFD02
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 7
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xF609
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 8
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xFA05
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 9
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xFE01
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 10
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xDA25
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 11
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xD926
              repeat:
                times: 2
                wait_time: 100ms

      - platform: template
        name: RetroTINK 4k Profile 12
        on_press:
          - remote_transmitter.transmit_nec:
              address: 0xB649
              command: 0xD827
              repeat:
                times: 2
                wait_time: 100ms













Home Assistant MQTT
-------------------

Prerequisites
^^^^^^^^^^^^^

Setup :ref:`Home Assistant's MQTT Broker<homeassistant_mqtt>`.

Automation
^^^^^^^^^^

Create an automation to send the nec codes to :ref:`mqtt_to_nec`.

The codes match `mqtt2nec's config.csv <https://github.com/jrobichaud/mqtt2nec/blob/main/config.csv>`_. You can also send codes as hex strings.

The first value is common for the device and the next ones are the actual code you want to send.

.. code-block:: yaml

    service: mqtt.publish
    data:
      topic: nec/tx
      payload: "{\"codes\":  [  \"TINK4K\", \"TINK4K_1\" ]}"



.. _mqtt_to_nec:

mqtt2nec
--------

Python program interfacing Home assistant with the arduino. It is installed on the Raspberry pi and runs as a service.

`sources <https://github.com/jrobichaud/mqtt2nec>`_

Installing the program
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    git clone git@github.com:jrobichaud/mqtt2nec.git
    cd mqtt2nec
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt

Running the program
^^^^^^^^^^^^^^^^^^^

Make sure to change the arguments to match your mqtt broker configuration.

.. code-block:: bash

    python3 -m "mqtt2nec" "<home assistant url>" -u "<mqtt user>" -p "<mqtt password>" -a "./config.csv"



Service configuration
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: ini

    [Unit]
    Description=mqtt2nec
    Documentation=
    After=network.target

    [Service]
    Type=simple
    User=retro
    ExecStart=/usr/bin/python3 -m "mqtt2nec" "<home assistant url>" -u "<mqtt user>" -p "<mqtt password>" -a "/home/retro/mqtt2nec/config.csv"
    Restart=always
    MemorySwapMax=0

    [Install]
    WantedBy=multi-user.target


Arduino
-------

I used this kit to prototype: `Basic Kit for Arduino <https://www.canakit.com/arduino-starter-kit.html>`_

I use the `Arduino Nano Every <https://store-usa.arduino.cc/products/arduino-nano-every>`_ on my setup.

Circuit
^^^^^^^

I followed `adafruit's "sending ir codes" tutorial <https://learn.adafruit.com/using-an-infrared-library/sending-ir-codes>`_ to build the circuit.

I used these :ref:`ir_leds`.

infrared-nec (Arduino program)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The program to install on the arduino: `infrared-nec <https://github.com/jrobichaud/infrared-nec>`_

This is a custom programming that communicates with the mqtt2nec program. It was heavily inspired by `adafruit's "sending ir codes" tutorial <https://learn.adafruit.com/using-an-infrared-library/sending-ir-codes>`_.


Capturing infrared codes (optional)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

I used the circuit `described here <https://learn.adafruit.com/using-an-infrared-library/hardware-needed>`_ but I used this old source to capture the codes: `MinimalReceiver.ino <https://raw.githubusercontent.com/Arduino-IRremote/Arduino-IRremote/922d2c5c81c9057b2dbf6b1772c6f3195ec6ef85/examples/MinimalReceiver/MinimalReceiver.ino>`_.
