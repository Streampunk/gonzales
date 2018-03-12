# Gonzales

Network speed testing designed to move simulated frames of video over the network using various combinations of protocol and acceleration techniques. The protocols supported are UDP (unicast and multicast), HTTP and HTTPS.

As well as comparing the protocols themselves, the scripts are designed to allow comparison of:

* moving frames in series and in parallel with HTTP and HTTPS with a configurable number of parallel connections;
* using encryption or in the clear;
* going as fast as possible or using a specified maximum pull rate, e.g. one frame every 40ms for 1080i50;
* trying out different payload sizes, with a default of 1080i50 but configurable to 720p60, SD, quarter frames etc.;
* configuring TCP socket options, such as send and receive buffers and the use of Nagle's algorithm;
* switching on and off [HTTP keep-alive](https://en.wikipedia.org/wiki/HTTP_persistent_connection);
* pushing or pulling frames with HTTP and HTTPS;
* using [Windows RIO](https://technet.microsoft.com/en-us/library/hh997032(v=ws.11).aspx) for UDP acceleration vs the built in [Node.JS datagram API](https://nodejs.org/dist/latest-v6.x/docs/api/dgram.html).

The results of tests carried out with these scripts are being used to inform the design of the [arachnid](https://github.com/Streampunk/arachnid) transport protocol for NMOS, design of the [HTTP/S support for dynamorse](https://github.com/Streampunk/node-red-contrib-dynamorse-http-io) and enhance its uncompressed [RTP capability](https://github.com/Streampunk/node-red-contrib-dynamorse-rtp-io).

## Installation

Install the appropriate [Node.JS LTS](https://nodejs.org/) version for your platform and [node-gyp](https://github.com/nodejs/node-gyp) pre-requisites.

Install the project from git:

    git clone https://github.com/Streampunk/gonzales

Then entire the folder and install

    cd gonzales
    npm install

For multi-threaded testing, make sure that the `UV_THREADPOOL_SIZE` environment is set high enough. For example, on Linux and Mac:

    export UV_THREADPOOL_SIZE=42

For high bandwidth network transport tests, ensure your network interface card is tuned for maximum bandwidth transport on the LAN. For example, see the [advanced settings for Intel Ethernet adapters](https://www.intel.com/content/www/us/en/support/articles/000005593/network-and-i-o/ethernet-products.html).

## Running

The Javascript applications in this folder are designed to be self describing when run with `node`. Each one has a `--help` option that prints out the available options and provides the default values.

The applications are related by protocol:

* `http_server.js`, `pull_client.js` and `push_client.js` - For testing HTTP transport.
* `https_server.js`, `pull_sclient.js` and `push_sclient.js` - For testing HTTPS transport.
* `udp_sender.js` and `udp_receiver.js` - Testing UDP transport using the [Node.JS dgram API](https://nodejs.org/dist/latest-v6.x/docs/api/dgram.html) and [Windows Registered Input/Output](https://technet.microsoft.com/en-us/library/hh997032(v=ws.11).aspx). Can be used with unicast and multicast addresses.

For all the tests, an actual frame of video is read into memory and sent repeatedly. The frame is 1080i50 and can be either stacked or sliced to make it bigger or smaller to simulate other frame rates and resolutions, such as 720p60 or 575i25, or sending frames in partial chunks.

### Example

To run an HTTP pull test, first run a server:

    node http_server.js

Assuming the hostname of the server is `dumpty`, on another computer run:

    node pull_client.js -h dumpty -n 1000

This will simulate the transfer 1000 frames in sequence from the server to the client, moving them as fast as possible.

### Analysing

Wireshark can be used to analyse the behaviour of generated streams. To do this for multicast streams, the system where Wireshark is running needs to do just that.

    node join_group.js -h 234.5.6.7 -p 6789 -i 10.11.12.13

## Results

The results of tests carried out in the BBC's IP PoC lab in Belfast as included in Microsoft Excel document [./gonzales_graphs.xlsx].

## Status, support and further development

The security certificates are fixed and unmanaged. They are provided to give just enough for HTTPS to encrypt a stream and have no validation by a certificate authority. Please do not copy this approach as a means to secure your infrastructure! Further work will follow later this summer to link these tools with a directory service, certificate authority etc..

Contributions can be made via pull requests and will be considered by the author on their merits. Enhancement requests and bug reports should be raised as github issues. For support, please contact [Streampunk Media](http://www.streampunk.media/).

## License

This software is released under the Apache 2.0 license. Copyright 2018 Streampunk Media Ltd.
