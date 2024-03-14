> [!NOTE]
> Branches/tags in this repo beginning with v1.x are forked from [pklaus](https://github.com/pklaus/brother_ql_web).  
> Branches ≥2.x are based on the work of [Noloxs](https://github.com/Noloxs/brother_ql_web).


# brother\_ql\_web

This is a web service to print labels on Brother QL label printers.

You need Python 3 for this software to work.

![Screenshot](./screenshots/Label-Designer_Desktop.png)

The web interface is [responsive](https://en.wikipedia.org/wiki/Responsive_web_design).
There's also a screenshot showing [how it looks on a smartphone](./screenshots/Label-Designer_Phone.png)

## Additional Features
* Print text as QR Code
    * Add text to QR Code
    * Change size of QR Code
* Upload files to print
    * .pdf, .png and .jpg files
    * automatically convertion to black/white or grayscale image
* Change print color for black/white/red labels
* Print lables multiple times
    * Cut every label
    * Cut only after the last label
    * Print without cutting
* Migrated GUI to Bootstrap 4
* Make preview for round labels.. round

## Installation

### Docker

Use Docker Image `dersimn/brother_ql_web:2`:

    docker run -d --restart=always --name=brother_ql_web \
        -p 8013:8013 \
        --device=/dev/usb/lp0 \
        dersimn/brother_ql_web:2 \
            --default-label-size d24 \
            --model QL-700 \
            file:///dev/usb/lp0

List all available options with: `docker run --rm dersimn/brother_ql_web:2 -h`

#### Native

This installation manual requires only a default installation of Debian 10 (Buster).
Install prerequisites:

    apt install fontconfig python3-venv python3-pip git poppler-utils

Get the code:

    cd /opt
    git clone https://github.com/tbnobody/brother_ql_web.git

Build the venv and install the requirements:

    cd /opt/brother_ql_web
    python3 -m venv .venv
    source /opt/brother_ql_web/.venv/bin/activate
    pip install -r requirements.txt

##### Configuration file

Copy `config.example.json` to `config.json` (e.g. `cp config.example.json config.json`) and adjust the values to match your needs.

##### Startup

To start the server, run `./run.py`. The command line parameters overwrite the values configured in `config.json`. Here's its command line interface:

    usage: run.py [-h] [--port PORT] [--loglevel LOGLEVEL]
                       [--font-folder FONT_FOLDER]
                       [--default-label-size DEFAULT_LABEL_SIZE]
                       [--default-orientation {standard,rotated}]
                       [--model {QL-500,QL-550,QL-560,QL-570,QL-580N,QL-650TD,QL-700,QL-710W,QL-720NW,QL-1050,QL-1060N}]
                       [printer]

    This is a web service to print labels on Brother QL label printers.

    positional arguments:
      printer               String descriptor for the printer to use (like
                            tcp://192.168.0.23:9100 or file:///dev/usb/lp0)

    optional arguments:
      -h, --help            show this help message and exit
      --port PORT
      --loglevel LOGLEVEL
      --font-folder FONT_FOLDER
                            folder for additional .ttf/.otf fonts
      --default-label-size DEFAULT_LABEL_SIZE
                            Label size inserted in your printer. Defaults to 62.
      --default-orientation {standard,rotated}
                            Label orientation, defaults to "standard". To turn
                            your text by 90°, state "rotated".
      --model {QL-500,QL-550,QL-560,QL-570,QL-580N,QL-650TD,QL-700,QL-710W,QL-720NW,QL-1050,QL-1060N}
                            The model of your printer (default: QL-500)

##### Automatic startup using systemd service

Copy service file, reload system, enable and start the service

    cp /opt/brother_ql_web/systemd/brother_ql_web.service /etc/systemd/system
    systemctl daemon-reload
    systemctl enable brother_ql_web
    systemctl start brother_ql_web

## Usage

Once it's running, access the web interface by opening the page with your browser.
If you run it on your local machine, go to <http://localhost:8013> (You can change
the default port 8013 using the --port argument).
You will then be forwarded by default to the interactive web gui located at `/labeldesigner`.

All in all, the web server offers:

* a Web GUI allowing you to print your labels at `/labeldesigner`,
* an API at `/api/print/text?text=Your_Text&font_size=100&font_family=Minion%20Pro%20(%20Semibold%20)`
  to print a label containing 'Your Text' with the specified font properties.

## Development

### Docker Build

Simple:

    docker build -t brother_ql_web -f Dockerfile-debian .

or using docker compose

    docker compose build
    docker compose up -d

'Cross-compile' with *Docker for Mac* and `buildx`:

    docker buildx create --name mybuilder
    docker buildx use mybuilder
    docker buildx build \
        --platform \
            linux/amd64,\
            linux/arm/v7 \
        -t dersimn/brother_ql_web \
        -t dersimn/brother_ql_web:2 \
        -t dersimn/brother_ql_web:2.x.x \
        --push .

### GitHub Actions

This repo is using auto-build on every push and nightly rebuild (if the parent base image was updated on Docker Hub), see [my example repo](https://github.com/dersimn/GitHub-Actions-to-Docker-Hub-with-SemVer-Tags-and-multiple-Flavors) for details.

## Credits

- [pklaus](https://github.com/pklaus/brother_ql_web) for the original work (my branch ≤1.x)
- [Noloxs](https://github.com/Noloxs/brother_ql_web) for extending the original project (my branches ≥2.x)

## License

This software is published under the terms of the GPLv3, see the LICENSE file in the repository.

Parts of this package are redistributed software products from 3rd parties. They are subject to different licenses:

* [Bootstrap](https://github.com/twbs/bootstrap), MIT License
* [Font Awesome](https://github.com/FortAwesome/Font-Awesome), CC BY 4.0 License
* [jQuery](https://github.com/jquery/jquery), MIT License
