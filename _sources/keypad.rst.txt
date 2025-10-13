.. _keypad:

Keypad v2
=========

It started at first with a :ref:`very complicated contraption <keypad_v1>` because I could not find any bluetooth keyboard matrix.

Later, a friend of mine ended up building me a custom 4x4 keyboard using a `nice!nano v2 boards <https://nicekeyboards.com/nice-nano/>`_ with `zmk firmware <https://zmk.dev/>`_.


ZMK firmware
------------

`bluetooth version <https://github.com/jrobichaud/zmk-config>`_

`dongle version (the one I use) <https://github.com/jrobichaud/zmk-config/tree/dongle>`_. It requires an additional board but we do not need to mess with the bluetooth connection on the home assistant host.

To update the firmware I have to double tap the reset button then drag and drop the firmware file on the mounted device (like a usb stick).

Using the firmware ``settings_reset`` is very handy to reset the device settings.

Connecting the keyboard and identifying the device
--------------------------------------------------

(Only if not using a dongle) Connect the bluetooth keyboard on the host of home assistant
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We need to connect the keyboard to home assistant using ``bluetoothctl``.

.. code-block:: bash

    bluetoothctl
    agent on
    power on
    scan on  # wait for your device's address to show up here and note down the MAC address
    scan off
    trust <MAC_ADDRESS>
    pair <MAC_ADDRRESS>
    connect <MAC_ADDRESS>


Identifying the device name using
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   $ cat /proc/bus/input/devices | grep ZMK
   N: Name="ZMK Project Pacane Macro Bo Keyboard"


Home assistant configuration
----------------------------

configuration.yaml

.. code-block:: yaml

    keyboard_remote:
      #device_descriptor: /dev/input/event3
      device_name: 'ZMK Project Pacane Macro Bo Keyboard'
      type:
        - "key_down"
        #- "key_up"


Automation configurations
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

    - trigger: event
        event_type: keyboard_remote_command_received
        event_data:
          device_name: ZMK Project Pacane Macro Bo Keyboard
          key_code: 59
          type: key_down


Keycodes for F1 to F16
^^^^^^^^^^^^^^^^^^^^^^

.. csv-table::
    :file: tables/keycodes.csv
    :header-rows: 1
