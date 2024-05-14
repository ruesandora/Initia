> İlk görev, geldikçe güncelleyeceğim:

```console
git clone https://github.com/skip-mev/slinky.git
cd slinky

make build

mv build/slinky /usr/local/bin/
rm -rf build
````
```console
# bir kerede copy paste
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 0.0.0.0:17990
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
````console
sudo systemctl daemon-reload
sudo systemctl enable slinky.service
sudo systemctl start slinky.service
````
```console
screen -S oracle
make run-oracle-client
# CTRL A D ile çıkalım sonra
````
```console
# bu komutla dosyayı açalım
nano config/app.toml
# içine aşağıdakileri yapıstıralım komple:
```
```console
###############################################################################
###                                  Oracle                                 ###
###############################################################################
[oracle]
# Enabled indicates whether the oracle is enabled.
enabled = "true"

# Oracle Address is the URL of the out of process oracle sidecar. This is used to
# connect to the oracle sidecar when the application boots up. Note that the address
# can be modified at any point, but will only take effect after the application is
# restarted. This can be the address of an oracle container running on the same
# machine or a remote machine.
oracle_address = "127.0.0.1:8080"

# Client Timeout is the time that the client is willing to wait for responses from 
# the oracle before timing out.
client_timeout = "500ms"

# MetricsEnabled determines whether oracle metrics are enabled. Specifically
# this enables instrumentation of the oracle client and the interaction between
# the oracle and the app.
metrics_enabled = "false"
```

```
journalctl -fu slinky --no-hostname
```

> Akıyorsa tamamdır, ctrl c ile durudurabilirsiniz h.o. <3
