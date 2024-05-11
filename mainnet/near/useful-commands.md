---
description: >-
  Useful set of commands for node operators. From key management to chain
  governance.
---

# Useful commands

<figure><img src="https://github.com/BabyScope/Gitbook/blob/main/images/near.png?raw=true" alt=""><figcaption></figcaption></figure>

## 🚨 Maintenance

#### Get sync info

```bash
curl -s http://127.0.0.1:3030/status | jq ".sync_info"
```

#### Get version

```bash
curl -s http://127.0.0.1:3030/status | jq ".version"
```


#### Remove node

{% hint style="danger" %}
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!
{% endhint %}

```bash
cd $HOME
sudo systemctl stop neard
sudo systemctl disable neard
sudo rm /etc/systemd/system/neard.service
sudo systemctl daemon-reload
rm -f $(which neard)
rm -rf $HOME/.near
rm -rf $HOME/nearcore
```

## ⚙️ Service Management

#### Reload service configuration

```bash
sudo systemctl daemon-reload
```

#### Enable service

```bash
sudo systemctl enable neard
```

#### Disable service

```bash
sudo systemctl disable neard
```

#### Start service

```bash
sudo systemctl start neard
```

#### Stop service

```bash
sudo systemctl stop neard
```

#### Restart service

```bash
sudo systemctl restart neard
```

#### Check service status

```bash
sudo systemctl status neard
```

#### Check service logs

```bash
sudo journalctl -u neard -f --no-hostname -o cat
```
