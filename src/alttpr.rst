.. _alttpr:

A Link to the Past Randomizer
=============================

Synopsis
--------

`Zelda: A Link to the Past` is a Super Nintendo game.

`A Link to the Past Randomizer <https://alttpr.com/en>`_ is a new way to replay this classic game by shuffling the location of the items (any may other things).

Its then possible to track the progress of the game using a tracker. Ex: `Dunka Track <https://alttprtracker.dunka.net/>`_ that you can use manually or automatically by connecting the game to the site (using the emulator or your flash cart connected on your computer using sd2snes).

Play a game
^^^^^^^^^^^

Normally you would:

1. Go the the website
2. Upload the rom (you have to find the ROM yourself first)
3. Select the game options
4. Download the rom

With the ROM you can play the game on a SNES emulator OR use a flash cart (Ex: :ref:`fxpak_pro`) to play on a real SNES.

To play on a flash cart you need to:

1. remove the SD Card from the flash cart
2. insert it into your computer
3. copy the rom to the SD Card
4. remove the SD Card from your computer
5. insert it into the flash cart
6. insert the flash cart into the SNES
7. turn on the SNES
8. use the controller to find the rom and Boot the game

You need to do this every single time you want to play a new seed.

However for advanced user tools can let you upload directly the rom on the flash cart when it's connected on your computer.


My problems
-----------

1. I don't want to use my computer at all in the process
2. I want to play on the real SNES
3. I am too lazy to do so many steps

My Overkill Solution
--------------------

I had to spend countless hours to continue to be lazy.

1. create a mobile website on my local network I use to generate the rom
2. create an API used by my website to generate the rom from `https://alttpr.com/`
3. upload the generated rom to my flash cart from the website
4. start the game on the flash cart

.. video:: _static/alttpr/sni-web.mp4
    :height: 600

.. role:: strike
    :class: strike

Oh and also auto track the progress of my game on the :strike:`wall mounted` monitor (This video is a bit old, the monitor is now wall mounted):

.. video:: _static/alttpr/tracker.mp4
    :width: 600

The Pieces of the Puzzle
------------------------

This part is not fully detailed but all the pieces of the puzzle are there.

Reverse Proxy
^^^^^^^^^^^^^

I use `Caddy <https://caddyserver.com/>`_ as a reverse proxy to expose the website and the API on my local network from the raspberry pi.

.. code-block:: Caddyfile

    {
      auto_https disable_redirects
    }

    http://retro-controller.local, http://192.168.18.3 {
      # expose sni
      handle_path /sni/* {
       reverse_proxy :8190
      }
      # website of the tracker
      handle_path /zt/* {
        root * /home/retro/alttptracker/
        file_server {
           hide .git
        }
      }
      # redirection of the tracker's second page
      redir /tracker.html /zt{uri}

      # mobile website
      handle_path /* {
         root * /home/retro/sni-web/public
         file_server {
             hide .git
         }
      }

      # wetbsocket for the tracker
      handle_path /ws:80 {
        reverse_proxy :8080
      }

      # api used by the front-end
      handle_path /pyz3r/* {
        reverse_proxy :8000
      }
    }


Mobile website (sni-web)
^^^^^^^^^^^^^^^^^^^^^^^^
This is the source code of my website.
`sni-web <https://github.com/jrobichaud/sni-web>`_

The build of the static site is uploaded to the raspberry pi using scp:

.. code-block:: bash

    scp ./public/* retro@retro:/home/retro/sni-web/public/


API used by the website (pyz3r-api)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
`pyz3r-api <https://github.com/jrobichaud/pyz3r-api>`_ is the API I made that communicates with `https://alttpr.com/` (through `pyz3r <https://github.com/tcprescott/pyz3r>`_) to generate the rom.

Its configured this way:

1. git clone on the device
2. setup a virtual environment
3. install the roms on the machine
4. configure a service

.. code-block:: yaml

    [Unit]
    Description=pyz3r
    After=network.target

    [Service]
    User=retro
    WorkingDirectory=/home/retro/pyz3r-api
    ExecStart=/home/retro/pyz3r-api/venv/bin/uvicorn --host 127.0.0.1 --port 8000 pyz3r_api.asgi:app
    Restart=always
    Environment=FLASK_ROM_PATH="./Zelda no Densetsu - Kamigami no Triforce (Japan).sfc"
    Environment=FLASK_SM_ROM_PATH="./Super Metroid (JU) [!].smc"
    MemorySwapMax=0

    [Install]
    WantedBy=multi-user.target

SNI
^^^

`SNI <https://github.com/alttpo/sni>`_ stands for Super Nintendo Interface. It's a tool to control a flash cart from a computer.

I made this `SNI pi compiler <https://github.com/jrobichaud/sni-pi-compiler>`_ to make a headless version for raspberry pi. Then I upload it on my raspberry pi using scp.

Sni is exposed on my local network using the reverse proxy.

.. code-block:: yaml

    [Unit]
    Description=sni
    Documentation=
    After=network.target

    [Service]
    Type=simple
    User=retro
    ExecStart=/home/retro/sni
    Restart=always
    #RuntimeMaxSec=1d
    MemorySwapMax=0
    MemoryMax=120M
    #MemoryHigh=90M

    [Install]
    WantedBy=multi-user.target

Tracker
^^^^^^^
This is a `custom version of Dunka Track <https://github.com/jrobichaud/alttptracker>`_ I tweaked to be able to run over my local network.

It's a static website exposed by caddy on my local network but it communicates with the websocket of my raspberry pi.

.. _ha_catt_service_alttpr:

Casting on my wall mounted monitor
..................................

I use the Home Assistant integration :ref:`ha_catt_service` I created to start the tracker on the Chromecast of my wall mounted monitor.

I trigger it using a button my Home Assistant's dashboard.

.. code-block:: yaml

    service: catt_service.cast_site
    data:
      device_id: 74e61b172c2aafaaa1f753ffed2d8335
      # the chromecast does not work with my hostname, I have to use the IP
      url: >-
        http://192.168.18.3/zt/tracker.html?f=ONNNNNNN0000NNNNGC7C7R&d=CNNYY0080000000N0&s=00000000000000000000000000&p=princess_peach&h=192.168.18.3/ws&r=1699666453697#!

It has the following caveats:

1. I cannot interact with the tracker (ex: changing the number of chests opened in the dungeon);
2. I hardcoded the tracker's URL parameters for the gameplay settings I play normally.
