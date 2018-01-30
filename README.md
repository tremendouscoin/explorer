Iquidus Explorer - 1.6.1 - for Tremendouscoin
================

Here is how to setup Iquidus Explorer - 1.6.1 - for Tremendouscoin.
We are using the open source block explorer written in node.js from @ https://github.com/iquidus/explorer -- some additional info is available there.

### See it in action

*  [Tremendouscoin Explorer](http://tremendouscoin.info/)

### Get ready

Get your AWS Server running using the /doc-tremendouscoin/aws-daemon.md instructions from @ https://github.com/tremendouscoin/tremendouscoin

Except in this case instead of starting the server via ```~/tremendouscoin/src/tremendouscoind &```
start the server via ```~/tremendouscoin/src/tremendouscoind -txindex &```


Since you'll be adding Iquidus Explorer to this server, you'll need to

```
apt-get install mongodb-clients
apt-get install mongodb-server
apt-get install npm
apt-get install node
sudo ln -s /usr/bin/nodejs /usr/bin/node

```

Get your mongo server running with ```mongod```

### Create database

Enter MongoDB cli:

    $ mongo

Create database:

    > use explorerdb

Create user with read/write access (change "password" here!):

    > db.addUser( { user: "iquidus", pwd: "password", roles: [ "readWrite"] })

### Install the source

    cd /opt
    git clone https://github.com/tremendouscoin/explorer explorer
    cd /opt/explorer
    npm install --production

### Configure

    cp ./settings.json.template ./settings.json

Make required changes in settings.json, namely

*   updating dbsettings password
*   updating wallet pass

URGENT! Also fix values of exports.wallet pass in lib/settings.js !
It probably needs to be done due to an oversight in iquidus's code, but, it needs to be done to avoid ECONNREFUSED errors!

### Open up ports
3001 (or in my case I'm going to put 80 so it's at tremendouscoin.info instead of tremendouscoin.info:3001), 4647, and 27017.
On your AWS server usng their web interface.
On Gandi using ufw like https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04

### Start Explorer

    cd /opt/explorer
    npm start

*Note: tremendouscoind and mongod must be running to start the explorer*

To stop the cluster you can use

    npm stop
    
After you've tested that it works you can keep it going unless you kill it with

    nohup npm start > /dev/null &

### Syncing database with the blockchain

Do it for the first time manually, for fun!
You have to do this in a separate console.
Note that we type ```nodejs``` instead of ```node``` like they say in the original docs for some reason.

```
cd /opt/explorer/
```
then
```
nodejs scripts/sync.js index update
```
or
```
nodejs scripts/sync.js index reindex
```

### Example crontab to sync periodically

On gandi first you may need to ```apt-get install cron```

*Example crontab; update index every minute*

Type ```crontab -e``` and add these two lines

    */1 * * * * cd /opt/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1
    */5 * * * * cd /opt/explorer && /usr/bin/nodejs scripts/peers.js > /dev/null 2>&1

### Below is the License info from @ https://github.com/iquidus/explorer

### License

Copyright (c) 2015, Iquidus Technology  
Copyright (c) 2015, Luke Williams  
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of Iquidus Technology nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
