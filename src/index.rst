.. meta::
   :description: This website describes in detail my retrogaming setup.
   :keywords: retrogaming,homeassistant,arduino,automation,infrared,raspberry pi
   :author: Jules Robichaud-Gagnon
   :og:title property=My Retrogaming Setup: My Retrogaming Setup
   :og:type property=article: article
   :og:image property=https://jrobichaud.github.io/retrogaming/_static/oveview.jpg: https://jrobichaud.github.io/retrogaming/_static/oveview.jpg
   :og:url property=https://jrobichaud.github.io/retrogaming/: https://jrobichaud.github.io/retrogaming/
   :twitter:card: summary_large_image
   :og:description property=This website describes in detail my retrogaming setup.: This website describes in detail my retrogaming setup.

My Retrogaming Setup
====================

.. note::
    This website is under construction. If you have any questions, feel free to open a `github issue <https://github.com/jrobichaud/retrogaming/issues/new>`_.

This website describes in detail my retrogaming setup. It's more of a personal documentation than a tutorial. I hope it can be useful to someone else.

.. image:: _static/demo.gif
   :alt: StreamPlayer
   :align: center

There is a lot of things going on here. Here's an overview of what is going on when I press the keypad's SNES button:

1. :ref:`the button pressed is intercepted by a python program running on a raspberry pi, and the program sends a MQTT message to Home Assistant that the SNES button was pressed <keypad>`
2. :ref:`Home Assistant<homeassistant>` starts multiple automations
    1. it activates a :ref:`smart plug<smart_plugs>` to power on the scart switch and the upscaler;
    2. a :ref:`cec` signal is sent to the TV to power on and then switches to the correct input;
    3. an :ref:`infrared signal<infrared>` simulating a button press on its remote controller is transmitted to the upscaler in order to change to the appropriate profile for the SNES;
    4. it powers on another :ref:`smart plug<smart_plugs>` for the external :ref:`audio card and the audio mixer<audio>`;
    5. Lights are dimmed to the correct level through Zigbee.
3. The scart switch detects the signal and switches to the correct input. (I have no merit here, it just works)
4. Now I can play my game.

I also made some quality of life improvements for :ref:`alttpr` in my setup.


Contents, indices and tables
============================

.. toctree::
  :maxdepth: 2

  infrared
  keypad
  cec
  power
  audio
  cast
  products
  homeassistant
  alttpr

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
