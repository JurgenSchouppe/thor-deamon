# thor-deamon


Creating and Modifying thor systemd Unit Files.
This can be usefull in order to be able to use standard systemctl status commands and in order to start the deamon on reboot.

### thor.service
```
/etc/systemd/system/
```
```
[Unit]
Description=Thor VeChain Daemon
After=network.target auditd.service

[Service]
EnvironmentFile=/etc/thor/environ
ExecStart=/usr/local/thor/bin/thor $THORARGS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
Type=simple

[Install]
WantedBy=multi-user.target
Alias=thor.service
```
### ENV variables
```
/etc/thor/environ
```
```
GOROOT=/usr/local/go
GOPATH=/usr/local/thor
PATH=$GOPATH/bin:$GOROOT/bin:$PATH
THORARGS="--network test"
```
Do not forget to add the --api-addr 0.0.0.0:8669 flag if you want other containers and/or hosts to have access to the RESTful API. Thorbinds to localhost by default and it will not accept requests outside the container itself without the flag. Also you will need to set a list of domains from which to accept cross origini API requests.
```
THORARGS="--network test" becomes THORARGS="--network test --api-addr 0.0.0.0:8669 --api-cors $domain1,$domain2"
```
A list of other command line options can be found here.
```
--network value the network to join (main|test)
--data-dir value directory for block-chain databases
--beneficiary value address for block rewards
--api-addr value API service listening address (default: "localhost:8669")
--api-cors value comma separated list of domains from which to accept cross origin requests to API
--verbosity value log verbosity (0-9) (default: 3)
--max-peers value maximum number of P2P network peers (P2P network disabled if set to 0) (default: 25)
--p2p-port value P2P network listening port (default: 11235)
--nat value port mapping mechanism (any|none|upnp|pmp|extip:) (default: "none")
--help, -h show help
--version, -v print the version
```

If you want to switch in between Vechain's main and test net, just change the thor arguments in the environ file.
```
THORARGS="--network test"
THORARGS="--network main"
```
solo client runs in solo mode for test & dev
```
THORARGS="solo --on-demand"               # create new block when there is pending transaction
THORARGS="solo --persist"                 # save blockchain data to disk(default to memory)
THORARGS="solo --persist --on-demand"     # two options can work together
```

The thor status when running in solo (test & dev) mode
```
● thor.service - Thor VeChain Daemon
   Loaded: loaded (/etc/systemd/system/thor.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2018-07-18 10:57:51 UTC; 3s ago
 Main PID: 7175 (thor)
    Tasks: 12
   Memory: 345.2M
      CPU: 94ms
   CGroup: /system.slice/thor.service
           └─7175 /usr/local/thor/bin/thor solo --persist --on-demand

Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x29f72dc07224a4c6270407bfd6b8fec559d29f6c │ 0xfbb9e7ba5fe9969a71c6599052237b91adeb1e5fc0c96727b66e56ff5d02f9d0 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x47109a193c49862c89bd76fe2de3585743dd2bb0 │ 0x547fb081e73dc2e22b4aae5c60e2970b008ac4fc3073aebc27d41ace9c4f53e9 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0xa5e255d4c65af201b97210ff4cd9521a46427654 │ 0xc8c53657e41a8d669349fc287f57457bd746cb1fcfc38cf94d235deb2cfca81b │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x0489a3fff1930b85f1d73eff8a4699281aadb558 │ 0x87e0eba9c86c494d98353800571089f316740b0cb84c9a7cdf2fe5c9997c7966 │
Jul 18 10:57:52 vthor-chain thor[7175]: └────────────────────────────────────────────┴────────────────────────────────────────────────────────────────────┘
Jul 18 10:57:52 vthor-chain thor[7175]: t=2018-07-18T10:57:52+0000 lvl=info msg="prepared to pack block"
```

Syslog output
```
Jul 18 10:57:51 vthor-chain systemd[1]: Started Thor VeChain Daemon.
Jul 18 10:57:51 vthor-chain thor[7175]: t=2018-07-18T10:57:51+0000 lvl=warn msg="low fd limit, increase it if possible" limit=1024
Jul 18 10:57:52 vthor-chain thor[7175]: Starting Thor solo/v1.0.0-e8260bf-dev/linux/go1.10.3
Jul 18 10:57:52 vthor-chain thor[7175]:     Network     [ 0x000000003a3e7437634e9ab026cd279a88a8f086c2f332421d424668ac976bc7 devnet ]
Jul 18 10:57:52 vthor-chain thor[7175]:     Best block  [ 0x000000003a3e7437634e9ab026cd279a88a8f086c2f332421d424668ac976bc7 #0 @2018-05-15 16:00:00 +0000 UTC ]
Jul 18 10:57:52 vthor-chain thor[7175]:     Data dir    [ /root/.org.vechain.thor/instance-1d424668ac976bc7 ]
Jul 18 10:57:52 vthor-chain thor[7175]:     API portal  [ http://127.0.0.1:8669/ ]
Jul 18 10:57:52 vthor-chain thor[7175]: ┌────────────────────────────────────────────┬────────────────────────────────────────────────────────────────────┐
Jul 18 10:57:52 vthor-chain thor[7175]: │                   Address                  │                             Private Key                            │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x7567d83b7b8d80addcb281a71d54fc7b3364ffed │ 0xdce1443bd2ef0c2631adc1c67e5c93f13dc23a41c18b536effbbdcbcdb96fb65 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0xd3ae78222beadb038203be21ed5ce7c9b1bff602 │ 0x321d6443bc6177273b5abf54210fe806d451d6b7973bccc2384ef78bbcd0bf51 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x733b7269443c70de16bbf9b0615307884bcc5636 │ 0x2d7c882bad2a01105e36dda3646693bc1aaaa45b0ed63fb0ce23c060294f3af2 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x115eabb4f62973d0dba138ab7df5c0375ec87256 │ 0x593537225b037191d322c3b1df585fb1e5100811b71a6f7fc7e29cca1333483e │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x199b836d8a57365baccd4f371c1fabb7be77d389 │ 0xca7b25fc980c759df5f3ce17a3d881d6e19a38e651fc4315fc08917edab41058 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x5e4efedf3d71232340280d8bc475421352994b63 │ 0x88d2d80b12b92feaa0da6d62309463d20408157723f2d7e799b6a74ead9a673b │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────��───────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x29f72dc07224a4c6270407bfd6b8fec559d29f6c │ 0xfbb9e7ba5fe9969a71c6599052237b91adeb1e5fc0c96727b66e56ff5d02f9d0 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x47109a193c49862c89bd76fe2de3585743dd2bb0 │ 0x547fb081e73dc2e22b4aae5c60e2970b008ac4fc3073aebc27d41ace9c4f53e9 │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0xa5e255d4c65af201b97210ff4cd9521a46427654 │ 0xc8c53657e41a8d669349fc287f57457bd746cb1fcfc38cf94d235deb2cfca81b │
Jul 18 10:57:52 vthor-chain thor[7175]: ├────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────┤
Jul 18 10:57:52 vthor-chain thor[7175]: │ 0x0489a3fff1930b85f1d73eff8a4699281aadb558 │ 0x87e0eba9c86c494d98353800571089f316740b0cb84c9a7cdf2fe5c9997c7966 │
Jul 18 10:57:52 vthor-chain thor[7175]: └────────────────────────────────────────────┴────────────────────────────────────────────────────────────────────┘
Jul 18 10:57:52 vthor-chain thor[7175]: t=2018-07-18T10:57:52+0000 lvl=info msg="prepared to pack block"
```

### commands
```
systemctl status thor
```
```
● thor.service - Thor VeChain Daemon
   Loaded: loaded (/etc/systemd/system/thor.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2018-07-18 09:42:07 UTC; 41min ago
 Main PID: 5345 (thor)
    Tasks: 15
   Memory: 419.3M
      CPU: 14.835s
   CGroup: /system.slice/thor.service
           └─5345 /usr/local/thor/bin/thor --network test

Jul 18 10:21:29 vthor-chain thor[5345]: t=2018-07-18T10:21:29+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=129.856µs|343.06µs mgas/s=0.000 id=[#189475…1829d56f]
Jul 18 10:21:39 vthor-chain thor[5345]: t=2018-07-18T10:21:39+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=455.641µs|353.739µs mgas/s=0.000 id=[#189476…ef0e796b]
Jul 18 10:21:50 vthor-chain thor[5345]: t=2018-07-18T10:21:50+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=152.974µs|448.72µs mgas/s=0.000 id=[#189477…f21009d9]
Jul 18 10:21:59 vthor-chain thor[5345]: t=2018-07-18T10:21:59+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=758.219µs|456.086µs mgas/s=0.000 id=[#189478…ab2a58f8]
Jul 18 10:22:09 vthor-chain thor[5345]: t=2018-07-18T10:22:09+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=188.573µs|465.877µs mgas/s=0.000 id=[#189479…0bf526ff]
Jul 18 10:22:19 vthor-chain thor[5345]: t=2018-07-18T10:22:19+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=420.698µs|317.048µs mgas/s=0.000 id=[#189480…4498a254]
Jul 18 10:22:29 vthor-chain thor[5345]: t=2018-07-18T10:22:29+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=431.503µs|313.412µs mgas/s=0.000 id=[#189481…5eabbd08]
Jul 18 10:22:39 vthor-chain thor[5345]: t=2018-07-18T10:22:39+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=454.416µs|320.077µs mgas/s=0.000 id=[#189482…24018be9]
Jul 18 10:22:49 vthor-chain thor[5345]: t=2018-07-18T10:22:49+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 et=116.818µs|275.931µs mgas/s=0.000 id=[#189483…89a9367d]
Jul 18 10:22:59 vthor-chain thor[5345]: t=2018-07-18T10:22:59+0000 lvl=info msg="imported blocks (1)" pkg=node txs=0 mgas=0.000 e
```

```
systemctl stop thor
```

Stop the thor deamon 

```
● thor.service - Thor VeChain Daemon
   Loaded: loaded (/etc/systemd/system/thor.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since Wed 2018-07-18 10:30:15 UTC; 1s ago
  Process: 5780 ExecStart=/usr/local/thor/bin/thor $THORARGS (code=exited, status=0/SUCCESS)
 Main PID: 5780 (code=exited, status=0/SUCCESS)

Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="exit signal received" signal=terminated
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="stopping communicator..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="stopping P2P server..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="saving peers cache..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="stopping API server..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="closing tx pool..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="closing log database..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg="closing main database..."
Jul 18 10:30:15 vthor-chain thor[5780]: t=2018-07-18T10:30:15+0000 lvl=info msg=exited
Jul 18 10:30:15 vthor-chain systemd[1]: Stopped Thor VeChain Daemon.
```
```
systemctl start thor
```
Start the thor deamon 
```
● thor.service - Thor VeChain Daemon
   Loaded: loaded (/etc/systemd/system/thor.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2018-07-18 10:30:54 UTC; 2s ago
 Main PID: 5806 (thor)
    Tasks: 13
   Memory: 31.0M
      CPU: 95ms
   CGroup: /system.slice/thor.service
           └─5806 /usr/local/thor/bin/thor --network test

Jul 18 10:30:54 vthor-chain thor[5806]: t=2018-07-18T10:30:54+0000 lvl=warn msg="low fd limit, increase it if possible" limit=1024
Jul 18 10:30:54 vthor-chain thor[5806]: Starting Thor/v1.0.0-e8260bf-dev/linux/go1.10.3
Jul 18 10:30:54 vthor-chain thor[5806]:     Network      [ 0x000000000b2bce3c70bc649a02749e8687721b09ed2e15997f466536b20bb127 test
Jul 18 10:30:54 vthor-chain thor[5806]:     Best block   [ 0x0002e45771dac9e99dbed69216d9c7363c711c5c05721caa5ab7768511a46043 #189
Jul 18 10:30:54 vthor-chain thor[5806]:     Master       [ 0x0111745bb560b2f6f507e0cd959c1e5d7ba3c64c ]
Jul 18 10:30:54 vthor-chain thor[5806]:     Beneficiary  [ not set, defaults to endorsor ]
Jul 18 10:30:54 vthor-chain thor[5806]:     Instance dir [ /root/.org.vechain.thor/instance-7f466536b20bb127 ]
Jul 18 10:30:54 vthor-chain thor[5806]:     API portal   [ http://127.0.0.1:8669/ ]
Jul 18 10:30:54 vthor-chain thor[5806]: t=2018-07-18T10:30:54+0000 lvl=info msg="starting P2P networking"
Jul 18 10:30:56 vthor-chain thor[5806]: t=2018-07-18T10:30:56+0000 lvl=info msg="waiting for synchronization..." pkg=node
```
