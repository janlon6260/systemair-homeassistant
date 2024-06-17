# Integrate Systemair/Villavent in Home Assistant
How to integrate Systemair/Villavent MODBUS via ESPHome in Home Assistant. 

# Necessary equipment

1. ESP32 board <a href="https://www.ebay.com/itm/325728325517?itmmeta=01J0GDJJ87TEB5G3ZS6P07KAZT&hash=item4bd6ebcf8d:g:mJwAAOSw8QdkrjPK&itmprp=enc%3AAQAJAAAA8JK95Nk1tViUcCHuKzJWSsfPigTdp43JnBRj5um4ibBsKnsdG1wT8sYyfYLfasAEJ%2BTpkYxMBbW%2FlwdZgMf1LP4DAgJRyuRha%2BnB8ALxqExu82cDDPY1n8Y3z331hH5Vp1JKMmOhsktaSZ4N2dxL6odY%2BHA7mWkWNZqwGDmnxZyTQy3AOMCUS3PctciMFFrHgGMYFIRF%2BiBW17gcyJzBJg970KUcaMSWqEkIvcicGxfHg4Cbv4QhrL43G6aFh4nY5vknZcm7mDRIk%2BjXAehvCSIqgRsW3icZ%2B2P4cWXS1x8pzkuodLFhaPqoS3wLrTOsXw%3D%3D%7Ctkp%3ABFBMmqTKjYRk">(ex. this board)</a>
<img src="https://github.com/janlon6260/systemair-homeassistant/blob/main/img/ESPWROOM.PNG" width="200" height="200">
<p></p>
2. TTL to RS-485 board <a href="https://www.ebay.com/itm/381374599127?_trkparms=amclksrc%3DITM%26aid%3D777008%26algo%3DPaERSONAL.TOPIC%26ao%3D1%26asc%3D20230823115209%26meid%3D1bf9533e277940f097c47891d0ce3745%26pid%3D101800%26rk%3D1%26rkt%3D1%26itm%3D381374599127%26pmt%3D0%26noa%3D1%26pg%3D4375194%26algv%3DRecentlyViewedItemsV2SignedOut%26brand%3DUnbranded&_trksid=p4375194.c101800.m5481&_trkparms=parentrq%3A20d8be291900a0d955fd5b8dfffdeff4%7Cpageci%3A26114880-2bd5-11ef-8ae6-d65d7246d408%7Ciid%3A1%7Cvlpname%3Avlp_homepage">(ex. this board)</a>
<img src="https://github.com/janlon6260/systemair-homeassistant/blob/main/img/TTLMOD485.PNG" width="200" height="200">

<p></p>
You will need basic soldering skills as the ESP board comes unsoldered. I would recommend buying pin jumper cables to connect this more easily <a href="https://www.ebay.com/itm/235403476219?itmmeta=01J0GG2T1S06KS19Y0ZRAYREA9&hash=item36cf23fcfb:g:uEAAAOSwVq5lsyq2&itmprp=enc%3AAQAJAAAA4NdZYiDEKVbTQ3cELgBwIpJzEIc%2Bixi2ZuWp9m8NM%2FPLRgYG5GeUYf33ZNKUlj9UCE93%2FPn97G9fIYRop8uWZLcJ06lFBnFFNNj40siDfy63rvt%2F3Y7EjpgiV%2Bqf3QBROt6JVnNLWH84NXiGNVjEwwkMebdr5WJZvst7X%2BnSxJKilMiUzaj%2FS%2FJk9lJdssSqnqw9fj%2Fspn14d2y9LS3U2HMDGbbdAQtulZYL8p8XODokj4%2BaYBf23vQBX65K3bgLCM6pd7FK2Kq%2Bbw8CYvwcPpgDQmAybIbKl3rISqrx7ZP2%7Ctkp%3ABFBMjqGLkIRk">(ex. this pin jumpers (female-female)</a>.
<br>
I power up to the ESP board via USB-C to USB-A through power adapter in the wall socket. Since the TTL board is specified for 5v, I don't think it is possible to use a battery to power up this setup.

# Procedure for soldering/connecting boards

Solder on the supplied pin array that comes with the ESP32 board.
The TTL-RS485 board has eight PINs. VCC, GND, A and B on one side and DI, DE, RE, and RO on the other side.
<p></p>
The table below shows how the cards should be connected together (PIN number/names):
<table>
  <tr>
    <th>TTL RS-485 board</th>
    <th>ESP32 board</th>
    <th>Systemair unit</th>
  </tr>
  <tr>
    <td>VCC</td>
    <td>5v</td>
    <td></td>
  </tr>
    <tr>
    <td>GND</td>
    <td>GND</td>
      <td></td>
  </tr>
    <tr>
    <td>DI</td>
    <td>TX</td>
      <td></td>
  </tr>
      <tr>
    <td>DE and RE together</td>
    <td>5</td>
        <td></td>
  </tr>
      <tr>
    <td>RO</td>
    <td>RX</td>
        <td></td>
  </tr>
      <tr>
    <td>A</td>
    <td></td>
    <td>A+</td>
  </tr>
      <tr>
    <td>B</td>
    <td></td>
    <td>B-</td>
  </tr>
</table>

<p></p>

# ESP code and Systemair MODBUS register
First, you need to familiarize yourself with the MODBUS register that Systemair uses. The register for units (ex. SAVE) can be found <a href="https://shop.systemair.com/upload/assets/SAVE_MODBUS_VARIABLE_LIST_20190116__REV__29_.PDF">here.</a> For older models (ex. VR) can be found <a href="https://shop.systemair.com/upload/assets/MODBUS_FOR_RESIDENTIAL_USER_MANUAL.PDF">here.</a>
<br>
My working code is in the "config" folder in this repository. Feel free to use it as a starting point, and change it to your wishes.
<p>
<b>For some reason, you have to lower the number in the code compared to what is written in Systemair's MODBUS register. For example, when it says MODBUS register number "12401 - Supply Air Fan RPM indication from TACHO", you must use register number 12400 in the ESP code.</b>
<p>
Remember to edit WIFI settings to your setup. 
