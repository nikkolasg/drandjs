# drandjs

drandjs is a Javascript library able to communicate with a public [drand](https://github.com/dedis/drand) network. drandjs can fetch *public* randomness from a node or a group of nodes and verify it in the browser. drandjs uses the pairing-based library [kyberJS](https://github.com/dedis/cothority/tree/master/external/js/kyber) from dedis to perform the verification locally.

**NOTE**: This software is considered experimental and has NOT received a third-party audit yet. Therefore, DO NOT USE it in production or for anything security critical at this point.

## Installation

There are two ways to install drandjs: by using a CDN or compile it locally and bundle it with your application.

In the former case, you can use:
```javascript
<script src="https://cdn.jsdelivr.net/gh/PizzaWhisperer/drandjs/dist/drand.js"></script>
```
In the latter case, simply run `make compile` to bundle every file together and create the `dist/drand.js` file ready to use in your application.

## Usage

drandjs has a single function `fetchAndVerify(identity, distkey)` to fetch and verify public randomness from a drand network. 

### Identity

The identity structure has the following form:
```json
{
  Address: "drand-test.nikkolasg.xyz:8888",
  TLS: true,
}
```
The **Address** is the IP address or DNS name of the drand node the user wishes to contact. **TLS** is true if drandjs should contact this node over HTTPS (drand nodes by default are using HTTPS) and false if the node does not have a TLS certificate.
One can retrieve the identity of drand nodes from the group configuration file of the network. See [drand](https://github.com/dedis/drand) for more information.

### Distributed Key

The **distkey** argument represents the distributed key of the participants created during the setup phase.
The key should be in hexadecimal format and should be given out-of-bands (it can be obtained from the group configuration file or fetched from a drand node operator).
It should look like:
```
distkey = "017f2254bc09a999661f92457122613adb773a6b7c74333a59bde7dd552a7eac2a79263bb6fb1f3840218f3181218b952e2af35be09edaee66566b458c92609f7571e8bb519c9109055b84f392c9e84f5bb828f988ce0423ce708be1dcf808d9cc63a610352b504115ee38bc23dd259e88a5d1221d53e45c9520be9b601fb4f578"
```

Nevertheless, drandjs allows to set this field to "", in which case, drandjs fetches the distributed key *as well as* the randomness, in order to verify the latter. 
Note that in this mode of operation, the server may lie about the distributed key and create any valid randomness it wants.

### Randomness 

The function `fetchAndVerify` returns a Promise with the following structure:
```javascript
[randomness, previous, round]
```
such as:
```javascript
[
"3393f21a641e7324b0b75ad0a40ba388e0add0bb5c9d61532ff501f35815bca85af6471f1f181a4d3c484d9cdf7a8fded25645ddde15fc33a15a01f61361c723", 
"05b851a3b36f11c6f38b2cfa808e3ed55256359694dc482639103c7668e702e70a165d73438cb30b5b73531cd6e17bed1ff623c3638cfdae85d815f339e85120",
18332
]
```

### Example

```javascript
identity = {
  Address: "drand-test.nikkolasg.xyz:8888",
  TLS: true,
}
distkey = "";

fetchAndVerify(identity, distkey)
  .then(function (fulfilled) {
  //The random output was successfully verified, you can
  //do something with fulfilled = [randomness, previous, round] such as printing it.
  })
  .catch(function (error) {
    //A problem occurred during the verification process. You can
    //do something with error = [randomness, previous, round] such as printing it.
  })
```

### Test Server

We provide a script to locally run a server that will fake a drand server and a simple html file which show what you could do with `fetchAndVerify`.

To launch the server and open the html file, go to the `example` folder and execute:
```bash
python3 script.py
```


