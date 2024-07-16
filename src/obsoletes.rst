.. _obsoletes:

=========
Graveyard
=========

This file contains a list of previous implementations that have been replaced with newer ones.


.. _cec_rpi:

CEC through Raspberry PI
========================

*I no longer use this method because Homeassistant was regularly losing connection with the Raspberry PI and the only way to resolve it was to restart regularly Homeassistant.*

CEC is a feature of HDMI that allows individual CEC-enabled devices to control each other without user intervention.

Overview
--------

I use CEC protocol control my TV from Home Assistant. Ex: Turn on/off TV, change input source, and control volume.

.. graphviz:: graphs/obsoletes/cec_sequence.dot

Proxy configuration
-------------------


Setup service
^^^^^^^^^^^^^


Compile/Install `libcec <https://github.com/Pulse-Eight/libcec>`_

Install `pyCEC <https://pypi.org/project/pyCEC/>`_

.. code-block:: bash

    pip3 install pyCEC

Configure a service for pyCEC:

.. code-block:: yaml

    [Unit]
    Description=PyCEC
    Documentation=
    After=network.target

    [Service]
    Type=simple
    User=retro
    ExecStart=/usr/bin/python3 -m pycec
    Restart=always
    #RuntimeMaxSec=1d
    #MemoryHigh=50M
    MemoryMax=60M
    MemorySwapMax=0

    [Install]
    WantedBy=multi-user.target

It appears to have a memory leak and I made sure it kills itself when it gets to 60M of memory.

Home Assistant
--------------

Configuration
^^^^^^^^^^^^^

`Documentation <https://www.home-assistant.io/integrations/hdmi_cec/>`_

create a file ``hdmi_cec.yaml`` and add the hostname/ip of the cec proxy:

.. code-block:: yaml

    host: retro-controller.local # this the hostname of my raspberry pi

in ``configuration.yaml`` add:

.. code-block:: yaml

    hdmi_cec: !include hdmi_cec.yaml

Automations
^^^^^^^^^^^

I used `CEC-O-Matic <https://cec-o-matic.com/>`_ to determine the messages for my TV.

.. csv-table:: CEC messages for my TV
   :file: tables/cec_messages.csv
   :header-rows: 1

This is an example home assistant automation to switch to hdmi 1:

.. code-block:: yaml

    service: hdmi_cec.send_command
    data:
        raw: 1f:82:10:00

