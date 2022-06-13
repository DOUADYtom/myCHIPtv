<div id="top"></div>
<!-- To do Some comments -->

[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/DOUADYtom/myCHIPtv">
    <img src="images/logo.png" alt="Logo" width="80" height="80">
  </a>

<h3 align="center">myCHIPtv</h3>

  <p align="center">
    project_description
    <br />
    <a href="https://github.com/DOUADYtom/myCHIPtv"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="https://github.com/DOUADYtom/myCHIPtv">View Demo</a>
    ·
    <a href="https://github.com/DOUADYtom/myCHIPtv/issues">Report Bug</a>
    ·
    <a href="https://github.com/DOUADYtom/myCHIPtv/issues">Request Feature</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#install-prerequisites">Install Prerequisites</a></li>
      </ul>
    </li>
    <li>
      <a href="#installation">Installation</a>
      <ul>
        <li><a href="#checking-out-the-matter-code">Checking out the Matter code</a></li>
        <li><a href="#prepare-for-building">Prepare for building</a></li>
        <li><a href="#building-controller">Building controller</a></li>
        <li><a href="#run-apps">Run apps</a></li>
        <li><a href="#commissionning">Commissionning</a></li>
      </ul>
    </li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

[![Product Name Screen Shot][product-screenshot]](https://example.com)

Here's a blank template to get started: To avoid retyping too much info. Do a search and replace with your text editor for the following: `DOUADYtom`, `myCHIPtv`, `twitter_handle`, `linkedin_username`, `email_client`, `email`, `project_title`, `project_description`

<p align="right">(<a href="#top">back to top</a>)</p>


<!-- GETTING STARTED -->
## Getting Started

This is an example of how you may give instructions on setting up your project locally.
To get a local copy up and running follow these simple example steps.

### Prerequisites
 
1. A Raspberry Pi 4 board
2. A USB Bluetooth Dongle, Ubuntu desktop will send Bluetooth advertisement,
       which will block CHIP from connecting via BLE. 
3. Ubuntu 20.04 or newer image for ARM64 platform.

My two Raspberry use the following Linux version : ``Linux raspberrypi 5.15.32-v8+ aarch64 GNU/Linux``

<p align="right">(<a href="#top">back to top</a>)</p>



### Install Prerequisites

On Ubuntu server, you need to install `pi-bluetooth` and `avahi-utils` via APT.
   ```sh
   sudo apt install pi-bluetooth avahi-utils
   ```
You need to reboot your RPi after install `pi-bluetooth`.

Do `hciconfig` to show bluetooth devices.

Installing prerequisites 
   ```sh
   sudo apt-get install git gcc g++ pkg-config libssl-dev libdbus-1-dev \
   libglib2.0-dev libavahi-client-dev ninja-build python3-venv python3-dev \
   python3-pip unzip libgirepository1.0-dev libcairo2-dev libreadline-dev
   ```

By default, wpa_supplicant is not allowed to update (overwrite) configuration,
if you want the Matter app to be able to store the configuration changes
permanently, we need to make the following changes.

1. Edit the dbus-fi.w1.wpa_supplicant1.service file to use configuration file instead.
     
   ```sh
   sudo nano /etc/systemd/system/dbus-fi.w1.wpa_supplicant1.service
   ```
     
Change the wpa_supplicant start parameters to:
     
   ```sh
   ExecStart=/sbin/wpa_supplicant -u -s -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
   ```
     
2. Add the wpa-supplicant configuration file
     
   ```sh
   sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
   ```
     
And add the following content to the file:
     
   ```sh
   ctrl_interface=DIR=/run/wpa_supplicant
   update_config=1
   ```

Finally, reboot your RPi.

<p align="right">(<a href="#top">back to top</a>)</p>


## Installation

### Checking out the Matter code

1. Check out the Matter repository:
   ```sh
   git clone --recurse-submodules https://github.com:project-chip/connectedhomeip.git
   ```

If you already have a checkout, run the following command to sync submodules:
   ```sh
   git submodule update --init
   ```

2. Change folder
   ```sh
   cd connectedhomeip/
   ```

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- PRE-BUILDING -->
### Prepare for building

Before running any other build command, the `scripts/activate.sh` environment
setup script should be sourced at the top level. This script takes care of
downloading GN, ninja, and setting up a Python environment with libraries used
to build and test.

```sh
source scripts/activate.sh
```

If this script says the environment is out of date, it can be updated by
running:

```sh
source scripts/bootstrap.sh
```

<!-- BUILDING CONTROLLERS -->
### Building controller

1. Prepare python environement for `chip-device-ctrl` :
    ```sh
    scripts/build_python.sh -m platform -i separate
    ```

2. Build `tv-app` :
   ```sh
   scripts/examples/gn_build_example.sh examples/tv-app/linux/ out/host
   ```

2. Build `tv-casting-app` :
   ```sh
   scripts/examples/gn_build_example.sh examples/tv-casting-app/linux/ out/host
   ```

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- RUN APPS -->
## Run apps

### Python controller
Run our python controller without bluetooth commissioning :
   ```sh
   ./out/python_env/bin/chip-device-ctrl --no-ble
   ```
To see all devices commissionnable :
   ```sh
   discover -all
   ```

### Tv App
Run our tv-app with default Pin : `20202021` Discriminator : `3840` Manual Paring Code : `749701123365521327694`:
   ```sh
   ./out/host/chip-tv-app --wifi --secured-device-port 5640 --secured-commissioner-port 5552
   ```

<!-- COMMISSIONNING EXAMPLES -->
## Commissionning

Use this space to show useful examples of how a project can be used. Additional screenshots, code examples and demos work well in this space. You may also link to more resources.

_For more examples, please refer to the [Documentation](https://example.com)_

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- FEATURES -->
## Functional Features

- [x] Feature 1
- [ ] Feature 2
- [ ] Feature 3
    - [ ] Nested Feature

See the [open issues](https://github.com/DOUADYtom/myCHIPtv/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->

[contributors-shield]: https://img.shields.io/github/contributors/DOUADYtom/myCHIPtv.svg?style=for-the-badge
[contributors-url]: https://github.com/DOUADYtom/myCHIPtv/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/DOUADYtom/myCHIPtv.svg?style=for-the-badge
[forks-url]: https://github.com/DOUADYtom/myCHIPtv/network/members
[stars-shield]: https://img.shields.io/github/stars/DOUADYtom/myCHIPtv.svg?style=for-the-badge
[stars-url]: https://github.com/DOUADYtom/myCHIPtv/stargazers
[issues-shield]: https://img.shields.io/github/issues/DOUADYtom/myCHIPtv.svg?style=for-the-badge
[issues-url]: https://github.com/DOUADYtom/myCHIPtv/issues
[license-shield]: https://img.shields.io/github/license/DOUADYtom/myCHIPtv.svg?style=for-the-badge
[license-url]: https://github.com/DOUADYtom/myCHIPtv/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/linkedin_username
[product-screenshot]: images/screenshot.png